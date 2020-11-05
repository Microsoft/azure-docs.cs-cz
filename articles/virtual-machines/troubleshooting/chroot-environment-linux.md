---
title: Chroot prostředí na záchranném virtuálním počítači se systémem Linux.
description: Tento článek popisuje, jak řešit potíže s prostředím chroot v záchranném virtuálním počítači (VM) v systému Linux.
mservices: virtual-machines-linux
documentationcenter: ''
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.date: 05/05/2020
ms.author: kaib
ms.openlocfilehash: 0d8a030061ef6aa848344152edaa3267ad916e2a
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93377934"
---
# <a name="chroot-environment-in-a-linux-rescue-vm"></a>Prostředí chroot na záchranném virtuálním počítači se systémem Linux

Tento článek popisuje, jak řešit potíže s prostředím chroot v záchranném virtuálním počítači (VM) v systému Linux.

## <a name="ubuntu-16x--ubuntu-18x"></a>Ubuntu 16. x && Ubuntu 18. x

1. Zastavte nebo zrušte přidělení ovlivněného virtuálního počítače.
1. Vytvořte image záchranných virtuálních počítačů se stejnou verzí operačního systému, ve stejné skupině prostředků (RSG) a umístění pomocí spravovaného disku.
1. Pomocí Azure Portal pořídit snímek disku s operačním systémem příslušného virtuálního počítače.
1. Vytvořte disk ze snímku disku s operačním systémem a připojte ho k záchrannému virtuálnímu počítači.
1. Po vytvoření disku vyřešte potíže s chroot prostředím v záchranném virtuálním počítači.

   1. K VIRTUÁLNÍmu počítači se dostanete jako uživatel root pomocí následujícího příkazu:

      `sudo su -`

   1. Najděte disk pomocí `dmesg` (metoda, kterou použijete ke zjištění nového disku, se může lišit). Následující příklad používá **dmesg** k filtrování na discích **SCSI** :

      `dmesg | grep SCSI`

      Výstup bude vypadat podobně jako v následujícím příkladu. V tomto příkladu chceme **SDC** disk:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Pro přístup k prostředí chroot použijte následující příkazy:

      ```
      mkdir /rescue
      mount /dev/sdc1 /rescue
      mount /dev/sdc15 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Řešení potíží s prostředím chroot

   1. Pomocí následujících příkazů ukončete prostředí chroot:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue
      ```

      > [!NOTE]
      > Pokud se zobrazí chybová zpráva `unable to unmount /rescue` , přidejte do příkazu umount parametr-l.
      >
      > Příklad: `umount -l /rescue`

1. Odpojte disk od záchranného virtuálního počítače a proveďte prohození disku s původním virtuálním počítačem.
1. Spusťte původní virtuální počítač a ověřte jeho připojení.

## <a name="rhelcentosoracle-6x--oracle-8x--rhelcentos-7x-with-raw-partitions"></a>RHEL/CentOS/Oracle 6. x && Oracle 8. x && RHEL/CentOS 7. x s nezpracovanými oddíly

1. Zastavte nebo zrušte přidělení ovlivněného virtuálního počítače.
1. Vytvořte image záchranných virtuálních počítačů se stejnou verzí operačního systému, ve stejné skupině prostředků (RSG) a umístění pomocí spravovaného disku.
1. Pomocí Azure Portal pořídit snímek disku s operačním systémem příslušného virtuálního počítače.
1. Vytvořte disk ze snímku disku s operačním systémem a připojte ho k záchrannému virtuálnímu počítači.
1. Po vytvoření disku vyřešte potíže s chroot prostředím v záchranném virtuálním počítači.

   1. K VIRTUÁLNÍmu počítači se dostanete jako uživatel root pomocí následujícího příkazu:

      `sudo su -`

   1. Najděte disk pomocí `dmesg` (metoda, kterou použijete ke zjištění nového disku, se může lišit). Následující příklad používá **dmesg** k filtrování na discích **SCSI** :

      `dmesg | grep SCSI`

      Výstup bude vypadat podobně jako v následujícím příkladu. V tomto příkladu chceme **SDC** disk:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Pro přístup k prostředí chroot použijte následující příkazy:

      ```
      mkdir /rescue
      mount -o nouuid /dev/sdc2 /rescue
      mount -o nouuid /dev/sdc1 /rescue/boot/
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Řešení potíží s prostředím chroot

   1. Pomocí následujících příkazů ukončete prostředí chroot:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/
      umount /rescue
      ```

      > [!NOTE]
      > Pokud se zobrazí chybová zpráva `unable to unmount /rescue` , přidejte do příkazu umount parametr-l.
      >
      > Příklad: `umount -l /rescue`

