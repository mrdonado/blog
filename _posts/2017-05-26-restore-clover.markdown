---
layout: post
title:  "Restore clover using Windows"
date:   2017-05-26
category: clover
backgrounds:
    - http://blog.jdonado.com/assets/images/jsbg.jpg
thumb: http://blog.jdonado.com/assets/images/clover.png
tags: hackintosh clover windows
---

# Restore your config.plist

Clover is a boot manager that allows you to select the OS you want to boot with. It is used mostly by Hackintosh users, that is, people that install MacOSX (along with other OSs, like Linux and Windows) on a non-Apple machine.

The Clover configuration can be quite tricky, and if you're not lucky you can easily mess it up. That's why it's more than advisable to keep a backup of the `/EFI/CLOVER/config.plist` configuration file.

If you happen to break down your MacOSX boot, but you still have a working Windows boot on the same machine, you can follow these steps in order to restore a backup of `config.plist`:

- Open an administrator console (run `cmd.exe` as Administrator) and type the following:
- `diskpart` (open diskpart)
- `list disk` (list your hard drives with)
- `select disk 1` (select the disk where your Clover EFI boot has been installed. 1 is just an example)
- `list partition` (list the partitions available on the selected disk)
- `select partition 1` (select the boot EFI partition)
- `assign letter=s` (assign a letter. S in this case)
- `exit` (exit diskpart)
- `s:` (go to the just mounted boot partition)
- `cd EFI\CLOVER\`
- `copy c:\backups\config.plist .` (c:\backups is just an example. Replace this path by the path where you have your config.plist backup)

If everything worked fine, you can now restart and use your MacOSX boot again.

In addition to that, some Clover prefixes that can help you find out what went wrong are:

- `-x` start using the safe mode
- `-v` verbose mode

You can find more information about Hackintosh and Clover on [tonymac86](https://www.tonymacx86.com/).
