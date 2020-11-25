---
title: Obnovení virtuálních počítačů se systémem Linux pomocí chroot, kde se používá LVM (Správce logických svazků) – virtuální počítače Azure
description: Obnovení virtuálních počítačů se systémem Linux pomocí LVMs.
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
ms.openlocfilehash: 390443874ea63a8661ef8baea627015fcf679719
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002693"
---
# <a name="troubleshooting-a-linux-vm-when-there-is-no-access-to-the-azure-serial-console-and-the-disk-layout-is-using-lvm-logical-volume-manager"></a>Řešení potíží s virtuálním počítačem Linux, když není k dispozici přístup ke konzole sériového rozhraní Azure a rozložení disku používá LVM (Správce logických svazků)

Tato příručka pro řešení potíží je výhodná pro situace, kdy se virtuální počítač se systémem Linux nespouští, SSH není možné a základní rozložení systému souborů je nakonfigurované pomocí LVM (Správce logických svazků).

## <a name="take-snapshot-of-the-failing-vm"></a>Pořídit snímek neúspěšného virtuálního počítače

Pořídit snímek ovlivněného virtuálního počítače. 

Snímek se pak připojí k **záchrannému** virtuálnímu počítači. Postupujte podle [pokynů,](../linux/snapshot-copy-managed-disk.md#use-azure-portal) jak si pořídit **snímek**.

## <a name="create-a-rescue-vm"></a>Vytvoření záchranného virtuálního počítače
Obvykle se doporučuje zachránit virtuální počítač se stejnou nebo podobnou verzí operačního systému. Použijte stejnou **oblast** a **skupinu prostředků** ovlivněného virtuálního počítače.

## <a name="connect-to-the-rescue-vm"></a>Připojení k záchrannému virtuálnímu počítači
Připojte se pomocí SSH k **záchrannému** virtuálnímu počítači. Zvýšení oprávnění a použití superuživatele jako uživatel

`sudo su -`

## <a name="attach-the-disk"></a>Připojit disk
Připojte disk k **záchrannému** virtuálnímu počítači vytvořenému ze snímku, který jste provedli dříve.

Azure Portal > výběr **disků** **záchranného** virtuálního počítače > 

![Vytvořit disk](./media/chroot-logical-volume-manager/create-disk-from-snap.png)

Naplňte pole. Přiřaďte novému disku název, vyberte stejnou skupinu prostředků jako snímek, ovlivněný virtuální počítač a záchranný virtuální počítač.

**Typ zdroje** je **snímek** .
**Zdrojový snímek** je název **snímku** , který byl dříve vytvořen.

![vytvořit disk 2](./media/chroot-logical-volume-manager/create-disk-from-snap-2.png)

Vytvořte přípojný bod připojeného disku.

`mkdir /rescue`

Spuštěním příkazu **fdisk-l** ověříte, že je disk snímku připojený a že je v seznamu všechna zařízení a oddíly k dispozici.

`fdisk -l`

U většiny scénářů se k připojenému disku snímku zobrazuje **/dev/sdc** , kde se zobrazují dva oddíly **/dev/sdc1** a **/dev/sdc2** .

![Fdisk](./media/chroot-logical-volume-manager/fdisk-output-sdc.png)

Znak * *\** _ označuje spouštěcí oddíl, oba oddíly budou připojeny.

Spuštěním příkazu _ *lsblk** Zobrazte LVMs OVLIVNĚNÉHO virtuálního počítače.

`lsblk`

![Snímek obrazovky, který zobrazuje výstup z příkazu lsblk](./media/chroot-logical-volume-manager/lsblk-output-mounted.png)


Ověřte, jestli se zobrazuje LVMs z ovlivněného virtuálního počítače.
V takovém případě pomocí následujících příkazů je povolte a znovu spusťte **lsblk**.
Než budete pokračovat, ujistěte se, že je LVMs z připojeného disku viditelný.

```
vgscan --mknodes
vgchange -ay
lvscan
mount –a
lsblk
```

Vyhledejte cestu k připojení logického svazku, který obsahuje oddíl/(root). Obsahuje konfigurační soubory, jako je například/etc/default/grub

V tomto příkladu převezme výstup z předchozího příkazu **lsblk**  **rootvg-rootlv** správnou **kořenovou** hodnotu LV pro připojení a dá se použít v dalším příkazu.

Výstup dalšího příkazu zobrazí cestu pro připojení pro **kořene** LV.

`pvdisplay -m | grep -i rootlv`

![Rootlv](./media/chroot-logical-volume-manager/locate-rootlv.png)

Pokračujte a připojte toto zařízení k adresáři/Rescue

`mount /dev/rootvg/rootlv /rescue`

Připojte oddíl, který má **příznak spouštění** nastavený na/Rescue/Boot.

`
mount /dev/sdc1 /rescue/boot
`

Ověřte, jestli jsou systémy souborů připojeného disku teď správně připojené pomocí příkazu **lsblk** .

![Spustit lsblk](./media/chroot-logical-volume-manager/lsblk-output-1.png)

nebo **DF-th –** příkaz

![Příznak](./media/chroot-logical-volume-manager/df-output.png)

## <a name="gaining-chroot-access"></a>Získání přístupu chroot

Získejte přístup **chroot** , který vám umožní provádět různé opravy, pro každou distribuci Linux existují mírné odchylky.

```
 cd /rescue
 mount -t proc proc proc
 mount -t sysfs sys sys/
 mount -o bind /dev dev/
 mount -o bind /dev/pts dev/pts/
 chroot /rescue
```

V případě, že došlo k chybě, například:

**chroot: nepovedlo se spustit příkaz/bin/bash: žádný takový soubor nebo adresář.**

pokus o připojení logického svazku **usr**

`
mount  /dev/mapper/rootvg-usrlv /rescue/usr
`

> [!TIP]
> Při provádění příkazů v prostředí **chroot** si všimněte, že se spouštějí na připojeném disku s operačním systémem a ne na místním virtuálním počítači pro **záchranu** . 

Příkazy lze použít k instalaci, odebrání a aktualizaci softwaru. Pokud chcete opravit chyby, vyřešte problémy s virtuálními počítači.


Spusťte příkaz lsblk a/Rescue je teď/a/Rescue/Boot je/Boot ![ snímek obrazovky, kde se zobrazí okno konzoly s příkazem BLK l s a jeho výstupní stromová struktura.](./media/chroot-logical-volume-manager/chrooted.png)

## <a name="perform-fixes"></a>Provést opravy

### <a name="example-1---configure-the-vm-to-boot-from-a-different-kernel"></a>Příklad 1 – konfigurace virtuálního počítače pro spuštění z jiného jádra

Běžným scénářem je vynutit spuštění virtuálního počítače z předchozího jádra, protože je možné, že aktuální nainstalované jádro je poškozené nebo že se upgrade nedokončil správně.


```
cd /boot/grub2

grep -i linux grub.cfg

grub2-editenv list

grub2-set-default "CentOS Linux (3.10.0-1062.1.1.el7.x86_64) 7 (Core)"

grub2-editenv list

grub2-mkconfig -o /boot/grub2/grub.cfg
```

*Podrobné*

Příkaz **grep** vypíše seznam jader, na které **grub. cfg** ví.
![Snímek obrazovky znázorňující okno konzoly zobrazující výsledek hledání grep pro jádro.](./media/chroot-logical-volume-manager/kernels.png)

**seznam grub2-editenv** zobrazuje, které jádro se načte při výchozím nastavení dalších spouštěcích ![ jader.](./media/chroot-logical-volume-manager/kernel-default.png)

**grub2-set-default** se používá ke změně na jinou ![ sadu grub2 jádra.](./media/chroot-logical-volume-manager/grub2-set-default.png)

seznam **grub2-editenv** zobrazuje, které jádro se načte při příštím spuštění ![ nového jádra.](./media/chroot-logical-volume-manager/kernel-new.png)

**grub2-mkconfig** znovu sestaví grub. cfg pomocí požadovaných verzí ![ grub2 mkconfig](./media/chroot-logical-volume-manager/grub2-mkconfig.png)



### <a name="example-2---upgrade-packages"></a>Příklad 2 – aktualizace balíčků

Při neúspěšném upgradu jádra se dá virtuální počítač vykreslit jako nespouštěcí.
Připojit všechny logické svazky, aby bylo možné balíčky odebrat nebo přeinstalovat

Spuštěním příkazu **LVS** ověřte, které **LVS** jsou k dispozici pro připojení, všechny virtuální počítače, které byly migrovány nebo pocházejí od jiného poskytovatele cloudu, se budou lišit v konfiguraci.

Ukončení prostředí **chroot** připojit požadované **LV**

![Snímek obrazovky ukazuje okno konzoly s příkazem l v s a pak připojení L V.](./media/chroot-logical-volume-manager/advanced.png)

Teď znovu přihlaste k prostředí **chroot** spuštěním

`chroot /rescue`

Všechny LVs musí být viditelné jako připojené oddíly.

![Snímek obrazovky, který zobrazuje LVs viditelné jako připojené oddíly.](./media/chroot-logical-volume-manager/chroot-all-mounts.png)

Dotaz na nainstalované **jádro**

![Snímek obrazovky zobrazující dotaz na nainstalované jádro](./media/chroot-logical-volume-manager/rpm-kernel.png)

V případě potřeby odebrat nebo upgradovat **kernel** 
 ![ Pokročilé jádro](./media/chroot-logical-volume-manager/rpm-remove-kernel.png)


### <a name="example-3---enable-serial-console"></a>Příklad 3 – povolení sériové konzoly
Pokud nemáte přístup ke konzole sériového prostředí Azure, ověřte parametry konfigurace GRUB pro virtuální počítač se systémem Linux a opravte je. Podrobné informace najdete [v tomto dokumentu](./serial-console-grub-proactive-configuration.md) .

### <a name="example-4---kernel-loading-with-problematic-lvm-swap-volume"></a>Příklad 4 – načítání jádra s problematickým svazkem LVM swap

Spuštění virtuálního počítače se nemusí zdařit a na příkazovém řádku se **Dracut** .
Další podrobnosti o tomto selhání najdete v části buď pomocí sériové konzole Azure, nebo v části > Azure Portal Diagnostika spouštění – > sériového protokolu.


Může se zobrazit chyba podobná této:

```
[  188.000765] dracut-initqueue[324]: Warning: /dev/VG/SwapVol does not exist
         Starting Dracut Emergency Shell...
Warning: /dev/VG/SwapVol does not exist
```

V tomto příkladu je nakonfigurován grub. cfg, aby se načetla LV s názvem **Rd. LVM. lv = VG/SwapVol** a virtuální počítač ho nemůže najít. Tento řádek ukazuje, jak se jádro načítá na SwapVol LV.

```
[    0.000000] Command line: BOOT_IMAGE=/vmlinuz-3.10.0-1062.4.1.el7.x86_64 root=/dev/mapper/VG-OSVol ro console=tty0 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0 biosdevname=0 crashkernel=256M rd.lvm.lv=VG/OSVol rd.lvm.lv=VG/SwapVol nodmraid rhgb quiet
[    0.000000] e820: BIOS-provided physical RAM map:
```

 Odstraňte problematický Lotyšsko z konfigurace/etc/default/grub a znovu sestavte grub2. cfg.


## <a name="exit-chroot-and-swap-the-os-disk"></a>Ukončení chroot a prohození disku s operačním systémem

Po vyřešení problému přejděte k odpojování a odpojte disk od záchranného virtuálního počítače, aby bylo možné ho prohodit s ovlivněným diskem s operačním systémem virtuálního počítače.

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

Vyberte virtuální počítač z **disků** na portálu a vyberte **Odpojit** 
 ![ disk odpojit.](./media/chroot-logical-volume-manager/detach-disk.png) 

Uložení změn ![ Uložit odpojit](./media/chroot-logical-volume-manager/save-detach.png) 

Disk bude nyní k dispozici, takže ho bude možné vyměnit s původním diskem s operačním systémem ovlivněného virtuálního počítače.

Přejděte v Azure Portal k neúspěšnému virtuálnímu počítači a vyberte **disky** odkládací  ->  disk s **operačním systémem**. 
 ![](./media/chroot-logical-volume-manager/swap-disk.png) 

Dokončete pole, na kterých je vybraný **disk** , je snímkový disk, který je právě odpojený v předchozím kroku. Vyžaduje se taky název virtuálního počítače, který se týká daného virtuálního počítače, a pak vyberte **OK** .

![Nový disk s operačním systémem](./media/chroot-logical-volume-manager/new-osdisk.png) 

Pokud je na virtuálním počítači spuštěná Výměna disku, vypne se, jakmile se operace prohození disku dokončí, restartujte virtuální počítač.


## <a name="next-steps"></a>Další kroky
Další informace:

 [Sériová konzola Azure]( ./serial-console-linux.md)

[Režim jednoho uživatele](./serial-console-grub-single-user-mode.md)