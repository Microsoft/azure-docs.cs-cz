---
title: "Vytvoření a nahrání virtuálního pevného disku Oracle Linux | Microsoft Docs"
description: "Naučte se vytvořit a odeslat Azure virtuální pevný disk (VHD), který obsahuje operační systém Oracle Linux."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
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
ms.openlocfilehash: 52771c8cf401bb60339182644cd8755637650140
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Příprava virtuálního počítače s Oracle Linuxem pro Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že jste již nainstalovali Oracle Linux operačního systému na virtuální pevný disk. Postup vytvoření souborů VHD, například řešení virtualizace jako je například technologie Hyper-V existuje několik nástrojů. Pokyny najdete v tématu [nainstalovat roli Hyper-V a konfigurace virtuálního počítače](http://technet.microsoft.com/library/hh846766.aspx).

### <a name="oracle-linux-installation-notes"></a>Poznámky k instalaci Oracle Linux
* Najdete také [obecné poznámky k instalaci Linux](create-upload-generic.md#general-linux-installation-notes) Příprava na Azure Linux další tipy pro.
* Oracle Red Hat kompatibilní jádra a jejich UEK3 (nedělitelné Enterprise jádra) jsou podporovány v Hyper-V a Azure. Nejlepších výsledků dosáhnete nezapomeňte aktualizovat na nejnovější jádra při přípravě virtuálního pevného disku vašeho Oracle Linux.
* Oracle na UEK2 se nepodporuje na Hyper-V a Azure, protože neobsahuje požadované ovladače.
* Formát VHDX není podporován v Azure, pouze **pevný virtuální pevný disk**.  Disk můžete převést do formátu virtuálního pevného disku pomocí Správce technologie Hyper-V nebo rutiny convert-VHD prostředí.
* Při instalaci systému Linux se doporučuje použít standardní oddíly spíše než LVM (často výchozí pro mnoho instalace). Tím se vyhnete LVM název je v konfliktu s klonovaný virtuální počítače, zvlášť pokud někdy musí být připojené k jiným virtuálním Počítačem pro řešení potíží s disk s operačním systémem. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lze použít v datových disků, pokud upřednostňovaný.
* Pro větší velikosti virtuálních počítačů z důvodu chyby ve verzích systému Linux jádra níže 2.6.37 nepodporuje NUMA. Tento problém ovlivňuje především distribuce pomocí nadřazený Red Hat 2.6.32 jádra. Ruční instalace agenta Azure Linux (příkaz waagent) automaticky zakáže NUMA v konfiguraci GRUB jádra systému Linux. Další informace o této naleznete v následujících krocích.
* Nekonfigurujte přepnutí oddílu na disku operačního systému. Chcete-li vytvořit odkládací soubor na disku dočasných prostředků lze nakonfigurovat agenta systému Linux.  Další informace o této naleznete v následujících krocích.
* Všechny virtuální pevné disky na platformě Azure, musí mít virtuální velikost zarovnán 1MB. Při převodu z nezpracovaná disku na virtuální pevný disk je nutné zajistit, aby velikost disku nezpracovaná není násobkem 1MB před převodem. V tématu [poznámky k instalaci Linux](create-upload-generic.md#general-linux-installation-notes) Další informace.
* Ujistěte se, že `Addons` je povoleno úložiště. Upravte soubor `/etc/yum.repo.d/public-yum-ol6.repo`(Oracle Linux 6) nebo `/etc/yum.repo.d/public-yum-ol7.repo`(Oracle Linux) a změňte řádek `enabled=0` k `enabled=1` pod **[ol6_addons]** nebo **[ol7_addons]** v tomto souboru.

## <a name="oracle-linux-64"></a>Oracle Linux 6.4 +
Je třeba provést určitou konfiguraci kroky v operačním systému pro virtuální počítač na spuštění v Azure.

1. V prostředním podokně Správce technologie Hyper-V vyberte virtuální počítač.
2. Klikněte na tlačítko **Connect** a otevřete okno pro virtuální počítač.
3. Odinstalace NetworkManager spuštěním následujícího příkazu:
   
        # sudo rpm -e --nodeps NetworkManager
   
    **Poznámka:** Pokud ještě není nainstalovaný balíček, tento příkaz se nezdaří s chybovou zprávou. Toto chování se očekává.
4. Vytvořte soubor s názvem **sítě** v `/etc/sysconfig/` adresář, který obsahuje následující text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
5. Vytvořte soubor s názvem **ifcfg eth0** v `/etc/sysconfig/network-scripts/` adresář, který obsahuje následující text:
   
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
   
        # chkconfig network on
8. Nainstalujte python pyasn1 spuštěním následujícího příkazu:
   
        # sudo yum install python-pyasn1
9. Upravte řádku spouštěcí jádra ve vaší konfiguraci grub pro zahrnutí dalších jádra parametry Azure. Chcete tuto otevřete "/ boot/grub/menu.lst" v textovém editoru a ujistěte se, že výchozí jádra zahrnuje následující parametry:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off
   
   To také zajistí, všechny zprávy konzoly odešlou do první sériového portu, který může být užitečné Azure podporu pro ladění problémů. Tato akce zakáže NUMA z důvodu chyby Oracle Red Hat kompatibilní jádra systému.
   
   Kromě výše uvedeného, doporučuje se *odebrat* následující parametry:
   
        rhgb quiet crashkernel=auto
   
   Grafické a quiet spouštěcí nejsou užitečné při cloudovém prostředí, kde chceme, aby všechny protokoly pro odeslání do sériového portu.
   
   `crashkernel` Možnost může být vlevo nakonfigurované v případě potřeby, ale Všimněte si, že tento parametr se sníží množství dostupné paměti ve virtuálním počítači 128 MB nebo informace, které mohou způsobovat menší velikostí virtuálního počítače.
10. Ujistěte se, že SSH server je nainstalován a nakonfigurován na spuštění při spuštění.  Obvykle je to výchozí hodnota.
11. Nainstalujte Azure Linux Agent spuštěním následujícího příkazu. Nejnovější verze je 2.0.15.
    
        # sudo yum install WALinuxAgent
    
    Upozorňujeme, že instalace balíčku WALinuxAgent dojde k odebrání NetworkManager a balíčky NetworkManager gnome Pokud nebyly již odebrána jak je popsáno v kroku 2.
12. Nevytvářejte odkládacího prostoru na disku operačního systému.
    
    Azure Linux Agent mohou automaticky konfigurovat odkládacího souboru pomocí disku místní prostředek, který je připojen k virtuálnímu počítači po zřízení v Azure. Všimněte si, že je disk místní prostředek *dočasné* na disku a může vyprázdněny, když je virtuální počítač zrušit. Po instalaci Azure Linux Agent (viz předchozí krok), upravte následující parametry v /etc/waagent.conf odpovídajícím způsobem:
    
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

- - -
## <a name="oracle-linux-70"></a>Oracle Linux 7.0 +
**Změny v systému Oracle Linux 7**

Připravuje se virtuální počítač Oracle Linux 7 pro Azure je velmi podobné Oracle Linux 6, ale existuje několik důležitých rozdílů vhodné poznamenat:

* Red Hat kompatibilní jádra a Oracle je UEK3 jsou podporovány v Azure.  Doporučuje se UEK3 jádra.
* NetworkManager balíčku už v konfliktu s Azure Linux agent. Tento balíček je ve výchozím nastavení nainstalovaná a doporučujeme vám, že se neodebere.
* GRUB2 se teď používá jako výchozí zaváděcí program pro spouštění, tak postup úpravy parametry jádra se změnila (viz níže).
* XFS je nyní na výchozí systém souborů. Systém souborů ext4 pořád můžou použít v případě potřeby.

**Kroky konfigurace**

1. Ve Správci technologie Hyper-V vyberte virtuální počítač.
2. Klikněte na tlačítko **Connect** k otevření okna konzoly pro virtuální počítač.
3. Vytvořte soubor s názvem **sítě** v `/etc/sysconfig/` adresář, který obsahuje následující text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain
4. Vytvořte soubor s názvem **ifcfg eth0** v `/etc/sysconfig/network-scripts/` adresář, který obsahuje následující text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
5. Změňte pravidla udev, aby nedošlo ke generování statická pravidla pro alespoň jedno rozhraní sítě Ethernet. Tato pravidla může způsobit problémy při klonování virtuálního počítače v Microsoft Azure nebo Hyper-V:
   
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
6. Zajistěte, aby že síťové služby se spustí při spuštění spuštěním následujícího příkazu:
   
        # sudo chkconfig network on
7. Instalovat balíček python pyasn1 spuštěním následujícího příkazu:
   
        # sudo yum install python-pyasn1
8. Spusťte následující příkaz, který zrušte aktuální metadata yum a nainstalujte všechny aktualizace:
   
        # sudo yum clean all
        # sudo yum -y update
9. Upravte řádku spouštěcí jádra ve vaší konfiguraci grub pro zahrnutí dalších jádra parametry Azure. K tomu otevřete "/ etc/výchozí/grub" v textovém editoru a upravit `GRUB_CMDLINE_LINUX` parametr, například:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   To také zajistí, všechny zprávy konzoly odešlou do první sériového portu, který může být užitečné Azure podporu pro ladění problémů. Je také vypne nové zásady vytváření názvů OEL 7 pro síťové adaptéry. Kromě výše uvedeného, doporučuje se *odebrat* následující parametry:
   
       rhgb quiet crashkernel=auto
   
   Grafické a quiet spouštěcí nejsou užitečné při cloudovém prostředí, kde chceme, aby všechny protokoly pro odeslání do sériového portu.
   
   `crashkernel` Možnost může být vlevo nakonfigurované v případě potřeby, ale Všimněte si, že tento parametr se sníží množství dostupné paměti ve virtuálním počítači 128 MB nebo informace, které mohou způsobovat menší velikostí virtuálního počítače.
10. Po dokončení úprav "/ etc/výchozí/grub" za výše, spusťte následující příkaz k opětovnému sestavení grub konfigurace:
    
        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
11. Ujistěte se, že SSH server je nainstalován a nakonfigurován na spuštění při spuštění.  Obvykle je to výchozí hodnota.
12. Nainstalujte Azure Linux Agent spuštěním následujícího příkazu:
    
        # sudo yum install WALinuxAgent
        # sudo systemctl enable waagent
13. Nevytvářejte odkládacího prostoru na disku operačního systému.
    
    Azure Linux Agent mohou automaticky konfigurovat odkládacího souboru pomocí disku místní prostředek, který je připojen k virtuálnímu počítači po zřízení v Azure. Všimněte si, že je disk místní prostředek *dočasné* na disku a může vyprázdněny, když je virtuální počítač zrušit. Po instalaci Azure Linux Agent (viz předchozí krok), upravte následující parametry v /etc/waagent.conf odpovídajícím způsobem:
    
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
14. Spusťte následující příkaz pro zrušení zřízení virtuálního počítače a jeho přípravu pro zřizování na Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
15. Klikněte na tlačítko **akce -> vypnutí dolů** ve Správci technologie Hyper-V. Svůj disk VHD Linux je nyní připravena k odeslání do Azure.

## <a name="next-steps"></a>Další kroky
Nyní jste připraveni používat vaše VHD Oracle Linux k vytvoření nové virtuální počítače v Azure. Pokud je poprvé, že jste nahrávání souboru VHD do Azure, najdete v části [vytvořit virtuální počítač s Linuxem z vlastní disku](upload-vhd.md#option-1-upload-a-vhd).