1. Odpojte disk od záchranného virtuálního počítače a proveďte prohození disku s původním virtuálním počítačem.
1. Spusťte původní virtuální počítač a ověřte jeho připojení.

## <a name="rhelcentos-7x-with-lvm"></a>RHEL/CentOS 7. x s LVM

   > [!NOTE]
   > Pokud na disku s operačním systémem váš původní virtuální počítač obsahuje Správce logických svazků (LVM), vytvořte záchranný virtuální počítač pomocí image s nezpracovanými oddíly na disku s operačním systémem.

1. Zastavte nebo zrušte přidělení ovlivněného virtuálního počítače.
1. Vytvořte image záchranných virtuálních počítačů se stejnou verzí operačního systému, ve stejné skupině prostředků (RSG) a umístění pomocí spravovaného disku.
1. Pomocí Azure Portal pořídit snímek disku s operačním systémem příslušného virtuálního počítače.
1. Vytvořte disk ze snímku disku s operačním systémem a připojte ho k záchrannému virtuálnímu počítači.
1. Po vytvoření disku vyřešte potíže s chroot prostředím v záchranném virtuálním počítači.

   1. K VIRTUÁLNÍmu počítači se dostanete jako uživatel root pomocí následujícího příkazu:

      `sudo su -`

   1. Najděte disk pomocí `dmesg` (metoda, kterou použijete ke zjištění nového disku, se může lišit). Následující příklad používá **dmesg** k filtrování na discích **SCSI** :

      `dmesg | grep SCSI`

      Výstup bude vypadat podobně jako v následujícím příkladu. V tomto příkladu chceme **SDC** disk:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Pomocí následujícího příkazu aktivujte skupinu logických svazků:

      ```
      vgscan --mknodes
      vgchange -ay
      lvscan
      ```

   1. `lsblk`K načtení názvů LVM použijte příkaz:

      ```
      [user@myvm ~]$ lsblk
      NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda                 8:0    0   64G  0 disk
      ├─sda1              8:1    0  500M  0 part /boot
      ├─sda2              8:2    0   63G  0 part /
      sdb                 8:16   0    4G  0 disk
      └─sdb1              8:17   0    4G  0 part /mnt/resource
      sdc                 8:0    0   64G  0 disk
      ├─sdc1              8:1    0  500M  0 part
      ├─sdc2              8:2    0   63G  0 part
      ├─sdc3              8:3    0    2M  0 part
      ├─sdc4              8:4    0   63G  0 part
        ├─rootvg-tmplv  253:0    0    2G  0 lvm  
        ├─rootvg-usrlv  253:1    0   10G  0 lvm  
        ├─rootvg-optlv  253:2    0    2G  0 lvm  
        ├─rootvg-homelv 253:3    0    1G  0 lvm  
        ├─rootvg-varlv  253:4    0    8G  0 lvm  
        └─rootvg-rootlv 253:5    0    2G  0 lvm
      ```

   1. Pro přístup k prostředí chroot použijte následující příkazy:

      ```
      mkdir /rescue
      mount /dev/mapper/rootvg-rootlv /rescue
      mount /dev/mapper/rootvg-varlv /rescue/var
      mount /dev/mapper/rootvg-homelv /rescue/home
      mount /dev/mapper/rootvg-usrlv /rescue/usr
      mount /dev/mapper/rootvg-tmplv /rescue/tmp
      mount /dev/mapper/rootvg-optlv /rescue/opt
      mount /dev/sdc2 /rescue/boot/
      mount /dev/sdc1 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Řešení potíží s prostředím chroot

   1. Pomocí následujících příkazů ukončete prostředí chroot:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue/boot
      umount /rescue/home
      umount /rescue/var
      umount /rescue/usr
      umount /rescue/tmp
      umount /rescue/opt
      umount /rescue
      ```

      > [!NOTE]
      > Pokud se zobrazí chybová zpráva `unable to unmount /rescue` , přidejte do příkazu umount parametr-l.
      >
      > Příklad: `umount -l /rescue`

1. Odpojte disk od záchranného virtuálního počítače a proveďte prohození disku s původním virtuálním počítačem.
1. Spusťte původní virtuální počítač a ověřte jeho připojení.

