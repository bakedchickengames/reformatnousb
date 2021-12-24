# Reformatnousb
Reformat from another partition without usb, skim through this doc for a general understanding of how to get this done.
OTHER LINKS [**1**](https://github.com/iidanL/InstallWindowsWithoutUSB/blob/main/Install%20Windows.bat) [**2**](https://superuser.com/questions/193912/install-windows-7-x64-from-a-separate-partition-on-same-hard-drive-no-dvd-usb) [**3**](https://www.tenforums.com/tutorials/84331-apply-windows-image-using-dism-instead-clean-install.html)

## General overview
* Check example at bottom to see an example. I posted the exact commands I used.
* **If you only care about installing the iso with no extra steps:** 
  * Extract iso using 7zip. It is now in a folder ex: C:\examplewindows.
  * in admin cmd: DISM /Apply-Image /ImageFile:C:\examplewindows\sources\install.wim /Index:4 /ApplyDir:E:\
    * Index: is telling which edition you will use. Index:4 is my win7 Ultimate. Use DISM /Get-WimInfo /WimFile:C:examplewindows\sources\install.wim to see which windows edition you want to install.
    * /ApplyDir:E:\ is the partition i want to install the windows on. 
  * in admin cmd: bcdboot E:\Windows
  * You must be on a different partition to do this. Then you are done. Rest of guide is for adding drivers and automating the username and password (OOBE)

* important terms: DISM, install.wim, autounattend.xml unattend.xml (answer files)
  * DISM is THE cmd command you will to do everything with the install.wim  
  * install.wim the file located in windows.iso that contains the windows image files
  * autounattend.xml and unattend.xml are used to make windows automatically configure a wide range of settings without you touching it. I used them mostly to skip the username/password phase of the install (they call this the OOBE, or Out-Of-Box Experience).
* Extract a windows.iso with 7zip into a folder (for this example C:\examplewindows). 
* Use DISM to mount the install.wim (to some folder, located, for this example, at C:\testmount). 
* Use DISM to /add-drivers or windows KB updates.
* USE WSIM or NTLite to make autounattend.xml and unattend.xml files so they automate setting up more windows settings for you.
* ?Use DISM to apply an autounattend.xml? im not really sure about this part. see below. ?this file automates everything before the OOBE? See autounattend.xml below for more info
* Copy your unattend.xml to the Panther folder (located, for this example, at C:\testmount\Windows\Panther). You'll have to make the folder since it won't exist. This file contains the information to skip the OOBE (Out-Of-Box Experience). Basically the steps where you enter username/password/time and date. You can skip it all using this.
* Use DISM to unmount the install.wim. DISM will then apply and save the install.
* You now have folder, for this example, C:\examplewindows which contains the extracted and modified windows.iso files. Save this folder for the future installs you wish to repeat
* You'll be using the install.wim that you modified using DISM to install windows
* Format a partition and install the unzipped and now modified iso (located, for this example, at C:\examplewindows\sources\install.wim) using DISM /Apply-Image to that partition.
* Run bcdboot on that partition you just installed windows and it is ready to go. This step is to make sure you can boot to it. The bootloader needs info of its existence and bcdboot adds the info to the System Partition. You can see the System Partition in winkey+r>diskmgmt.msc

* rest of the doc will be giving more detailed explanations

## [DISM](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/what-is-dism?view=windows-11)
* [/Apply-Image](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/dism-image-management-command-line-options-s14?view=windows-11#apply-image)
* [/Add-Driver](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/add-and-remove-drivers-to-an-offline-windows-image?view=windows-11)
* [/Add-Package](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/dism-operating-system-package-servicing-command-line-options?view=windows-11#add-package)
* [/Mount-Image /Unmount-Image](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/mount-and-modify-a-windows-image-using-dism?view=windows-11)
* [/Apply-Unattend](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/dism-unattended-servicing-command-line-options?view=windows-11#apply-unattend)

## install.wim
  1. This is the wim (Windows Image File) that you will ultimately use "DISM /Apply-Image" command to reformat a partition. Until then, you will modify the install.wim by
     1. Mounting it (DISM's way of essentially unzipping it so it can modify it)
        * ex: Dism /Mount-Image /ImageFile:C:\test\images\myimage.wim /index:1 /MountDir:C:\test\offline
          * they call it offline as opposed to online. online means the windows install that is currently running. some DISM commands like /Add-Driver can only be done with an offline image. You would have to use devmgmt.msc to install drives to the current install you are running, the online install.
          * /index:1 since install.wim is a compressed file, they put inside it multiple versions of windows (home, pro, ultimate, etc.) the index indicates which one you want to select. Check [**_here_**](https://www.tenforums.com/general-support/162980-what-index-number-how-do-i-find-thank-you-post2000764.html?s=ab6904756d100e190fc1593666d2cc3d#post2000764) for an example of how to view the indexes in install.wim and what it looks like
     2. Modifying it (with DISM /Add-Driver to add drivers and /Apply-Unattend if you have any offlineServicing changes in the autoaunattend.xml file)
     3. Unmounting it (to unmount and apply changes to the install.wim)

  2. **Explanation**  when you mount the install.wim and choose the index, you will specify a folder as the destination. inside that folder will look exactly like a C:\ drive of a fresh windows install. This is because the install.wim file is the DISM's method of compressing the windows.

     The install.wim will be in the sources folder of the mounted directory.

## Autounattend.xml and unattend.xml

Files used to automate setting up settings in windows so you dont have to. they just have to be placed in the right place

These are called answer files. The big difference between the two is that they go in different places because they are usedto automate different parts of the reformat process. [here](https://win10.guru/answer-file-autounattend-xml-or-unattend-xml/) for info. Autounattend is for Windows Setup (the part where you can delete and partition drives, format them and choose which drive to install windows to) and the unattend is for what they call OOBE (the part where you enter your username and password after the computer finishes Windows Setup and reboots)

I used [WSIM](https://docs.microsoft.com/en-us/windows-hardware/customize/desktop/wsim/windows-system-image-manager-technical-reference) in the Windows ADK to make an answer file (autounattend.xml and unattend.xml) you can use NTLite free version  [image of me installing wsim](https://i.imgur.com/UiTzA3v.png)

* Autounattend.xml goes to the root of install media supposedly, i just used dism to apply it. TBH I don't really know what im doing. i just know where the unattend.xml is supposed to go. Using DISM on a file called autounattend.xml im not sure is even right [read more here](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/dism-unattended-servicing-command-line-options?view=windows-11)
* DISM /Apply-Unattend used on an autounattend.xml should only have what they call the offlineServicing "configuration pass" for ONLINE installs [explanation here](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/how-configuration-passes-work?view=windows-11).
* Unattend.xml is copied into the %WINDIR%/Panther (for example if you mounted the install.wim to C:\testmount it would go to C:testmount\Windows\Panther)
  [here](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/windows-setup-automation-overview?view=windows-11#implicit-answer-file-search-order) for more information on why Panther folder

## put example of what I did with exact steps, will add images here later
```
::all these commands are done in cmd administrator
::extract the iso file with 7z
7z.exe x -y -oD:\win7 "D:\win7\Win7_Ult_SP1_English_x64.iso"

::index4 is win7 ultimate for my particular install.wim
::this is to open up the install.wim into a folder called test so I can modify it using DISM
DISM /Mount-Image /ImageFile:D:\win7\sources\install.wim /Index:4 /MountDir:D:\test

::these commands are for modifying the mounted install.wim. I added my aquantia ethernet driver, asmedia usb drivers, the nvme samsung 960 evo driver, and the usb 3.0 drivers to the install.wim since my z370 maximus x apex doesnt work with the default windows 7 drivers.
DISM /Image:D:\test /Add-Driver /Driver:"D:\shove onto usb\driver\aq620\aqnic620.inf"
DISM /Image:D:\test /Add-Driver /Driver:"D:\shove onto usb\driver\asmedia\asmthub3.inf"
DISM /Image:D:\test /Add-Driver /Driver:"D:\shove onto usb\driver\asmedia\asmtxhci.inf"
DISM /Image:D:\test /Add-Driver /Driver:"D:\shove onto usb\driver\Samsung_NVMe\secnvme.inf"
DISM /Image:D:\test /Add-Driver /Driver:"D:\shove onto usb\driver\usb\iusb3hub.inf"
DISM /Image:D:\test /Add-Driver /Driver:"D:\shove onto usb\driver\usb\iusb3xhc.inf"

::This is convenience for me. As opposed to a necessary step, like my windows 7 drivers for my particular motherboard. I integrated the windows kb4474419 update to get the driver signatures to be recognized. Otherwise I would do it manually every install. Without this update I can't install nvidia drivers and my aquantia driver won't work because it has to verify the driver signature since it needs this update for some SHA thing related to it. I don't really know exactly. I just know I need this and I can integrate it with DISM instead of manually each time I install.
DISM /Image:D:\test /Add-Package /PackagePath:"D:\shove onto usb\windows6.1-kb4474419-v3-x64_b5614c6cea5cb4e198717789633dca16308ef79c.msu"

::this is to turn LUA off
DISM /Image:D:\test /Apply-Unattend:D:\autounattend.xml

::this is to save the changes I did
DISM /Unmount-Image /MountDir:D:\test /Commit
```
```
::all these commands done in cmd administrator
::this is the line that installed windows 7 on my E drive
DISM /Apply-Image /ImageFile:D:\win7\sources\install.wim /Index:4 /ApplyDir:E:\

::this adds boot info to the system partition so you can boot to it
bcdboot E:\Windows
```
<details>
<summary>my autounattend.xml and unattend.xml (i used the same file since i wasnt really sure about the autounattend.xml situation)</summary>

[autounattend.xml](https://github.com/bakedchickengames/reformatnousb/blob/main/autounattend.xml) [unattend.xml](https://github.com/bakedchickengames/reformatnousb/blob/main/unattend.xml)

images FOR what i did personally on my windows 7. the following images are the settings used to skip username/password. I placed the unattend.xml file in %WINDIR%\Panther before first boot
I will show 2 images for the first setting.
 * first will be how i added it to the unattend.xml (finding the relevant setting in the bottom left "Windows Image" section
 * second will be what i entered in the "Answer File section"
Afterwards you can just see the configuration pass by looking at the "Answer File" section i.e. Microsoft-Windows-Shell-Setup is under 4 specialize. so to add that into the "Answer File" section you would right click Microsoft-Windows-Shell-Setup in the "Windows Image" section and select "Add Setting to Pass 4 specialize"
<details>
 <summary>computer name and Product Key</summary>

This is some generic product key to just skip this step [here](https://docs.microsoft.com/en-us/windows-server/get-started/kms-client-activation-keys) to find yours
![1](https://i.imgur.com/Zbb1JqE.png) ![2](https://i.imgur.com/gAW1knm.png)
For some reason to skip product key section you have to add it in this section here. Also for some reason the Computer Name is placed here. Probably because it applies to the entire computer its not set in the OOBE. [Type a computer name](https://i.imgur.com/dBi1Znf.png) that's the part it skips
</details>
<details>
 <summary>skipping Language section</summary>

![2](https://i.imgur.com/yHiP1bg.png)
</details>
<details>
 <summary>skipping time zone setup</summary>

![2](https://i.imgur.com/yV3vseQ.png)
</details>
<details>
 <summary>skipping EULA page, network location setup, and some protect your pc setting im not even sure is in win7</summary>

[protect your pc](https://docs.microsoft.com/en-us/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup-oobe-protectyourpc)
![2](https://i.imgur.com/CfjrD7R.png)
</details>
<details>
 <summary>skip the account setup phase</summary>

![2](https://i.imgur.com/CfjrD7R.png) ![password section](https://i.imgur.com/7On3nq9.png)
</details>
<details>
 <summary>rest of the features i changed </summary>

![image](https://user-images.githubusercontent.com/34696110/147355641-a7cdcb7c-f015-45d2-bcee-6650b8efe599.png)
![image](https://user-images.githubusercontent.com/34696110/147355647-f8f44db4-efbf-4e15-9931-72a2bf68cb3c.png)
changing the defaultthemesoff will make it create a new theme with classic. the first ease of access theme. ThemeName is where you put the location of the theme you want to use. ex: "C:\Windows\resources\Ease of Access Themes\classic.theme"
 </details>

<details>
 <summary>ill show the rest of the Window PE configuration pass i did, but im pretty sure this did nothing for me</summary>

![image](https://user-images.githubusercontent.com/34696110/147355548-b9b987e0-6354-43b8-843a-d335f93f004f.png)
![image](https://user-images.githubusercontent.com/34696110/147355561-1afd79c4-1438-48af-b828-7723c250bd7b.png)
![image](https://user-images.githubusercontent.com/34696110/147355573-00b46e80-1abb-47f5-9bcb-6101019a7801.png)
![image](https://user-images.githubusercontent.com/34696110/147355577-c740b712-0618-4354-aa94-4101f9b18522.png)
![image](https://user-images.githubusercontent.com/34696110/147355582-1e30975e-b247-4c77-804b-bd3e33d83497.png)

</details>

</details>

