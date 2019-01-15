---
title: Vytvoření a nahrání VHD s operačním systémem SUSE Linuxem v Azure
description: Zjistěte, jak vytvořit a nahrát Azure virtuálního pevného disku (VHD), který obsahuje operační systém SUSE Linux.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 066d01a6-2a54-4718-bcd0-90fe7a5303a1
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: 8ecc29e9422c1d427dd76059f1a427f3d49da38f
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54262367"
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Příprava virtuálního počítače se SLES nebo openSUSE pro Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že jste již nainstalovali SUSE nebo openSUSE Linux operačního systému na virtuální pevný disk. Existují více nástroje k vytvoření souborů .vhd, třeba řešení virtualizace jako je Hyper-V. Pokyny najdete v tématu [instalace Role Hyper-V a konfigurace virtuálního počítače](https://technet.microsoft.com/library/hh846766.aspx).

### <a name="sles--opensuse-installation-notes"></a>SLES nebo openSUSE poznámky k instalaci
* Podrobnosti najdete také [obecné poznámky k instalaci Linux](create-upload-generic.md#general-linux-installation-notes) další tipy pro na Příprava Linuxu na Azure.
* Formát VHDX nepodporuje v Azure, pouze **oprava virtuálního pevného disku**.  Převést disk na formát virtuálního pevného disku pomocí Správce technologie Hyper-V nebo rutiny convert-vhd.
* Při instalaci systému Linux se doporučuje použít standardní oddíly spíše než LVM (často výchozí nastavení pro mnoho zařízení). Tím se vyhnete LVM název je v konfliktu s klonovaný virtuální počítače, zejména v případě, že disk s operačním systémem je někdy potřeba připojit k jinému virtuálnímu počítači pro řešení potíží. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) může být použita na datové disky, pokud tomu dávají přednost.
* Neprovádějte konfiguraci odkládací oddíl na disk s operačním systémem. Chcete-li vytvořit odkládací soubor na disku dočasný prostředek, který lze nastavit agenta pro Linux.  Další informace najdete v níže uvedeném postupu.
* Všechny virtuální pevné disky v Azure musí mít virtuální velikost, zarovnání na 1MB. Při převodu z nezpracované disku do virtuálního pevného disku je nutné zajistit, že velikost nezpracovaných disku je násobkem 1MB před převodem. Zobrazit [poznámky k instalaci Linux](create-upload-generic.md#general-linux-installation-notes) Další informace.

## <a name="use-suse-studio"></a>Použít SUSE Studio
[SUSE Studio](http://www.susestudio.com) můžete snadno vytvořit a spravovat Image SLES a openSUSE pro Azure a Hyper-V. Toto je doporučený postup při přizpůsobení svých vlastních imagí SLES a openSUSE.

Jako alternativu k vytváření vlastní virtuální pevné disky, SUSE, publikuje také BYOS (přineste si vlastní předplatné) imagí pro SLES na [VMDepot](https://www.microsoft.com/en-us/research/wp-content/uploads/2016/04/using-and-contributing-vms-to-vm-depot.pdf).

## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>Příprava operačního systému SUSE Linux Enterprise Server 11 SP4
1. V prostředním podokně Správce technologie Hyper-V vyberte virtuální počítač.
2. Klikněte na tlačítko **připojit** otevřete okno pro virtuální počítač.
3. Zaregistrujte systému SUSE Linux Enterprise a umožnit její aktualizace stáhnout a nainstalovat balíčky.
4. Aktualizace systému s nejnovějšími opravami:
   
        # sudo zypper update
5. Nainstalujte agenta Azure Linux z SLES úložiště:
   
        # sudo zypper install python-azure-agent
6. Vrácení se změnami chkconfig Pokud waagent nastavená na "on" a pokud ne, ji povolit pro automatické spuštění:
   
        # sudo chkconfig waagent on
7. Zkontrolujte, zda je waagent služba spuštěná a pokud ne, spusťte ho: 
   
        # sudo service waagent start
8. Upravte řádek pro spuštění jádra v konfiguraci grub tak, aby zahrnout další jádra parametry pro Azure. Provedete tuto otevřete "/ boot/grub/menu.lst" v textovém editoru a ujistěte se, že výchozí jádra zahrnuje následující parametry:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
    Tím se zajistí všechny zprávy konzoly se odesílají do první sériového portu, který vám může pomoct Azure odborné pomoci s laděním problémů.
9. Potvrďte, že /boot/grub/menu.lst a /etc/fstab obě odkazují na disku pomocí jeho identifikátoru UUID (podle uuid) místo ID disku (podle id). 
   
    Získání disku UUID
   
        # ls /dev/disk/by-uuid/
   
    Pokud /dev/disk/by-id / je použít, aktualizujte /boot/grub/menu.lst a/etc/fstab hodnotou správné podle uuid
   
    Před změnou
   
        root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1
   
    Po změně
   
        root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
10. Upravte proces udev pravidla pro zabránění generování statická pravidla pro rozhraní sítě Ethernet. Tato pravidla mohou způsobit problémy při klonování virtuálního počítače v Microsoft Azure nebo technologie Hyper-V:
    
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
11. Je doporučeno na úpravy souboru "/ etc/sysconfig/network/dhcp" a změňte `DHCLIENT_SET_HOSTNAME` parametr takto:
    
     DHCLIENT_SET_HOSTNAME="no"
12. V "/ etc/sudoers" okomentovat nebo odstranit následující řádky, pokud existují:
    
     Výchozí hodnota targetpw # požádat o heslo cílový uživatel tedy kořenové všechny ALL=(ALL) všechny # upozornění! Jen to používají společně s 'Targetpw výchozí hodnoty'!
13. Zajistěte, aby SSH server je nainstalován a nakonfigurován na spuštění při spuštění.  Obvykle se jedná o výchozí nastavení.
14. Nevytvářejte odkládacího prostoru na disku s operačním systémem.
    
    Azure Linux Agent mohou automaticky konfigurovat odkládacího prostoru pomocí disku místního prostředku, který je připojen k virtuálnímu počítači po zřízení v Azure. Všimněte si, že je místní prostředek disku *dočasné* disk a může být vyprázdněna při zřízení virtuálního počítače. Po instalaci agenta Azure Linux (viz předchozí krok), upravte následující parametry v /etc/waagent.conf odpovídajícím způsobem:
    
     ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resource ResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 ## Poznámka: nastavte na cokoli, co potřebujete k jejímu.
15. Spusťte následující příkaz pro zrušení zřízení virtuálního počítače a připravte je ke zřizování v Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
16. Klikněte na tlačítko **akce -> vypnout dolů** ve Správci technologie Hyper-V. Vašeho linuxového virtuálního pevného disku je teď připravený k nahrání do Azure.

- - -
## <a name="prepare-opensuse-131"></a>Příprava openSUSE 13.1 +
1. V prostředním podokně Správce technologie Hyper-V vyberte virtuální počítač.
2. Klikněte na tlačítko **připojit** otevřete okno pro virtuální počítač.
3. V prostředí, spusťte příkaz "`zypper lr`". Pokud tento příkaz vrátí výstup podobný následujícímu a potom úložiště jsou nakonfigurované podle očekávání – bez úprav jsou nezbytné (Všimněte si, že čísel verzí může lišit):
   
        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes
   
    Pokud příkaz vrátí "Žádná úložiště definované..." pomocí následujících příkazů můžete přidat tato úložiště:
   
        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f http://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
   
    Můžete si ověřit, databází nepřidaly spuštěním příkazu "`zypper lr`" znovu. V případě jednoho z úložišť pro relevantní aktualizace není povolený, povolte ho pomocí následujícího příkazu:
   
        # sudo zypper mr -e [NUMBER OF REPOSITORY]
4. Aktualizace jádra na nejnovější dostupnou verzi:
   
        # sudo zypper up kernel-default
   
    Nebo aktualizaci systému s nejnovějšími opravami:
   
        # sudo zypper update
5. Instalace agenta Azure Linux.
   
        # sudo zypper install WALinuxAgent
6. Upravte řádek pro spuštění jádra v konfiguraci grub tak, aby zahrnout další jádra parametry pro Azure. Chcete-li to provést, otevřete "/ boot/grub/menu.lst" v textovém editoru a ujistěte se, že výchozí jádra zahrnuje následující parametry:
   
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
   Tím se zajistí všechny zprávy konzoly se odesílají do první sériového portu, který vám může pomoct Azure odborné pomoci s laděním problémů. Kromě toho odeberte tyto parametry z řádek pro spuštění jádra, pokud existují:
   
     libata.atapi_enabled=0 reserve=0x1f0,0x8
7. Je doporučeno na úpravy souboru "/ etc/sysconfig/network/dhcp" a změňte `DHCLIENT_SET_HOSTNAME` parametr takto:
   
     DHCLIENT_SET_HOSTNAME="no"
8. **Důležité:** V "/ etc/sudoers" okomentovat nebo odstranit následující řádky, pokud existují:
   
     Výchozí hodnota targetpw # požádat o heslo cílový uživatel tedy kořenové všechny ALL=(ALL) všechny # upozornění! Jen to používají společně s 'Targetpw výchozí hodnoty'!
9. Zajistěte, aby SSH server je nainstalován a nakonfigurován na spuštění při spuštění.  Obvykle se jedná o výchozí nastavení.
10. Nevytvářejte odkládacího prostoru na disku s operačním systémem.
    
    Azure Linux Agent mohou automaticky konfigurovat odkládacího prostoru pomocí disku místního prostředku, který je připojen k virtuálnímu počítači po zřízení v Azure. Všimněte si, že je místní prostředek disku *dočasné* disk a může být vyprázdněna při zřízení virtuálního počítače. Po instalaci agenta Azure Linux (viz předchozí krok), upravte následující parametry v /etc/waagent.conf odpovídajícím způsobem:
    
     ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resource ResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 ## Poznámka: nastavte na cokoli, co potřebujete k jejímu.
11. Spusťte následující příkaz pro zrušení zřízení virtuálního počítače a připravte je ke zřizování v Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
12. Ujistěte se, že Azure Linux Agent se spustí při spuštění:
    
        # sudo systemctl enable waagent.service
13. Klikněte na tlačítko **akce -> vypnout dolů** ve Správci technologie Hyper-V. Vašeho linuxového virtuálního pevného disku je teď připravený k nahrání do Azure.

## <a name="next-steps"></a>Další postup
Teď jste připraveni používat virtuální pevný disk operačního systému SUSE Linux k vytvoření nových virtuálních počítačů v Azure. Pokud je to poprvé, že jste nahrání souboru VHD do Azure, najdete v článku [vytvoření virtuálního počítače s Linuxem z vlastního disku](upload-vhd.md#option-1-upload-a-vhd).
