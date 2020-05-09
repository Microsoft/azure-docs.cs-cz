---
title: Změna velikosti disku s operačním systémem pomocí oddílu GPT | Microsoft Docs
description: Tento článek poskytuje pokyny k změně velikosti disku s operačním systémem pomocí oddílu GPT.
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
ms.openlocfilehash: f863233f0a34271841cc8e973f9aa3ca9416ceeb
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858987"
---
# <a name="resize-an-os-disk-with-a-gpt-partition"></a>Změna velikosti disku s operačním systémem pomocí oddílu GPT

> [!NOTE]
> Tento scénář se vztahuje jenom na disk s operačním systémem s oddílem GPT.

Tento článek popisuje, jak zvětšit velikost disku s operačním systémem pomocí oddílu GPT v systému Linux.

## <a name="identify-whether-the-os-disk-has-an-mbr-or-gpt-partition"></a>Určení, jestli disk s operačním systémem má oddíl MBR nebo GPT

Pomocí příkazu **, který jste** vytvořili, zjistěte, jestli byl diskový oddíl vytvořený pomocí oddílu MBR (Master Boot Record), nebo oddílu GPT (GUID Partition Table).

### <a name="mbr-partition"></a>Oddíl MBR

V následujícím výstupu **tabulka oddílů** zobrazuje hodnotu **MSDOS**a identifikuje oddíl **MBR** .

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

V následujícím výstupu **tabulka oddílů** zobrazuje hodnotu **GPT**, která identifikuje oddíl GPT.

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

### <a name="ubuntu-16x-and-18x"></a>Ubuntu 16. x a 18. x

Chcete-li zvětšit velikost disku s operačním systémem v Ubuntu 16. x a 18. x:

1. Zastavte virtuální počítač.
1. Zvětšete velikost OSDisk z portálu.
1. Restartujte virtuální počítač a pak se přihlaste k virtuálnímu počítači jako uživatel **root** .
1. V OSDisk se teď zobrazí zvětšená velikost systému souborů.

Jak ukazuje následující příklad, velikost disku s operačním systémem se změnila z portálu na 100 GB, protože systém souborů **/dev/sda1** se **/** teď zobrazuje 97 GB.

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

### <a name="suse-12-sp4suse-sles-12-for-sap-suse-sles-15-and-suse-sles-15-for-sap"></a>SUSE 12 SP4, SUSE SLES 12 pro SAP, SUSE SLES 15 a SUSE SLES 15 pro SAP

Pokud chcete zvětšit velikost disku s operačním systémem v SUSE 12 SP4, SUSE SLES 15 a SUSE SLES 15 pro SAP:

1. Zastavte virtuální počítač.
1. Zvětšete velikost OSDisk z portálu.
1. Restartujte virtuální počítač.

Po restartování virtuálního počítače proveďte následující kroky:

   1. K VIRTUÁLNÍmu počítači se dostanete jako **uživatel s kořenovým adresářem** pomocí následujícího příkazu:
   
      `#sudo su`

   1. Pomocí následujícího příkazu nainstalujte balíček **gptfdisk** , který je nutný ke zvýšení velikosti disku s operačním systémem:

      `#zypper install gptfdisk -y`

   1. Chcete-li zobrazit největší sektor dostupný na disku, spusťte následující příkaz:

      `#sgdisk -e /dev/sda`

   1. Změňte velikost oddílu, aniž byste ho odstranili, a to pomocí následujícího příkazu. Rozdělený **příkaz má** možnost s názvem **resizepart** pro změnu velikosti oddílu bez jeho odstranění. Počet 4 po resizepart indikuje změnu velikosti čtvrtého oddílu (4).

      `#parted -s /dev/sda "resizepart 4 -1" quit`

   1. Spusťte `#lsblk` příkaz a ověřte, zda byl oddíl zvětšen.

      Následující výstup ukazuje, že se změnila velikost oddílu **/dev/sda4** na 98,5 GB.

      ```
      user@myvm:~ # lsblk
      NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda      8:0    0  100G  0 disk
      ├─sda1   8:1    0    2M  0 part
      ├─sda2   8:2    0  512M  0 part /boot/efi
      └─sda4   8:4    0 98.5G  0 part /
      sdb      8:16   0   20G  0 disk
      └─sdb1   8:17   0   20G  0 part /mnt/resource
      ```
      
   1. Určete typ systému souborů v OSDisk pomocí následujícího příkazu:

      `blkid`

      Příklad výstupu:

      ```
      #blkid

      user@myvm:~ # blkid
      /dev/sda1: PARTLABEL="p.legacy" PARTUUID="0122fd4c-0069-4a45-bfd4-98b97ccb6e8c"
      /dev/sda2: SEC_TYPE="msdos" LABEL_FATBOOT="EFI" LABEL="EFI" UUID="00A9-D170" TYPE="vfat" PARTLABEL="p.UEFI" PARTUUID="abac3cd8-949b-4e83-81b1-9636493388c7"
      /dev/sda3: LABEL="BOOT" UUID="aa2492db-f9ed-4f5a-822a-1233c06d57cc" TYPE="xfs" PARTLABEL="p.lxboot" PARTUUID="dfb36c61-b15f-4505-8e06-552cf1589cf7"
      /dev/sda4: LABEL="ROOT" UUID="26104965-251c-4e8d-b069-5f5323d2a9ba" TYPE="xfs" PARTLABEL="p.lxroot" PARTUUID="50fecee0-f22b-4406-94c3-622507e2dbce"
      /dev/sdb1: UUID="95239fce-ca97-4f03-a077-4e291588afc9" TYPE="ext4" PARTUUID="953afef3-01"
      ```

   1. V závislosti na typu systému souborů použijte příslušné příkazy pro změnu velikosti systému souborů.

      Pro **XFS**použijte následující příkaz:

      ` #xfs_growfs /`

      Příklad výstupu:

      ```
      user@myvm:~ # xfs_growfs /
      meta-data=/dev/sda4              isize=512    agcount=4, agsize=1867583 blks
               =                       sectsz=512   attr=2, projid32bit=1
               =                       crc=1        finobt=1 spinodes=0 rmapbt=0
               =                       reflink=0
      data     =                       bsize=4096   blocks=7470331, imaxpct=25
               =                       sunit=0      swidth=0 blks
      naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
      log      =internal               bsize=4096   blocks=3647, version=2
               =                       sectsz=512   sunit=0 blks, lazy-count=1
      realtime =none                   extsz=4096   blocks=0, rtextents=0
      data blocks changed from 7470331 to 25820172
      ```

      Pro **ext4**použijte následující příkaz:

      ```#resize2fs /dev/sda4```

   1. Ověřte zvýšenou velikost systému souborů pro **DF-th**pomocí následujícího příkazu:

      `#df -Th`

      Příklad výstupu:

      ```
      user@myvm:~ # df -Th
      Filesystem     Type      Size  Used Avail Use% Mounted on
      devtmpfs       devtmpfs  306M  4.0K  306M   1% /dev
      tmpfs          tmpfs     320M     0  320M   0% /dev/shm
      tmpfs          tmpfs     320M  8.8M  311M   3% /run
      tmpfs          tmpfs     320M     0  320M   0% /sys/fs/cgroup
      /dev/sda4      xfs        99G  1.8G   97G   2% /
      /dev/sda3      xfs      1014M   88M  927M   9% /boot
      /dev/sda2      vfat      512M  1.1M  511M   1% /boot/efi
      /dev/sdb1      ext4       20G   45M   19G   1% /mnt/resource
      tmpfs          tmpfs      64M     0   64M   0% /run/user/1000
      user@myvm:~ #
      ```

