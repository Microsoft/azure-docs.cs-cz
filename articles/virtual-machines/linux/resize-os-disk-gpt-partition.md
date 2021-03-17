---
title: Změna velikosti disku s operačním systémem, který má oddíl GPT
description: Tento článek obsahuje informace o tom, jak změnit velikost disku s operačním systémem, který má oddíl GPT (GUID Partition Table) v systému Linux.
services: virtual-machines
ms.topic: article
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.date: 05/03/2020
ms.author: kaib
ms.custom: seodec18
ms.openlocfilehash: 46b6ceff74dd3a296d26cc018b380c1c3f76664c
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102552945"
---
# <a name="resize-an-os-disk-that-has-a-gpt-partition"></a>Změna velikosti disku s operačním systémem, který má oddíl GPT

> [!NOTE]
> Tento článek se týká jenom disků s operačním systémem, které mají oddíl GPT (GUID Partition Table).

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

Zvýšení velikosti disku s operačním systémem v Ubuntu 16. *x* a 18. *x*:

1. Zastavte virtuální počítač.
1. Zvětšete velikost disku s operačním systémem z portálu.
1. Restartujte virtuální počítač a přihlaste se k virtuálnímu počítači jako uživatel **root** .
1. Ověřte, že disk s operačním systémem teď zobrazuje zvýšenou velikost systému souborů.

