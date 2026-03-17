# Intel-WiFi-BT-and_Audio-Fix-Tahoe
How to fix Intel Wifi (tested with AX210/1675) and Bluetooth with OCPL-Mod by laobamac

Hi, I was trying to fix and Wifi and Bluetooth on my Thinkpad T480 running macOS Tahoe 26.3.1 with an Intel AX210 and I got a little confused and overwhelmed by all the posts an guides online so I thought to write a guide myself based on what worked for me.
I'll upload all the files that I used and also the links from where I had downloaded them. Please note that I'll follow the hierarcy of the folders and so on, and especually that the config.plist that you will find it's mearly for reference for the things used in this guide, nothing more.

Disclaimer:
Please note that is's my first guide/repo that I wrote/create so if I do something wrong or if I forget to mention or credits someone else's work that I used to make this work please let me know and I'll make it right ASAP.

With that out of the way, let's get into it: please note, I know it's kind of ironic, that you'll need a working internet connection for running OCLP-Mod, so make sure that you have some kind of internet connectivity working on your Hackintosh. In my case I used itwlm with Heliport (and I disabled the kext from my config.plist just before rebooting, it will be more clear as I explain along the guide) but it may also be a good alternative usb tethering, if you had an android phone you can use HoRNDIS from TomHeaven, everything will be linked down below.
I will not focusing at all about the audio, has if you had it working before with AppleALC or similar everything will be handled automatically in the last step of the guide by OCLP-Mod without needing the user to do anything.
Necessary files:
  - OCLP-Mod by laobamac from https://github.com/laobamac/OCLP-Mod/releases
    I used version 3.1.5 as it is the latest at the time of writing,

  - the following kexts loaded in this exact order (if you use ProperTree just     drag the kext entries following this scheme and make sure that are ordered     properly) 
      For the WiFi:
		    - IOSkywalkFamily.kext
		    - IO80211FamilyLegacy.kext
		    - AirPortBrcmNIC.kext (in Propertree the entry will be loaded                    automatically as it is part of IO80211FamilyLegacy.kext)
		    - AMFIPass.kext (I'm using version 1.4.1))
		    - AirportItlwm.kext (v2.3.0_stable_Ventura)

      For the Bluetooth:
		    - BlueToolFixup.kext
		    - IntelBTPatcher.kext
		    - IntelBluetoothFirmware.kext

If you're using ProperTree I'll suggest to use the OC Snapshot feature (or OC Clean Snapshot feature if you had previous wifi/bt kexts that you deleted from previous setup or attempts)

While you're messing in your config.plist you'll also need some other modifications:
   - in Kernel->Block youìll need to enable:
     com.apple.iokit.IOSkywalkFamily

   - in NVRAM 7C436110-AB2A-4BBB-A880-FE41995C9F82 you'll need to:
       - disable SIP, so set csr-active-config to 03080000 (and make sure that          the type is set to "Data" where you input 03080000);
       - add the following boot-args: -amfipassbeta -ipc_control_port_options=0

Please not that in my case, from a previous attempt of fixing wifi/bt I also have the following boot-args: lilubetaall -ibtcompatbeta
I don't know if they are necessary, honestly I forgot to remove them and given the fact that everything seems to be working... I still didn't test my system without them. Probably my advice will be to fist test it without, and if something doesn't work try to add them (one per time or both, your choice).

Now, the guide itself: your starting point must be a working internet connection and SIP already disabled (see above).
If you have these you're good to go.
Mount your EFI partition and copy the kexts in EFI->OC->Kexts.
Then open ProperTree or you favourite plist editor: add your kexts there (e.g. ProperTree OC Snapshot). Then go to Kernel->Block as explained before and enable the entry I mentioned.
Then go to NVRAM->Add and set the boot-args.
If you don't have any conflicting wifi kexts enabled such ad itlwm that you're using for having internet access, you can save the config.plist and close it. On the other hand, this shuldn't be strictly necessary, but I'd like to disable the wifi kext that I'm currently using just before the reboot, just in case, as we still need internet.

Now we finally can open OCLP-Mod: unfortunatly the whole app it's in chinese, I used Google Translate, but you can follow along the screenshots that I'll attach.

<img width="615" height="331" alt="Screenshot 2026-03-17 alle 12 46 11" src="https://github.com/user-attachments/assets/6af13bb6-7a5a-4290-8c27-078d1f164ca8" />

First you want to make sure that you did everything so far correctly: clicking that button will show you if you can apply patches or not.

<img width="615" height="331" alt="Screenshot 2026-03-17 alle 12 47 58" src="https://github.com/user-attachments/assets/fcacf959-4789-43a6-a7e1-92d3dcd27641" />

If you see the two buttons inside the red square (1) active you're good (mine has the first greyed out because I've already patched my system. The yellow square (1.2) tells you what patched are avaible for your system and/or the reason or reasons why you can't patch yet, as SIP enabled, for example.

The green square (2) is the back button.

If you have the buttons working, go back and click the KDK button in the bottom left corner.

<img width="615" height="331" alt="Screenshot 2026-03-17 alle 12 52 49" src="https://github.com/user-attachments/assets/4d5ec3db-a03a-48d1-8d8d-ab5333dcbb31" />

Then you will see this page:

<img width="813" height="615" alt="Screenshot 2026-03-17 alle 12 54 32" src="https://github.com/user-attachments/assets/2499db1f-9e05-42ae-95a6-548b6e7fae00" />

You'll need tho choose the matching macOS 26 version (I tested 26.3.1) and click the button on the bottom right, as showed.

Then it will ask you where you want to download the KDK, you can choose any location you'd like.

<img width="378" height="247" alt="Screenshot 2026-03-17 alle 12 57 01" src="https://github.com/user-attachments/assets/f9d858a7-1664-4d6a-90d4-9b50ae0c5874" />

Then it will start the download. When it finishes you can close the KDK windows(s) and go to the directory that you choose earlier. There you'll find a .dmg: open it and install the .pkg inside. When it finishes installing, ahead back to the main page of OCLP-Mod and click again the first button.

<img width="615" height="331" alt="Screenshot 2026-03-17 alle 12 46 11" src="https://github.com/user-attachments/assets/3262a2cb-dc44-413d-a5fc-7dbd571b7301" />

Then you can proceed installing the patches clicking the first button.
<img width="615" height="331" alt="Screenshot 2026-03-17 alle 12 46 11" src="https://github.com/user-attachments/assets/a1993a03-6126-4663-8640-a0dc1e6dce46" />

I'm not remembering if it will ask for confirmation (I'll check on another laptop when I get the chance) and let it to do is magic.
When it finishes a pop up will show up asking you to reboot the system, it's the blue button in the right bottom corner (I'll try to attach a screenshot). Now as I said before, if you were using something like itlwm and it is still the moment, BEFORE rebooting to disabling it in the config.plist (or deleting the kext in the EFI and removing the entry in the config.plist: given that the installation has a chance of going wrong I'd prefer just disabling it in case you need it again). Now save the plist if you didn'to do already and reboot.
Now it's very important that you reset the NVRAM so that macOS will load the changes (hopefully) without issues so: say yes to reboot, when you arrive in OpenCore trigger the reset NVRAM (if you can't see the option because it's hidden just press the space bar). The system will reboot and you can go ahead and test the wifi, bluetooth and audio out.

P.S. If your computer as multiple bootloaders that have the tendencies to overthrow OpenCore in the bios boot menu, after resetting the NVRAM just go to the bios and fix the order.

And that's should be all, if something isn't clear enough or maybe you need help for something, I can't guarantee that I'll be able to help but I'll be happy to try.