Jak je znázorněno v předchozím příkladu, můžeme vidět, že velikost systému souborů pro OSDisk se zvýšila.

### <a name="rhel-7x-with-lvm"></a>RHEL 7. x s LVM

1. Zastavte virtuální počítač.
1. Zvětšete velikost OSDisk z portálu.
1. Spusťte virtuální počítač.

Po restartování virtuálního počítače proveďte následující kroky:

   1. K VIRTUÁLNÍmu počítači se dostanete jako **uživatel s kořenovým adresářem** pomocí následujícího příkazu:
   
      `#sudo su`

   1. Nainstalujte balíček **gptfdisk** , který je nutný ke zvýšení velikosti disku s operačním systémem.

      `#yum install gdisk -y`

   1. Chcete-li zobrazit největší sektor dostupný na disku, spusťte následující příkaz:

      `#sgdisk -e /dev/sda`

   1. Změňte velikost oddílu, aniž byste ho odstranili, a to pomocí následujícího příkazu. Rozdělený **příkaz má** možnost s názvem **resizepart** pro změnu velikosti oddílu bez jeho odstranění. Počet 4 po resizepart indikuje změnu velikosti čtvrtého oddílu (4).

      `#parted -s /dev/sda "resizepart 4 -1" quit`
    
   1. Spuštěním následujícího příkazu ověřte, že se oddíl zvýšil:

      `#lsblk`

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

   1. Chcete-li změnit velikost **fyzického svazku (souč_hod)**, použijte následující příkaz:

      `#pvresize /dev/sda4`

      Následující výstup ukazuje, že se hodnota SOUČHODNOTA změnila na 99,02 GB.

      ```
      [user@myvm ~]# pvresize /dev/sda4
      Physical volume "/dev/sda4" changed
      1 physical volume(s) resized or updated / 0 physical volume(s) not resized

      [user@myvm ~]# pvs
      PV         VG     Fmt  Attr PSize   PFree
      /dev/sda4  rootvg lvm2 a--  <99.02g <74.02g
      ```

   1. V následujícím příkladu `/dev/mapper/rootvg-rootlv` se mění velikost z 2 GB na 12GB (zvýšení 10 GB) pomocí následujícího příkazu, ve kterém se také změní velikost systému souborů:

      `#lvresize -r -L +10G /dev/mapper/rootvg-rootlv`

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
         
   1. Ověřte, `/dev/mapper/rootvg-rootlv` zda se zvýšila velikost systému souborů nebo nepoužívá následující příkaz:

      `#df -Th /`

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