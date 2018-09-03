Script to patch AppleHDA binary for osx10.7/10.8

I've recently written a script to auto-patch the AppleHDA binary to support hackintosh codecs under OSX 10.7 and 10.8.

[b]Background:[/b]
Historically the binary patching of AppleHDA was simple, merely requiring replacement of the best matching codec id found in the driver with the codec id for your hardware.  In more recent releases of AppleHDA, the compiler is better optimizing its code and patching now often requires patching the AppleHDA binary in multiple places.  For some codecs, this has resulted in a patch that is different depending upon which release you're using.  The old strategy of using a perl one-liner to publish&distribute these patches thus became less practical than ever, leading to confusion, seemingly conflicting information and lists of questionable patches for one to try.  This script is meant to replace that.

[b]The solution[/b]
The script is able to dynamically figure out which extra comparisons should be patched to get the matching to work properly.  The script contains a static list of hackintosh codecs (referred to as target ids in the script) and patch ids (the codecs built-in to AppleHDA which we have successfully used with hackintoshes).

Every OSX update that installs a new version of AppleHDA (that is, most of them) requires re-patching your system's AppleHDA.  That is unless you resort to other means such as rolling back your AppleHDA driver (not recommended).

I've included support for the codecs I have, those found in HDA wizard, as well as many more from the thread http://www.insanelymac.com/forum/topic/280468-applehda-patching-in-mountain-lion/

[b]What this thread is not:[/b]
A complete solution for getting your AppleHDA audio working.
In addition to patching your AppleHDA binary, you need to also configure xml for your codec.  (A pathmap and pin configuration as it's referred to in the drivers).  See http://www.projectosx.com/forum/index.php?showtopic=465
Thirdly, you need a method to inject into your ioregistry the layout-id used by your pathmap and/or also some flags for working HDMI audio.  These edits can be done quite concisely via DSDT.  Alternatively you may also inject them into your ioregistry thru other means such as a kext.  Again see the above thread.

[b]Usage:[/b]
Run this script from a terminal window, supplying the codec name or the hexadecimal codec id of the codec found on your hackintosh.  If your codec is in the database (patch-hda-codecs.pl) the script will patch things for you automatically.  Alternatively you can click on the patch-hda shell script from the finder.  

[b]Implementation details:[/b]

The script accomplishes patching of the codec comparisons by zeroing out the codec ids found as operands to the applicable comparison instructions.  This is easier to do in a script than other patch methods such as jumping over unwanted comparisons.  The solution in this script is easier to implement as the script can avoid parsing AppleHDA's match routine's instructions to work successfully.  (see ati-personality.pl for an example of that more complicated kind of processing).

[b]How to help[/b]
Please DO contribute updates to this script, especially the patch-hda-codecs.pl list, to support new hardware.  I do not have a lab of hardware to test against; I rely upon users to fill in the blanks as to what does/doesn't work.  I do not have time to test codecs for hardware I do not have; I barely had time to write this post.  Feel free to post revised versions of patch-hda-codecs.pl here.

Please DON'T post to this thread to beg for a solution for your codec.  It takes quite a bit of tinkering to make a working pathmap&pinconfig for a new codec.  And there are better threads for such discussion such as: http://www.insanelymac.com/forum/topic/280468-applehda-patching-in-mountain-lion/

Please DON'T PM me for tech support either, I likely don't even have time to answer.

This script resembles the concept of [url="http://www.insanelymac.com/forum/index.php?showtopic=266531"]HDA wizard[/url] but instead of being GUI focused, it focuses on getting the AppleHDA binary patching done automatic&right across osx releases.

Examples, under 10.7:

[code]
% ./patch-hda.pl 111d7675
Patching AppleHDA codec 11d41984 with 111d7675
1 codec range comparison(s) to patch
Patching range comparison 11d41983
AppleHDA patched successfully.[/code]
under 10.8:
[code]
% ./patch-hda.pl 111d7675
Patching AppleHDA codec 11d41984 with 111d7675
No codec range comparisons require patching
AppleHDA patched successfully.
% ./patch-hda.pl 10ec0889
Patching AppleHDA codec 10ec0885 with 10ec0889
No codec range comparisons require patching
AppleHDA patched successfully.
% ./patch-hda.pl 'Realtek ALC889+inputs'
Patching AppleHDA codec 11d41984 with 10ec0889
No codec range comparisons require patching
AppleHDA patched successfully.
% ./patch-hda.pl
Usage: patch-hda.pl <codec-id>|<codec-name>
Examples: patch-hda.pl 111d7675
Examples: patch-hda.pl 'IDT 7675'
Supported codecs:
Target          Patch           Name
Codec ID        Codec ID
-------------------------------------------
111d7675        11d41984        IDT 7675
111d76d1        11d41984        IDT 76d1
111d76e0        11d4198b        IDT 76e0
10ec0269        11d41984        Realtek ALC269
10ec0889        10ec0885        Realtek ALC889
10ec0889        11d41984        Realtek ALC889+inputs
10ec0892        10ec0885        Realtek ALC892
10ec0662        10ec0885        Realtek ALC662
10ec0882        10ec0885        Realtek ALC882
10ec0883        10ec0885        Realtek ALC883
10ec0887        10ec0885        Realtek ALC887
10ec0888        10ec0885        Realtek ALC888
111d7603        11d41984        IDT 7603
111d7605        11d4198b        IDT 7605
11d4989b        11d4198b        ADI AD2000B
11060441        11d41984        VIA VT2021

%[/code]

Note: Since there is debate as to which codec to use for alc889, two choices are offered above.

B.C.
bcc24x7@gmail.com
