---
title: Vytvoření a nahrání Oracle Linux VHD
description: Naučte se vytvořit a nahrát virtuální pevný disk Azure (VHD), který obsahuje Oracle Linux operační systém.
author: danielsollondon
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: 34ebc126ba4a1ae725325f8d888899af2dd72a59
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98875664"
---
# <a name="prepare-an-oracle-linux-virtual-machine-for-azure"></a>Příprava virtuálního počítače s Oracle Linux pro Azure

V tomto článku se předpokládá, že jste už Oracle Linux operační systém nainstalovali na virtuální pevný disk. Pro vytváření souborů. VHD, například virtualizačního řešení, jako je například Hyper-V, existuje více nástrojů. Pokyny najdete v tématu [instalace role Hyper-V a konfigurace virtuálního počítače](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

## <a name="oracle-linux-installation-notes"></a>Poznámky k instalaci Oracle Linux
* Další tipy k přípravě Linux pro Azure najdete v tématu [Obecné poznámky k instalaci pro Linux](create-upload-generic.md#general-linux-installation-notes) .
* Technologie Hyper-V a Azure se Oracle Linux s využitím jádra neUEKho podnikového jádra () nebo Red Hat kompatibilního s prostředím Red Hat.
* Technologie Hyper-V a Azure UEK2 se nepodporuje, protože neobsahují požadované ovladače.
* Formát VHDX není v Azure podporovaný, jenom **pevný virtuální pevný disk**.  Disk můžete převést na formát VHD pomocí Správce technologie Hyper-V nebo rutiny Convert-VHD.
* **Vyžaduje se podpora jádra pro připojení systémů souborů UDF.** Při prvním spuštění v Azure se konfigurace zřizování předává virtuálnímu počítači Linux přes médium ve formátu UDF, které je připojené k hostu. Agent Azure Linux musí být schopný připojit systém souborů UDF ke čtení konfigurace a zřídit virtuální počítač.
* Při instalaci systému Linux doporučujeme místo LVM použít standardní oddíly (často se jedná o výchozí nastavení pro mnoho instalací). Tím se vyhnete LVM názvům v konfliktu s klonovanými virtuálními počítači, zejména pokud se disk s operačním systémem někdy potřebuje připojit k jinému virtuálnímu počítači pro řešení potíží. [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) nebo [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) se můžou použít na datových discích, pokud jsou preferované.
* Verze jádra Linux starší než 2.6.37 nepodporují architekturu NUMA na technologii Hyper-V s většími velikostmi virtuálních počítačů. Tento problém se týká především starších distribucí pomocí nadřazeného jádra Red Hat 2.6.32 a byl opraven v Oracle Linux 6,6 a novějším.
* Nekonfigurujte odkládací oddíl na disku s operačním systémem. Další informace o tomto postupu najdete v následujících krocích.
* Všechny virtuální pevné disky v Azure musí mít virtuální velikost zarovnaná na 1 MB. Při převodu z nezpracovaného disku na virtuální pevný disk je nutné před převodem zajistit, aby velikost nezpracovaného disku byla násobkem 1 MB. Další informace najdete v [poznámkách k instalaci systému Linux](create-upload-generic.md#general-linux-installation-notes) .
* Ujistěte se, že `Addons` je úložiště povolené. Upravte soubor `/etc/yum.repos.d/public-yum-ol6.repo` (Oracle Linux 6) nebo `/etc/yum.repos.d/public-yum-ol7.repo` (Oracle Linux 7) a `enabled=0` v tomto souboru změňte řádek na `enabled=1` **[ol6_addons]** nebo **[ol7_addons]** .

## <a name="oracle-linux-64-and-later"></a>Oracle Linux 6,4 a novější
Aby se virtuální počítač spouštěl v Azure, musíte v operačním systému provést konkrétní konfigurační kroky.

1. V prostředním podokně Správce technologie Hyper-V vyberte virtuální počítač.
2. Kliknutím na **připojit** otevřete okno pro virtuální počítač.
3. Odinstalujte NetworkManager spuštěním následujícího příkazu:

    ```console
    # sudo rpm -e --nodeps NetworkManager
    ```

    **Poznámka:** Pokud balíček ještě není nainstalovaný, tento příkaz se nezdaří a zobrazí se chybová zpráva. To se očekává.
4. V adresáři vytvořte soubor  s názvem Network `/etc/sysconfig/` , který obsahuje následující text:

    ```config   
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

5. Vytvořte soubor s názvem **soubor ifcfg-eth0** v `/etc/sysconfig/network-scripts/` adresáři, který obsahuje následující text:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

6. Upravte pravidla udev, abyste se vyhnuli generování statických pravidel pro rozhraní sítě Ethernet. Tato pravidla můžou způsobit problémy při klonování virtuálního počítače v Microsoft Azure nebo Hyper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

7. Spuštěním následujícího příkazu zajistěte, aby se síťová služba spouštěla v době spuštění:

    ```console
    # chkconfig network on
    ```

8. Nainstalujte Python-pyasn1 spuštěním následujícího příkazu:

    ```console
    # sudo yum install python-pyasn1
    ```

9. Upravte spouštěcí řádek jádra v konfiguraci GRUB tak, aby zahrnoval další parametry jádra pro Azure. Uděláte to tak, že v textovém editoru otevřete "/boot/grub/menu.lst" a zajistěte, aby jádro zahrnovalo následující parametry:

    ```config-grub
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

   Tím se zajistí, že se všechny zprávy konzoly odešlou na první sériový port, což může pomoct podpoře Azure s problémy ladění.
   
   Kromě výše uvedeného se doporučuje *Odebrat* následující parametry:

    ```config-grub
    rhgb quiet crashkernel=auto
    ```

   Grafické a tiché spouštění nejsou užitečné v cloudovém prostředí, kde chceme, aby se všechny protokoly odesílaly na sériový port.
   
   `crashkernel`Možnost může být ponechána v případě potřeby nakonfigurovaná, ale Všimněte si, že tento parametr sníží velikost dostupné paměti ve virtuálním počítači o 128 MB nebo více, což může být problematické u menších velikostí virtuálních počítačů.
10. Ujistěte se, že je server SSH nainstalovaný a nakonfigurované tak, aby se spouštěl při spuštění.  Obvykle se jedná o výchozí nastavení.
11. Nainstalujte agenta Azure Linux spuštěním následujícího příkazu. Nejnovější verze je 2.0.15.

    ```console
    # sudo yum install WALinuxAgent
    ```

    Všimněte si, že při instalaci balíčku WALinuxAgent se odstraní balíčky NetworkManager a NetworkManager-GNOME, pokud už se neodebraly, jak je popsáno v kroku 2.
12. Nevytvářejte odkládací místo na disku s operačním systémem.
    
    Agent Azure Linux může automaticky nakonfigurovat odkládací prostor pomocí disku místního prostředku, který je připojen k virtuálnímu počítači po zřízení v Azure. Všimněte si, že místní disk prostředků je *dočasný* disk a při zrušení zřízení virtuálního počítače může dojít k jeho vyprázdnění. Po instalaci agenta Azure Linux (viz předchozí krok) upravte odpovídajícím způsobem následující parametry v/etc/waagent.conf:

    ```config-conf
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

13. Spuštěním následujících příkazů můžete virtuální počítač zrušit a připravit ho pro zřizování v Azure:

    ```console
    # sudo waagent -force -deprovision
    # export HISTSIZE=0
    # logout
    ```

14. Klikněte na **Akce – > vypnout** ve Správci technologie Hyper-V. Virtuální pevný disk se systémem Linux je teď připravený k nahrání do Azure.

---
## <a name="oracle-linux-70-and-later"></a>Oracle Linux 7,0 a novější
**Změny ve Oracle Linux 7**

Příprava virtuálního počítače s Oracle Linux 7 pro Azure je velmi podobná Oracle Linux 6, ale existuje několik důležitých rozdílů, které se zabývají:

* Azure podporuje Oracle Linux s jádrem neUEK (Enterprise kernel) nebo se systémem Red Hat, který je kompatibilní s Red Hat. Doporučuje se Oracle Linux se UEK.
* Balíček NetworkManager už není v konfliktu s agentem Azure Linux. Tento balíček se nainstaluje ve výchozím nastavení a doporučujeme, abyste ho neodebrali.
* GRUB2 se teď používá jako výchozí zaváděcí program pro spouštění, takže se změnil postup pro úpravu parametrů jádra (viz níže).
* XFS je teď výchozím systémem souborů. Systém souborů ext4 se může v případě potřeby i nadále použít.

**Postup konfigurace**

1. Ve Správci technologie Hyper-V vyberte virtuální počítač.
2. Kliknutím na **připojit** otevřete okno konzoly pro virtuální počítač.
3. V adresáři vytvořte soubor  s názvem Network `/etc/sysconfig/` , který obsahuje následující text:

    ```config
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

4. Vytvořte soubor s názvem **soubor ifcfg-eth0** v `/etc/sysconfig/network-scripts/` adresáři, který obsahuje následující text:

    ```config
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

5. Upravte pravidla udev, abyste se vyhnuli generování statických pravidel pro rozhraní sítě Ethernet. Tato pravidla můžou způsobit problémy při klonování virtuálního počítače v Microsoft Azure nebo Hyper-V:

    ```console
    # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    ```

6. Spuštěním následujícího příkazu zajistěte, aby se síťová služba spouštěla v době spuštění:

    ```console
    # sudo chkconfig network on
    ```

7. Nainstalujte balíček python-pyasn1 spuštěním následujícího příkazu:

    ```console
    # sudo yum install python-pyasn1
    ```

8. Spusťte následující příkaz, který vymaže aktuální metadata Yumu a nainstaluje aktualizace:

    ```console 
    # sudo yum clean all
    # sudo yum -y update
    ```

9. Upravte spouštěcí řádek jádra v konfiguraci GRUB tak, aby zahrnoval další parametry jádra pro Azure. Uděláte to tak, že v textovém editoru otevřete "/etc/default/GRUB" a upravíte `GRUB_CMDLINE_LINUX` parametr, například:

    ```config-grub
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Tím se také zajistí, že se všechny zprávy konzoly odešlou na první sériový port, což může pomoct podpoře Azure s problémy ladění. Také vypne konvence pojmenování pro síťové karty v Oracle Linux 7 pomocí nepřerušeného podnikového jádra. Kromě výše uvedeného se doporučuje *Odebrat* následující parametry:

    ```config-grub
       rhgb quiet crashkernel=auto
    ```
 
   Grafické a tiché spouštění nejsou užitečné v cloudovém prostředí, kde chceme, aby se všechny protokoly odesílaly na sériový port.
   
   `crashkernel`Možnost může být ponechána v případě potřeby nakonfigurovaná, ale Všimněte si, že tento parametr sníží velikost dostupné paměti ve virtuálním počítači o 128 MB nebo více, což může být problematické u menších velikostí virtuálních počítačů.
10. Až budete hotovi s úpravou "/etc/default/GRUB", spusťte následující příkaz, který znovu sestaví konfiguraci grub:

    ```console
    # sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

11. Ujistěte se, že je server SSH nainstalovaný a nakonfigurované tak, aby se spouštěl při spuštění.  Obvykle se jedná o výchozí nastavení.
12. Nainstalujte agenta a závislosti pro Azure Linux:

    ```bash
    sudo yum install WALinuxAgent
    sudo systemctl enable waagent
    ```

13. Instalace Cloud-init pro zpracování zřizování

    ```console
    yum install -y cloud-init cloud-utils-growpart gdisk hyperv-daemons

    # Configure waagent for cloud-init
    sed -i 's/Provisioning.UseCloudInit=n/Provisioning.UseCloudInit=y/g' /etc/waagent.conf
    sed -i 's/Provisioning.Enabled=y/Provisioning.Enabled=n/g' /etc/waagent.conf


    echo "Adding mounts and disk_setup to init stage"
    sed -i '/ - mounts/d' /etc/cloud/cloud.cfg
    sed -i '/ - disk_setup/d' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - mounts' /etc/cloud/cloud.cfg
    sed -i '/cloud_init_modules/a\\ - disk_setup' /etc/cloud/cloud.cfg

    echo "Allow only Azure datasource, disable fetching network setting via IMDS"
    cat > /etc/cloud/cloud.cfg.d/91-azure_datasource.cfg <<EOF
    datasource_list: [ Azure ]
    datasource:
    Azure:
        apply_network_config: False
    EOF

    if [[ -f /mnt/resource/swapfile ]]; then
    echo Removing swapfile - RHEL uses a swapfile by default
    swapoff /mnt/resource/swapfile
    rm /mnt/resource/swapfile -f
    fi

    echo "Add console log file"
    cat >> /etc/cloud/cloud.cfg.d/05_logging.cfg <<EOF

    # This tells cloud-init to redirect its stdout and stderr to
    # 'tee -a /var/log/cloud-init-output.log' so the user can see output
    # there without needing to look on the console.
    output: {all: '| tee -a /var/log/cloud-init-output.log'}
    EOF

    ```

14. Přepnutí konfigurace nevytváří na disku s operačním systémem odkládací místo.

    Dříve se agent Azure Linux použil k automatické konfiguraci odkládacího prostoru pomocí disku místního prostředku, který je připojený k virtuálnímu počítači po zřízení virtuálního počítače v Azure. To je ale teď zpracovávané pomocí Cloud-init. k naformátování disku prostředků pro vytvoření odkládacího souboru **nemusíte** použít agenta pro Linux. Upravte následující parametry `/etc/waagent.conf` správným způsobem:

    ```console
    sed -i 's/ResourceDisk.Format=y/ResourceDisk.Format=n/g' /etc/waagent.conf
    sed -i 's/ResourceDisk.EnableSwap=y/ResourceDisk.EnableSwap=n/g' /etc/waagent.conf
    ```

    Pokud chcete připojit, formátovat a vytvořit prohození, můžete buď:
    * Při každém vytvoření virtuálního počítače ho předejte jako Cloud-init config.
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
    # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
    # skip the deprovision step
    # sudo rm -rf /var/lib/waagent/
    # sudo rm -f /var/log/waagent.log

    # waagent -force -deprovision+user
    # rm -f ~/.bash_history
    

    # export HISTSIZE=0

    # logout
    ```

16. Klikněte na **Akce – > vypnout** ve Správci technologie Hyper-V. Virtuální pevný disk se systémem Linux je teď připravený k nahrání do Azure.

## <a name="next-steps"></a>Další kroky
Nyní jste připraveni k vytváření nových virtuálních počítačů v Azure pomocí Oracle Linux. VHD. Pokud soubor. VHD do Azure nahráváte poprvé, přečtěte si článek [Vytvoření virtuálního počítače se systémem Linux z vlastního disku](upload-vhd.md#option-1-upload-a-vhd).