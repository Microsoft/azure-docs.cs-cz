---
title: Vytvoření a nahrání VHD s Linuxem v Azure
description: Zjistěte, jak vytvořit a nahrát Azure virtuálního pevného disku (VHD), který obsahuje operační systém Linux.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: d351396c-95a0-4092-b7bf-c6aae0bbd112
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: szark
ms.openlocfilehash: 67796cc3cbb925bb18a917d17b8abb7c085de370
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638189"
---
# <a name="information-for-non-endorsed-distributions"></a>Informace pro neschválené distribuce
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Platforma Azure vztahuje smlouva SLA pro virtuální počítače spuštěné operačního systému Linux, pouze pokud je to jeden z [distribuce schválené pro](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) se používá. Pro tyto doporučených distribucích jsou k dispozici předem nakonfigurovaných imagí Linuxu v Tržišti Azure Marketplace.

* [Distribuce schválené pro Linux v Azure –](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Podpora pro Linuxové Image v Microsoft Azure](https://support.microsoft.com/kb/2941892)

Všechny distribuce běžící v Azure mají počet požadavků. Tento článek nemůže být kompletní, protože každá distribuce se liší. I v případě, že splňujete následující kritéria, budete muset upravit výrazně systému Linux, chcete-li správně fungovat.

Doporučujeme začít s některou [Linux v distribucích schválených pro Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Následující články ukazují, jak připravit různých doporučených distribucích systému Linux, podporované v Azure:

* **[Distribuce založené na centOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Tento článek se zaměřuje na obecné pokyny pro spuštění vaší distribuci Linuxu v Azure.

## <a name="general-linux-installation-notes"></a>Poznámky k instalaci obecné Linux
* Formát virtuálního pevného disku (VHDX) technologie Hyper-V se nepodporuje v Azure, pouze *oprava virtuálního pevného disku*.  Disk můžete převést do formátu virtuálního pevného disku pomocí Správce technologie Hyper-V nebo [Convert-VHD](https://docs.microsoft.com/powershell/module/hyper-v/convert-vhd) rutiny. Pokud používáte VirtualBox, vyberte **pevnou velikost** místo výchozí (dynamicky přidělit) při vytváření disku.
* Azure podporuje pouze virtuální počítače generace 1. Můžete převést virtuální počítač generace 1 z formátu souboru virtuálního pevného disku VHDX a dynamicky se zvětšující na disk pevné velikosti. Nelze změnit generaci virtuálního počítače. Další informace najdete v tématu [bych si měl vytvořit virtuální počítač generace 1 nebo 2 v Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)
* Maximální velikost povolenou pro virtuální pevný disk je 1,023 GB.
* Při instalaci systému Linux, které doporučujeme použít standardní oddíly, nikoli logické svazku správce (LVM) což je výchozí nastavení pro mnoho zařízení. Pomocí standardní oddíly se vyhnete LVM název je v konfliktu s klonovaný virtuální počítače, zejména v případě, že disk s operačním systémem je někdy připojen k jinému virtuálnímu počítači identické pro řešení potíží. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) může být použita na datové disky.
* Podpora jádra pro připojení systému souborů UDF je nezbytné. Při prvním spuštění počítače v Azure předána konfigurace zřizování virtuálního počítače s Linuxem s použitím formátu UDF média, který je připojen k hosta. Agent Azure Linux musí připojit a načíst jeho konfiguraci a zřízení virtuálního počítače v systému souborů UDF.
* Verze jádra Linuxu dříve než 2.6.37 nepodporují NUMA v Hyper-V s větší velikostí virtuálních počítačů. Tento problém ovlivňuje hlavně starší distribuce využívající nadřazený jádra systému Red Hat 2.6.32 a byla stanovena v Red Hat Enterprise Linux (RHEL) 6.6 (jádra 2.6.32 504). Systémy s operačním systémem vlastní jádrech starší než 2.6.37 nebo systémem RHEL jádrech starší než 2.6.32-504 musí nastavit parametr spouštěcí `numa=off` na příkazovém řádku v grub.conf jádra. Další informace najdete v tématu [436883 znalostní BÁZE Red Hat](https://access.redhat.com/solutions/436883).
* Nekonfigurujte odkládací oddíl na disk s operačním systémem. Chcete-li vytvořit odkládací soubor na disku dočasný prostředek, který je nakonfigurovat agenta pro Linux, jak je popsáno v následujících krocích.
* Všechny virtuální pevné disky v Azure musí mít virtuální velikost, zarovnání na 1 MB. Při převodu z nezpracované disku do virtuálního pevného disku je nutné zajistit, že velikost nezpracovaných disku je násobkem 1 MB před převodem, jak je popsáno v následujících krocích.

### <a name="installing-kernel-modules-without-hyper-v"></a>Instalace modulů jádra bez technologie Hyper-V
Azure běží na hypervizoru Hyper-V Linux vyžaduje některé moduly jádra na spuštění v Azure. Pokud máte virtuální počítač, který byl vytvořen mimo Hyper-V, instalačních programů Linux nemusí obsahovat ovladače pro technologii Hyper-V v počáteční disku paměti RAM (initrd nebo initramfs), pokud virtuální počítač rozpozná, že běží v prostředí Hyper-V. Při používání různých virtualizace systému (například Virtualbox a KVM) k přípravě image s Linuxem, budete muset znovu vytvořit initrd tak aby nejnižší hv_vmbus a hv_storvsc jádra moduly jsou k dispozici na počáteční disku paměti RAM.  Tento známý problém se týká systémů založených na nadřazený distribuční Red Hat a případně jiné.

Mechanismus pro nové sestavení image initrd nebo initramfs může lišit v závislosti na distribuci. Správný postup naleznete v dokumentaci vaší distribuce nebo podpory.  Tady je jeden příklad pro nové sestavení initrd pomocí `mkinitrd` nástroje:

1. Proveďte zálohu existující image initrd:

    ```
    cd /boot
    sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak
    ```

2. Znovu sestavte initrd s hv_vmbus a hv_storvsc modulů jádra:

    ```
    sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`
    ```

### <a name="resizing-vhds"></a>Změna velikosti virtuálních pevných disků
Image virtuálních pevných disků v Azure musí mít virtuální velikost, zarovnání na 1 MB.  Obvykle je správně zarovnán virtuální pevné disky vytvořené pomocí technologie Hyper-V.  Pokud virtuální pevný disk není správně zarovnán, zobrazí chybová zpráva podobná následující při pokusu o vytvoření image z vašeho virtuálního pevného disku.

* Virtuální pevný disk http://<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd má nepodporovanou virtuální velikost 21475270656 bajtů. Velikost musí být celé číslo (v MB).

V takovém případě změňte velikost virtuálního počítače pomocí konzoly Správce technologie Hyper-V nebo [změny velikosti virtuálního pevného disku](http://technet.microsoft.com/library/hh848535.aspx) rutiny Powershellu.  Pokud nejsou spuštěny v prostředí s Windows, doporučujeme použít `qemu-img` pro převod (v případě potřeby) a změna velikosti virtuálního pevného disku.

> [!NOTE]
> Je [známého problému nástroje qemu img](https://bugs.launchpad.net/qemu/+bug/1490611) verze > = 2.2.1, jejímž výsledkem nesprávně formátovaná VHD. Problém byl vyřešen ve verzi 2.6 QEMU. Doporučujeme použít buď `qemu-img` 2.2.0 nebo nižší, nebo 2.6 nebo novější.
> 

1. Změna velikosti virtuálního pevného disku přímo pomocí nástrojů, jako `qemu-img` nebo `vbox-manage` může vést nelze spustit virtuální pevný disk.  Doporučujeme nejprve převod virtuálního pevného disku na bitové kopii disku NEZPRACOVANÉ.  Pokud jako RAW disk image (výchozí pro některé hypervisory jako je například KVM) byla vytvořena image virtuálního počítače, může tento krok přeskočit.
 
    ```
    qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw
    ```

1. Vypočítá požadovaná velikost bitové kopie disku, takže virtuální velikost je 1 MB.  Použití skriptů prostředí bash následující `qemu-img info` určit virtuální velikost bitové kopie disku a pak vypočítá velikost další 1 MB.

    ```bash
    rawdisk="MyLinuxVM.raw"
    vhddisk="MyLinuxVM.vhd"

    MB=$((1024*1024))
    size=$(qemu-img info -f raw --output json "$rawdisk" | \
    gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')

    rounded_size=$((($size/$MB + 1)*$MB))
    
    echo "Rounded Size = $rounded_size"
    ```

3. Změnit velikost nezpracovaných disk pomocí `$rounded_size` výše.

    ```bash
    qemu-img resize MyLinuxVM.raw $rounded_size
    ```

4. Nyní NEZPRACOVANÁ disku převeďte zpět na virtuální pevný disk pevné velikosti.

    ```bash
    qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

   Nebo qemu verze 2.6 +, zahrnout `force_size` možnost.

    ```bash
    qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd
    ```

## <a name="linux-kernel-requirements"></a>Požadavky na jádro Linuxu

Jsou ovladače služby Linux Integration Services (LIS) pro Hyper-V a Azure přispět přímo do nadřazeného jádro Linuxu. Množství distribucí, které obsahují nejnovější verze jádra Linuxu (například 3.x) již mít k dispozici tyto ovladače nebo jiný způsob dodání přeneseny zpět verze těchto ovladačů s jejich jádra.  Tyto ovladače neustále aktualizují se v jádru nadřazeného se nové opravy a funkce, takže pokud je to možné, doporučujeme vám běží [distribuce schválené pro](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , který obsahuje tyto opravy a aktualizace.

Pokud používáte hodnotu typu variant Red Hat Enterprise Linux verze 6.0 k 6.3 a potom budete muset nainstalovat [nejnovější ovladače služby LIS pro Hyper-V](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). Od verze RHEL 6.4 + (a vy) ovladače služby LIS jsou již zahrnuty v rámci jádra a proto žádné další instalační balíčky nejsou potřeba.

Pokud vlastní jádra je potřeba, doporučujeme nejnovější verze jádra (například 3.8 +). Pro distribuce nebo dodavatele, kteří udržují vlastní jádra bude potřeba pravidelně backport ovladače služby LIS z nadřazeného jádra na vlastní jádra.  I v případě, že už používáte relativně novější verze jádra, důrazně doporučujeme udržovat přehled o žádné upstream opravuje ovladače služby LIS a backport je podle potřeby. Umístění zdrojových souborů ovladače služby LIS jsou určené v [programu](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) soubor ve zdrojovém stromu jádro Linuxu:
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
Následující opravy musí být součástí jádra. Tento seznam nelze dokončit pro všechny distribuce.

* [ata_piix: ve výchozím nastavení odložení disky k ovladačům Hyper-V](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc: účet pro použití během přenosu paketů na cestě k RESETOVÁNÍ](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc: Vyhněte se použití WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc: zakázat zápis stejné diskového pole RAID a ovladače adaptéru virtuální hostitel](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc: oprava přesměrování ukazatele NULL](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc: selhání kanál vyrovnávací paměti může způsobit zablokování vstupně-výstupních operací](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: ochrana proti double provádění __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>Azure Linux Agent
[Agenta Azure Linux](../extensions/agent-linux.md) `waagent` zřizuje virtuální počítač s Linuxem v Azure. Můžete získat nejnovější verzi, soubor problémy nebo odeslání žádosti o přijetí změn na [úložiště GitHub Agent systému Linux](https://github.com/Azure/WALinuxAgent).

* Agenta pro Linux je všeobecně dostupné v rámci licence Apache 2.0. Množství distribucí už zadejte RPM nebo deb balíčky pro agenta, a tyto balíčky se můžou snadno nainstalovat a aktualizovat.
* Azure Linux Agent vyžaduje Python v2.6 +.
* Agent také vyžaduje modul python pyasn1. Většině distribucí poskytují jako samostatný balíček k instalaci tohoto modulu.
* V některých případech nemusí být kompatibilní s NetworkManager agenta Azure Linux. Mnoho balíčků RPM nebo Deb poskytované distribuce konfigurace NetworkManager jako konflikt waagent balíčku. V těchto případech je odinstalovat NetworkManager při instalaci balíčku agent systému Linux.
* Azure Linux Agent musí být dosahovalo nebo přesahovalo [minimální podporovaná verze](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

## <a name="general-linux-system-requirements"></a>Požadavky na systém Linux obecné

1. Upravte řádek pro spuštění jádra v GRUB nebo GRUB2 zahrnout tyto parametry tak, aby všechny zprávy konzoly se odesílají do první sériového portu. Tyto zprávy mohou pomoci Azure odborné pomoci s laděním problémů.
    ```  
    console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
    ```
    Doporučujeme také *odebrání* následující parametry, pokud existují.
    ```  
    rhgb quiet crashkernel=auto
    ```
    Grafické a quiet spouštěcí není užitečné v cloudovém prostředí, ve kterém chceme, aby všechny protokoly se odeslaly do sériového portu. `crashkernel` Možnost může být levé straně nakonfigurované v případě potřeby, ale Všimněte si, že tento parametr snižuje množství dostupné paměti ve virtuálním počítači na nejméně 128 MB, což může být problematické pro menší velikosti virtuálních počítačů.

2. Instalace agenta Azure Linux.
  
    Azure Linux Agent je vyžadován pro zřizování image Linuxu v Azure.  Množství distribucí poskytují agenta jako balíčku RPM nebo Deb (balíček je obvykle volána WALinuxAgent nebo walinuxagent).  Agenta můžete také nainstalovat ručně pomocí následujících kroků v [Průvodce Agent systému Linux](../extensions/agent-linux.md).

3. Zkontrolujte, zda je SSH server nainstalován a nakonfigurován na spuštění při spuštění.  Tato konfigurace je obvykle výchozí.

4. Nevytvářejte velikosti odkládacího souboru v disku s operačním systémem.
  
    Azure Linux Agent mohou automaticky konfigurovat odkládacího prostoru pomocí disku místního prostředku, který je připojen k virtuálnímu počítači po zřízení v Azure. Je disku místního prostředku *dočasné* disk a může být vyprázdněna při zřízení virtuálního počítače. Po instalaci agenta Azure Linux (kroku 2 výše), upravte následující parametry v /etc/waagent.conf podle potřeby.
    ```  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: Set this to your desired size.
    ```
* Spusťte následující příkazy ke zrušení zřízení virtuálního počítače.
  
    ```
    sudo waagent -force -deprovision
    export HISTSIZE=0
    logout
    ```  
  > [!NOTE]
  > Na Virtualbox může se zobrazit následující chyba po spuštění `waagent -force -deprovision` , která říká `[Errno 5] Input/output error`. Tato chybová zpráva není důležité a můžete ignorovat.

* Vypnout virtuální počítač a nahrání virtuálního pevného disku do Azure.

