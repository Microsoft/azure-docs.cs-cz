---
title: Příprava Debian Linux virtuální pevný disk v Azure | Microsoft Docs
description: Naučte se vytvářet Debian 7 a 8 soubory virtuálního pevného disku pro nasazení v Azure.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: a6de7a7c-cc70-44e7-aed0-2ae6884d401a
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: f8e98ae823d03dae475efca48a4ce32f27317882
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
ms.locfileid: "30908839"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Příprava virtuálního pevného disku Debian pro Azure
## <a name="prerequisites"></a>Požadavky
Této části se předpokládá, že po instalaci operačního systému Debian Linux ze souboru .iso stáhnout z [Debian webu](https://www.debian.org/distrib/) na virtuální pevný disk. Existuje několik nástrojů k vytvoření soubory VHD; Technologie Hyper-V je pouze jedním z příkladů. Pokyny k použití technologie Hyper-V najdete v tématu [nainstalovat roli Hyper-V a konfigurace virtuálního počítače](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="installation-notes"></a>Poznámky k instalaci
* Najdete také [obecné poznámky k instalaci Linux](create-upload-generic.md#general-linux-installation-notes) Příprava na Azure Linux další tipy pro.
* Novější formát VHDX není podporovaný v Azure. Disk můžete převést do formátu virtuálního pevného disku pomocí Správce technologie Hyper-V nebo **convert-VHD prostředí** rutiny.
* Při instalaci systému Linux se doporučuje použít standardní oddíly spíše než LVM (často výchozí pro mnoho instalace). Tím se vyhnete LVM název je v konfliktu s klonovaný virtuální počítače, zvlášť pokud někdy musí být připojené k jiným virtuálním Počítačem pro řešení potíží s disk s operačním systémem. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lze použít v datových disků, pokud upřednostňovaný.
* Nekonfigurujte přepnutí oddílu na disku operačního systému. Chcete-li vytvořit odkládací soubor na disku dočasných prostředků lze nakonfigurovat Azure Linux agent. Další informace o této naleznete v následujících krocích.
* Všechny virtuální pevné disky na platformě Azure, musí mít virtuální velikost zarovnán 1MB. Při převodu z nezpracovaná disku na virtuální pevný disk je nutné zajistit, aby velikost disku nezpracovaná není násobkem 1MB před převodem. V tématu [poznámky k instalaci Linux](create-upload-generic.md#general-linux-installation-notes) Další informace.

## <a name="use-azure-manage-to-create-debian-vhds"></a>Použít správu Azure k vytvoření Debian virtuálních pevných disků
Nejsou k dispozici pro generování Debian virtuální pevné disky pro Azure, jako například nástroje [azure-spravovat](https://github.com/credativ/azure-manage) skriptů z [credativ](http://www.credativ.com/). Toto je doporučený postup a vytvoření bitové kopie od začátku. Můžete například vytvořit Debian virtuální pevný disk 8 spusťte následující příkazy ke stažení azure a spravovat (a závislosti) a spusťte skript azure_build_image:

    # sudo apt-get update
    # sudo apt-get install git qemu-utils mbr kpartx debootstrap

    # sudo apt-get install python3-pip python3-dateutil python3-cryptography
    # sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
    # git clone https://github.com/credativ/azure-manage.git
    # cd azure-manage
    # sudo pip3 install .

    # sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## <a name="manually-prepare-a-debian-vhd"></a>Ruční Příprava Debian virtuálního pevného disku
1. Ve Správci technologie Hyper-V vyberte virtuální počítač.
2. Klikněte na tlačítko **Connect** k otevření okna konzoly pro virtuální počítač.
3. Komentář čáry použité k **cdrom bázi deb** v `/etc/apt/source.list` Pokud nastavíte virtuální počítač pro soubor ISO.
4. Upravit `/etc/default/grub` soubor a upravte **GRUB_CMDLINE_LINUX** parametr takto pro zahrnutí dalších jádra parametry Azure.
   
        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200 earlyprintk=ttyS0,115200 rootdelay=30"
5. Grub znovu sestavte a spusťte:
   
        # sudo update-grub
6. Přidejte úložiště Azure pro Debian /etc/apt/sources.list Debian 7 nebo 8:
   
    **Debian 7.x "Wheezy"**
   
        deb http://debian-archive.trafficmanager.net/debian wheezy-backports main
        deb-src http://debian-archive.trafficmanager.net/debian wheezy-backports main
        deb http://debian-archive.trafficmanager.net/debian-azure wheezy main
        deb-src http://debian-archive.trafficmanager.net/debian-azure wheezy main

    **Debian 8.x "Klára"**

        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb http://debian-archive.trafficmanager.net/debian-azure jessie main
        deb-src http://debian-archive.trafficmanager.net/debian-azure jessie main


1. Nainstalujte agenta Azure Linux:
   
        # sudo apt-get update
        # sudo apt-get install waagent
2. U Debian 7 se vyžaduje ke spuštění na základě 3.16 jádra z wheezy backports úložiště. Nejprve vytvořte soubor s názvem /etc/apt/preferences.d/linux.pref s tímto obsahem:
   
        Package: linux-image-amd64 initramfs-tools
        Pin: release n=wheezy-backports
        Pin-Priority: 500
   
    Potom spusťte "sudo instalace výstižný get linux-image-amd64" k instalaci nové jádra.
3. Zrušení zřízení virtuálního počítače a jeho přípravu pro zřizování v Azure a spusťte:
   
        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout
4. Klikněte na tlačítko **akce** -> vypnutí dolů ve Správci technologie Hyper-V. Svůj disk VHD Linux je nyní připravena k odeslání do Azure.

## <a name="next-steps"></a>Další postup
Nyní jste připraveni použít Debian virtuální pevný disk k vytvoření nové virtuální počítače v Azure. Pokud je poprvé, že jste nahrávání souboru VHD do Azure, najdete v části [vytvořit virtuální počítač s Linuxem z vlastní disku](upload-vhd.md#option-1-upload-a-vhd).

