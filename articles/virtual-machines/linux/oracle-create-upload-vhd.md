---
title: Vytvoření a nahrání virtuálního pevného disku Oracle Linuxem | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit a nahrát Azure virtuálního pevného disku (VHD), který obsahuje operační systém Oracle Linux.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-service-management,azure-resource-manager
ms.assetid: dd96f771-26eb-4391-9a89-8c8b6d691822
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: c2c02283518bab0723b7bc815f034c4324c944e1
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51232877"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Příprava virtuálního počítače s Oracle Linuxem pro Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že jste již nainstalovali operačního systému Oracle Linux na virtuální pevný disk. Existují více nástroje k vytvoření souborů .vhd, třeba řešení virtualizace jako je Hyper-V. Pokyny najdete v tématu [instalace Role Hyper-V a konfigurace virtuálního počítače](https://technet.microsoft.com/library/hh846766.aspx).

### <a name="oracle-linux-installation-notes"></a>Poznámky k instalaci Oracle Linux
* Podrobnosti najdete také [obecné poznámky k instalaci Linux](create-upload-generic.md#general-linux-installation-notes) další tipy pro na Příprava Linuxu na Azure.
* Oracle, Red Hat kompatibilní jádra a jejich UEK3 (jádro nedělitelné Enterprise) se podporují v prostředí Hyper-V a Azure. Nejlepších výsledků dosáhnete nezapomeňte aktualizovat na nejnovější jádra při přípravě virtuálního pevného disku vašeho Oracle Linux.
* Oracle UEK2 se nepodporuje v prostředí Hyper-V a Azure, protože neobsahuje požadované ovladače.
* Formát VHDX nepodporuje v Azure, pouze **oprava virtuálního pevného disku**.  Převést disk na formát virtuálního pevného disku pomocí Správce technologie Hyper-V nebo rutiny convert-vhd.
* Při instalaci systému Linux se doporučuje použít standardní oddíly spíše než LVM (často výchozí nastavení pro mnoho zařízení). Tím se vyhnete LVM název je v konfliktu s klonovaný virtuální počítače, zejména v případě, že disk s operačním systémem je někdy potřeba připojit k jinému virtuálnímu počítači pro řešení potíží. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) může být použita na datové disky, pokud tomu dávají přednost.
* Pro větší velikosti virtuálních počítačů kvůli chybě v systému Linux verze jádra níže 2.6.37 nepodporuje technologii NUMA. Tento problém ovlivňuje hlavně distribuce využívající nadřazený Red Hat 2.6.32 jádra. Ruční instalace agenta pro Linux v Azure (waagent) automaticky zakáže NUMA v konfiguraci GRUB pro jádro Linuxu. Další informace najdete v níže uvedeném postupu.
* Neprovádějte konfiguraci odkládací oddíl na disk s operačním systémem. Chcete-li vytvořit odkládací soubor na disku dočasný prostředek, který lze nastavit agenta pro Linux.  Další informace najdete v níže uvedeném postupu.
* Všechny virtuální pevné disky v Azure musí mít virtuální velikost, zarovnání na 1MB. Při převodu z nezpracované disku do virtuálního pevného disku je nutné zajistit, že velikost nezpracovaných disku je násobkem 1MB před převodem. Zobrazit [poznámky k instalaci Linux](create-upload-generic.md#general-linux-installation-notes) Další informace.
* Ujistěte se, že `Addons` úložiště je povolená. Upravte soubor `/etc/yum.repo.d/public-yum-ol6.repo`(Oracle Linux 6) nebo `/etc/yum.repo.d/public-yum-ol7.repo`(Oracle Linux) a změňte řádek `enabled=0` k `enabled=1` pod **[ol6_addons]** nebo **[ol7_addons]** v tomto souboru.

## <a name="oracle-linux-64"></a>Oracle Linux 6.4 +
Je třeba provést zvláštní konfigurační kroky v operačním systému pro virtuální počítač pro spuštění v Azure.

1. V prostředním podokně Správce technologie Hyper-V vyberte virtuální počítač.
2. Klikněte na tlačítko **připojit** otevřete okno pro virtuální počítač.
3. Odinstalace NetworkManager spuštěním následujícího příkazu:
   
        # sudo rpm -e --nodeps NetworkManager
   
    **Poznámka:** Pokud balíček není nainstalovaná, tento příkaz se nezdaří s chybovou zprávou. To se očekává.
4. Vytvořte soubor s názvem **sítě** v `/etc/sysconfig/` adresáře, který obsahuje následující text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
5. Vytvořte soubor s názvem **ifcfg-eth0** v `/etc/sysconfig/network-scripts/` adresáře, který obsahuje následující text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
6. Upravte proces udev pravidla pro zabránění generování statická pravidla pro rozhraní sítě Ethernet. Tato pravidla mohou způsobit problémy při klonování virtuálního počítače v Microsoft Azure nebo technologie Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
7. Zajistěte, aby že síťové služby se spustí při spuštění spuštěním následujícího příkazu:
   
        # chkconfig network on
8. Nainstalujte python pyasn1 spuštěním následujícího příkazu:
   
        # sudo yum install python-pyasn1
9. Upravte řádek pro spuštění jádra v konfiguraci grub tak, aby zahrnout další jádra parametry pro Azure. Provedete tuto otevřete "/ boot/grub/menu.lst" v textovém editoru a ujistěte se, že výchozí jádra zahrnuje následující parametry:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off
   
   To také zajistí, všechny zprávy konzoly se odesílají do první sériového portu, který vám může pomoct Azure odborné pomoci s laděním problémů. Tato akce zakáže NUMA kvůli chybě v Oracle, Red Hat kompatibilní jádra.
   
   Kromě výše uvedeného, doporučuje se *odebrat* následující parametry:
   
        rhgb quiet crashkernel=auto
   
   Grafické a quiet spouštěcí nejsou užitečné v cloudovém prostředí, ve kterém chceme, všech protokolů k odeslání do sériového portu.
   
   `crashkernel` Možnost může být levé straně nakonfigurované v případě potřeby, ale Všimněte si, že tento parametr se sníží množství dostupné paměti ve virtuálním počítači 128 MB nebo víc, což může být problematické u menší velikosti virtuálních počítačů.
10. Zajistěte, aby SSH server je nainstalován a nakonfigurován na spuštění při spuštění.  Obvykle se jedná o výchozí nastavení.
11. Spuštěním následujícího příkazu nainstalujte agenta Azure Linux. Nejnovější verze je 2.0.15.
    
        # sudo yum install WALinuxAgent
    
    Mějte na paměti, že instalace balíčku WALinuxAgent odebere NetworkManager a balíčky NetworkManager gnome Pokud nebyly již odebrána jak je popsáno v kroku 2.
12. Nevytvářejte odkládacího prostoru na disku s operačním systémem.
    
    Azure Linux Agent mohou automaticky konfigurovat odkládacího prostoru pomocí disku místního prostředku, který je připojen k virtuálnímu počítači po zřízení v Azure. Všimněte si, že je místní prostředek disku *dočasné* disk a může být vyprázdněna při zřízení virtuálního počítače. Po instalaci agenta Azure Linux (viz předchozí krok), upravte následující parametry v /etc/waagent.conf odpovídajícím způsobem:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
13. Spusťte následující příkaz pro zrušení zřízení virtuálního počítače a připravte je ke zřizování v Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
14. Klikněte na tlačítko **akce -> vypnout dolů** ve Správci technologie Hyper-V. Vašeho linuxového virtuálního pevného disku je teď připravený k nahrání do Azure.

- - -
## <a name="oracle-linux-70"></a>Oracle Linux 7.0 +
**Změny v Oracle Linux 7**

Příprava virtuálního počítače s Oracle Linux 7 na Azure je velmi podobný Oracle Linux 6, ale existují poznamenat několik důležitých rozdílů:

* Kompatibilní jádra Red Hat a Oracle UEK3 se podporují v Azure.  Doporučuje se UEK3 jádra.
* Balíček NetworkManager již nadále nekoliduje s agentem Azure Linux. Tento balíček je ve výchozím nastavení nainstalován, a doporučujeme vám, že se neodebere.
* GRUB2 se teď používá jako výchozí spouštěcí zavaděč, takže postup úpravy parametrů jádra došlo ke změně (viz níže).
* XFS je teď výchozí systém souborů. Systém souborů ext4 je stále možné v případě potřeby.

**Postup konfigurace**

1. Ve Správci technologie Hyper-V vyberte virtuální počítač.
2. Klikněte na tlačítko **připojit** otevřete okno konzoly pro virtuální počítač.
3. Vytvořte soubor s názvem **sítě** v `/etc/sysconfig/` adresáře, který obsahuje následující text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
4. Vytvořte soubor s názvem **ifcfg-eth0** v `/etc/sysconfig/network-scripts/` adresáře, který obsahuje následující text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
5. Upravte proces udev pravidla pro zabránění generování statická pravidla pro rozhraní sítě Ethernet. Tato pravidla mohou způsobit problémy při klonování virtuálního počítače v Microsoft Azure nebo technologie Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
6. Zajistěte, aby že síťové služby se spustí při spuštění spuštěním následujícího příkazu:
   
        # sudo chkconfig network on
7. Nainstalujte balíček python pyasn1 spuštěním následujícího příkazu:
   
        # sudo yum install python-pyasn1
8. Spuštěním následujícího příkazu Vymazat aktuální yumu metadat a nainstalujte všechny aktualizace:
   
        # sudo yum clean all
        # sudo yum -y update
9. Upravte řádek pro spuštění jádra v konfiguraci grub tak, aby zahrnout další jádra parametry pro Azure. Provedete to tak otevřít "/ etc/výchozí/grub" v textovém editoru a úpravy `GRUB_CMDLINE_LINUX` parametru, například:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   To také zajistí, všechny zprávy konzoly se odesílají do první sériového portu, který vám může pomoct Azure odborné pomoci s laděním problémů. Také vypne nové OEL 7 zásady vytváření názvů pro síťové adaptéry. Kromě výše uvedeného, doporučuje se *odebrat* následující parametry:
   
       rhgb quiet crashkernel=auto
   
   Grafické a quiet spouštěcí nejsou užitečné v cloudovém prostředí, ve kterém chceme, všech protokolů k odeslání do sériového portu.
   
   `crashkernel` Možnost může být levé straně nakonfigurované v případě potřeby, ale Všimněte si, že tento parametr se sníží množství dostupné paměti ve virtuálním počítači 128 MB nebo víc, což může být problematické u menší velikosti virtuálních počítačů.
10. Po dokončení úprav "/ etc/výchozí/grub" za výše, spusťte následující příkaz k opětovnému sestavení konfigurace grub:
    
        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
11. Zajistěte, aby SSH server je nainstalován a nakonfigurován na spuštění při spuštění.  Obvykle se jedná o výchozí nastavení.
12. Instalace agenta Azure Linux spuštěním následujícího příkazu:
    
        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent
13. Nevytvářejte odkládacího prostoru na disku s operačním systémem.
    
    Azure Linux Agent mohou automaticky konfigurovat odkládacího prostoru pomocí disku místního prostředku, který je připojen k virtuálnímu počítači po zřízení v Azure. Všimněte si, že je místní prostředek disku *dočasné* disk a může být vyprázdněna při zřízení virtuálního počítače. Po instalaci agenta Azure Linux (viz předchozí krok), upravte následující parametry v /etc/waagent.conf odpovídajícím způsobem:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
14. Spusťte následující příkaz pro zrušení zřízení virtuálního počítače a připravte je ke zřizování v Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
15. Klikněte na tlačítko **akce -> vypnout dolů** ve Správci technologie Hyper-V. Vašeho linuxového virtuálního pevného disku je teď připravený k nahrání do Azure.

## <a name="next-steps"></a>Další postup
Nyní jste připraveni používat váš VHD Oracle Linux můžete vytvořit nové virtuální počítače v Azure. Pokud je to poprvé, že jste nahrání souboru VHD do Azure, najdete v článku [vytvoření virtuálního počítače s Linuxem z vlastního disku](upload-vhd.md#option-1-upload-a-vhd).

