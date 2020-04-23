---
title: Vytvoření a nahrání virtuálního pevného disku Red Hat Enterprise Linux pro použití v Azure
description: Naučte se vytvářet a nahrávat virtuální pevný disk Azure (VHD), který obsahuje operační systém Red Hat Linux.
author: gbowerman
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/17/2019
ms.author: guybo
ms.openlocfilehash: 4140f9f07a0fd653c8e0370d017cbae7effd0a07
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084307"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Příprava virtuálního počítače založeného na Red Hat pro Azure
V tomto článku se dozvíte, jak připravit virtuální počítač Red Hat Enterprise Linux (RHEL) pro použití v Azure. Verze RHEL, které jsou zahrnuty v tomto článku jsou 6.7+ a 7.1+. Hypervisory pro přípravu, které jsou zahrnuty v tomto článku jsou Hyper-V, virtuální počítač založený na jádru (KVM) a VMware. Další informace o požadavcích na způsobilost pro účast v programu Red Hat pro cloud ový přístup najdete v [tématu Red Hat's Cloud Access website](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) and [Running RHEL on Azure](https://access.redhat.com/ecosystem/ccsp/microsoft-azure). Způsoby automatizace vytváření ibi obrazů RHEL najdete v [azure image builder](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-overview).

## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Příprava virtuálního počítače na bázi Red Hat od správce Technologie Hyper-V

### <a name="prerequisites"></a>Požadované součásti
Tato část předpokládá, že jste již získali soubor ISO z webu Red Hat a nainstalovali obraz RHEL na virtuální pevný disk (VHD). Další podrobnosti o použití správce technologie Hyper-V k instalaci bitové kopie operačního systému naleznete [v tématu Instalace role Technologie Hyper-V a Konfigurace virtuálního počítače](https://technet.microsoft.com/library/hh846766.aspx).

**Poznámky k instalaci RHEL**

* Azure nepodporuje formát VHDX. Azure podporuje jenom pevný Virtuální Pevný virtuální disk. Správce technologie Hyper-V můžete použít k převodu disku do formátu VHD nebo můžete použít rutinu convert-vhd. Pokud používáte VirtualBox, vyberte **Pevná velikost** na rozdíl od výchozí dynamicky přidělené možnosti při vytváření disku.
* Azure podporuje virtuální počítače Gen1 (BIOS boot) & Gen2 (UEFI boot).
* Maximální velikost, která je povolena pro virtuální pevný disk, je 1 023 GB.
* Správce logických svazků (LVM) je podporovaný a může být použit na disku operačního systému nebo datových discích ve virtuálních počítačích Azure. Obecně se však doporučuje používat standardní oddíly na disku operačního systému spíše než LVM. Tento postup zabrání konfliktu názvů LVM s klonovanými virtuálními počítači, zejména pokud někdy potřebujete připojit disk operačního systému k jinému identickému virtuálnímu počítači pro řešení potíží. Viz také dokumentace [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) a [RAID.](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* Je vyžadována podpora jádra pro připojení systémů souborů UDF (Universal Disk Format). Při prvním spuštění v Azure předá médium ve formátu UDF připojené k hostu konfiguraci zřizování do virtuálního počítače SIP. Agent Azure Linux musí být schopen připojit systém souborů UDF číst jeho konfiguraci a zřízení virtuálního počítače.
* Nekonfigurujte odkládací oddíl na disku operačního systému. Agenta Linuxu lze nakonfigurovat tak, aby vytvořil odkládací soubor na disku s dočasným prostředkem.  Další informace o tom naleznete v následujících krocích.
* Všechny virtuální počítače v Azure musí mít virtuální velikost zarovnanou na 1 MB. Při převodu ze surového disku na virtuální pevný disk je nutné zajistit, aby velikost nezpracovaného disku byla před převodem násobkem 1 MB. Další podrobnosti naleznete v následujících krocích. Další informace najdete také v [poznámkách k instalaci linuxu.](create-upload-generic.md#general-linux-installation-notes)

### <a name="prepare-a-rhel-6-virtual-machine-from-hyper-v-manager"></a>Příprava virtuálního počítače RHEL 6 od hyper-V manageru

1. Ve Správci technologie Hyper-V vyberte virtuální počítač.

1. Kliknutím na **Připojit** otevřete okno konzoly pro virtuální počítač.

1. V RHEL 6 NetworkManager můžete zasahovat agenta Azure Linuxu. Odinstalujte tento balíček spuštěním následujícího příkazu:
   
        # sudo rpm -e --nodeps NetworkManager

1. Vytvořte nebo `/etc/sysconfig/network` upravte soubor a přidejte následující text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Vytvořte nebo `/etc/sysconfig/network-scripts/ifcfg-eth0` upravte soubor a přidejte následující text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Přesuňte (nebo odeberte) pravidla udev, abyste se vyhnuli generování statických pravidel pro ethernetové rozhraní. Tato pravidla způsobují problémy při klonování virtuálního počítače v Microsoft Azure nebo Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Ujistěte se, že síťová služba bude spuštěna při spuštění spuštěním následujícího příkazu:

        # sudo chkconfig network on

1. Zaregistrujte své předplatné Red Hat a povolte instalaci balíčků z úložiště RHEL spuštěním následujícího příkazu:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Balíček WALinuxAgent `WALinuxAgent-<version>`, byl posunut do úložiště Red Hat extras. Povolte úložiště doplňků spuštěním následujícího příkazu:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Upravte zaváděcí řádek jádra v konfiguraci grub tak, aby zahrnovala další parametry jádra pro Azure. Chcete-li provést `/boot/grub/menu.lst` tuto změnu, otevřete v textovém editoru a ujistěte se, že výchozí jádro obsahuje následující parametry:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Tím se také zajistí, že všechny zprávy konzoly jsou odesílány na první sériový port, který může pomoci podporu Azure s problémy ladění.
    
    Kromě toho doporučujeme odebrat následující parametry:
    
        rhgb quiet crashkernel=auto
    
    Grafické a tiché spuštění není užitečné v cloudovém prostředí, kde chceme, aby všechny protokoly byly odeslány do sériového portu.  V případě `crashkernel` potřeby můžete tuto možnost ponechat nakonfigurovanou. Všimněte si, že tento parametr snižuje množství dostupné paměti ve virtuálním počítači o 128 MB nebo více. Tato konfigurace může být problematická u menších velikostí virtuálních počítačů.


1. Ujistěte se, že je server zabezpečeného prostředí (SSH) nainstalován a nakonfigurován tak, aby se spouštěl při spuštění, což je obvykle výchozí. Upravte /etc/ssh/sshd_config tak, aby obsahovala následující řádek:

        ClientAliveInterval 180

1. Nainstalujte agenta Azure Linux spuštěním následujícího příkazu:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

    Instalací balíčku WALinuxAgent odeberete balíčky NetworkManager a NetworkManager-gnome, pokud ještě nebyly odebrány v kroku 3.

1. Nevytvářejte odkládací místo na disku operačního systému.

    Azure Linux Agent můžete automaticky nakonfigurovat odkládací prostor pomocí disku místního prostředku, který je připojen k virtuálnímu počítači po zřízení virtuálního počítače v Azure. Všimněte si, že disk místního prostředku je dočasný disk a že může být vyprázdněn, pokud je virtuální počítač zrušen. Po instalaci Agenta Azure Linux v předchozím kroku upravte v /etc/waagent.conf odpovídajícím způsobem následující parametry:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Zrušení registrace předplatného (v případě potřeby) spuštěním následujícího příkazu:

        # sudo subscription-manager unregister

1. Spusťte následující příkazy, abyste deprovision zanatii virtuálního počítače a připravit ho na zřizování v Azure:

        # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Ve Správci technologie Hyper-V klikněte na **Akce** > **vypnuto.** Virtuální disk Linux ukterýzdvižený disk linuxu je teď připravený k nahrání do Azure.


### <a name="prepare-a-rhel-7-virtual-machine-from-hyper-v-manager"></a>Příprava virtuálního počítače RHEL 7 od hyper-V manageru

1. Ve Správci technologie Hyper-V vyberte virtuální počítač.

1. Kliknutím na **Připojit** otevřete okno konzoly pro virtuální počítač.

1. Vytvořte nebo `/etc/sysconfig/network` upravte soubor a přidejte následující text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Vytvořte nebo `/etc/sysconfig/network-scripts/ifcfg-eth0` upravte soubor a přidejte následující text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
    PERSISTENT_DHCLIENT=ano NM_CONTROLLED=ano

1. Ujistěte se, že síťová služba bude spuštěna při spuštění spuštěním následujícího příkazu:

        # sudo systemctl enable network

1. Zaregistrujte své předplatné Red Hat a povolte instalaci balíčků z úložiště RHEL spuštěním následujícího příkazu:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Upravte zaváděcí řádek jádra v konfiguraci grub tak, aby zahrnovala další parametry jádra pro Azure. Chcete-li provést `/etc/default/grub` tuto změnu, otevřete `GRUB_CMDLINE_LINUX` v textovém editoru a upravte parametr. Příklad:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Tím se také zajistí, že všechny zprávy konzoly jsou odesílány na první sériový port, který může pomoci podporu Azure s problémy ladění. Tato konfigurace také vypne nové konvence pojmenování RHEL 7 pro síťové karty. Kromě toho doporučujeme odebrat následující parametry:
   
        rhgb quiet crashkernel=auto
   
    Grafické a tiché spuštění není užitečné v cloudovém prostředí, kde chceme, aby všechny protokoly byly odeslány do sériového portu. V případě `crashkernel` potřeby můžete tuto možnost ponechat nakonfigurovanou. Všimněte si, že tento parametr snižuje množství dostupné paměti ve virtuálním počítači o 128 MB nebo více, což může být problematické u menších velikostí virtuálních počítačů.

1. Po dokončení úprav `/etc/default/grub`spusťte následující příkaz pro opětovné sestavení konfigurace grubu:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

1. Ujistěte se, že je server SSH nainstalován a nakonfigurován tak, aby se spouštěl při spuštění, což je obvykle výchozí. Upravte `/etc/ssh/sshd_config` tak, aby obsahoval následující řádek:

        ClientAliveInterval 180

1. Balíček WALinuxAgent `WALinuxAgent-<version>`, byl posunut do úložiště Red Hat extras. Povolte úložiště doplňků spuštěním následujícího příkazu:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Nainstalujte agenta Azure Linux spuštěním následujícího příkazu:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

1. Nevytvářejte odkládací místo na disku operačního systému.

    Azure Linux Agent můžete automaticky nakonfigurovat odkládací prostor pomocí disku místního prostředku, který je připojen k virtuálnímu počítači po zřízení virtuálního počítače v Azure. Všimněte si, že disk místního prostředku je dočasný disk a může být vyprázdněn, pokud je virtuální počítač zrušen. Po instalaci Agenta Azure Linuxv předchozím kroku upravte `/etc/waagent.conf` odpovídajícím způsobem následující parametry:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Pokud chcete zrušit registraci předplatného, spusťte následující příkaz:

        # sudo subscription-manager unregister

1. Spusťte následující příkazy, abyste deprovision zanatii virtuálního počítače a připravit ho na zřizování v Azure:

        # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Ve Správci technologie Hyper-V klikněte na **Akce** > **vypnuto.** Virtuální disk Linux ukterýzdvižený disk linuxu je teď připravený k nahrání do Azure.


## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Příprava virtuálního počítače na bázi Red Hat u KVM
### <a name="prepare-a-rhel-6-virtual-machine-from-kvm"></a>Příprava virtuálního stroje RHEL 6 od KVM

1. Stáhněte si obraz KVM RHEL 6 z webových stránek Red Hat.

1. Nastavte kořenové heslo.

    Vygenerujte šifrované heslo a zkopírujte výstup příkazu:

        # openssl passwd -1 changeme

    Nastavte kořenové heslo s guestfish:
        
        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Změnit druhé pole kořenového uživatele z "!!" k šifrovanému heslu.

1. Vytvořte virtuální stroj v KVM z obrázku qcow2. Nastavte typ disku na **qcow2**a nastavte model zařízení virtuální sítě na **virtio**. Potom spusťte virtuální počítač a přihlaste se jako root.

1. Vytvořte nebo `/etc/sysconfig/network` upravte soubor a přidejte následující text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Vytvořte nebo `/etc/sysconfig/network-scripts/ifcfg-eth0` upravte soubor a přidejte následující text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Přesuňte (nebo odeberte) pravidla udev, abyste se vyhnuli generování statických pravidel pro ethernetové rozhraní. Tato pravidla způsobují problémy při klonování virtuálního počítače v Azure nebo Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Ujistěte se, že síťová služba bude spuštěna při spuštění spuštěním následujícího příkazu:

        # chkconfig network on

1. Zaregistrujte své předplatné Red Hat a povolte instalaci balíčků z úložiště RHEL spuštěním následujícího příkazu:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

1. Upravte zaváděcí řádek jádra v konfiguraci grub tak, aby zahrnovala další parametry jádra pro Azure. Chcete-li provést `/boot/grub/menu.lst` tuto konfiguraci, otevřete v textovém editoru a ujistěte se, že výchozí jádro obsahuje následující parametry:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Tím se také zajistí, že všechny zprávy konzoly jsou odesílány na první sériový port, který může pomoci podporu Azure s problémy ladění.
    
    Kromě toho doporučujeme odebrat následující parametry:
    
        rhgb quiet crashkernel=auto
    
    Grafické a tiché spuštění není užitečné v cloudovém prostředí, kde chceme, aby všechny protokoly byly odeslány do sériového portu. V případě `crashkernel` potřeby můžete tuto možnost ponechat nakonfigurovanou. Všimněte si, že tento parametr snižuje množství dostupné paměti ve virtuálním počítači o 128 MB nebo více, což může být problematické u menších velikostí virtuálních počítačů.


1. Přidejte moduly Hyper-V do initramfs:  

    Upravte `/etc/dracut.conf`a přidejte následující obsah:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Znovu sestavit initramfs:

        # dracut -f -v

1. Odinstalujte cloud-init:

        # yum remove cloud-init

1. Ujistěte se, že je server SSH nainstalován a nakonfigurován tak, aby se spouštěl při spuštění:

        # chkconfig sshd on

    Upravte /etc/ssh/sshd_config tak, aby obsahovala následující řádky:

        PasswordAuthentication yes
        ClientAliveInterval 180

1. Balíček WALinuxAgent `WALinuxAgent-<version>`, byl posunut do úložiště Red Hat extras. Povolte úložiště doplňků spuštěním následujícího příkazu:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Nainstalujte agenta Azure Linux spuštěním následujícího příkazu:

        # yum install WALinuxAgent

        # chkconfig waagent on

1. Azure Linux Agent můžete automaticky nakonfigurovat odkládací prostor pomocí disku místního prostředku, který je připojen k virtuálnímu počítači po zřízení virtuálního počítače v Azure. Všimněte si, že disk místního prostředku je dočasný disk a může být vyprázdněn, pokud je virtuální počítač zrušen. Po instalaci Agenta Azure Linux v předchozím kroku upravte v **/etc/waagent.conf** odpovídajícím způsobem následující parametry:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Zrušení registrace předplatného (v případě potřeby) spuštěním následujícího příkazu:

        # subscription-manager unregister

1. Spusťte následující příkazy, abyste deprovision zanatii virtuálního počítače a připravit ho na zřizování v Azure:

        # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Vypněte virtuální počítač v KVM.

1. Převeďte obraz qcow2 do formátu VHD.

> [!NOTE]
> Existuje známá chyba ve verzích qemu-img >=2.2.1, která má za následek nesprávně formátovaný virtuální pevný disk. Problém byl vyřešen v QEMU 2.6. Doporučuje se používat buď qemu-img 2.2.0 nebo nižší, nebo aktualizovat na 2.6 nebo vyšší. Odkaz: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f qcow2 -O raw rhel-6.9.qcow2 rhel-6.9.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.9.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd

        
### <a name="prepare-a-rhel-7-virtual-machine-from-kvm"></a>Příprava virtuálního stroje RHEL 7 od KVM

1. Stáhněte si obraz KVM RHEL 7 z webových stránek Red Hat. Tento postup používá RHEL 7 jako příklad.

1. Nastavte kořenové heslo.

    Vygenerujte šifrované heslo a zkopírujte výstup příkazu:

        # openssl passwd -1 changeme

    Nastavte kořenové heslo s guestfish:

        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Změna druhého pole uživatele kořenového adresáře z "!!" k šifrovanému heslu.

1. Vytvořte virtuální stroj v KVM z obrázku qcow2. Nastavte typ disku na **qcow2**a nastavte model zařízení virtuální sítě na **virtio**. Potom spusťte virtuální počítač a přihlaste se jako root.

1. Vytvořte nebo `/etc/sysconfig/network` upravte soubor a přidejte následující text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Vytvořte nebo `/etc/sysconfig/network-scripts/ifcfg-eth0` upravte soubor a přidejte následující text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
    PERSISTENT_DHCLIENT=ano NM_CONTROLLED=ano

1. Ujistěte se, že síťová služba bude spuštěna při spuštění spuštěním následujícího příkazu:

        # sudo systemctl enable network

1. Zaregistrujte své předplatné Red Hat a povolte instalaci balíčků z úložiště RHEL spuštěním následujícího příkazu:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

1. Upravte zaváděcí řádek jádra v konfiguraci grub tak, aby zahrnovala další parametry jádra pro Azure. Chcete-li provést `/etc/default/grub` tuto konfiguraci, otevřete `GRUB_CMDLINE_LINUX` v textovém editoru a upravte parametr. Příklad:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Tento příkaz také zajišťuje, že všechny zprávy konzoly jsou odesílány na první sériový port, který může pomoci podporu Azure s problémy ladění. Příkaz také vypne nové konvence pojmenování RHEL 7 pro síťové karty. Kromě toho doporučujeme odebrat následující parametry:
   
        rhgb quiet crashkernel=auto
   
    Grafické a tiché spuštění není užitečné v cloudovém prostředí, kde chceme, aby všechny protokoly byly odeslány do sériového portu. V případě `crashkernel` potřeby můžete tuto možnost ponechat nakonfigurovanou. Všimněte si, že tento parametr snižuje množství dostupné paměti ve virtuálním počítači o 128 MB nebo více, což může být problematické u menších velikostí virtuálních počítačů.

1. Po dokončení úprav `/etc/default/grub`spusťte následující příkaz pro opětovné sestavení konfigurace grubu:

        # grub2-mkconfig -o /boot/grub2/grub.cfg

1. Přidejte moduly Hyper-V do initramfs.

    Úpravy `/etc/dracut.conf` a přidání obsahu:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Znovu sestavit initramfs:

        # dracut -f -v

1. Odinstalujte cloud-init:

        # yum remove cloud-init

1. Ujistěte se, že je server SSH nainstalován a nakonfigurován tak, aby se spouštěl při spuštění:

        # systemctl enable sshd

    Upravte /etc/ssh/sshd_config tak, aby obsahovala následující řádky:

        PasswordAuthentication yes
        ClientAliveInterval 180

1. Balíček WALinuxAgent `WALinuxAgent-<version>`, byl posunut do úložiště Red Hat extras. Povolte úložiště doplňků spuštěním následujícího příkazu:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Nainstalujte agenta Azure Linux spuštěním následujícího příkazu:

        # yum install WALinuxAgent

    Povolte službu waagent:

        # systemctl enable waagent.service

1. Nevytvářejte odkládací místo na disku operačního systému.

    Azure Linux Agent můžete automaticky nakonfigurovat odkládací prostor pomocí disku místního prostředku, který je připojen k virtuálnímu počítači po zřízení virtuálního počítače v Azure. Všimněte si, že disk místního prostředku je dočasný disk a může být vyprázdněn, pokud je virtuální počítač zrušen. Po instalaci Agenta Azure Linuxv předchozím kroku upravte `/etc/waagent.conf` odpovídajícím způsobem následující parametry:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Zrušení registrace předplatného (v případě potřeby) spuštěním následujícího příkazu:

        # subscription-manager unregister

1. Spusťte následující příkazy, abyste deprovision zanatii virtuálního počítače a připravit ho na zřizování v Azure:

        # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Vypněte virtuální počítač v KVM.

1. Převeďte obraz qcow2 do formátu VHD.

> [!NOTE]
> Existuje známá chyba ve verzích qemu-img >=2.2.1, která má za následek nesprávně formátovaný virtuální pevný disk. Problém byl vyřešen v QEMU 2.6. Doporučuje se používat buď qemu-img 2.2.0 nebo nižší, nebo aktualizovat na 2.6 nebo vyšší. Odkaz: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f qcow2 -O raw rhel-7.4.qcow2 rhel-7.4.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.4.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>Příprava virtuálního počítače se systémem Red Hat od společnosti VMware
### <a name="prerequisites"></a>Požadované součásti
Tato část předpokládá, že jste již nainstalovali virtuální počítač RHEL ve společnosti VMware. Podrobnosti o instalaci operačního systému ve společnosti VMware naleznete v [příručce k instalaci hostovaného operačního systému VMware](https://partnerweb.vmware.com/GOSIG/home.html).

* Při instalaci operačního systému Linux doporučujeme použít standardní oddíly spíše než LVM, což je často výchozí pro mnoho instalací. Tím se zabrání lvm název konflikty s klonovaným virtuálním počítačem, zejména v případě, že disk operačního systému někdy musí být připojen k jinému virtuálnímu počítači pro řešení potíží. LVM nebo RAID lze použít na datových discích, pokud je to preferováno.
* Nekonfigurujte odkládací oddíl na disku operačního systému. Agenta Linuxu můžete nakonfigurovat tak, aby na disku s dočasným prostředkem vytvořil odkládací soubor. Další informace o tom najdete v následujících krocích.
* Při vytváření virtuálního pevného disku vyberte **Uložit virtuální disk jako jeden soubor**.

### <a name="prepare-a-rhel-6-virtual-machine-from-vmware"></a>Příprava virtuálního stroje RHEL 6 od společnosti VMware
1. V RHEL 6 NetworkManager můžete zasahovat agenta Azure Linuxu. Odinstalujte tento balíček spuštěním následujícího příkazu:
   
        # sudo rpm -e --nodeps NetworkManager

1. Vytvořte soubor s názvem **síť** v adresáři /etc/sysconfig/, který obsahuje následující text:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Vytvořte nebo `/etc/sysconfig/network-scripts/ifcfg-eth0` upravte soubor a přidejte následující text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. Přesuňte (nebo odeberte) pravidla udev, abyste se vyhnuli generování statických pravidel pro ethernetové rozhraní. Tato pravidla způsobují problémy při klonování virtuálního počítače v Azure nebo Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Ujistěte se, že síťová služba bude spuštěna při spuštění spuštěním následujícího příkazu:

        # sudo chkconfig network on

1. Zaregistrujte své předplatné Red Hat a povolte instalaci balíčků z úložiště RHEL spuštěním následujícího příkazu:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Balíček WALinuxAgent `WALinuxAgent-<version>`, byl posunut do úložiště Red Hat extras. Povolte úložiště doplňků spuštěním následujícího příkazu:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Upravte zaváděcí řádek jádra v konfiguraci grub tak, aby zahrnovala další parametry jádra pro Azure. Chcete-li to `/etc/default/grub` provést, otevřete v `GRUB_CMDLINE_LINUX` textovém editoru a upravte parametr. Příklad:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"
   
   Tím se také zajistí, že všechny zprávy konzoly jsou odesílány na první sériový port, který může pomoci podporu Azure s problémy ladění. Kromě toho doporučujeme odebrat následující parametry:
   
        rhgb quiet crashkernel=auto
   
    Grafické a tiché spuštění není užitečné v cloudovém prostředí, kde chceme, aby všechny protokoly byly odeslány do sériového portu. V případě `crashkernel` potřeby můžete tuto možnost ponechat nakonfigurovanou. Všimněte si, že tento parametr snižuje množství dostupné paměti ve virtuálním počítači o 128 MB nebo více, což může být problematické u menších velikostí virtuálních počítačů.

1. Přidejte moduly Hyper-V do initramfs:

    Upravte `/etc/dracut.conf`a přidejte následující obsah:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Znovu sestavit initramfs:

        # dracut -f -v

1. Ujistěte se, že je server SSH nainstalován a nakonfigurován tak, aby se spouštěl při spuštění, což je obvykle výchozí. Upravte `/etc/ssh/sshd_config` tak, aby obsahoval následující řádek:

    KlientAliveInterval 180

1. Nainstalujte agenta Azure Linux spuštěním následujícího příkazu:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

1. Nevytvářejte odkládací místo na disku operačního systému.

    Azure Linux Agent můžete automaticky nakonfigurovat odkládací prostor pomocí disku místního prostředku, který je připojen k virtuálnímu počítači po zřízení virtuálního počítače v Azure. Všimněte si, že disk místního prostředku je dočasný disk a může být vyprázdněn, pokud je virtuální počítač zrušen. Po instalaci Agenta Azure Linuxv předchozím kroku upravte `/etc/waagent.conf` odpovídajícím způsobem následující parametry:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Zrušení registrace předplatného (v případě potřeby) spuštěním následujícího příkazu:

        # sudo subscription-manager unregister

1. Spusťte následující příkazy, abyste deprovision zanatii virtuálního počítače a připravit ho na zřizování v Azure:

        # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Vypněte virtuální počítač a převeďte soubor VMDK na soubor VHD.

> [!NOTE]
> Existuje známá chyba ve verzích qemu-img >=2.2.1, která má za následek nesprávně formátovaný virtuální pevný disk. Problém byl vyřešen v QEMU 2.6. Doporučuje se používat buď qemu-img 2.2.0 nebo nižší, nebo aktualizovat na 2.6 nebo vyšší. Odkaz: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f vmdk -O raw rhel-6.9.vmdk rhel-6.9.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-6.9.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-6.9.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-6.9.raw rhel-6.9.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-6.9.raw rhel-6.9.vhd


### <a name="prepare-a-rhel-7-virtual-machine-from-vmware"></a>Příprava virtuálního stroje RHEL 7 od společnosti VMware
1. Vytvořte nebo `/etc/sysconfig/network` upravte soubor a přidejte následující text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Vytvořte nebo `/etc/sysconfig/network-scripts/ifcfg-eth0` upravte soubor a přidejte následující text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
    PERSISTENT_DHCLIENT=ano NM_CONTROLLED=ano

1. Ujistěte se, že síťová služba bude spuštěna při spuštění spuštěním následujícího příkazu:

        # sudo systemctl enable network

1. Zaregistrujte své předplatné Red Hat a povolte instalaci balíčků z úložiště RHEL spuštěním následujícího příkazu:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Upravte zaváděcí řádek jádra v konfiguraci grub tak, aby zahrnovala další parametry jádra pro Azure. Chcete-li provést `/etc/default/grub` tuto změnu, otevřete `GRUB_CMDLINE_LINUX` v textovém editoru a upravte parametr. Příklad:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Tato konfigurace také zajišťuje, že všechny zprávy konzoly jsou odesílány na první sériový port, který může pomoci podporu Azure s problémy ladění. Také vypne nové konvence pojmenování RHEL 7 pro síťové karty. Kromě toho doporučujeme odebrat následující parametry:
   
        rhgb quiet crashkernel=auto
   
    Grafické a tiché spuštění není užitečné v cloudovém prostředí, kde chceme, aby všechny protokoly byly odeslány do sériového portu. V případě `crashkernel` potřeby můžete tuto možnost ponechat nakonfigurovanou. Všimněte si, že tento parametr snižuje množství dostupné paměti ve virtuálním počítači o 128 MB nebo více, což může být problematické u menších velikostí virtuálních počítačů.

1. Po dokončení úprav `/etc/default/grub`spusťte následující příkaz pro opětovné sestavení konfigurace grubu:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

1. Přidejte moduly Hyper-V do initramfs.

    Upravit `/etc/dracut.conf`, přidat obsah:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Znovu sestavit initramfs:

        # dracut -f -v

1. Ujistěte se, že je server SSH nainstalován a nakonfigurován tak, aby se spouštěl při spuštění. Toto nastavení je obvykle výchozí. Upravte `/etc/ssh/sshd_config` tak, aby obsahoval následující řádek:

        ClientAliveInterval 180

1. Balíček WALinuxAgent `WALinuxAgent-<version>`, byl posunut do úložiště Red Hat extras. Povolte úložiště doplňků spuštěním následujícího příkazu:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Nainstalujte agenta Azure Linux spuštěním následujícího příkazu:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

1. Nevytvářejte odkládací místo na disku operačního systému.

    Azure Linux Agent můžete automaticky nakonfigurovat odkládací prostor pomocí disku místního prostředku, který je připojen k virtuálnímu počítači po zřízení virtuálního počítače v Azure. Všimněte si, že disk místního prostředku je dočasný disk a může být vyprázdněn, pokud je virtuální počítač zrušen. Po instalaci Agenta Azure Linuxv předchozím kroku upravte `/etc/waagent.conf` odpovídajícím způsobem následující parametry:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Pokud chcete zrušit registraci předplatného, spusťte následující příkaz:

        # sudo subscription-manager unregister

1. Spusťte následující příkazy, abyste deprovision zanatii virtuálního počítače a připravit ho na zřizování v Azure:

        # Note: if you are migrating a specific virtual machine and do not wish to create a generalized image,
        # skip the deprovision step
        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Vypněte virtuální počítač a převeďte soubor VMDK do formátu Virtuálního pevného disku.

> [!NOTE]
> Existuje známá chyba ve verzích qemu-img >=2.2.1, která má za následek nesprávně formátovaný virtuální pevný disk. Problém byl vyřešen v QEMU 2.6. Doporučuje se používat buď qemu-img 2.2.0 nebo nižší, nebo aktualizovat na 2.6 nebo vyšší. Odkaz: https://bugs.launchpad.net/qemu/+bug/1490611.
>


    First convert the image to raw format:

        # qemu-img convert -f vmdk -O raw rhel-7.4.vmdk rhel-7.4.raw

    Make sure that the size of the raw image is aligned with 1 MB. Otherwise, round up the size to align with 1 MB:

        # MB=$((1024*1024))
        # size=$(qemu-img info -f raw --output json "rhel-7.4.raw" | \
          gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

        # rounded_size=$((($size/$MB + 1)*$MB))
        # qemu-img resize rhel-7.4.raw $rounded_size

    Convert the raw disk to a fixed-sized VHD:

        # qemu-img convert -f raw -o subformat=fixed -O vpc rhel-7.4.raw rhel-7.4.vhd

    Or, with qemu version **2.6+** include the `force_size` option:

        # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc rhel-7.4.raw rhel-7.4.vhd


## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>Příprava virtuálního počítače založeného na Red Hat u ISO pomocí automaticky spustit soubor
### <a name="prepare-a-rhel-7-virtual-machine-from-a-kickstart-file"></a>Příprava virtuálního stroje RHEL 7 ze souboru kickstart

1.  Vytvořte soubor kickstart, který obsahuje následující obsah, a uložte soubor. Podrobnosti o instalaci kickstartu naleznete v [průvodci instalací kickstartu](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

        # Kickstart for provisioning a RHEL 7 Azure VM

        # System authorization information
          auth --enableshadow --passalgo=sha512

        # Use graphical install
        text

        # Do not run the Setup Agent on first boot
        firstboot --disable

        # Keyboard layouts
        keyboard --vckeymap=us --xlayouts='us'

        # System language
        lang en_US.UTF-8

        # Network information
        network  --bootproto=dhcp

        # Root password
        rootpw --plaintext "to_be_disabled"

        # System services
        services --enabled="sshd,waagent,NetworkManager"

        # System timezone
        timezone Etc/UTC --isUtc --ntpservers 0.rhel.pool.ntp.org,1.rhel.pool.ntp.org,2.rhel.pool.ntp.org,3.rhel.pool.ntp.org

        # Partition clearing information
        clearpart --all --initlabel

        # Clear the MBR
        zerombr

        # Disk partitioning information
        part /boot --fstype="xfs" --size=500
        part / --fstyp="xfs" --size=1 --grow --asprimary

        # System bootloader configuration
        bootloader --location=mbr

        # Firewall configuration
        firewall --disabled

        # Enable SELinux
        selinux --enforcing

        # Don't configure X
        skipx

        # Power down the machine after install
        poweroff

        %packages
        @base
        @console-internet
        chrony
        sudo
        parted
        -dracut-config-rescue

        %end

        %post --log=/var/log/anaconda/post-install.log

        #!/bin/bash

        # Register Red Hat Subscription
        subscription-manager register --username=XXX --password=XXX --auto-attach --force

        # Install latest repo update
        yum update -y

        # Enable extras repo
        subscription-manager repos --enable=rhel-7-server-extras-rpms

        # Install WALinuxAgent
        yum install -y WALinuxAgent

        # Unregister Red Hat subscription
        subscription-manager unregister

        # Enable waaagent at boot-up
        systemctl enable waagent

        # Disable the root account
        usermod root -p '!!'

        # Configure swap in WALinuxAgent
        sed -i 's/^\(ResourceDisk\.EnableSwap\)=[Nn]$/\1=y/g' /etc/waagent.conf
        sed -i 's/^\(ResourceDisk\.SwapSizeMB\)=[0-9]*$/\1=2048/g' /etc/waagent.conf

        # Set the cmdline
        sed -i 's/^\(GRUB_CMDLINE_LINUX\)=".*"$/\1="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"/g' /etc/default/grub

        # Enable SSH keepalive
        sed -i 's/^#\(ClientAliveInterval\).*$/\1 180/g' /etc/ssh/sshd_config

        # Build the grub cfg
        grub2-mkconfig -o /boot/grub2/grub.cfg

        # Configure network
        cat << EOF > /etc/sysconfig/network-scripts/ifcfg-eth0
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
    PERSISTENT_DHCLIENT=ano NM_CONTROLLED=ano EOF

        # Deprovision and prepare for Azure if you are creating a generalized image
        waagent -force -deprovision

        %end

1. Umístěte soubor kickstart, kde k němu bude mít instalační systém přístup.

1. Ve Správci technologie Hyper-V vytvořte nový virtuální počítač. Na stránce **Připojit virtuální pevný disk** vyberte **později připojit virtuální pevný disk**a dokončete Průvodce novým virtuálním počítačem.

1. Otevřete nastavení virtuálního počítače:

    a.  Připojte k virtuálnímu počítači nový virtuální pevný disk. Nezapomeňte vybrat **formát v hd** a **pevnou velikost**.

    b.  Připojte instalační ISO k jednotce DVD.

    c.  Nastavte systém BIOS tak, aby se spouštěl z disku CD.

1. Spusťte virtuální počítač. Po spuštění průvodce instalací nakonfigurujte možnosti spouštění stisknutím **klávesy Tab.**

1. Zadejte `inst.ks=<the location of the kickstart file>` na konec možností spuštění a stiskněte **Enter**.

1. Počkejte na dokončení instalace. Po dokončení se virtuální počítač automaticky vypne. Virtuální disk Linux ukterýzdvižený disk linuxu je teď připravený k nahrání do Azure.

## <a name="known-issues"></a>Známé problémy
### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>Ovladač Hyper-V nelze zahrnout do počátečního disku RAM při použití hypervisoru jiného než Hyper-V

V některých případech nemusí instalační programy Linuxu obsahovat ovladače pro hyper-V v počátečním disku RAM (initrd nebo initramfs), pokud Linux nezjistí, že je spuštěn v prostředí Hyper-V.

Pokud používáte jiný virtualizační systém (to znamená VirtualBox, Xen atd.) k přípravě bitové kopie Linuxu, možná budete muset znovu sestavit initrd, abyste zajistili, že alespoň hv_vmbus a hv_storvsc moduly jádra jsou k dispozici na počátečním disku RAM. Jedná se o známý problém alespoň v systémech, které jsou založeny na distribuci upstream Red Hat.

Chcete-li tento problém vyřešit, přidejte moduly Hyper-V do initramfs a znovu jej znovu vytvořit:

Upravte `/etc/dracut.conf`a přidejte následující obsah:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

Znovu sestavit initramfs:

        # dracut -f -v

Další podrobnosti naleznete v informacích o [obnově initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Další kroky
* Teď jste připraveni použít virtuální pevný disk Red Hat Enterprise Linux k vytvoření nových virtuálních počítačů v Azure. Pokud je to poprvé, co nahráváte soubor .vhd do Azure, přečtěte si téma [Vytvoření virtuálního počítače s Linuxem z vlastního disku](upload-vhd.md#option-1-upload-a-vhd).
* Další podrobnosti o hypervisorech, které jsou certifikovány pro provoz Red Hat Enterprise Linux, najdete [na webových stránkách Red Hat](https://access.redhat.com/certified-hypervisors).
* Další informace o používání obrázků RHEL BYOS připravených pro produkční prostředí najdete na stránce dokumentace pro [BYOS](../workloads/redhat/byos.md).
