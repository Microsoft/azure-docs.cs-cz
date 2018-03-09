---
title: "Vytvoření a nahrání virtuálního pevného disku Linux v Azure"
description: "Naučte se vytvořit a odeslat Azure virtuálního pevného disku (VHD) obsahující operační systém Linux."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: tysonn
tags: azure-resource-manager,azure-service-management
ms.assetid: d351396c-95a0-4092-b7bf-c6aae0bbd112
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
ms.openlocfilehash: 631557e0ad712827bb3375c4f152c0e2185fda18
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="information-for-non-endorsed-distributions"></a>Informace pro neschválené distribuce
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Platformy Azure SLA se vztahuje na virtuální počítače běžící operační systém Linux jenom v případě, že je jeden z [distribuce schválené](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) se používá. Pro tyto potvrzená distribuce bitové kopie systému Linux jsou uvedeny v Azure Marketplace s požadovanou konfigurací.

* [Distribuce schválené pro Linux ve službě Azure-](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Podpora pro Linux obrázků v Microsoft Azure](https://support.microsoft.com/kb/2941892)

Všechny distribuce spuštěné v Azure budou muset splňovat určité požadavky na šance, že správně spouštět na platformě.  Tento článek je komplexní rozhodně není, protože každý distribuční se liší. a je dost možné, že i v případě splnění všech následujících kritérií stále potřebujete k výraznému vylepšení systému Linux a ujistěte se, zda správně pracuje na platformě.

Je z tohoto důvodu, který doporučujeme spouštět některý z našich [Linux na distribuce schválené pro Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Pokud je to možné. V následujících článcích vás provede postup přípravy různé potvrzená distribuce systému Linux, které jsou podporovány v Azure:

* **[Na základě centOS distribuce](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Zbývající část tohoto článku se soustředí na obecných pokynů pro spuštění distribuční Linux v Azure.

## <a name="general-linux-installation-notes"></a>Poznámky k instalaci obecné Linux
* Formát VHDX není podporován v Azure, pouze **pevný virtuální pevný disk**.  Disk můžete převést do formátu virtuálního pevného disku pomocí Správce technologie Hyper-V nebo rutiny convert-VHD prostředí. Pokud používáte VirtualBox to znamená výběr **pevnou velikost** oproti výchozí přidělí dynamicky při vytváření disku.
* Azure podporuje pouze virtuální počítače generace 1. Virtuální počítač generace 1 můžete převést z VHDX do formátu souboru virtuálního pevného disku a dynamicky se zvětšující pevné velikosti disku. Ale nemůžete změnit generaci virtuálního počítače. Další informace najdete v tématu [by měl vytvořit virtuální počítač generace 1 nebo 2 v Hyper-V?](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)
* Maximální velikost povolenou pro virtuální pevný disk je 1,023 GB.
* Při instalaci systému Linux je *doporučená* použít standardní oddíly spíše než LVM (často výchozí pro mnoho instalace). Tím se vyhnete LVM název je v konfliktu s klonovaný virtuální počítače, zvlášť pokud někdy musí být připojen k virtuálnímu počítači jiné identické pro řešení potíží s disk s operačním systémem. [LVM](configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) nebo [RAID](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) mohou být použity na datové disky.
* Vyžaduje se podpora jádra pro připojení systémy souborů UDF. Při prvním spuštění v Azure je předána zřizování konfigurace do virtuálního počítače s Linuxem pomocí formátu UDF média, který je připojen k Host. Azure Linux agent musí být schopný se připojit a načíst jeho konfiguraci a zřídit virtuální počítač v systému souborů UDF.
* Verze jádra Linux pod 2.6.37 nepodporují NUMA v technologii Hyper-V s větší velikostí virtuálních počítačů. -Li tento problém ovlivňuje především starší distribuce pomocí nadřazený Red Hat 2.6.32 jádra která byla opravena v RHEL 6.6 (jádra 2.6.32 504). Systémy s operačním systémem vlastní jádra starší než 2.6.37 nebo na základě RHEL jádra starší než 2.6.32-504 musíte nastavit parametr spouštěcího `numa=off` na příkazového řádku v grub.conf jádra. Další informace najdete v části Red Hat [KB 436883](https://access.redhat.com/solutions/436883).
* Nekonfigurujte přepnutí oddílu na disku operačního systému. Chcete-li vytvořit odkládací soubor na disku dočasných prostředků lze nakonfigurovat agenta systému Linux.  Další informace o této naleznete v následujících krocích.
* Všechny virtuální pevné disky musí mít velikostí, které jsou násobky 1 MB.

### <a name="installing-kernel-modules-without-hyper-v"></a>Instalace modulů jádra bez technologie Hyper-V
Azure je spuštěná na hypervisoru technologie Hyper-V, takže Linux vyžaduje nainstalované moduly určité jádra pro spuštění v Azure. Pokud máte virtuální počítač, který se vytvořil mimo technologie Hyper-V, instalačních programů Linux nemusí zahrnovat ovladače pro Hyper-V v počáteční disku paměti RAM (initrd nebo initramfs) Pokud zjistí, zda je spuštěna prostředí Hyper-V. Při použití jiným virtualizačním systému (tj. Virtualbox, KVM atd.) Příprava bitové kopie systému Linux, budete muset znovu vytvořit initrd zajistit, aby alespoň `hv_vmbus` a `hv_storvsc` jádra moduly jsou k dispozici na počáteční disku paměti RAM.  Jedná se o známý problém alespoň na systémy založené na nadřazený distribuce Red Hat.

Tento mechanismus pro nové sestavení initrd nebo initramfs image může lišit v závislosti na rozdělení. Projděte si dokumentaci k vaší distribuční nebo podporu pro správné postup.  Tady je jedním z příkladů jak znovu sestavte initrd pomocí `mkinitrd` nástroje:

Nejprve zálohujte existující bitová kopie initrd:

    # cd /boot
    # sudo cp initrd-`uname -r`.img  initrd-`uname -r`.img.bak

V dalším kroku znovu sestavit initrd s `hv_vmbus` a `hv_storvsc` moduly jádra:

    # sudo mkinitrd --preload=hv_storvsc --preload=hv_vmbus -v -f initrd-`uname -r`.img `uname -r`


### <a name="resizing-vhds"></a>Změna velikosti virtuálních pevných disků
Image virtuálního pevného disku na Azure musí mít virtuální velikost zarovnán 1MB.  Obvykle virtuální pevné disky vytvořené pomocí technologie Hyper-V už zarovnání správně.  Pokud není správně zarovnaná virtuálního pevného disku, pak při pokusu o vytvoření, může se zobrazit chybová zpráva podobná následující *image* z vaší virtuálního pevného disku:

    "The VHD http://<mystorageaccount>.blob.core.windows.net/vhds/MyLinuxVM.vhd has an unsupported virtual size of 21475270656 bytes. The size must be a whole number (in MBs).”

Chcete-li opravit to můžete změnit velikost virtuálního počítače pomocí konzoly Správce technologie Hyper-V nebo [změny velikosti virtuálního pevného disku](http://technet.microsoft.com/library/hh848535.aspx) rutiny prostředí Powershell.  Pokud používáte v prostředí systému Windows, se doporučuje použít qemu img převést (v případě potřeby) a změnit velikost virtuálního pevného disku.

> [!NOTE]
> Je známého problému v qemu img verze > = 2.2.1, jejímž výsledkem nesprávně naformátovaný VHD. Ve verzi 2.6 QEMU byl opraven problém. Doporučuje se používat qemu-img 2.2.0 nebo nižší, nebo aktualizace na 2.6 nebo novější. Reference: https://bugs.launchpad.net/qemu/+bug/1490611.
> 
> 

1. Změna velikosti virtuálního pevného disku přímo pomocí nástrojů, jako `qemu-img` nebo `vbox-manage` může mít za následek nelze spustit VHD.  Proto se doporučuje první převést virtuální pevný disk do bitové kopie NEZPRACOVANÁ disku.  Pokud image virtuálního počítače byla již vytvořena jako image NEZPRACOVANÁ disku (výchozí nastavení pro některé hypervisory například KVM) může tento krok přeskočit:
   
       # qemu-img convert -f vpc -O raw MyLinuxVM.vhd MyLinuxVM.raw

2. Vypočítejte požadovaná velikost bitové kopie disku a ujistěte se, že virtuální velikost je zarovnán 1MB.  Následující skript prostředí bash s tím pomůže.  Tento skript využívá "`qemu-img info`" určit virtuální velikost bitové kopie disku a pak vypočítá velikost další 1 MB:
   
       rawdisk="MyLinuxVM.raw"
       vhddisk="MyLinuxVM.vhd"
   
       MB=$((1024*1024))
       size=$(qemu-img info -f raw --output json "$rawdisk" | \
              gawk 'match($0, /"virtual-size": ([0-9]+),/, val) {print val[1]}')
   
       rounded_size=$((($size/$MB + 1)*$MB))
       echo "Rounded Size = $rounded_size"

3. Změna velikosti nezpracovaná disku pomocí $rounded_size nastavená na skript výše:
   
       # qemu-img resize MyLinuxVM.raw $rounded_size

4. Nyní NEZPRACOVANÁ disku převeďte zpátky na virtuální pevný disk pevné velikosti:
   
       # qemu-img convert -f raw -o subformat=fixed -O vpc MyLinuxVM.raw MyLinuxVM.vhd

   Nebo s verzí qemu **2.6 +** zahrnují `force_size` možnost:

       # qemu-img convert -f raw -o subformat=fixed,force_size -O vpc MyLinuxVM.raw MyLinuxVM.vhd

## <a name="linux-kernel-requirements"></a>Požadavky na Linux jádra
Přímo na nadřazený jádra systému Linux jsou podílí ovladače Linux integrační služby (LIS) pro technologii Hyper-V a Azure. Velkém množství distribucí, které obsahují nejnovější verzi jádra Linux (tj. 3.x) bude již je k dispozici tyto ovladače, nebo v opačném případě zadejte přeneseny zpět verze těchto ovladačů s jejich jádra.  Tyto ovladače neustále aktualizované v nadřazeného jádra s nové opravy a funkce, takže pokud je to možné se doporučuje spustit [schválené distribuční](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) který bude obsahovat tyto opravy a aktualizace.

Pokud používáte hodnotu typu variant Red Hat Enterprise Linux verze **6.0 6.3**, bude nutné nainstalovat nejnovější ovladače LIS pro Hyper-V. Ovladače lze nalézt [v tomto umístění](http://go.microsoft.com/fwlink/p/?LinkID=254263&clcid=0x409). Od systému RHEL **6.4 +** (a odvozené konfigurace) LIS jsou již součástí jádra a proto žádné další instalační balíčky jsou potřebné ke spuštění těchto systémech v Azure.

Pokud se vyžaduje vlastní jádra, se doporučuje použít novější verzi jádra (tj. **3.8 +**). U těchto distribuce nebo dodavatelů, kteří spravují své vlastní jádra některé úsilí bude potřeba pravidelně backport LIS ovladače z nadřazeného jádra pro vaše vlastní jádra.  I když už používáte relativně nejnovější verzi jádra, důrazně doporučujeme ke sledování všech nadřazeného opravy LIS ovladače a backport ty podle potřeby. Je k dispozici v umístění zdrojových souborů ovladačů LIS [údržby programu](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/tree/MAINTAINERS) ve stromu Linux jádra zdrojového souboru:

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

V velmi minimální bylo zjištěno chybí následující opravy, způsobit problémy v Azure a tak tyto musí být obsaženy v jádra. Tento seznam je rozhodně není vyčerpávající nebo úplný pro všechny distribuce:

* [ata_piix: ve výchozím nastavení odložené disků, aby se ovladače technologie Hyper-V](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/ata/ata_piix.c?id=cd006086fa5d91414d8ff9ff2b78fbb593878e3c)
* [miniport storvsc: účet pro pakety na cestě v cestě k RESETOVÁNÍ](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/drivers/scsi/storvsc_drv.c?id=5c1b10ab7f93d24f29b5630286e323d1c5802d5c)
* [miniport storvsc: Vyhněte se použití WRITE_SAME](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=3e8f4f4065901c8dfc51407e1984495e1748c090)
* [miniport storvsc: zakázat zápis stejné diskového pole RAID a ovladače adaptéru virtuální hostitel](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=54b2b50c20a61b51199bedb6e5d2f8ec2568fb43)
* [miniport storvsc: oprava zrušení ukazatele NULL.](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=b12bb60d6c350b348a4e1460cd68f97ccae9822e)
* [miniport storvsc: selhání prstenec vyrovnávací paměti může vést k zablokování vstupně-výstupních operací](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/storvsc_drv.c?id=e86fb5e8ab95f10ec5f2e9430119d5d35020c951)
* [scsi_sysfs: ochranu proti dvojité provádění __scsi_remove_device](https://git.kernel.org/cgit/linux/kernel/git/next/linux-next.git/commit/drivers/scsi/scsi_sysfs.c?id=be821fd8e62765de43cc4f0e2db363d0e30a7e9b)

## <a name="the-azure-linux-agent"></a>Azure Linux Agent
[Azure Linux Agent](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (příkaz waagent) je potřeba správně zřídit virtuální počítač s Linuxem v Azure. Můžete získat nejnovější verzi, soubor problémy nebo odeslání žádosti o přijetí změn na [úložiště GitHub agenta Linux](https://github.com/Azure/WALinuxAgent).

* V rámci Apache 2.0 licence vydání agenta systému Linux. Velkém množství distribucí již zadejte ot. / min nebo bázi deb balíčky pro agenta, a proto v některých případech to lze nainstalovat a aktualizovat s malým množstvím úsilí.
* Azure Linux Agent vyžaduje Python v2.6 +.
* Agent vyžaduje taky modul python pyasn1. Většina distribuce to zadejte jako samostatný balíček, který může být instalován.
* V některých případech nemusí být kompatibilní s NetworkManager Azure Linux Agent. Mnoho balíčků RPM/bázi Deb poskytované distribuce nakonfigurujte NetworkManager konflikt balíčku příkaz waagent a proto odinstaluje NetworkManager při instalaci balíčku agenta systému Linux.
* Azure Linux Agent musí být vyšší než minimální podporovaná verze, najdete v tomto článku [podrobnosti](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

## <a name="general-linux-system-requirements"></a>Požadavky na systém Linux obecné

* Změňte na řádku spouštěcí jádra GRUB nebo GRUB2, zahrnout následující parametry. To také zajistí, všechny zprávy konzoly odešlou do první sériového portu, který může být užitečné Azure podporu pro ladění problémů:
  
        console=ttyS0,115200n8 earlyprintk=ttyS0,115200 rootdelay=300
  
    Kromě výše uvedeného, doporučuje se *odebrat* Pokud existují následující parametry:
  
        rhgb quiet crashkernel=auto
  
    Grafické a quiet spouštěcí nejsou užitečné při cloudovém prostředí, kde chceme, aby všechny protokoly pro odeslání do sériového portu. `crashkernel` Možnost může být vlevo nakonfigurované v případě potřeby, ale Všimněte si, že tento parametr se sníží množství dostupné paměti ve virtuálním počítači 128 MB nebo informace, které mohou způsobovat menší velikostí virtuálního počítače.

* Instalace agenta Azure Linux
  
    Azure Linux Agent je požadované pro zřizování Linux image na platformě Azure.  Velkém množství distribucí zadejte agenta jako balíček RPM nebo bázi Deb (balíčku se obvykle označuje jako 'WALinuxAgent' nebo 'walinuxagent').  Agent lze také nainstalovat ručně pomocí následujících kroků v [Linux Agent průvodce](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

* Ujistěte se, že SSH server je nainstalován a nakonfigurován na spuštění při spuštění.  Obvykle je to výchozí hodnota.

* Nevytvářejte odkládacího prostoru na disku operačního systému
  
    Azure Linux Agent mohou automaticky konfigurovat odkládacího souboru pomocí disku místní prostředek, který je připojen k virtuálnímu počítači po zřízení v Azure. Všimněte si, že je disk místní prostředek *dočasné* na disku a může vyprázdněny, když je virtuální počítač zrušit. Po instalaci Azure Linux Agent (viz předchozí krok), upravte následující parametry v /etc/waagent.conf odpovídajícím způsobem:
  
        ResourceDisk.Format=y
        ResourceDisk.Filesystem=ext4
        ResourceDisk.MountPoint=/mnt/resource
        ResourceDisk.EnableSwap=y
        ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

* V posledním kroku spusťte následující příkaz pro zrušení zřízení virtuálního počítače:
  
        # sudo waagent -force -deprovision
        # export HISTSIZE=0
        # logout
  
  > [!NOTE]
  > Na Virtualbox mohou se zobrazit chybová zpráva po spuštění se příkaz waagent-force - deprovision.: `[Errno 5] Input/output error`. Tato chybová zpráva není příliš důležitý a můžete ignorovat.
  > 
  > 

* Pak musíte vypnout virtuální počítač a nahrání virtuálního pevného disku do Azure.