## <a name="rhel-8x-with-lvm"></a>RHEL 8. x s LVM

   > [!NOTE]
   > Pokud na disku s operačním systémem váš původní virtuální počítač obsahuje Správce logických svazků (LVM), vytvořte záchranný virtuální počítač pomocí image s nezpracovanými oddíly na disku s operačním systémem.

1. Zastavte nebo zrušte přidělení ovlivněného virtuálního počítače.
1. Vytvořte image záchranných virtuálních počítačů se stejnou verzí operačního systému, ve stejné skupině prostředků (RSG) a umístění pomocí spravovaného disku.
1. Pomocí Azure Portal pořídit snímek disku s operačním systémem příslušného virtuálního počítače.
1. Vytvořte disk ze snímku disku s operačním systémem a připojte ho k záchrannému virtuálnímu počítači.
1. Po vytvoření disku vyřešte potíže s chroot prostředím v záchranném virtuálním počítači.

   1. K VIRTUÁLNÍmu počítači se dostanete jako uživatel root pomocí následujícího příkazu:

      `sudo su -`

   1. Najděte disk pomocí `dmesg` (metoda, kterou použijete ke zjištění nového disku, se může lišit). Následující příklad používá **dmesg** k filtrování na discích **SCSI** :

      `dmesg | grep SCSI`

      Výstup bude vypadat podobně jako v následujícím příkladu. V tomto příkladu chceme **SDC** disk:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Pomocí následujícího příkazu aktivujte skupinu logických svazků:

      ```
      vgscan --mknodes
      vgchange -ay
      lvscan
      ```

   1. `lsblk`K načtení názvů LVM použijte příkaz:

      ```
      [user@myvm ~]$ lsblk
      NAME              MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
      sda                 8:0    0   64G  0 disk
      ├─sda1              8:1    0  500M  0 part /boot
      ├─sda2              8:2    0   63G  0 part /
      sdb                 8:16   0    4G  0 disk
      └─sdb1              8:17   0    4G  0 part /mnt/resource
      sdc                 8:0    0   64G  0 disk
      ├─sdc1              8:1    0  500M  0 part
      ├─sdc2              8:2    0   63G  0 part
      │ ├─rootvg-tmplv  253:0    0    2G  0 lvm  
      │ ├─rootvg-usrlv  253:1    0   10G  0 lvm  
      │ ├─rootvg-homelv 253:2    0    1G  0 lvm  
      │ ├─rootvg-varlv  253:3    0    8G  0 lvm  
      │ └─rootvg-rootlv 253:4    0    2G  0 lvm  
      ├─sdc14             8:14   0    4M  0 part
      └─sdc15             8:15   0  495M  0 part
      ```

   1. Pro přístup k prostředí chroot použijte následující příkazy:

      ```
      mkdir /rescue
      mount /dev/mapper/rootvg-rootlv /rescue
      mount /dev/mapper/rootvg-varlv /rescue/var
      mount /dev/mapper/rootvg-homelv /rescue/home
      mount /dev/mapper/rootvg-usrlv /rescue/usr
      mount /dev/mapper/rootvg-tmplv /rescue/tmp
      mount /dev/sdc1 /rescue/boot/
      mount /dev/sdc15 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Řešení potíží s prostředím chroot

   1. Pomocí následujících příkazů ukončete prostředí chroot:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue/boot
      umount /rescue/home
      umount /rescue/var
      umount /rescue/usr
      umount /rescue/tmp
      umount /rescue
      ```

      > [!NOTE]
      > Pokud se zobrazí chybová zpráva `unable to unmount /rescue` , přidejte do příkazu umount parametr-l.
      >
      > Příklad: `umount -l /rescue`

1. Odpojte disk od záchranného virtuálního počítače a proveďte prohození disku s původním virtuálním počítačem.
1. Spusťte původní virtuální počítač a ověřte jeho připojení.

## <a name="oracle-7x"></a>Oracle 7. x

