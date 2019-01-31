---
title: Optimalizace virtuálního počítače s Linuxem v Azure | Dokumentace Microsoftu
description: Další tipy optimalizace, abyste měli jistotu, že nastavíte virtuální počítač s Linuxem pro zajištění optimálního výkonu v Azure
keywords: linuxový virtuální počítač, virtuální počítač linux, virtuální počítač s ubuntu
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
ms.subservice: disks
ms.openlocfilehash: 61f4237756d7a808bcc8a5983508d72fa49f4cbd
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55459163"
---
# <a name="optimize-your-linux-vm-on-azure"></a>Optimalizace virtuálního počítače s Linuxem v Azure
Vytvoření virtuálního počítače s Linuxem (VM) je snadné provést z příkazového řádku nebo z portálu. V tomto kurzu se dozvíte, jak zajistit jste ho nastavili optimalizace jeho výkonu na platformě Microsoft Azure. Toto téma používá virtuální počítač s Ubuntu Server, ale můžete také vytvořit virtuální počítač Linux pomocí [svých vlastních imagí jako šablony](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

## <a name="prerequisites"></a>Požadavky
Toto téma předpokládá, že již máte funkční předplatného Azure ([registraci bezplatné zkušební verze](https://azure.microsoft.com/pricing/free-trial/)) a už zřízení virtuálního počítače do vašeho předplatného Azure. Ujistěte se, že máte nejnovější [rozhraní příkazového řádku Azure](/cli/azure/install-az-cli2) nainstalovaný a přihlášení k předplatnému Azure pomocí [az login](/cli/azure/reference-index#az_login) před [vytvoření virtuálního počítače](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-os-disk"></a>Azure Disk s operačním systémem
Po vytvoření virtuálního počítače s Linuxem v Azure má dva disky, které s ním spojená. **/ dev/sda** je disk s operačním systémem **/dev/sdb** je dočasný disk.  Nepoužívejte hlavní disk s operačním systémem (**/dev/sda**) pro všechno, co s výjimkou operačního systému, protože je optimalizovaný pro rychlé spuštění virtuálního počítače a neposkytuje dostatečný výkon pro vaše úlohy. Chcete se připojit jeden nebo víc disků k virtuálnímu počítači k získání trvalého a optimalizované úložiště pro vaše data. 

## <a name="adding-disks-for-size-and-performance-targets"></a>Přidání disků pro velikost a výkonnostní cíle
Na základě velikosti virtuálního počítače můžete připojit až 16 další disky na A-Series, 32 disky v D-Series a strojové 64 disků na G-Series – každá až do velikosti 1 TB. Podle potřeby za prostor a požadavky na vstupně-výstupních operací, přidejte další disky. Každý disk má cíl výkonnosti 500 iops pro úložiště úrovně Standard a maximálně 5000 IOps na disku pro Premium Storage.  Další informace o discích Premium Storage najdete v tématu [Premium Storage: Vysoce výkonné úložiště pro virtuální počítače Azure](../windows/premium-storage.md)

K dosažení nejvyšší počet IOps na disky Premium Storage, kde jejich nastavení mezipaměti nebyly nastaveny na hodnotu **jen pro čtení** nebo **žádný**, je nutné zakázat **bariéry** při připojení systém souborů v systému Linux. Protože jsou zápisy na disky Premium Storage zajišťuje trvalé pro tato nastavení mezipaměti nepotřebujete překážek.

* Pokud používáte **reiserFS**, překážek zakázat použití možnosti připojení `barrier=none` (pro povolení bariéry, použijte `barrier=flush`)
* Pokud používáte **ext3/ext4**, překážek zakázat použití možnosti připojení `barrier=0` (pro povolení bariéry, použijte `barrier=1`)
* Pokud používáte **XFS**, překážek zakázat použití možnosti připojení `nobarrier` (pro povolení bariéry, použijte možnost `barrier`)

## <a name="unmanaged-storage-account-considerations"></a>Důležité informace o účtech nespravovaného úložiště
Výchozí akce při vytváření virtuálního počítače pomocí Azure CLI je používat službu Azure Managed Disks.  Tyto disky jsou zpracovány platformou Azure, nevyžadují žádné přípravy ani umístění, kam ji uložit.  Nespravované disky se vyžaduje účet úložiště a mají některé důležité informace o dalších výkonu.  Další informace o spravovaných discích najdete v tématu [Přehled služby Azure Managed Disks](../windows/managed-disks-overview.md).  Následující část popisuje důležité informace o výkonu pouze v případě, že používáte nespravované disky.  Znovu, výchozí a doporučený úložiště řešením je použití spravovaných disků.

Pokud vytvoříte virtuální počítač s nespravovanými disky, ujistěte se, že disky připojit z účtů úložiště, které se nacházejí ve stejné oblasti jako virtuální počítač k zajištění těsné blízkosti a minimalizaci latence sítě.  Každý účet úložiště úrovně Standard může mít nejvýše z 20 tisíc vstupně-výstupních operací a kapacity velikosti 500 TB.  Tento limit funguje na přibližně 40 vytížený disky, včetně disk s operačním systémem a všechny datové disky, které vytvoříte. Pro účty služby Premium Storage neexistuje žádné omezení maximální vstupně-výstupních operací, ale neexistuje omezení velikosti 32 TB. 

Při práci s vysokým vstupně-výstupních operací úloh a rozhodli úložiště úrovně Standard pro disky, můžete potřebovat rozdělit disky ve více účtech úložiště, abyste měli jistotu, že nebyly dosáhli limitu 20 000 IOps pro účty úložiště úrovně Standard. Váš virtuální počítač může obsahovat kombinaci disků z různých účtů úložiště a typech účtů úložiště, abyste dosáhli optimální konfigurace.
 

## <a name="your-vm-temporary-drive"></a>Virtuální počítač dočasné jednotky
Ve výchozím nastavení při vytváření virtuálního počítače, Azure vám poskytne disk s operačním systémem (**/dev/sda**) a dočasný disk (**/dev/sdb**).  Všechny další disky, zobrazit si můžete přidat jako **/dev/sdc**, **/dev/sdd**, **/dev/sde** a tak dále. Všechna data na dočasném disku (**/dev/sdb**) není trvalý a může dojít ke ztrátě určitých událostí, jako je změna velikosti virtuálního počítače, opětovné nasazení, nebo údržby vynutí restartování vašeho virtuálního počítače.  Velikost a typ dočasného disku se týká velikost virtuálního počítače, kterou jste zvolili v době nasazení. Všechny premium velikosti virtuálních počítačů (řady DS, G a DS_V2) se zálohují dočasné jednotky na místní disk SSD pro další výkon až 48k vstupně-výstupních operací. 

## <a name="linux-swap-file"></a>Linux stránkovacího souboru
Pokud je váš virtuální počítač Azure z image Ubuntu nebo CoreOS, můžete použít CustomData odeslat konfiguraci cloudu cloud-init. Pokud jste [nahrát vlastní image Linuxu](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) , která používá cloud-init, můžete také nakonfigurovat odkládací oddíl použití cloud-init.

Ubuntu cloudových Imagí je potřeba použít cloud-init konfigurace odkládací oddíl. Další informace najdete v tématu [AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions).

Imagí virtuálních počítačů nasazených z Azure Marketplace pro Image bez podpory cloud-init, mají integrované s operačním systémem Linux agenta virtuálního počítače. Tento agent umožňuje virtuálnímu počítači komunikovat s různými službami Azure. Za předpokladu, že jste nasadili standardní image z Azure Marketplace, musíte ji správně nakonfigurovat odkládací soubor Linuxu následujícím způsobem:

Vyhledejte a upravit dvou položek **/etc/waagent.conf** souboru. Řídit jejich existenci vyhrazené odkládacího souboru a velikost odkládacího souboru. Chcete-li upravit parametry jsou `ResourceDisk.EnableSwap=N` a `ResourceDisk.SwapSizeMB=0` 

Změňte parametry následující nastavení:

* ResourceDisk.EnableSwap=Y
* ResourceDisk.SwapSizeMB={size v MB podle svých potřeb} 

Po provedení změny, budete muset restartovat waagent nebo restartovat virtuální počítač s Linuxem tak, aby odrážela tyto změny.  Víte, byly provedeny změny a odkládacího souboru se vytvořil při použití `free` příkazu zobrazíte volného místa. V následujícím příkladu má vytvořené v důsledku změny 512MB odkládacího souboru **waagent.conf** souboru:

```bash
azuseruser@myVM:~$ free
            total       used       free     shared    buffers     cached
Mem:       3525156     804168    2720988        408       8428     633192
-/+ buffers/cache:     162548    3362608
Swap:       524284          0     524284
```

## <a name="io-scheduling-algorithm-for-premium-storage"></a>Plánování algoritmů vstupně-výstupních operací pro Premium Storage
S 2.6.18 jádro Linuxu, výchozí plánování algoritmus vstupně-výstupní operace se změnil z konečného termínu k CFQ (algoritmus zcela veletrh zařazování do fronty). Pro vzory vstupů/výstupů náhodný přístup je zanedbatelný rozdíl ve výkonu rozdíly mezi CFQ a konečným termínem.  Založené na discích SSD disků, kde je převážně sekvenční vzor vstupně-výstupních operací disku můžete přepnout zpět na algoritmus NOOP nebo termín dosahovat lepšího výkonu vstupně-výstupních operací.

### <a name="view-the-current-io-scheduler"></a>Zobrazit aktuální Plánovač vstupně-výstupních operací
Použijte následující příkaz:  

```bash
cat /sys/block/sda/queue/scheduler
```

Zobrazí se následující výstup, který označuje aktuálního plánovače.  

```bash
noop [deadline] cfq
```

### <a name="change-the-current-device-devsda-of-io-scheduling-algorithm"></a>Změňte aktuální zařízení (/ dev/sda) vstupně-výstupních operací plánování algoritmus
Pomocí následujících příkazů:  

```bash
azureuser@myVM:~$ sudo su -
root@myVM:~# echo "noop" >/sys/block/sda/queue/scheduler
root@myVM:~# sed -i 's/GRUB_CMDLINE_LINUX=""/GRUB_CMDLINE_LINUX_DEFAULT="quiet splash elevator=noop"/g' /etc/default/grub
root@myVM:~# update-grub
```

> [!NOTE]
> Použití tohoto nastavení pro **/dev/sda** samostatně není užitečné. Nastavení pro všechny datové disky, kde sekvenčních vstupně-výstupních operací dominuje vzor vstupně-výstupních operací.  

Byste měli vidět následující výstup, což indikuje, že **grub.cfg** byla znovu sestavena úspěšně a že výchozí plánovač NOOP aktualizovaný.  

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

Pro distribuce řady Red Hat potřebujete jenom následující příkaz:   

```bash
echo 'echo noop >/sys/block/sda/queue/scheduler' >> /etc/rc.local
```

## <a name="using-software-raid-to-achieve-higher-iops"></a>Pomocí softwaru diskového pole RAID k dosažení vyšší můžu / operace
Pokud vaše úlohy vyžadují další vstupně-výstupních operací, než můžete zadat jeden disk, budete muset použít konfiguraci RAID softwaru více disků. Protože Azure už provádí odolnost proti chybám disku ve vrstvě místní infrastruktury, můžete dosáhnout nejvyšší úroveň výkonu z prokládáním konfigurace RAID-0.  Zřízení a vytvoření disků v prostředí Azure a připojení k virtuálním počítačům s Linuxem před rozdělení do oddílů, formátování a připojení jednotky.  Další podrobnosti o konfiguraci nastavení softwaru diskového pole RAID na virtuální počítač s Linuxem v azure najdete v **[konfigurace softwaru diskového pole RAID v Linuxu](configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)** dokumentu.

## <a name="next-steps"></a>Další kroky
Mějte na paměti, jak se všechny diskuse optimalizace, je potřeba provést testy před a po každé změně měřit dopad, který má tato změna.  Optimalizace je proces krok za krokem, který má odlišné výsledky v různých počítačích ve vašem prostředí.  Co funguje u jedné konfigurace, nemusí fungovat pro ostatní uživatele.

Několik užitečných odkazů pro další zdroje informací: 

* [Premium Storage: Vysoce výkonné úložiště pro úlohy virtuálních počítačů Azure](premium-storage.md)
* [Uživatelská příručka k Azure Linux Agent](../extensions/agent-linux.md)
* [Optimalizace výkonu MySQL na virtuálních počítačích Azure s Linuxem](classic/optimize-mysql.md)
* [Konfigurace softwarového pole RAID v Linuxu](configure-raid.md)
