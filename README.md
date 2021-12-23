# reformatnousb
reformat from another partition without usb

## general overview, links and explanations
[DISM](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/what-is-dism?view=windows-11)
* [/Apply-Image](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/dism-image-management-command-line-options-s14?view=windows-11#apply-image)
* [/Add-Driver](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/add-and-remove-drivers-to-an-offline-windows-image?view=windows-11)
* [/Mount-Image /Unmount-Image](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/mount-and-modify-a-windows-image-using-dism?view=windows-11)
* [/Apply-Unattend](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/dism-unattended-servicing-command-line-options?view=windows-11#apply-unattend)

**install.wim**
  1. this is the wim (Windows Image File) that you will ultimately use "DISM /Apply-Image" command to reformat a partition. Until then, you will modify the install.wim by
     1. mounting it (DISM's way of essentially unzipping it so it can modify it)
        * Dism /Mount-Image /ImageFile:C:\test\images\myimage.wim /index:1 /MountDir:C:\test\offline
          * /index:1 selecting the right windows version (e.g. home, pro, ultimate) later on when you run /Apply-Image to install windows onto a drive, you will specify the same index: that you mounted and modified here. look [**_here_**](https://www.tenforums.com/general-support/162980-what-index-number-how-do-i-find-thank-you-post2000764.html?s=ab6904756d100e190fc1593666d2cc3d#post2000764) for an example of how to view the indexes in install.wim and what it looks like
     2. modifying it (with DISM /Add-Driver to add drivers and /Apply-Unattend if you have any offlineServicing changes in the autoaunattend.xml file)
     3. unmounting it (to unmount and apply changes to the install.wim)

  2. **Explanation**  when you mount the install.wim and choose the index, you will specify a folder as the destination. inside that folder will look exactly like a C:\ drive of a fresh windows install. this is because the install.wim file is the DISM's method of compressing the windows.

     asdf 

autounattend.xml and unattend.xml

files used to automate settings in windows so you dont have to

these are called answer files. the big differences to know is that they go in different places because they are used in different parts of the reformat process. [here](https://win10.guru/answer-file-autounattend-xml-or-unattend-xml/) for info. autounattend is for Windows Setup (the part where you can delete and partition drives, format them and choose which drive to install windows to) athe unattend is for what they call OOBE (the part where you enter your username and password after the computer finishes Windows Setup and reboots)

I used [WSIM](https://docs.microsoft.com/en-us/windows-hardware/customize/desktop/wsim/windows-system-image-manager-technical-reference) in the windows adk to make an answer file (autounattend.xml and unattend.xml) you can use NTLite free version

* autounattend.xml goes to the root of install media supposedly,
* DISM /Apply-Unattend used on an autounattend.xml should only have what they call the offlineServicing "configuration pass" [explanation here](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/how-configuration-passes-work?view=windows-11)
* unattend.xml is copied into the %WINDIR%/Panther (for example if you extracted the install.wim to a test folder it would go to test\Windows\Panther)
