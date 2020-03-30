---
title: Vytvoření a nahrání virtuálního pevného disku linuxu
description: Naučte se vytvářet a nahrávat virtuální pevný disk Azure (VHD), který obsahuje operační systém Linux.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 10/08/2018
ms.author: guybo
ms.openlocfilehash: f700dec6486bad9e7024d7c908a70dd0ff2b342c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066764"
---
# <a name="information-for-non-endorsed-distributions"></a>Informace pro neschválené distribuce

SLA platformy Azure se vztahuje na virtuální počítače se systémem Linux OS pouze v případě, že se používá jedna z [schválených distribucí.](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Pro tyto schválené distribuce jsou předkonfigurované inamfigurované linuxové image k dispozici na Azure Marketplace.

* [Linux v Azure – schválené distribuce](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Podpora ibi z Linuxu v Microsoft Azure](https://support.microsoft.com/kb/2941892)

Všechny distribuce spuštěné v Azure mají řadu předpokladů. Tento článek nemůže být komplexní, protože každá distribuce je jiná. Dokonce i když splňujete všechna níže uvedená kritéria, možná budete muset výrazně vyladit systém Linux, aby správně běžel.

Doporučujeme začít s jedním z Linuxu v [Azure endorsed Distributions](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Následující články ukazují, jak připravit různé schválené linuxové distribuce, které jsou podporované v Azure:

* **[Distribuce založené na CentOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Tento článek se zaměřuje na obecné pokyny pro spuštění distribuce Linuxu v Azure.

## <a name="general-linux-installation-notes"></a>Obecné instalační poznámky k Linuxu
* Formát virtuálního pevného disku Hyper-V (VHDX) není v Azure podporován, *jenom pevný virtuální disk.*  Disk můžete převést do formátu Virtuálního pevného disku pomocí Správce technologie Hyper-V nebo rutiny [Convert-VHD.](https://docs.microsoft.com/powershell/module/hyper-v/convert-vhd) Pokud používáte VirtualBox, **vyberte** při vytváření disku místo výchozí (dynamicky přidělené) místo výchozí (dynamicky přidělené).
* Azure podporuje virtuální počítače Gen1 (BIOS boot) & Gen2 (UEFI boot).
* Maximální velikost virtuálního pevného disku je 1 023 GB.
* Při instalaci systému Linux doporučujeme používat standardní oddíly, spíše než Logický Správce svazků (LVM), který je výchozí pro mnoho instalací. Použití standardních oddílů zabrání konfliktům názvů LVM s klonovanými virtuálními počítačemi, zejména pokud je disk operačního systému někdy připojený k jinému identickému virtuálnímu počítače pro řešení potíží. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) lze použít na datových discích.
* Podpora jádra pro montáž souborůových systémů UDF je nutná. Při prvním spuštění v Azure je konfigurace zřizování předána virtuálnímu počítači Sn Linux pomocí média ve formátu UDF, která je připojena k hostu. Agent Azure Linux musí připojit systém souborů UDF číst jeho konfiguraci a zřízení virtuálního počítače.
* Verze linuxového jádra starší než 2.6.37 nepodporují NUMA na Hyper-V s většími velikostmi virtuálních her. Tento problém se týká především staršídistribuce pomocí upstream Red Hat 2.6.32 jádra a byl opraven v Red Hat Enterprise Linux (RHEL) 6.6 (kernel-2.6.32-504). Systémy s vlastními jádry staršími než 2.6.37 nebo jádra založená na RHEL starší než `numa=off` 2.6.32-504 musí nastavit zaváděcí parametr na příkazovém řádku jádra v grub.conf. Další informace naleznete v [tématu Red Hat KB 436883](https://access.redhat.com/solutions/436883).
* Nekonfigurujte odkládací oddíl na disku operačního systému. Agenta Linuxu lze nakonfigurovat tak, aby vytvořil odkládací soubor na disku s dočasným prostředkem, jak je popsáno v následujících krocích.
* Všechny virtuální počítače v Azure musí mít virtuální velikost zarovnanou na 1 MB. Při převodu ze surového disku na virtuální pevný disk je nutné zajistit, aby velikost nezpracovaného disku byla před převodem násobkem 1 MB, jak je popsáno v následujících krocích.

### <a name="installing-kernel-modules-without-hyper-v"></a>Instalace modulů jádra bez technologie Hyper-V
Azure běží na hypervisoru Hyper-V, takže Linux vyžaduje, aby v Azure běžely určité moduly jádra. Pokud máte virtuální počítač, který byl vytvořen mimo Hyper-V, instalační programy Linuxu nemusí obsahovat ovladače pro Hyper-V v počátečníramdisk (initrd nebo initramfs), pokud virtuální počítač zjistí, že je spuštěn v prostředí Hyper-V. Při použití jiného virtualizačního systému (například VirtualBox, KVM a tak dále) k přípravě bitové kopie Linuxu může být nutné znovu sestavit initrd tak, aby alespoň hv_vmbus a hv_storvsc moduly jádra byly k dispozici na počátečním ramdisku.  Tento známý problém je pro systémy založené na distribuci upstream Red Hat a případně další.

Mechanismus pro opětovné sestavení initrd nebo initramfs image se může lišit v závislosti na distribuci. Správný postup naleznete v dokumentaci nebo v dokumentaci k distribuci.  Zde je jeden příklad pro opětovné sestavení `mkinitrd` initrd pomocí nástroje:

1. Zálohovat existující initrd image:

    ```
    cd /boot
    sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak
    ```

2. Znovu sestavte initrd pomocí modulů jádra hv_vmbus a hv_storvsc:

    ```
    sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`
    ```

### <a name="resizing-vhds"></a>Změna velikosti virtuálních dispon
INa hd v Azure musí mít virtuální velikost zarovnanou na 1 MB.  Virtuální počítač vytvořený pomocí technologie Hyper-V je obvykle správně zarovnán.  Pokud není virtuální pevný disk správně zarovnán, může se při pokusu o vytvoření obrázku z virtuálního pevného disku zobrazit chybová zpráva podobná následující.

* VHD http:\//\<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd má nepodporovanou virtuální velikost 21475270656 bajtů. Velikost musí být celé číslo (v MBs).

V takovém případě změňte velikost virtuálního počítače pomocí konzoly Správce Technologie Hyper-V nebo rutiny prostředí [Resize-VHD](https://technet.microsoft.com/library/hh848535.aspx) PowerShell.  Pokud neběžíte v prostředí Windows, doporučujeme použít `qemu-img` k převodu (v případě potřeby) a změně velikosti vhd.

> [!NOTE]
> Existuje známá chyba ve verzích [qemu-img](https://bugs.launchpad.net/qemu/+bug/1490611) >= 2.2.1, která má za následek nesprávně formátovaný virtuální pevný disk. Problém byl vyřešen v QEMU 2.6. Doporučujeme používat `qemu-img` buď 2.2.0 nebo nižší, nebo 2.6 nebo vyšší.
> 

1. Změna velikosti virtuálního pevného `qemu-img` disku `vbox-manage` přímo pomocí nástrojů, jako je nebo může mít za následek nespustitelný virtuální pevný disk.  Doporučujeme nejprve převést virtuální pevný disk na bitovou kopii disku RAW.  Pokud byla bitová kopie virtuálního počítače vytvořena jako bitová kopie disku RAW (výchozí pro některé hypervisory, jako je Například KVM), můžete tento krok přeskočit.
 
    ```
    qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw
    ```

1. Vypočítejte požadovanou velikost bitové kopie disku tak, aby virtuální velikost byla zarovnána na 1 MB.  Následující bash shell `qemu-img info` skript používá k určení virtuální velikost bitové kopie disku a potom vypočítá velikost na další 1 MB.

    ```bash
    rawdisk="MyLinuxVM.raw"
    vhddisk="MyLinuxVM.vhd"

    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "$rawdisk" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    rounded_size=$(((($size+$MB-1)/$MB)*$MB))
    
    echo "Rounded Size = $rounded_size"
    ```

3. Změňte velikost nezpracovaného disku pomocí `$rounded_size` výše uvedeného nastavení.

    ```bash
    qemu-img resize MyLinuxVM.raw $rounded_size
    ```

4. Nyní převeďte disk RAW zpět na pevný virtuální disk.

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

   Nebo s qemu verze 2.6+, patří `force_size` možnost.

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

## <a name="linux-kernel-requirements"></a>Požadavky na jádro Linuxu

Ovladače Linux Integration Services (LIS) pro Hyper-V a Azure jsou přímo přispívají k nadřazenému jádru Linuxu. Mnoho distribucí, které obsahují nedávnou verzi jádra Linuxu (například 3.x), mají tyto ovladače již k dispozici, nebo jinak poskytují backportované verze těchto ovladačů s jejich jádry.  Tyto ovladače jsou neustále aktualizovány v upstream jádru s novými opravami a funkcemi, takže pokud je to možné, doporučujeme spustit [schválenou distribuci,](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) která zahrnuje tyto opravy a aktualizace.

Pokud používáte variantu Red Hat Enterprise Linux verze 6.0 až 6.3, budete muset nainstalovat [nejnovější ovladače LIS pro Hyper-V](https://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). Počínaje RHEL 6.4+ (a deriváty) jsou ovladače LIS již součástí jádra, a proto nejsou potřeba žádné další instalační balíčky.

Pokud je vyžadováno vlastní jádro, doporučujeme nejnovější verzi jádra (například 3.8+). Pro distribuce nebo dodavatele, kteří udržují své vlastní jádro, budete muset pravidelně backportovat ovladače LIS z předcházejícího jádra do vlastního jádra.  I v případě, že již používáte relativně nedávnou verzi jádra, důrazně doporučujeme sledovat všechny upstream opravy v ovladačích LIS a backport je podle potřeby. Umístění zdrojových souborů ovladače LIS jsou specifikována v souboru [MAINTAINERS](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) ve zdrojovém stromu jádra Linuxu:
```
    F:    arch/x86/include/asm/mshyperv.h
    F:    arch/x86/include/uapi/asm/hyperv.h
    F:    arch/x86/kernel/cpu/mshyperv.c
    F:    drivers/hid/hid-hyperv.c
    F:    drivers/hv/
    F:    drivers/input/serio/hyperv-keyboard.c
    F:    drivers/net/hyperv/
    F:    drivers/scsi/storvsc_drv.c
    F:    drivers/video/fbdev/hyperv_fb.c
    F:    include/linux/hyperv.h
    F:    tools/hv/
```
Do jádra musí být zahrnuty následující záplaty. Tento seznam nelze dokončit pro všechny distribuce.

* [ata_piix: ve výchozím nastavení odložit disky na ovladače Technologie Hyper-V](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc: Účet pro pakety na cestě pro přenos v cestě RESET](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc: vyhněte se použití WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc: Zakázat write same pro ovladače RAID a virtuálního hostitelského adaptéru](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc: Oprava odkazu ukazatele NULL](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc: Selhání kruhového vyrovnávacípaměti může mít za následek zmrazení vstupně-in/o](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: ochrana před dvojím provedením __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>Agent Azure Linuxu
[Azure Linux Agent](../extensions/agent-linux.md) `waagent` zřdí virtuální počítač Linux v Azure. Můžete získat nejnovější verzi, problémy se soubory nebo odeslat žádosti o přijetí vyžádat se z [úložiště Linux Agent GitHub](https://github.com/Azure/WALinuxAgent).

* Linuxagent je propuštěn pod licencí Apache 2.0. Mnoho distribucí již poskytuje balíčky RPM nebo .deb pro agenta a tyto balíčky lze snadno nainstalovat a aktualizovat.
* Agent Azure Linux vyžaduje Python v2.6+.
* Agent také vyžaduje modul python-pyasn1. Většina distribucí poskytuje tento modul jako samostatný balíček, který má být nainstalován.
* V některých případech agent Azure Linux nemusí být kompatibilní s NetworkManager. Mnoho balíčků RPM/deb poskytovaných distribucí konfiguruje program NetworkManager jako konflikt s balíčkem waagent. V těchto případech se odinstaluje NetworkManager při instalaci balíčku agenta Linuxu.
* Azure Linux Agent musí být na nebo nad [minimální podporovanou verzi](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

## <a name="general-linux-system-requirements"></a>Obecné požadavky na systém Linux

1. Upravte zaváděcí řádek jádra v GRUB nebo GRUB2 tak, aby zahrnovala následující parametry tak, aby všechny zprávy konzoly jsou odesílány na první sériový port. Tyto zprávy mohou pomoci podpoře Azure s laděním problémů.
    ```  
    console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
    ```
    Doporučujeme také *odebrat* následující parametry, pokud existují.
    ```  
    rhgb quiet crashkernel=auto
    ```
    Grafické a tiché spuštění není užitečné v cloudovém prostředí, kde chceme, aby všechny protokoly byly odeslány do sériového portu. Možnost `crashkernel` může být v případě potřeby nakonfigurována, ale všimněte si, že tento parametr snižuje množství dostupné paměti ve virtuálním počítače alespoň o 128 MB, což může být problematické pro menší velikosti virtuálních počítače.

1. Nainstalujte agenta Azure Linuxu.
  
    Azure Linux Agent je potřeba pro zřizování image Linuxu v Azure.  Mnoho distribucí poskytuje agenta jako balíček RPM nebo .deb (balíček se obvykle nazývá WALinuxAgent nebo walinuxagent).  Agenta lze nainstalovat také ručně podle kroků v [průvodci agenty Linuxu](../extensions/agent-linux.md).

1. Ujistěte se, že je server SSH nainstalován a nakonfigurován tak, aby se spustil při spuštění.  Tato konfigurace je obvykle výchozí.

1. Nevytvářejte odkládací prostor na disku operačního systému.
  
    Azure Linux Agent můžete automaticky nakonfigurovat odkládací prostor pomocí disku místního prostředku, který je připojen k virtuálnímu počítači po zřizování v Azure. Disk místního prostředku je *dočasný* disk a může být vyprázdněn při zrušení zřízení virtuálního počítače. Po instalaci Azure Linux Agent (krok 2 výše), upravte následující parametry v /etc/waagent.conf podle potřeby.
    ```  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: Set this to your desired size.
    ```
1. Spusťte následující příkazy, abyste deprovision virtuální počítač.
  
     ```
     sudo waagent -force -deprovision
     export HISTSIZE=0
     logout
     ```  
   > [!NOTE]
   > Na VirtualBox se může zobrazit `waagent -force -deprovision` následující `[Errno 5] Input/output error`chyba po spuštění, který říká , že . Tato chybová zpráva není kritická a lze ji ignorovat.

* Vypněte virtuální počítač a nahrajte virtuální pevný disk do Azure.

