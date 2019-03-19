---
title: Příprava Debian linuxového virtuálního pevného disku v Azure | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit Image virtuálního pevného disku Debian pro nasazení v Azure.
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
ms.date: 11/13/2018
ms.author: szark
ms.openlocfilehash: 6ef0a9f6efbf5f8398ba242150b2eb6102875f7e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58000324"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Příprava virtuálního pevného disku Debian pro Azure
## <a name="prerequisites"></a>Požadavky
Tato část předpokládá, že už jste nainstalovali operačního systému Debian Linux ze souboru ISO stáhli z [Debian webu](https://www.debian.org/distrib/) na virtuální pevný disk. Postup vytvoření souborů .vhd; existují více nástroje Technologie Hyper-V je pouze jedním z příkladů. Pokyny k použití technologie Hyper-V najdete v tématu [instalace Role Hyper-V a konfigurace virtuálního počítače](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="installation-notes"></a>Poznámky k instalaci
* Viz také [obecné poznámky k instalaci Linux](create-upload-generic.md#general-linux-installation-notes) další tipy pro na Příprava Linuxu na Azure.
* Novější formát VHDX nepodporuje v Azure. Disk můžete převést do formátu virtuálního pevného disku pomocí Správce technologie Hyper-V nebo **convert-vhd** rutiny.
* Při instalaci systému Linux, doporučuje se použít standardní oddíly spíše než LVM (často výchozí nastavení pro mnoho zařízení). Tím se vyhnete LVM název je v konfliktu s klonovaný virtuální počítače, zejména v případě, že disk s operačním systémem je někdy potřeba připojit k jinému virtuálnímu počítači pro řešení potíží. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) může být použita na datové disky, pokud tomu dávají přednost.
* Neprovádějte konfiguraci odkládací oddíl na disk s operačním systémem. Chcete-li vytvořit odkládací soubor na disku dočasný prostředek, který lze nastavit agenta Azure Linux. Další informace najdete v níže uvedeném postupu.
* Všechny virtuální pevné disky v Azure musí mít virtuální velikost, zarovnání na 1MB. Při převodu z nezpracované disku na virtuální pevný disk, je nutné zajistit, že velikost nezpracovaných disku je násobkem 1MB před převodem. Další informace najdete v tématu [poznámky k instalaci Linux](create-upload-generic.md#general-linux-installation-notes).

## <a name="use-azure-manage-to-create-debian-vhds"></a>Použít spravovat Azure k vytvoření Debian virtuálních pevných disků
Nejsou k dispozici pro generování Debian virtuální pevné disky pro Azure, jako například nástroje [azure – Správa](https://github.com/credativ/azure-manage) skriptům z [Credativ](https://www.credativ.com/). Toto je doporučený postup porovnání s vytvářením image od začátku. Například k vytvoření virtuálního pevného disku Debian 8 spustit následující příkazy ke stažení `azure-manage` nástroj (a závislostí) a spusťte `azure_build_image` skriptu:

    # sudo apt-get update
    # sudo apt-get install git qemu-utils mbr kpartx debootstrap

    # sudo apt-get install python3-pip python3-dateutil python3-cryptography
    # sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
    # git clone https://github.com/credativ/azure-manage.git
    # cd azure-manage
    # sudo pip3 install .

    # sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## <a name="manually-prepare-a-debian-vhd"></a>Ruční Příprava virtuálního pevného disku Debian
1. Ve Správci technologie Hyper-V vyberte virtuální počítač.
2. Klikněte na tlačítko **připojit** otevřete okno konzoly pro virtuální počítač.
3. Pokud jste nainstalovali operačního systému pomocí Image ISO, pak okomentovat každý řádek týkající se "`deb cdrom`" v `/etc/apt/source.list`.

4. Upravit `/etc/default/grub` souborů a upravovat **GRUB_CMDLINE_LINUX** parametr takto zahrnout další jádra parametry pro Azure.
   
        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200n8 earlyprintk=ttyS0,115200"

5. Grub znovu sestavte a spusťte:

        # sudo update-grub

6. Přidejte úložiště Azure pro Debian /etc/apt/sources.list pro Debian 8 a 9:

    **Debian 8.x "Jessie"**

        deb http://debian-archive.trafficmanager.net/debian jessie main
        deb-src http://debian-archive.trafficmanager.net/debian jessie main
        deb http://debian-archive.trafficmanager.net/debian-security jessie/updates main
        deb-src http://debian-archive.trafficmanager.net/debian-security jessie/updates
        deb http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main

    **Debian 9.x "Stretch"**

        deb http://debian-archive.trafficmanager.net/debian stretch main
        deb-src http://debian-archive.trafficmanager.net/debian stretch main
        deb http://debian-archive.trafficmanager.net/debian-security stretch/updates main
        deb-src http://debian-archive.trafficmanager.net/debian-security stretch/updates main
        deb http://debian-archive.trafficmanager.net/debian stretch-updates main
        deb-src http://debian-archive.trafficmanager.net/debian stretch-updates main
        deb http://debian-archive.trafficmanager.net/debian stretch-backports main
        deb-src http://debian-archive.trafficmanager.net/debian stretch-backports main


7. Instalace agenta Azure Linux:
   
        # sudo apt-get update
        # sudo apt-get install waagent

8. Pro Debian 9 + doporučujeme používat novou jádra Debian cloudu pro použití s virtuálními počítači v Azure. Chcete-li nainstalovat tento nový jádra, nejprve vytvořte soubor s názvem /etc/apt/preferences.d/linux.pref s následujícím obsahem:
   
        Package: linux-* initramfs-tools
        Pin: release n=stretch-backports
        Pin-Priority: 500
   
    Potom spusťte "sudo apt-get install linux-image-cloud – amd64" k instalaci nové jádra Debian cloudu.

9. Zrušení zřízení virtuálního počítače a připravit ho ke zřizování v Azure a spusťte:
   
        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout

10. Klikněte na tlačítko **akce** -> vypnout dolů ve Správci technologie Hyper-V. Vašeho linuxového virtuálního pevného disku je teď připravený k nahrání do Azure.

## <a name="next-steps"></a>Další postup
Teď jste připraveni používat Debian virtuální pevný disk k vytvoření nových virtuálních počítačů v Azure. Pokud je to poprvé, že jste nahrání souboru VHD do Azure, najdete v článku [vytvoření virtuálního počítače s Linuxem z vlastního disku](upload-vhd.md#option-1-upload-a-vhd).

