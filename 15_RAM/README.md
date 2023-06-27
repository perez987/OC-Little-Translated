# Fixing falsely reported RAM speed in macOS

## About
I recently acquired a Lenovo T490 Laptop. While checking the installed RAM, I noticed in System Profiler the the reported RAM speed was 2400 MHz:

![2400](https://github.com/5T33Z0/OC-Little-Translated/assets/76865553/e068bb0e-d9e7-4e0f-a591-50a6ba992ac4)

But in Windows it was reported correctly, running @2666 MHz:

![MemSpeed](https://github.com/5T33Z0/OC-Little-Translated/assets/76865553/41e21b50-d19c-4ac8-9c2e-5fbd615cfe01)

Since in Wintel systems RAM speed is are handle by the BIOS, I assume there's a memory speed reporting issue in macOS.

## Fix
In order to fix the falsely reported memory speed in macOS, yo *can* do the following. I say *can* because this fix a purely cosmetic:

## Gathering RAM data

### Using Windows
1. Run Windows, open a command prompt and enter:
	
	```bash
	wmic memorychip list full
	```
2. <details>
<summary>The output shoul look like this (click to reveal):</summary>

	```
	BankLabel=BANK 0
	Capacity=8589934592
	DataWidth=64
	Description=Physical Memory
	DeviceLocator=ChannelA-DIMM0
	FormFactor=12
	HotSwappable=
	InstallDate=
	InterleaveDataDepth=
	InterleavePosition=
	Manufacturer=Samsung
	MemoryType=0
	Model=
	Name=Physical Memory
	OtherIdentifyingInfo=
	PartNumber=M471A1K43BB1-CTD
	PositionInRow=
	PoweredOn=
	Removable=
	Replaceable=
	SerialNumber=00000000
	SKU=
	Speed=2667
	Status=
	Tag=Physical Memory 0
	TotalWidth=64
	TypeDetail=128
	Version=

	BankLabel=BANK 2
	Capacity=8589934592
	DataWidth=64
	Description=Physical Memory
	DeviceLocator=ChannelB-DIMM0
	FormFactor=12
	HotSwappable=
	InstallDate=
	InterleaveDataDepth=
	InterleavePosition=
	Manufacturer=Samsung
	MemoryType=0
	Model=
	Name=Physical Memory
	OtherIdentifyingInfo=
	PartNumber=M471A1K43CB1-CTD
	PositionInRow=
	PoweredOn=
	Removable=
	Replaceable=
	SerialNumber=397269CB
	SKU=
	Speed=2667
	Status=
	Tag=Physical Memory 1
	TotalWidth=64
	TypeDetail=128
	Version=
	```
</details>
	
3. Save the data as a .txt file so you can access it later from within macOS

### Using Linux (recommended)
Using Linux is recommended because the command used under Windoesdoesn't show the `AssetTag`. Listed below are the instructions to to tun a live version of Linux from a USB flash drive directly from an .iso

1. Prepare a USB Flash Drive with [Ventoy](https://github.com/ventoy/Ventoy)
2. Download the .iso for a Linux distro of your choice (like Ubuntu or Zorin, etc.)
3. Copy the .iso to your Ventoy USB stick
4. Reboot from USB flash drive
5. In the ventoy menu select your Linux distro and click on "Normal Boot"
6. Select the "Try" option instead of the "Install" option 
7. Once you're reached the Desktop, run Terminal and enter:
	
	```bash
	sudo dmidecode -t memory
	```

8. <details> 
	<summary>The output shoul look like this (click to reveal):</summary>
	
	```
	dmidecode 3.2
```
</details>

9. Save the data as a .txt file on a USB flash drive so you can access it later from within macOS

> **Note**: It seems that macOS displays the "Configured Memory Speed" value instead of the "Speed" value. And since the Configured Memory Speed is 2400 MT/s in my case, that's the reason why the reported speed is lower in macOS than in Windows. But "Configured Memory Speed" actually refers to the *actual* speed the RAM is running at and not the maximum possible speed it is capable of. This can be changed in BIOS but Laptop BIOSes usually don't let you configure RAM speeds. Mhh…

## Add the memory data to your config.plist 
:warning: Read OpenCore's "Documentation.pdf", chapter 10.4: "Memory Properties" to get famiiar with the available parameters *before entering any data into your config.plist!* Because some parameters use different values in OpenCore and also have to be converted from hex to decimal.

1. Reboot into macOS
2. Run OpenCore Auxiliary Tools, mount the EFI and open your `config.plist`
3. Navigate to `PlatformInfo/Memory`
4. Enable `CustomMemory` (disbaling it ignores the whole `Memory` section)
5. Enter the relevant data you gathered earlier and ensure it follows OpenCore's standards: ![CstmMem](https://github.com/5T33Z0/OC-Little-Translated/assets/76865553/b40dd4e2-aca6-454f-86bd-75ab8faf78c6)
6. Save your config and reboot

## Check the results
- Run System Profiler
- Check "Memory" Section: <br>![2667](https://github.com/5T33Z0/OC-Little-Translated/assets/76865553/338f44f4-f7db-4bbf-91ca-53ec9afbf187)
- Done

## Addtional Info
- [New Memory Properties Section](https://www.insanelymac.com/forum/topic/345520-opencore-063-new-memory-properties-section/) by miliuco