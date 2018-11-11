---
title: Vytvoření a nahrání VHD Ubuntu Linux v Azure
description: Zjistěte, jak vytvořit a nahrát Azure virtuálního pevného disku (VHD), který obsahuje operační systém Ubuntu Linux.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 3e097959-84fc-4f6a-8cc8-35e087fd1542
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: 70aa49cf15b095697eb00cc2a0b8e6dfd2e07546
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51240473"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Příprava virtuálního počítače s Ubuntu pro Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="official-ubuntu-cloud-images"></a>Oficiální Image Ubuntu cloudu
Ubuntu nyní publikuje oficiální Azure virtuálních pevných disků ke stažení na [ http://cloud-images.ubuntu.com/ ](http://cloud-images.ubuntu.com/). Pokud budete muset sestavit vlastní specializované image Ubuntu pro Azure, spíše než pomocí níže uvedeného ručního postupu se doporučuje spustit s těmito označuje práci virtuální pevné disky a podle potřeby upravte. Nejnovější verze image vždy najdete v následujících umístěních:

* Ubuntu 12.04/přesné: [ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/precise/current/precise-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 14.04/Trusty: [ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 16.04/Xenial: [ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 18.04/Bionic: [bionic-server-cloudimg-amd64.vhd.zip](http://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.vhd.zip)
* Ubuntu 18.10/Cosmic: [cosmic-server-cloudimg-amd64.vhd.zip](http://cloud-images.ubuntu.com/cosmic/current/cosmic-server-cloudimg-amd64.vhd.zip)

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že jste již nainstalovali operačního systému Ubuntu Linux na virtuální pevný disk. Existují více nástroje k vytvoření souborů .vhd, třeba řešení virtualizace jako je Hyper-V. Pokyny najdete v tématu [instalace Role Hyper-V a konfigurace virtuálního počítače](https://technet.microsoft.com/library/hh846766.aspx).

**Poznámky k instalaci Ubuntu**

* Podrobnosti najdete také [obecné poznámky k instalaci Linux](create-upload-generic.md#general-linux-installation-notes) další tipy pro na Příprava Linuxu na Azure.
* Formát VHDX nepodporuje v Azure, pouze **oprava virtuálního pevného disku**.  Převést disk na formát virtuálního pevného disku pomocí Správce technologie Hyper-V nebo rutiny convert-vhd.
* Při instalaci systému Linux se doporučuje použít standardní oddíly spíše než LVM (často výchozí nastavení pro mnoho zařízení). Tím se vyhnete LVM název je v konfliktu s klonovaný virtuální počítače, zejména v případě, že disk s operačním systémem je někdy potřeba připojit k jinému virtuálnímu počítači pro řešení potíží. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) může být použita na datové disky, pokud tomu dávají přednost.
* Neprovádějte konfiguraci odkládací oddíl na disk s operačním systémem. Chcete-li vytvořit odkládací soubor na disku dočasný prostředek, který lze nastavit agenta pro Linux.  Další informace najdete v níže uvedeném postupu.
* Všechny virtuální pevné disky v Azure musí mít virtuální velikost, zarovnání na 1MB. Při převodu z nezpracované disku do virtuálního pevného disku je nutné zajistit, že velikost nezpracovaných disku je násobkem 1MB před převodem. Zobrazit [poznámky k instalaci Linux](create-upload-generic.md#general-linux-installation-notes) Další informace.

## <a name="manual-steps"></a>Vyžadováno provedení ručních kroků
> [!NOTE]
> Před pokusem o vytvoření vlastní image Ubuntu pro Azure, zvažte použití předem připravené a otestovaná Image z [ http://cloud-images.ubuntu.com/ ](http://cloud-images.ubuntu.com/) místo.
> 
> 

1. V prostředním podokně Správce technologie Hyper-V vyberte virtuální počítač.

2. Klikněte na tlačítko **připojit** otevřete okno pro virtuální počítač.

3. Nahraďte aktuální úložišť v požadované image Ubuntu pro úložiště Azure. Kroky mírně lišit v závislosti na verzi Ubuntu.
   
    Před úpravou `/etc/apt/sources.list`, doporučuje se vytvořit zálohu:
   
        # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

    Ubuntu 12.04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 14.04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

    Ubuntu 16.04:
   
        # sudo sed -i 's/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g' /etc/apt/sources.list
        # sudo apt-get update

4. Nyní jste tuto Image Ubuntu v Azure *hardwaru povolení* jádra (HWE). Aktualizujte operační systém na nejnovější jádra spuštěním následujících příkazů:

    Ubuntu 12.04:
   
        # sudo apt-get update
        # sudo apt-get install linux-image-generic-lts-trusty linux-cloud-tools-generic-lts-trusty
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot
   
    Ubuntu 14.04:
   
        # sudo apt-get update
        # sudo apt-get install linux-image-virtual-lts-vivid linux-lts-vivid-tools-common
        # sudo apt-get install hv-kvp-daemon-init
        (recommended) sudo apt-get dist-upgrade
   
        # sudo reboot

    Ubuntu 16.04:
   
        # sudo apt-get update
        # sudo apt-get install linux-generic-hwe-16.04 linux-cloud-tools-generic-hwe-16.04
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot

    **Viz také:**
    - [https://wiki.ubuntu.com/Kernel/LTSEnablementStack](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)
    - [https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack](https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack)


5. Upravte řádek pro spuštění jádra pro Grub zahrnout další jádra parametry pro Azure. Provedete tuto otevřete `/etc/default/grub` v textovém editoru, najdete proměnnou s názvem `GRUB_CMDLINE_LINUX_DEFAULT` (nebo ji v případě potřeby přidejte) a upravit tak, aby obsahovala následující parametry:
   
        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    Uložte a zavřete tento soubor a pak spusťte `sudo update-grub`. Tím se zajistí, že jsou všechny konzoly zprávy odeslané do první sériového portu, které vám můžou pomoct technické podpory Azure s laděním problémů.

6. Zajistěte, aby SSH server je nainstalován a nakonfigurován na spuštění při spuštění.  Obvykle se jedná o výchozí nastavení.

7. Instalace agenta Azure Linux:
   
        # sudo apt-get update
        # sudo apt-get install walinuxagent

    >[!Note]
    `walinuxagent` Může odebrat balíček `NetworkManager` a `NetworkManager-gnome` balíčky, pokud jsou nainstalovány.

8. Spusťte následující příkaz pro zrušení zřízení virtuálního počítače a připravte je ke zřizování v Azure:
   
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

9. Klikněte na tlačítko **akce -> vypnout dolů** ve Správci technologie Hyper-V. Vašeho linuxového virtuálního pevného disku je teď připravený k nahrání do Azure.

## <a name="references"></a>Odkazy
[Ubuntu hardwaru povolení (HWE) jádra](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)

## <a name="next-steps"></a>Další postup
Nyní jste připraveni použít virtuální pevný disk se systémem Ubuntu Linux můžete vytvořit nové virtuální počítače v Azure. Pokud je to poprvé, že jste nahrání souboru VHD do Azure, najdete v článku [vytvoření virtuálního počítače s Linuxem z vlastního disku](upload-vhd.md#option-1-upload-a-vhd).




