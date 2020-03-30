---
title: Vytvoření a nahrání virtuálního pevného disku SZP se sídlem v Systému <c)
description: Naučte se vytvářet a nahrávat virtuální pevný disk Azure (VHD), který obsahuje operační systém Linux založený na CentOS.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/25/2019
ms.author: guybo
ms.openlocfilehash: 8899249fd284f69fa26bab8cd70aaf6a67fbb83c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066786"
---
# <a name="prepare-a-centos-based-virtual-machine-for-azure"></a>Příprava virtuálního počítače založeného na CentOS pro Azure

Naučte se vytvářet a nahrávat virtuální pevný disk Azure (VHD), který obsahuje operační systém Linux založený na CentOS.

* [Příprava virtuálního počítače CentOS 6.x pro Azure](#centos-6x)
* [Příprava virtuálního počítače CentOS 7.0+ pro Azure](#centos-70)


## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste již nainstalovali operační systém CentOS (nebo podobný derivát) Linux na virtuální pevný disk. Existuje více nástrojů pro vytváření souborů .vhd, například virtualizační řešení, jako je Hyper-V. Pokyny naleznete [v tématu Instalace role Technologie Hyper-V a Konfigurace virtuálního počítače](https://technet.microsoft.com/library/hh846766.aspx).

**Poznámky k instalaci CentOS**

* Další tipy na přípravu Linuxu pro Azure najdete také v [obecných poznámkách k instalaci Linuxu.](create-upload-generic.md#general-linux-installation-notes)
* Formát VHDX není v Azure podporován, pouze **pevný virtuální pevný disk .**  Disk můžete převést do formátu Virtuálního pevného disku pomocí Správce technologie Hyper-V nebo rutiny convert-vhd. Pokud používáte VirtualBox, znamená to výběr **pevné velikosti** na rozdíl od výchozího dynamicky přidělenépři vytváření disku.
* Při instalaci systému Linux *se doporučuje* používat standardní oddíly spíše než LVM (často výchozí pro mnoho instalací). Tím se zabrání lVM název konflikty s klonované virtuální počítače, zejména v případě, že disk operačního systému někdy musí být připojen k jinému identickévirtuálního počítače pro řešení potíží. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lze použít na datových discích.
* Je vyžadována podpora jádra pro montáž souborových systémů UDF. Při prvním spuštění v Azure je konfigurace zřizování předána virtuálnímu počítači Sn Linuxprostřednictvím média ve formátu UDF, která je připojena k hostu. Agent Azure Linux musí být schopen připojit systém souborů UDF číst jeho konfiguraci a zřízení virtuálního počítače.
* Verze linuxového jádra pod 2.6.37 nepodporují NUMA na Hyper-V s většími velikostmi virtuálních her. Tento problém se týká především starší distribuce pomocí upstream Red Hat 2.6.32 jádra a byl opraven v RHEL 6.6 (kernel-2.6.32-504). Systémy s vlastními jádry staršími než 2.6.37 nebo jádra založená na RHEL starší než `numa=off` 2.6.32-504 musí nastavit zaváděcí parametr na příkazovém řádku jádra v grub.conf. Více informací naleznete v red [hatu KB 436883](https://access.redhat.com/solutions/436883).
* Nekonfigurujte odkládací oddíl na disku operačního systému. Agenta Linuxu lze nakonfigurovat tak, aby vytvořil odkládací soubor na disku s dočasným prostředkem.  Další informace o tom naleznete v následujících krocích.
* Všechny virtuální počítače v Azure musí mít virtuální velikost zarovnanou na 1 MB. Při převodu ze surového disku na virtuální pevný disk je nutné zajistit, aby velikost nezpracovaného disku byla před převodem násobkem 1 MB. Další informace naleznete v [poznámkách k instalaci Linuxu.](create-upload-generic.md#general-linux-installation-notes)

## <a name="centos-6x"></a>CentOS 6,x

1. Ve Správci technologie Hyper-V vyberte virtuální počítač.

2. Kliknutím na **Připojit** otevřete okno konzoly pro virtuální počítač.

3. V CentOS 6 NetworkManager můžete zasahovat agenta Azure Linuxu. Odinstalujte tento balíček spuštěním následujícího příkazu:

    ```bash
    sudo rpm -e --nodeps NetworkManager
    ```

4. Vytvořte nebo `/etc/sysconfig/network` upravte soubor a přidejte následující text:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

5. Vytvořte nebo `/etc/sysconfig/network-scripts/ifcfg-eth0` upravte soubor a přidejte následující text:

    ```console
    DEVICE=eth0
    ONBOOT=yes
    BOOTPROTO=dhcp
    TYPE=Ethernet
    USERCTL=no
    PEERDNS=yes
    IPV6INIT=no
    ```

6. Upravte pravidla udev, abyste se vyhnuli generování statických pravidel pro ethernetové rozhraní. Tato pravidla mohou způsobit problémy při klonování virtuálního počítače v Microsoft Azure nebo Hyper-V:

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    sudo rm -f /etc/udev/rules.d/70-persistent-net.rules
    ```

7. Ujistěte se, že síťová služba bude spuštěna při spuštění spuštěním následujícího příkazu:

    ```bash
    sudo chkconfig network on
    ```

8. Pokud chcete použít openlogic zrcadla, které jsou hostované v datových `/etc/yum.repos.d/CentOS-Base.repo` centrech Azure, pak nahraďte soubor s následujícími úložišti.  Tím se také přidá úložiště **[openlogic],** které obsahuje další balíčky, jako je například agent Azure Linux:

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
    > Zbytek této příručky bude předpokládat, `[openlogic]` že používáte alespoň repo, které se použije k instalaci agenta Azure Linuxu níže.

9. Přidejte následující řádek do /etc/yum.conf:

    ```console
    http_caching=packages
    ```

10. Spusťte následující příkaz pro vymazání aktuálních metadat yum a aktualizujte systém nejnovějšími balíčky:

    ```bash
    yum clean all
    ```

    Pokud nevytváříte bitovou kopii pro starší verzi CentOS, doporučujeme aktualizovat všechny balíčky na nejnovější:

    ```bash
    sudo yum -y update
    ```

    Po spuštění tohoto příkazu může být vyžadováno restartování počítače.

11. (Nepovinné) Nainstalujte ovladače pro linuxové integrační služby (LIS).

    > [!IMPORTANT]
    > Krok je **vyžadován** pro CentOS 6.3 a starší a volitelný pro pozdější verze.

    ```bash
    sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
    sudo yum install microsoft-hyper-v
    ```

    Případně můžete podle pokynů pro ruční instalaci na [stránce stažení LIS](https://www.microsoft.com/download/details.aspx?id=51612) nainstalovat RPM do virtuálního počítače.

12. Nainstalujte agenta Azure Linuxu a závislosti. Spuštění a povolení služby waagent:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo service waagent start
    sudo chkconfig waagent on
    ```


    Balíček WALinuxAgent odebere balíčky NetworkManager a NetworkManager-gnome, pokud ještě nebyly odebrány, jak je popsáno v kroku 3.

13. Upravte zaváděcí řádek jádra v konfiguraci grub tak, aby zahrnovala další parametry jádra pro Azure. Chcete-li to `/boot/grub/menu.lst` provést, otevřete v textovém editoru a ujistěte se, že výchozí jádro obsahuje následující parametry:

    ```console
    console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    ```

    Tím se také zajistí, že všechny zprávy konzoly jsou odesílány na první sériový port, který může pomoci podporu Azure s problémy ladění.

    Kromě výše uvedeného se doporučuje *odstranit* následující parametry:

    ```console
    rhgb quiet crashkernel=auto
    ```

    Grafické a tiché spuštění není užitečné v cloudovém prostředí, kde chceme, aby všechny protokoly byly odeslány do sériového portu.  Možnost `crashkernel` může zůstat nakonfigurován v případě potřeby, ale všimněte si, že tento parametr sníží množství dostupné paměti ve virtuálním počítači o 128 MB nebo více, což může být problematické na menší velikosti virtuálních zařízení.

    > [!Important]
    > CentOS 6.5 a starší musí `numa=off`také nastavit parametr jádra . Viz Červený klobouk [KB 436883](https://access.redhat.com/solutions/436883).

14. Ujistěte se, že je server SSH nainstalován a nakonfigurován tak, aby se spouštěl při spuštění.  Toto je obvykle výchozí.

15. Nevytvářejte odkládací prostor na disku operačního systému.

    Azure Linux Agent můžete automaticky nakonfigurovat odkládací prostor pomocí disku místního prostředku, který je připojen k virtuálnímu počítači po zřizování v Azure. Všimněte si, že disk místního prostředku je *dočasný* disk a může být vyprázdněn při zrušení zřízení virtuálního počítače. Po instalaci Azure Linux Agent (viz předchozí krok), `/etc/waagent.conf` upravte následující parametry v odpovídajícím způsobem:

    ```console
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048 ## NOTE: set this to whatever you need it to be.
    ```

16. Spusťte následující příkazy, abyste deprovision zanatii virtuálního počítače a připravit ho na zřizování v Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

17. Ve Správci technologie Hyper-V klikněte na **Akce -> Vypnout.** Virtuální disk Linux ukterýzdvižený disk linuxu je teď připravený k nahrání do Azure.



## <a name="centos-70"></a>Centos 7,0+

**Změny v CentOS 7 (a podobné deriváty)**

Příprava virtuálního počítače CentOS 7 pro Azure je velmi podobná CentOS 6, ale existuje několik důležitých rozdílů, které stojí za zmínku:

* Balíček NetworkManager již není v konfliktu s agentem Azure Linux. Tento balíček je nainstalován ve výchozím nastavení a doporučujeme, aby nebyl odebrán.
* GRUB2 se nyní používá jako výchozí zavaděč, takže se změnil postup pro úpravu parametrů jádra (viz níže).
* XFS je nyní výchozí souborový systém. Systém souborů ext4 lze v případě potřeby stále používat.

**Kroky konfigurace**

1. Ve Správci technologie Hyper-V vyberte virtuální počítač.

2. Kliknutím na **Připojit** otevřete okno konzoly pro virtuální počítač.

3. Vytvořte nebo `/etc/sysconfig/network` upravte soubor a přidejte následující text:

    ```console
    NETWORKING=yes
    HOSTNAME=localhost.localdomain
    ```

4. Vytvořte nebo `/etc/sysconfig/network-scripts/ifcfg-eth0` upravte soubor a přidejte následující text:

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

5. Upravte pravidla udev, abyste se vyhnuli generování statických pravidel pro ethernetové rozhraní. Tato pravidla mohou způsobit problémy při klonování virtuálního počítače v Microsoft Azure nebo Hyper-V:

    ```bash
    sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
    ```

6. Pokud chcete použít openlogic zrcadla, které jsou hostované v datových `/etc/yum.repos.d/CentOS-Base.repo` centrech Azure, pak nahraďte soubor s následujícími úložišti.  Tím se také přidá úložiště **[openlogic],** které obsahuje balíčky pro agenta Azure Linuxu:

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
   > Zbytek této příručky bude předpokládat, `[openlogic]` že používáte alespoň repo, které se použije k instalaci agenta Azure Linuxu níže.

7. Spuštěním následujícího příkazu vymažte aktuální metadata yum a nainstalujte všechny aktualizace:

    ```bash
    sudo yum clean all
    ```

    Pokud nevytváříte bitovou kopii pro starší verzi CentOS, doporučujeme aktualizovat všechny balíčky na nejnovější:

    ```bash
    sudo yum -y update
    ```

    Po spuštění tohoto příkazu může být nutné restartovat počítač.

8. Upravte zaváděcí řádek jádra v konfiguraci grub tak, aby zahrnovala další parametry jádra pro Azure. Chcete-li to `/etc/default/grub` provést, otevřete `GRUB_CMDLINE_LINUX` v textovém editoru a upravte parametr, například:

    ```console
    GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
    ```

   Tím se také zajistí, že všechny zprávy konzoly jsou odesílány na první sériový port, který může pomoci podporu Azure s problémy ladění. Vypne také nové konvence pojmenování CentOS 7 pro síťové karty. Kromě výše uvedeného se doporučuje *odstranit* následující parametry:

    ```console
    rhgb quiet crashkernel=auto
    ```

    Grafické a tiché spuštění není užitečné v cloudovém prostředí, kde chceme, aby všechny protokoly byly odeslány do sériového portu. Možnost `crashkernel` může zůstat nakonfigurován v případě potřeby, ale všimněte si, že tento parametr sníží množství dostupné paměti ve virtuálním počítači o 128 MB nebo více, což může být problematické na menší velikosti virtuálních zařízení.

9. Po dokončení úprav `/etc/default/grub` podle výše spusťte následující příkaz pro opětovné sestavení konfigurace grubu:

    ```bash
    sudo grub2-mkconfig -o /boot/grub2/grub.cfg
    ```

10. Pokud vytváříte obraz z **VMware, VirtualBox nebo KVM:** Ujistěte se, že ovladače Hyper-V jsou zahrnuty v initramfs:

    Upravit `/etc/dracut.conf`, přidat obsah:

    ```console
    add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "
    ```

    Znovu vytvořit initramfs:

    ```bash
    sudo dracut -f -v
    ```

11. Nainstalujte agenta Azure Linuxu a závislosti:

    ```bash
    sudo yum install python-pyasn1 WALinuxAgent
    sudo systemctl enable waagent
    ```

12. Nevytvářejte odkládací prostor na disku operačního systému.

    Azure Linux Agent můžete automaticky nakonfigurovat odkládací prostor pomocí disku místního prostředku, který je připojen k virtuálnímu počítači po zřizování v Azure. Všimněte si, že disk místního prostředku je *dočasný* disk a může být vyprázdněn při zrušení zřízení virtuálního počítače. Po instalaci Azure Linux Agent (viz předchozí krok), `/etc/waagent.conf` upravte následující parametry v odpovídajícím způsobem:

    ```console
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.EnableSwap=y
    ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.
    ```

13. Spusťte následující příkazy, abyste deprovision zanatii virtuálního počítače a připravit ho na zřizování v Azure:

    ```bash
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```

14. Ve Správci technologie Hyper-V klikněte na **Akce -> Vypnout.** Virtuální disk Linux ukterýzdvižený disk linuxu je teď připravený k nahrání do Azure.

## <a name="next-steps"></a>Další kroky

Teď jste připraveni použít virtuální pevný disk CentOS Linux k vytvoření nových virtuálních počítačů v Azure. Pokud je to poprvé, co nahráváte soubor .vhd do Azure, přečtěte si téma [Vytvoření virtuálního počítače s Linuxem z vlastního disku](upload-vhd.md#option-1-upload-a-vhd).
