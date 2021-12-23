# Reformatnousb
Reformat from another partition without usb, skim through this doc for a general understanding of how to get this done.

## General overview

* DISM, install.wim, answer files (autounattend.xml unattend.xml)
  * DISM is THE cmd command you will to do everything with the install.wim  
  * install.wim the file located in windows.iso that contains the windows image files
  * autounattend.xml and unattend.xml are used to make windows automatically configure a wide range of settings without you touching it. I used them mostly to skip the username/password phase of the install (they call this the OOBE, or Out-Of-Box Experience).
* Extract a windows.iso with 7zip into a folder (for this example C:\examplewindows). 
* Use DISM to mount the install.wim (to some folder, located, for this example, at C:\testmount). 
* Use DISM to /add-drivers or windows KB updates.
* USE WSIM or NTLite to make autounattend.xml and unattend.xml files so they automate setting up more windows settings for you.
* Use DISM to apply an autounattend.xml with only offlineServicing (i only used this to turn the microsoft LUA settings off).  
* Copy your unattend.xml to the Panther folder (located, for this example, at C:\testmount\Windows\Panther). You'll have to make the folder since it won't exist. This file contains the information to skip the OOBE (Out-Of-Box Experience). Basically the steps where you enter username/password/time and date. You can skip it all using this.
* Use DISM to Unmount the install.wim. DISM will then apply and save the install.
* You now have folder, for this example, C:\examplewindows which contains the extracted and modified windows.iso files. Save this folder for the future installs you wish to repeat
* You'll be using the install.wim that you modified using DISM to install windows now
* Format a partition and install the unzipped iso (located, for this example, at C:\examplewindows\sources\install.wim) using DISM /Apply-Image to that partition.
* Run bcdboot on that drive and it is ready to go

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

Files used to automate settings in windows so you dont have to. they just have to be placed in the right place

They call these answer files. The big differences to know is that they go in different places because they are used in different parts of the reformat process. [here](https://win10.guru/answer-file-autounattend-xml-or-unattend-xml/) for info. Autounattend is for Windows Setup (the part where you can delete and partition drives, format them and choose which drive to install windows to) and the unattend is for what they call OOBE (the part where you enter your username and password after the computer finishes Windows Setup and reboots)

I used [WSIM](https://docs.microsoft.com/en-us/windows-hardware/customize/desktop/wsim/windows-system-image-manager-technical-reference) in the windows adk to make an answer file (autounattend.xml and unattend.xml) you can use NTLite free version

* Autounattend.xml goes to the root of install media supposedly,
* DISM /Apply-Unattend used on an autounattend.xml should only have what they call the offlineServicing "configuration pass" [explanation here](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/how-configuration-passes-work?view=windows-11)
* Unattend.xml is copied into the %WINDIR%/Panther (for example if you mounted the install.wim to C:\testmount it would go to C:testmount\Windows\Panther)
  [here](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/windows-setup-automation-overview?view=windows-11#implicit-answer-file-search-order) for more information on why Panther folder

## put example of what i did exact steps here later
