---
title: Vytvoření a odeslání Red Hat Enterprise Linux virtuální pevný disk pro použití v zásobníku Azure | Microsoft Docs
description: Naučte se vytvořit a odeslat Azure virtuální pevný disk (VHD) obsahující operační systém Red Hat Linux.
services: azure-stack
documentationcenter: ''
author: JeffGoldner
manager: BradleyB
editor: ''
tags: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2018
ms.author: jeffgo
ms.openlocfilehash: 524c3b3b86e43b56e1d1f2a1653bdf7b82061022
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
ms.locfileid: "34076611"
---
# <a name="prepare-a-red-hat-based-virtual-machine-for-azure-stack"></a>Příprava virtuálního počítače, na základě Red Hat pro Azure zásobníku
V tomto článku se dozvíte, jak připravit virtuální počítač Red Hat Enterprise Linux (RHEL) pro použití v Azure zásobníku. Verze RHEL, které jsou popsané v tomto článku jsou 7.1 +. Hypervisory pro přípravu, které jsou popsané v tomto článku jsou virtuální počítače Hyper-V, na základě jádra (KVM) a VMware.

Informace o podpoře Red Hat Enterprise Linux naleznete [Azure zásobníku a Red Hat: Nejčastější dotazy](https://access.redhat.com/articles/3413531).

## <a name="prepare-a-red-hat-based-virtual-machine-from-hyper-v-manager"></a>Příprava virtuálního počítače, na základě Red Hat ze Správce technologie Hyper-V

### <a name="prerequisites"></a>Požadavky
V této části předpokládá, že jste již získat soubor ISO z webu Red Hat a nainstalovat bitovou kopii systému RHEL na virtuální pevný disk (VHD). Další podrobnosti o tom, jak nainstalovat bitovou kopii operačního systému pomocí Správce technologie Hyper-V najdete v tématu [nainstalovat roli Hyper-V a konfigurace virtuálního počítače](http://technet.microsoft.com/library/hh846766.aspx).

**Poznámky k instalaci RHEL**

* Azure zásobník nepodporuje formátu VHDX. Azure podporuje pouze dlouhodobý virtuálního pevného disku. Převést disk na formát virtuálního pevného disku můžete použít Správce technologie Hyper-V, nebo můžete pomocí rutiny convert-VHD prostředí. Pokud používáte VirtualBox, vyberte **pevnou velikost** oproti výchozí možnost přidělí dynamicky při vytváření disku.
* Azure zásobníku podporuje pouze virtuální počítače generace 1. Virtuální počítač generace 1 můžete převést z VHDX do formátu souboru virtuálního pevného disku a dynamicky se zvětšující na disk pevné velikosti. Nelze změnit generaci virtuálního počítače. Další informace najdete v tématu [by měl vytvořit virtuální počítač generace 1 nebo 2 v Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
* Maximální velikost povolenou pro virtuální pevný disk je 1,023 GB.
* Při instalaci operačního systému Linux, doporučujeme použít standardní oddíly spíše než logické svazku Manager (LVM), což je často na výchozí hodnoty pro mnoho instalace. Tento postup zabráníte LVM název je v konfliktu s klonovaný virtuální počítače, zvlášť pokud byste někdy potřebovali pro připojení k jiné identický virtuální počítač pro řešení potíží s diskem operačního systému.
* Vyžaduje se podpora jádra pro připojení systémy souborů univerzální formát disku (UDF). Při prvním spuštění v Azure formátu UDF média, který je připojen k Host předá konfiguraci zřizování virtuální počítač Linux. Azure Linux Agent musí být schopný se připojit a načíst jeho konfiguraci a zřízení virtuálního počítače v systému souborů UDF.
* Verze jádra systému Linux, které jsou starší než 2.6.37 nepodporují přístup k nerovnoměrné paměti (NUMA) s technologií Hyper-V s větší velikostí virtuálních počítačů. -Li tento problém ovlivňuje především starší distribuce, které používají nadřazený Red Hat 2.6.32 jádra která byla opravena v RHEL 6.6 (jádra 2.6.32 504). Systémy, které spouštějí vlastní jádra, které jsou starší než 2.6.37 nebo na základě RHEL jádra, která jsou starší než 2.6.32-504 musíte nastavit `numa=off` spouštění na příkazovém řádku jádra v grub.conf parametr. Další informace najdete v tématu Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Nekonfigurujte přepnutí oddílu na disku operačního systému. Chcete-li vytvořit odkládací soubor na disku dočasných prostředků lze nakonfigurovat agenta systému Linux.  Další informace o této naleznete v následujících krocích.
* Všechny virtuální pevné disky na platformě Azure, musí mít virtuální velikost zarovnán 1MB. Při převodu z nezpracovaná disku na virtuální pevný disk je nutné zajistit, aby velikost disku nezpracovaná není násobkem 1MB před převodem. Další podrobnosti naleznete v následujících krocích.
* Azure zásobník nepodporuje init cloudu. Je virtuální počítač musí být nakonfigurované s podporovanou verzí systému Windows Azure Linux Agent (WALA).

### <a name="prepare-a-rhel-7-virtual-machine-from-hyper-v-manager"></a>Příprava RHEL 7 virtuálního počítače ze Správce technologie Hyper-V

1. Ve Správci technologie Hyper-V vyberte virtuální počítač.

2. Klikněte na tlačítko **Connect** k otevření okna konzoly pro virtuální počítač.

3. Vytvořit nebo upravit `/etc/sysconfig/network` souboru a přidejte následující text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

4. Vytvořit nebo upravit `/etc/sysconfig/network-scripts/ifcfg-eth0` souboru a přidejte následující text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
    NM_CONTROLLED = ne

5. Ujistěte se, že síťové služby se spustí při spuštění spuštěním následujícího příkazu:

        # sudo systemctl enable network

6. Registrujte předplatné Red Hat povolit instalaci balíčků z úložiště RHEL spuštěním následujícího příkazu:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

7. Upravte řádku spouštěcí jádra ve vaší konfiguraci grub pro zahrnutí dalších jádra parametry Azure. Chcete-li provést tuto změny, otevřete `/etc/default/grub` v textovém editoru a upravit `GRUB_CMDLINE_LINUX` parametr. Příklad:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   To také zajistí, že všechny zprávy konzoly odešlou do první sériového portu, který může být užitečné Azure podporu pro ladění problémů. Tato konfigurace taky vypne nové zásady vytváření názvů RHEL 7 pro síťové adaptéry. Kromě toho doporučujeme odebrat následující parametry:
   
        rhgb quiet crashkernel=auto
   
    Grafické a quiet spouštěcí nejsou užitečné při cloudovém prostředí, kde chceme, aby všechny protokoly pro odeslání do sériového portu. Můžete nechat `crashkernel` možnost nakonfigurované v případě potřeby. Všimněte si, že tento parametr snižuje množství dostupné paměti ve virtuálním počítači 128 MB a víc, což může způsobovat problémy menší velikostí virtuálního počítače.

8. Po dokončení úprav `/etc/default/grub`, spusťte následující příkaz k opětovnému sestavení grub konfigurace:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

9. Zajistěte, aby serverem SSH je nainstalován a nakonfigurován na spuštění při spuštění, což obvykle výchozí nastavení. Upravit `/etc/ssh/sshd_config` obsahovala následující řádek:

        ClientAliveInterval 180

10. Balíček WALinuxAgent `WALinuxAgent-<version>`, bylo posunuto do Red Hat funkce úložiště. Povolení funkce úložiště tak, že spustíte následující příkaz:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

11. Nainstalujte Azure Linux Agent spuštěním následujícího příkazu:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

12. Nevytvářejte odkládacího prostoru na disku operačního systému.

    Azure Linux Agent mohou automaticky konfigurovat odkládacího souboru pomocí disku místní prostředek, který je připojen k virtuálnímu počítači po zřízení virtuálního počítače na platformě Azure. Upozorňujeme, že disk místní prostředek je dočasný a může být vyprázdněny, když virtuální počítač je zrušit. Po instalaci Azure Linux Agent v předchozím kroku, upravte následující parametry v `/etc/waagent.conf` odpovídajícím způsobem:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

13. Pokud chcete zrušit registraci předplatného, spusťte následující příkaz:

        # sudo subscription-manager unregister

14. Spusťte následující příkaz pro zrušení zřízení virtuálního počítače a jeho přípravu pro zřizování na Azure:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

15. Klikněte na tlačítko **akce** > **vypnout** ve Správci technologie Hyper-V. Svůj disk VHD Linux je nyní připravena k odeslání do Azure.


## <a name="prepare-a-red-hat-based-virtual-machine-from-kvm"></a>Příprava virtuálního počítače, na základě Red Hat z KVM

### <a name="prepare-a-rhel-7-virtual-machine-from-kvm"></a>Příprava virtuálního počítače, RHEL 7 z KVM

1. Stáhněte z webu Red Hat KVM bitové kopie systému RHEL 7. Tento postup používá RHEL 7 jako v příkladu.

2. Nastavení kořenové heslo.

    Generovat zašifrované heslo a zkopírujte výstup tohoto příkazu:

        # openssl passwd -1 changeme

    Nastavení kořenové heslo s guestfish:

        # guestfish --rw -a <image-name>
        > <fs> run
        > <fs> list-filesystems
        > <fs> mount /dev/sda1 /
        > <fs> vi /etc/shadow
        > <fs> exit

   Změňte druhé pole kořenové uživatele z "!" do zašifrované heslo.

3. Vytvoření virtuálního počítače v KVM z qcow2 bitové kopie. Nastavte typ disku na **qcow2**a nastavte model zařízení rozhraní virtuální sítě na **virtio**. Potom spusťte virtuální počítač a přihlaste se jako kořenový.

4. Vytvořit nebo upravit `/etc/sysconfig/network` souboru a přidejte následující text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

5. Vytvořit nebo upravit `/etc/sysconfig/network-scripts/ifcfg-eth0` souboru a přidejte následující text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

6. Ujistěte se, že síťové služby se spustí při spuštění spuštěním následujícího příkazu:

        # sudo systemctl enable network

7. Registrujte předplatné Red Hat povolit instalaci balíčků z úložiště RHEL spuštěním následujícího příkazu:

        # subscription-manager register --auto-attach --username=XXX --password=XXX

8. Upravte řádku spouštěcí jádra ve vaší konfiguraci grub pro zahrnutí dalších jádra parametry Azure. Chcete-li provést tuto konfiguraci, otevřete `/etc/default/grub` v textovém editoru a upravit `GRUB_CMDLINE_LINUX` parametr. Příklad:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Tento příkaz také zajistí, že všechny zprávy konzoly odešlou do první sériového portu, který může být užitečné Azure podporu pro ladění problémů. Příkaz vypne také nové zásady vytváření názvů RHEL 7 pro síťové adaptéry. Kromě toho doporučujeme odebrat následující parametry:
   
        rhgb quiet crashkernel=auto
   
    Grafické a quiet spouštěcí nejsou užitečné při cloudovém prostředí, kde chceme, aby všechny protokoly pro odeslání do sériového portu. Můžete nechat `crashkernel` možnost nakonfigurované v případě potřeby. Všimněte si, že tento parametr snižuje množství dostupné paměti ve virtuálním počítači 128 MB a víc, což může způsobovat problémy menší velikostí virtuálního počítače.

9. Po dokončení úprav `/etc/default/grub`, spusťte následující příkaz k opětovnému sestavení grub konfigurace:

        # grub2-mkconfig -o /boot/grub2/grub.cfg

10. Přidejte do initramfs moduly Hyper-V.

    Upravit `/etc/dracut.conf` a přidejte obsah:

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

    Znovu sestavte initramfs:

        # dracut -f -v

11. Odinstalace init cloudu:

        # yum remove cloud-init

12. Ujistěte se, že SSH server je nainstalován a nakonfigurován na spuštění při spuštění:

        # systemctl enable sshd

    Upravte /etc/ssh/sshd_config zahrnuty následující řádky:

        PasswordAuthentication yes
        ClientAliveInterval 180

13. Balíček WALinuxAgent `WALinuxAgent-<version>`, bylo posunuto do Red Hat funkce úložiště. Povolení funkce úložiště tak, že spustíte následující příkaz:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

14. Nainstalujte Azure Linux Agent spuštěním následujícího příkazu:

        # yum install WALinuxAgent

    Povolte službu příkaz waagent:

        # systemctl enable waagent.service

15. Nevytvářejte odkládacího prostoru na disku operačního systému.

    Azure Linux Agent mohou automaticky konfigurovat odkládacího souboru pomocí disku místní prostředek, který je připojen k virtuálnímu počítači po zřízení virtuálního počítače na platformě Azure. Upozorňujeme, že disk místní prostředek je dočasný a může být vyprázdněny, když virtuální počítač je zrušit. Po instalaci Azure Linux Agent v předchozím kroku, upravte následující parametry v `/etc/waagent.conf` odpovídajícím způsobem:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

16. Zrušení registrace předplatného (v případě potřeby) tak, že spustíte následující příkaz:

        # subscription-manager unregister

17. Spusťte následující příkaz pro zrušení zřízení virtuálního počítače a jeho přípravu pro zřizování na Azure:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

18. Vypnete virtuální počítač v KVM.

19. Bitovou kopii qcow2 převeďte na formát VHD.

> [!NOTE]
> Je známého problému v qemu img verze > = 2.2.1, jejímž výsledkem nesprávně naformátovaný VHD. Ve verzi 2.6 QEMU byl opraven problém. Doporučuje se používat qemu-img 2.2.0 nebo nižší, nebo aktualizace na 2.6 nebo novější. Referenční dokumentace: https://bugs.launchpad.net/qemu/+bug/1490611.
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


## <a name="prepare-a-red-hat-based-virtual-machine-from-vmware"></a>Příprava virtuálního počítače, na základě Red Hat z VMware
### <a name="prerequisites"></a>Požadavky
V této části se předpokládá, že jste již nainstalovali RHEL virtuálního počítače v prostředí VMware. Podrobnosti o tom, jak nainstalovat operační systém v prostředí VMware najdete v tématu [Průvodce instalací operačního systému hosta VMware](http://partnerweb.vmware.com/GOSIG/home.html).

* Při instalaci operačního systému Linux, doporučujeme použít standardní oddíly spíše než LVM, což je často na výchozí hodnoty pro mnoho instalace. Tím se vyhnete LVM název je v konfliktu s naklonovaný virtuální počítač, zvlášť pokud někdy musí být připojen k jinému virtuálnímu počítači pro řešení potíží s diskem operačního systému. LVM nebo RAID lze použít v datových disků Pokud dáváte přednost.
* Nekonfigurujte přepnutí oddílu na disku operačního systému. Můžete nakonfigurovat agenta systému Linux, chcete-li vytvořit odkládací soubor na disku dočasných prostředků. Další informace o tom najdete v krocích, které následují.
* Když vytvoříte virtuální pevný disk, vyberte **virtuálního disku úložiště jako samostatný soubor**.


### <a name="prepare-a-rhel-7-virtual-machine-from-vmware"></a>Příprava virtuálního počítače, RHEL 7 z VMware
1. Vytvořit nebo upravit `/etc/sysconfig/network` souboru a přidejte následující text:
   
        NETWORKING=yes
        HOSTNAME=localhost.localdomain

2. Vytvořit nebo upravit `/etc/sysconfig/network-scripts/ifcfg-eth0` souboru a přidejte následující text:
   
        DEVICE=eth0
        ONBOOT=yes
        BOOTPROTO=dhcp
        TYPE=Ethernet
        USERCTL=no
        PEERDNS=yes
        IPV6INIT=no
        NM_CONTROLLED=no

3. Ujistěte se, že síťové služby se spustí při spuštění spuštěním následujícího příkazu:

        # sudo chkconfig network on

4. Registrujte předplatné Red Hat povolit instalaci balíčků z úložiště RHEL spuštěním následujícího příkazu:

        # sudo subscription-manager register --auto-attach --username=XXX --password=XXX

5. Upravte řádku spouštěcí jádra ve vaší konfiguraci grub pro zahrnutí dalších jádra parametry Azure. Chcete-li provést tuto změny, otevřete `/etc/default/grub` v textovém editoru a upravit `GRUB_CMDLINE_LINUX` parametr. Příklad:
   
        GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0 net.ifnames=0"
   
   Tato konfigurace taky zajišťuje, že všechny zprávy konzoly odešlou do první sériového portu, který může být užitečné Azure podporu pro ladění problémů. Je také vypne nové zásady vytváření názvů RHEL 7 pro síťové adaptéry. Kromě toho doporučujeme odebrat následující parametry:
   
        rhgb quiet crashkernel=auto
   
    Grafické a quiet spouštěcí nejsou užitečné při cloudovém prostředí, kde chceme, aby všechny protokoly pro odeslání do sériového portu. Můžete nechat `crashkernel` možnost nakonfigurované v případě potřeby. Všimněte si, že tento parametr snižuje množství dostupné paměti ve virtuálním počítači 128 MB a víc, což může způsobovat problémy menší velikostí virtuálního počítače.

6. Po dokončení úprav `/etc/default/grub`, spusťte následující příkaz k opětovnému sestavení grub konfigurace:

        # sudo grub2-mkconfig -o /boot/grub2/grub.cfg

7. Přidejte do initramfs moduly Hyper-V.

    Upravit `/etc/dracut.conf`, přidejte obsah:

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

    Znovu sestavte initramfs:

        # dracut -f -v

8. Ujistěte se, že SSH server je nainstalován a nakonfigurován na spuštění při spuštění. Toto nastavení je obvykle výchozí. Upravit `/etc/ssh/sshd_config` obsahovala následující řádek:

        ClientAliveInterval 180

9. Balíček WALinuxAgent `WALinuxAgent-<version>`, bylo posunuto do Red Hat funkce úložiště. Povolení funkce úložiště tak, že spustíte následující příkaz:

        # subscription-manager repos --enable=rhel-7-server-extras-rpms

10. Nainstalujte Azure Linux Agent spuštěním následujícího příkazu:

        # sudo yum install WALinuxAgent

        # sudo systemctl enable waagent.service

11. Nevytvářejte odkládacího prostoru na disku operačního systému.

    Azure Linux Agent mohou automaticky konfigurovat odkládacího souboru pomocí disku místní prostředek, který je připojen k virtuálnímu počítači po zřízení virtuálního počítače na platformě Azure. Upozorňujeme, že disk místní prostředek je dočasný a může být vyprázdněny, když virtuální počítač je zrušit. Po instalaci Azure Linux Agent v předchozím kroku, upravte následující parametry v `/etc/waagent.conf` odpovídajícím způsobem:

        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

12. Pokud chcete zrušit registraci předplatného, spusťte následující příkaz:

        # sudo subscription-manager unregister

13. Spusťte následující příkaz pro zrušení zřízení virtuálního počítače a jeho přípravu pro zřizování na Azure:

        # sudo waagent -force -deprovision

        # export HISTSIZE=0

        # logout

14. Vypněte virtuální počítač a převeďte soubor VMDK na formát VHD.

> [!NOTE]
> Je známého problému v qemu img verze > = 2.2.1, jejímž výsledkem nesprávně naformátovaný VHD. Ve verzi 2.6 QEMU byl opraven problém. Doporučuje se používat qemu-img 2.2.0 nebo nižší, nebo aktualizace na 2.6 nebo novější. Referenční dokumentace: https://bugs.launchpad.net/qemu/+bug/1490611.
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


## <a name="prepare-a-red-hat-based-virtual-machine-from-an-iso-by-using-a-kickstart-file-automatically"></a>Příprava virtuálního počítače, na základě Red Hat ze souboru ISO pomocí souboru kickstart automaticky
### <a name="prepare-a-rhel-7-virtual-machine-from-a-kickstart-file"></a>Příprava virtuálního počítače ze souboru kickstart RHEL 7

1.  Vytvořte soubor kickstart, který obsahuje následující obsah a uložte soubor. Podrobnosti o kickstart instalace najdete v tématu [Průvodce instalací Kickstart](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Installation_Guide/chap-kickstart-installations.html).

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

2. Umístěte soubor kickstart, kde systém instalace k němu přístup.

3. Ve Správci technologie Hyper-V vytvořte nový virtuální počítač. Na **připojit virtuální pevný Disk** vyberte **připojit virtuální pevný disk později**a dokončete Průvodce novým virtuálním počítačem.

4. Otevřete nastavení virtuálního počítače:

    a.  Připojte nový virtuální pevný disk k virtuálnímu počítači. Je nutné vybrat **formátu virtuálního pevného disku** a **pevné velikosti**.

    b.  Připojte instalace ISO k jednotce DVD.

    c.  Nastavení systému BIOS spouštění z disku CD.

5. Umožňuje spustit virtuální počítač. Jakmile se zobrazí v instalační příručce, stiskněte klávesu **kartě** nakonfigurovat možnosti spuštění.

6. Zadejte `inst.ks=<the location of the kickstart file>` na konci možnosti spuštění a stiskněte klávesu **Enter**.

7. Počkejte na dokončení instalace. Po dokončení, virtuální počítač se vypne automaticky. Svůj disk VHD Linux je nyní připravena k odeslání do Azure.

## <a name="known-issues"></a>Známé problémy
### <a name="the-hyper-v-driver-could-not-be-included-in-the-initial-ram-disk-when-using-a-non-hyper-v-hypervisor"></a>Ovladač technologie Hyper-V není v počáteční disku paměti RAM při použití hypervisor bez technologie Hyper-V

V některých případech instalačních programů Linux nemusí zahrnovat ovladače pro Hyper-V na počáteční disku paměti RAM (initrd nebo initramfs) Pokud Linux zjistí, zda je spuštěna v prostředí Hyper-V.

Pokud používáte jiný virtualizační systému (tj. Virtualbox, Xen atd.) Příprava bitové kopie systému Linux, možná budete muset znovu vytvořit initrd zajistit, aby aspoň jádra modulů hv_vmbus a hv_storvsc jsou k dispozici na počáteční disku paměti RAM. V systémech, které jsou založeny na nadřazený distribuce Red Hat alespoň jde o známý problém.

Chcete-li vyřešit tento problém, přidejte do initramfs moduly Hyper-V a znovu sestavte jej:

Upravit `/etc/dracut.conf`a přidejte následující obsah:

        add_drivers+="hv_vmbus hv_netvsc hv_storvsc"

Znovu sestavte initramfs:

        # dracut -f -v

Další podrobnosti najdete v tématu informace [znovu sestavit initramfs](https://access.redhat.com/solutions/1958).

## <a name="next-steps"></a>Další postup
Nyní jste připraveni použít k vytvoření nové virtuální počítače v Azure zásobníku Red Hat Enterprise Linux virtuální pevný disk. Pokud je poprvé, že jste nahrávání souboru VHD do zásobníku Azure, najdete v části [použijte sadu nástrojů Marketplace vytvoření a publikování položky marketplace](azure-stack-marketplace-publisher.md).

Další podrobnosti o hypervisorů, které jsou certifikované ke spuštění Red Hat Enterprise Linux najdete v tématu [webu Red Hat](https://access.redhat.com/certified-hypervisors).
