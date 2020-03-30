---
title: Obnovení virtuálních počítačů S Linuxem pomocí chroot, kde se používá LVM (Logický správce svazků) – virtuální počítače Azure
description: Obnovení virtuálních počítačů s Linuxem s LVM.
services: virtual-machines-linux
documentationcenter: ''
author: vilibert
manager: spogge
editor: ''
tags: Linux chroot LVM
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/24/2019
ms.author: vilibert
ms.openlocfilehash: 20d710f717a9dff26f46ac7a201a9b694f3fbe84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74684132"
---
# <a name="troubleshooting-a-linux-vm-when-there-is-no-access-to-the-azure-serial-console-and-the-disk-layout-is-using-lvm-logical-volume-manager"></a>Řešení potíží s virtuálním počítačem s Linuxem, když není přístup k konzolové konzoli Azure a rozložení disku používá LVM (Logical Volume Manager)

Tento průvodce odstraňováním potíží je přínosem pro scénáře, kde není spuštěn virtuální počítač s Operačním systémem Linux, ssh není možné a základní rozložení systému souborů je nakonfigurováno s LVM (Logical Volume Manager).

## <a name="take-snapshot-of-the-failing-vm"></a>Pořízení snímku neúspěšného virtuálního počítače

Pořizovat snímek ovlivněného virtuálního počítače. 