1. Zastavte nebo zrušte přidělení ovlivněného virtuálního počítače.
1. Vytvořte image záchranných virtuálních počítačů se stejnou verzí operačního systému, ve stejné skupině prostředků (RSG) a umístění pomocí spravovaného disku.
1. Pomocí Azure Portal pořídit snímek disku s operačním systémem příslušného virtuálního počítače.
1. Vytvořte disk ze snímku disku s operačním systémem a připojte ho k záchrannému virtuálnímu počítači.
1. Po vytvoření disku vyřešte potíže s chroot prostředím v záchranném virtuálním počítači.

   1. K VIRTUÁLNÍmu počítači se dostanete jako uživatel root pomocí následujícího příkazu:

      `sudo su -`

   1. Najděte disk pomocí `dmesg` (metoda, kterou použijete ke zjištění nového disku, se může lišit). Následující příklad používá **dmesg** k filtrování na discích **SCSI** :

      `dmesg | grep SCSI`

      Výstup bude vypadat podobně jako v následujícím příkladu. V tomto příkladu chceme **SDC** disk:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Pro přístup k prostředí chroot použijte následující příkazy:

      ```
      mkdir /rescue
      mount -o nouuid /dev/sdc2 /rescue
      mount -o nouuid /dev/sdc1 /rescue/boot/
      mount /dev/sdc15 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Řešení potíží s prostředím chroot

   1. Pomocí následujících příkazů ukončete prostředí chroot:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue/boot
      umount /rescue
      ```

      > [!NOTE]
      > Pokud se zobrazí chybová zpráva `unable to unmount /rescue` , přidejte do příkazu umount parametr-l.
      >
      > Příklad: `umount -l /rescue`

1. Odpojte disk od záchranného virtuálního počítače a proveďte prohození disku s původním virtuálním počítačem.
1. Spusťte původní virtuální počítač a ověřte jeho připojení.

## <a name="suse-sles-12-sp4-suse-sles-12-sp4-for-sap---suse-sles-15-sp1-suse-sles-15-sp1-for-sap"></a>SUSE-SLES 12 SP4, SUSE-SLES 12 SP4 pro SAP &&  # # SUSE-SLES 15 SP1, SUSE-SLES 15 SP1 pro SAP

1. Zastavte nebo zrušte přidělení ovlivněného virtuálního počítače.
1. Vytvořte image záchranných virtuálních počítačů se stejnou verzí operačního systému, ve stejné skupině prostředků (RSG) a umístění pomocí spravovaného disku.
1. Pomocí Azure Portal pořídit snímek disku s operačním systémem příslušného virtuálního počítače.
1. Vytvořte disk ze snímku disku s operačním systémem a připojte ho k záchrannému virtuálnímu počítači.
1. Po vytvoření disku vyřešte potíže s chroot prostředím v záchranném virtuálním počítači.

   1. K VIRTUÁLNÍmu počítači se dostanete jako uživatel root pomocí následujícího příkazu:

      `sudo su -`

   1. Najděte disk pomocí `dmesg` (metoda, kterou použijete ke zjištění nového disku, se může lišit). Následující příklad používá **dmesg** k filtrování na discích **SCSI** :

      `dmesg | grep SCSI`

      Výstup bude vypadat podobně jako v následujícím příkladu. V tomto příkladu chceme **SDC** disk:

      ```
      [    0.294784] SCSI subsystem initialized
      [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
      [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
      [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
      [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
      ```

   1. Pro přístup k prostředí chroot použijte následující příkazy:

      ```
      mkdir /rescue
      mount -o nouuid /dev/sdc4 /rescue
      mount -o nouuid /dev/sdc3 /rescue/boot/
      mount /dev/sdc2 /rescue/boot/efi
      cd /rescue

      mount -t proc proc proc
      mount -t sysfs sys sys/
      mount -o bind /dev dev/
      mount -o bind /dev/pts dev/pts/
      mount -o bind /run run/
      chroot /rescue
      ```

   1. Řešení potíží s prostředím chroot

   1. Pomocí následujících příkazů ukončete prostředí chroot:

      ```
      exit

      umount /rescue/proc/
      umount /rescue/sys/
      umount /rescue/dev/pts
      umount /rescue/dev/
      umount /rescue/run
      cd /
      umount /rescue/boot/efi
      umount /rescue/boot
      umount /rescue
      ```

      > [!NOTE]
      > Pokud se zobrazí chybová zpráva `unable to unmount /rescue` , přidejte do příkazu umount parametr-l.
      >
      > Příklad: `umount -l /rescue`

1. Odpojte disk od záchranného virtuálního počítače a proveďte prohození disku s původním virtuálním počítačem.
1. Spusťte původní virtuální počítač a ověřte jeho připojení.

## <a name="next-steps"></a>Další kroky

- [Řešení potíží s připojením SSH](troubleshoot-ssh-connection.md)
