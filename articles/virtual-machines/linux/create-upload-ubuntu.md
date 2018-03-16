---
title: "Vytvořit a odeslat Ubuntu Linux virtuální pevný disk v Azure"
description: "Naučte se vytvořit a odeslat Azure virtuálního pevného disku (VHD) obsahující Ubuntu Linux operačního systému."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
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
ms.openlocfilehash: 4c49cbefafe71646ba08dd049baf50ff04463fdc
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Příprava virtuálního počítače s Ubuntu pro Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="official-ubuntu-cloud-images"></a>Oficiální Ubuntu cloudu obrázků
Ubuntu nyní publikuje oficiální Azure virtuální pevné disky pro stahování na [ http://cloud-images.ubuntu.com/ ](http://cloud-images.ubuntu.com/). Pokud potřebujete vytvořit vlastní specializované Ubuntu bitovou kopii pro Azure, spíš než ruční následujícího postupu se doporučuje spustit pomocí těchto známé práce virtuální pevné disky a přizpůsobit podle potřeby. Nejnovější verze bitové kopie vždycky naleznete v následujících umístěních:

* Ubuntu 12.04 nebo přesné: [ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/precise/current/precise-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 14.04/Trusty: [ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 16.04/Xenial: [ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip](http://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vhd.zip)

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že jste již nainstalovali Ubuntu Linux operačního systému na virtuální pevný disk. Postup vytvoření souborů VHD, například řešení virtualizace jako je například technologie Hyper-V existuje několik nástrojů. Pokyny najdete v tématu [nainstalovat roli Hyper-V a konfigurace virtuálního počítače](http://technet.microsoft.com/library/hh846766.aspx).

**Poznámky k instalaci Ubuntu**

* Najdete také [obecné poznámky k instalaci Linux](create-upload-generic.md#general-linux-installation-notes) Příprava na Azure Linux další tipy pro.
* Formát VHDX není podporován v Azure, pouze **pevný virtuální pevný disk**.  Disk můžete převést do formátu virtuálního pevného disku pomocí Správce technologie Hyper-V nebo rutiny convert-VHD prostředí.
* Při instalaci systému Linux se doporučuje použít standardní oddíly spíše než LVM (často výchozí pro mnoho instalace). Tím se vyhnete LVM název je v konfliktu s klonovaný virtuální počítače, zvlášť pokud někdy musí být připojené k jiným virtuálním Počítačem pro řešení potíží s disk s operačním systémem. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lze použít v datových disků, pokud upřednostňovaný.
* Nekonfigurujte přepnutí oddílu na disku operačního systému. Chcete-li vytvořit odkládací soubor na disku dočasných prostředků lze nakonfigurovat agenta systému Linux.  Další informace o této naleznete v následujících krocích.
* Všechny virtuální pevné disky na platformě Azure, musí mít virtuální velikost zarovnán 1MB. Při převodu z nezpracovaná disku na virtuální pevný disk je nutné zajistit, aby velikost disku nezpracovaná není násobkem 1MB před převodem. V tématu [poznámky k instalaci Linux](create-upload-generic.md#general-linux-installation-notes) Další informace.

## <a name="manual-steps"></a>Provedení ručních kroků
> [!NOTE]
> Před pokusem o vytvoření vlastní image Ubuntu pro Azure, zvažte prosím pomocí předdefinovaných a otestovaná Image z [ http://cloud-images.ubuntu.com/ ](http://cloud-images.ubuntu.com/) místo.
> 
> 

1. V prostředním podokně Správce technologie Hyper-V vyberte virtuální počítač.

2. Klikněte na tlačítko **Connect** a otevřete okno pro virtuální počítač.

3. Nahraďte aktuální úložiště v bitové kopii používat Ubuntu na úložiště Azure. Kroky mírně lišit v závislosti na verzi Ubuntu.
   
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

4. Jsou nyní následující obrázky Ubuntu Azure *hardwaru povolování* jádra (HWE). Aktualizujte operační systém na nejnovější jádra spuštěním následujících příkazů:

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


5. Upravte řádek spouštěcí jádra pro Grub pro zahrnutí dalších jádra parametry Azure. Chcete tuto otevřete `/etc/default/grub` v textovém editoru, najdete proměnné s názvem `GRUB_CMDLINE_LINUX_DEFAULT` (nebo v případě potřeby přidejte ho) a upravit tak, aby obsahovala následující parametry:
   
        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    Uložte a zavřete tento soubor a pak spusťte `sudo update-grub`. Tím bude zajištěno, že všechny zprávy konzoly odešlou do první sériového portu, který může být užitečné Azure technickou podporu pro ladění problémů.

6. Ujistěte se, že SSH server je nainstalován a nakonfigurován na spuštění při spuštění.  Obvykle je to výchozí hodnota.

7. Nainstalujte agenta Azure Linux:
   
        # sudo apt-get update
        # sudo apt-get install walinuxagent

    >[!Note]
    `walinuxagent` Může dojít k odstranění balíčku `NetworkManager` a `NetworkManager-gnome` balíčky, pokud jsou nainstalovány.

8. Spusťte následující příkaz pro zrušení zřízení virtuálního počítače a jeho přípravu pro zřizování na Azure:
   
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

9. Klikněte na tlačítko **akce -> vypnutí dolů** ve Správci technologie Hyper-V. Svůj disk VHD Linux je nyní připravena k odeslání do Azure.

## <a name="next-steps"></a>Další kroky
Nyní jste připraveni používat Ubuntu Linux virtuální pevný disk pro vytvoření nového virtuálního počítače v Azure. Pokud je poprvé, že jste nahrávání souboru VHD do Azure, najdete v části [vytvořit virtuální počítač s Linuxem z vlastní disku](upload-vhd.md#option-1-upload-a-vhd).

## <a name="references"></a>Odkazy
Ubuntu hardwaru povolování (HWE) jádra:

* [http://blog.utlemming.org/2015/01/ubuntu-1404-azure-images-now-tracking.html](http://blog.utlemming.org/2015/01/ubuntu-1404-azure-images-now-tracking.html)
* [http://blog.utlemming.org/2015/02/1204-azure-cloud-images-now-using-hwe.html](http://blog.utlemming.org/2015/02/1204-azure-cloud-images-now-using-hwe.html)

