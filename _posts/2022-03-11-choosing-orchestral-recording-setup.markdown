---
layout: post
title:  "Choosing an orchestral recording setup"
date:   2022-03-11 15:48:00 +0000
categories: recording
---

Okay, so you're probably here because you need to record a large group of
musicians. Before I get into the concrete setup guide, I'm going to go over a
little bit of the theory.

Chances are that your group are performing in a lovely big echoey room. The
recording is inevitably going to capture some of that sound, which is good. But
as nice as the reverb is, it's also important to make sure that you get a good
clear recording with a good amount of sound coming directly from the instruments
themselves. In order to achieve this, the sweet spot is generally to place the
microphones at the front of the group, probably just behind the conductor, and
reasonably high up (at least 2m!). It's a good idea to err on the side of being
closer, because it's easy to add more reverb and (pretty much) impossible to
take it away.

One of the other main considerations is stereo width. Put simply, we have two
ears, and some techniques are going to give you 'wider' signals (that is,
signals with more differences between the two ears) than others. In a vacuum,
more stereo width is a good thing because it sounds nice, but it shouldn't come
at the cost of things sounding natural.

For these orchestral recordings, you generally want a faithful reproduction of
the sound with minimal colouration and high fidelity even in quiet sections.
You're also probably not using the microphones for amplification, so feedback
won't be an issue. For this reason, you're almost certainly going to use
condenser microphones. These microphones are perfect for the job, but it's worth
you knowing that condensers need 'phantom power'. This is when the microphone
cable not only carries the audio from the mic back to the recorder but also
carries 48V in the opposite direction to polarise the microphone's diaphragm.

I'm also going to need to briefly discuss 'phase' and the problems it can cause.
Basically, imagine a nice sine wave coming from your sound source:

> ![Sine wave](https://upload.wikimedia.org/wikipedia/commons/c/cd/Wave_sine.svg)
> 
> Waveforms.svg: Omegatron; Derivative work: MrLejinad, CC BY-SA 3.0 
> <https://creativecommons.org/licenses/by-sa/3.0>, via Wikimedia Commons

Now let's say you've got two microphones receiving that sine wave. All good. But
what if they're slightly different distances from the sound source? Then each
mic would be receiving the wave in a different 'phase': that is, that one
microphone is picking up a 'peak' as the other is at the bottom of a 'valley'.

This isn't a major problem in and of itself, but it becomes more of an issue if
your microphones get added together later on. In a basic situation, this is most
likely to happen if you have a left mic and a right mic - in this case it will
play back fine, except if someone listens to your recording in mono (that is, on
a device with only one speaker). This might happen on a phone speaker, for
example.

When two sounds get 'summed' together, what we're essentially doing is averaging
the y-axis of those two waves at each point. When our signals are out of phase,
that means that a peak will average with an exactly opposite valley, giving
us... zero. That means that the wave will cancel itself out, meaning anything at
that frequency will magically vanish when summed to mono. In practice, this will
happen at a number of frequencies, creating a 'harsh' and 'unnatural' sound. See
[this video](https://youtu.be/kneNsn65EBg?t=47) for an example.

Finally, there are two main kinds of 'pickup pattern' to discuss. A microphone's
pickup pattern defines what directions it is sensitive to incoming sound from. A
'cardioid' microphone is most sensitive to things in front of it and tends to
'reject' sound from behind it, whereas an 'omnidirectional' microphone picks up
sound (pretty much) equally from all directions.


# Step 0: Choose your microphone setup
Next up, you need to think about what physical setup you're going to use. Since
this guide is primarily written for people that are borrowing equipment from
Music Durham, I'm going to focus on the equipment that we have. That doesn't
mean it's not applicable to other equipment too though!

There are a few main options in terms of how you set up the microphones. In
general, you want your main microphones to all be in one place to reduce phase
issues and to make the recording sound more natural (since someone sitting in
the room would be sitting in one place too - they wouldn't have ears spread
around the room). You'll get the "best" sound just behind (and/or above) the
conductor, so that's where you want to put your main microphones! As a general
rule, 3-4m off the ground is a pretty good sweet spot as it means sound won't be
blocked by things like musicians or the conductor but is still reasonably close
to the performers.

For a bigger orchestra (or more professional recording), you may also want to
place 'outrigger microphones' either side of centre to capture sound from the
wider fringes of the orchestra, but I'm not going to discuss this here.

## XY Stereo
> ![XY Stereo diagram](https://upload.wikimedia.org/wikipedia/commons/1/1d/XY_stereo.svg)
> 
> By Iainf 23:51, 21 September 2007 (UTC) - self-made; based on Image:XY-Stereo.png and
> Image:Cardioidpattern.svg, CC BY-SA 3.0,
> https://commons.wikimedia.org/w/index.php?curid=2792301

This is one of the most foolproof options. Basically, XY stereo places two
microphones on top of each other, pointing approximately 90 degrees apart,
either side of the centre line. The mics need to be cardioid, or you'd just be
listening to the same point in space twice! At Music Durham, we have a pair of
Rode M5 microphones that are perfect for XY stereo.

Pros:
- Very difficult to set up wrong
- No phase issues
- Only needs one mic stand
- Only needs two mics (and two channels on your recorder)
- Angle can be adjusted to suit the width of the ensemble

Cons:
- Not much stereo width (you're only reliant on the difference in the
  microphones' pickup patterns)
- May miss the centre of the group if the mics are angled too wide

## AB Stereo
While useful for recording individual performers or instruments, AB stereo tends
not to be that popular for larger recordings due to its potential for phase
issues and prominent perceived 'gap' in the centre. The idea is essentially to
place two microphones either side of the centre line, so that they each pick up
one half of the group.

The problem with AB stereo for larger recordings is that you want your mics to
be far apart to avoid phase problems. However, by placing them far apart you'll
also lose the centre of the group. This tradeoff is something that will always
need to be overcome with AB stereo, hence its lack of use in orchestral
recording. When recording a single instrument, it's much easier to be closer to
the sound source, meaning the mics can pick up the centre easily without causing
as much phasing.

Pros:
- Simple to set up
- Can use cardioid or omnidirectional mics (research this first though as they
  will sound very different!)
- Stereo concept makes logical sense
- Very wide sound

Cons:
- You're always going to have to compromise between limiting phase cancellation
  and not missing out on the centre

## Decca Tree
Named after Decca Records, the British record label who invented it, the Decca
Tree is not only a hugely popular choice in capturing orchestras but also can be
used in a range of other applications, particularly choral recordings. It's
based on an AB stereo technique, but with the addition of a centre mic to fill
the hole in the centre, and positioned more centrally.

This technique uses three omnidirectional mics, positioned in an isosceles
triangle at the front of the ensemble.
