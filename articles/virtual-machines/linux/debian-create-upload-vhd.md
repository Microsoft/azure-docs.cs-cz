---
title: Příprava virtuálního pevného disku s Debian Linux
description: Naučte se vytvářet Debian image VHD pro nasazení virtuálních počítačů v Azure.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 11/13/2018
ms.author: guybo
ms.openlocfilehash: 80272896bd314a1f5f05094afa83568e077ab480
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87368196"
---
# <a name="prepare-a-debian-vhd-for-azure"></a>Příprava virtuálního pevného disku Debian pro Azure
## <a name="prerequisites"></a>Požadavky
V této části se předpokládá, že jste už nainstalovali operační systém Debian Linux ze souboru. ISO staženého z [webu Debian](https://www.debian.org/distrib/) na virtuální pevný disk. Pro vytváření souborů. VHD existuje víc nástrojů. Hyper-V je jenom jeden příklad. Pokyny k používání technologie Hyper-V najdete v tématu [instalace role Hyper-v a konfigurace virtuálního počítače](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

## <a name="installation-notes"></a>Poznámky k instalaci
* Další tipy k přípravě Linux pro Azure najdete v tématu [Obecné poznámky k instalaci pro Linux](create-upload-generic.md#general-linux-installation-notes) .
* Novější formát VHDX se v Azure nepodporuje. Disk můžete převést na formát VHD pomocí Správce technologie Hyper-V nebo rutiny **Convert-VHD** .
* Při instalaci systému Linux doporučujeme místo LVM použít standardní oddíly (často se jedná o výchozí nastavení pro mnoho instalací). Tím se vyhnete LVM názvům v konfliktu s klonovanými virtuálními počítači, zejména pokud se disk s operačním systémem někdy potřebuje připojit k jinému virtuálnímu počítači pro řešení potíží. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) se můžou použít na datových discích, pokud jsou preferované.
* Nekonfigurujte odkládací oddíl na disku s operačním systémem. Agent Azure Linux se dá nakonfigurovat tak, aby na dočasném disku prostředků vytvořil odkládací soubor. Další informace najdete v následujících krocích.
* Všechny virtuální pevné disky v Azure musí mít virtuální velikost zarovnaná na 1 MB. Při převodu z nezpracovaného disku na virtuální pevný disk je nutné před převodem zajistit, aby velikost nezpracovaného disku byla násobkem 1 MB. Další informace najdete v [poznámkách k instalaci systému Linux](create-upload-generic.md#general-linux-installation-notes).

## <a name="use-azure-manage-to-create-debian-vhds"></a>Vytvoření VHD Debian pomocí Azure-Manage
K dispozici jsou nástroje pro generování Debian VHD pro Azure, například skripty [Azure-Manage](https://github.com/credativ/azure-manage) z [credativ](https://www.credativ.com/). Toto je doporučený přístup a vytvoření obrázku od začátku. Pokud třeba chcete vytvořit virtuální pevný disk Debian 8, spusťte následující příkazy ke stažení `azure-manage` nástroje (a závislostí) a spusťte tento `azure_build_image` skript:

```console
# sudo apt-get update
# sudo apt-get install git qemu-utils mbr kpartx debootstrap

# sudo apt-get install python3-pip python3-dateutil python3-cryptography
# sudo pip3 install azure-storage azure-servicemanagement-legacy azure-common pytest pyyaml
# git clone https://github.com/credativ/azure-manage.git
# cd azure-manage
# sudo pip3 install .

# sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section
```


## <a name="manually-prepare-a-debian-vhd"></a>Ruční Příprava virtuálního pevného disku Debian
1. Ve Správci technologie Hyper-V vyberte virtuální počítač.
2. Kliknutím na **připojit** otevřete okno konzoly pro virtuální počítač.
3. Pokud jste operační systém nainstalovali s použitím ISO, pak zakomentujte všechny řádky týkající se " `deb cdrom` " v tématu `/etc/apt/source.list` .

4. Upravte `/etc/default/grub` soubor a upravte parametr **GRUB_CMDLINE_LINUX** následujícím způsobem, aby se zahrnuly další parametry jádra pro Azure.

    ```config-grub
    GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200n8 earlyprintk=ttyS0,115200"
    ```

5. Znovu sestavte grub a spusťte:

    ```console
    # sudo update-grub
    ```

6. Přidejte Debian úložiště Azure do/etc/apt/sources.list pro Debian 8 nebo 9:

    **Debian 8. x "Jessie"**

    ```config-grub
    deb http://debian-archive.trafficmanager.net/debian jessie main
    deb-src http://debian-archive.trafficmanager.net/debian jessie main
    deb http://debian-archive.trafficmanager.net/debian-security jessie/updates main
    deb-src http://debian-archive.trafficmanager.net/debian-security jessie/updates
    deb http://debian-archive.trafficmanager.net/debian jessie-updates main
    deb-src http://debian-archive.trafficmanager.net/debian jessie-updates main
    deb http://debian-archive.trafficmanager.net/debian jessie-backports main
    deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main
    ```

    **Debian 9. x "Stretch"**

    ```config-grub
    deb http://debian-archive.trafficmanager.net/debian stretch main
    deb-src http://debian-archive.trafficmanager.net/debian stretch main
    deb http://debian-archive.trafficmanager.net/debian-security stretch/updates main
    deb-src http://debian-archive.trafficmanager.net/debian-security stretch/updates main
    deb http://debian-archive.trafficmanager.net/debian stretch-updates main
    deb-src http://debian-archive.trafficmanager.net/debian stretch-updates main
    deb http://debian-archive.trafficmanager.net/debian stretch-backports main
    deb-src http://debian-archive.trafficmanager.net/debian stretch-backports main
    ```


7. Instalace agenta Azure Linux:

    ```console
    # sudo apt-get update
    # sudo apt-get install waagent
    ```

8. Pro Debian 9 + se doporučuje používat pro použití s virtuálními počítači v Azure nové jádro cloudu Debian. Chcete-li nainstalovat toto nové jádro, nejprve vytvořte soubor s názvem/etc/apt/Preferences.d/Linux.PREF s následujícím obsahem:

    ```config-pref
    Package: linux-* initramfs-tools
    Pin: release n=stretch-backports
    Pin-Priority: 500
    ```

    Pak spuštěním příkazu "sudo apt-get install linux-image-Cloud-amd64" nainstalujte novou Debian cloudové jádro.

9. Zrušení zřízení virtuálního počítače a jeho příprava pro zřizování v Azure a spuštění:

    ```console
    # sudo waagent –force -deprovision
    # export HISTSIZE=0
    # logout
    ```

10. Klikněte na **Akce** – > vypnout ve Správci technologie Hyper-V. Virtuální pevný disk se systémem Linux je teď připravený k nahrání do Azure.

## <a name="next-steps"></a>Další kroky
Nyní jste připraveni k vytváření nových virtuálních počítačů v Azure pomocí Debian virtuálního pevného disku. Pokud soubor. VHD do Azure nahráváte poprvé, přečtěte si článek [Vytvoření virtuálního počítače se systémem Linux z vlastního disku](upload-vhd.md#option-1-upload-a-vhd).