Snímek pak bude připojen k **záchrannému** virtuálnímu počítače. Postupujte podle pokynů [zde](https://docs.microsoft.com/azure/virtual-machines/linux/snapshot-copy-managed-disk#use-azure-portal) o tom, jak pořídit **snímek**.

## <a name="create-a-rescue-vm"></a>Vytvoření záchranného virtuálního virtuálního mísa
Obvykle se doporučuje záchranný virtuální virtuální soud stejné nebo podobné verze operačního systému. Použití stejné **oblasti** a **skupiny prostředků** ovlivněného virtuálního mísy

## <a name="connect-to-the-rescue-vm"></a>Připojení k záchrannému virtuálnímu virtuálnímu zařízení
Připojte se pomocí ssh do **záchranného** virtuálního zařízení. Zvýšit oprávnění a stát se super uživatelem pomocí

`sudo su -`

## <a name="attach-the-disk"></a>Připojte disk
Připojte disk k **záchrannému** virtuálnímu počítače provedenému ze snímku pořízeného dříve.

Portál Azure -> vybrat **záchranné** **disky** > virtuálních počítačů 

![Vytvořit disk](./media/chroot-logical-volume-manager/create-disk-from-snap.png)

Naplňte pole. Přiřaďte nový disk název, vyberte stejnou skupinu prostředků jako snímek, ovlivněný virtuální počítač a záchranný virtuální počítač.

**Typ Zdroje** je **Snímek** .
**Source snímek** je název **snímku** dříve vytvořené.

![vytvořit disk 2](./media/chroot-logical-volume-manager/create-disk-from-snap-2.png)

Vytvořte přípojný bod pro připojený disk.

`mkdir /rescue`

Spuštěnípříkaze **fdisk -l** ověřte, zda byl disk snímku připojen, a uveďte seznam všech dostupných zařízení a oddílů

`fdisk -l`

Ve většině scénářů bude připojený snímek disk zobrazit jako **/dev/sdc** zobrazující dva oddíly **/dev/sdc1** a **/dev/sdc2**

![Fdisk](./media/chroot-logical-volume-manager/fdisk-output-sdc.png)

Označuje **\*** spouštěcí oddíl, oba oddíly mají být připojeny.

Spuštěním příkazu **Lsblk** zobrazíte lvm postiženého virtuálního virtuálního soudu.

`lsblk`

![Spustit lsblk](./media/chroot-logical-volume-manager/lsblk-output-mounted.png)


Ověřte, jestli se zobrazí LVM z ovlivněného virtuálního jevu.
Pokud ne, použijte níže uvedené příkazy, aby jim a znovu spustit **lsblk**.
Ujistěte se, že lvm z připojeného disku viditelné před pokračováním.

```
vgscan --mknodes
vgchange -ay
lvscan
mount –a
lsblk
```

Vyhledejte cestu k připojení logického svazku, který obsahuje oddíl / (kořen). Má konfigurační soubory, jako je /etc/default/grub

V tomto příkladu je přijetí výstupu z předchozího příkazu **lsblk** **rootvg-rootlv** správné **kořenové** LV pro připojení a lze jej použít v dalším příkazu.

Výstup dalšího příkazu zobrazí cestu k připojení pro **kořenové** LV

`pvdisplay -m | grep -i rootlv`

![Rootlv](./media/chroot-logical-volume-manager/locate-rootlv.png)

Pokračujte k připojení tohoto zařízení do adresáře /rescue

`mount /dev/rootvg/rootlv /rescue`

Připojení oddílu, který má **nastavený příznak Boot** na /rescue/boot

`
mount /dev/sdc1 /rescue/boot
`

Ověřte, zda jsou systémy souborů připojeného disku správně připojeny pomocí příkazu **Lsblk**

![Spustit lsblk](./media/chroot-logical-volume-manager/lsblk-output-1.png)

nebo **příkaz df -Th**

![Df](./media/chroot-logical-volume-manager/df-output.png)

## <a name="gaining-chroot-access"></a>Získání přístupu chroot

Získejte **chroot** přístup, který vám umožní provádět různé opravy, existují mírné varianty pro každou distribuci Linuxu.

```
 cd /rescue
 mount -t proc proc proc
 mount -t sysfs sys sys/
 mount -o bind /dev dev/
 mount -o bind /dev/pts dev/pts/
 chroot /rescue
```

Pokud dojde k chybě, například:

**chroot: nepodařilo se spustit příkaz '/bin/bash': Žádný takový soubor nebo adresář**

pokus o připojení logického svazku **USR**

`
mount  /dev/mapper/rootvg-usrlv /rescue/usr
`

> [!TIP]
> Při provádění příkazů v prostředí **chroot,** všimněte si, že jsou spuštěny proti připojenému disku operačního systému a nikoli místní **záchranné** virtuální počítače. 

Příkazy lze použít k instalaci, odebrání a aktualizaci softwaru. Poradce při potížích s virtuálními maješky za účelem opravy chyb.


Vykonat příkaz lsblk a /rescue is now / ![and /rescue/boot is /boot Chrooted](./media/chroot-logical-volume-manager/chrooted.png)

## <a name="perform-fixes"></a>Provádění oprav

### <a name="example-1---configure-the-vm-to-boot-from-a-different-kernel"></a>Příklad 1 – konfigurace spuštění virtuálního počítače z jiného jádra

Běžným scénářem je vynucení spuštění virtuálního počítače z předchozího jádra, protože aktuální nainstalované jádro mohlo být poškozeno nebo upgrade nebyl dokončen správně.


```
cd /boot/grub2

grep -i linux grub.cfg

grub2-editenv list

grub2-set-default "CentOS Linux (3.10.0-1062.1.1.el7.x86_64) 7 (Core)"

grub2-editenv list

grub2-mkconfig -o /boot/grub2/grub.cfg
```

*názorném postupu*

Příkaz **grep** uvádí jádra, která **grub.cfg** ví.
![Jádra](./media/chroot-logical-volume-manager/kernels.png)

**Grub2-editenv seznam** zobrazí, které jádro ![bude načteno při příštím výchozím nastavení jádra](./media/chroot-logical-volume-manager/kernel-default.png)

**grub2-set-default** se používá ke ![změně na jinou sadu grub2 jádra](./media/chroot-logical-volume-manager/grub2-set-default.png)

**grub2-editenv** seznam zobrazuje, které jádro ![bude načteno při příštím startu Nové jádro](./media/chroot-logical-volume-manager/kernel-new.png)

**grub2-mkconfig** obnoví grub.cfg pomocí požadovaných ![verzí Grub2 mkconfig](./media/chroot-logical-volume-manager/grub2-mkconfig.png)



### <a name="example-2---upgrade-packages"></a>Příklad 2 - balíčky pro upgrade

Neúspěšný upgrade jádra může vykreslit nezaváděcí modul virtuálního virtuálního zařízení.
Připojení všech logických svazků, aby bylo možné odebrat nebo přeinstalovat balíčky

Spusťte příkaz **LVs** a ověřte, které **lV** jsou k dispozici pro montáž, každý virtuální počítače, který byl migrován nebo pochází od jiného poskytovatele cloudu, se bude lišit v konfiguraci.

Ukončete prostředí **chroot** připojit požadovaný **LV**

![Upřesnit](./media/chroot-logical-volume-manager/advanced.png)

Nyní přístup **chroot** prostředí znovu spuštěním

`chroot /rescue`

Všechny LV by měly být viditelné jako namontované oddíly

![Upřesnit](./media/chroot-logical-volume-manager/chroot-all-mounts.png)

Dotaz na nainstalované **jádro**

![Upřesnit](./media/chroot-logical-volume-manager/rpm-kernel.png)

V případě potřeby odeberte nebo upgradujte **jádro**
![Upřesnit](./media/chroot-logical-volume-manager/rpm-remove-kernel.png)


### <a name="example-3---enable-serial-console"></a>Příklad 3 – povolit sériovou konzolu
Pokud přístup nebyl možný pro konzolu Azure sériové konzole, ověřte grub parametry konfigurace pro váš virtuální počítač s Linuxem a opravit je. Podrobné informace naleznete [v tomto dokumentu](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-proactive-configuration)

### <a name="example-4---kernel-loading-with-problematic-lvm-swap-volume"></a>Příklad 4 - načítání jádra s problematickým objemem výměny LVM

Virtuální ho virtuálního zařízení se nemusí plně spustit a klesne do výzvy **dracut.**
Další podrobnosti o selhání můžete lokalizovat buď ze sériové konzoly Azure nebo přejděte na Azure Portal -> diagnostika spuštění -> sériový protokol


Může se chybět tato:

```
[  188.000765] dracut-initqueue[324]: Warning: /dev/VG/SwapVol does not exist
         Starting Dracut Emergency Shell...
Warning: /dev/VG/SwapVol does not exist
```

Grub.cfg je nakonfigurován v tomto příkladu načíst LV s názvem **rd.lvm.lv=VG/SwapVol** a virtuální ho dohledu není schopen najít. Tento řádek ukazuje, jak se jádro načítá odkazující na LV SwapVol

```
[    0.000000] Command line: BOOT_IMAGE=/vmlinuz-3.10.0-1062.4.1.el7.x86_64 root=/dev/mapper/VG-OSVol ro console=tty0 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0 biosdevname=0 crashkernel=256M rd.lvm.lv=VG/OSVol rd.lvm.lv=VG/SwapVol nodmraid rhgb quiet
[    0.000000] e820: BIOS-provided physical RAM map:
```

 Odeberte problematický LV z /etc/default/grub konfigurace a znovu grub2.cfg


## <a name="exit-chroot-and-swap-the-os-disk"></a>Ukončete kořenovou moc a zaměřte disk operačního systému

Po opravě problému pokračujte k odpojení a odpojení disku od záchranného virtuálního počítače, který umožňuje jeho výměnu s postiženým diskem operačního systému VM.

```
exit
cd /
umount /rescue/proc/
umount /rescue/sys/
umount /rescue/dev/pts
umount /rescue/dev/
umount /rescue/boot
umount /rescue
```

Odpojte disk od záchranného virtuálního počítače a proveďte prohození disku.

Vyberte virtuální virtuální ms z portálu **Disky** a vyberte **odpojit**
![disk odpojení](./media/chroot-logical-volume-manager/detach-disk.png) 

Uložit změny ![Uložit odpojení](./media/chroot-logical-volume-manager/save-detach.png) 

Disk bude nyní k dispozici umožňuje proměnit s původním diskem operačního systému ovlivněného virtuálního počítače.

Přejděte na webu Azure portal na neúspěšný virtuální počítač a vyberte **disky** -> **Swap OS Disk**
![Swap disk disk](./media/chroot-logical-volume-manager/swap-disk.png) 

Vyplňte pole **Zvolte disk** je snímek disk právě odpojen v předchozím kroku. Název virtuálního_ virtuálního_ **OK**

![Nový disk operačního systému](./media/chroot-logical-volume-manager/new-osdisk.png) 

Pokud virtuální počítač běží disk swap vypne, restartujte virtuální počítač po dokončení operace odkládání disků.


## <a name="next-steps"></a>Další kroky
Další informace:

 [Azure Serial Console]( https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-linux)

[Režim jednoho uživatele](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-grub-single-user-mode)
