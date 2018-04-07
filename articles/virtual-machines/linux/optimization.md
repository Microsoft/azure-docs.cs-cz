---
title: Optimalizace virtuálním počítačům s Linuxem v Azure | Microsoft Docs
description: Další tipy optimalizace a ujistěte se, že jste nastavili virtuálním počítačům s Linuxem pro optimální výkon na Azure
keywords: virtuální počítač Linux, virtuální počítač linux, ubuntu virtuálního počítače
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: 8baa30c8-d40e-41ac-93d0-74e96fe18d4c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/06/2016
ms.author: rclaus
ms.openlocfilehash: 5b0d7eb0767bb18cd74c545dc386aa16be487dae
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="optimize-your-linux-vm-on-azure"></a>Optimalizace virtuálního počítače s Linuxem v Azure
Vytvoření virtuálního počítače (VM) Linux je snadné provést z příkazového řádku nebo z portálu. V tomto kurzu se dozvíte, jak zajistit jste ho nastavili za účelem optimalizace jeho výkon na platformě Microsoft Azure. Toto téma používá virtuálního počítače s Ubuntu Server, ale můžete vytvořit také pomocí virtuálních počítačů Linux [vlastní Image jako šablona](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

## <a name="prerequisites"></a>Požadavky
Toto téma předpokládá, že již máte funkční předplatné Azure ([bezplatné zkušební verze registrace](https://azure.microsoft.com/pricing/free-trial/)) a již zřídit virtuální počítač do vašeho předplatného Azure. Ujistěte se, že máte nejnovější [Azure CLI 2.0](/cli/azure/install-az-cli2) nainstalován a přihlášení k předplatnému Azure s [az přihlášení](/cli/azure/reference-index#az_login) před [vytvoření virtuálního počítače](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-os-disk"></a>Azure OS Disk
Po vytvoření virtuálního počítače s Linuxem v Azure, má dva disky, které jsou s ním spojená. **/ dev/sda** je disk operačního systému, **/dev/sdb** je dočasným diskovým.  Nepoužívejte hlavní disk operačního systému (**/dev/sda**) pro všechno, co s výjimkou operačního systému, protože je optimalizovaná pro rychlé spuštění virtuálního počítače a neposkytuje dobrý výkon pro zatížení. Chcete připojit k virtuálnímu počítači získat trvalé jeden nebo více disků a optimalizované úložiště pro vaše data. 

## <a name="adding-disks-for-size-and-performance-targets"></a>Přidání disků pro velikost a cílech výkonnosti
Podle velikosti virtuálního počítače, můžete připojit až 16 další disky na A-Series, 32 disky na D-Series a 64 disků na řadu G počítače – velikost každý až 1 TB. Podle potřeby na místa a požadavky na IOps přidáte další disky. Každý disk má výkonu cílem 500 IOps pro standardní úložiště a až 5000 IOps na disku pro Storage úrovně Premium.  Další informace o discích Premium Storage najdete v tématu [úložiště Premium: vysoce výkonné úložiště pro virtuální počítače Azure](../windows/premium-storage.md)

K dosažení nejvyšší IOps na discích úložiště Premium, kde jejich mezipaměti nastavení buď **jen pro čtení** nebo **žádné**, je nutné zakázat **překážek** při připojování systém souborů v systému Linux. Protože jsou zápisy na disky úložiště Premium zálohovaný trvanlivý pro tato nastavení mezipaměti nepotřebujete překážek.

* Pokud používáte **reiserFS**, zakažte překážek pomocí možnosti připojení `barrier=none` (pro povolení překážek, použijte `barrier=flush`)
* Pokud používáte **ext3/ext4**, zakažte překážek pomocí možnosti připojení `barrier=0` (pro povolení překážek, použijte `barrier=1`)
* Pokud používáte **XFS**, zakažte překážek pomocí možnosti připojení `nobarrier` (pro povolení překážek, použijte možnost `barrier`)

## <a name="unmanaged-storage-account-considerations"></a>Důležité informace o účtu nespravovaného úložiště
Výchozí akcí při vytváření virtuálního počítače pomocí Azure CLI 2.0, je použít Azure spravované disky.  Tyto disky jsou zpracovávány platformy Azure a nevyžadují, aby všechny přípravné nebo umístění pro uložení.  Nespravované disky se vyžaduje účet úložiště a mají některé další výkonem.  Další informace o spravovaných discích najdete v tématu [Přehled služby Azure Managed Disks](../windows/managed-disks-overview.md).  V následující části jsou nastíněné aspekty výkonu, jenom když použijete nespravované disky.  Znovu, výchozí a doporučený úložiště řešením je použití spravovaných disky.

Pokud vytvoříte virtuální počítač s nespravované disky, ujistěte se, připojit disky z účty úložiště, které se nacházejí ve stejné oblasti jako virtuální počítač k zajištění těsné blízkosti a minimalizaci latence sítě.  Každý účet standardního úložiště má maximálně 20 tisíc IOps a kapacitou velikost 500 TB.  Tento limit funguje na přibližně 40 vytíženou disky, včetně disku operačního systému a všech datových disků, které vytvoříte. Pro účty služby Premium Storage neexistuje žádné omezení maximální IOps, ale existuje omezení velikosti 32 TB. 

Při práci s vysoké zatížení IOps a vybrali standardní úložiště pro disky, možná budete muset rozdělit disky do více účtů úložiště a ujistěte se, že nebyly dosáhl limit IOps 20 000 pro účty úložiště Standard Storage. Virtuální počítač může obsahovat kombinaci disků z mezi typy účtů úložiště zajistit optimální konfiguraci a jiným účtům úložiště.
 

## <a name="your-vm-temporary-drive"></a>Virtuální počítač dočasné jednotce
Ve výchozím nastavení při vytváření virtuálního počítače, Azure poskytuje disk s operačním systémem (**/dev/sda**) a dočasným diskovým (**/dev/sdb**).  Všechny další disky přidat zobrazit si jako **/dev/sdc**, **/dev/sdd**, **/dev/sde** a tak dále. Všechna data na dočasné disku (**/dev/sdb**) není odolné a může dojít ke ztrátě, pokud konkrétní události, například změna velikosti virtuálních počítačů, opakované nasazení, nebo údržby vynutí restartování virtuálního počítače.  Velikost a typ dočasné disku souvisí s velikost virtuálního počítače, které jste zvolili v době nasazení. Všechny premium velikost dočasné jednotce jsou zajišťované místní SSD pro další výkonu až 48 kb virtuálních počítačů (řady DS, G a DS_V2) IOps. 

## <a name="linux-swap-file"></a>Linux odkládací soubor
Pokud virtuální počítač Azure z image Ubuntu nebo CoreOS CustomData můžete použít k odeslání do cloudu init cloudu config. Pokud jste [nahrát vlastní image Linux](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) init cloudu, který používá, je také nakonfigurovat oddíly odkládacího souboru v cloudu init.

Ubuntu cloudu Image musíte použít cloudové init ke konfiguraci oddílu odkládacího souboru. Další informace najdete v tématu [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

Pro Image bez podpory cloudu init být Image virtuálních počítačů nasadit v Azure Marketplace Agent virtuálních počítačů Linux integrované s operačním systémem. Tento agent umožňuje virtuálnímu počítači komunikovat s různými službami Azure. Za předpokladu, že jste nasadili standardní bitové kopie z Azure Marketplace, museli byste ji správně nakonfigurovat Linux odkládací soubor následujícím způsobem:

Vyhledejte a upravit dvě položky v **/etc/waagent.conf** souboru. Tím i určovat existenci vyhrazené odkládací soubor a velikost odkládacího souboru. Hledáte upravit parametry jsou `ResourceDisk.EnableSwap=N` a `ResourceDisk.SwapSizeMB=0` 

Změňte parametry následující nastavení:

* ResourceDisk.EnableSwap=Y
* ResourceDisk.SwapSizeMB={size v MB pro podle svých potřeb} 

Po provedení změn, budete muset restartovat příkaz waagent nebo restartujte virtuálním počítačům s Linuxem tak, aby odrážela tyto změny.  Víte, byly provedeny změny a odkládací soubor byl vytvořen při použití `free` příkazu zobrazte volného místa. V následujícím příkladu má odkládací soubor 512MB vytvořeny v důsledku změny **waagent.conf** souboru:

```bash
azuseruser@myVM:~$ free
            total       used       free     shared    buffers     cached
Mem:       3525156     804168    2720988        408       8428     633192
-/+ buffers/cache:     162548    3362608
Swap:       524284          0     524284
```

## <a name="io-scheduling-algorithm-for-premium-storage"></a>Plánování algoritmus vstupně-výstupních operací pro Storage úrovně Premium
Pomocí 2.6.18 Linux jádra, výchozí vstupně-výstupních operací plánování algoritmus byl změněn z konečný termín na CFQ (algoritmus úplně správného řízení front). Pro vzory vstupů/výstupů náhodný přístup je nepatrné rozdíl ve výkonu rozdíly mezi CFQ a konečným termínem.  Pro založená na SSD disky, kde je převážně sekvenčních vstupně-výstupních operací vzoru disku, přepnutím zpět algoritmus nedojde k žádné akci nebo termín lepšího výkonu můžete dosáhnout vstupně-výstupní operace.

### <a name="view-the-current-io-scheduler"></a>Zobrazení aktuálního plánovače vstupně-výstupních operací
Použijte následující příkaz:  

```bash
cat /sys/block/sda/queue/scheduler
```

Zobrazí se následující výstup, který označuje aktuálního plánovače.  

```bash
noop [deadline] cfq
```

### <a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Změňte aktuální zařízení (/ dev/sda) plánování algoritmus vstupně-výstupních operací
Použijte následující příkazy:  

```bash
azureuser@myVM:~$ sudo su -
root@myVM:~# echo "noop" >/sys/block/sda/queue/scheduler
root@myVM:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
root@myVM:~# update-grub
```

> [!NOTE]
> Toto nastavení pro použití **/dev/sda** samostatně není užitečné. Nastavte u všech datových disků, kde sekvenčních vstupně-výstupních operací dominuje vzoru vstupně-výstupních operací.  

Byste měli vidět následující výstup, což indikuje, že **grub.cfg** byla znovu sestavena úspěšně a že se aktualizovalo výchozí Plánovač a nedojde k žádné akci.  

```bash
Generating grub configuration file ...
Found linux image: /boot/vmlinuz-3.13.0-34-generic
Found initrd image: /boot/initrd.img-3.13.0-34-generic
Found linux image: /boot/vmlinuz-3.13.0-32-generic
Found initrd image: /boot/initrd.img-3.13.0-32-generic
Found memtest86+ image: /memtest86+.elf
Found memtest86+ image: /memtest86+.bin
done
```

Pro řadu distribuční Redhat stačí pouze následující příkaz:   

```bash
echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local
```

## <a name="using-software-raid-to-achieve-higher-iops"></a>Pomocí softwaru diskového pole RAID k dosažení vyšší I / Ops
Pokud vaše úlohy vyžadují více procesorů, než může poskytnout jeden disk, budete muset použít konfiguraci RAID softwaru několik disků. Protože Azure již provádí odolnosti disku ve vrstvě místní infrastruktury, můžete dosáhnout nejvyšší úroveň výkonu z proložení konfigurace RAID-0.  Zřídit a vytvořit disků v prostředí Azure a připojte je k virtuálním počítačům s Linuxem před rozdělení do oddílů, formátování a připojení jednotky.  Další informace o konfiguraci instalace softwaru diskového pole RAID na virtuálním počítačům s Linuxem v azure najdete v **[konfigurace RAID softwaru v systému Linux](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)** dokumentu.

## <a name="next-steps"></a>Další kroky
Pamatujte si, jak se všechny diskuse optimalizace, potřebujete provést testy před a po každé změně k měření dopad, který má změnu.  Optimalizace je krok za krokem proces, který má odlišné výsledky v různých počítačích ve vašem prostředí.  Co funguje pro jednu konfiguraci nemusí fungovat pro ostatní.

Některé užitečné odkazy na další zdroje informací: 

* [Storage úrovně Premium: Vysoce výkonné úložiště pro úlohy virtuálních počítačů Azure](premium-storage.md)
* [Uživatelská příručka k Azure Linux Agent](agent-user-guide.md)
* [Optimalizace výkonu databáze MySQL na virtuálních počítačích Azure Linux](classic/optimize-mysql.md)
* [Konfigurace softwaru diskového pole RAID v systému Linux](configure-raid.md)
