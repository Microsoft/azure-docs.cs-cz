---
title: Vytvoření a nahrání virtuálního pevného disku Ubuntu Linux v Azure
description: Naučte se vytvářet a nahrávat virtuální pevný disk Azure (VHD), který obsahuje operační systém Ubuntu Linux.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 06/24/2019
ms.author: guybo
ms.openlocfilehash: 5fa3415d8663f358bf0ae48be46ac52b8f8b4b06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066734"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Příprava virtuálního počítače s Ubuntu pro Azure


Ubuntu nyní vydává oficiální Azure VDke [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/)ke stažení na . Pokud potřebujete vytvořit vlastní specializovanou bitovou kopii Ubuntu pro Azure, spíše než použít níže uvedený ruční postup, doporučujeme začít s těmito známými funkčními virtuálními počítači a přizpůsobit se podle potřeby. Nejnovější verze obrázků lze vždy nalézt na následujících místech:

* Ubuntu 12.04/Precise: [ubuntu-12.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/precise/current/precise-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 14.04/Trusty: [ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip](https://cloud-images.ubuntu.com/releases/trusty/release/ubuntu-14.04-server-cloudimg-amd64-disk1.vhd.zip)
* Ubuntu 16.04/Xenial: [ubuntu-16.04-server-cloudimg-amd64-disk1.vmdk](https://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vmdk)
* Ubuntu 18.04/Bionic: [bionic-server-cloudimg-amd64.vmdk](https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.vmdk)
* Ubuntu 18.10/Cosmic: [cosmic-server-cloudimg-amd64.vhd.zip](http://cloud-images.ubuntu.com/releases/cosmic/release/ubuntu-18.10-server-cloudimg-amd64.vhd.zip)

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že jste již nainstalovali operační systém Ubuntu Linux na virtuální pevný disk. Existuje více nástrojů pro vytváření souborů .vhd, například virtualizační řešení, jako je Hyper-V. Pokyny naleznete [v tématu Instalace role Technologie Hyper-V a Konfigurace virtuálního počítače](https://technet.microsoft.com/library/hh846766.aspx).

**Poznámky k instalaci Ubuntu**

* Další tipy na přípravu Linuxu pro Azure najdete také v [obecných poznámkách k instalaci Linuxu.](create-upload-generic.md#general-linux-installation-notes)
* Formát VHDX není v Azure podporován, pouze **pevný virtuální pevný disk .**  Disk můžete převést do formátu Virtuálního pevného disku pomocí Správce technologie Hyper-V nebo rutiny convert-vhd.
* Při instalaci systému Linux se doporučuje používat standardní oddíly spíše než LVM (často výchozí pro mnoho instalací). Tím se zabrání lVM název konflikty s klonované virtuální počítače, zejména v případě, že disk operačního systému někdy musí být připojen k jinému virtuálnímu počítače pro řešení potíží. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) mohou být použity na datových discích, pokud je to výhodné.
* Nekonfigurujte odkládací oddíl na disku operačního systému. Agenta Linuxu lze nakonfigurovat tak, aby vytvořil odkládací soubor na disku s dočasným prostředkem.  Další informace o tom naleznete v následujících krocích.
* Všechny virtuální počítače v Azure musí mít virtuální velikost zarovnanou na 1 MB. Při převodu ze surového disku na virtuální pevný disk je nutné zajistit, aby velikost nezpracovaného disku byla před převodem násobkem 1 MB. Další informace naleznete v [poznámkách k instalaci Linuxu.](create-upload-generic.md#general-linux-installation-notes)

## <a name="manual-steps"></a>Ruční kroky
> [!NOTE]
> Než se pokusíte vytvořit vlastní image Ubuntu pro Azure, zvažte [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) místo toho použít předem sestavené a testované obrázky.
> 
> 

1. V prostředním podokně Správce technologie Hyper-V v vyberte virtuální počítač.

2. Kliknutím na **Připojit** otevřete okno virtuálního počítače.

3. Nahraďte aktuální úložiště v bitové kopii a použijte úložiště Azure ubuntu. Kroky se mírně liší v závislosti na verzi Ubuntu.
   
    Před úpravou `/etc/apt/sources.list`se doporučuje vytvořit zálohu:
   
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

4. Image Ubuntu Azure nyní sledují jádro *hardwarového povolení* (HWE). Aktualizujte operační systém na nejnovější jádro spuštěním následujících příkazů:

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


5. Upravte zaváděcí řádek jádra pro Grub tak, aby obsahovala další parametry jádra pro Azure. Chcete-li `/etc/default/grub` to otevřít v textovém `GRUB_CMDLINE_LINUX_DEFAULT` editoru, vyhledejte proměnnou nazvanou (nebo ji v případě potřeby přidejte) a upravte ji tak, aby zahrnovala následující parametry:
   
        GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300"

    Uložte a zavřete tento `sudo update-grub`soubor a spusťte jej . Tím zajistíte, že všechny zprávy konzoly jsou odesílány na první sériový port, který může pomoci technické podpoře Azure s problémy s laděním.

6. Ujistěte se, že je server SSH nainstalován a nakonfigurován tak, aby se spouštěl při spuštění.  Toto je obvykle výchozí.

7. Nainstalujte agenta Azure Linuxu:
   
        # sudo apt-get update
        # sudo apt-get install walinuxagent

   > [!Note]
   >  Balíček `walinuxagent` může `NetworkManager` odebrat a `NetworkManager-gnome` balíčky, pokud jsou nainstalovány.


1. Spusťte následující příkazy, abyste deprovision zanatii virtuálního počítače a připravit ho na zřizování v Azure:
   
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout

1. Ve Správci technologie Hyper-V klikněte na **Akce -> Vypnout.** Virtuální disk Linux ukterýzdvižený disk linuxu je teď připravený k nahrání do Azure.

## <a name="references"></a>Odkazy
[Ubuntu hardware enablement (HWE) jádro](https://wiki.ubuntu.com/Kernel/LTSEnablementStack)

## <a name="next-steps"></a>Další kroky
Teď jste připraveni použít virtuální pevný disk Ubuntu Linux k vytvoření nových virtuálních počítačů v Azure. Pokud je to poprvé, co nahráváte soubor .vhd do Azure, přečtěte si téma [Vytvoření virtuálního počítače s Linuxem z vlastního disku](upload-vhd.md#option-1-upload-a-vhd).

