---
title: Změna velikosti disku s operačním systémem, který má oddíl GPT | Microsoft Docs
description: Tento článek obsahuje pokyny k změně velikosti disku s operačním systémem, který má oddíl GPT.
services: virtual-machines-linux
documentationcenter: ''
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: security
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.date: 05/03/2020
ms.author: kaib
ms.custom: seodec18
ms.openlocfilehash: 30a960c3ed76788158b15022947fec49a95ae299
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89375206"
---
# <a name="resize-an-os-disk-that-has-a-gpt-partition"></a>Změna velikosti disku s operačním systémem, který má oddíl GPT

> [!NOTE]
> Tento scénář se vztahuje jenom na disky s operačním systémem, které mají oddíl GPT (GUID Partition Table).

Tento článek popisuje, jak zvětšit velikost disku s operačním systémem, který má oddíl GPT v systému Linux. 

## <a name="identify-whether-the-os-disk-has-an-mbr-or-gpt-partition"></a>Určení, jestli disk s operačním systémem má oddíl MBR nebo GPT

Pomocí `parted` příkazu Zjistěte, jestli byl diskový oddíl vytvořený pomocí oddílu MBR (Master Boot Record) nebo oddílu GPT.

### <a name="mbr-partition"></a>Oddíl MBR

V následujícím výstupu **tabulka oddílů** zobrazuje hodnotu **MSDOS**. Tato hodnota identifikuje oddíl MBR.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 107GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Number  Start   End     Size    Type     File system  Flags
1       1049kB  525MB   524MB   primary  ext4         boot
2       525MB   34.4GB  33.8GB  primary  ext4
[user@myvm ~]#
```

### <a name="gpt-partition"></a>Oddíl GPT

V následujícím výstupu **tabulka oddílů** zobrazuje hodnotu **GPT**. Tato hodnota identifikuje oddíl GPT.

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 68.7GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name                  Flags
1       1049kB  525MB   524MB   fat16        EFI System Partition  boot
2       525MB   1050MB  524MB   xfs
3       1050MB  1052MB  2097kB                                     bios_grub
4       1052MB  68.7GB  67.7GB                                     lvm
```

Pokud má váš virtuální počítač oddíl GPT na disku s operačním systémem, zvětšete velikost disku s operačním systémem.

## <a name="increase-the-size-of-the-os-disk"></a>Zvětšit velikost disku s operačním systémem

Následující pokyny se vztahují na distribuce schválené systémem Linux.

> [!NOTE]
> Než budete pokračovat, vytvořte si záložní kopii virtuálního počítače nebo si poznamenejte disk s operačním systémem.

### <a name="ubuntu"></a>Ubuntu

Chcete-li zvětšit velikost disku s operačním systémem v Ubuntu 16. x a 18. x:

1. Zastavte virtuální počítač.
1. Zvětšete velikost disku s operačním systémem z portálu.
1. Restartujte virtuální počítač a pak se přihlaste k virtuálnímu počítači jako uživatel **root** .
1. Ověřte, že disk s operačním systémem teď zobrazuje zvýšenou velikost systému souborů.

