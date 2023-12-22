---
layout: post
title:  "How to host serverless Next.js 14+ in AWS (or other providers)"
date:   2023-12-22 13:48:00 +0000
categories: web-dev
---

## Introduction

Next.js is a really powerful framework for maximising the performance of a React
frontend. In this post, I'll share how I used AWS Lambda, S3 and CloudFront to
host it in the cloud. The example given will be for AWS, but is also applicable
to other providers with similar services.

The feature that this guide relies upon is the [Next.js "standalone" output
mode](https://nextjs.org/docs/pages/api-reference/next-config-js/output#automatically-copying-traced-files).
Enabling standalone mode tells Next.js to write only the files you need for
production into the `/.next/standalone/` directory. The code in the standalone
build is only what's needed to generate the content that your Next.js server
returns (predominantly, HTML pages) and excludes all static assets such as
images, compiled JS chunks and stylesheets. The standalone function is really
lightweight, which makes it perfect for serverless hosting.

As the standalone function isn't designed to serve static assets, we need to
host them elsewhere. In AWS, the obvious product is S3, which is designed
specifically for this task. [It is
possible](https://nextjs.org/docs/pages/api-reference/next-config-js/output#automatically-copying-traced-files)
to get your standalone function to serve the static assets directly by copying
the `public/` and `.next/static/` directories into the `standalone` directory,
but this is not recommended as it will increase the size of your serverless
function and make it harder to write different caching rules for static content
and the server-side generated responses that Next.js sends back.

The final major piece of the puzzle is how we route different requests to our
serverless function versus our static content host. Again, AWS CloudFront is
specifically designed for this. CloudFront is a global network of servers that
sits behind your domain name and forwards client requests to your hosts.
Responses can then be cached physically close to your users, which will maximise
the performance of your website.

To summarise, here's how your frontend will be configured by the end of this
article:

<img src="/assets/images/nextjs-diagram.svg" width="600px">

I should note that this guide assumes that you want both your static assets and
your Next.js function on the same domain. It should be pretty easy to adapt if
you want to have a separate CDN domain for your static assets.

## Step 1: Uploading your static assets to S3

After building your Next.js server, you'll find that you have static assets in
two places:

1. `.next/static/`
2. `public/`

The `static` directory contains a compiled version of everything inside your
frontend codebase (including media files and client-side code). These are pretty
straightforward to host - we can simply upload them somewhere, and tell Next.js
where they live by providing it with the [`assetPrefix` configuration
value](https://nextjs.org/docs/pages/api-reference/next-config-js/assetPrefix).
Whatever you provide as the `assetPrefix` will be added to the start of any
reference to your static assets in the HTML that your Next.js function returns.
For example, if Next compiles some image to `/.next/static/myImage.jpg`, and you
provide the `https://myhost.com` asset prefix, then any HTML that Next.js
generates will refer to your image as
`https://myhost.com/_next/static/myImage.jpg`.

The `public` directory is a bit more tricky. The things we put in `public/` are
generally things that we expect to find at the root of the frontend host. If we
create some file at `public/robots.txt`, then we expect to find it at
`https://myfrontend.com/robots.txt`. This creates a bit of a conflict with our
Next.js server, because that also needs to live at the root of the frontend
domain.

The easiest way to handle this is to:
- Move everything that's only referred to by frontend code (ie, site media
  content) into the `src` directory (and thereby into `.next/static`)
- Organise anything that remains and that doesn't need to live directly at the
  root into a small number of directories under the root
- Create rules in the CDN for those directories, plus any files that do need to
  stay at the root (`robots.txt`, favicon, etc)
- Direct everything else to Next.js

Configuring this will happen at the end though - for now we just need to get the
static assets uploaded to the S3 bucket.

The way I chose to do this was to copy `.next/static/` and `public/` to
`/content/[commit-hash]/_next/static` and `/content/[commit-hash]/public` inside
the bucket respectively. I chose this scheme because:

- Using a common prefix (`/content/`) allows me to have a single, unchanging CDN
  rule for static assets
- Using the commit hash in the path allows for different releases to be
  available simultaneously, in case users are using cached HTML content from a
  previous version. These releases can also be easily identified in the bucket
  (meaning the bucket can be tidied easily). The commit hash is easy to access
  in deploy jobs, and makes it easy to trace a set of assets back to a specific
  code state. It also would help with [cache
  busting](https://javascript.plainenglish.io/what-is-cache-busting-55366b3ac022),
  except that Next.js does that already.
- Putting all assets for a particular version inside a single prefix also makes
  the bucket easy to manage and tidy.

Obviously, though, there are many ways to organise this, each with their own
pros and cons. The rest of this post assumes this scheme, but tweaking the
instructions for your own scheme should be straightforward.

Actually uploading the assets is pretty straightforward - just build your
frontend, and upload your data to the bucket. If you're using continuous
deployment, the [AWS
CLI](https://docs.aws.amazon.com/cli/latest/userguide/cli-services-s3-commands.html#using-s3-commands-managing-objects-copy)
makes this easy.

The bucket really doesn't need any special configuration at all - all the
defaults are configured appropriately, and it doesn't need to be public once we
have a CDN. You can make it public temporarily for testing though.

## Step 2: Hosting Next.js on Lambda

### Build and configuration

Hosting web applications on Lambda has one small caveat: when a Lambda function
is invoked, it doesn't just get passed a HTTP request. The original HTTP request
is actually encapsulated inside an AWS event, which gives us the ability to get
a bit more data about, eg, the service that invoked the Lambda function. The
problem here is that Next.js runs as a server, responding to HTTP requests on a
given port, and therefore has no idea how to handle an AWS event.

Thankfully, AWS provides a handy tool called [Lambda Web
Adapter](https://github.com/awslabs/aws-lambda-web-adapter#aws-lambda-web-adapter).
When a user sends a request to your Next.js server, LWA does the following:

- Launch your server if it's running in a fresh Lambda function
- Wait for your server to respond to requests on the appropriate port
- Once it gets a response from the server, turn the event back into an HTTP
  request and pass it into your server
- Return the server's response back to the client

Lambda Web Adapter takes the form of a 'layer' that is applied on top of your
function code. We therefore just need to apply the layer and configure it using
environment variables, and it will work nicely.

This is obviously the most AWS-specific part of this process, but it is a common
use case so I would imagine that most hosting providers will offer similar
functionality.

### Infrastructure

For Next.js, all we need is a fairly standard Lambda function. The main thing it
does need is a function URL (that is, a long public URL that you can use to
invoke it). This is just a tickbox on the function's configuration, and no
authorisation is needed.

Here is a recommended list of resources that your function will need:

- A CloudWatch log group with the name `/aws/lambda/${function-name}` (your
  function will write to this log group automatically)
- An IAM role with the following:
  - [The AWS Lambda trust
    policy](https://docs.aws.amazon.com/lambda/latest/dg/lambda-intro-execution-role.html)
    (allows the Lambda service to provide your function runtime with
    credentials)
  - [The Lambda basic execution
    role](https://docs.aws.amazon.com/aws-managed-policy/latest/reference/AWSLambdaBasicExecutionRole.html)
    (allows your function to write logs to CloudWatch)
- The Lambda function itself, with the following configuration:
  - ZIP package upload
  - Timeout and memory size of your choice (maybe start at 1024 MB memory and
    reduce until you start having issues)
  - Node.js runtime (I used `nodejs18.x` but use what makes sense to you)
  - Architecture: I used `x86_64` but I don't see a reason why `arm64` wouldn't
    also work
  - Layers: use Lambda Web Adapter - the ARN info can be found in the AWS docs
  - Role: your execution role
  - Function URL: enabled; no authorisation
  - Environment variables:
    - `AWS_LAMBDA_EXEC_WRAPPER`: `"/opt/bootstrap"`
      - required by LWA
    - `PORT`: [some integer port of your choice]
      - tells Next.js which port to host your application on - [avoid ports 9001
        and
        3000](https://github.com/awslabs/aws-lambda-web-adapter#configurations)
        as these are used by AWS services
    - `AWS_LWA_PORT`: [some integer port of your choice]
      - tells LWA what port to look for your server on; should be the same as
        your `PORT`
    - `RUST_LOG`: `"info"`
      - tells LWA what log level to use; change this if you need to
        troubleshoot. Possible values
        [here](https://docs.rs/env_logger/0.10.1/env_logger/#enabling-logging)

You can find more info on AWS LWA configuration
[here](https://github.com/awslabs/aws-lambda-web-adapter#lambda-functions-packaged-as-zip-package-for-aws-managed-runtimes).
You may wish to enable gzip compression on your responses by setting
`AWS_LWA_ENABLE_COMPRESSION` to `true`.

With your function configured, you should find that you can reach your function
by copying its URL into your browser. If you've made your static assets bucket
public and given the server a correct `assetPrefix` then you should even be able
to see your frontend fully-formed at this stage. Make sure that you upload the
same server build as the one that you took the static assets from - Next.js
generates different file names on every build for cache busting purposes, so
non-matching assets and server code will result in missing assets.

## Step 3: Caching & Routing

The final step is to use CloudFront to sit between your users and your deployed
frontend. The main thing we'll use CloudFront for is to route different requests
to different places (Next.js vs the static assets bucket), but it's possible to
do loads of clever stuff with its caching and firewall options too.

When configuring a CloudFront distribution, there are two concepts that should
be understood:

- An origin is a thing that CloudFront can point requests to. For our purposes,
  the two origin resources will be Next.js and the static assets host.
- A cache behaviour describes a rule that CloudFront should follow when serving
  a certain set of routes under your URL. It allows you to map a set of routes
  to an origin, and specify how that origin's responses should be cached.

Before we make the CloudFront distribution itself, there are a few final things
to configure:

- An Origin Access Control (OAC) for the static assets bucket. This configures
  how we allow CloudFront to talk to the bucket. For more information, see
  [here](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-restricting-access-to-s3.html).
- An IAM policy which allows the CloudFront service to access the static assets
  bucket. See the [OAC
  docs](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/private-content-restricting-access-to-s3.html)
  for details on what the policy should look like. Remember to attach the policy
  to the bucket.
- An Origin Request Policy (ORP), with a query string behaviour of `all`. This
  policy, when applied, will tell CloudFront to forward URL query strings. We
  need this in order to receive query strings in our Next.js server.

So, we need a CloudFront distribution with the following configuration:

- Alias: Your frontend domain
- Price class: Choose the most appropriate one for your use case (list
  [here](https://docs.aws.amazon.com/AmazonCloudFront/latest/DeveloperGuide/PriceClass.html))
- Certificate: Use an appropriate certificate here. It's possible to register a
  free public certificate with AWS Certificate Manager (ACM) - [docs
  here](https://docs.aws.amazon.com/acm/latest/userguide/acm-services.html)
  1. One origin pointed at the Next.js server's function URL.
- Origins:
  2. One origin pointed at the static aqssets bucket, using the OAC mentioned above.
  3. One origin pointed at the `/content/[commit-hash]/public` prefix in the
     static assets bucket, also using the OAC mentioned above.
    - This is needed in order to map the `public` directory to the root of our
      frontend domain. It needs to be updated with each release.
- Cache behaviours (in ascending order of precedence):
  - As many behaviours as you need: point some file or directory at origin 3
    (the current `public` directory). Caching enabled.
  - One behaviour: point `/content/*` at origin 2 (the root of the static assets
    bucket). Caching enabled.
  - Default behaviour: point to origin 1. No caching (we want the website to
    update straight away when we update our Next.js function). Apply the ORP
    mentioned above.
  
You'll also need to configure the DNS for your domain to point to the correct
CloudFront addresses. If you have your domain name in AWS Route 53, [this is
easy](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/routing-to-cloudfront-distribution.html#routing-to-cloudfront-distribution-config).
Otherwise, you can [create an alias
record](https://stackoverflow.com/a/65028375), pointed at the correct CloudFront
URL.

Once all this is configured and deployed, your frontend should be working! The
last thing you might want to do is set up a serverless function or cronjob that
clears out old releases from your static assets bucket every so often. I won't
go into detail on that here though, as this guide is focussed on getting the
basic infrastructure in place.
