---
title: Vytvořit a nahrát VHD na základě CentOS Linux v Azure
description: Naučte se vytvořit a odeslat Azure virtuálního pevného disku (VHD) obsahující operační systém na základě CentOS Linux.
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
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: e2af462d6fe0a6a9811e885199d70a182bf145c7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>Příprava virtuálního počítače založeného na CentOS pro Azure
* [Příprava virtuálního počítače, CentOS 6.x pro Azure.](#centos-6x)
* [Příprava virtuálního počítače, CentOS 7.0 + pro Azure.](#centos-70)

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že jste již nainstalovali CentOS (nebo podobné odvozených) operační systém Linux virtuálního pevného disku. Postup vytvoření souborů VHD, například řešení virtualizace jako je například technologie Hyper-V existuje několik nástrojů. Pokyny najdete v tématu [nainstalovat roli Hyper-V a konfigurace virtuálního počítače](http://technet.microsoft.com/library/hh846766.aspx).

**Poznámky k instalaci centOS**

* Najdete také [obecné poznámky k instalaci Linux](create-upload-generic.md#general-linux-installation-notes) Příprava na Azure Linux další tipy pro.
* Formát VHDX není podporován v Azure, pouze **pevný virtuální pevný disk**.  Disk můžete převést do formátu virtuálního pevného disku pomocí Správce technologie Hyper-V nebo rutiny convert-VHD prostředí. Pokud používáte VirtualBox to znamená výběr **pevnou velikost** oproti výchozí přidělí dynamicky při vytváření disku.
* Při instalaci systému Linux je *doporučená* použít standardní oddíly spíše než LVM (často výchozí pro mnoho instalace). Tím se vyhnete LVM název je v konfliktu s klonovaný virtuální počítače, zvlášť pokud někdy musí být připojen k virtuálnímu počítači jiné identické pro řešení potíží s disk s operačním systémem. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) mohou být použity na datové disky.
* Vyžaduje se podpora jádra pro připojení systémy souborů UDF. Při prvním spuštění v Azure je předána zřizování konfigurace do virtuálního počítače s Linuxem pomocí formátu UDF média, který je připojen k Host. Azure Linux agent musí být schopný se připojit a načíst jeho konfiguraci a zřídit virtuální počítač v systému souborů UDF.
* Verze jádra Linux pod 2.6.37 nepodporují NUMA v technologii Hyper-V s větší velikostí virtuálních počítačů. -Li tento problém ovlivňuje především starší distribuce pomocí nadřazený Red Hat 2.6.32 jádra která byla opravena v RHEL 6.6 (jádra 2.6.32 504). Systémy s operačním systémem vlastní jádra starší než 2.6.37 nebo na základě RHEL jádra starší než 2.6.32-504 musíte nastavit parametr spouštěcího `numa=off` na příkazového řádku v grub.conf jádra. Další informace najdete v části Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Nekonfigurujte přepnutí oddílu na disku operačního systému. Chcete-li vytvořit odkládací soubor na disku dočasných prostředků lze nakonfigurovat agenta systému Linux.  Další informace o této naleznete v následujících krocích.
* Všechny virtuální pevné disky na platformě Azure, musí mít virtuální velikost zarovnán 1MB. Při převodu z nezpracovaná disku na virtuální pevný disk je nutné zajistit, aby velikost disku nezpracovaná není násobkem 1MB před převodem. V tématu [poznámky k instalaci Linux](create-upload-generic.md#general-linux-installation-notes) Další informace.

## <a name="centos-6x"></a>CentOS 6.x

1. Ve Správci technologie Hyper-V vyberte virtuální počítač.

2. Klikněte na tlačítko **Connect** k otevření okna konzoly pro virtuální počítač.

3. V CentOS 6 NetworkManager může narušovat Azure Linux agent. Odinstalaci tohoto balíčku tak, že spustíte následující příkaz:
   
        # sudo rpm -e --nodeps NetworkManager

4. Vytvořte nebo upravte soubor `/etc/sysconfig/network` a přidejte následující text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5. Vytvořte nebo upravte soubor `/etc/sysconfig/network-scripts/ifcfg-eth0` a přidejte následující text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

6. Změňte pravidla udev, aby nedošlo ke generování statická pravidla pro alespoň jedno rozhraní sítě Ethernet. Tato pravidla může způsobit problémy při klonování virtuálního počítače v Microsoft Azure nebo Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

7. Zajistěte, aby že síťové služby se spustí při spuštění spuštěním následujícího příkazu:
   
        # sudo chkconfig network on

8. Pokud chcete použít OpenLogic zrcadlení, které jsou hostované v datových centrech Azure, a potom nahraďte `/etc/yum.repos.d/CentOS-Base.repo` soubor s následující úložiště.  Taky se přidá **[openlogic]** úložiště, který zahrnuje další balíčků, jako jsou Azure Linux agent:

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
    Zbývající části této příručky bude předpokládat, že používáte alespoň `[openlogic]` úložišti, který se použije k instalaci níže Azure Linux agent.


9. Přidejte následující řádek na /etc/yum.conf:
    
        http_caching=packages

10. Spusťte následující příkaz, který zrušte aktuální metadata yum a aktualizujte systém s nejnovější balíčky:
    
        # yum clean all

    Pokud chcete vytvořit bitovou kopii pro starší verze CentOS, doporučujeme aktualizovat všechny balíčky na nejnovější:

        # sudo yum -y update

    Po spuštění tohoto příkazu může být nutný restart.

11. (Volitelné) Nainstalujte ovladače pro integraci služby Linux (LIS).
   
    >[!IMPORTANT]
    V kroku **požadované** pro CentOS 6.3 a starší a volitelné pro pozdějších verzích.

        # sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
        # sudo yum install microsoft-hyper-v

    Alternativně můžete podle pokynů ruční instalace na [stránce pro stažení LIS](https://go.microsoft.com/fwlink/?linkid=403033) k instalaci RPM do virtuálního počítače.
 
12. Instalace Azure Linux Agent a závislosti:
    
        # sudo yum install python-pyasn1 WALinuxAgent
    
    Odebere balíček WALinuxAgent NetworkManager a balíčky NetworkManager gnome Pokud nebyly již odebrána jak je popsáno v kroku 3.


13. Upravte řádku spouštěcí jádra ve vaší konfiguraci grub pro zahrnutí dalších jádra parametry Azure. Chcete-li to provést, otevřete `/boot/grub/menu.lst` v textovém editoru a ujistěte se, že výchozí jádra zahrnuje následující parametry:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    To také zajistí, všechny zprávy konzoly odešlou do první sériového portu, který může být užitečné Azure podporu pro ladění problémů.
    
    Kromě výše uvedeného, doporučuje se *odebrat* následující parametry:
    
        rhgb quiet crashkernel=auto
    
    Grafické a quiet spouštěcí nejsou užitečné při cloudovém prostředí, kde chceme, aby všechny protokoly pro odeslání do sériového portu.  `crashkernel` Možnost může být vlevo nakonfigurované v případě potřeby, ale Všimněte si, že tento parametr se sníží množství dostupné paměti ve virtuálním počítači 128 MB nebo informace, které mohou způsobovat menší velikostí virtuálního počítače.

    >[!Important]
    CentOS verze 6.5 a starší, musíte taky nastavit parametr jádra `numa=off`. V tématu Red Hat [KB 436883](https://access.redhat.com/solutions/436883).

14. Ujistěte se, že SSH server je nainstalován a nakonfigurován na spuštění při spuštění.  Obvykle je to výchozí hodnota.

15. Nevytvářejte odkládacího prostoru na disku operačního systému.
    
    Azure Linux Agent mohou automaticky konfigurovat odkládacího souboru pomocí disku místní prostředek, který je připojen k virtuálnímu počítači po zřízení v Azure. Všimněte si, že je disk místní prostředek *dočasné* na disku a může vyprázdněny, když je virtuální počítač zrušit. Po instalaci Azure Linux Agent (viz předchozí krok), upravte následující parametry v `/etc/waagent.conf` odpovídajícím způsobem:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Spusťte následující příkaz pro zrušení zřízení virtuálního počítače a jeho přípravu pro zřizování na Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

17. Klikněte na tlačítko **akce -> vypnutí dolů** ve Správci technologie Hyper-V. Svůj disk VHD Linux je nyní připravena k odeslání do Azure.


- - -
## <a name="centos-70"></a>CentOS 7.0 +
**Změny v CentOS 7 (a podobné odvozené konfigurace)**

Příprava CentOS 7 virtuálního počítače na Azure je velmi podobné CentOS 6, ale existuje několik důležitých rozdílů vhodné poznamenat:

* NetworkManager balíčku už v konfliktu s Azure Linux agent. Tento balíček je ve výchozím nastavení nainstalovaná a doporučujeme vám, že se neodebere.
* GRUB2 se teď používá jako výchozí zaváděcí program pro spouštění, tak postup úpravy parametry jádra se změnila (viz níže).
* XFS je nyní na výchozí systém souborů. Systém souborů ext4 pořád můžou použít v případě potřeby.

**Kroky konfigurace**

1. Ve Správci technologie Hyper-V vyberte virtuální počítač.

2. Klikněte na tlačítko **Connect** k otevření okna konzoly pro virtuální počítač.

3. Vytvořte nebo upravte soubor `/etc/sysconfig/network` a přidejte následující text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4. Vytvořte nebo upravte soubor `/etc/sysconfig/network-scripts/ifcfg-eth0` a přidejte následující text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

5. Změňte pravidla udev, aby nedošlo ke generování statická pravidla pro alespoň jedno rozhraní sítě Ethernet. Tato pravidla může způsobit problémy při klonování virtuálního počítače v Microsoft Azure nebo Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

6. Pokud chcete použít OpenLogic zrcadlení, které jsou hostované v datových centrech Azure, a potom nahraďte `/etc/yum.repos.d/CentOS-Base.repo` soubor s následující úložiště.  Taky se přidá **[openlogic]** úložiště, který obsahuje balíčky pro Azure Linux agent:
   
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
    Zbývající části této příručky bude předpokládat, že používáte alespoň `[openlogic]` úložišti, který se použije k instalaci níže Azure Linux agent.

7. Spusťte následující příkaz, který zrušte aktuální metadata yum a nainstalujte všechny aktualizace:
   
        # sudo yum clean all

    Pokud chcete vytvořit bitovou kopii pro starší verze CentOS, doporučujeme aktualizovat všechny balíčky na nejnovější:

        # sudo yum -y update

    Restartování, může být nutné po spuštění tohoto příkazu.

8. Upravte řádku spouštěcí jádra ve vaší konfiguraci grub pro zahrnutí dalších jádra parametry Azure. Chcete-li to provést, otevřete `/etc/default/grub` v textovém editoru a upravit `GRUB_CMDLINE_LINUX` parametr, například:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   To také zajistí, všechny zprávy konzoly odešlou do první sériového portu, který může být užitečné Azure podporu pro ladění problémů. Je také vypne nové zásady vytváření názvů CentOS 7 pro síťové adaptéry. Kromě výše uvedeného, doporučuje se *odebrat* následující parametry:
   
        rhgb quiet crashkernel=auto
   
    Grafické a quiet spouštěcí nejsou užitečné při cloudovém prostředí, kde chceme, aby všechny protokoly pro odeslání do sériového portu. `crashkernel` Možnost může být vlevo nakonfigurované v případě potřeby, ale Všimněte si, že tento parametr se sníží množství dostupné paměti ve virtuálním počítači 128 MB nebo informace, které mohou způsobovat menší velikostí virtuálního počítače.

9. Po dokončení úprav `/etc/default/grub` za výše, spusťte následující příkaz k opětovnému sestavení grub konfigurace:
   
        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

10. Pokud vytváření bitové kopie z **VMWare, VirtualBox nebo KVM:** ovladače zajistěte, aby technologie Hyper-V jsou součástí initramfs:
   
   Upravit `/etc/dracut.conf`, přidejte obsah:
   
        add_drivers+=”hv_vmbus hv_netvsc hv_storvsc”
   
   Znovu sestavte initramfs:
   
        # sudo dracut –f -v

11. Instalace Azure Linux Agent a závislosti:

        # sudo yum install python-pyasn1 WALinuxAgent
        # sudo systemctl enable waagent

12. Nevytvářejte odkládacího prostoru na disku operačního systému.
   
   Azure Linux Agent mohou automaticky konfigurovat odkládacího souboru pomocí disku místní prostředek, který je připojen k virtuálnímu počítači po zřízení v Azure. Všimněte si, že je disk místní prostředek *dočasné* na disku a může vyprázdněny, když je virtuální počítač zrušit. Po instalaci Azure Linux Agent (viz předchozí krok), upravte následující parametry v `/etc/waagent.conf` odpovídajícím způsobem:
   
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Spusťte následující příkaz pro zrušení zřízení virtuálního počítače a jeho přípravu pro zřizování na Azure:
   
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

14. Klikněte na tlačítko **akce -> vypnutí dolů** ve Správci technologie Hyper-V. Svůj disk VHD Linux je nyní připravena k odeslání do Azure.

## <a name="next-steps"></a>Další postup
Nyní jste připraveni použít systému CentOS Linux virtuální pevný disk k vytvoření nové virtuální počítače v Azure. Pokud je poprvé, že jste nahrávání souboru VHD do Azure, najdete v části [vytvořit virtuální počítač s Linuxem z vlastní disku](upload-vhd.md#option-1-upload-a-vhd).

