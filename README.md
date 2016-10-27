script.kodi.hue.ambilight
=========================

# No Longer Maintained
I wrote this a while ago to explain a bit, but didn't post because it sounded angsty. I was silent for too long, so here goes nothing:

I've come to the difficult decision to discontinue my work on this addon. This comes down to a few reasons which I'll list below. I welcome anyone to fork the code base to enhance or fix things, but I will not be able to dedicate any time to this project anymore. It looks like @koying has forked and made some fixes, maybe check out his...

## I've Moved!
I recently moved to a new apartment and no longer have a Hue light setup. The living room where my Kodi setup is has built-in lighting features so I'll likely have to pursue Z-Wave for my lighting automation needs.

## Ambilight ¯\\\_(ツ)_/¯
I originally forked this project from @cees-elzinga's original work to enhance the Theater mode, as that was all the functionality I was looking for. The Ambilight mode that was already present in the code came as a "bonus", but it has some problems of its own:

- ```xbmc.RenderCapture()``` doesn't work well on all platforms, leading to lots of "this isn't working" bug reports with no resolution
- The capture, analyze, send command run-loop is CPU-hungry, and just not all that pretty. This type of stuff should be done in native code, not a scripting language like Python
- *Ambilight is hard to do right.* Try as I might, I still don't fully understand the color bucketization and frequency counting, or the hue, saturation, and brightness shift calculations that I inherited from the original codebase enough to properly debug, or apply enhancements that are requested (also the color accuracy is greatly effected by the Hue bulbs themselves (see section below)).
- *The current implementation only analyzes the full screen as one "zone"*. Other ambilight projects divide the screen/edge of the screen into many discrete parts and extend the dominant color in each zone to a corresponding light, which in my eyes, is the *right* way to do ambilight. Very rarely in movies/TV will you have a single color that is representative of the whole frame. To create immersion through the illusion that your screen is larger than it is by projecting the edge colors on the wall you really need a 30+ individually addressable lights on a wired strip.
- *Philips Hue is not good for Ambilight.*
  - They're slow :thumbsdown:. [Philips themselves](http://www.developers.meethue.com/documentation/core-concepts) advise a rate limit of 10/second light commands, and 1/second for group commands . This isn't an arbitrary limit, but rather a function of the ZigBee radio they use to communicate, as it has a high latency and low data rate. For Ambilight to keep in sync with your content, you need at minimum 24 updates/second (for a single light), and more if you start doing multiple zones (which would be preferable).
  - Color accuracy = :poop: They don't have the proper color gamut to match most TV's. From that same [Philips link](http://www.developers.meethue.com/documentation/core-concepts), they describe the color gamut or range of colors that their lights can produce in a chart that is embedded below and compared to the standards for TV color gamut. Most Philips Hue bulbs fall under the "B" gamut, which is quite restricted in the green sector as compared to the standard TV gamuts. Not only does this effect the bulb's ability to recreate colors in the green segment, but it also hinders its accuracy to colors within its gamut as well. This is a design choice on the part of Philips, as most of their bulbs do not use truly-green LED's, but rather a [proprietary green-ish white LED](https://plus.google.com/photos/107696725527584609973/albums/5806291983792940817). Without a gamut thats in the same neighborhood as your TV, even the most perfect math to convert the colors you see on the screen to the color(s) of the bulb will likely not match the colors you expect to see. *One notable exception to the gamut issue in the Philips Hue world exists: the Philips Hue Lightstrips have either "A" or "C" gamuts due to their true RGB primary LED's, which come much closer to the TV gamuts. UPDATE: (Fall 2016) [New Philips Hue Color & Ambiance bulbs](http://www.ifa.philips.com/news/lighting/philps-hue-new-lamps-and-bulbs) claim to have better color rendering.*

    | Hue Gamuts | TV Gamuts | 
    | --- | --- |
    | <img src="http://www.developers.meethue.com/sites/default/files/gamut_0.png" width="350" height="400" /> | <img src="https://dotcolordotcom.files.wordpress.com/2012/12/rec2020-vs-rec709-001.png" width="350" height="400" /> |

There are some great alternatives out there for Ambilight. Many of them are more advanced than this ever was, and certainly better maintained. Some even support Hue and XBMC/Kodi, like [Hyperion](https://github.com/tvdzwan/hyperion).




## In Short
This was fun. Maybe I'm wrong about Hue being bad for ambilight. I learned a lot and made some good changes, but I'm unable to continue developing something that I can't really use.

## Original README.md Below:

script.kodi.hue.ambilight
=========================

A Kodi add-on that controls Philips Hue lights. In "Theater mode" the add-on dims the the Philips Hue lights as soon as a movie starts playing, and turns the lights back on once the movie is done. "Ambilight mode" turns your Philips Hue lights in a room-sized ambilight.

0.8 Beta 2 Release
==================
Check out the pre-release version in the [Releases](https://github.com/michaelrcarroll/script.kodi.hue.ambilight/releases) section. Lots of big improvements, and should behave better than 0.7 and older versions. It might be a good idea to clear your settings before starting to use the 0.8 releases.

Support
-------
This is a side project for me, and as such I'll update it when and if I have time . I'll happily respond to issues and feature requests (provided that they are detailed enough to realistically debug your problem (**LOGS**)). Since this is based on @cees-elzinga's original work, I've only modified some of the functions to enhance the features. I don't personally use the ambilight feature, and therefore havent done much testing with it... Again, happy to try and help out, but don't expect super-quick responses!

Please fork and enhance! Pull requests welcome!

Debugging
---------
Please turn on Debug Logging through the addon (Configure -> Advanced Settings -> Debug Logging) and follow the procedure at http://kodi.wiki/view/Log_file/Easy to upload a log file. Provide a link to your logfile in the issue.

Installation
------------

The add-on depends on the Kodi add-on "requests" for the ambilight mode.

**Kodi add-on script.module.requests**

 - Download the add-on as a ZIP file from https://github.com/beenje/script.module.requests
  - (Right click on the "ZIP" icon and select "Download Linked File").
 - Open Kodi
 - Go to `System -> Settings -> Add-ons -> Install from zip file`
 - Select the zip file.

**Kodi add-on script.kodi.hue.ambilight**

 - Download the add-on as a ZIP file from the top of this page
   - (Right click on the "ZIP" icon and select "Download Linked File")
 - Open Kodi
 - Go to `System -> Settings -> Add-ons -> Install from zip file`
 -  Restart Kodi and configure the add-on:
   - `System -> Settings -> Add-ons -> Enabled add-ons -> Services -> Kodi Philips Hue`
   - Run `Start auto discovery of bridge IP and User`.

Note for Raspberry Pi users:

 - Save the add-on configuration by exiting Kodi before shutting down the Pi completely
 - Ambilight mode doesn't work on a Raspberry Pi due to the way it renders video

Release history
---------------
  * 2015-11-02 v 0.7.2 Minor update, attempting to resolve autodiscover issues
  * 2015-07-26 v 0.7.1 Updated Icon, code refactor, bugfixes, better group performance, handling of "pause during screen refresh rate change" setting
  * 2015-01-15 v 0.7.0 Fixed Kodi references, added paused brightness override (changes beginning here by @michaelrcarroll)
  * 2014-01-12 v 0.6.2 Minor improvements
  * 2013-07-13 v 0.6.0 General improvements all around (by robwalch)
  * 2013-05-25 v 0.5.0 Debug logging, livingwhite lights
  * 2013-05-04 v 0.4.0 Advanced settings
  * 2013-04-25 v 0.3.6 Custom dimmed brightness in theatre mode
  * 2013-04-02 v 0.3.4 Ambilight is more responsive
  * 2013-04-01 v 0.3.3 Rename to script.xbmc.hue.ambilight
  * 2013-02-25 v 0.3.1 Improved handling for grouped lights
  * 2013-01-27 v 0.1.0 Initial release

TODO:
-----
  * Add: saturation override so that full light parameters can be overridden
  * Fix: lights may turn on if they are already off, need better state checking
  * Add: setting to customize what is considered a "short" video
  * Add: *experimental* auto undim when movie reaches credits (does not always work, may ruin your movie-watching experience)
  * Refactor: clean up a lot of the old code that's still in the addon, but not necessary
  * Add: [OpenHAB](http://github.com/openhab/openhab) support for a grouped switch endpoint (to turn off a set of lights that are controlled by something other than Philips Hue... i.e. Z-Wave)
  * Publish: get addon onto the official Kodi addon repository
