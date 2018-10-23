---
title: Vytvoření a nahrání VHD Red Hat Enterprise Linux pro použití v Azure | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit a nahrát Azure virtuálního pevného disku (VHD), který obsahuje operační systém Red Hat Linux.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: 6c6b8f72-32d3-47fa-be94-6cb54537c69f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/27/2018
ms.author: szark
ms.openlocfilehash: 43253423e3a27a61000c3f93868dd8b42809b7ae
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49650158"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure"></a>Příprava virtuálního počítače založeného na Red Hat pro Azure
V tomto článku se dozvíte, jak připravit virtuální počítač s Red Hat Enterprise Linux (RHEL) pro použití v Azure. Verze RHEL, které jsou popsané v tomto článku jsou 6.7 + a 7.1 +. Hypervisory pro přípravu, které jsou popsané v tomto článku jsou virtuální počítače Hyper-V, na základě jádra (KVM) a VMware. Další informace o požadavky zakládající nárok pro účast v programu Red Hat Cloud Access najdete v tématu [Red Hat Cloud Access web](http://www.redhat.com/en/technologies/cloud-computing/cloud-access) a [systémem RHEL v Azure](https://access.redhat.com/ecosystem/ccsp/microsoft-azure).

## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Příprava virtuálního počítače založeného na Red Hat ve Správci technologie Hyper-V

### <a name="prerequisites"></a>Požadavky
V této části se předpokládá, že jste už soubor ISO získali z webu Red Hat a nainstalovali RHEL image na virtuální pevný disk (VHD). Další podrobnosti o tom, jak použít Správce technologie Hyper-V k instalaci image operačního systému najdete v tématu [instalace Role Hyper-V a konfigurace virtuálního počítače](http://technet.microsoft.com/library/hh846766.aspx).

**Poznámky k instalaci RHEL**

* Azure nepodporuje formát VHDX. Azure podporuje pouze dlouhodobý virtuálního pevného disku. Převést disk na formát virtuálního pevného disku můžete použít Správce technologie Hyper-V nebo pomocí rutiny convert-vhd. Pokud používáte VirtualBox, vyberte **pevnou velikost** na rozdíl od výchozí dynamicky přidělené možnost při vytváření disku.
* Azure podporuje pouze virtuální počítače generace 1. Virtuální počítač generace 1 můžete převést do formátu souboru virtuálního pevného disku VHDX a dynamicky se zvětšující na disk pevné velikosti. Nelze změnit generaci virtuálního počítače. Další informace najdete v tématu [bych si měl vytvořit virtuální počítač generace 1 nebo 2 v Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
* Maximální velikost, která je pro virtuální pevný disk je 1,023 GB.
* Správce logických svazků (LVM) je podporováno a může být použita na disk s operačním systémem nebo datové disky ve virtuálních počítačích Azure. Ale obecně se doporučuje použít standardní oddílů na disku s operačním systémem místo LVM. Zabráníte tak LVM název je v konfliktu s naklonované virtuální počítače, zejména v případě, že byste někdy potřebovali připojte disk s operačním systémem k jinému identické virtuálnímu počítači pro řešení potíží. Viz také [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) a [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) dokumentaci.
* Podpora jádra pro připojování systémů souborů univerzální formát disku (UDF) je povinný. Při prvním spuštění počítače v Azure ve formátu UDF média, který je připojen k hosta předává konfigurace zřizování na virtuálním počítači s Linuxem. Azure Linux Agent musí být schopen připojit a načíst jeho konfiguraci a zřízení virtuálního počítače v systému souborů UDF.
* Neprovádějte konfiguraci odkládací oddíl na disk s operačním systémem. Chcete-li vytvořit odkládací soubor na disku dočasný prostředek, který lze nastavit agenta pro Linux.  Další informace najdete v následujících krocích.
* Všechny virtuální pevné disky v Azure musí mít virtuální velikost, zarovnání na 1MB. Při převodu z nezpracované disku do virtuálního pevného disku je nutné zajistit, že velikost nezpracovaných disku je násobkem 1MB před převodem. Další podrobnosti najdete v níže uvedeném postupu. Viz také [poznámky k instalaci Linux](create-upload-generic.md#general-linux-installation-notes) Další informace.

### <a name="prepare-a-rhel-6-virtual-machine-from-hyper-v-manager"></a>Příprava virtuálního počítače RHEL 6 ze Správce technologie Hyper-V

1. Ve Správci technologie Hyper-V vyberte virtuální počítač.

1. Klikněte na tlačítko **připojit** otevřete okno konzoly pro virtuální počítač.

1. V systému RHEL 6 NetworkManager může vést k potížím s agentem Azure Linux. Odinstaluje tento balíček spuštěním následujícího příkazu:
   
        # sudo rpm -e --nodeps NetworkManager

1. Vytvoření nebo úpravě `/etc/sysconfig/network` a přidejte následující text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Vytvoření nebo úpravě `/etc/sysconfig/network-scripts/ifcfg-eth0` a přidejte následující text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. (Nebo odebrat) procesu udev pravidla pro zabránění generování statická pravidla pro rozhraní sítě Ethernet. Tato pravidla způsobit problémy při klonování virtuálního počítače v Microsoft Azure nebo technologie Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules
        
        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Ujistěte se, že síťové služby se spustí při spuštění spuštěním následujícího příkazu:

        # sudo chkconfig network on

1. Registrace předplatného Red Hat, jak povolit instalaci balíčků z úložiště RHEL spuštěním následujícího příkazu:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Balíček WALinuxAgent `WALinuxAgent-<version>`, byly nahrány do funkce úložiště Red Hat. Povolte funkce úložiště spuštěním následujícího příkazu:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Upravte řádek pro spuštění jádra v konfiguraci grub tak, aby zahrnout další jádra parametry pro Azure. Chcete-li provést tuto změnu, otevřete `/boot/grub/menu.lst` v textovém editoru a ujistěte se, že výchozí jádra zahrnuje následující parametry:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Tím se také zajistí, že jsou všechny konzoly zprávy odeslané do první sériového portu, který vám může pomoct Azure odborné pomoci s laděním problémů.
    
    Kromě toho doporučujeme, abyste odebrali následující parametry:
    
        rhgb quiet crashkernel=auto
    
    Grafické a quiet spouštěcí nejsou užitečné v cloudovém prostředí, ve kterém chceme, všech protokolů k odeslání do sériového portu.  Můžete nechat `crashkernel` možnost nakonfigurovat v případě potřeby. Všimněte si, že tento parametr snižuje množství dostupné paměti ve virtuálním počítači 128 MB a víc. Tato konfigurace může být problematické u menší velikosti virtuálních počítačů.


1. Zajistěte, aby server (SSH secure shell) je nainstalovaná a nakonfigurovaná tak, aby spuštění při spuštění, což je obvykle výchozí. Upravte /etc/ssh/sshd_config, přidejte následující řádek:

        ClientAliveInterval 180

1. Instalace agenta Azure Linux spuštěním následujícího příkazu:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

    Instaluje se balíček WALinuxAgent odebere NetworkManager a balíčky NetworkManager gnome Pokud již nebyly odebrány v kroku 3.

1. Nevytvářejte odkládacího prostoru na disku s operačním systémem.

    Azure Linux Agent mohou automaticky konfigurovat velikosti odkládacího souboru s použitím disku místního prostředku, který je připojen k virtuálnímu počítači po zřízení virtuálního počítače v Azure. Mějte na paměti, disku místního prostředku je dočasný disk a zda ho může vyprázdnit při zřízení virtuálního počítače. Po instalaci agenta Azure Linux v předchozím kroku, upravte následující parametry v /etc/waagent.conf odpovídajícím způsobem:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Zrušit registraci předplatného (v případě potřeby) spuštěním následujícího příkazu:

        # sudo subscription-manager unregister

1. Spusťte následující příkaz pro zrušení zřízení virtuálního počítače a připravte je ke zřizování v Azure:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Klikněte na tlačítko **akce** > **vypnout** ve Správci technologie Hyper-V. Vašeho linuxového virtuálního pevného disku je teď připravený k nahrání do Azure.


### <a name="prepare-a-rhel-7-virtual-machine-from-hyper-v-manager"></a>Příprava virtuálního počítače RHEL 7 ve Správci technologie Hyper-V

1. Ve Správci technologie Hyper-V vyberte virtuální počítač.

1. Klikněte na tlačítko **připojit** otevřete okno konzoly pro virtuální počítač.

1. Vytvoření nebo úpravě `/etc/sysconfig/network` a přidejte následující text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Vytvoření nebo úpravě `/etc/sysconfig/network-scripts/ifcfg-eth0` a přidejte následující text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

1. Ujistěte se, že síťové služby se spustí při spuštění spuštěním následujícího příkazu:

        # sudo systemctl enable network

1. Registrace předplatného Red Hat, jak povolit instalaci balíčků z úložiště RHEL spuštěním následujícího příkazu:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Upravte řádek pro spuštění jádra v konfiguraci grub tak, aby zahrnout další jádra parametry pro Azure. Chcete-li provést tuto změnu, otevřete `/etc/default/grub` v textovém editoru a úpravy `GRUB_CMDLINE_LINUX` parametru. Příklad:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Tím se také zajistí, že jsou všechny konzoly zprávy odeslané do první sériového portu, který vám může pomoct Azure odborné pomoci s laděním problémů. Tato konfigurace vypne také nové RHEL 7 zásady vytváření názvů pro síťové adaptéry. Kromě toho doporučujeme, abyste odebrali následující parametry:
   
        rhgb quiet crashkernel=auto
   
    Grafické a quiet spouštěcí nejsou užitečné v cloudovém prostředí, ve kterém chceme, všech protokolů k odeslání do sériového portu. Můžete nechat `crashkernel` možnost nakonfigurovat v případě potřeby. Všimněte si, že tento parametr snižuje množství dostupné paměti na virtuální počítač o 128 MB nebo víc, který může být problematické u menší velikosti virtuálních počítačů.

1. Po dokončení úprav `/etc/default/grub`, spusťte následující příkaz k opětovnému sestavení konfigurace grub:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

1. Ujistěte se, že je nainstalován a nakonfigurován na spuštění při spuštění, což je obvykle výchozí SSH server. Upravit `/etc/ssh/sshd_config` zahrnout následující řádek:

        ClientAliveInterval 180

1. Balíček WALinuxAgent `WALinuxAgent-<version>`, byly nahrány do funkce úložiště Red Hat. Povolte funkce úložiště spuštěním následujícího příkazu:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Instalace agenta Azure Linux spuštěním následujícího příkazu:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

1. Nevytvářejte odkládacího prostoru na disku s operačním systémem.

    Azure Linux Agent mohou automaticky konfigurovat velikosti odkládacího souboru s použitím disku místního prostředku, který je připojen k virtuálnímu počítači po zřízení virtuálního počítače v Azure. Mějte na paměti, disku místního prostředku je dočasný disk a může být vyprázdněna při zřízení virtuálního počítače. Po instalaci agenta Azure Linux v předchozím kroku, upravte následující parametry v `/etc/waagent.conf` odpovídajícím způsobem:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Pokud chcete zrušit předplatné, spusťte následující příkaz:

        # sudo subscription-manager unregister

1. Spusťte následující příkaz pro zrušení zřízení virtuálního počítače a připravte je ke zřizování v Azure:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Klikněte na tlačítko **akce** > **vypnout** ve Správci technologie Hyper-V. Vašeho linuxového virtuálního pevného disku je teď připravený k nahrání do Azure.


## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Příprava virtuálního počítače založeného na Red Hat z KVM
### <a name="prepare-a-rhel-6-virtual-machine-from-kvm"></a>Příprava virtuálního počítače RHEL 6 z KVM

1. Stáhněte z webu Red Hat KVM bitové kopie systému RHEL 6.

1. Nastavte kořenové heslo.

    Generovat šifrované heslo a zkopírujte výstup příkazu:

        # openssl passwd -1 changeme

    Nastavte kořenové heslo s guestfish:
        
        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Změna kořenového uživatele z druhé pole "!" pro šifrované heslo.

1. Vytvoření virtuálního počítače z qcow2 image v KVM. Nastavte typ disku na **qcow2**a nastavte model zařízení rozhraní virtuální sítě na **virtio**. Potom spusťte virtuální počítač a přihlásit jako uživatel root.

1. Vytvoření nebo úpravě `/etc/sysconfig/network` a přidejte následující text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Vytvoření nebo úpravě `/etc/sysconfig/network-scripts/ifcfg-eth0` a přidejte následující text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. (Nebo odebrat) procesu udev pravidla pro zabránění generování statická pravidla pro rozhraní sítě Ethernet. Tato pravidla způsobit problémy při klonování virtuálního počítače v Azure nebo technologie Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Ujistěte se, že síťové služby se spustí při spuštění spuštěním následujícího příkazu:

        # chkconfig network on

1. Registrace předplatného Red Hat, jak povolit instalaci balíčků z úložiště RHEL spuštěním následujícího příkazu:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

1. Upravte řádek pro spuštění jádra v konfiguraci grub tak, aby zahrnout další jádra parametry pro Azure. Chcete-li provést tuto konfiguraci, otevřete `/boot/grub/menu.lst` v textovém editoru a ujistěte se, že výchozí jádra zahrnuje následující parametry:
    
        console=ttyS0 earlyprintk=ttyS0 rootdelay=300
    
    Tím se také zajistí, že jsou všechny konzoly zprávy odeslané do první sériového portu, který vám může pomoct Azure odborné pomoci s laděním problémů.
    
    Kromě toho doporučujeme, abyste odebrali následující parametry:
    
        rhgb quiet crashkernel=auto
    
    Grafické a quiet spouštěcí nejsou užitečné v cloudovém prostředí, ve kterém chceme, všech protokolů k odeslání do sériového portu. Můžete nechat `crashkernel` možnost nakonfigurovat v případě potřeby. Všimněte si, že tento parametr snižuje množství dostupné paměti na virtuální počítač o 128 MB nebo víc, který může být problematické u menší velikosti virtuálních počítačů.


1. Přidání modulů technologie Hyper-V do initramfs:  

    Upravit `/etc/dracut.conf`a přidejte následující obsah:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Znovu sestavte initramfs:

        # dracut -f -v

1. Odinstalace cloud-init:

        # yum remove cloud-init

1. Zajistěte, aby SSH server je nainstalován a nakonfigurován na spuštění při spuštění:

        # chkconfig sshd on

    Upravte /etc/ssh/sshd_config zahrnout následující řádky:

        PasswordAuthentication yes
        ClientAliveInterval 180

1. Balíček WALinuxAgent `WALinuxAgent-<version>`, byly nahrány do funkce úložiště Red Hat. Povolte funkce úložiště spuštěním následujícího příkazu:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Instalace agenta Azure Linux spuštěním následujícího příkazu:

        # yum install WALinuxAgent

        # chkconfig waagent on

1. Azure Linux Agent mohou automaticky konfigurovat velikosti odkládacího souboru s použitím disku místního prostředku, který je připojen k virtuálnímu počítači po zřízení virtuálního počítače v Azure. Mějte na paměti, disku místního prostředku je dočasný disk a může být vyprázdněna při zřízení virtuálního počítače. Po instalaci agenta Azure Linux v předchozím kroku, upravte následující parametry v **/etc/waagent.conf** odpovídajícím způsobem:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Zrušit registraci předplatného (v případě potřeby) spuštěním následujícího příkazu:

        # subscription-manager unregister

1. Spusťte následující příkaz pro zrušení zřízení virtuálního počítače a připravte je ke zřizování v Azure:

        # waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Vypnete virtuální počítač v KVM.

1. Obrázek qcow2 převeďte na formát virtuálního pevného disku.

> [!NOTE]
> Je známého problému v qemu img verze > = 2.2.1, jejímž výsledkem nesprávně formátovaná VHD. Problém byl vyřešen ve verzi 2.6 QEMU. Doporučuje se použít qemu-img 2.2.0 nebo nižší ani aktualizovat na 2.6 nebo novější. Referenční dokumentace: https://bugs.launchpad.net/qemu/+bug/1490611.
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

        
### <a name="prepare-a-rhel-7-virtual-machine-from-kvm"></a>Příprava virtuálního počítače RHEL 7 z KVM

1. Stáhněte z webu Red Hat KVM image RHEL 7. Tento postup používá RHEL 7 stejně jako v příkladu.

1. Nastavte kořenové heslo.

    Generovat šifrované heslo a zkopírujte výstup příkazu:

        # openssl passwd -1 changeme

    Nastavte kořenové heslo s guestfish:

        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Změna kořenového uživatele z druhé pole "!" pro šifrované heslo.

1. Vytvoření virtuálního počítače z qcow2 image v KVM. Nastavte typ disku na **qcow2**a nastavte model zařízení rozhraní virtuální sítě na **virtio**. Potom spusťte virtuální počítač a přihlásit jako uživatel root.

1. Vytvoření nebo úpravě `/etc/sysconfig/network` a přidejte následující text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Vytvoření nebo úpravě `/etc/sysconfig/network-scripts/ifcfg-eth0` a přidejte následující text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

1. Ujistěte se, že síťové služby se spustí při spuštění spuštěním následujícího příkazu:

        # sudo systemctl enable network

1. Registrace předplatného Red Hat povolit instalaci balíčků z úložiště RHEL spuštěním následujícího příkazu:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

1. Upravte řádek pro spuštění jádra v konfiguraci grub tak, aby zahrnout další jádra parametry pro Azure. Chcete-li provést tuto konfiguraci, otevřete `/etc/default/grub` v textovém editoru a úpravy `GRUB_CMDLINE_LINUX` parametru. Příklad:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Tento příkaz také zajišťuje, že jsou všechny konzoly zprávy odeslané do první sériového portu, který vám může pomoct Azure odborné pomoci s laděním problémů. Příkaz vypne také nové RHEL 7 zásady vytváření názvů pro síťové adaptéry. Kromě toho doporučujeme, abyste odebrali následující parametry:
   
        rhgb quiet crashkernel=auto
   
    Grafické a quiet spouštěcí nejsou užitečné v cloudovém prostředí, ve kterém chceme, všech protokolů k odeslání do sériového portu. Můžete nechat `crashkernel` možnost nakonfigurovat v případě potřeby. Všimněte si, že tento parametr snižuje množství dostupné paměti na virtuální počítač o 128 MB nebo víc, který může být problematické u menší velikosti virtuálních počítačů.

1. Po dokončení úprav `/etc/default/grub`, spusťte následující příkaz k opětovnému sestavení konfigurace grub:

        # grub2-mkconfig -o /boot/grub2/grub.cfg

1. Přidejte do initramfs moduly Hyper-V.

    Upravit `/etc/dracut.conf` a přidat obsah:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Znovu sestavte initramfs:

        # dracut -f -v

1. Odinstalace cloud-init:

        # yum remove cloud-init

1. Zajistěte, aby SSH server je nainstalován a nakonfigurován na spuštění při spuštění:

        # systemctl enable sshd

    Upravte /etc/ssh/sshd_config zahrnout následující řádky:

        PasswordAuthentication yes
        ClientAliveInterval 180

1. Balíček WALinuxAgent `WALinuxAgent-<version>`, byly nahrány do funkce úložiště Red Hat. Povolte funkce úložiště spuštěním následujícího příkazu:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Instalace agenta Azure Linux spuštěním následujícího příkazu:

        # yum install WALinuxAgent

    Povolte službu waagent:

        # systemctl enable waagent.service

1. Nevytvářejte odkládacího prostoru na disku s operačním systémem.

    Azure Linux Agent mohou automaticky konfigurovat velikosti odkládacího souboru s použitím disku místního prostředku, který je připojen k virtuálnímu počítači po zřízení virtuálního počítače v Azure. Mějte na paměti, disku místního prostředku je dočasný disk a může být vyprázdněna při zřízení virtuálního počítače. Po instalaci agenta Azure Linux v předchozím kroku, upravte následující parametry v `/etc/waagent.conf` odpovídajícím způsobem:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Zrušit registraci předplatného (v případě potřeby) spuštěním následujícího příkazu:

        # subscription-manager unregister

1. Spusťte následující příkaz pro zrušení zřízení virtuálního počítače a připravte je ke zřizování v Azure:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Vypnete virtuální počítač v KVM.

1. Obrázek qcow2 převeďte na formát virtuálního pevného disku.

> [!NOTE]
> Je známého problému v qemu img verze > = 2.2.1, jejímž výsledkem nesprávně formátovaná VHD. Problém byl vyřešen ve verzi 2.6 QEMU. Doporučuje se použít qemu-img 2.2.0 nebo nižší ani aktualizovat na 2.6 nebo novější. Referenční dokumentace: https://bugs.launchpad.net/qemu/+bug/1490611.
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


## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>Příprava virtuálního počítače založeného na Red Hat z VMware
### <a name="prerequisites"></a>Požadavky
V této části se předpokládá, že jste už nainstalovali virtuální počítač s RHEL ve službě VMware. Podrobnosti o postupu při instalaci operačního systému v prostředí VMware najdete v tématu [Průvodce instalací operačního systému hosta VMware](http://partnerweb.vmware.com/GOSIG/home.html).

* Při instalaci operačního systému Linux, doporučujeme použít standardní oddíly spíše než LVM, což je často na výchozí hodnoty pro mnoho zařízení. Tím se vyhnete LVM název je v konfliktu s naklonovaný virtuální počítač, zejména v případě, že disk s operačním systémem nikdy musí být připojen k jinému virtuálnímu počítači pro řešení potíží. LVM nebo RAID je možné pro datové disky, pokud tomu dávají přednost.
* Neprovádějte konfiguraci odkládací oddíl na disk s operačním systémem. Můžete nakonfigurovat agenta pro Linux Chcete-li vytvořit odkládací soubor na disku dočasných prostředků. Další informace najdete v následujících kroků.
* Při vytváření virtuálního pevného disku, vyberte **Store virtuální disk jako jeden soubor**.

### <a name="prepare-a-rhel-6-virtual-machine-from-vmware"></a>Příprava systému RHEL 6 virtuálního počítače z VMware
1. V systému RHEL 6 NetworkManager může vést k potížím s agentem Azure Linux. Odinstaluje tento balíček spuštěním následujícího příkazu:
   
        # sudo rpm -e --nodeps NetworkManager

1. Vytvořte soubor s názvem **sítě** v/etc/sysconfig/adresáře, který obsahuje následující text:

        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Vytvoření nebo úpravě `/etc/sysconfig/network-scripts/ifcfg-eth0` a přidejte následující text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no

1. (Nebo odebrat) procesu udev pravidla pro zabránění generování statická pravidla pro rozhraní sítě Ethernet. Tato pravidla způsobit problémy při klonování virtuálního počítače v Azure nebo technologie Hyper-V:

        # sudo ln -s /dev/null /etc/udev/rules.d/75-persistent-net-generator.rules

        # sudo rm -f /etc/udev/rules.d/70-persistent-net.rules

1. Ujistěte se, že síťové služby se spustí při spuštění spuštěním následujícího příkazu:

        # sudo chkconfig network on

1. Registrace předplatného Red Hat, jak povolit instalaci balíčků z úložiště RHEL spuštěním následujícího příkazu:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Balíček WALinuxAgent `WALinuxAgent-<version>`, byly nahrány do funkce úložiště Red Hat. Povolte funkce úložiště spuštěním následujícího příkazu:

        # subscription-manager repos --enable=rhel-6-server-extras-rpms

1. Upravte řádek pro spuštění jádra v konfiguraci grub tak, aby zahrnout další jádra parametry pro Azure. Chcete-li to provést, otevřete `/etc/default/grub` v textovém editoru a úpravy `GRUB_CMDLINE_LINUX` parametru. Příklad:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"
   
   Tím se také zajistí, že jsou všechny konzoly zprávy odeslané do první sériového portu, který vám může pomoct Azure odborné pomoci s laděním problémů. Kromě toho doporučujeme, abyste odebrali následující parametry:
   
        rhgb quiet crashkernel=auto
   
    Grafické a quiet spouštěcí nejsou užitečné v cloudovém prostředí, ve kterém chceme, všech protokolů k odeslání do sériového portu. Můžete nechat `crashkernel` možnost nakonfigurovat v případě potřeby. Všimněte si, že tento parametr snižuje množství dostupné paměti na virtuální počítač o 128 MB nebo víc, který může být problematické u menší velikosti virtuálních počítačů.

1. Přidání modulů technologie Hyper-V do initramfs:

    Upravit `/etc/dracut.conf`a přidejte následující obsah:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Znovu sestavte initramfs:

        # dracut -f -v

1. Ujistěte se, že je nainstalován a nakonfigurován na spuštění při spuštění, což je obvykle výchozí SSH server. Upravit `/etc/ssh/sshd_config` zahrnout následující řádek:

    ClientAliveInterval 180

1. Instalace agenta Azure Linux spuštěním následujícího příkazu:

        # sudo yum install WALinuxAgent

        # sudo chkconfig waagent on

1. Nevytvářejte odkládacího prostoru na disku s operačním systémem.

    Azure Linux Agent mohou automaticky konfigurovat velikosti odkládacího souboru s použitím disku místního prostředku, který je připojen k virtuálnímu počítači po zřízení virtuálního počítače v Azure. Mějte na paměti, disku místního prostředku je dočasný disk a může být vyprázdněna při zřízení virtuálního počítače. Po instalaci agenta Azure Linux v předchozím kroku, upravte následující parametry v `/etc/waagent.conf` odpovídajícím způsobem:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Zrušit registraci předplatného (v případě potřeby) spuštěním následujícího příkazu:

        # sudo subscription-manager unregister

1. Spusťte následující příkaz pro zrušení zřízení virtuálního počítače a připravte je ke zřizování v Azure:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Vypnout virtuální počítač a převeďte soubor VMDK na soubor VHD.

> [!NOTE]
> Je známého problému v qemu img verze > = 2.2.1, jejímž výsledkem nesprávně formátovaná VHD. Problém byl vyřešen ve verzi 2.6 QEMU. Doporučuje se použít qemu-img 2.2.0 nebo nižší ani aktualizovat na 2.6 nebo novější. Referenční dokumentace: https://bugs.launchpad.net/qemu/+bug/1490611.
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


### <a name="prepare-a-rhel-7-virtual-machine-from-vmware"></a>Příprava virtuálního počítače RHEL 7 z VMware
1. Vytvoření nebo úpravě `/etc/sysconfig/network` a přidejte následující text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

1. Vytvoření nebo úpravě `/etc/sysconfig/network-scripts/ifcfg-eth0` a přidejte následující text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

1. Ujistěte se, že síťové služby se spustí při spuštění spuštěním následujícího příkazu:

        # sudo systemctl enable network

1. Registrace předplatného Red Hat, jak povolit instalaci balíčků z úložiště RHEL spuštěním následujícího příkazu:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

1. Upravte řádek pro spuštění jádra v konfiguraci grub tak, aby zahrnout další jádra parametry pro Azure. Chcete-li provést tuto změnu, otevřete `/etc/default/grub` v textovém editoru a úpravy `GRUB_CMDLINE_LINUX` parametru. Příklad:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Tato konfigurace taky zajišťuje, že jsou všechny konzoly zprávy odeslané do první sériového portu, který vám může pomoct Azure odborné pomoci s laděním problémů. Také vypne nové RHEL 7 zásady vytváření názvů pro síťové adaptéry. Kromě toho doporučujeme, abyste odebrali následující parametry:
   
        rhgb quiet crashkernel=auto
   
    Grafické a quiet spouštěcí nejsou užitečné v cloudovém prostředí, ve kterém chceme, všech protokolů k odeslání do sériového portu. Můžete nechat `crashkernel` možnost nakonfigurovat v případě potřeby. Všimněte si, že tento parametr snižuje množství dostupné paměti na virtuální počítač o 128 MB nebo víc, který může být problematické u menší velikosti virtuálních počítačů.

1. Po dokončení úprav `/etc/default/grub`, spusťte následující příkaz k opětovnému sestavení konfigurace grub:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

1. Přidejte do initramfs moduly Hyper-V.

    Upravit `/etc/dracut.conf`, přidat obsah:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

    Znovu sestavte initramfs:

        # dracut -f -v

1. Zajistěte, aby SSH server je nainstalován a nakonfigurován na spuštění při spuštění. Toto nastavení je obvykle výchozí. Upravit `/etc/ssh/sshd_config` zahrnout následující řádek:

        ClientAliveInterval 180

1. Balíček WALinuxAgent `WALinuxAgent-<version>`, byly nahrány do funkce úložiště Red Hat. Povolte funkce úložiště spuštěním následujícího příkazu:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

1. Instalace agenta Azure Linux spuštěním následujícího příkazu:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

1. Nevytvářejte odkládacího prostoru na disku s operačním systémem.

    Azure Linux Agent mohou automaticky konfigurovat velikosti odkládacího souboru s použitím disku místního prostředku, který je připojen k virtuálnímu počítači po zřízení virtuálního počítače v Azure. Mějte na paměti, disku místního prostředku je dočasný disk a může být vyprázdněna při zřízení virtuálního počítače. Po instalaci agenta Azure Linux v předchozím kroku, upravte následující parametry v `/etc/waagent.conf` odpovídajícím způsobem:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

1. Pokud chcete zrušit předplatné, spusťte následující příkaz:

        # sudo subscription-manager unregister

1. Spusťte následující příkaz pro zrušení zřízení virtuálního počítače a připravte je ke zřizování v Azure:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

1. Vypnout virtuální počítač a převeďte soubor VMDK na formát virtuálního pevného disku.

> [!NOTE]
> Je známého problému v qemu img verze > = 2.2.1, jejímž výsledkem nesprávně formátovaná VHD. Problém byl vyřešen ve verzi 2.6 QEMU. Doporučuje se použít qemu-img 2.2.0 nebo nižší ani aktualizovat na 2.6 nebo novější. Referenční dokumentace: https://bugs.launchpad.net/qemu/+bug/1490611.
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


## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>Příprava virtuálního počítače založeného na Red Hat ze systému ISO pomocí souboru první kroky automaticky
### <a name="prepare-a-rhel-7-virtual-machine-from-a-kickstart-file"></a>Příprava virtuálního počítače RHEL 7 ze souboru první kroky

1.  Vytvořte první kroky soubor, který obsahuje následující obsah a uložte soubor. Podrobnosti o první kroky instalace najdete v tématu [první kroky Průvodce instalací](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

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
        NM_CONTROLLED=no
        EOF

        # Deprovision and prepare for Azure
        waagent -force -deprovision

        %end

1. Umístěte soubor první kroky, kde instalace systému k němu přístup.

1. Ve Správci technologie Hyper-V vytvořte nový virtuální počítač. Na **připojit virtuální pevný Disk** stránce **připojit virtuální pevný disk později**a dokončete Průvodce novým virtuálním počítačem.

1. Otevřete nastavení virtuálního počítače:

    a.  Připojte nový virtuální pevný disk k virtuálnímu počítači. Je nutné vybrat **Formát virtuálního pevného disku** a **pevné velikosti**.

    b.  Připojte instalace ISO k jednotce DVD.

    c.  Nastavte spuštění z CD systému BIOS.

1. Umožňuje spustit virtuální počítač. Jakmile se zobrazí v instalační příručce, stiskněte klávesu **kartu** nakonfigurovat možnosti spuštění.

1. Zadejte `inst.ks=<the location of the kickstart file>` na konci možnosti spuštění a stiskněte klávesu **Enter**.

1. Počkejte na dokončení instalace. Když se dokončí, virtuální počítač bude vypnutý automaticky. Vašeho linuxového virtuálního pevného disku je teď připravený k nahrání do Azure.

## <a name="known-issues"></a>Známé problémy
### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>Ovladač technologie Hyper-V nelze zahrnout do počáteční paměť RAM disk, při použití Hyper V hypervisoru

V některých případech instalačních programů Linux nemusí obsahovat ovladače pro technologii Hyper-V na počáteční disku paměti RAM (initrd nebo initramfs) Pokud Linux zjistí, zda je spuštěna v prostředí Hyper-V.

Pokud používáte jiný virtualizace systému (to znamená Virtualbox, Xen, atd.) k přípravě image s Linuxem, možná budete muset znovu sestavit initrd a zkontrolujte, že alespoň modulů jádra hv_vmbus a hv_storvsc jsou k dispozici na počáteční disku paměti RAM. V systémech, které jsou založeny na nadřazený distribuční Red Hat alespoň jde o známý problém.

Chcete-li tento problém vyřešit, přidejte do initramfs moduly Hyper-V a její opětovné sestavení:

Upravit `/etc/dracut.conf`a přidejte následující obsah:

        add_drivers+=" hv_vmbus hv_netvsc hv_storvsc "

Znovu sestavte initramfs:

        # dracut -f -v

Další podrobnosti najdete v informacích [znovu sestavit initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Další postup
Teď jste připraveni používat virtuální pevný disk systému Red Hat Enterprise Linux k vytváření nových virtuálních počítačů v Azure. Pokud je to poprvé, že jste nahrání souboru VHD do Azure, najdete v článku [vytvoření virtuálního počítače s Linuxem z vlastního disku](upload-vhd.md#option-1-upload-a-vhd).

Další podrobnosti o hypervisorů, které certifikaci pro Red Hat Enterprise Linux najdete v tématu [web Red Hat](https://access.redhat.com/certified-hypervisors).
