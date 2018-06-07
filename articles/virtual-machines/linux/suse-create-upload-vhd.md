---
title: Vytvořit a nahrát VHD SUSE Linux v Azure
description: Naučte se vytvořit a odeslat Azure virtuálního pevného disku (VHD) obsahující operační systém SUSE Linux.
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
ms.openlocfilehash: 99838a7038672998d4940bfb437bd31311d3600f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34653429"
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Příprava virtuálního počítače se SLES nebo openSUSE pro Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že jste již nainstalovali SUSE nebo openSUSE operační systém Linux virtuálního pevného disku. Postup vytvoření souborů VHD, například řešení virtualizace jako je například technologie Hyper-V existuje několik nástrojů. Pokyny najdete v tématu [nainstalovat roli Hyper-V a konfigurace virtuálního počítače](http://technet.microsoft.com/library/hh846766.aspx).

### <a name="sles--opensuse-installation-notes"></a>SLES / openSUSE poznámky k instalaci
* Najdete také [obecné poznámky k instalaci Linux](create-upload-generic.md#general-linux-installation-notes) Příprava na Azure Linux další tipy pro.
* Formát VHDX není podporován v Azure, pouze **pevný virtuální pevný disk**.  Disk můžete převést do formátu virtuálního pevného disku pomocí Správce technologie Hyper-V nebo rutiny convert-VHD prostředí.
* Při instalaci systému Linux se doporučuje použít standardní oddíly spíše než LVM (často výchozí pro mnoho instalace). Tím se vyhnete LVM název je v konfliktu s klonovaný virtuální počítače, zvlášť pokud někdy musí být připojené k jiným virtuálním Počítačem pro řešení potíží s disk s operačním systémem. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lze použít v datových disků, pokud upřednostňovaný.
* Nekonfigurujte přepnutí oddílu na disku operačního systému. Chcete-li vytvořit odkládací soubor na disku dočasných prostředků lze nakonfigurovat agenta systému Linux.  Další informace o této naleznete v následujících krocích.
* Všechny virtuální pevné disky na platformě Azure, musí mít virtuální velikost zarovnán 1MB. Při převodu z nezpracovaná disku na virtuální pevný disk je nutné zajistit, aby velikost disku nezpracovaná není násobkem 1MB před převodem. V tématu [poznámky k instalaci Linux](create-upload-generic.md#general-linux-installation-notes) Další informace.

## <a name="use-suse-studio"></a>Pomocí SUSE Studio
[SUSE Studio](http://www.susestudio.com) můžete snadno vytvořit a spravovat vaše SLES a openSUSE Image Azure a technologie Hyper-V. Toto je doporučený postup pro přizpůsobení vlastní SLES a openSUSE bitové kopie.

Jako alternativu k vytvoření vlastního virtuálního pevného disku, SUSE, publikuje také Image BYOS (přineste si vlastní předplatné) pro SLES v [VMDepot](https://vmdepot.msopentech.com/User/Show?user=1007).

## <a name="prepare-suse-linux-enterprise-server-11-sp4"></a>Příprava SUSE Linux Enterprise Server 11 SP4
1. V prostředním podokně Správce technologie Hyper-V vyberte virtuální počítač.
2. Klikněte na tlačítko **Connect** a otevřete okno pro virtuální počítač.
3. Zaregistrujte systému SUSE Linux Enterprise tak, aby ji ke stahování aktualizací a instalaci balíčků.
4. Aktualizace pomocí nejnovějších oprav systému:
   
        # sudo zypper update
5. Instalovat agenta systému Linux Azure z úložiště SLES:
   
        # sudo zypper install WALinuxAgent
6. Zkontrolujte Pokud příkaz waagent nastavená na "on" v chkconfig a pokud ne, povolit pro automatické spuštění:
   
        # sudo chkconfig waagent on
7. Zkontrolujte, zda příkaz waagent služby používá a pokud ne, spusťte ji: 
   
        # sudo service waagent start
8. Upravte řádku spouštěcí jádra ve vaší konfiguraci grub pro zahrnutí dalších jádra parametry Azure. Chcete tuto otevřete "/ boot/grub/menu.lst" v textovém editoru a ujistěte se, že výchozí jádra zahrnuje následující parametry:
   
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
   
    Tím bude zajištěno, všechny zprávy konzoly odešlou do první sériového portu, který může být užitečné Azure podporu pro ladění problémů.
9. Potvrďte, že /boot/grub/menu.lst a /etc/fstab obě odkazují disku pomocí jeho UUID (podle uuid) namísto ID disku (podle id). 
   
    Získat UUID disku
   
        # ls /dev/disk/by-uuid/
   
    Pokud /dev/disk/by-id nebo použít, aktualizovat /boot/grub/menu.lst a/etc/fstab s hodnotou správné podle uuid
   
    Před změnou
   
        root=/dev/disk/by-id/SCSI-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx-part1
   
    Po změně
   
        root=/dev/disk/by-uuid/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
10. Změňte pravidla udev, aby nedošlo ke generování statická pravidla pro alespoň jedno rozhraní sítě Ethernet. Tato pravidla může způsobit problémy při klonování virtuálního počítače v Microsoft Azure nebo Hyper-V:
    
        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
11. Je doporučené upravit soubor "/ etc a sysconfig nebo sítě nebo dhcp" a změnit `DHCLIENT_SET_HOSTNAME` parametr pro následující:
    
     DHCLIENT_SET_HOSTNAME = "žádný"
12. Komentář "/ etc/sudoers", nebo pokud existují, odeberte následující řádky:
    
     Výchozí nastavení targetpw # požádat o heslo cílový uživatel tj kořenové všechny ALL=(ALL) všechny # upozornění! Pouze to používají společně s 'Výchozí hodnoty targetpw'!
13. Ujistěte se, že SSH server je nainstalován a nakonfigurován na spuštění při spuštění.  Obvykle je to výchozí hodnota.
14. Nevytvářejte odkládacího prostoru na disku operačního systému.
    
    Azure Linux Agent mohou automaticky konfigurovat odkládacího souboru pomocí disku místní prostředek, který je připojen k virtuálnímu počítači po zřízení v Azure. Všimněte si, že je disk místní prostředek *dočasné* na disku a může vyprázdněny, když je virtuální počítač zrušit. Po instalaci Azure Linux Agent (viz předchozí krok), upravte následující parametry v /etc/waagent.conf odpovídajícím způsobem:
    
     ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resource ResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 ## Poznámka: tuto možnost nastavíte na ať už budete potřebovat mohla být.
15. Spusťte následující příkaz pro zrušení zřízení virtuálního počítače a jeho přípravu pro zřizování na Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
16. Klikněte na tlačítko **akce -> vypnutí dolů** ve Správci technologie Hyper-V. Svůj disk VHD Linux je nyní připravena k odeslání do Azure.

- - -
## <a name="prepare-opensuse-131"></a>Příprava openSUSE 13.1 +
1. V prostředním podokně Správce technologie Hyper-V vyberte virtuální počítač.
2. Klikněte na tlačítko **Connect** a otevřete okno pro virtuální počítač.
3. V prostředí, spusťte příkaz '`zypper lr`'. Pokud tento příkaz vrátí výstup podobný následujícímu, pak úložiště jsou nakonfigurované podle očekávání – bez úprav jsou nezbytné (Všimněte si, že verze čísla se mohou lišit):
   
        # | Alias                 | Name                  | Enabled | Refresh
        --+-----------------------+-----------------------+---------+--------
        1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
        2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
        3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes
   
    Pokud příkaz vrátí "Žádné úložiště definované..." použijte následující příkazy pro přidání těchto úložiště:
   
        # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
        # sudo zypper ar -f http://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
        # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
   
    Následně můžete ověřit úložiště přidané spuštěním příkazu '`zypper lr`se znovu. V případě, že jeden z úložiště příslušné aktualizace není povolen, můžete ji povolte pomocí následujícího příkazu:
   
        # sudo zypper mr -e [NUMBER OF REPOSITORY]
4. Aktualizujte na nejnovější dostupnou verzi jádra:
   
        # sudo zypper up kernel-default
   
    Nebo aktualizujte systém s nejnovější opravy:
   
        # sudo zypper update
5. Nainstalujte Azure Linux Agent.
   
        # sudo zypper install WALinuxAgent
6. Upravte řádku spouštěcí jádra ve vaší konfiguraci grub pro zahrnutí dalších jádra parametry Azure. Chcete-li to provést, otevřete "/ boot/grub/menu.lst" v textovém editoru a ujistěte se, že výchozí jádra zahrnuje následující parametry:
   
     konzole = ttyS0 earlyprintk = ttyS0 rootdelay = 300
   
   Tím bude zajištěno, všechny zprávy konzoly odešlou do první sériového portu, který může být užitečné Azure podporu pro ladění problémů. Kromě toho odeberte tyto parametry z řádku spouštěcí jádra, pokud existují:
   
     libata.atapi_enabled=0 reserve=0x1f0,0x8
7. Je doporučené upravit soubor "/ etc a sysconfig nebo sítě nebo dhcp" a změnit `DHCLIENT_SET_HOSTNAME` parametr pro následující:
   
     DHCLIENT_SET_HOSTNAME = "žádný"
8. **Důležité:** komentář "/ etc/sudoers", nebo odeberte následující řádky, pokud existují:
   
     Výchozí nastavení targetpw # požádat o heslo cílový uživatel tj kořenové všechny ALL=(ALL) všechny # upozornění! Pouze to používají společně s 'Výchozí hodnoty targetpw'!
9. Ujistěte se, že SSH server je nainstalován a nakonfigurován na spuštění při spuštění.  Obvykle je to výchozí hodnota.
10. Nevytvářejte odkládacího prostoru na disku operačního systému.
    
    Azure Linux Agent mohou automaticky konfigurovat odkládacího souboru pomocí disku místní prostředek, který je připojen k virtuálnímu počítači po zřízení v Azure. Všimněte si, že je disk místní prostředek *dočasné* na disku a může vyprázdněny, když je virtuální počítač zrušit. Po instalaci Azure Linux Agent (viz předchozí krok), upravte následující parametry v /etc/waagent.conf odpovídajícím způsobem:
    
     ResourceDisk.Format=y ResourceDisk.Filesystem=ext4 ResourceDisk.MountPoint=/mnt/resource ResourceDisk.EnableSwap=y ResourceDisk.SwapSizeMB=2048 ## Poznámka: tuto možnost nastavíte na ať už budete potřebovat mohla být.
11. Spusťte následující příkaz pro zrušení zřízení virtuálního počítače a jeho přípravu pro zřizování na Azure:
    
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
12. Zajistěte, aby že Azure Linux Agent spuštěna při spuštění systému:
    
        # sudo systemctl enable waagent.service
13. Klikněte na tlačítko **akce -> vypnutí dolů** ve Správci technologie Hyper-V. Svůj disk VHD Linux je nyní připravena k odeslání do Azure.

## <a name="next-steps"></a>Další postup
Nyní jste připraveni použít virtuální pevný disk SUSE Linux k vytvoření nové virtuální počítače v Azure. Pokud je poprvé, že jste nahrávání souboru VHD do Azure, najdete v části [vytvořit virtuální počítač s Linuxem z vlastní disku](upload-vhd.md#option-1-upload-a-vhd).
