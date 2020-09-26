---
title: Řešení potíží se spouštěním virtuálního počítače se systémem Linux kvůli chybám systému souborů | Microsoft Docs
description: Naučte se řešit potíže se spouštěním virtuálních počítačů se systémem Linux kvůli chybám systému souborů.
services: virtual-machines-linux
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 10/09/2019
ms.author: v-six
ms.openlocfilehash: 8f12b88a0ddbc6ae31f40ab31b0126e4fd66b1a5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91325942"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-file-system-errors"></a>Řešení potíží se spouštěním virtuálních počítačů se systémem Linux kvůli chybám systému souborů

Nemůžete se připojit k virtuálnímu počítači Azure Linux pomocí Secure Shell (SSH). Když spustíte funkci diagnostiky spouštění na [Azure Portal](https://portal.azure.com/), zobrazí se položky protokolu, které budou vypadat jako v následujících příkladech.

## <a name="examples"></a>Příklady

Níže jsou uvedeny příklady možných chyb.

### <a name="example-1"></a>Příklad 1 

```
Checking all file systems.
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1 contains a file system with errors, check forced.
/dev/sda1: Inodes that were part of a corrupted orphan linked list found.
/dev/sda1: UNEXPECTED INCONSISTENCY; RUN fsck MANUALLY
```

### <a name="example-2"></a>Příklad 2

```
EXT4-fs (sda1): INFO: recovery required on readonly filesystem
EXT4-fs (sda1): write access will be enabled during recovery
EXT4-fs warning (device sda1): ext4_clear_journal_err:4531: Filesystem error recorded from previous mount: IO failure
EXT4-fs warning (device sda1): ext4_clear_journal_err:4532: Making fs in need of filesystem check.
```

### <a name="example-3"></a>Příklad 3

```
[  14.252404] EXT4-fs (sda1): Couldn’t remount RDWR because of unprocessed orphan inode list.  Please unmount/remount instead
An error occurred while mounting /.
```

### <a name="example-4"></a>Příklad 4 

Tento příklad je způsoben čistým fsck. V tomto případě jsou k virtuálnímu počítači připojené taky další datové disky (/dev/sdc1 a/dev/SDE1).

```
Checking all file systems. 
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 65405/1905008 files, 732749/7608064 blocks
[/sbin/fsck.ext4 (1) — /tmp] fsck.ext4 -a /dev/sdc1
[/sbin/fsck.ext4 (2) — /backup] fsck.ext4 -a /dev/sde1
/dev/sdc1: clean, 12/1048576 files, 109842/4192957 blocks
/dev/sde1 : clean, 51/67043328 files, 4259482/268173037 blocks
```

K tomuto problému může dojít v případě, že systém souborů nebyl vypnutý čistě nebo dochází k problémům souvisejícím s úložištěm. Mezi tyto problémy patří hardwarové nebo softwarové chyby, problémy s ovladači nebo programy, chyby zápisu atd. Vždy je důležité mít zálohu důležitých dat. Nástroje, které popisují tento článek, můžou pomáhat obnovit systémy souborů, ale může dojít ke ztrátě dat.

Pro Linux je k dispozici několik kontrol systému souborů. Nejběžnější je distribuce v Azure: [FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/fsck-fs-specific), [E2FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/fsck-fs-specific)a [Xfs_repair](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/xfsrepair).

## <a name="resolution"></a>Řešení

Chcete-li tento problém vyřešit, spusťte virtuální počítač do nouzového režimu pomocí [konzoly sériového portu](./serial-console-linux.md) a pomocí tohoto nástroje opravte systém souborů. Pokud není na vašem VIRTUÁLNÍm počítači povolená konzola sériového prostředí nebo nefunguje, přečtěte si část [opravy offline virtuálního počítače](#repair-the-vm-offline) v tomto článku.

## <a name="use-the-serial-console"></a>Použití sériové konzoly

1. Připojte se ke konzole sériového portu.

   > [!Note]
   > Další informace o používání sériové konzoly pro Linux najdete v těchto tématech:
   > * [Použití sériové konzoly pro přístup k GRUB a jednomu uživatelskému režimu](serial-console-grub-single-user-mode.md)
   > * [Použití sériové konzoly pro volání SysRq a NMI](./serial-console-nmi-sysrq.md)

2. Vyberte tlačítko ikona napájení a pak vyberte restartovat virtuální počítač. (Pokud není sériová konzola povolená nebo není úspěšně připojená, tlačítko se nezobrazí.)

   ![OBRÁZEK](./media/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck/restart-vm.png)

3. Spusťte virtuální počítač v režimu nouze.

4. Zadejte heslo ke kořenovému účtu pro přihlášení do nouzového režimu.

5. K detekci chyb v systému souborů použijte xfs_repair s možností-n. V následující ukázce předpokládáme, že systémový oddíl je/dev/sda1. Nahraďte příslušnou hodnotou pro váš virtuální počítač:

   ```
   xfs_repair -n /dev/sda1
   ```

6. Spusťte následující příkaz, který opraví systém souborů:

   ```
   xfs_repair /dev/sda1
   ```

7. Pokud se zobrazí chybová zpráva "Chyba: systém souborů má cenné změny metadat v protokolu, které je třeba přehrajte", vytvořte dočasný adresář a připojte systém souborů:

   ```
   mkdir /temp
   mount /dev/sda1 /temp
   ```

8. Pokud se disk nepovede připojit, spusťte příkaz xfs_repair s možností-L (vynucení nulového protokolu):

   ```
   xfs_repair /dev/sda1 -L
   ```

9. Potom se pokuste připojit systém souborů. Pokud je disk úspěšně připojen, zobrazí se následující výstup:
 
   ```
   XFS (sda1): Mounting V1 Filesystem
   XFS (sda1): Ending clean mount
   ```

10. Restartujte virtuální počítač a potom zkontrolujte, jestli je problém vyřešený.

    ```
    Reboot -f
    ```

## <a name="repair-the-vm-offline"></a>Oprava virtuálního počítače v režimu offline

1. Připojte systémový disk virtuálního počítače jako datový disk k virtuálnímu počítači pro obnovení (libovolný pracovní virtuální počítač se systémem Linux). K tomu můžete použít [příkazy rozhraní příkazového řádku](./troubleshoot-recovery-disks-linux.md) nebo můžete automatizovat nastavení virtuálního počítače pro obnovení pomocí příkazů pro [opravu virtuálního počítače](repair-linux-vm-using-azure-virtual-machine-repair-commands.md).

2. Vyhledejte označení jednotky systémového disku, který jste připojili. V takovém případě předpokládáme, že popisek systémového disku, který jste připojili, je/dev/sdc1.. Nahraďte ji odpovídající hodnotou pro váš virtuální počítač.

3. K detekci chyb v systému souborů použijte xfs_repair s možností-n.

   ```
   xfs_repair -n /dev/sdc1
   ```

4. Spusťte následující příkaz, který opraví systém souborů:

   ```
   xfs_repair /dev/sdc1
   ```

5. Pokud se zobrazí chybová zpráva "Chyba: systém souborů má cenné změny metadat v protokolu, které je třeba přehrajte", vytvořte dočasný adresář a připojte systém souborů:

   ```
   mkdir /temp

   mount /dev/sdc1 /temp
   ```

   Pokud se disk nepovede připojit, spusťte příkaz xfs_repair s možností-L (vynucení nulového protokolu):

   ```
   xfs_repair /dev/sdc1 -L
   ```

6. Potom se pokuste připojit systém souborů. Pokud je disk úspěšně připojen, zobrazí se následující výstup:

   ```
   XFS (sdc1): Mounting V1 Filesystem
   
   XFS (sdc1): Ending clean mount
   ```

7. Odpojte a odpojte původní virtuální pevný disk a pak vytvořte virtuální počítač z původního systémového disku. K tomu můžete použít [příkazy rozhraní příkazového řádku](troubleshoot-recovery-disks-linux.md) nebo [příkazy pro opravu virtuálního počítače](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) , pokud jste je použili k vytvoření virtuálního počítače pro obnovení.

8. Ověřte, zda byl problém vyřešen.

## <a name="next-steps"></a>Další kroky

* [Řešení potíží s virtuálním počítačem se systémem Linux připojením disku s operačním systémem k virtuálnímu počítači pro obnovení pomocí Azure CLI 2,0](./troubleshoot-recovery-disks-linux.md)
* [Připojení datového disku k virtuálnímu počítači se systémem Linux pomocí portálu](../linux/attach-disk-portal.md)
