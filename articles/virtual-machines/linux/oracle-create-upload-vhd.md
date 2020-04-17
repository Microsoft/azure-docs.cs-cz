---
title: Vytvoření a nahrání virtuálního pevného disku Oracle Linux
description: Naučte se vytvářet a nahrávat virtuální pevný disk Azure (VHD), který obsahuje operační systém Oracle Linux.
services: virtual-machines-linux
documentationcenter: ''
author: gbowerman
manager: gwallace
tags: azure-service-management,azure-resource-manager
ms.assetid: dd96f771-26eb-4391-9a89-8c8b6d691822
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/10/2019
ms.author: guybo
ms.openlocfilehash: 6d1e0eacb3d0a2327b8b26ca480b80f957838543
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459759"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Příprava virtuálního počítače Oracle Linux pro Azure

Tento článek předpokládá, že jste již nainstalovali operační systém Oracle Linux na virtuální pevný disk. Existuje více nástrojů pro vytváření souborů .vhd, například virtualizační řešení, jako je Hyper-V. Pokyny naleznete [v tématu Instalace role Technologie Hyper-V a Konfigurace virtuálního počítače](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="oracle-linux-installation-notes"></a>Poznámky k instalaci oracle linuxu
* Další tipy na přípravu Linuxu pro Azure najdete také v [obecných poznámkách k instalaci Linuxu.](create-upload-generic.md#general-linux-installation-notes)
* Technologie Hyper-V a Azure podporují oracle linux s nerozbitným podnikovým jádrem (UEK) nebo jádrem kompatibilním s Red Hat.
* Oracle UEK2 není podporována na Hyper-V a Azure, protože neobsahuje požadované ovladače.
* Formát VHDX není v Azure podporován, pouze **pevný virtuální pevný disk .**  Disk můžete převést do formátu Virtuálního pevného disku pomocí Správce technologie Hyper-V nebo rutiny convert-vhd.
* Při instalaci systému Linux se doporučuje používat standardní oddíly spíše než LVM (často výchozí pro mnoho instalací). Tím se zabrání lVM název konflikty s klonované virtuální počítače, zejména v případě, že disk operačního systému někdy musí být připojen k jinému virtuálnímu počítače pro řešení potíží. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) mohou být použity na datových discích, pokud je to výhodné.
* Verze linuxového jádra starší než 2.6.37 nepodporují NUMA na Hyper-V s většími velikostmi virtuálních her. Tento problém se týká především starších distribucí pomocí jádra Red Hat 2.6.32 a byl opraven v oracle linuxu 6.6 a novějším
* Nekonfigurujte odkládací oddíl na disku operačního systému. Agenta Linuxu lze nakonfigurovat tak, aby vytvořil odkládací soubor na disku s dočasným prostředkem.  Další informace o tom naleznete v následujících krocích.
* Všechny virtuální počítače v Azure musí mít virtuální velikost zarovnanou na 1 MB. Při převodu ze surového disku na virtuální pevný disk je nutné zajistit, aby velikost nezpracovaného disku byla před převodem násobkem 1 MB. Další informace naleznete v [poznámkách k instalaci Linuxu.](create-upload-generic.md#general-linux-installation-notes)
* Ujistěte se, že `Addons` je úložiště povoleno. Upravte `/etc/yum.repos.d/public-yum-ol6.repo`soubor (Oracle Linux `/etc/yum.repos.d/public-yum-ol7.repo`6) nebo (Oracle `enabled=0` Linux `enabled=1` 7) a změňte řádek pod **[ol6_addons]** nebo **[ol7_addons]** v tomto souboru.

## <a name="oracle-linux-64-and-later"></a>Oracle Linux 6.4 a novější
Chcete-li virtuální počítač spustit v Azure, musíte v operačním systému provést konkrétní kroky konfigurace.

1. V prostředním podokně Správce technologie Hyper-V v vyberte virtuální počítač.
2. Kliknutím na **Připojit** otevřete okno virtuálního počítače.
3. Odinstalujte program NetworkManager spuštěním následujícího příkazu:
   
        # sudo rpm -e --nodeps NetworkManager
   
    **Poznámka:** Pokud balíček ještě není nainstalován, tento příkaz se nezdaří s chybovou zprávou. To se očekává.
4. Vytvořte soubor **network** s `/etc/sysconfig/` názvem síť v adresáři, který obsahuje následující text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
5. Vytvořte soubor s názvem **ifcfg-eth0** v adresáři, `/etc/sysconfig/network-scripts/` který obsahuje následující text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
6. Upravte pravidla udev, abyste se vyhnuli generování statických pravidel pro ethernetové rozhraní. Tato pravidla mohou způsobit problémy při klonování virtuálního počítače v Microsoft Azure nebo Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
7. Ujistěte se, že síťová služba bude spuštěna při spuštění spuštěním následujícího příkazu:
   
        # chkconfig network on
8. Nainstalujte python-pyasn1 spuštěním následujícího příkazu:
   
        # sudo yum install python-pyasn1
9. Upravte zaváděcí řádek jádra v konfiguraci grub tak, aby zahrnovala další parametry jádra pro Azure. Chcete-li to provést, otevřete "/boot/grub/menu.lst" v textovém editoru a ujistěte se, že jádro obsahuje následující parametry:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
   Tím zajistíte, že všechny zprávy konzoly jsou odesílány na první sériový port, který může pomoci podporu Azure s problémy ladění.
   
   Kromě výše uvedeného se doporučuje *odstranit* následující parametry:
   
        rhgb quiet crashkernel=auto
   
   Grafické a tiché spuštění není užitečné v cloudovém prostředí, kde chceme, aby všechny protokoly byly odeslány do sériového portu.
   
   Možnost `crashkernel` může zůstat nakonfigurován v případě potřeby, ale všimněte si, že tento parametr sníží množství dostupné paměti ve virtuálním počítači o 128 MB nebo více, což může být problematické na menší velikosti virtuálních zařízení.
10. Ujistěte se, že je server SSH nainstalován a nakonfigurován tak, aby se spouštěl při spuštění.  Toto je obvykle výchozí.
11. Nainstalujte agenta Azure Linux spuštěním následujícího příkazu. Nejnovější verze je 2.0.15.
    
        # sudo yum install WALinuxAgent
    
    Všimněte si, že instalace balíčku WALinuxAgent odebere balíčky NetworkManager a NetworkManager-gnome, pokud již nebyly odebrány, jak je popsáno v kroku 2.
12. Nevytvářejte odkládací prostor na disku operačního systému.
    
    Azure Linux Agent můžete automaticky nakonfigurovat odkládací prostor pomocí disku místního prostředku, který je připojen k virtuálnímu počítači po zřizování v Azure. Všimněte si, že disk místního prostředku je *dočasný* disk a může být vyprázdněn při zrušení zřízení virtuálního počítače. Po instalaci Agenta Azure Linux (viz předchozí krok), upravte následující parametry v /etc/waagent.conf odpovídajícím způsobem:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
13. Spusťte následující příkazy, abyste deprovision zanatii virtuálního počítače a připravit ho na zřizování v Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
14. Ve Správci technologie Hyper-V klikněte na **Akce -> Vypnout.** Virtuální disk Linux ukterýzdvižený disk linuxu je teď připravený k nahrání do Azure.

---
## <a name="oracle-linux-70-and-later"></a>Oracle Linux 7.0 a novější
**Změny v systému Oracle Linux 7**

Příprava virtuálního počítače Oracle Linux 7 pro Azure je velmi podobná oracle linux 6, ale existuje několik důležitých rozdílů, které stojí za zmínku:

* Azure podporuje Oracle Linux buď s nerozbitným enterprise jádrem (UEK) nebo jádrem kompatibilním s Red Hat. Doporučujese oracle linux s UEK.
* Balíček NetworkManager již není v konfliktu s agentem Azure Linux. Tento balíček je nainstalován ve výchozím nastavení a doporučujeme, aby nebyl odebrán.
* GRUB2 se nyní používá jako výchozí zavaděč, takže se změnil postup pro úpravu parametrů jádra (viz níže).
* XFS je nyní výchozí souborový systém. Systém souborů ext4 lze v případě potřeby stále používat.

**Postup konfigurace**

1. Ve Správci technologie Hyper-V vyberte virtuální počítač.
2. Kliknutím na **Připojit** otevřete okno konzoly pro virtuální počítač.
3. Vytvořte soubor **network** s `/etc/sysconfig/` názvem síť v adresáři, který obsahuje následující text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
4. Vytvořte soubor s názvem **ifcfg-eth0** v adresáři, `/etc/sysconfig/network-scripts/` který obsahuje následující text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
5. Upravte pravidla udev, abyste se vyhnuli generování statických pravidel pro ethernetové rozhraní. Tato pravidla mohou způsobit problémy při klonování virtuálního počítače v Microsoft Azure nebo Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
6. Ujistěte se, že síťová služba bude spuštěna při spuštění spuštěním následujícího příkazu:
   
        # sudo chkconfig network on
7. Nainstalujte balíček python-pyasn1 spuštěním následujícího příkazu:
   
        # sudo yum install python-pyasn1
8. Spuštěním následujícího příkazu vymažte aktuální metadata yum a nainstalujte všechny aktualizace:
   
        # sudo yum clean all
        # sudo yum -y update
9. Upravte zaváděcí řádek jádra v konfiguraci grub tak, aby zahrnovala další parametry jádra pro Azure. Chcete-li to otevřít "/etc/default/grub" v `GRUB_CMDLINE_LINUX` textovém editoru a upravit parametr, například:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Tím se také zajistí, že všechny zprávy konzoly jsou odesílány na první sériový port, který může pomoci podporu Azure s problémy ladění. Také vypne konvence pojmenování pro síťové karty v oracle linux 7 s nerozbitným enterprise jádrem. Kromě výše uvedeného se doporučuje *odstranit* následující parametry:
   
       rhgb quiet crashkernel=auto
   
   Grafické a tiché spuštění není užitečné v cloudovém prostředí, kde chceme, aby všechny protokoly byly odeslány do sériového portu.
   
   Možnost `crashkernel` může zůstat nakonfigurován v případě potřeby, ale všimněte si, že tento parametr sníží množství dostupné paměti ve virtuálním počítači o 128 MB nebo více, což může být problematické na menší velikosti virtuálních zařízení.
10. Jakmile jste hotovi editace "/ etc / default / grub" na výše, spusťte následující příkaz pro opětovné konfigurace grub:
    
        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
11. Ujistěte se, že je server SSH nainstalován a nakonfigurován tak, aby se spouštěl při spuštění.  Toto je obvykle výchozí.
12. Nainstalujte agenta Azure Linux spuštěním následujícího příkazu:
    
        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent
13. Nevytvářejte odkládací prostor na disku operačního systému.
    
    Azure Linux Agent můžete automaticky nakonfigurovat odkládací prostor pomocí disku místního prostředku, který je připojen k virtuálnímu počítači po zřizování v Azure. Všimněte si, že disk místního prostředku je *dočasný* disk a může být vyprázdněn při zrušení zřízení virtuálního počítače. Po instalaci Agenta Azure Linuxu (viz předchozí krok) upravte v /etc/waagent.conf odpovídajícím způsobem následující parametry:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
14. Spusťte následující příkazy, abyste deprovision zanatii virtuálního počítače a připravit ho na zřizování v Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
15. Ve Správci technologie Hyper-V klikněte na **Akce -> Vypnout.** Virtuální disk Linux ukterýzdvižený disk linuxu je teď připravený k nahrání do Azure.

## <a name="next-steps"></a>Další kroky
Teď jste připraveni použít svůj Oracle Linux .vhd k vytvoření nových virtuálních počítačů v Azure. Pokud je to poprvé, co nahráváte soubor .vhd do Azure, přečtěte si téma [Vytvoření virtuálního počítače s Linuxem z vlastního disku](upload-vhd.md#option-1-upload-a-vhd).

