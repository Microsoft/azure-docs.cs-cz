---
title: Poradce při potížích se spuštěním virtuálního počítače s Linuxem kvůli chybám systému souborů | Dokumenty společnosti Microsoft
description: Vysvětluje, proč se virtuální počítač s Linuxem nemůže spustit a jak problém vyřešit.
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
ms.openlocfilehash: 455cb1e0067217be6edcf665e8c07e8fcd684ab5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842397"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-file-system-errors"></a>Poradce při potížích se spuštěním virtuálního počítače s Linuxem kvůli chybám systému souborů

K virtuálnímu počítači (VM) Azure Linuxu se nelze připojit pomocí zabezpečeného prostředí (SSH). Při spuštění funkce Boot Diagnostics na [webu Azure Portal](https://portal.azure.com/)se zobrazí položky protokolu, které se podobají následujícím příkladům.

## <a name="examples"></a>Příklady

Následují příklady možných chyb.

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

Tento příklad je způsoben čistý fsck. V tomto případě jsou k virtuálnímu počítače připojeny také další datové disky (/dev/sdc1 a /dev/sde1).

```
Checking all file systems. 
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 65405/1905008 files, 732749/7608064 blocks
[/sbin/fsck.ext4 (1) — /tmp] fsck.ext4 -a /dev/sdc1
[/sbin/fsck.ext4 (2) — /backup] fsck.ext4 -a /dev/sde1
/dev/sdc1: clean, 12/1048576 files, 109842/4192957 blocks
/dev/sde1 : clean, 51/67043328 files, 4259482/268173037 blocks
```

K tomuto problému může dojít, pokud systém souborů nebyl vypnut čistě nebo problémy související s úložištěm. Problémy zahrnují chyby hardwaru nebo softwaru, problémy s ovladači nebo programy, chyby zápisu atd. Vždy je důležité mít zálohu důležitých dat. Nástroje, které popisují v tomto článku může pomoci obnovit systémy souborů, ale je ztráta dat může stále dojít.

Linux má k dispozici několik kontrol. Nejběžnější pro distribuce v Azure jsou: [FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/storage_administration_guide/fsck-fs-specific), [E2FSCK](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/fsck-fs-specific)a [Xfs_repair](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/storage_administration_guide/xfsrepair).

## <a name="resolution"></a>Řešení

Chcete-li tento problém vyřešit, spusťte virtuální ho do nouzového režimu pomocí [sériové konzoly](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux) a pomocí tohoto nástroje k opravě systému souborů. Pokud sériová konzola není na vašem virtuálním počítači povolená nebo nefunguje, přečtěte si článek [Oprava virtuálního počítače offline](#repair-the-vm-offline) v tomto článku.

## <a name="use-the-serial-console"></a>Použití sériové konzoly

1. Připojte se k sériové konzoli.

   > [!Note]
   > Další informace o používání sériové konzole pro Linux naleznete v tématu:
   > * [Použití sériové konzoly pro přístup k režimu GRUB a režimu jednoho uživatele](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console-grub-single-user-mode)
   > * [Použití sériové konzoly pro volání SysRq a NMI](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-nmi-sysrq)

2. Vyberte tlačítko ikony Napájení a pak vyberte Restartovat virtuální počítač. (Pokud konzola sériového zařízení není povolena nebo není úspěšně připojena, tlačítko se nezobrazí.)

   ![OBRÁZEK](./media/linux-recovery-cannot-ssh-to-linux-vm-due-to-file-system-errors-fsck/restart-vm.png)

3. Spusťte virtuální ho do nouzového režimu.

4. Zadejte heslo svého kořenového účtu pro přihlášení do nouzového režimu.

5. Pomocí xfs_repair s možností -n zjišťujte chyby v systému souborů. V následující ukázce předpokládáme, že systémový oddíl je /dev/sda1. Nahraďte ho příslušnou hodnotou pro váš virtuální počítač:

   ```
   xfs_repair -n /dev/sda1
   ```

6. Chcete-li opravit systém souborů, spusťte následující příkaz:

   ```
   xfs_repair /dev/sda1
   ```

7. Pokud se zobrazí chybová zpráva "CHYBA: Souborový systém obsahuje cenné změny metadat v protokolu, který je třeba přehrát", vytvořte dočasný adresář a připojte souborový systém:

   ```
   mkdir /temp
   mount /dev/sda1 /temp
   ```

8. Pokud se disk nepřipojí, spusťte příkaz xfs_repair s volbou -L (vynulování protokolu síly):

   ```
   xfs_repair /dev/sda1 -L
   ```

9. Dále se pokuste připojit systém souborů. Pokud je disk úspěšně připojen, obdržíte následující výstup:
 
   ```
   XFS (sda1): Mounting V1 Filesystem
   XFS (sda1): Ending clean mount
   ```

10. Restartujte virtuální počítač a zkontrolujte, jestli je problém vyřešen.

    ```
    Reboot -f
    ```

## <a name="repair-the-vm-offline"></a>Oprava virtuálního virtuálního montovadova offline

1. Připojte systémový disk virtuálního počítače jako datový disk k virtuálnímu počítači pro obnovení (jakýkoli funkční virtuální počítač s Linuxem). Chcete-li to provést, můžete použít [příkazy příkazu příkazu příkazu příkazy nebo](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux) můžete automatizovat nastavení virtuálního virtuálního soudu pro obnovení pomocí [příkazů pro opravu virtuálních virtuálních stránek](repair-linux-vm-using-azure-virtual-machine-repair-commands.md).

2. Vyhledejte popisek jednotky připojeného systémového disku. V tomto případě předpokládáme, že popisek připojeného systémového disku je /dev/sdc1. Nahraďte ji příslušnou hodnotou pro váš virtuální počítač.

3. Pomocí xfs_repair s možností -n zjišťujte chyby v systému souborů.

   ```
   xfs_repair -n /dev/sdc1
   ```

4. Chcete-li opravit systém souborů, spusťte následující příkaz:

   ```
   xfs_repair /dev/sdc1
   ```

5. Pokud se zobrazí chybová zpráva "CHYBA: Souborový systém obsahuje cenné změny metadat v protokolu, který je třeba přehrát", vytvořte dočasný adresář a připojte souborový systém:

   ```
   mkdir /temp

   mount /dev/sdc1 /temp
   ```

   Pokud se disk nepřipojí, spusťte příkaz xfs_repair s volbou -L (vynulování protokolu síly):

   ```
   xfs_repair /dev/sdc1 -L
   ```

6. Dále se pokuste připojit systém souborů. Pokud je disk úspěšně připojen, obdržíte následující výstup:

   ```
   XFS (sdc1): Mounting V1 Filesystem
   
   XFS (sdc1): Ending clean mount
   ```

7. Odpojte a odpojte původní virtuální pevný disk a potom vytvořte virtuální počítač z původního systémového disku. Chcete-li to provést, můžete použít [příkazy příkazu příkazu příkazu příkazu příkazu příkazu příkazu nebo](troubleshoot-recovery-disks-linux.md) [příkazy pro opravu virtuálního provozu,](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) pokud jste je použili k vytvoření virtuálního virtuálního soudu pro obnovení.

8. Zkontrolujte, zda je problém vyřešen.

## <a name="next-steps"></a>Další kroky

* [Poradce při potížích s virtuálním počítačem s Linuxem připojením disku operačního systému k virtuálnímu počítači pro obnovení pomocí azure CLI 2.0](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-troubleshoot-recovery-disks)
* [Připojení datového disku k virtuálnímu počítači s Linuxem pomocí portálu](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal)

