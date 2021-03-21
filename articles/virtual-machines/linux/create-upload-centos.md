---
title: Vytvoření a nahrání virtuálního pevného disku se systémem Linux CentOS
description: Naučte se vytvořit a nahrát virtuální pevný disk Azure (VHD), který obsahuje operační systém Linux založený na CentOS.
author: danielsollondon
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 12/01/2020
ms.author: danis
ms.openlocfilehash: 4745e631bd92675f8dd1ef0d390baa88f7666552
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554662"
---
# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>Příprava virtuálního počítače založeného na CentOS pro Azure

Naučte se vytvořit a nahrát virtuální pevný disk Azure (VHD), který obsahuje operační systém Linux založený na CentOS.

* [Příprava virtuálního počítače s CentOS 6. x pro Azure](#centos-6x)
* [Příprava virtuálního počítače s CentOS 7.0 pro Azure](#centos-70)


## <a name="prerequisites"></a>Předpoklady

V tomto článku se předpokládá, že jste už nainstalovali CentOS (nebo podobný odvozený) operační systém Linux na virtuální pevný disk. Pro vytváření souborů. VHD, například virtualizačního řešení, jako je například Hyper-V, existuje více nástrojů. Pokyny najdete v tématu [instalace role Hyper-V a konfigurace virtuálního počítače](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

**Poznámky k instalaci CentOS**

* Další tipy k přípravě Linux pro Azure najdete v tématu [Obecné poznámky k instalaci pro Linux](create-upload-generic.md#general-linux-installation-notes) .
* Formát VHDX není v Azure podporovaný, jenom **pevný virtuální pevný disk**.  Disk můžete převést na formát VHD pomocí Správce technologie Hyper-V nebo rutiny Convert-VHD. Pokud používáte VirtualBox, znamená to, že při vytváření disku vyberete **pevnou velikost** na rozdíl od výchozího dynamicky přiděleného disku.
* Při instalaci systému Linux *doporučujeme* místo LVM použít standardní oddíly (často se jedná o výchozí nastavení pro mnoho instalací). Tím se vyhnete konfliktům LVM názvů s klonovanými virtuálními počítači, zejména pokud se disk s operačním systémem někdy potřebuje připojit k jinému stejnému virtuálnímu počítači pro řešení potíží. [LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm) nebo [RAID](/previous-versions/azure/virtual-machines/linux/configure-raid) se můžou používat na datových discích.
* **Vyžaduje se podpora jádra pro připojení systémů souborů UDF.** Při prvním spuštění v Azure se konfigurace zřizování předává virtuálnímu počítači Linux přes médium ve formátu UDF, které je připojené k hostu. Agent Azure Linux musí být schopný připojit systém souborů UDF ke čtení konfigurace a zřídit virtuální počítač.
* Verze jádra systému Linux pod 2.6.37 nepodporují architekturu NUMA na technologii Hyper-V s většími velikostmi virtuálních počítačů. Tento problém se týká především starších distribucí pomocí nadřazeného jádra Red Hat 2.6.32 a byl opraven v RHEL 6,6 (kernel-2.6.32-504). Systémy s vlastními jádry staršími než 2.6.37 nebo jádry založenými na RHEL, které jsou starší než 2.6.32-504, musí nastavit parametr boot v `numa=off` příkazovém řádku jádra v souboru GRUB. conf. Další informace najdete v článku Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Nekonfigurujte odkládací oddíl na disku s operačním systémem. Další informace o tomto postupu najdete v následujících krocích.
* Všechny virtuální pevné disky v Azure musí mít virtuální velikost zarovnaná na 1 MB. Při převodu z nezpracovaného disku na virtuální pevný disk je nutné před převodem zajistit, aby velikost nezpracovaného disku byla násobkem 1 MB. Další informace najdete v [poznámkách k instalaci systému Linux](create-upload-generic.md#general-linux-installation-notes) .

## <a name="centos-6x"></a>CentOS 6. x

1. Ve Správci technologie Hyper-V vyberte virtuální počítač.

2. Kliknutím na **připojit** otevřete okno konzoly pro virtuální počítač.

3. V CentOS 6 může NetworkManager kolidovat s agentem Azure Linux. Tento balíček odinstalujte spuštěním následujícího příkazu:

    ```bash
    sudo rpm -e --nodeps NetworkManager
    ```

4. Vytvořte nebo upravte soubor `/etc/sysconfig/network` a přidejte následující text:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

5. Vytvořte nebo upravte soubor `/etc/sysconfig/network-scripts/ifcfg-eth0` a přidejte následující text:

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

6. Upravte pravidla udev, abyste se vyhnuli generování statických pravidel pro rozhraní sítě Ethernet. Tato pravidla můžou způsobit problémy při klonování virtuálního počítače v Microsoft Azure nebo Hyper-V:

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

7. Spuštěním následujícího příkazu zajistěte, aby se síťová služba spouštěla v době spuštění:

    ```bash
    sudo chkconfig network on
    ```

8. Pokud chcete použít OpenLogic zrcadly, které jsou hostovány v datacentrech Azure, nahraďte `/etc/yum.repos.d/CentOS-Base.repo` soubor následujícími úložištěmi.  Tím se také přidá úložiště **[OpenLogic]** , které zahrnuje další balíčky, jako je například agent Azure Linux:

   ```console
   [openlogic]
   name=CentOS-$releasever - openlogic packages for $basearch
   baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
   enabled=1
   gpgcheck=0

   [base]
   name=CentOS-$releasever - Base
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #released updates
   [updates]
   name=CentOS-$releasever - Updates
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #additional packages that may be useful
   [extras]
   name=CentOS-$releasever - Extras
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #additional packages that extend functionality of existing packages
   [centosplus]
   name=CentOS-$releasever - Plus
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

   #contrib - packages by Centos Users
   [contrib]
   name=CentOS-$releasever - Contrib
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=contrib&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
   ```

    > [!Note]
    > Zbývající část tohoto průvodce se předpokládá, že používáte aspoň `[openlogic]` úložiště, které se použije k instalaci agenta Azure Linux níže.

9. Přidejte následující řádek do/etc/yum.conf:

    ```console
    http_caching=packages
    ```

10. Spuštěním následujícího příkazu vymažete aktuální metadata Yumu a aktualizujte systém pomocí nejnovějších balíčků:

    ```bash
    yum clean all
    ```

    Pokud nevytváříte image pro starší verzi CentOS, doporučuje se aktualizovat všechny balíčky na nejnovější:

    ```bash
    sudo yum -y update
    ```

    Po spuštění tohoto příkazu může být vyžadováno restartování počítače.

11. Volitelné Nainstalujte ovladače pro Linux Integration Services (LIS).

    > [!IMPORTANT]
    > Tento krok je **vyžadován** pro CentOS 6,3 a starší a volitelný pro pozdější verze.

    ```bash
    sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
    sudo yum install microsoft-hyper-v
    ```

    Případně můžete postupovat podle pokynů k ruční instalaci na [stránce pro stažení aplikace LIS](https://www.microsoft.com/download/details.aspx?id=55106) a nainstalovat na virtuální počítač službu ot./min..

12. Nainstalujte agenta a závislosti pro Azure Linux. Spusťte a povolte službu waagent:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo service waagent start
    sudo chkconfig waagent on
    ```


    Balíček WALinuxAgent odstraní balíčky NetworkManager a NetworkManager-GNOME, pokud již nebyly odebrány, jak je popsáno v kroku 3.

13. Upravte spouštěcí řádek jádra v konfiguraci GRUB tak, aby zahrnoval další parametry jádra pro Azure. Provedete to tak, `/boot/grub/menu.lst` že otevřete v textovém editoru a zajistěte, aby výchozí jádro zahrnovalo následující parametry:

    ```console
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    Tím se také zajistí, že se všechny zprávy konzoly odešlou na první sériový port, což může pomoct podpoře Azure s problémy ladění.

    Kromě výše uvedeného se doporučuje *Odebrat* následující parametry:

    ```console
    rhgb quiet crashkernel=auto
    ```

    Grafické a tiché spouštění nejsou užitečné v cloudovém prostředí, kde chceme, aby se všechny protokoly odesílaly na sériový port.  `crashkernel`Možnost může být ponechána v případě potřeby nakonfigurovaná, ale Všimněte si, že tento parametr sníží velikost dostupné paměti ve virtuálním počítači o 128 MB nebo více, což může být problematické u menších velikostí virtuálních počítačů.

    > [!Important]
    > CentOS 6,5 a starší musí také nastavit parametr jádra `numa=off` . Podívejte se na Red Hat [KB 436883](https://access.redhat.com/solutions/436883).

14. Ujistěte se, že je server SSH nainstalovaný a nakonfigurované tak, aby se spouštěl při spuštění.  Obvykle se jedná o výchozí nastavení.

15. Nevytvářejte odkládací místo na disku s operačním systémem.

    Agent Azure Linux může automaticky nakonfigurovat odkládací prostor pomocí disku místního prostředku, který je připojen k virtuálnímu počítači po zřízení v Azure. Všimněte si, že místní disk prostředků je *dočasný* disk a při zrušení zřízení virtuálního počítače může dojít k jeho vyprázdnění. Po instalaci agenta Azure Linux (viz předchozí krok) upravte `/etc/waagent.conf` odpovídajícím způsobem následující parametry:

    ```console
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048 ## NOTE: set this to whatever you need it to be.
    ```

16. Spuštěním následujících příkazů můžete virtuální počítač zrušit a připravit ho pro zřizování v Azure:

    ```bash
    sudo waagent -force -deprovision+user
    export HISTSIZE=0
    logout
    ```

17. Klikněte na **Akce – > vypnout** ve Správci technologie Hyper-V. Virtuální pevný disk se systémem Linux je teď připravený k nahrání do Azure.



## <a name="centos-70"></a>CentOS 7.0 +

**Změny v CentOS 7 (a podobné deriváty)**

Příprava virtuálního počítače s CentOS 7 pro Azure je velmi podobná CentOS 6, ale existuje několik důležitých rozdílů, které se zabývají:

* Balíček NetworkManager už není v konfliktu s agentem Azure Linux. Tento balíček se nainstaluje ve výchozím nastavení a doporučujeme, abyste ho neodebrali.
* GRUB2 se teď používá jako výchozí zaváděcí program pro spouštění, takže se změnil postup pro úpravu parametrů jádra (viz níže).
* XFS je teď výchozím systémem souborů. Systém souborů ext4 se může v případě potřeby i nadále použít.

**Kroky konfigurace**

1. Ve Správci technologie Hyper-V vyberte virtuální počítač.

2. Kliknutím na **připojit** otevřete okno konzoly pro virtuální počítač.

3. Vytvořte nebo upravte soubor `/etc/sysconfig/network` a přidejte následující text:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

4. Vytvořte nebo upravte soubor `/etc/sysconfig/network-scripts/ifcfg-eth0` a přidejte následující text:

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    NM_CONTROLLED=no
    ```

5. Upravte pravidla udev, abyste se vyhnuli generování statických pravidel pro rozhraní sítě Ethernet. Tato pravidla můžou způsobit problémy při klonování virtuálního počítače v Microsoft Azure nebo Hyper-V:

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    ```

6. Pokud chcete použít OpenLogic zrcadly, které jsou hostovány v datacentrech Azure, nahraďte `/etc/yum.repos.d/CentOS-Base.repo` soubor následujícími úložištěmi.  Tím se také přidá úložiště **[OpenLogic]** zahrnující balíčky pro agenta Azure Linux:

   ```console
   [openlogic]
   name=CentOS-$releasever - openlogic packages for $basearch
   baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
   enabled=1
   gpgcheck=0
    
   [base]
   name=CentOS-$releasever - Base
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=os&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #released updates
   [updates]
   name=CentOS-$releasever - Updates
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=updates&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #additional packages that may be useful
   [extras]
   name=CentOS-$releasever - Extras
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=extras&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
   gpgcheck=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
    
   #additional packages that extend functionality of existing packages
   [centosplus]
   name=CentOS-$releasever - Plus
   #mirrorlist=http://mirrorlist.centos.org/?release=$releasever&arch=$basearch&repo=centosplus&infra=$infra
   baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
   gpgcheck=1
   enabled=0
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
   ```
    
   > [!Note]
   > Zbývající část tohoto průvodce se předpokládá, že používáte aspoň `[openlogic]` úložiště, které se použije k instalaci agenta Azure Linux níže.

7. Spusťte následující příkaz, který vymaže aktuální metadata Yumu a nainstaluje aktualizace:

    ```bash
    sudo yum clean all
    ```

    Pokud nevytváříte image pro starší verzi CentOS, doporučuje se aktualizovat všechny balíčky na nejnovější:

    ```bash
    sudo yum -y update
    ```

    Po spuštění tohoto příkazu se možná vyžaduje restart.

8. Upravte spouštěcí řádek jádra v konfiguraci GRUB tak, aby zahrnoval další parametry jádra pro Azure. Provedete to tak, `/etc/default/grub` že otevřete v textovém editoru a upravíte `GRUB_CMDLINE_LINUX` parametr, například:

    ```console
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Tím se také zajistí, že se všechny zprávy konzoly odešlou na první sériový port, což může pomoct podpoře Azure s problémy ladění. Také vypne nové zásady vytváření názvů pro síťové karty v CentOS 7. Kromě výše uvedeného se doporučuje *Odebrat* následující parametry:

    ```console
    rhgb quiet crashkernel=auto
    ```

    Grafické a tiché spouštění nejsou užitečné v cloudovém prostředí, kde chceme, aby se všechny protokoly odesílaly na sériový port. `crashkernel`Možnost může být ponechána v případě potřeby nakonfigurovaná, ale Všimněte si, že tento parametr sníží velikost dostupné paměti ve virtuálním počítači o 128 MB nebo více, což může být problematické u menších velikostí virtuálních počítačů.

9. Po dokončení úprav `/etc/default/grub` na výše spusťte následující příkaz pro opětovné sestavení konfigurace grub:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

10. Při sestavování image z **VMware, VirtualBox nebo KVM:** zajistěte, aby byly ovladače Hyper-V součástí initramfs:

    Upravit `/etc/dracut.conf` , přidat obsah:

    ```console
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Znovu sestavte initramfs:

    ```bash
    sudo dracut -f -v
    ```

11. Instalace agenta Azure Linux a závislostí pro rozšíření virtuálních počítačů Azure:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo systemctl enable waagent
    ```

12. Instalace Cloud-init pro zpracování zřizování

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


13. Přepnutí konfigurace nevytváří na disku s operačním systémem odkládací místo.

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

14. Spuštěním následujících příkazů můžete virtuální počítač zrušit a připravit ho pro zřizování v Azure:

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

15. Klikněte na **Akce – > vypnout** ve Správci technologie Hyper-V. Virtuální pevný disk se systémem Linux je teď připravený k nahrání do Azure.

## <a name="next-steps"></a>Další kroky

Nyní jste připraveni k vytváření nových virtuálních počítačů v Azure pomocí virtuálního pevného disku s CentOS Linux. Pokud soubor. VHD do Azure nahráváte poprvé, přečtěte si článek [Vytvoření virtuálního počítače se systémem Linux z vlastního disku](upload-vhd.md#option-1-upload-a-vhd).