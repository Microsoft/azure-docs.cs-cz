---
title: Vytvoření a nahrání virtuálního pevného disku SUSE Linux v Azure
description: Naučte se vytvářet a nahrávat virtuální pevný disk Azure (VHD), který obsahuje operační systém SUSE Linux.
author: gbowerman
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/12/2018
ms.author: guybo
ms.openlocfilehash: 032b49631c6adb30d4b25f8b82d35dab49ffd3a2
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757669"
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Příprava virtuálního počítače se SLES nebo openSUSE pro Azure


Tento článek předpokládá, že jste již nainstalovali operační systém SUSE nebo openSUSE Linux na virtuální pevný disk. Existuje více nástrojů pro vytváření souborů .vhd, například virtualizační řešení, jako je Hyper-V. Pokyny naleznete [v tématu Instalace role Technologie Hyper-V a Konfigurace virtuálního počítače](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="sles--opensuse-installation-notes"></a>SLES / openSUSE poznámky k instalaci
* Další tipy na přípravu Linuxu pro Azure najdete také v [obecných poznámkách k instalaci Linuxu.](create-upload-generic.md#general-linux-installation-notes)
* Formát VHDX není v Azure podporován, pouze **pevný virtuální pevný disk .**  Disk můžete převést do formátu Virtuálního pevného disku pomocí Správce technologie Hyper-V nebo rutiny convert-vhd.
* Při instalaci systému Linux se doporučuje používat standardní oddíly spíše než LVM (často výchozí pro mnoho instalací). Tím se zabrání lVM název konflikty s klonované virtuální počítače, zejména v případě, že disk operačního systému někdy musí být připojen k jinému virtuálnímu počítače pro řešení potíží. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) mohou být použity na datových discích, pokud je to výhodné.
* Nekonfigurujte odkládací oddíl na disku operačního systému. Agenta Linuxu lze nakonfigurovat tak, aby vytvořil odkládací soubor na disku s dočasným prostředkem.  Další informace o tom naleznete v následujících krocích.
* Všechny virtuální počítače v Azure musí mít virtuální velikost zarovnanou na 1 MB. Při převodu ze surového disku na virtuální pevný disk je nutné zajistit, aby velikost nezpracovaného disku byla před převodem násobkem 1 MB. Další informace naleznete v [poznámkách k instalaci Linuxu.](create-upload-generic.md#general-linux-installation-notes)

## <a name="use-suse-studio"></a>Použití suse studia
[SUSE Studio](http://www.susestudio.com) můžete snadno vytvářet a spravovat vaše SLES a openSUSE image pro Azure a Hyper-V. Toto je doporučený přístup pro přizpůsobení vlastních obrázků SLES a openSUSE.

Jako alternativu k budování vlastní VHD, SUSE také publikuje BYOS (Přineste si vlastní předplatné) obrázky pro SLES na [VMDepot](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/04/using-and-contributing-vms-to-vm-depot.pdf).

## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>Příprava aktualizace SUSE Linux Enterprise Server 11 SP4
1. V prostředním podokně Správce technologie Hyper-V v vyberte virtuální počítač.
2. Kliknutím na **Připojit** otevřete okno virtuálního počítače.
3. Zaregistrujte svůj systém SUSE Linux Enterprise, aby mohl stahovat aktualizace a instalovat balíčky.
4. Aktualizujte systém nejnovějšími záplatami:
   
        # sudo zypper update
5. Nainstalujte agenta Azure Linux u úložiště SLES:
   
        # sudo zypper install python-azure-agent
6. Zkontrolujte, zda je waagent v chkconfig nastaven na "zapnuto", a pokud ne, povolte jej pro automatické spuštění:
   
        # sudo chkconfig waagent on
7. Zkontrolujte, zda je spuštěna služba waagent, a pokud ne, spusťte ji: 
   
        # sudo service waagent start
8. Upravte zaváděcí řádek jádra v konfiguraci grub tak, aby zahrnovala další parametry jádra pro Azure. Chcete-li to provést, otevřete "/boot/grub/menu.lst" v textovém editoru a ujistěte se, že výchozí jádro obsahuje následující parametry:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
    Tím zajistíte, že všechny zprávy konzoly jsou odesílány na první sériový port, který může pomoci podporu Azure s problémy ladění.
9. Potvrďte, že /boot/grub/menu.lst a /etc/fstab oba odkazují na disk pomocí jeho UUID (by-uuid) namísto ID disku (by-id). 
   
    Získat disk UUID
   
        # ls /dev/disk/by-uuid/
   
    Pokud je použit /dev/disk/by-id/, aktualizujte /boot/grub/menu.lst a /etc/fstab správnou hodnotou by-uuid
   
    Před změnou
   
        root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1
   
    Po změně
   
        root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
10. Upravte pravidla udev, abyste se vyhnuli generování statických pravidel pro ethernetové rozhraní. Tato pravidla mohou způsobit problémy při klonování virtuálního počítače v Microsoft Azure nebo Hyper-V:
    
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
11. Doporučujeme upravit soubor "/etc/sysconfig/network/dhcp" a `DHCLIENT_SET_HOSTNAME` změnit parametr na následující:
    
     DHCLIENT_SET_HOSTNAME="ne"
12. V "/etc/sudoers" zakomentujte nebo odeberte následující řádky, pokud existují:
    
    ```
     Defaults targetpw   # ask for the password of the target user i.e. root
     ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
     ```
13. Ujistěte se, že je server SSH nainstalován a nakonfigurován tak, aby se spouštěl při spuštění.  Toto je obvykle výchozí.
14. Nevytvářejte odkládací prostor na disku operačního systému.
    
    Azure Linux Agent můžete automaticky nakonfigurovat odkládací prostor pomocí disku místního prostředku, který je připojen k virtuálnímu počítači po zřizování v Azure. Všimněte si, že disk místního prostředku je *dočasný* disk a může být vyprázdněn při zrušení zřízení virtuálního počítače. Po instalaci Agenta Azure Linux (viz předchozí krok), upravte následující parametry v /etc/waagent.conf odpovídajícím způsobem:
    
     ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 ## POZNÁMKA: Nastavte tuto hodnotu na cokoli, co potřebujete.
15. Spusťte následující příkazy, abyste deprovision zanatii virtuálního počítače a připravit ho na zřizování v Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
16. Ve Správci technologie Hyper-V klikněte na **Akce -> Vypnout.** Virtuální disk Linux ukterýzdvižený disk linuxu je teď připravený k nahrání do Azure.

---
## <a name="prepare-opensuse-131"></a>Připravte openSUSE 13.1+
1. V prostředním podokně Správce technologie Hyper-V v vyberte virtuální počítač.
2. Kliknutím na **Připojit** otevřete okno virtuálního počítače.
3. Na skořepině spusťte příkaz '`zypper lr`'. Pokud tento příkaz vrátí výstup podobný následujícímu, pak jsou úložiště nakonfigurována podle očekávání – nejsou nutné žádné úpravy (všimněte si, že čísla verzí se mohou lišit):
   
        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes
   
    Pokud příkaz vrátí "Nejsou definována žádná úložiště..." pak pomocí následujících příkazů přidejte tato repos:
   
        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f https://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
   
    Potom můžete ověřit, že úložiště byla přidána`zypper lr`spuštěním příkazu ' ' znovu. V případě, že jeden z příslušných úložišť aktualizací není povolen, povolte jej následujícím příkazem:
   
        # sudo zypper mr -e [NUMBER OF REPOSITORY]
4. Aktualizujte jádro na nejnovější dostupnou verzi:
   
        # sudo zypper up kernel-default
   
    Nebo aktualizovat systém se všemi nejnovějšími záplatami:
   
        # sudo zypper update
5. Nainstalujte agenta Azure Linuxu.
   
        # sudo zypper install WALinuxAgent
6. Upravte zaváděcí řádek jádra v konfiguraci grub tak, aby zahrnovala další parametry jádra pro Azure. Chcete-li to provést, otevřete "/boot/grub/menu.lst" v textovém editoru a ujistěte se, že výchozí jádro obsahuje následující parametry:
   
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
   Tím zajistíte, že všechny zprávy konzoly jsou odesílány na první sériový port, který může pomoci podporu Azure s problémy ladění. Kromě toho odeberte ze zaváděcího řádku jádra následující parametry, pokud existují:
   
     libata.atapi_enabled=0 rezerva=0x1f0,0x8
7. Doporučujeme upravit soubor "/etc/sysconfig/network/dhcp" a `DHCLIENT_SET_HOSTNAME` změnit parametr na následující:
   
     DHCLIENT_SET_HOSTNAME="ne"
8. **Důležité:** V "/etc/sudoers" zakomentujte nebo odeberte následující řádky, pokud existují:
     
     ```
     Defaults targetpw   # ask for the password of the target user i.e. root
     ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
     ```

9. Ujistěte se, že je server SSH nainstalován a nakonfigurován tak, aby se spouštěl při spuštění.  Toto je obvykle výchozí.
10. Nevytvářejte odkládací prostor na disku operačního systému.
    
    Azure Linux Agent můžete automaticky nakonfigurovat odkládací prostor pomocí disku místního prostředku, který je připojen k virtuálnímu počítači po zřizování v Azure. Všimněte si, že disk místního prostředku je *dočasný* disk a může být vyprázdněn při zrušení zřízení virtuálního počítače. Po instalaci Agenta Azure Linux (viz předchozí krok), upravte následující parametry v /etc/waagent.conf odpovídajícím způsobem:
    
     ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 ## POZNÁMKA: Nastavte tuto hodnotu na cokoli, co potřebujete.
11. Spusťte následující příkazy, abyste deprovision zanatii virtuálního počítače a připravit ho na zřizování v Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
12. Ujistěte se, že Azure Linux Agent běží při spuštění:
    
        # sudo systemctl enable waagent.service
13. Ve Správci technologie Hyper-V klikněte na **Akce -> Vypnout.** Virtuální disk Linux ukterýzdvižený disk linuxu je teď připravený k nahrání do Azure.

## <a name="next-steps"></a>Další kroky
Teď jste připraveni použít virtuální pevný disk SUSE Linux k vytvoření nových virtuálních počítačů v Azure. Pokud je to poprvé, co nahráváte soubor .vhd do Azure, přečtěte si téma [Vytvoření virtuálního počítače s Linuxem z vlastního disku](upload-vhd.md#option-1-upload-a-vhd).
