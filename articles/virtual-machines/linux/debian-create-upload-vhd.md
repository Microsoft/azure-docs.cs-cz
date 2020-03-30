---
title: Příprava Virtuálního pevného disku Debianlinux
description: Přečtěte si, jak vytvořit image Virtuálního pevného disku Debianu pro nasazení virtuálních mích v Azure.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/13/2018
ms.author: guybo
ms.openlocfilehash: d54f7a11d929c31fee29a788eb3a2ae2cc8f2703
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066713"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Příprava virtuálního pevného disku Debianu pro Azure
## <a name="prerequisites"></a>Požadavky
Tato část předpokládá, že jste již nainstalovali operační systém Debian Linux ze souboru ISO staženého z [webových stránek Debianu](https://www.debian.org/distrib/) na virtuální pevný disk. Existuje více nástrojů pro vytváření souborů .vhd; Hyper-V je pouze jeden příklad. Pokyny pomocí technologie Hyper-V naleznete [v tématu Instalace role Technologie Hyper-V a Konfigurace virtuálního počítače](https://technet.microsoft.com/library/hh846766.aspx).

## <a name="installation-notes"></a>Poznámky k instalaci
* Další tipy na přípravu Linuxu pro Azure najdete taky v [obecných poznámkách](create-upload-generic.md#general-linux-installation-notes) k instalaci Linuxu.
* Novější formát VHDX není v Azure podporován. Disk můžete převést do formátu Virtuálního pevného disku pomocí Správce technologie Hyper-V nebo rutiny **convert-vhd.**
* Při instalaci systému Linux se doporučuje používat standardní oddíly spíše než LVM (často výchozí pro mnoho instalací). Tím se zabrání lVM název konflikty s klonované virtuální počítače, zejména v případě, že disk operačního systému někdy musí být připojen k jinému virtuálnímu počítače pro řešení potíží. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) mohou být použity na datových discích, pokud je to výhodné.
* Nekonfigurujte odkládací oddíl na disku operačního systému. Agent azure linux lze nakonfigurovat k vytvoření odkládacího souboru na disku dočasného prostředku. Další informace naleznete v následujících krocích.
* Všechny virtuální počítače v Azure musí mít virtuální velikost zarovnanou na 1 MB. Při převodu z nezpracovaného disku na virtuální pevný disk je nutné zajistit, aby velikost nezpracovaného disku byla před převodem násobkem 1 MB. Další informace naleznete v [tématu Poznámky k instalaci linuxu](create-upload-generic.md#general-linux-installation-notes).

## <a name="use-azure-manage-to-create-debian-vhds"></a>Použití Azure-Manage k vytvoření Virtuálních dispozíka Debianu
K dispozici jsou nástroje pro generování vsucích VD Debianu pro Azure, jako jsou skripty [azure-manage](https://github.com/credativ/azure-manage) z [Credativu](https://www.credativ.com/). Toto je doporučený přístup versus vytvoření obrázku od začátku. Chcete-li například vytvořit virtuální pevný disk Debian 8, spusťte následující příkazy ke stažení `azure-manage` nástroje (a závislostí) a spuštění skriptu: `azure_build_image`

    # sudo apt-get update
    # sudo apt-get install git qemu-utils mbr kpartx debootstrap

    # sudo apt-get install python3-pip python3-dateutil python3-cryptography
    # sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
    # git clone https://github.com/credativ/azure-manage.git
    # cd azure-manage
    # sudo pip3 install .

    # sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## <a name="manually-prepare-a-debian-vhd"></a>Ruční příprava hd rozhraní Debian
1. Ve Správci technologie Hyper-V vyberte virtuální počítač.
2. Kliknutím na **Připojit** otevřete okno konzoly pro virtuální počítač.
3. Pokud jste operační systém nainstalovali pomocí iso, zakomentujte všechny řádky týkající se "`deb cdrom`v `/etc/apt/source.list`.

4. Upravte `/etc/default/grub` soubor a upravte parametr **GRUB_CMDLINE_LINUX** takto, aby zahrnoval další parametry jádra pro Azure.
   
        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200n8 earlyprintk=ttyS0,115200"

5. Znovu vybudovat grub a spustit:

        # sudo update-grub

6. Přidejte repozitáře Azure debianu do /etc/apt/sources.list pro Debian 8 nebo 9:

    **Debian 8.x "Jessie"**

        deb http://debian-archive.trafficmanager.net/debian jessie main
        deb-src http://debian-archive.trafficmanager.net/debian jessie main
        deb http://debian-archive.trafficmanager.net/debian-security jessie/updates main
        deb-src http://debian-archive.trafficmanager.net/debian-security jessie/updates
        deb http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-updates main
        deb http://debian-archive.trafficmanager.net/debian jessie-backports main
        deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main

    **Debian 9.x "Roztáhnout"**

        deb http://debian-archive.trafficmanager.net/debian stretch main
        deb-src http://debian-archive.trafficmanager.net/debian stretch main
        deb http://debian-archive.trafficmanager.net/debian-security stretch/updates main
        deb-src http://debian-archive.trafficmanager.net/debian-security stretch/updates main
        deb http://debian-archive.trafficmanager.net/debian stretch-updates main
        deb-src http://debian-archive.trafficmanager.net/debian stretch-updates main
        deb http://debian-archive.trafficmanager.net/debian stretch-backports main
        deb-src http://debian-archive.trafficmanager.net/debian stretch-backports main


7. Nainstalujte agenta Azure Linuxu:
   
        # sudo apt-get update
        # sudo apt-get install waagent

8. Pro Debian 9+ se doporučuje používat nové jádro Debian Cloud pro použití s virtuálními počítači v Azure. Chcete-li nainstalovat toto nové jádro, nejprve vytvořte soubor s názvem /etc/apt/preferences.d/linux.pref s následujícím obsahem:
   
        Package: linux-* initramfs-tools
        Pin: release n=stretch-backports
        Pin-Priority: 500
   
    Pak spusťte "sudo apt-get install linux-image-cloud-amd64" pro instalaci nového jádra Debian Cloud.

9. Zrušení zřízení virtuálního počítače a jeho příprava na zřizování v Azure a spuštění:
   
        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout

10. Ve Správci technologie Hyper-V klikněte na **Akce** -> Vypnout. Virtuální disk Linux ukterýzdvižený disk linuxu je teď připravený k nahrání do Azure.

## <a name="next-steps"></a>Další kroky
Nyní jste připraveni použít virtuální pevný disk Debianu k vytvoření nových virtuálních počítačů v Azure. Pokud je to poprvé, co nahráváte soubor .vhd do Azure, přečtěte si téma [Vytvoření virtuálního počítače s Linuxem z vlastního disku](upload-vhd.md#option-1-upload-a-vhd).

