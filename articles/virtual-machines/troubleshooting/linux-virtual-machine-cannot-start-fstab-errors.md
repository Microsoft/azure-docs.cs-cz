---
title: Řešení potíží se spouštěním virtuálních počítačů s Linuxem z důvodu chyb fstab | Microsoft Docs
description: Vysvětluje, proč nelze spustit virtuální počítač se systémem Linux a jak tento problém vyřešit.
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
ms.openlocfilehash: fd49993e6825c47bbae8f034715c03191e06ab2d
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89441659"
---
# <a name="troubleshoot-linux-vm-starting-issues-due-to-fstab-errors"></a>Řešení potíží se spouštěním virtuálních počítačů s Linuxem z důvodu chyb fstab

K virtuálnímu počítači Azure Linux se nemůžete připojit pomocí připojení Secure Shell (SSH). Když spustíte funkci [diagnostiky spouštění](./boot-diagnostics.md) na [Azure Portal](https://portal.azure.com/), zobrazí se položky protokolu, které budou vypadat jako v následujících příkladech:

## <a name="examples"></a>Příklady

Níže jsou uvedeny příklady možných chyb.

### <a name="example-1-a-disk-is-mounted-by-the-scsi-id-instead-of-the-universally-unique-identifier-uuid"></a>Příklad 1: disk je připojen pomocí ID SCSI místo univerzálního jedinečného identifikátoru (UUID).

```
[K[[1;31m TIME [0m] Timed out waiting for device dev-incorrect.device.
[[1;33mDEPEND[0m] Dependency failed for /data.
[[1;33mDEPEND[0m] Dependency failed for Local File Systems.
…
Welcome to emergency mode! After logging in, type "journalctl -xb" to viewsystem logs, "systemctl reboot" to reboot, "systemctl default" to try again to boot into default mode.
Give root password for maintenance
(or type Control-D to continue)
```

### <a name="example-2-an-unattached-device-is-missing-on-centos"></a>Příklad 2: v CentOS chybí nepřipojené zařízení

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

### <a name="example-3-a-vm-cannot-start-because-of-an-fstab-misconfiguration-or-because-the-disk-is-no-longer-attached"></a>Příklad 3: virtuální počítač nejde spustit z důvodu nefstabé konfigurace nebo to, že disk už není připojený.

```
The disk drive for /var/lib/mysql is not ready yet or not present.
Continue to wait, or Press S to skip mounting or M for manual recovery
```

### <a name="example-4-a-serial-log-entry-shows-an-incorrect-uuid"></a>Příklad 4: položka sériového protokolu zobrazuje nesprávný UUID

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

K tomuto problému může dojít v případě, že syntaxe tabulky systému souborů (fstab) je nesprávná nebo pokud není k virtuálnímu počítači připojen požadovaný datový disk, který je namapován na záznam v souboru "/etc/fstab".

## <a name="resolution"></a>Řešení

Pokud chcete tento problém vyřešit, spusťte virtuální počítač v nouzovém režimu pomocí sériové konzoly pro Azure Virtual Machines. Pak pomocí nástroje opravte systém souborů. Pokud není na vašem VIRTUÁLNÍm počítači povolená sériová konzola, přečtěte si část [opravy offline virtuálního počítače](#repair-the-vm-offline) .

## <a name="use-the-serial-console"></a>Použití sériové konzoly

### <a name="using-single-user-mode"></a>Použití režimu Single User

1. Připojte se ke [konzole sériového portu](./serial-console-linux.md).
2. Použití konzoly sériového režimu pro jeden [uživatelský](serial-console-grub-single-user-mode.md) režim single user
3. Po spuštění virtuálního počítače do režimu jednoho uživatele. K otevření souboru fstab použijte oblíbený textový editor. 

   ```
   # nano /etc/fstab
   ```

4. Zkontrolujte uvedené systémy souborů. Každý řádek v souboru fstab označuje systém souborů, který je připojený při spuštění virtuálního počítače. Další informace o syntaxi souboru fstab spustíte příkazem Man fstab. Chcete-li vyřešit chybu spuštění, zkontrolujte jednotlivé řádky a ujistěte se, že jsou správné v obou strukturách i v obsahu.

   > [!Note]
   > * Pole na každém řádku jsou oddělená tabulátory nebo mezerami. Prázdné řádky jsou ignorovány. Řádky, které mají znak čísla (#) jako první znak jsou komentáře. Řádky s komentářem mohou zůstat v souboru fstab, ale nebudou zpracovány. Doporučujeme, abyste fstab řádky, na které si nejste jisti, místo odebrání řádků.
   > * Aby se virtuální počítač mohl zotavit a spustit, měly by být oddíly systému souborů jedinými požadovanými oddíly. Virtuální počítač může zaznamenat chyby aplikace o dalších oddílech s komentářem. Virtuální počítač by se ale měl spustit bez dalších oddílů. Později můžete odkomentovat všechny řádky s komentářem.
   > * Pro virtuální počítače Azure doporučujeme připojit datové disky pomocí identifikátoru UUID oddílu systému souborů. Například spusťte následující příkaz: ``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Chcete-li zjistit UUID systému souborů, spusťte příkaz blkid. Další informace o syntaxi získáte spuštěním příkazu man blkid.
   > * Možnost neúspěchu pomáhá zajistit, že se virtuální počítač spustí i v případě, že je systém souborů poškozený nebo když systém souborů neexistuje při spuštění. Doporučujeme, abyste v souboru fstab použili možnost neúspěšného spuštění, abyste mohli v případě výskytu chyb v oddílech, které nejsou nutné k tomu, aby se virtuální počítač spustil, pokračovat v práci.

5. Pokud chcete povolit, aby se virtuální počítač spouštěl správně, změňte nebo zakomentujte všechny nesprávné nebo nepotřebné řádky v souboru fstab.

6. Uložte změny do souboru fstab.

7. Restartujte virtuální počítač pomocí níže uvedeného příkazu.
   
   ```
   # reboot -f
   ```
> [!Note]
   > Můžete také použít příkaz "Ctrl + x", který také restartuje virtuální počítač.


8. Pokud komentář nebo opravu záznamů proběhla úspěšně, měl by se systém dostat na výzvu bash na portálu. Ověřte, jestli se můžete připojit k virtuálnímu počítači.

### <a name="using-root-password"></a>Použití kořenového hesla

1. Připojte se ke [konzole sériového portu](./serial-console-linux.md).
2. Přihlaste se k systému pomocí místního uživatele a hesla.

   > [!Note]
   > Nelze použít klíč SSH pro přihlášení k systému v konzole sériového portu.

3. Vyhledejte chybu, která indikuje, že disk není připojený. V následujícím příkladu se systém pokusil připojit disk, který již není přítomen:

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

4. Připojte se k virtuálnímu počítači pomocí kořenového hesla (virtuální počítače se systémem Red Hat).

5. K otevření souboru fstab použijte oblíbený textový editor. Po připojení disku spusťte následující příkaz pro nano:

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Zkontrolujte uvedené systémy souborů. Každý řádek v souboru fstab označuje systém souborů, který je připojený při spuštění virtuálního počítače. Další informace o syntaxi souboru fstab spustíte příkazem Man fstab. Chcete-li vyřešit chybu spuštění, zkontrolujte jednotlivé řádky a ujistěte se, že jsou správné v obou strukturách i v obsahu.

   > [!Note]
   > * Pole na každém řádku jsou oddělená tabulátory nebo mezerami. Prázdné řádky jsou ignorovány. Řádky, které mají znak čísla (#) jako první znak jsou komentáře. Řádky s komentářem mohou zůstat v souboru fstab, ale nebudou zpracovány. Doporučujeme, abyste fstab řádky, na které si nejste jisti, místo odebrání řádků.
   > * Aby se virtuální počítač mohl zotavit a spustit, měly by být oddíly systému souborů jedinými požadovanými oddíly. Virtuální počítač může zaznamenat chyby aplikace o dalších oddílech s komentářem. Virtuální počítač by se ale měl spustit bez dalších oddílů. Později můžete odkomentovat všechny řádky s komentářem.
   > * Pro virtuální počítače Azure doporučujeme připojit datové disky pomocí identifikátoru UUID oddílu systému souborů. Například spusťte následující příkaz: ``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Chcete-li zjistit UUID systému souborů, spusťte příkaz blkid. Další informace o syntaxi získáte spuštěním příkazu man blkid.
   > * Možnost neúspěchu pomáhá zajistit, že se virtuální počítač spustí i v případě, že je systém souborů poškozený nebo když systém souborů neexistuje při spuštění. Doporučujeme, abyste v souboru fstab použili možnost neúspěšného spuštění, abyste mohli v případě výskytu chyb v oddílech, které nejsou nutné k tomu, aby se virtuální počítač spustil, pokračovat v práci.

7. Pokud chcete povolit, aby se virtuální počítač spouštěl správně, změňte nebo zakomentujte všechny nesprávné nebo nepotřebné řádky v souboru fstab.

8. Uložte změny do souboru fstab.

9. Restartujte virtuální počítač.

10. Pokud komentář nebo opravu záznamů proběhla úspěšně, měl by se systém dostat na výzvu bash na portálu. Ověřte, jestli se můžete připojit k virtuálnímu počítači.

11. Zkontrolujte přípojné body, když otestujete jakoukoli fstab změnu spuštěním příkazu Mount – a. Pokud nedochází k žádným chybám, měly by být přípojné body správné.

## <a name="repair-the-vm-offline"></a>Oprava virtuálního počítače v režimu offline

1. Připojte systémový disk virtuálního počítače jako datový disk k virtuálnímu počítači pro obnovení (libovolný pracovní virtuální počítač se systémem Linux). K tomu můžete použít [příkazy rozhraní příkazového řádku](./troubleshoot-recovery-disks-linux.md) nebo můžete automatizovat nastavení virtuálního počítače pro obnovení pomocí příkazů pro [opravu virtuálního počítače](repair-linux-vm-using-azure-virtual-machine-repair-commands.md).

2. Jakmile připojíte systémový disk jako datový disk na virtuálním počítači pro obnovení, před provedením změn zálohujte soubor fstab a pak postupujte podle dalších kroků a opravte soubor fstab.

3. Vyhledejte chybu, která indikuje, že disk není připojený. V následujícím příkladu se systém pokusil připojit disk, který již není přítomen:

   ```output
   [DEPEND] Dependency failed for /datadisk1.
   [DEPEND] Dependency failed for Local File Systems.
   [DEPEND] Dependency failed for Relabel all filesystems, if necessary.
   [DEPEND] Dependency failed for Migrate local... structure to the new structure.
   Welcome to emergency mode! After logging in, type "journalctl -xb" to view system logs, "systemctl reboot" to reboot, "systemctl default" or ^D to try again to boot into default mode.
   Give root password for maintenance (or type Control-D to continue):
   ```

4. Připojte se k virtuálnímu počítači pomocí kořenového hesla (virtuální počítače se systémem Red Hat).

5. K otevření souboru fstab použijte oblíbený textový editor. Po připojení disku spusťte následující příkaz pro nano. Ujistěte se, že pracujete na souboru fstab, který je umístěný na připojeném disku, a ne na fstab soubor, který je na záchranném virtuálním počítači.

   ```
   $ nano /mnt/troubleshootingdisk/etc/fstab
   ```

6. Zkontrolujte uvedené systémy souborů. Každý řádek v souboru fstab označuje systém souborů, který je připojený při spuštění virtuálního počítače. Další informace o syntaxi souboru fstab spustíte příkazem Man fstab. Chcete-li vyřešit chybu spuštění, zkontrolujte jednotlivé řádky a ujistěte se, že jsou správné v obou strukturách i v obsahu.

   > [!Note]
   > * Pole na každém řádku jsou oddělená tabulátory nebo mezerami. Prázdné řádky jsou ignorovány. Řádky, které mají znak čísla (#) jako první znak jsou komentáře. Řádky s komentářem mohou zůstat v souboru fstab, ale nebudou zpracovány. Doporučujeme, abyste fstab řádky, na které si nejste jisti, místo odebrání řádků.
   > * Aby se virtuální počítač mohl zotavit a spustit, měly by být oddíly systému souborů jedinými požadovanými oddíly. Virtuální počítač může zaznamenat chyby aplikace o dalších oddílech s komentářem. Virtuální počítač by se ale měl spustit bez dalších oddílů. Později můžete odkomentovat všechny řádky s komentářem.
   > * Pro virtuální počítače Azure doporučujeme připojit datové disky pomocí identifikátoru UUID oddílu systému souborů. Například spusťte následující příkaz: ``/dev/sdc1: LABEL="cloudimg-rootfs" UUID="<UUID>" TYPE="ext4" PARTUUID="<PartUUID>"``
   > * Chcete-li zjistit UUID systému souborů, spusťte příkaz blkid. Další informace o syntaxi získáte spuštěním příkazu man blkid. Všimněte si, že disk, který chcete obnovit, je nyní připojen k novému virtuálnímu počítači. I když by identifikátory UUID měly být konzistentní, ID oddílů zařízení (například "/dev/sda1") se na tomto virtuálním počítači liší. Oddíly systému souborů původního neúspěšného virtuálního počítače, které jsou umístěné na nesystémovém virtuálním pevném disku, nejsou k dispozici pro virtuální počítač pro obnovení [pomocí příkazů rozhraní příkazového řádku](./troubleshoot-recovery-disks-linux.md).
   > * Možnost neúspěchu pomáhá zajistit, že se virtuální počítač spustí i v případě, že je systém souborů poškozený nebo když systém souborů neexistuje při spuštění. Doporučujeme, abyste v souboru fstab použili možnost neúspěšného spuštění, abyste mohli v případě výskytu chyb v oddílech, které nejsou nutné k tomu, aby se virtuální počítač spustil, pokračovat v práci.

7. Pokud chcete povolit, aby se virtuální počítač spouštěl správně, změňte nebo zakomentujte všechny nesprávné nebo nepotřebné řádky v souboru fstab.

8. Uložte změny do souboru fstab.

9. Restartujte virtuální počítač nebo znovu sestavte původní virtuální počítač.

10. Pokud komentář nebo opravu záznamů proběhla úspěšně, měl by se systém dostat na výzvu bash na portálu. Ověřte, jestli se můžete připojit k virtuálnímu počítači.

11. Zkontrolujte přípojné body, když otestujete jakoukoli fstab změnu spuštěním příkazu Mount – a. Pokud nedochází k žádným chybám, měly by být přípojné body správné.

12. Odpojte a odpojte původní virtuální pevný disk a pak vytvořte virtuální počítač z původního systémového disku. K tomu můžete použít [příkazy rozhraní příkazového řádku](troubleshoot-recovery-disks-linux.md) nebo [příkazy pro opravu virtuálního počítače](repair-linux-vm-using-azure-virtual-machine-repair-commands.md) , pokud jste je použili k vytvoření virtuálního počítače pro obnovení.

13. Jakmile znovu vytvoříte virtuální počítač a můžete se k němu připojit přes SSH, proveďte následující akce:
    * Projděte si všechny fstab řádky, které se změnily nebo Zakomentovat při obnovení.
    * Ujistěte se, že používáte UUID a možnost neúspěchu.
    * Před restartováním virtuálního počítače otestujte všechny fstab změny. K tomu použijte následující příkaz: ``$ sudo mount -a``
    * Vytvořte další kopii opraveného souboru fstab pro použití v budoucích scénářích obnovení.

## <a name="next-steps"></a>Další kroky

* [Řešení potíží s virtuálním počítačem se systémem Linux připojením disku s operačním systémem k virtuálnímu počítači pro obnovení pomocí Azure CLI 2,0](./troubleshoot-recovery-disks-linux.md)
* [Řešení potíží s virtuálním počítačem se systémem Linux připojením disku s operačním systémem k virtuálnímu počítači pro obnovení pomocí Azure Portal](./troubleshoot-recovery-disks-portal-linux.md)