Jak je znázorněno v následujícím příkladu, změnila se velikost disku s operačním systémem z portálu na 100 GB. **/Dev/sda1** systém souborů, který je připojen k systému, **/** nyní zobrazuje 97 GB.

```
user@myvm:~# df -Th
Filesystem     Type      Size  Used Avail Use% Mounted on
udev           devtmpfs  314M     0  314M   0% /dev
tmpfs          tmpfs      65M  2.3M   63M   4% /run
/dev/sda1      ext4       97G  1.8G   95G   2% /
tmpfs          tmpfs     324M     0  324M   0% /dev/shm
tmpfs          tmpfs     5.0M     0  5.0M   0% /run/lock
tmpfs          tmpfs     324M     0  324M   0% /sys/fs/cgroup
/dev/sda15     vfat      105M  3.6M  101M   4% /boot/efi
/dev/sdb1      ext4       20G   44M   19G   1% /mnt
tmpfs          tmpfs      65M     0   65M   0% /run/user/1000
user@myvm:~#
```

### <a name="suse"></a>SUSE

Pokud chcete zvětšit velikost disku s operačním systémem v SUSE 12 SP4, SUSE SLES 12 pro SAP, SUSE SLES 15 a SUSE SLES 15 pro SAP:

1. Zastavte virtuální počítač.
1. Zvětšete velikost disku s operačním systémem z portálu.
1. Restartujte virtuální počítač.

Po restartování virtuálního počítače proveďte následující kroky:

1. K VIRTUÁLNÍmu počítači se dostanete jako uživatel **root** pomocí tohoto příkazu:

   ```
   # sudo -i
   ```

1. Pomocí následujícího příkazu nainstalujte balíček **growpart** , který se použije ke změně velikosti oddílu:

   ```
   # zypper install growpart
   ```

1. Pomocí `lsblk` příkazu Najděte oddíl připojený do kořenového adresáře systému souborů ("/"). V tomto případě se zobrazí, že je oddíl 4 SDA zařízení připojený k/:

   ```
   # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 28.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. Změňte velikost požadovaného oddílu pomocí `growpart` příkazu pomocí čísla oddílu, který najdete v předchozím kroku.

   ```
   # growpart /dev/sda 4
   CHANGED: partition=4 start=3151872 old: size=59762655 end=62914527 new: size=97511391 end=100663263
   ```

1. Spusťte `lsblk` příkaz znovu a ověřte, zda byl oddíl zvětšen.

   Následující výstup ukazuje, že se velikost oddílu **/dev/sda4** změnila na 46,5 GB:
   
   ```
   linux:~ # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 46.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. Identifikujte typ systému souborů na disku s operačním systémem pomocí `lsblk` příkazu s `-f` příznakem:

   ```
   linux:~ # lsblk -f
   NAME   FSTYPE LABEL UUID                                 MOUNTPOINT
   sda
   ├─sda1
   ├─sda2 vfat   EFI   AC67-D22D                            /boot/efi
   ├─sda3 xfs    BOOT  5731a128-db36-4899-b3d2-eb5ae8126188 /boot
   └─sda4 xfs    ROOT  70f83359-c7f2-4409-bba5-37b07534af96 /
   sdb
   └─sdb1 ext4         8c4ca904-cd93-4939-b240-fb45401e2ec6 /mnt/resource
   ```

1. V závislosti na typu systému souborů použijte příslušné příkazy pro změnu velikosti systému souborů.
   
   Pro **XFS**použijte následující příkaz:
   
   ```
   #xfs_growfs /
   ```
   
   Příklad výstupu:
   
   ```
   linux:~ # xfs_growfs /
   meta-data=/dev/sda4              isize=512    agcount=4, agsize=1867583 blks
            =                       sectsz=512   attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0 rmapbt=0
            =                       reflink=0
   data     =                       bsize=4096   blocks=7470331, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=3647, version=2
            =                       sectsz=512   sunit=0 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 7470331 to 12188923
   ```
   
   Pro **ext4**použijte následující příkaz:
   
   ```
   #resize2fs /dev/sda4
   ```
   
1. Ověřte zvýšenou velikost systému souborů pro **DF-th**pomocí následujícího příkazu:
   
   ```
   #df -Thl
   ```
   
   Příklad výstupu:
   
   ```
   linux:~ # df -Thl
   Filesystem     Type      Size  Used Avail Use% Mounted on
   devtmpfs       devtmpfs  445M  4.0K  445M   1% /dev
   tmpfs          tmpfs     458M     0  458M   0% /dev/shm
   tmpfs          tmpfs     458M   14M  445M   3% /run
   tmpfs          tmpfs     458M     0  458M   0% /sys/fs/cgroup
   /dev/sda4      xfs        47G  2.2G   45G   5% /
   /dev/sda3      xfs      1014M   86M  929M   9% /boot
   /dev/sda2      vfat      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1      ext4      3.9G   16M  3.7G   1% /mnt/resource
   tmpfs          tmpfs      92M     0   92M   0% /run/user/1000
   tmpfs          tmpfs      92M     0   92M   0% /run/user/490
   ```
   
   V předchozím příkladu vidíte, že se zvýšila velikost systému souborů pro disk s operačním systémem.

### <a name="rhel"></a>RHEL

Chcete-li zvětšit velikost disku s operačním systémem v RHEL 7. x s LVM:

1. Zastavte virtuální počítač.
1. Zvětšete velikost disku s operačním systémem z portálu.
1. Spusťte virtuální počítač.

Po restartování virtuálního počítače proveďte následující kroky:

1. K VIRTUÁLNÍmu počítači se dostanete jako uživatel **root** pomocí tohoto příkazu:
 
   ```
   #sudo su
   ```

1. Nainstalujte balíček **gptfdisk** , který je nutný ke zvýšení velikosti disku s operačním systémem.

   ```
   #yum install gdisk -y
   ```

1. Chcete-li zobrazit největší sektor dostupný na disku, spusťte následující příkaz:

   ```
   #sgdisk -e /dev/sda
   ```

1. Změňte velikost oddílu, aniž byste ho odstranili pomocí následujícího příkazu. Rozdělený **příkaz má** možnost s názvem **resizepart** pro změnu velikosti oddílu bez jeho odstranění. Počet 4 po **resizepart** indikuje změnu velikosti čtvrtého oddílu.

   ```
   #parted -s /dev/sda "resizepart 4 -1" quit
   ```
    
1. Spuštěním následujícího příkazu ověřte, že se oddíl zvýšil:

   ```
   #lsblk
   ```

   Následující výstup ukazuje, že se změnila velikost oddílu **/dev/sda4** na 99 GB.

   ```
   [user@myvm ~]# lsblk
   NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   fd0                 2:0    1    4K  0 disk
   sda                 8:0    0  100G  0 disk
   ├─sda1              8:1    0  500M  0 part /boot/efi
   ├─sda2              8:2    0  500M  0 part /boot
   ├─sda3              8:3    0    2M  0 part
   └─sda4              8:4    0   99G  0 part
   ├─rootvg-tmplv    253:0    0    2G  0 lvm  /tmp
   ├─rootvg-usrlv    253:1    0   10G  0 lvm  /usr
   ├─rootvg-optlv    253:2    0    2G  0 lvm  /opt
   ├─rootvg-homelv   253:3    0    1G  0 lvm  /home
   ├─rootvg-varlv    253:4    0    8G  0 lvm  /var
   └─rootvg-rootlv   253:5    0    2G  0 lvm  /
   sdb                 8:16   0   50G  0 disk
   └─sdb1              8:17   0   50G  0 part /mnt/resource
   ```

1. Chcete-li změnit velikost fyzického svazku (souč_hod), použijte následující příkaz:

   ```
   #pvresize /dev/sda4
   ```

   Následující výstup ukazuje, že se hodnota SOUČHODNOTA změnila na 99,02 GB.

   ```
   [user@myvm ~]# pvresize /dev/sda4
   Physical volume "/dev/sda4" changed
   1 physical volume(s) resized or updated / 0 physical volume(s) not resized

   [user@myvm ~]# pvs
   PV         VG     Fmt  Attr PSize   PFree
   /dev/sda4  rootvg lvm2 a--  <99.02g <74.02g
   ```

1. V následujícím příkladu se velikost **/dev/Mapper/rootvg-rootlv** mění z 2 GB na 12 GB (zvýšení velikosti 10 GB) prostřednictvím následujícího příkazu. Tento příkaz změní také velikost systému souborů.

   ```
   #lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   ```

   Příklad výstupu:

   ```
   [user@myvm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   Size of logical volume rootvg/rootlv changed from 2.00 GiB (512 extents) to 12.00 GiB (3072 extents).
   Logical volume rootvg/rootlv successfully resized.
   meta-data=/dev/mapper/rootvg-rootlv isize=512    agcount=4, agsize=131072 blks
            =                       sectsz=4096  attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0
   data     =                       bsize=4096   blocks=524288, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=2560, version=2
            =                       sectsz=4096  sunit=1 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 524288 to 3145728
   ```
         
1. Pomocí následujícího příkazu ověřte, zda má **/dev/Mapper/rootvg-rootlv** zvýšenou velikost systému souborů:

   ```
   #df -Th /
   ```

   Příklad výstupu:

   ```
   [user@myvm ~]# df -Th /
   Filesystem                Type  Size  Used Avail Use% Mounted on
   /dev/mapper/rootvg-rootlv xfs    12G   71M   12G   1% /
   [user@myvm ~]#
   ```

> [!NOTE]
> Chcete-li použít stejný postup pro změnu velikosti jakéhokoli jiného logického svazku, změňte název **LV** v kroku 7.

## <a name="next-steps"></a>Další kroky

- [Změnit velikost disku](expand-disks.md)
