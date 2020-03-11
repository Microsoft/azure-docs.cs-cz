---
title: Vytvoření a nahrání Ubuntu Linux VHD v Azure
description: Naučte se vytvořit a nahrát virtuální pevný disk Azure (VHD), který obsahuje Ubuntu Linux operační systém.
author: mimckitt
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 06/24/2019
ms.author: mimckitt
ms.openlocfilehash: cbb10d544cb299e15022ae47f00d3887d03619c0
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970286"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Příprava virtuálního počítače s Ubuntu pro Azure


Ubuntu nyní zveřejňuje oficiální virtuální pevné disky Azure ke stažení na adrese [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/). Pokud pro Azure potřebujete vytvořit vlastní specializovanou image Ubuntu, místo ručního použití níže uvedeného postupu doporučujeme začít s těmito známými pracovními virtuálními počítači a přizpůsobit podle potřeby. Nejnovější verze imagí lze vždy nalézt v následujících umístěních:

* Ubuntu 12.04/přesný: [Ubuntu-12,04-Server-cloudimg-amd64-Disk1. VHD. zip](https://cloud-images.ubuntu.com/precise/current/precise-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 14.04/TRUSTe: [Ubuntu-14,04-Server-cloudimg-amd64-Disk1. VHD. zip](https://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 16.04/Xenial: [Ubuntu-16,04-Server-cloudimg-amd64-Disk1. vmdk](https://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vmdk)
* Ubuntu 18.04/Bionic: [Bionic-Server-cloudimg-amd64. vmdk](https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.vmdk)
* Ubuntu 18.10/Cosmic: [Cosmic-Server-cloudimg-amd64. VHD. zip](http://cloud-images.ubuntu.com/releases/cosmic/release/ubuntu-18.10-server-cloudimg-amd64.vhd.zip)

## <a name="prerequisites"></a>Předpoklady
V tomto článku se předpokládá, že jste už Ubuntu Linux operační systém nainstalovali na virtuální pevný disk. Pro vytváření souborů. VHD, například virtualizačního řešení, jako je například Hyper-V, existuje více nástrojů. Pokyny najdete v tématu [instalace role Hyper-V a konfigurace virtuálního počítače](https://technet.microsoft.com/library/hh846766.aspx).

**Poznámky k instalaci Ubuntu**

* Další tipy k přípravě Linux pro Azure najdete v tématu [Obecné poznámky k instalaci pro Linux](create-upload-generic.md#general-linux-installation-notes) .
* Formát VHDX není v Azure podporovaný, jenom **pevný virtuální pevný disk**.  Disk můžete převést na formát VHD pomocí Správce technologie Hyper-V nebo rutiny Convert-VHD.
* Při instalaci systému Linux doporučujeme místo LVM použít standardní oddíly (často se jedná o výchozí nastavení pro mnoho instalací). Tím se vyhnete LVM názvům v konfliktu s klonovanými virtuálními počítači, zejména pokud se disk s operačním systémem někdy potřebuje připojit k jinému virtuálnímu počítači pro řešení potíží. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) se můžou použít na datových discích, pokud jsou preferované.
* Nekonfigurujte odkládací oddíl na disku s operačním systémem. Agent pro Linux se dá nakonfigurovat tak, aby na dočasném disku prostředků vytvořil odkládací soubor.  Další informace o tomto postupu najdete v následujících krocích.
* Všechny virtuální pevné disky v Azure musí mít virtuální velikost zarovnaná na 1 MB. Při převodu z nezpracovaného disku na virtuální pevný disk je nutné před převodem zajistit, aby velikost nezpracovaného disku byla násobkem 1 MB. Další informace najdete v [poznámkách k instalaci systému Linux](create-upload-generic.md#general-linux-installation-notes) .

## <a name="manual-steps"></a>Ruční kroky
> [!NOTE]
> Než se pokusíte vytvořit vlastní image Ubuntu pro Azure, zvažte použití předem připravených a testovaných imagí z [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) místo toho.
> 
> 

1. V prostředním podokně Správce technologie Hyper-V vyberte virtuální počítač.

2. Kliknutím na **připojit** otevřete okno pro virtuální počítač.

3. Nahraďte aktuální úložiště v imagi a použijte úložiště Azure Ubuntu. Postup se mírně liší v závislosti na verzi Ubuntu.
   
    Před úpravou `/etc/apt/sources.list`se doporučuje provést zálohování:
   
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

4. Image Ubuntu Azure teď následují za jádrem *povolení hardwaru* (hwe). Aktualizujte operační systém na nejnovější jádro spuštěním následujících příkazů:

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
    
    Ubuntu 18.04.04:
    
        # sudo apt-get update
        # sudo apt-get install --install-recommends linux-generic-hwe-18.04 xserver-xorg-hwe-18.04
        # sudo apt-get install --install-recommends linux-cloud-tools-generic-hwe-18.04
        (recommended) sudo apt-get dist-upgrade

        # sudo reboot
    
    **Viz také:**
    - [https://wiki.ubuntu.com/Kernel/LTSEnablementStack](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)
    - [https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack](https://wiki.ubuntu.com/Kernel/RollingLTSEnablementStack)


5. Upravte spouštěcí řádek jádra pro GRUB tak, aby zahrnoval další parametry jádra pro Azure. Chcete-li tento příkaz Otevřít `/etc/default/grub` v textovém editoru, najděte proměnnou s názvem `GRUB_CMDLINE_LINUX_DEFAULT` (nebo ji přidejte v případě potřeby) a upravte ji tak, aby obsahovala následující parametry:
   
        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    Uložte a zavřete tento soubor a potom spusťte `sudo update-grub`. Tím se zajistí, že se všechny zprávy konzoly odešlou na první sériový port, což může pomoct technickou podporu Azure s problémy ladění.

6. Ujistěte se, že je server SSH nainstalovaný a nakonfigurované tak, aby se spouštěl při spuštění.  Obvykle se jedná o výchozí nastavení.

7. Instalace agenta Azure Linux:
   
        # sudo apt-get update
        # sudo apt-get install walinuxagent

   > [!Note]
   >  Balíček `walinuxagent` může odebrat balíčky `NetworkManager` a `NetworkManager-gnome`, pokud jsou nainstalovány.


1. Spuštěním následujících příkazů můžete virtuální počítač zrušit a připravit ho pro zřizování v Azure:
   
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

1. Klikněte na **Akce – > vypnout** ve Správci technologie Hyper-V. Virtuální pevný disk se systémem Linux je teď připravený k nahrání do Azure.

## <a name="references"></a>Odkazy
[Jádro HWE (Ubuntu hardware Enable)](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)

## <a name="next-steps"></a>Další kroky
Nyní jste připraveni k vytváření nových virtuálních počítačů v Azure pomocí svého Ubuntu Linuxho virtuálního pevného disku. Pokud soubor. VHD do Azure nahráváte poprvé, přečtěte si článek [Vytvoření virtuálního počítače se systémem Linux z vlastního disku](upload-vhd.md#option-1-upload-a-vhd).

