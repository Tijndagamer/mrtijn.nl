---
title: Actually using a RTL-SDR stick the way it was meant to be used
description: Watching DVB-T TV with VLC
layout: blog_post.liquid
published_date: 2018-09-19 16:00:00 +0200
data:
    last_updated_date: 2018-10-21
    published_date: 2018-09-19
---

So a few days ago, I experienced the rare moment where I actually wanted
to watch some TV. I however don't own one, but I do have a computer and
an <a href="https://www.rtl-sdr.com/about-rtl-sdr/">RTL-SDR</a>. I
initially bought it to receive ADS-B data from airplanes, but it had
recently just been laying around to collect dust. The device, which is
based on the RTL2832U chip, is actually intended to be used as a
DVB-T receiver.

Before we get started, I recommend that you look up if there's DVB-T
coverage for your area, because otherwise this whole post is kinda
worthless for you.

I figured that someone already had made the software I needed to get
it up and running on Linux. Luckily my feeling was correct and I quickly
found an abundance of information on the <a href="https://www.linuxtv.org/wiki/index.php/Main_Page">LinuxTV wiki</a>.

Apparently, VLC has built-in support for all kinds of digital TV, including
DVB-T, which is used here in the Netherlands. But before I could use
VLC, I needed to know what frequency I needed to tune in to. On LinuxTV
I found the <code><a href="https://www.linuxtv.org/wiki/index.php/W_scan">w_scan</a></code>
tool. This tool, confusingly available on the Ubuntu repositories under
the name <code>w-scan</code> (note the dash instead of an underscore),
scans DVB channels. It also has an option to generate a VLC compatible
xspf playlist with all the channels it found, so that you don't have to
type in the frequency yourself every time you want to watch TV.

So, let's get started by installing <code>w_scan</code> (I'm assuming that
you've already got VLC):
<pre>$ sudo apt install w-scan</pre>

And then we run this:
<pre>$ w_scan -ft -c NL -E 0</pre>
With <code>-ft</code> we specify that we want to scan for DVB-T stations
and we specify our country settings with <code>-c NL</code>. I'm also
using <code>-E 0</code>, which makes <code>w_scan</code> ignore all
encrypted channels. Since I don't have the necessary subscriptions or
hardware, they're useless to me anyway. <code>w_scan</code> will give
a lot of output, the most important part is once it's done and dumps
a list of all the services it found. For me, it looks a bit like this:
<pre>NPO 1;Digitenne:474000: etc...
NPO 2;Digitenne:474000: etc...
NPO 3;Digitenne:474000: etc...
</pre>
For the sake of brevity I left out all the information we won't use.
NPO 1, 2 and 3 are the only publicly available channels in my region,
so let's try those out. The 6-digit number is the frequency in kHz,
which is what we need to give to VLC.

Next, open VLC and hit <code>CTRL+C</code> (or click on "Media" and then
"Open Capture Device"). Under "Capture mode", select "TV - digital".
You'll see a dialogue like this one:

<a href="/static/vlc_capture_mode_dialog.png">
	<img src="/static/vlc_capture_mode_dialog.png" width=704 height=568></img>
</a>

You can see that I already filled in the frequency that <code>w_scan</code>
found. You'll probably need to change that. Also be sure to check the
"DVB-T" option. Then hit play and we're done! Right? Well, sadly not.
If you're lucky, your antenna is already set up correctly and you're
getting a good signal. If not, you'll probably have to tinker around
with your antenna placing. Antenna's are however out of the scope of
this post, so you'll have to find that out yourself. Good luck!

_A small note for all Dutch readers: when I started to tinker around
with this, I found out that Digitenne (KPN) will start to move
from DVB-T to DVB-T2, the second generation of DVB-T. DVB-T2 will
start to roll out October 2018, starting with the northern parts of
the Netherlands. DVB-T2 will allow for HD TV, but is not supported
by any RTL2832U-based receivers as far as I know._
