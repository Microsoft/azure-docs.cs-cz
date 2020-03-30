---
title: Poradce při potížích se spuštěním virtuálního počítače s Linuxem kvůli chybám fstab | Dokumenty společnosti Microsoft
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
ms.openlocfilehash: 7e16eabc4f9572591eabd37b93258fcd783cce7e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351154"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-fstab-errors"></a>Poradce při potížích se spuštěním virtuálního počítače s Linuxem kvůli chybám fstab

K virtuálnímu počítači Azure Linux (VM) se nemůžete připojit pomocí zabezpečeného prostředí (SSH). Při spuštění funkce [Diagnostika spouštění](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) na [webu Azure Portal](https://portal.azure.com/)se zobrazí položky protokolu, které se podobají následujícím příkladům:

## <a name="examples"></a>Příklady

Následují příklady možných chyb.

### <a name="example-1-a-disk-is-mounted-by-the-scsi-id-instead-of-the-universally-unique-identifier-uuid"></a>Příklad 1: Disk je připojen pomocí ID SCSI namísto univerzálně jedinečného identifikátoru (UUID)

```
[K[[1;31m TIME [0m] Timed out waiting for device dev-incorrect.device.
[[1;33mDEPEND[0m] Dependency failed for /data.
[[1;33mDEPEND[0m] Dependency failed for Local File Systems.
…
Welcome to emergency mode! After logging in, type "journalctl -xb" to viewsystem logs, "systemctl reboot" to reboot, "systemctl default" to try again to boot into default mode.
Give root password for maintenance
(or type Control-D to continue)
```

### <a name="example-2-an-unattached-device-is-missing-on-centos"></a>Příklad 2: V CentOS chybí nepřipojené zařízení

```
Checking file systems…
fsck from util-linux 2.19.1
Checking all file systems.
/dev/sdc1: nonexistent device ("nofail" fstab option may be used to skip this device)
/dev/sdd1: nonexistent device ("nofail" fstab option may be used to skip this device)
/dev/sde1: nonexistent device ("nofail" fstab option may be used to skip this device)

[/sbin/fsck.ext3 (1) — /CODE] sck.ext3 -a /dev/sdc1
fsck.ext3: No such file or directory while trying to open /dev/sdc1

/dev/sdc1:
The superblock could not be read or does not describe a correct ext2
filesystem. If the device is valid and it really contains an ext2
filesystem (and not swap or ufs or something else), then the superblock
is corrupt, and you might try running e2fsck with an alternate superblock:

e2fsck -b 8193 <device>

[/sbin/fsck.xfs (1) — /GLUSTERDISK] fsck.xfs -a /dev/sdd1
/sbin/fsck.xfs: /dev/sdd1 does not exist
[/sbin/fsck.ext3 (1) — /DATATEMP] fsck.ext3 -a /dev/sde1 fsck.ext3: No such file or directory while trying to open /dev/sde1
```

### <a name="example-3-a-vm-cannot-start-because-of-an-fstab-misconfiguration-or-because-the-disk-is-no-longer-attached"></a>Příklad 3: Virtuální modul nelze spustit z důvodu chybné konfigurace fstab nebo protože disk již není připojen

```
The disk drive for /var/lib/mysql is not ready yet or not present.
Continue to wait, or Press S to skip mounting or M for manual recovery
```

### <a name="example-4-a-serial-log-entry-shows-an-incorrect-uuid"></a>Příklad 4: Položka sériového protokolu zobrazuje nesprávné UUID

```
Checking filesystems
Checking all file systems.
[/sbin/fsck.ext4 (1) — /] fsck.ext4 -a /dev/sda1
/dev/sda1: clean, 70442/1905008 files, 800094/7608064 blocks
[/sbin/fsck.ext4 (1) — /datadrive] fsck.ext4 -a UUID="<UUID>"
fsck.ext4: Unable to resolve UUID="<UUID>"
[FAILED

*** An error occurred during the file system check.
*** Dropping you to a shell; the system will reboot
*** when you leave the shell.
*** Warning — SELinux is active
*** Disabling security enforcement for system recovery.
*** Run 'setenforce 1' to reenable.
type=1404 audit(1428047455.949:4): enforcing=0 old_enforcing=1 auid=<AUID> ses=4294967295
Give root password for maintenance
(or type Control-D to continue)
```

K tomuto problému může dojít, pokud je nesprávná syntaxe tabulky systémů souborů (fstab) nebo pokud není k virtuálnímu počítače připojen požadovaný datový disk, který je mapován na položku v souboru "/etc/fstab".

## <a name="resolution"></a>Řešení

Chcete-li tento problém vyřešit, spusťte virtuální počítač v nouzovém režimu pomocí sériové konzoly pro virtuální počítače Azure. Potom pomocí nástroje opravte systém souborů. Pokud konzole sériové konzole není na vašem virtuálním počítači povolená, přejděte do části [Oprava virtuálního počítače offline.](#repair-the-vm-offline)

## <a name="use-the-serial-console"></a>Použití sériové konzoly

### <a name="using-single-user-mode"></a>Použití režimu jednoho uživatele

1. Připojte se [k sériové konzoli](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux).
2. Použití sériové konzoly k převzít režim jednoho uživatele [režimu jednoho uživatele](https://docs.microsoft.com/azure/virtual-machines/linux/serial-console-grub-single-user-mode)
3. Jakmile je virtuální měna spuštěna do režimu jednoho uživatele. Pomocí oblíbeného textového editoru otevřete soubor fstab. 

   ```
   # nano /etc/fstab
   ```

4. Zkontrolujte uvedené systémy souborů. Každý řádek v souboru fstab označuje systém souborů, který je připojen při spuštění virtuálního aplikace. Pro více informací o syntaxi souboru fstab, spusťte příkaz man fstab. Chcete-li vyřešit selhání spuštění, zkontrolujte každý řádek a ujistěte se, že je správný ve struktuře i obsahu.

   > [!Note]
   > * Pole na každém řádku jsou oddělena kartami nebo mezerami. Prázdné řádky jsou ignorovány. Řádky, které mají znak čísla (#) jako první znak jsou komentáře. Komentované řádky mohou zůstat v souboru fstab, ale nebudou zpracovány. Doporučujeme, abyste místo odstranění řádků komentovali řádky, o kterých si nejste jisti.
   > * Pro virtuální ho virtuálního mě nit k obnovení a spuštění by měly být oddíly systému souborů pouze požadované oddíly. Virtuální ho virtuálního objektu může dojít k chybám aplikace o další komentáře oddíly. Virtuální virtuální byl však spustit bez další oddíly. Později můžete odkomentovat všechny komentované řádky.
   > * Doporučujeme připojit datové disky na virtuálních počítačích Azure pomocí UUID oddílu systému souborů. Spusťte například následující příkaz:``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Chcete-li určit UUID systému souborů, spusťte příkaz blkid. Další informace o syntaxi získáte spuštěním příkazu man blkid.
   > * Možnost nofail pomáhá zajistit, že se virtuální vír spustí i v případě, že je systém souborů poškozen nebo při spuštění neexistuje systém souborů. Doporučujeme použít možnost nofail v souboru fstab, abyste povolili pokračování při spuštění po chybách v oddílech, které nejsou nutné pro spuštění virtuálního aplikace.

5. Změňte nebo zakomentujte všechny nesprávné nebo nepotřebné řádky v souboru fstab, aby se virtuální svět mohl správně spustit.

6. Uložte změny do souboru fstab.

7. Restartujte virtuální počítač pomocí níže uvedeného příkazu.
   
   ```
   # reboot -f
   ```
> [!Note]
   > Můžete také použít příkaz "ctrl+x", který by také restartoval virtuální počítač.


8. Pokud položky komentář nebo oprava byla úspěšná, systém by měl dosáhnout bash výzvu na portálu. Zkontrolujte, jestli se můžete připojit k virtuálnímu virtuálnímu zařízení.

### <a name="using-root-password"></a>Použití kořenového hesla

1. Připojte se [k sériové konzoli](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux).
2. Přihlaste se k systému pomocí místního uživatele a hesla.

   > [!Note]
   > K přihlášení k systému v konzole sériové ho nelze použít klíč SSH.

3. Vyhledejte chybu, která označuje, že disk nebyl připojen. V následujícím příkladu se systém pokoušel připojit disk, který již nebyl přítomen:

   ```
   [DEPEND] Dependency failed for /datadisk1.
   [DEPEND] Dependency failed for Local File Systems.
   [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
   [DEPEND] Dependency failed for Migrate local... structure to the new structure.
   Welcome to emergency mode! After logging in, type "journalctl -xb" to view
   system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
   Give root password for maintenance
   (or type Control-D to continue):
   ```

4. Připojte se k virtuálnímu počítače pomocí kořenového hesla (virtuální počítače založené na Red Hat).

5. Pomocí oblíbeného textového editoru otevřete soubor fstab. Po navjetí disku spusťte pro Nano následující příkaz:

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Zkontrolujte uvedené systémy souborů. Každý řádek v souboru fstab označuje systém souborů, který je připojen při spuštění virtuálního aplikace. Pro více informací o syntaxi souboru fstab, spusťte příkaz man fstab. Chcete-li vyřešit selhání spuštění, zkontrolujte každý řádek a ujistěte se, že je správný ve struktuře i obsahu.

   > [!Note]
   > * Pole na každém řádku jsou oddělena kartami nebo mezerami. Prázdné řádky jsou ignorovány. Řádky, které mají znak čísla (#) jako první znak jsou komentáře. Komentované řádky mohou zůstat v souboru fstab, ale nebudou zpracovány. Doporučujeme, abyste místo odstranění řádků komentovali řádky, o kterých si nejste jisti.
   > * Pro virtuální ho virtuálního mě nit k obnovení a spuštění by měly být oddíly systému souborů pouze požadované oddíly. Virtuální ho virtuálního objektu může dojít k chybám aplikace o další komentáře oddíly. Virtuální virtuální byl však spustit bez další oddíly. Později můžete odkomentovat všechny komentované řádky.
   > * Doporučujeme připojit datové disky na virtuálních počítačích Azure pomocí UUID oddílu systému souborů. Spusťte například následující příkaz:``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Chcete-li určit UUID systému souborů, spusťte příkaz blkid. Další informace o syntaxi získáte spuštěním příkazu man blkid.
   > * Možnost nofail pomáhá zajistit, že se virtuální vír spustí i v případě, že je systém souborů poškozen nebo při spuštění neexistuje systém souborů. Doporučujeme použít možnost nofail v souboru fstab, abyste povolili pokračování při spuštění po chybách v oddílech, které nejsou nutné pro spuštění virtuálního aplikace.

7. Změňte nebo zakomentujte všechny nesprávné nebo nepotřebné řádky v souboru fstab, aby se virtuální svět mohl správně spustit.

8. Uložte změny do souboru fstab.

9. Restartujte virtuální počítač.

10. Pokud položky komentář nebo oprava byla úspěšná, systém by měl dosáhnout bash výzvu na portálu. Zkontrolujte, jestli se můžete připojit k virtuálnímu virtuálnímu zařízení.

11. Zkontrolujte, zda vaše přípojné body při testování jakékoli fstab změnit spuštěním připojit -příkaz. Pokud nejsou žádné chyby, přípojné body by měly být dobré.

## <a name="repair-the-vm-offline"></a>Oprava virtuálního virtuálního montovadova offline

1. Připojte systémový disk virtuálního počítače jako datový disk k virtuálnímu počítači pro obnovení (jakýkoli funkční virtuální počítač s Linuxem). Chcete-li to provést, můžete použít [příkazy příkazu příkazu příkazu příkazy nebo](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux) můžete automatizovat nastavení virtuálního virtuálního soudu pro obnovení pomocí [příkazů pro opravu virtuálních virtuálních stránek](repair-linux-vm-using-azure-virtual-machine-repair-commands.md).

2. Po připojení systémového disku jako datového disku na virtuální ms pro obnovení zálohujte soubor fstab před prováděním změn a postupujte podle následujících kroků k opravě souboru fstab.

3.    Vyhledejte chybu, která označuje, že disk nebyl připojen. V následujícím příkladu se systém pokoušel připojit disk, který již nebyl přítomen:

    ```
    [DEPEND] Dependency failed for /datadisk1.
    [DEPEND] Dependency failed for Local File Systems.
    [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
    [DEPEND] Dependency failed for Migrate local... structure to the new structure.
    Welcome to emergency mode! After logging in, type "journalctl -xb" to view system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
    Give root password for maintenance (or type Control-D to continue):
    ```

4. Připojte se k virtuálnímu počítače pomocí kořenového hesla (virtuální počítače založené na Red Hat).

5. Pomocí oblíbeného textového editoru otevřete soubor fstab. Po navjetí disku spusťte následující příkaz pro Nano. Ujistěte se, že pracujete na souboru fstab, který je umístěn na připojeném disku, a ne na souboru fstab, který je na záchranném virtuálním počítači.

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Zkontrolujte uvedené systémy souborů. Každý řádek v souboru fstab označuje systém souborů, který je připojen při spuštění virtuálního aplikace. Pro více informací o syntaxi souboru fstab, spusťte příkaz man fstab. Chcete-li vyřešit selhání spuštění, zkontrolujte každý řádek a ujistěte se, že je správný ve struktuře i obsahu.

   > [!Note]
   > * Pole na každém řádku jsou oddělena kartami nebo mezerami. Prázdné řádky jsou ignorovány. Řádky, které mají znak čísla (#) jako první znak jsou komentáře. Komentované řádky mohou zůstat v souboru fstab, ale nebudou zpracovány. Doporučujeme, abyste místo odstranění řádků komentovali řádky, o kterých si nejste jisti.
   > * Pro virtuální ho virtuálního mě nit k obnovení a spuštění by měly být oddíly systému souborů pouze požadované oddíly. Virtuální ho virtuálního objektu může dojít k chybám aplikace o další komentáře oddíly. Virtuální virtuální byl však spustit bez další oddíly. Později můžete odkomentovat všechny komentované řádky.
   > * Doporučujeme připojit datové disky na virtuálních počítačích Azure pomocí UUID oddílu systému souborů. Spusťte například následující příkaz:``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Chcete-li určit UUID systému souborů, spusťte příkaz blkid. Další informace o syntaxi získáte spuštěním příkazu man blkid. Všimněte si, že disk, který chcete obnovit, je teď připojený k novému virtuálnímu počítači. Přestože UUID by měly být konzistentní, ID oddílu zařízení (například "/dev/sda1") se na tomto virtuálním počítači liší. Oddíly systému souborů původního selhání virtuálního počítači, které jsou umístěny na non-systém v hd nejsou k dispozici pro obnovení virtuálního počítači [pomocí příkazů rozhraní příkazu příkazu příkazu příkazu .](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-recovery-disks-linux)
   > * Možnost nofail pomáhá zajistit, že se virtuální vír spustí i v případě, že je systém souborů poškozen nebo při spuštění neexistuje systém souborů. Doporučujeme použít možnost nofail v souboru fstab, abyste povolili pokračování při spuštění po chybách v oddílech, které nejsou nutné pro spuštění virtuálního aplikace.

7. Změňte nebo zakomentujte všechny nesprávné nebo nepotřebné řádky v souboru fstab, aby se virtuální svět mohl správně spustit.

8. Uložte změny do souboru fstab.

9. Restartujte virtuální počítač nebo znovu vytvořte původní virtuální počítač.

10. Pokud položky komentář nebo oprava byla úspěšná, systém by měl dosáhnout bash výzvu na portálu. Zkontrolujte, jestli se můžete připojit k virtuálnímu virtuálnímu zařízení.

11. Zkontrolujte, zda vaše přípojné body při testování jakékoli fstab změnit spuštěním připojit -příkaz. Pokud nejsou žádné chyby, přípojné body by měly být dobré.

12. Odpojte a odpojte původní virtuální pevný disk a potom vytvořte virtuální počítač z původního systémového disku. Chcete-li to provést, můžete použít [příkazy příkazu příkazu příkazu příkazu příkazu příkazu příkazu nebo](troubleshoot-recovery-disks-linux.md) [příkazy pro opravu virtuálního provozu,](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) pokud jste je použili k vytvoření virtuálního virtuálního soudu pro obnovení.

13. Po vytvoření virtuálního virtuálního uživatele znovu a můžete se k němu připojit prostřednictvím SSH, provést následující akce:
    * Zkontrolujte všechny řádky fstab, které byly během obnovení změněny nebo zakomentovány.
    * Ujistěte se, že používáte UUID a možnost nofail vhodně.
    * Otestujte všechny změny fstab před restartováním virtuálního počítače. Chcete-li to provést, použijte následující příkaz:``$ sudo mount -a``
    * Vytvořte další kopii opraveného souboru fstab pro použití v budoucích scénářích obnovení.

## <a name="next-steps"></a>Další kroky

* [Poradce při potížích s virtuálním počítačem s Linuxem připojením disku operačního systému k virtuálnímu počítači pro obnovení pomocí azure CLI 2.0](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-troubleshoot-recovery-disks)
* [Poradce při potížích s virtuálním počítačem s Linuxem připojením disku operačního systému k virtuálnímu počítači pro obnovení pomocí portálu Azure](https://docs.microsoft.com/azure/virtual-machines/linux/troubleshoot-recovery-disks-portal)

