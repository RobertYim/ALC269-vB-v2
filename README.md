# [How To Patch AppleHDA.kext][1]

## Using AppleALC and Lilu

### 1. Place [AppleALC.kext][5] and [Lilu.kext][6] into Clover/Kext/Other

### 2. [Inject Audio ID 4 in Clover or edit DSDT][4]

#### legacy content

<details>
	<summary>
		Show
	</summary>

    1. Edit AppleHDA.kext/Contents/PlugIns/AppleHDAHardwareConfigDriver.kext/Contents/Info.plist
    Add following codec to the key IOKitPersonalities / HDA Hardware Config Resource / HDAConfigDefault:
    ```

    <dict>
    	<key>AFGLowPowerState</key>
    	<data>
    	AwAAAA==
    	</data>
    	<key>Codec</key>
    	<string>Mirone - Realtek ALC269</string>
    	<key>CodecID</key>
    	<integer>283902569</integer>
    	<key>ConfigData</key>
    	<data>
    	AUccEAFHHQABRx4TAUcfkAGHHCABhx2QAYce
    	gQGHHwIBtxwwAbcdEAG3HqABtx+QAhccQAIX
    	HRACFx4hAhcfAgFHDAI=
    	</data>
    	<key>FuncGroup</key>
    	<integer>1</integer>
    	<key>LayoutID</key>
    	<integer>3</integer>
    </dict>
    ```
    2. Patch Platform.xml and Layout3.xml
    Copy Platform.xml.zlib and layout3.xml.zlib to AppleHDA.kext/Content/Resources/
    ```
    zlib.pl usage:
    inflate：
    perl zlib.pl inflate layout3.xml.zlib > layout3.xml
    perl zlib.pl inflate Platforms.xml.zlib > Platforms.xml
    deflate
    perl zlib.pl deflate layout3.xml > layout3.xml.zlib
    perl zlib.pl deflate Platforms.xml > Platforms.xml.zlib
    ```

    3. Binary Patch

    [Clover patch][2] or manually:

    ```
    tools/patch-hda/patch-hda.pl -s /Path/to/AppleHDA.kext 10ec0269
    ```

    4. DSDT Patch

    Use layout 3 (edit DSDT or inject with Clover).

</details>

[1]: http://www.insanelymac.com/forum/topic/295001-guide-to-patch-applehda-for-your-codec/
[2]: http://www.insanelymac.com/forum/topic/298027-guide-aio-guides-for-hackintosh/page-2#entry2030060
[3]: http://www.insanelymac.com/forum/files/file/496-applehda-patcher/
[4]: https://github.com/acidanthera/AppleALC/blob/4f095a21877520d8d548a8f033d345ede8da175d/Resources/ALC269/Info.plist#L41-L43
[5]: https://github.com/acidanthera/AppleALC
[6]: https://github.com/acidanthera/Lilu
