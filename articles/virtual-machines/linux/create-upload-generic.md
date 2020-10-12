---
title: Vytvoření a nahrání virtuálního pevného disku se systémem Linux
description: Naučte se vytvořit a nahrát virtuální pevný disk Azure (VHD), který obsahuje operační systém Linux.
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 10/08/2018
ms.author: guybo
ms.openlocfilehash: a80cc29f318cff8e5a4c665cd07ba1829d25d66d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87373381"
---
# <a name="information-for-non-endorsed-distributions"></a>Informace pro neschválené distribuce

Smlouva SLA platformy Azure se vztahuje na virtuální počítače s operačním systémem Linux jenom v případě, že se používá jedno z označených [distribucí](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) . Pro tyto schválené distribuce jsou předem nakonfigurované image pro Linux k dispozici v Azure Marketplace.

* [Linux v rozdělení schváleném pro Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Podpora imagí pro Linux v Microsoft Azure](https://support.microsoft.com/kb/2941892)

Všechna distribuce běžící v Azure mají řadu požadavků. Tento článek nemůže být komplexní, protože každá distribuce je odlišná. I v případě, že splňujete všechna kritéria uvedená níže, může být nutné významně upravit systém Linux, aby správně fungoval.

Doporučujeme, abyste začali s jedním ze systému [Linux v rámci schválených distribucí v Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Následující články ukazují, jak připravit různé schválené distribuce systému Linux podporované v Azure:

- [Distribuce založené na CentOS](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar Container Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES a openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)

Tento článek se zaměřuje na obecné pokyny pro provozování distribuce systému Linux v Azure.

## <a name="general-linux-installation-notes"></a>Obecné poznámky k instalaci pro Linux
* Formát virtuálního pevného disku Hyper-V (VHDX) se v Azure nepodporuje, jenom *pevný virtuální*pevný disk.  Disk můžete převést na formát VHD pomocí Správce technologie Hyper-V nebo rutiny [Convert-VHD](/powershell/module/hyper-v/convert-vhd) . Pokud používáte VirtualBox, při vytváření disku vyberte **pevnou velikost** , nikoli výchozí (dynamicky přidělené).
* Azure podporuje virtuální počítače Gen1 (Boot Boot) & Gen2 (UEFI Boot).
* Maximální velikost povolená pro virtuální pevný disk je 1 023 GB.
* Při instalaci systému Linux doporučujeme místo Správce logických svazků (LVM) používat standardní oddíly, což je výchozí nastavení pro mnoho instalací. Použití standardních oddílů zabrání v konfliktu LVM názvů s klonovanými virtuálními počítači, zejména pokud je disk s operačním systémem někdy připojený k jinému stejnému virtuálnímu počítači pro řešení potíží. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) se můžou používat na datových discích.
* Podpora jádra pro připojení systémů souborů UDF je nezbytná. Při prvním spuštění v Azure se konfigurace zřizování předává virtuálnímu počítači se systémem Linux pomocí média ve formátu UDF, které je připojené k hostu. Agent Azure Linux musí připojit systém souborů UDF a načíst jeho konfiguraci a zřídit virtuální počítač.
* Verze jádra Linux starší než 2.6.37 nepodporují architekturu NUMA na technologii Hyper-V s většími velikostmi virtuálních počítačů. Tento problém se týká především starších distribucí pomocí nadřazeného jádra Red Hat 2.6.32 a byl opraven v Red Hat Enterprise Linux (RHEL) 6,6 (kernel-2.6.32-504). Systémy s vlastními jádry staršími než 2.6.37 nebo jádry založenými na RHEL, které jsou starší než 2.6.32-504, musí nastavit parametr boot v `numa=off` příkazovém řádku jádra v souboru GRUB. conf. Další informace najdete v článku [Red Hat KB 436883](https://access.redhat.com/solutions/436883).
* Nekonfigurujte odkládací oddíl na disku s operačním systémem. Agent pro Linux se dá nakonfigurovat tak, aby na dočasném disku prostředků vytvořil odkládací soubor, jak je popsáno v následujícím postupu.
* Všechny virtuální pevné disky v Azure musí mít virtuální velikost zarovnaná na 1 MB. Při převodu z nezpracovaného disku na virtuální pevný disk musíte zajistit, aby velikost nezpracovaného disku byla před převodem násobkem 1 MB, jak je popsáno v následujícím postupu.

### <a name="installing-kernel-modules-without-hyper-v"></a>Instalace modulů jádra bez technologie Hyper-V
Azure běží na hypervisoru technologie Hyper-V, takže Linux vyžaduje, aby se v Azure spouštěly určité moduly jádra. Pokud máte virtuální počítač, který byl vytvořen mimo technologii Hyper-V, instalační programy pro Linux nemusí zahrnovat ovladače pro technologii Hyper-V v počátečním disku RAM (Image initrd nebo initramfs), pokud virtuální počítač nezjistí, že je spuštěn v prostředí technologie Hyper-V. Když při použití jiného virtualizačního systému (například VirtualBox, KVM atd.) připravujete svou image pro Linux, možná budete muset znovu sestavit image initrd, aby byly na počátečním disku RAM k dispozici aspoň hv_vmbus a hv_storvsc moduly jádra.  Tento známý problém je pro systémy založené na nadřazené distribuci Red Hat a případně na dalších.

Mechanismus pro nové sestavení image image initrd nebo initramfs se může lišit v závislosti na distribuci. Vhodný postup najdete v dokumentaci k distribuci nebo podpoře.  Tady je jeden příklad pro nové sestavení image initrd pomocí `mkinitrd` nástroje:

1. Zazálohujte existující image image initrd:

    ```
    cd /boot
    sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak
    ```

2. Znovu sestavte image initrd pomocí modulů jádra hv_vmbus a hv_storvsc:

    ```
    sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`
    ```

### <a name="resizing-vhds"></a>Změna velikosti virtuálních pevných disků
Image VHD v Azure musí mít virtuální velikost zarovnaná na 1 MB.  Virtuální pevné disky vytvořené pomocí technologie Hyper-V jsou obvykle zarovnané správně.  Pokud se virtuální pevný disk nerovná správně, může se při pokusu o vytvoření image z VHD zobrazit chybová zpráva podobná následující.

* Virtuální pevný disk http: \/ / \<mystorageaccount> . blob.Core.Windows.NET/VHDs/MyLinuxVM.VHD má nepodporovanou virtuální velikost 21475270656 bajtů. Velikost musí být celé číslo (v MB).

V takovém případě změňte velikost virtuálního počítače pomocí konzoly Správce technologie Hyper-V nebo rutiny [změnit velikost-VHD](/powershell/module/hyper-v/resize-vhd?view=win10-ps) PowerShell.  Pokud nepoužíváte v prostředí Windows, doporučujeme použít příkaz `qemu-img` k převedení (v případě potřeby) a změně velikosti VHD.

> [!NOTE]
> Verze [qemu-img obsahuje známou chybu](https://bugs.launchpad.net/qemu/+bug/1490611) >= 2.2.1, která má za následek nesprávně naformátovaný virtuální pevný disk. Tento problém byl opravený v QEMU 2,6. Doporučujeme použít buď `qemu-img` 2.2.0, nebo nižší, nebo 2,6 nebo vyšší.
> 

1. Změna velikosti VHD přímo pomocí nástrojů, jako je `qemu-img` nebo `vbox-manage` může mít za následek nespouštěcí virtuální pevný disk.  Doporučujeme nejprve převést virtuální pevný disk na bitovou kopii nezpracovaného disku.  Pokud se image virtuálního počítače vytvořila jako image nezpracovaného disku (výchozí pro některé hypervisory, jako je třeba KVM), můžete tento krok přeskočit.
 
    ```
    qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw
    ```

1. Vypočítejte požadovanou velikost image disku tak, aby byla virtuální velikost zarovnána na 1 MB.  Následující skript prostředí bash používá `qemu-img info` k určení virtuální velikosti image disku a pak vypočítá velikost na dalších 1 MB.

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

4. Nyní převeďte nezpracovaný disk zpátky na virtuální pevný disk s pevnou velikostí.

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

   Nebo s qemu verze 2.6 +, zahrňte `force_size` možnost.

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

## <a name="linux-kernel-requirements"></a>Požadavky na jádro systému Linux

Ovladače služby Linux Integration Services (LIS) pro Hyper-V a Azure se přímo podílejí na jádru nadřazeného Linux. Mnoho distribucí, které obsahují poslední verzi jádra systému Linux (například 3. x), mají tyto ovladače již k dispozici, nebo jinak poskytují nepoužívané verze těchto ovladačů s jejich jádry.  Tyto ovladače se neustále aktualizují v nadřazeném jádru s novými opravami a funkcemi, takže pokud je to možné, doporučujeme spustit [schválenou distribuci](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , která zahrnuje tyto opravy a aktualizace.

Pokud používáte variantu Red Hat Enterprise Linux verzí 6,0 až 6,3, budete muset nainstalovat [nejnovější ovladače aplikace lis pro technologii Hyper-V](https://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). Počínaje RHEL 6.4 + (a deriváty) jsou už ovladače aplikace LIS součástí jádra, takže není potřeba žádné další instalační balíčky.

Pokud je nutné vlastní jádro, doporučujeme použít nejnovější verzi jádra (například 3.8 +). U distribucí nebo dodavatelů, kteří udržují své vlastní jádro, budete muset pravidelně Backport ovladače z nadřazeného jádra do vlastního jádra.  I když už používáte relativně poslední verzi jádra, důrazně doporučujeme, abyste si udrželi přehled o všech případných opravách v ovladačích aplikace LIS a Backport je podle potřeby. Umístění zdrojových souborů ovladače aplikace LIS jsou uvedena v souboru [Maintainer](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) v stromu zdrojového kódu jádra systému Linux:
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
V jádru musí být zahrnuté následující opravy. Tento seznam se nedá dokončit pro všechny distribuce.

* [ata_piix: ve výchozím nastavení odložit disky do ovladačů technologie Hyper-V](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc: účet pro pakety v přenosu v cestě pro obnovení](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc: Vyhněte se použití WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc: zakázat zápis u ovladačů RAID a virtuálních hostitelských adaptérů](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc: Oprava zpětného odkazu na ukazatel na hodnotu NULL](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc: selhání vyrovnávací paměti prstence může způsobit zablokování vstupně-výstupních operací](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: Ochrana před dvojitým provedením __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>Agent Azure Linux
[Agent Azure Linux](../extensions/agent-linux.md) `waagent` zřídí virtuální počítač Linux v Azure. Můžete získat nejnovější verzi, problémy se soubory nebo odeslat žádosti o získání dat v [úložišti GitHub agenta pro Linux](https://github.com/Azure/WALinuxAgent).

* Agent pro Linux se uvolní v rámci licence Apache 2,0. Mnoho distribucí již pro agenta poskytuje balíčky ot. deb a tyto balíčky lze snadno nainstalovat a aktualizovat.
* Agent Azure Linux vyžaduje Python v 2.6 +.
* Agent také vyžaduje modul Python-pyasn1. Většina distribucí poskytuje tento modul jako samostatný balíček, který se má nainstalovat.
* V některých případech nemusí být agent Azure Linux kompatibilní s NetworkManager. Mnoho balíčků ot/deb poskytovaných distribucí nakonfiguruje NetworkManager jako konflikt balíčku waagent. V těchto případech dojde k odinstalaci NetworkManager při instalaci balíčku agenta pro Linux.
* Agent Azure Linux musí mít minimálně [podporovanou verzi](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

## <a name="general-linux-system-requirements"></a>Obecné požadavky na systém Linux

1. Upravte spouštěcí řádek jádra v GRUB nebo GRUB2 tak, aby zahrnoval následující parametry, aby byly všechny zprávy konzoly odesílány na první sériový port. Tyto zprávy můžou pomoct podporu Azure při ladění všech problémů.
    ```  
    console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
    ```
    Pokud existují, doporučujeme také *Odebrat* následující parametry.
    ```  
    rhgb quiet crashkernel=auto
    ```
    Grafické a tiché spouštění se nehodí v cloudovém prostředí, kde chceme, aby se všechny protokoly odesílaly na sériový port. `crashkernel`Možnost v případě potřeby může být nakonfigurované, ale Všimněte si, že tento parametr snižuje množství dostupné paměti virtuálního počítače minimálně 128 MB, což může být problematické pro menší velikosti virtuálních počítačů.

1. Nainstalujte agenta Azure Linux.
  
    K zřizování image pro Linux v Azure se vyžaduje agent Azure Linux.  Mnoho distribucí poskytuje agentovi jako soubor ot./min. nebo balíček. deb (balíček se obvykle označuje jako WALinuxAgent nebo WALinuxAgent).  Agenta je také možné nainstalovat ručně podle kroků v [Průvodci agenta pro Linux](../extensions/agent-linux.md).

1. Ujistěte se, že je server SSH nainstalovaný a že je nakonfigurován tak, aby se spouštěl v době spuštění.  Tato konfigurace je obvykle výchozí.

1. Nevytvářejte odkládací místo na disku s operačním systémem.
  
    Agent Azure Linux může automaticky nakonfigurovat odkládací prostor pomocí disku místního prostředku, který je připojen k virtuálnímu počítači po zřízení v Azure. Disk místního prostředku je *dočasný* disk a při zrušení zřízení virtuálního počítače může dojít k jeho vyprázdnění. Po instalaci agenta Azure Linux (krok 2 výše) upravte podle potřeby následující parametry v/etc/waagent.conf.
    ```  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: Set this to your desired size.
    ```
1. Spuštěním následujících příkazů zrušení zřízení virtuálního počítače.
  
     ```
     sudo waagent -force -deprovision
     export HISTSIZE=0
     logout
     ```  
   > [!NOTE]
   > V VirtualBox se může po spuštění tohoto říká zobrazit následující chyba `waagent -force -deprovision` `[Errno 5] Input/output error` . Tato chybová zpráva není kritická a je možné ji ignorovat.

* Vypněte virtuální počítač a nahrajte virtuální pevný disk do Azure.
