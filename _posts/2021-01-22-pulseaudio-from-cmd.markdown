---
layout: post
title:  "Linux: Switch sound output source through PulseAudio in command-line"
date:   2020-04-29 17:00:00 +0100
categories: linux
---

I'm currently running Ubuntu 20.04.1 LTS an when working from home, I found that switching sound output from one device to another is a pain. One moment I listen to music using my headphones, the other I want to push the sound through the external speakers. Even if I plug any sound device in and select it, the source seldom changes and I cannot control the volume either. I was looking for something a bit more comfortable, than navigating to 'settings'->'sound' and choosing the right device. But Linux is all about command-line isn't it? Well, behold PulseAudio CMD! :)

{% highlight bash %}
wacek@propwash:~ > pacmd list-sinks
{% endhighlight %}

will list all of the output sinks, which we can normally choose through the UI. I mainly use external Jabra headphones and laptop's internal speakers.
Let's note their names from the output:

{% highlight bash %}
5 sink(s) available.                   
  * index: 1
        name: <alsa_output.usb-0b0e_Jabra_Link_370_745C4B821E72-00.iec958-stereo>
        driver: <module-alsa-card.c>
(...)
    index: 4 
        name: <alsa_output.pci-0000_00_1f.3.analog-stereo>
        driver: <module-alsa-card.c> 
{% endhighlight %}

Now it's just a matter of switching them using this command:

{% highlight bash %}
wacek@propwash:~ > pacmd set-default-sink alsa_output.usb-0b0e_Jabra_Link_370_745C4B821E72-00.iec958-stereo
wacek@propwash:~ > pacmd set-default-sink alsa_output.pci-0000_00_1f.3.analog-stereo
{% endhighlight %}

Awesome! But I can't be expected to remember those identifiers, right? Time for some bash aliases.

{% highlight bash %}
wacek@propwash:~ > echo "alias headphones='pacmd set-default-sink alsa_output.usb-0b0e_Jabra_Link_370_745C4B821E72-00.iec958-stereo'" >> ~/.bashrc
wacek@propwash:~ > echo "alias speaker='pacmd set-default-sink alsa_output.pci-0000_00_1f.3.analog-stereo'" >> ~/.bashrc

wacek@propwash:~ > source ~/.bashrc
{% endhighlight %}

Let's give it a try!

{% highlight bash %}
wacek@propwash:~ > headphones
>>> music goes through headphones now >>>
wacek@propwash:~ > speaker
>>> music goes through the laptop's speaker >>>
{% endhighlight %}

Finally, I don't have to touch the mouse to set the sound output! Time to go back to work! :)
