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
ms.date: 03/12/2018
ms.author: szark
ms.openlocfilehash: 17b4df83b141d5365a8d6244c4ab73b0eba5ed73
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38972345"
---
# <a name="information-for-non-endorsed-distributions"></a>Informace pro neschválené distribuce
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Platforma Azure vztahuje smlouva SLA pro virtuální počítače spuštěné operačního systému Linux, pouze pokud je to jeden z [distribuce schválené pro](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) se používá. Pro tyto doporučených distribucích jsou k dispozici imagí Linuxu v Tržišti Azure Marketplace s požadovanou konfigurací.

* [Distribuce schválené pro Linux v Azure –](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Podpora pro Linuxové Image v Microsoft Azure](https://support.microsoft.com/kb/2941892)

Všechny distribuce běžící v Azure potřebujete ke splnění předpokladů mít příležitost dobře se správně spustit na platformě.  Tento článek je komplexní v žádném smyslu, protože každá distribuce se liší. a je možné, že i v případě, že splňujete následující kritéria budete muset upravit výrazně systému Linux a ujistěte se, že správně běží na platformě.

Je proto doporučujeme začít s [Linux v distribucích schválených pro Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Pokud je to možné. V následujícím článku vás provede postup přípravy různých doporučených distribucích systému Linux, podporované v Azure:

* **[Distribuce založené na centOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Zbývající část tohoto článku se zaměřuje na obecné pokyny pro spuštění vaší distribuci Linuxu v Azure.

## <a name="general-linux-installation-notes"></a>Poznámky k instalaci obecné Linux
* Formát VHDX nepodporuje v Azure, pouze **oprava virtuálního pevného disku**.  Převést disk na formát virtuálního pevného disku pomocí Správce technologie Hyper-V nebo rutiny convert-vhd. Pokud používáte VirtualBox to znamená, že vyberete **pevnou velikost** na rozdíl od výchozí dynamicky přidělené při vytváření disku.
* Azure podporuje pouze virtuální počítače generace 1. Virtuální počítač generace 1 můžete převést do formátu souboru virtuálního pevného disku VHDX a dynamicky se zvětšující na disk pevné velikosti. Nelze však změnit generaci virtuálního počítače. Další informace najdete v tématu [bych si měl vytvořit virtuální počítač generace 1 nebo 2 v Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)
* Maximální velikost povolenou pro virtuální pevný disk je 1,023 GB.
* Při instalaci systému Linux je *doporučuje* použít standardní oddíly spíše než LVM (často výchozí nastavení pro mnoho zařízení). Tím se vyhnete LVM název je v konfliktu s klonovaný virtuální počítače, zejména v případě, že disk s operačním systémem je někdy potřeba připojit k jinému virtuálnímu počítači identické pro řešení potíží. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) může být použita na datové disky.
* Je nutné podporovat jádra pro připojení systému souborů UDF. Při prvním spuštění počítače v Azure je konfigurace zřizování předány do virtuálního počítače s Linuxem prostřednictvím formátu UDF média, který je připojen k hosta. Agent Azure Linux musí být schopen připojit a načíst jeho konfiguraci a zřízení virtuálního počítače v systému souborů UDF.
* Verze jádra Linuxu níže 2.6.37 nepodporují NUMA v Hyper-V s větší velikostí virtuálních počítačů. Tento problém ovlivňuje hlavně starší distribuce využívající nadřazený jádra systému Red Hat 2.6.32 a byla stanovena v RHEL 6.6 (jádra 2.6.32 504). Systémy s operačním systémem vlastní jádrech starší než 2.6.37 nebo systémem RHEL jádrech starší než 2.6.32-504 musí nastavit parametr spouštěcí `numa=off` na příkazový řádek v grub.conf jádra. Další informace najdete v článku Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Neprovádějte konfiguraci odkládací oddíl na disk s operačním systémem. Chcete-li vytvořit odkládací soubor na disku dočasný prostředek, který lze nastavit agenta pro Linux.  Další informace najdete v níže uvedeném postupu.
* Všechny virtuální pevné disky v Azure musí mít virtuální velikost, zarovnání na 1MB. Při převodu z nezpracované disku do virtuálního pevného disku je nutné zajistit, že velikost nezpracovaných disku je násobkem 1MB před převodem. Další informace najdete v níže uvedeném postupu.

### <a name="installing-kernel-modules-without-hyper-v"></a>Instalace modulů jádra bez technologie Hyper-V
Azure běží na hypervizoru Hyper-V Linux vyžaduje, že jsou nainstalované některé moduly jádra, aby bylo možné spustit v Azure. Pokud máte virtuální počítač, který byl vytvořen mimo Hyper-V, instalačních programů Linux nemusí obsahovat ovladače pro technologii Hyper-V v počáteční disku paměti RAM (initrd nebo initramfs) Pokud zjistí, že běží v prostředí Hyper-V. Při používání různých virtualizace systému (tj. Virtualbox, KVM atd.) k přípravě image s Linuxem, je nutné znovu sestavit initrd zajistit, aby alespoň `hv_vmbus` a `hv_storvsc` jádra moduly jsou k dispozici na počáteční disku paměti RAM.  Jde o známý problém alespoň na systémy založené na nadřazený distribuční Red Hat.

Mechanismus pro nové sestavení image initrd nebo initramfs může lišit v závislosti na distribuci. Správný postup naleznete v dokumentaci vaší distribuce nebo podpory.  Tady je jeden příklad, jak znovu sestavte pomocí initrd `mkinitrd` nástroje:

Nejprve proveďte zálohu existující image initrd:

    # cd /boot
    # sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak

V dalším kroku znovu sestavit initrd s `hv_vmbus` a `hv_storvsc` modulů jádra:

    # sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`


### <a name="resizing-vhds"></a>Změna velikosti virtuálních pevných disků
Image virtuálních pevných disků v Azure musí mít virtuální velikost, zarovnání na 1 MB.  Obvykle virtuální pevné disky vytvořené pomocí technologie Hyper-V musí již být správně zarovnány.  Pokud virtuální pevný disk není zarovnaný správně, může zobrazit chybová zpráva podobná následující při pokusu o vytvoření *image* z vašeho virtuálního pevného disku:

    "The VHD http://<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd has an unsupported virtual size of 21475270656 bytes. The size must be a whole number (in MBs).”

Chcete tento problém napravit, změňte velikost virtuálního počítače pomocí konzoly Správce technologie Hyper-V nebo [změny velikosti virtuálního pevného disku](http://technet.microsoft.com/library/hh848535.aspx) rutiny Powershellu.  Pokud používáte v prostředí Windows, doporučujeme používat qemu img převést (v případě potřeby) a změna velikosti virtuálního pevného disku.

> [!NOTE]
> Je známého problému v qemu img verze > = 2.2.1, jejímž výsledkem nesprávně formátovaná VHD. Problém byl vyřešen ve verzi 2.6 QEMU. Doporučuje se použít qemu-img 2.2.0 nebo nižší ani aktualizovat na 2.6 nebo novější. Referenční dokumentace: https://bugs.launchpad.net/qemu/+bug/1490611.
> 
> 

1. Změna velikosti virtuálního pevného disku přímo pomocí nástrojů, jako `qemu-img` nebo `vbox-manage` může vést nelze spustit virtuální pevný disk.  Proto se doporučuje první převést virtuální pevný disk do bitové kopie disku NEZPRACOVANÉ.  Pokud image virtuálního počítače už byl vytvořen jako RAW disk obrázek (výchozí pro některé hypervisory jako je například KVM) může tento krok přeskočit:
   
       # qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw

2. Vypočítá požadovaná velikost bitové kopie disku a ujistěte se, že virtuální velikost je umístěno na 1 MB.  S tím vám můžou pomoct následující skript prostředí bash.  Tento skript využívá "`qemu-img info`" určit virtuální velikost bitové kopie disku a pak vypočítá velikost další 1 MB:
   
       rawdisk="MyLinuxVM.raw"
       vhddisk="MyLinuxVM.vhd"
   
       MB=$((1024*1024))
       size=$(qemu-img info -f raw --output json "$rawdisk" | \
              gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
   
       rounded_size=$((($size/$MB + 1)*$MB))
       echo "Rounded Size = $rounded_size"

3. Změnit velikost nezpracovaných disku pomocí $rounded_size jako sada ve výše uvedené skriptu:
   
       # qemu-img resize MyLinuxVM.raw $rounded_size

4. Nyní NEZPRACOVANÁ disku převeďte zpět na virtuální pevný disk pevné velikosti:
   
       # qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd

   Nebo s verzí qemu **2.6 +** zahrnout `force_size` možnost:

       # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd

## <a name="linux-kernel-requirements"></a>Požadavky na jádro Linuxu
Jsou ovladače služby Linux Integration Services (LIS) pro Hyper-V a Azure přispět přímo do nadřazeného jádro Linuxu. Množství distribucí, které obsahují nejnovější verze jádra Linux (tj. 3.x) již mít k dispozici tyto ovladače nebo jiný způsob dodání přeneseny zpět verze těchto ovladačů s jejich jádra.  Tyto ovladače neustále aktualizují se v jádru nadřazeného se nové opravy a funkce, takže pokud je to možné se doporučuje spustit [distribuce schválené pro](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , který obsahuje tyto opravy a aktualizace.

Pokud používáte hodnotu typu variant Red Hat Enterprise Linux verze **6.0 6.3**, pak je potřeba nainstalovat nejnovější ovladače služby LIS pro Hyper-V. Ovladače lze nalézt [na tomto místě](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). Od verze RHEL **6.4 +** (a vy) ovladače služby LIS jsou již zahrnuty v rámci jádra a proto žádné další instalační balíčky jsou potřeba ke spouštění těchto systémů v Azure.

Pokud vlastní jádra je potřeba, doporučuje se použít novější verze jádra (to znamená **3.8 +**). Pro tyto distribucí nebo dodavatele, kteří udržují vlastní jádra, je potřeba pravidelně backport LIS některé úsilí ovladače z nadřazeného jádra k vlastní jádra.  I v případě, že už používáte relativně novější verze jádra, důrazně doporučujeme můžete sledovat všechny upstreamové opravy ovladače služby LIS a backport ty podle potřeby. Je k dispozici v umístění zdrojových souborů ovladače služby LIS [programu](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) soubor ve zdrojovém stromu jádro Linuxu:

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

Minimálně neexistence následující opravy je známo způsobit problémy v Azure a tak tyto musí být součástí jádra. Tento seznam je v žádném smyslu vyčerpávající nebo dokončení pro všechny distribuce:

* [ata_piix: ve výchozím nastavení odložení disky k ovladačům Hyper-V](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [storvsc: účet pro použití během přenosu paketů na cestě k RESETOVÁNÍ](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [storvsc: Vyhněte se použití WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [storvsc: zakázat zápis stejné diskového pole RAID a ovladače adaptéru virtuální hostitel](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [storvsc: oprava přesměrování ukazatele NULL](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [storvsc: selhání kanál vyrovnávací paměti může způsobit zablokování vstupně-výstupních operací](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: ochrana proti double provádění __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>Azure Linux Agent
[Agenta Azure Linux](../extensions/agent-linux.md) (waagent) je potřeba správně zřízení virtuálního počítače s Linuxem v Azure. Můžete získat nejnovější verzi, soubor problémy nebo odeslání žádosti o přijetí změn na [úložiště GitHub Agent systému Linux](https://github.com/Azure/WALinuxAgent).

* Agenta pro Linux je všeobecně dostupné v rámci licence Apache 2.0. Množství distribucí již zadejte RPM nebo deb balíčky pro agenta, a proto v některých případech to lze nainstalovat a aktualizovat pomocí malé úsilí.
* Azure Linux Agent vyžaduje Python v2.6 +.
* Agent také vyžaduje modul python pyasn1. Většině distribucí to poskytnout jako samostatný balíček, který je možné nainstalovat.
* V některých případech nemusí být kompatibilní s NetworkManager agenta Azure Linux. Mnoho balíčků RPM nebo Deb distribucí poskytovaných nakonfigurujte NetworkManager konflikt waagent balíčku a tak se odinstaluje NetworkManager při instalaci balíčku agent systému Linux.
* Azure Linux Agent musí být vyšší než minimální podporovaná verze, najdete v tomto článku [podrobnosti](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

## <a name="general-linux-system-requirements"></a>Požadavky na systém Linux obecné

* Upravte řádek pro spuštění jádra v GRUB nebo GRUB2 zahrnují následující parametry. To také zajistí, že jsou všechny konzoly zprávy odeslané do první sériového portu, který vám může pomoct Azure odborné pomoci s laděním problémů:
  
        console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
  
    To také zajistí, že jsou všechny konzoly zprávy odeslané do první sériového portu, který vám může pomoct Azure odborné pomoci s laděním problémů.
  
    Kromě výše uvedeného, doporučuje se *odebrat* Pokud existují následující parametry:
  
        rhgb quiet crashkernel=auto
  
    Grafické a quiet spouštěcí není užitečný v cloudovém prostředí, ve kterém chceme, všech protokolů k odeslání do sériového portu. `crashkernel` Možnost může být levé straně nakonfigurované v případě potřeby, ale Všimněte si, že tento parametr snižuje množství dostupné paměti ve virtuálním počítači 128 MB nebo víc, což může být problematické u menší velikosti virtuálních počítačů.

* Instalace agenta Azure Linux
  
    Azure Linux Agent je vyžadován pro zřizování image Linuxu v Azure.  Množství distribucí poskytují agenta jako balíčku RPM nebo Deb (balíček se obvykle nazývá 'WALinuxAgent' nebo "walinuxagent").  Agenta můžete také nainstalovat ručně pomocí následujících kroků v [Průvodce Agent systému Linux](../extensions/agent-linux.md).

* Zajistěte, aby SSH server je nainstalován a nakonfigurován na spuštění při spuštění.  Obvykle se jedná o výchozí nastavení.

* Nevytvářejte odkládacího prostoru na disku s operačním systémem
  
    Azure Linux Agent mohou automaticky konfigurovat odkládacího prostoru pomocí disku místního prostředku, který je připojen k virtuálnímu počítači po zřízení v Azure. Je disku místního prostředku *dočasné* disk a může být vyprázdněna při zřízení virtuálního počítače. Po instalaci agenta Azure Linux (viz předchozí krok), upravte následující parametry v /etc/waagent.conf odpovídajícím způsobem:
  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

* V posledním kroku spusťte následující příkazy ke zrušení zřízení virtuálního počítače:
  
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
  
  > [!NOTE]
  > Na Virtualbox může se zobrazit následující chyba po spuštění ' waagent-force - zrušení zřízení ': `[Errno 5] Input/output error`. Tato chybová zpráva není důležité a můžete ignorovat.
  > 
  > 

* Vypnout virtuální počítač a nahrání virtuálního pevného disku do Azure.

