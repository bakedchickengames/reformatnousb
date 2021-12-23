# Reformatnousb
Reformat from another partition without usb, skim through this doc for a general understanding of how to get this done.

## General overview, links and explanations

DISM is cmd command you will to do everything with the install.wim (the thing that contains the windows image files). autounattend.xml and unattend.xml are used to make windows automatically configure a wide range of settings without you touching it. i used them mostly to skip the username/password phase of the install (they call this the OOBE, or out of box experience). You  extract a windows.iso with 7zip into a folder. Then you mount the install.wim using DISM to /add-drivers or KB updates. Then you use DISM to apply an autounattend.xml with only offlineServicing (i only used this to turn the microsoft LUA settings off). Then you make a folder in the folder DISM mounted the install.wim to called Windows\Panther. Then you copy your unattend.xml into that Windows\Panther folder. Then you unmount the install.wim to apply and save the install. Then you format a partition and install the unzipped iso (that you just finished changing) using DISM /Apply-Image to that partition. run bcdboot on that drive and it is ready to go

[DISM](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/what-is-dism?view=windows-11)
* [/Apply-Image](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/dism-image-management-command-line-options-s14?view=windows-11#apply-image)
* [/Add-Driver](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/add-and-remove-drivers-to-an-offline-windows-image?view=windows-11)
* [/Mount-Image /Unmount-Image](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/mount-and-modify-a-windows-image-using-dism?view=windows-11)
* [/Apply-Unattend](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/dism-unattended-servicing-command-line-options?view=windows-11#apply-unattend)

**install.wim**
  1. This is the wim (Windows Image File) that you will ultimately use "DISM /Apply-Image" command to reformat a partition. Until then, you will modify the install.wim by
     1. Mounting it (DISM's way of essentially unzipping it so it can modify it)
        * ex: Dism /Mount-Image /ImageFile:C:\test\images\myimage.wim /index:1 /MountDir:C:\test\offline
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
* Unattend.xml is copied into the %WINDIR%/Panther (for example if you extracted the install.wim to a test folder it would go to test\Windows\Panther)
