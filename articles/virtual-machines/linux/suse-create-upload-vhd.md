---
title: Vytvoření a nahrání virtuálního pevného disku s SUSE Linux v Azure
description: Naučte se vytvořit a nahrát virtuální pevný disk Azure (VHD), který obsahuje operační systém SUSE Linux.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: 276f5f4542ecea42c665764b8c4e5f66f2531126
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102552707"
---
# <a name="prepare-a-sles-or-opensuse-virtual-machine-for-azure"></a>Příprava virtuálního počítače se SLES nebo openSUSE pro Azure


V tomto článku se předpokládá, že jste už nainstalovali operační systém SUSE nebo openSUSE Linux na virtuální pevný disk. Pro vytváření souborů. VHD, například virtualizačního řešení, jako je například Hyper-V, existuje více nástrojů. Pokyny najdete v tématu [instalace role Hyper-V a konfigurace virtuálního počítače](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

## <a name="sles--opensuse-installation-notes"></a>Poznámky k instalaci SLES/openSUSE
* Další tipy k přípravě Linux pro Azure najdete v tématu [Obecné poznámky k instalaci pro Linux](create-upload-generic.md#general-linux-installation-notes) .
* Formát VHDX není v Azure podporovaný, jenom **pevný virtuální pevný disk**.  Disk můžete převést na formát VHD pomocí Správce technologie Hyper-V nebo rutiny Convert-VHD.
* Při instalaci systému Linux doporučujeme místo LVM použít standardní oddíly (často se jedná o výchozí nastavení pro mnoho instalací). Tím se vyhnete LVM názvům v konfliktu s klonovanými virtuálními počítači, zejména pokud se disk s operačním systémem někdy potřebuje připojit k jinému virtuálnímu počítači pro řešení potíží. [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) nebo [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) se můžou použít na datových discích, pokud jsou preferované.
* Nekonfigurujte odkládací oddíl na disku s operačním systémem. Agent pro Linux se dá nakonfigurovat tak, aby na dočasném disku prostředků vytvořil odkládací soubor.  Další informace o tomto postupu najdete v následujících krocích.
* Všechny virtuální pevné disky v Azure musí mít virtuální velikost zarovnaná na 1 MB. Při převodu z nezpracovaného disku na virtuální pevný disk je nutné před převodem zajistit, aby velikost nezpracovaného disku byla násobkem 1 MB. Další informace najdete v [poznámkách k instalaci systému Linux](create-upload-generic.md#general-linux-installation-notes) .

## <a name="use-suse-studio"></a>Použití SUSE studia
[SUSE Studio](https://studioexpress.opensuse.org/) může snadno vytvářet a spravovat vaše SLES a openSUSE image pro Azure a Hyper-V. Toto je doporučený postup pro přizpůsobení vlastních imagí SLES a openSUSE.

Jako alternativu k vytváření vlastního virtuálního pevného disku SUSE také publikuje image BYOS (Přineste si vlastní odběr) pro SLES ve [skladu](https://www.microsoft.com/research/wp-content/uploads/2016/04/using-and-contributing-vms-to-vm-depot.pdf).

## <a name="prepare-suse-linux-enterprise-server-for-azure"></a>Příprava SUSE Linux Enterprise Server pro Azure
1. V prostředním podokně Správce technologie Hyper-V vyberte virtuální počítač.
2. Kliknutím na **připojit** otevřete okno pro virtuální počítač.
3. Zaregistrujte systém SUSE Linux Enterprise, aby mohl stahovat aktualizace a instalovat balíčky.
4. Aktualizujte systém pomocí nejnovějších oprav:

    ```console
    # sudo zypper update
    ```
    
5. Nainstalovat agenta Azure Linux a Cloud-init

    ```console
    # SUSEConnect -p sle-module-public-cloud/15.2/x86_64  (SLES 15 SP2)
    # sudo zypper refresh
    # sudo zypper install python-azure-agent
    # sudo zypper install cloud-init
    ```

6. Povolit waagent & Cloud-init spustit při spuštění

    ```console
    # sudo chkconfig waagent on
    # systemctl enable cloud-init-local.service
    # systemctl enable cloud-init.service
    # systemctl enable cloud-config.service
    # systemctl enable cloud-final.service
    # systemctl daemon-reload
    # cloud-init clean
    ```

7. Aktualizace waagent a konfigurace Cloud-init

    ```console
    # sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    # sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf

    # sudo sh -c 'printf "datasource:\n  Azure:" > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg'
    # sudo sh -c 'printf "reporting:\n  logging:\n    type: log\n  telemetry:\n    type: hyperv" > /etc/cloud/cloud.cfg.d/10-azure-kvp.cfg'
    ```

8. Úpravou souboru/etc/default/grub zajistěte, aby byly protokoly konzoly odesílány do sériového portu, a pak aktualizujte hlavní konfigurační soubor pomocí grub2-mkconfig-o/Boot/grub2/grub.cfg

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```
    Tím se zajistí, že se všechny zprávy konzoly odešlou na první sériový port, což může pomoct podpoře Azure s problémy ladění.
    
9. Zajistěte, aby soubor/etc/fstab odkazoval na disk pomocí jeho UUID (by UUID).
         
10. Upravte pravidla udev, abyste se vyhnuli generování statických pravidel pro rozhraní sítě Ethernet. Tato pravidla můžou způsobit problémy při klonování virtuálního počítače v Microsoft Azure nebo Hyper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```
   
11. Doporučuje se upravit soubor "/etc/sysconfig/Network/DHCP" a změnit `DHCLIENT_SET_HOSTNAME` parametr na následující:

    ```config
    DHCLIENT_SET_HOSTNAME="no"
    ```

12. V části "/etc/sudoers", odkomentujte nebo odeberte následující řádky, pokud existují:

    ```text
    Defaults targetpw   # ask for the password of the target user i.e. root
    ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
    ```

13. Ujistěte se, že je server SSH nainstalovaný a nakonfigurované tak, aby se spouštěl při spuštění. Obvykle se jedná o výchozí nastavení.

14. Prohodit konfiguraci
 
    Nevytvářejte odkládací místo na disku s operačním systémem.

    Dříve se agent Azure Linux použil k automatické konfiguraci odkládacího prostoru pomocí disku místního prostředku, který je připojený k virtuálnímu počítači po zřízení virtuálního počítače v Azure. To je ale teď zpracovávané pomocí Cloud-init. k naformátování disku prostředků pro vytvoření odkládacího souboru **nemusíte** použít agenta pro Linux. Upravte následující parametry `/etc/waagent.conf` správným způsobem:

    ```console
    # sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    # sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```

    Pokud chcete připojit, formátovat a vytvořit prohození, můžete buď:
    * Předejte to ve formě Cloud-init config při každém vytvoření virtuálního počítače.
    * Pomocí direktivy Cloud-init vloženými do image, která to provede při každém vytvoření virtuálního počítače:

        ```console
        cat > /etc/cloud/cloud.cfg.d/00-azure-swap.cfg << EOF
        #cloud-config
        # Generated by Azure cloud image build
        disk_setup:
          ephemeral0:
            table_type: mbr
            layout: [66, [33, 82]]
            overwrite: True
        fs_setup:
          - device: ephemeral0.1
            filesystem: ext4
          - device: ephemeral0.2
            filesystem: swap
        mounts:
          - ["ephemeral0.1", "/mnt"]
          - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
        EOF
        ```

15. Spuštěním následujících příkazů můžete virtuální počítač zrušit a připravit ho pro zřizování v Azure:

    ```console
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log

    # waagent -force -deprovision+user
    # rm -f ~/.bash_history
    

    # export HISTSIZE=0

    # logout
    ```
16. Klikněte na **Akce – > vypnout** ve Správci technologie Hyper-V. Virtuální pevný disk se systémem Linux je teď připravený k nahrání do Azure.

---
## <a name="prepare-opensuse-131"></a>Příprava openSUSE 13.1 +
1. V prostředním podokně Správce technologie Hyper-V vyberte virtuální počítač.
2. Kliknutím na **připojit** otevřete okno pro virtuální počítač.
3. V prostředí spusťte příkaz `zypper lr` . Pokud tento příkaz vrátí výstup podobný následujícímu, pak jsou úložiště konfigurována podle očekávání – nejsou nutné žádné úpravy (Všimněte si, že čísla verzí se mohou lišit):

   | # | Alias                 | Name                  | Povoleno | Aktualizovat
   | - | :-------------------- | :-------------------- | :------ | :------
   | 1 | Cloud: Tools_13.1      | Cloud: Tools_13.1      | Yes     | Yes
   | 2 | openSUSE_13 openSUSE_13.1_OSS     | openSUSE_13 openSUSE_13.1_OSS     | Yes     | Yes
   | 3 | openSUSE_13 openSUSE_13.1_Updates | openSUSE_13 openSUSE_13.1_Updates | Yes     | Yes

    Pokud příkaz vrátí "žádná úložiště nebyla definována..." pak pomocí následujících příkazů přidejte tato úložišť:

    ```console
    # sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
    # sudo zypper ar -f https://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
    # sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates
    ```

    Pak můžete ověřit, že úložiště byla přidána, spuštěním příkazu ' `zypper lr` '. V případě, že některá z příslušných úložišť aktualizací není povolená, povolte ji pomocí následujícího příkazu:

    ```console
    # sudo zypper mr -e [NUMBER OF REPOSITORY]
    ```

4. Aktualizujte jádro na nejnovější dostupnou verzi:

    ```console
    # sudo zypper up kernel-default
    ```

    Nebo pokud chcete aktualizovat systém se všemi nejnovějšími opravami:

    ```console
    # sudo zypper update
    ```

5. Nainstalujte agenta Azure Linux.

    ```console
    # sudo zypper install WALinuxAgent
    ```

6. Upravte spouštěcí řádek jádra v konfiguraci GRUB tak, aby zahrnoval další parametry jádra pro Azure. Provedete to tak, že v textovém editoru otevřete "/boot/grub/menu.lst" a zajistěte, aby výchozí jádro zahrnovalo následující parametry:

    ```config-grub
     console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

   Tím se zajistí, že se všechny zprávy konzoly odešlou na první sériový port, což může pomoct podpoře Azure s problémy ladění. Kromě toho odeberte následující parametry z spouštěcího řádku jádra, pokud existují:

    ```config-grub
     libata.atapi_enabled=0 reserve=0x1f0,0x8
    ```

7. Doporučuje se upravit soubor "/etc/sysconfig/Network/DHCP" a změnit `DHCLIENT_SET_HOSTNAME` parametr na následující:

    ```config
     DHCLIENT_SET_HOSTNAME="no"
    ```

8. **Důležité informace:** V části "/etc/sudoers", odkomentujte nebo odeberte následující řádky, pokud existují:

    ```text
    Defaults targetpw   # ask for the password of the target user i.e. root
    ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!
    ```

9. Ujistěte se, že je server SSH nainstalovaný a nakonfigurované tak, aby se spouštěl při spuštění.  Obvykle se jedná o výchozí nastavení.
10. Nevytvářejte odkládací místo na disku s operačním systémem.

    Agent Azure Linux může automaticky nakonfigurovat odkládací prostor pomocí disku místního prostředku, který je připojen k virtuálnímu počítači po zřízení v Azure. Všimněte si, že místní disk prostředků je *dočasný* disk a při zrušení zřízení virtuálního počítače může dojít k jeho vyprázdnění. Po instalaci agenta Azure Linux (viz předchozí krok) upravte odpovídajícím způsobem následující parametry v/etc/waagent.conf:

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

11. Spuštěním následujících příkazů můžete virtuální počítač zrušit a připravit ho pro zřizování v Azure:

    ```console
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```

12. Zajistěte, aby byl agent Azure Linux spuštěn při spuštění:

    ```console
    # sudo systemctl enable waagent.service
    ```

13. Klikněte na **Akce – > vypnout** ve Správci technologie Hyper-V. Virtuální pevný disk se systémem Linux je teď připravený k nahrání do Azure.

## <a name="next-steps"></a>Další kroky
Nyní jste připraveni k vytváření nových virtuálních počítačů v Azure pomocí virtuálního pevného disku s SUSE Linux. Pokud soubor. VHD do Azure nahráváte poprvé, přečtěte si článek [Vytvoření virtuálního počítače se systémem Linux z vlastního disku](upload-vhd.md#option-1-upload-a-vhd).