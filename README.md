# reformatnousb
reformat from another partition without usb

## general overview, links and explanations
[DISM](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/what-is-dism?view=windows-11)
* [/Apply-Image](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/dism-image-management-command-line-options-s14?view=windows-11#apply-image)
* [/Add-Driver](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/add-and-remove-drivers-to-an-offline-windows-image?view=windows-11)
* [/Mount-Image /Unmount-Image](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/mount-and-modify-a-windows-image-using-dism?view=windows-11)
* [/Apply-Unattend](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/dism-unattended-servicing-command-line-options?view=windows-11#apply-unattend)

install.wim
 * this is the wim (Windows Image File) that you will ultimately use "DISM /Apply-Image" command to reformat a partition. Until then, you will modify the install.wim by
   * 1 mounting it (DISM's way of essentially unzipping it so it can modify it)
   * 2 modifying it (with DISM /Add-Driver to add drivers and /Apply-Unattend if you have any offlineServicing changes in the autoaunattend.xml file)

autounattend.xml and unattend.xml
