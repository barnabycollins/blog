---
layout: post
title:  "Executing orchestral recordings"
date:   2022-03-03 14:48:00 +0000
categories: recording
---

Once you know what setup you're using, setting up an orchestral recording is
pretty straightforward.

# Step 0: Making sure you have everything you need
Here's a quick checklist of what you will want to bring:
- Microphones
- Microphone stands
- Recording setup (probably just a portable recorder)
- Means of getting mains power to your recorder (probably a USB charger)
- Battery power for your recorder (just in case)
- XLR cables
- Wired headphones to monitor the signal
- (Maybe) a laptop to retrieve the recordings
- Cable covers and/or tape to avoid trip hazards

# Step 1: Positioning everything
Once all the equipment is in the venue, the first order of business is to figure
out where you're going to put your recorder. You want it to be somewhere safe,
preferably with power so you don't have to rely on batteries, and in a spot that
minimises trip hazards as well as being reasonably close to the microphones.

Once the recorder is placed, you need to place your microphones. When placing
microphones, remember that you are essentially deciding where a listener's
'ears' are going to end up when they hear the recording. As such, where you
place them will greatly affect what the recording sounds like. Typically the
best spot is just at the front of the ensemble (just behind the conductor, if
applicable, is usually a good rule of thumb), and at least 2 metres in the air.

## Case 1: XY Stereo
> ![XY Stereo diagram](https://upload.wikimedia.org/wikipedia/commons/1/1d/XY_stereo.svg)
> 
> By Iainf 23:51, 21 September 2007 (UTC) - self-made; based on Image:XY-Stereo.png and
> Image:Cardioidpattern.svg, CC BY-SA 3.0,
> https://commons.wikimedia.org/w/index.php?curid=2792301

In the case of XY stereo, your two microphones should be placed on a short bar,
such that they point inwards and cross over maybe 5-10mm from their ends (one
above the other). They should be roughly at right angles, but this can be
tweaked depending on how wide your ensemble is and/or how far away it is.

## Case 2: Decca Tree
> ![Decca Tree diagram](https://upload.wikimedia.org/wikipedia/commons/4/48/Decca_Tree.svg)
> 
> By Iainf 00:30, 23 September 2007 (UTC) - public domain,
> https://commons.wikimedia.org/wiki/File:Decca_Tree.svg

The centre microphone should be placed somewhere around the conductor (probably
just behind), and the side microphones should be placed either side of it, a
fair distance behind it. There isn't any particular rule for this, but
microphone manufacturer Schoeps recommend putting mics at least 1.5m apart to
reduce phase issues, so around 2m spacing is probably a safe bet. You may want
to adjust the dimensions a little based on your situation though - if the group
is reasonably small or narrow then maybe shrink the triangle a little, and if
it's quite wide then you could move the left and right mics further out.

# Step 2: Plugging it all in
Once you've decided where your mics are going you're ready to put them on the
stands, plug them in and raise them to 3-4m (in that order)! If they're
omnidirectional you probably want to point them either up or forwards (to not
look silly), whereas with cardioid mics you need to be more careful to get them
oriented correctly. If you're using Music Durham's tall mic stands (which are
K&M 20800s), just raise them to the top of the stand's range - this should put
your mics around 3.1m in the air.

When choosing cables, remember that you're going to need at least 3m just
to get from the mic to the floor, so you'll probably want more than 5m unless
your recorder is basically at the foot of the mic stand.

Run the cables to the recorder, using cable covers or tape to stop them becoming
trip hazards where necessary, and plug them in. I usually plug them into
channels 1-3 in the order L, C, R but you could also do L, R, C if you want!
Just remember which one you go for.

# Step 3: Setting up the recorder
If you're using a Music Durham recorder, visit its page on the [Music Durham
Tech Team
catalogue](https://durhamtech.org.uk/musicdurham/items#recording-production) to
download its manual and/or quick start guide. The process varies between
recorders but here's a quick checklist of things to do:
- Make sure there's an SD card with plenty of space inside the recorder
  - It will typically tell you how much recording time you have left before you
    run out of space on the recording screen
- Make sure the recorder is receiving power so you're not relying on batteries
  - You might need to tell it to use the USB input for power when you turn it on
- Set the recording settings - 48kHz WAV at 24-bit is generally a good bet!
- Make sure phantom power is on; without this you won't get any signal from your
  mics!
  - Phantom power is often switched on with a physical button or switch so if
    you're struggling to find it have a look around the recorder's body
- Turn off any pad switches
- Set the gain, with the following process:
  - Ask the group to sing/play at maximum volume for around a minute
  - For each channel, adjust the volume until it's just maxing out at -10 dB
- Plug in a pair of wired headphones if possible and monitor the signal to make
  sure there aren't any issues

# Step 4: Record
Start your recording! If recording a concert, your best bet is probably to set
it off just before the concert starts. I'd suggest pausing the recording for any
intervals etc as there's no point recording those, but don't do that if you're
worried you'll forget to resume it again!

# Step 5: Edit
Once you're done recording, grab your recordings off the SD card by either
plugging it directly into a computer or connecting the recorder over USB with
the SD card installed.

If you're okay mixing your recordings yourself, import your recordings into a
digital audio workstation; as a minimum I'd suggest you do the following:
- Set a low-cut (aka high-pass) filter at a minimum of 50 Hz to eliminate
  unnecessary sub-bass frequencies
- Pan your left and right signals all the way to their respective sides
- Set the gain of all your signals so they peak at around -6 dB at the loudest
  point of the recording
- If you used a Decca Tree, choose an appropriate mix between the centre mic and
  side mics
  - Make sure you're using stereo speakers or headphones so you can appreciate
    the width properly
  - The centre mic is intended to 'fill in the gap' between the side mics, so if
    in doubt start with only those and bring up the centre mic until things
    sound balanced
- Set the master gain so your recording peaks just below zero at the loudest point

You may also want to use a small amount of mastering compression and EQ, but
only do this if you know what you're doing, and keep it minimal to preserve the
natural sound of your recordings.
