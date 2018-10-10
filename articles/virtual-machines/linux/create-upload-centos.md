---
title: Vytvoření a nahrání VHD systémem CentOS Linux v Azure
description: Zjistěte, jak vytvořit a nahrát Azure virtuálního pevného disku (VHD), který obsahuje operační systém systémem CentOS Linux.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 0e518e92-e981-43f4-b12c-9cba1064c4bb
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: szark
ms.openlocfilehash: 5328ee7e3e3035ebce1ff9fccbfc6e9ccfd86ee8
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2018
ms.locfileid: "48888388"
---
# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>Příprava virtuálního počítače založeného na CentOS pro Azure
* [Příprava virtuálního počítače CentOS 6.x pro Azure](#centos-6x)
* [Příprava virtuálního počítače CentOS 7.0 + pro Azure](#centos-70)

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že jste už nainstalovali CentOS (nebo podobné odvozených děl na základě) operačního systému Linux na virtuální pevný disk. Existují více nástroje k vytvoření souborů .vhd, třeba řešení virtualizace jako je Hyper-V. Pokyny najdete v tématu [instalace Role Hyper-V a konfigurace virtuálního počítače](http://technet.microsoft.com/library/hh846766.aspx).

**Poznámky k instalaci centOS**

* Podrobnosti najdete také [obecné poznámky k instalaci Linux](create-upload-generic.md#general-linux-installation-notes) další tipy pro na Příprava Linuxu na Azure.
* Formát VHDX nepodporuje v Azure, pouze **oprava virtuálního pevného disku**.  Převést disk na formát virtuálního pevného disku pomocí Správce technologie Hyper-V nebo rutiny convert-vhd. Pokud používáte VirtualBox to znamená, že vyberete **pevnou velikost** na rozdíl od výchozí dynamicky přidělené při vytváření disku.
* Při instalaci systému Linux je *doporučuje* použít standardní oddíly spíše než LVM (často výchozí nastavení pro mnoho zařízení). Tím se vyhnete LVM název je v konfliktu s klonovaný virtuální počítače, zejména v případě, že disk s operačním systémem je někdy potřeba připojit k jinému virtuálnímu počítači identické pro řešení potíží. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) může být použita na datové disky.
* Je nutné podporovat jádra pro připojení systému souborů UDF. Při prvním spuštění počítače v Azure je konfigurace zřizování předány do virtuálního počítače s Linuxem prostřednictvím formátu UDF média, který je připojen k hosta. Agent Azure Linux musí být schopen připojit a načíst jeho konfiguraci a zřízení virtuálního počítače v systému souborů UDF.
* Verze jádra Linuxu níže 2.6.37 nepodporují NUMA v Hyper-V s větší velikostí virtuálních počítačů. Tento problém ovlivňuje hlavně starší distribuce využívající nadřazený jádra systému Red Hat 2.6.32 a byla stanovena v RHEL 6.6 (jádra 2.6.32 504). Systémy s operačním systémem vlastní jádrech starší než 2.6.37 nebo systémem RHEL jádrech starší než 2.6.32-504 musí nastavit parametr spouštěcí `numa=off` na příkazový řádek v grub.conf jádra. Další informace najdete v článku Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Neprovádějte konfiguraci odkládací oddíl na disk s operačním systémem. Chcete-li vytvořit odkládací soubor na disku dočasný prostředek, který lze nastavit agenta pro Linux.  Další informace najdete v níže uvedeném postupu.
* Všechny virtuální pevné disky v Azure musí mít virtuální velikost, zarovnání na 1MB. Při převodu z nezpracované disku do virtuálního pevného disku je nutné zajistit, že velikost nezpracovaných disku je násobkem 1MB před převodem. Zobrazit [poznámky k instalaci Linux](create-upload-generic.md#general-linux-installation-notes) Další informace.

## <a name="centos-6x"></a>CentOS 6.x

1. Ve Správci technologie Hyper-V vyberte virtuální počítač.

2. Klikněte na tlačítko **připojit** otevřete okno konzoly pro virtuální počítač.

3. CentOS 6 NetworkManager může vést k potížím s agentem Azure Linux. Odinstaluje tento balíček spuštěním následujícího příkazu:
   
        # sudo rpm -e --nodeps NetworkManager

4. Vytvoření nebo úpravě souboru `/etc/sysconfig/network` a přidejte následující text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5. Vytvoření nebo úpravě souboru `/etc/sysconfig/network-scripts/ifcfg-eth0` a přidejte následující text:
   
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
   
        # sudo chkconfig network on

8. Pokud chcete použít OpenLogic zrcadlení, které jsou hostovány v rámci datovými centry Azure a potom nahraďte `/etc/yum.repos.d/CentOS-Base.repo` soubor s následujícím úložišti.  Taky se přidá **[openlogic]** úložiště, které obsahuje další balíčky, jako je například agenta Azure Linux:

        [openlogic]
        name=CentOS-$releasever - openlogic packages for $basearch
        baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
        enabled=1
        gpgcheck=0
        
        [base]
        name=CentOS-$releasever - Base
        #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
        
        #released updates
        [updates]
        name=CentOS-$releasever - Updates
        #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
        
        #additional packages that may be useful
        [extras]
        name=CentOS-$releasever - Extras
        #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

        #additional packages that extend functionality of existing packages
        [centosplus]
        name=CentOS-$releasever - Plus
        #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
        
        #contrib - packages by Centos Users
        [contrib]
        name=CentOS-$releasever - Contrib
        #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=contrib&infra=$infra
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

    >[!Note]
    Zbývající části této příručky bude předpokládat, že používáte alespoň `[openlogic]` úložiště, který se použije k instalaci agenta Azure Linux níže.


9. Přidejte následující řádek /etc/yum.conf:
    
        http_caching=packages

10. Spuštěním následujícího příkazu Vymazat aktuální yumu metadat a aktualizace systému pomocí nejnovější balíčky:
    
        # yum clean all

    Pokud vytvoříte image pro starší verze CentOS, doporučujeme aktualizovat všechny balíčky na nejnovější verzi:

        # sudo yum -y update

    Po spuštění tohoto příkazu může být nutný restart počítače.

11. (Volitelné) Instalace ovladačů pro Linux Integration Services (LIS).
   
    >[!IMPORTANT]
    V kroku **požadované** pro CentOS 6.3 a dříve a volitelné pro novější verze.

        # sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
        # sudo yum install microsoft-hyper-v

    Alternativně můžete postupovat podle pokynů pro ruční instalaci [stránce pro stažení LIS](https://go.microsoft.com/fwlink/?linkid=403033) pro instalaci RPM na váš virtuální počítač.
 
12. Instalace agenta Azure Linux a závislostí:
    
        # sudo yum install python-pyasn1 WALinuxAgent
    
    Balíček WALinuxAgent odebere NetworkManager a balíčky NetworkManager gnome Pokud nebyly již odebrána jak je popsáno v kroku 3.


13. Upravte řádek pro spuštění jádra v konfiguraci grub tak, aby zahrnout další jádra parametry pro Azure. Chcete-li to provést, otevřete `/boot/grub/menu.lst` v textovém editoru a ujistěte se, že výchozí jádra zahrnuje následující parametry:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    To také zajistí, všechny zprávy konzoly se odesílají do první sériového portu, který vám může pomoct Azure odborné pomoci s laděním problémů.
    
    Kromě výše uvedeného, doporučuje se *odebrat* následující parametry:
    
        rhgb quiet crashkernel=auto
    
    Grafické a quiet spouštěcí nejsou užitečné v cloudovém prostředí, ve kterém chceme, všech protokolů k odeslání do sériového portu.  `crashkernel` Možnost může být levé straně nakonfigurované v případě potřeby, ale Všimněte si, že tento parametr se sníží množství dostupné paměti ve virtuálním počítači 128 MB nebo víc, což může být problematické u menší velikosti virtuálních počítačů.

    >[!Important]
    CentOS 6.5 a starší, musíte taky nastavit parametr jádra `numa=off`. Zobrazit Red Hat [KB 436883](https://access.redhat.com/solutions/436883).

14. Zajistěte, aby SSH server je nainstalován a nakonfigurován na spuštění při spuštění.  Obvykle se jedná o výchozí nastavení.

15. Nevytvářejte odkládacího prostoru na disku s operačním systémem.
    
    Azure Linux Agent mohou automaticky konfigurovat odkládacího prostoru pomocí disku místního prostředku, který je připojen k virtuálnímu počítači po zřízení v Azure. Všimněte si, že je místní prostředek disku *dočasné* disk a může být vyprázdněna při zřízení virtuálního počítače. Po instalaci agenta Azure Linux (viz předchozí krok), upravte následující parametry v `/etc/waagent.conf` odpovídajícím způsobem:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Spusťte následující příkaz pro zrušení zřízení virtuálního počítače a připravte je ke zřizování v Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

17. Klikněte na tlačítko **akce -> vypnout dolů** ve Správci technologie Hyper-V. Vašeho linuxového virtuálního pevného disku je teď připravený k nahrání do Azure.


- - -
## <a name="centos-70"></a>CentOS 7.0 +
**Změny v CentOS 7 (a podobné odvozené konfigurace)**

Příprava virtuálního počítače CentOS 7 pro Azure je velmi podobný CentOS 6, ale existují poznamenat několik důležitých rozdílů:

* Balíček NetworkManager již nadále nekoliduje s agentem Azure Linux. Tento balíček je ve výchozím nastavení nainstalován, a doporučujeme vám, že se neodebere.
* GRUB2 se teď používá jako výchozí spouštěcí zavaděč, takže postup úpravy parametrů jádra došlo ke změně (viz níže).
* XFS je teď výchozí systém souborů. Systém souborů ext4 je stále možné v případě potřeby.

**Postup konfigurace**

1. Ve Správci technologie Hyper-V vyberte virtuální počítač.

2. Klikněte na tlačítko **připojit** otevřete okno konzoly pro virtuální počítač.

3. Vytvoření nebo úpravě souboru `/etc/sysconfig/network` a přidejte následující text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4. Vytvoření nebo úpravě souboru `/etc/sysconfig/network-scripts/ifcfg-eth0` a přidejte následující text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

5. Upravte proces udev pravidla pro zabránění generování statická pravidla pro rozhraní sítě Ethernet. Tato pravidla mohou způsobit problémy při klonování virtuálního počítače v Microsoft Azure nebo technologie Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

6. Pokud chcete použít OpenLogic zrcadlení, které jsou hostovány v rámci datovými centry Azure a potom nahraďte `/etc/yum.repos.d/CentOS-Base.repo` soubor s následujícím úložišti.  Taky se přidá **[openlogic]** úložiště, které obsahuje balíčky pro agenta Azure Linux:
   
        [openlogic]
        name=CentOS-$releasever - openlogic packages for $basearch
        baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
        enabled=1
        gpgcheck=0
        
        [base]
        name=CentOS-$releasever - Base
        #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
        
        #released updates
        [updates]
        name=CentOS-$releasever - Updates
        #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
        
        #additional packages that may be useful
        [extras]
        name=CentOS-$releasever - Extras
        #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
        gpgcheck=1
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
        
        #additional packages that extend functionality of existing packages
        [centosplus]
        name=CentOS-$releasever - Plus
        #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
        baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
        gpgcheck=1
        enabled=0
        gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7

    >[!Note]
    Zbývající části této příručky bude předpokládat, že používáte alespoň `[openlogic]` úložiště, který se použije k instalaci agenta Azure Linux níže.

7. Spuštěním následujícího příkazu Vymazat aktuální yumu metadat a nainstalujte všechny aktualizace:
   
        # sudo yum clean all

    Pokud vytvoříte image pro starší verze CentOS, doporučujeme aktualizovat všechny balíčky na nejnovější verzi:

        # sudo yum -y update

    Restartování možná opakovaným načtením vyžadovaným po spuštění tohoto příkazu.

8. Upravte řádek pro spuštění jádra v konfiguraci grub tak, aby zahrnout další jádra parametry pro Azure. Chcete-li to provést, otevřete `/etc/default/grub` v textovém editoru a úpravy `GRUB_CMDLINE_LINUX` parametru, například:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   To také zajistí, všechny zprávy konzoly se odesílají do první sériového portu, který vám může pomoct Azure odborné pomoci s laděním problémů. Také vypne nové CentOS 7 zásady vytváření názvů pro síťové adaptéry. Kromě výše uvedeného, doporučuje se *odebrat* následující parametry:
   
        rhgb quiet crashkernel=auto
   
    Grafické a quiet spouštěcí nejsou užitečné v cloudovém prostředí, ve kterém chceme, všech protokolů k odeslání do sériového portu. `crashkernel` Možnost může být levé straně nakonfigurované v případě potřeby, ale Všimněte si, že tento parametr se sníží množství dostupné paměti ve virtuálním počítači 128 MB nebo víc, což může být problematické u menší velikosti virtuálních počítačů.

9. Po dokončení úprav `/etc/default/grub` za výše, spusťte následující příkaz k opětovnému sestavení konfigurace grub:
   
        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

10. Pokud sestavení image ze **VMware, VirtualBox nebo KVM:** ovladače Ujistěte se, že technologie Hyper-V jsou součástí initramfs:
   
   Upravit `/etc/dracut.conf`, přidat obsah:
   
        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”
   
   Znovu sestavte initramfs:
   
        # sudo dracut -f -v

11. Instalace agenta Azure Linux a závislostí:

        # sudo yum install python-pyasn1 WALinuxAgent
        # sudo systemctl enable waagent

12. Nevytvářejte odkládacího prostoru na disku s operačním systémem.
   
   Azure Linux Agent mohou automaticky konfigurovat odkládacího prostoru pomocí disku místního prostředku, který je připojen k virtuálnímu počítači po zřízení v Azure. Všimněte si, že je místní prostředek disku *dočasné* disk a může být vyprázdněna při zřízení virtuálního počítače. Po instalaci agenta Azure Linux (viz předchozí krok), upravte následující parametry v `/etc/waagent.conf` odpovídajícím způsobem:
   
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

## <a name="next-steps"></a>Další postup
Teď jste připraveni používat CentOS Linux virtuální pevný disk k vytvoření nových virtuálních počítačů v Azure. Pokud je to poprvé, že jste nahrání souboru VHD do Azure, najdete v článku [vytvoření virtuálního počítače s Linuxem z vlastního disku](upload-vhd.md#option-1-upload-a-vhd).