V následujícím příkladu se změnila velikost disku s operačním systémem z portálu na 100 GB. **/Dev/sda1** systém souborů, který je připojen k systému, **/** nyní zobrazuje 97 GB.

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

Po restartování virtuálního počítače proveďte tyto kroky:

1. K VIRTUÁLNÍmu počítači přistupujete jako uživatel **root** pomocí tohoto příkazu:

   ```
   # sudo -i
   ```

1. Pomocí následujícího příkazu nainstalujte balíček **growpart** , který použijete pro změnu velikosti oddílu:

   ```
   # zypper install growpart
   ```

1. Pomocí `lsblk` příkazu Najděte oddíl připojený k kořenovému adresáři systému souborů ( **/** ). V tomto případě uvidíme, že oddíl 4 **SDA** zařízení je připojený k **/** :

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

1. Změňte velikost požadovaného oddílu pomocí `growpart` příkazu a číslo oddílu určené v předchozím kroku:

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
   
   Pro **XFS** použijte tento příkaz:
   
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
   
   Pro **ext4** použijte tento příkaz:
   
   ```
   #resize2fs /dev/sda4
   ```
   
1. Ověřte zvýšenou velikost systému souborů pro **DF-th** pomocí tohoto příkazu:
   
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

### <a name="rhel-with-lvm"></a>RHEL s LVM

1. K VIRTUÁLNÍmu počítači přistupujete jako uživatel **root** pomocí tohoto příkazu:

   ```bash
   [root@dd-rhel7vm ~]# sudo -i
   ```

1. Pomocí `lsblk` příkazu určete, který logický svazek (LV) je připojen v kořenovém adresáři systému souborů ( **/** ). V tomto případě se zobrazí, že je **rootvg-rootlv** připojen **/** . Pokud chcete použít jiný systém souborů, nahraďte LV a přípojný bod v rámci tohoto článku.

   ```shell
   [root@dd-rhel7vm ~]# lsblk -f
   NAME                  FSTYPE      LABEL   UUID                                   MOUNTPOINT
   fd0
   sda
   ├─sda1                vfat                C13D-C339                              /boot/efi
   ├─sda2                xfs                 8cc4c23c-fa7b-4a4d-bba8-4108b7ac0135   /boot
   ├─sda3
   └─sda4                LVM2_member         zx0Lio-2YsN-ukmz-BvAY-LCKb-kRU0-ReRBzh
      ├─rootvg-tmplv      xfs                 174c3c3a-9e65-409a-af59-5204a5c00550   /tmp
      ├─rootvg-usrlv      xfs                 a48dbaac-75d4-4cf6-a5e6-dcd3ffed9af1   /usr
      ├─rootvg-optlv      xfs                 85fe8660-9acb-48b8-98aa-bf16f14b9587   /opt
      ├─rootvg-homelv     xfs                 b22432b1-c905-492b-a27f-199c1a6497e7   /home
      ├─rootvg-varlv      xfs                 24ad0b4e-1b6b-45e7-9605-8aca02d20d22   /var
      └─rootvg-rootlv     xfs                 4f3e6f40-61bf-4866-a7ae-5c6a94675193   /
   ```

1. Ověřte, zda je ve skupině svazků LVM (VG), která obsahuje kořenový oddíl, volné místo. Pokud je k dispozici volné místo, přejděte ke kroku 12.

   ```bash
   [root@dd-rhel7vm ~]# vgdisplay rootvg
   --- Volume group ---
   VG Name               rootvg
   System ID
   Format                lvm2
   Metadata Areas        1
   Metadata Sequence No  7
   VG Access             read/write
   VG Status             resizable
   MAX LV                0
   Cur LV                6
   Open LV               6
   Max PV                0
   Cur PV                1
   Act PV                1
   VG Size               <63.02 GiB
   PE Size               4.00 MiB
   Total PE              16132
   Alloc PE / Size       6400 / 25.00 GiB
   Free  PE / Size       9732 / <38.02 GiB
   VG UUID               lPUfnV-3aYT-zDJJ-JaPX-L2d7-n8sL-A9AgJb
   ```

   V tomto příkladu řádek **Free PE/Size** ukazuje, že ve skupině svazků je 38,02 GB volného místa. Před přidáním prostoru do skupiny svazků nemusíte měnit velikost disku.

1. Chcete-li zvětšit velikost disku s operačním systémem v RHEL 7. *x* s LVM:

   1. Zastavte virtuální počítač.
   1. Zvětšete velikost disku s operačním systémem z portálu.
   1. Spusťte virtuální počítač.

1. Po restartování virtuálního počítače proveďte následující krok:

   - Nainstalujte balíček **Cloud-utils-growpart** k poskytnutí příkazu **growpart** , který je nutný ke zvýšení velikosti disku s operačním systémem a obslužné rutiny GDISK pro rozložení disků GPT. Tyto balíčky jsou předinstalované na většině imagí na webu Marketplace.

   ```bash
   [root@dd-rhel7vm ~]# yum install cloud-utils-growpart gdisk
   ```

1. Pomocí příkazu určete, který disk a oddíl budou obsahovat LVM fyzický svazek nebo svazky (souč_hod) ve skupině  svazků s názvem rootvg `pvscan` . Všimněte si velikosti a volného místa uvedeného mezi hranatými závorkami (**[** a **]**).

   ```bash
   [root@dd-rhel7vm ~]# pvscan
     PV /dev/sda4   VG rootvg          lvm2 [<63.02 GiB / <38.02 GiB free]
   ```

1. Ověřte velikost oddílu pomocí `lsblk` . 

   ```bash
   [root@dd-rhel7vm ~]# lsblk /dev/sda4
   NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   sda4              8:4    0  63G  0 part
   ├─rootvg-tmplv  253:1    0   2G  0 lvm  /tmp
   ├─rootvg-usrlv  253:2    0  10G  0 lvm  /usr
   ├─rootvg-optlv  253:3    0   2G  0 lvm  /opt
   ├─rootvg-homelv 253:4    0   1G  0 lvm  /home
   ├─rootvg-varlv  253:5    0   8G  0 lvm  /var
   └─rootvg-rootlv 253:6    0   2G  0 lvm  /
   ```

1. Rozbalením oddílu obsahujícího tuto PV použijte `growpart` , název zařízení a číslo oddílu. Tím se rozbalí zadaný oddíl, aby se na zařízení použilo všechno volné místo.

   ```bash
   [root@dd-rhel7vm ~]# growpart /dev/sda 4
   CHANGED: partition=4 start=2054144 old: size=132161536 end=134215680 new: size=199272414 end=201326558
   ```

1. Pomocí příkazu ověřte, že se velikost oddílu změnila na očekávanou velikost `lsblk` . Všimněte si, že v příkladu **sda4** se změnila z 63 gb na 95 GB.

   ```bash
   [root@dd-rhel7vm ~]# lsblk /dev/sda4
   NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   sda4              8:4    0  95G  0 part
   ├─rootvg-tmplv  253:1    0   2G  0 lvm  /tmp
   ├─rootvg-usrlv  253:2    0  10G  0 lvm  /usr
   ├─rootvg-optlv  253:3    0   2G  0 lvm  /opt
   ├─rootvg-homelv 253:4    0   1G  0 lvm  /home
   ├─rootvg-varlv  253:5    0   8G  0 lvm  /var
   └─rootvg-rootlv 253:6    0   2G  0 lvm  /
   ```

1. Rozbalením PV využijete zbytek nově rozbaleného oddílu:

   ```bash
   [root@dd-rhel7vm ~]# pvresize /dev/sda4
   Physical volume "/dev/sda4" changed
   1 physical volume(s) resized or updated / 0 physical volume(s) not resized
   ```

1. Ověřte, že nová velikost PV má očekávanou velikost a porovnává ji s původními hodnotami **[size/Free]** :

   ```bash
   [root@dd-rhel7vm ~]# pvscan
   PV /dev/sda4   VG rootvg          lvm2 [<95.02 GiB / <70.02 GiB free]
   ```

1. Rozbalte požadovaný logický svazek (LV) podle požadované velikosti. Tato hodnota nemusí být ve skupině svazků na všech volných prostorech. V následujícím příkladu je velikost **/dev/Mapper/rootvg-rootlv** změněna z 2 GB na 12 GB (zvýšení velikosti 10 GB). Tento příkaz změní také velikost systému souborů.

   ```bash
   [root@dd-rhel7vm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   ```

   Příklad výstupu:

   ```bash
   [root@dd-rhel7vm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
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

1. `lvresize`Příkaz automaticky zavolá odpovídající příkaz pro změnu velikosti pro systém souborů v části LV. Pomocí tohoto příkazu ověřte, jestli má **/dev/Mapper/rootvg-rootlv**, která je připojená **/** , zvýšenou velikost systému souborů:

   ```shell
   [root@dd-rhel7vm ~]# df -Th /
   ```

   Příklad výstupu:

   ```shell
   [root@dd-rhel7vm ~]# df -Th /
   Filesystem                Type  Size  Used Avail Use% Mounted on
   /dev/mapper/rootvg-rootlv xfs    12G   71M   12G   1% /
   [root@dd-rhel7vm ~]#
   ```

> [!NOTE]
> Chcete-li použít stejný postup pro změnu velikosti jakéhokoli jiného logického svazku, změňte název LV v kroku 12.

### <a name="rhel-raw"></a>RHEL RAW
>[!NOTE] 
>Vždycky pořídit snímek virtuálního počítače před zvýšením velikosti disku s operačním systémem.

Pokud chcete zvětšit velikost disku s operačním systémem v nezpracovaném oddílu RHEL:

1. Zastavte virtuální počítač.
1. Zvětšete velikost disku s operačním systémem z portálu.
1. Spusťte virtuální počítač.

Po restartování virtuálního počítače proveďte následující kroky:

1. K VIRTUÁLNÍmu počítači se dostanete jako uživatel **root** pomocí tohoto příkazu:
 
   ```
   sudo su
   ```

1. Nainstalujte balíček **gptfdisk** , který je nutný ke zvýšení velikosti disku s operačním systémem.

   ```
   yum install gdisk -y
   ```

1.  Chcete-li zobrazit všechny sektory, které jsou na disku k dispozici, spusťte následující příkaz:
    ```
    gdisk -l /dev/sda
    ```

1. Zobrazí se podrobnosti o typu oddílu. Ujistěte se, že se jedná o GPT. Identifikujte kořenový oddíl. Neměňte ani neodstraňujte spouštěcí oddíl (spouštěcí oddíl systému BIOS) a systémový oddíl (' systémový oddíl EFI ')

1. Pro první spuštění vytváření oddílů použijte následující příkaz. 
    ```
    gdisk /dev/sda
    ```

1. Nyní se zobrazí zpráva s výzvou k zadání dalšího příkazu (' Command:? pro nápovědu '). 

   ```
   w
   ```

1. Zobrazí se upozornění. Sekundární hlavička je umístěná na disku příliš brzy. Chcete tento problém vyřešit? (A/N): ". Musíte stisknout Y.

   ```
   Y
   ```

1. Měla by se zobrazit zpráva informující o tom, že jsou dokončeny závěrečné kontroly a žádosti o potvrzení. Stiskněte klávesu Y

   ```
   Y
   ```

1. Ověřte, zda všechno proběhlo správně pomocí příkazu partprobe

   ```
   partprobe
   ```

1. Výše uvedené kroky zajistily, že sekundární hlavička GPT je umístěna na konci. Dalším krokem je spuštění procesu změny velikosti pomocí nástroje gDisk. Použijte následující příkaz.

   ```
   gdisk /dev/sda
   ```
1. V nabídce příkazů kliknutím na ' p ' zobrazte seznam oddílů. Identifikujte kořenový oddíl (v krocích sda2 se považuje za kořenový oddíl) a spouštěcí oddíl (v krocích se sda3 považuje za spouštěcí oddíl). 

   ```
   p
   ```
    ![Kořenový oddíl a spouštěcí oddíl](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw1.png)

1. Stisknutím klávesy a odstraňte oddíl a vyberte číslo oddílu přiřazené ke spuštění (v tomto příkladu je to 3).
   ```
   d
   3
   ```
1. Stisknutím klávesy a odstraňte oddíl a vyberte číslo oddílu přiřazené ke spuštění (v tomto příkladu je to "2").
   ```
   d
   2
   ```
    ![Odstranit kořenový oddíl a spouštěcí oddíl](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw2.png)

1. Pokud chcete znovu vytvořit kořenový oddíl se zvýšenou velikostí, stiskněte n, zadejte číslo oddílu, který jste předtím odstranili pro kořen (2 pro tento příklad), a zvolte první sektor jako výchozí hodnota, poslední sektor jako poslední hodnota sektoru – velikost spouštěcího sektoru (v tomto případě je to "4096") a šestnáctkový kód jako "8300".
   ```
   n
   2
   (Enter default)
   (Calculateed value of Last sector value - 4096)
   8300
   ```
1. Pokud chcete znovu vytvořit spouštěcí oddíl, stiskněte n, zadejte číslo oddílu, který jste předtím odstranili pro spuštění (3 pro tento příklad), a zvolte první sektor jako výchozí hodnota, poslední sektor jako výchozí hodnota a kód hex jako EF02.
   ```
   n
   3
   (Enter default)
   (Enter default)
   EF02
   ```

1. Zapište změny pomocí příkazu w a potvrďte ji stisknutím klávesy Y.
   ```
   w
   Y
   ```
1. Spusťte příkaz ' partprobe ' pro kontrolu stability disku
   ```
   partprobe
   ```
1. Restartujte virtuální počítač a velikost kořenového oddílu by se zvýšila.
   ```
   reboot
   ```

   ![Nový kořenový oddíl a spouštěcí oddíl](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw3.png)

1. Chcete-li změnit velikost, spusťte příkaz xfs_growfs v oddílu.
   ```
   xfs_growfs /dev/sda2
   ```

   ![XFS a rozšířit FS](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw4.png)


1. Ověřte, že se nová velikost odráží pomocí příkazu **DF** :

   ```bash
   [root@vm-dd-cent7 ~]# df -hl
   Filesystem      Size  Used Avail Use% Mounted on
   devtmpfs        452M     0  452M   0% /dev
   tmpfs           464M     0  464M   0% /dev/shm
   tmpfs           464M  6.8M  457M   2% /run
   tmpfs           464M     0  464M   0% /sys/fs/cgroup
   /dev/sda2        48G  2.1G   46G   5% /
   /dev/sda1       494M   65M  430M  13% /boot
   /dev/sda15      495M   12M  484M   3% /boot/efi
   /dev/sdb1       3.9G   16M  3.7G   1% /mnt/resource
   tmpfs            93M     0   93M   0% /run/user/1000
   ```
