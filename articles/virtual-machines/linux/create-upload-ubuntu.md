---
title: Vytvoření a nahrání Ubuntu Linux VHD v Azure
description: Naučte se vytvořit a nahrát virtuální pevný disk Azure (VHD), který obsahuje Ubuntu Linux operační systém.
author: danielsollondon
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 06/06/2020
ms.author: danis
ms.openlocfilehash: 92ceecd16a428593764fe5ab6478cc4ea7ab91d7
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102554611"
---
# <a name="prepare-an-ubuntu-virtual-machine-for-azure"></a>Příprava virtuálního počítače s Ubuntu pro Azure


Ubuntu nyní zveřejňuje oficiální virtuální pevné disky Azure ke stažení na adrese [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) . Pokud pro Azure potřebujete vytvořit vlastní specializovanou image Ubuntu, místo ručního použití níže uvedeného postupu doporučujeme začít s těmito známými pracovními virtuálními počítači a přizpůsobit podle potřeby. Nejnovější verze imagí lze vždy nalézt v následujících umístěních:

* Ubuntu 16.04/Xenial: [Ubuntu-16,04-Server-cloudimg-amd64-Disk1. vmdk](https://cloud-images.ubuntu.com/releases/xenial/release/ubuntu-16.04-server-cloudimg-amd64-disk1.vmdk)
* Ubuntu 18.04/Bionic: [Bionic-Server-cloudimg-amd64. vmdk](https://cloud-images.ubuntu.com/bionic/current/bionic-server-cloudimg-amd64.vmdk)

## <a name="prerequisites"></a>Požadavky
V tomto článku se předpokládá, že jste už Ubuntu Linux operační systém nainstalovali na virtuální pevný disk. Pro vytváření souborů. VHD, například virtualizačního řešení, jako je například Hyper-V, existuje více nástrojů. Pokyny najdete v tématu [instalace role Hyper-V a konfigurace virtuálního počítače](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

**Poznámky k instalaci Ubuntu**

* Další tipy k přípravě Linux pro Azure najdete v tématu [Obecné poznámky k instalaci pro Linux](create-upload-generic.md#general-linux-installation-notes) .
* Formát VHDX není v Azure podporovaný, jenom **pevný virtuální pevný disk**.  Disk můžete převést na formát VHD pomocí Správce technologie Hyper-V nebo `Convert-VHD` rutiny.
* Při instalaci systému Linux doporučujeme místo LVM použít standardní oddíly (často se jedná o výchozí nastavení pro mnoho instalací). Tím se vyhnete LVM názvům v konfliktu s klonovanými virtuálními počítači, zejména pokud se disk s operačním systémem někdy potřebuje připojit k jinému virtuálnímu počítači pro řešení potíží. [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) nebo [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) se můžou použít na datových discích, pokud jsou preferované.
* Nekonfigurujte na disku s operačním systémem odkládací oddíl ani swapfile. Agent zřizování Cloud-init se dá nakonfigurovat tak, aby vytvořil stránkovací soubor nebo odkládací oddíl na dočasném disku s prostředky. Další informace o tomto postupu najdete v následujících krocích.
* Všechny virtuální pevné disky v Azure musí mít virtuální velikost zarovnaná na 1 MB. Při převodu z nezpracovaného disku na virtuální pevný disk je nutné před převodem zajistit, aby velikost nezpracovaného disku byla násobkem 1 MB. Další informace najdete v [poznámkách k instalaci systému Linux](create-upload-generic.md#general-linux-installation-notes) .

## <a name="manual-steps"></a>Ruční kroky
> [!NOTE]
> Před pokusem o vytvoření vlastní image Ubuntu pro Azure zvažte místo toho použití předem připravených a testovaných imagí [https://cloud-images.ubuntu.com/](https://cloud-images.ubuntu.com/) .
> 
> 

1. V prostředním podokně Správce technologie Hyper-V vyberte virtuální počítač.

2. Kliknutím na **připojit** otevřete okno pro virtuální počítač.

3. Nahraďte aktuální úložiště v imagi a použijte úložiště Azure Ubuntu.

    Před úpravou `/etc/apt/sources.list` doporučujeme vytvořit zálohu:

    ```console
    # sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
    ```

    Ubuntu 16,04 a Ubuntu 18,04:

    ```console
    # sudo sed -i 's/http:\/\/archive\.ubuntu\.com\/ubuntu\//http:\/\/azure\.archive\.ubuntu\.com\/ubuntu\//g' /etc/apt/sources.list
    # sudo sed -i 's/http:\/\/[a-z][a-z]\.archive\.ubuntu\.com\/ubuntu\//http:\/\/azure\.archive\.ubuntu\.com\/ubuntu\//g' /etc/apt/sources.list
    # sudo apt-get update
    ```

4. Image Azure Ubuntu teď používají [jádro Azure](https://ubuntu.com/blog/microsoft-and-canonical-increase-velocity-with-azure-tailored-kernel), které se přizpůsobí. Aktualizujte operační systém na nejnovější jádro s upraveným prostředím Azure a nainstalujte nástroje Azure Linux (včetně závislostí technologie Hyper-V) spuštěním následujících příkazů:

    Ubuntu 16,04 a Ubuntu 18,04:

    ```console
    # sudo apt update
    # sudo apt install linux-azure linux-image-azure linux-headers-azure linux-tools-common linux-cloud-tools-common linux-tools-azure linux-cloud-tools-azure
    (recommended) # sudo apt full-upgrade

    # sudo reboot
    ```

5. Upravte spouštěcí řádek jádra pro GRUB tak, aby zahrnoval další parametry jádra pro Azure. Pokud to chcete otevřít `/etc/default/grub` v textovém editoru, najděte proměnnou s názvem `GRUB_CMDLINE_LINUX_DEFAULT` (nebo ji přidejte v případě potřeby) a upravte ji tak, aby zahrnovala následující parametry:

    ```text
    GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300 quiet splash"
    ```

    Uložte a zavřete tento soubor a potom spusťte příkaz `sudo update-grub` . Tím se zajistí, že se všechny zprávy konzoly odešlou na první sériový port, což může pomoct technickou podporu Azure s problémy ladění.

6. Ujistěte se, že je server SSH nainstalovaný a nakonfigurované tak, aby se spouštěl při spuštění.  Obvykle se jedná o výchozí nastavení.

7. Nainstalujte Cloud-init (zřizovací Agent) a agenta Azure Linux (obslužná rutina rozšíření hosta). Cloud-init používá netplan ke konfiguraci systémové konfigurace sítě během zřizování a při každém následném spuštění.

    ```console
    # sudo apt update
    # sudo apt install cloud-init netplan.io walinuxagent && systemctl stop walinuxagent
    ```

   > [!Note]
   >  `walinuxagent`Balíček může odebrat `NetworkManager` `NetworkManager-gnome` balíčky a, pokud jsou nainstalované.

8. Odeberte výchozí konfigurace Cloud-init a zbylé netplan artefakty, které můžou být v konfliktu s zřizováním Cloud-init v Azure:

    ```console
    # rm -f /etc/cloud/cloud.cfg.d/50-curtin-networking.cfg /etc/cloud/cloud.cfg.d/curtin-preserve-sources.cfg
    # rm -f /etc/cloud/ds-identify.cfg
    # rm -f /etc/netplan/*.yaml
    ```

9. Konfigurace Cloud-init pro zřízení systému pomocí zdroje dat Azure:

    ```console
    # cat > /etc/cloud/cloud.cfg.d/90_dpkg.cfg << EOF
    datasource_list: [ Azure ]
    EOF

    # cat > /etc/cloud/cloud.cfg.d/90-azure.cfg << EOF
    system_info:
       package_mirrors:
         - arches: [i386, amd64]
           failsafe:
             primary: http://archive.ubuntu.com/ubuntu
             security: http://security.ubuntu.com/ubuntu
           search:
             primary:
               - http://azure.archive.ubuntu.com/ubuntu/
             security: []
         - arches: [armhf, armel, default]
           failsafe:
             primary: http://ports.ubuntu.com/ubuntu-ports
             security: http://ports.ubuntu.com/ubuntu-ports
    EOF

    # cat > /etc/cloud/cloud.cfg.d/10-azure-kvp.cfg << EOF
    reporting:
      logging:
        type: log
      telemetry:
        type: hyperv
    EOF
    ```

10. Nakonfigurujte agenta Azure Linux tak, aby se při zřizování spoléhal na Cloud-init. Další informace o těchto možnostech najdete v [projektu WALinuxAgent](https://github.com/Azure/WALinuxAgent) .

    ```console
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf

    cat >> /etc/waagent.conf << EOF
    # For Azure Linux agent version >= 2.2.45, this is the option to configure,
    # enable, or disable the provisioning behavior of the Linux agent.
    # Accepted values are auto (default), waagent, cloud-init, or disabled.
    # A value of auto means that the agent will rely on cloud-init to handle
    # provisioning if it is installed and enabled, which in this case it will.
    Provisioning.Agent=auto
    EOF
    ```

11. Vyčištění artefaktů a protokolů runtime pro Cloud-init a Azure Linux agentů:

    ```console
    # sudo cloud-init clean --logs --seed
    # sudo rm -rf /var/lib/cloud/
    # sudo systemctl stop walinuxagent.service
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log
    ```

12. Spuštěním následujících příkazů můžete virtuální počítač zrušit a připravit ho pro zřizování v Azure:

    > [!NOTE]
    > `sudo waagent -force -deprovision+user`Příkaz se pokusí vyčistit systém a nastavit jej jako vhodný pro opětovné zřízení. `+user`Možnost odstraní naposledy zřízený uživatelský účet a přidružená data.

    > [!WARNING]
    > Zrušení zřízení pomocí příkazu výše nezaručuje, že se image vymaže ze všech citlivých informací a je vhodná pro redistribuci.

    ```console
    # sudo waagent -force -deprovision+user
    # rm -f ~/.bash_history
    # export HISTSIZE=0
    # logout
    ```

13. Klikněte na **Akce – > vypnout** ve Správci technologie Hyper-V.

14. Azure přijímá jenom virtuální pevné disky s pevnou velikostí. Pokud disk s operačním systémem virtuálního počítače není VHD s pevnou velikostí, použijte `Convert-VHD` rutinu prostředí PowerShell a určete `-VHDType Fixed` možnost. Podívejte se prosím na dokumentaci `Convert-VHD` zde: [Convert-VHD](/powershell/module/hyper-v/convert-vhd).


## <a name="next-steps"></a>Další kroky
Nyní jste připraveni k vytváření nových virtuálních počítačů v Azure pomocí svého Ubuntu Linuxho virtuálního pevného disku. Pokud soubor. VHD do Azure nahráváte poprvé, přečtěte si článek [Vytvoření virtuálního počítače se systémem Linux z vlastního disku](upload-vhd.md#option-1-upload-a-vhd).