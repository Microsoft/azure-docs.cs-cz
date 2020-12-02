---
title: Lsv2-Series – Azure Virtual Machines
description: Specifikace pro virtuální počítače řady Lsv2-Series.
author: sasha-melamed
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: ba971919037230cb6c09e627fde448647063b5b9
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96498417"
---
# <a name="lsv2-series"></a>Řada Lsv2

Funkce Lsv2-Series s vysokou propustností, nízkou latencí a přímo namapovaným místním úložištěm NVMe běžícím na [procesorech AMD EPYC<sup>TM</sup> 7551](https://www.amd.com/en/products/epyc-7000-series) se všemi základními zvýšeními úrovně 2.55 GHz a maximálním zvýšením velikosti 3,0 GHz. Virtuální počítače řady Lsv2-Series přicházejí v případě souběžné konfigurace s více vlákny do velikosti od 8 do 80 vCPU.  K dispozici je 8 GiB paměti na vCPU a jedno 1.92 TB NVMe SSD M. 2 zařízení na 8 vCPU a až 19.2 TB (10x 1.92 TB), které je dostupné na L80s v2.

> [!NOTE]
> Virtuální počítače řady Lsv2-Series jsou optimalizované tak, aby používaly místní disk na uzlu připojeném přímo k virtuálnímu počítači namísto použití trvalých datových disků. To umožňuje pro vaše úlohy větší počet vstupně-výstupních operací a propustnosti. Lsv2 a ls-series nepodporují vytváření místní mezipaměti, aby bylo možné navýšit IOPs dosažitelné pomocí trvalých datových disků.
>
> Vysoká propustnost a IOPs místního disku zpřístupňuje virtuální počítače řady Lsv2-Series ideální pro NoSQL úložiště, jako je Apache Cassandra a MongoDB, které replikují data napříč několika virtuálními počítači, aby se zajistila stálost v případě selhání jednoho virtuálního počítače.
>
> Další informace najdete v tématu Optimalizace výkonu na virtuálních počítačích Lsv2-Series pro [Windows](../virtual-machines/windows/storage-performance.md) nebo [Linux](../virtual-machines/linux/storage-performance.md).  

[ACU](acu.md): 150-175<br>
[Premium Storage](premium-storage-performance.md): podporováno<br>
[Ukládání Premium Storage do mezipaměti](premium-storage-performance.md): nepodporováno<br>
[Migrace za provozu](maintenance-and-updates.md): nepodporováno<br>
[Aktualizace pro zachování paměti](maintenance-and-updates.md): nepodporováno<br>
[Podpora generování virtuálních počítačů](generation-2.md): generace 1 a 2<br>
Shluking: podporováno<br>
<br>

| Velikost | Virtuální procesory | Paměť (GiB) | Dočasný disk<sup>1</sup> (GIB) | Disky NVMe<sup>2</sup> | Propustnost disku NVMe<sup>3</sup> (čtení za sekundu/s) | Propustnost datového disku neuloženého v mezipaměti (IOPs/MB/s)<sup>4</sup> | Maximální propustnost datového disku neuloženého shluku (IOPs/MB/s)<sup>5</sup>| Maximální počet datových disků | Maximální počet síťových karet | Očekávaná šířka pásma sítě (MB/s) |
|---|---|---|---|---|---|---|---|---|---|---|
| Standard_L8s_v2   |  8 |  64 |  80 |  1x 1.92 TB  | 400000/2000  | 8000/160   | 8000/1280 | 16 | 2 | 3200   |
| Standard_L16s_v2  | 16 | 128 | 160 |  2x 1.92 TB  | 800000/4000  | 16000/320  | 16000/1280 | 32 | 4 | 6400   |
| Standard_L32s_v2  | 32 | 256 | 320 |  4x 1.92 TB  | 1,5 m/8000    | 32000/640  | 32000/1280 | 32 | 8 | 12800  |
| Standard_L48s_v2  | 48 | 384 | 480 |  6x 1.92 TB  | 2,2 m/14000   | 48000/960  | 48000/2000 | 32 | 8 | 16000 + |
| Standard_L64s_v2  | 64 | 512 | 640 |  8rychlostní 1.92 TB  | 2.9 m/16000   | 64000/1280 | 64000/2000 | 32 | 8 | 16000 + |
| Standard_L80s_v2<sup>6</sup> | 80 | 640 | 800 | 10x 1.92 TB | 3.8 m/20000 | 80000/1400 | 80000/2000 | 32 | 8 | 16000 + |

<sup>1</sup> virtuální počítače řady Lsv2-Series mají na disku dočasnou jednotku na bázi SCSI pro použití stránkovacího nebo stránkovacího souboru operačního systému (D: ve Windows,/dev/sdb v Linux). Tento disk poskytuje 80 GiB úložiště, 4 000 IOPS 80 a MB/s přenosovou rychlostí pro každých 8 vCPU (např. Standard_L80s_v2 poskytuje 800 GiB při 40 000 IOPS a 800 MB/s). To zajistí, že NVMe jednotky mohou být plně vyhrazené pro použití aplikací. Tento disk je dočasný a veškerá data budou ztracena při zastavení nebo zrušení přidělení.

<sup>2</sup> místní disky NVMe jsou dočasné, data se na těchto discích ztratí, když virtuální počítač zastavíte nebo zrušíte jeho přidělení.

<sup>3</sup> technologie Hyper-V NVMe Direct poskytuje zabezpečený přístup k místním jednotkám NVMe mapované na místo virtuálního počítače hosta.  Dosažení maximálního výkonu vyžaduje použití nejnovějšího sestavení WS2019 nebo Ubuntu 18,04 nebo 16,04 z Azure Marketplace.  Výkon zápisu se liší v závislosti na velikosti vstupně-výstupních operací, zatížení jednotky a využití kapacity.

<sup>4</sup> virtuální počítače řady Lsv2-Series neposkytují mezipaměť hostitele pro datový disk, protože nevyužívají úlohy Lsv2.

<sup>5</sup> virtuálních počítačů řady Lsv2- [Series můžou zvýšit](./disk-bursting.md) výkon svého disku po dobu až 30 minut. 

<sup>6</sup> virtuálních počítačů s více než 64 vCPU vyžadují jeden z těchto podporovaných hostovaných operačních systémů:

- Windows Server 2016 nebo novější
- Ubuntu 16,04 LTS nebo novější s vyladěným jádrem Azure (4,15 jádro nebo novější)
- SLES 12 SP2 nebo novější
- RHEL nebo CentOS verze 6,7 až 6,10 s nainstalovaným balíčkem LIS (nebo novějším) od Microsoftu
- RHEL nebo CentOS verze 7,3 s nainstalovaným balíčkem aplikace LIS verze 4.2.1 (nebo novější)
- RHEL nebo CentOS verze 7,6 nebo novější
- Oracle Linux s UEK4 nebo novějším
- Debian 9 s jádrem pro porty, Debian 10 nebo novější
- CoreOS s jádrem 4,14 nebo novějším

## <a name="size-table-definitions"></a>Definice tabulky velikostí

- Kapacita úložiště je v jednotkách GiB, tj. 1024^3 bajtů. Při porovnávání diskové kapacity měřené v GB (1000^3 B) s kapacitou měřenou v GiB (1024^3) pamatujte, že údaj v GiB je číselně menší. Například 1023 GiB = 1098,4 GB
- Propustnost disku se měří v počtu V/V operací za sekundu (IOPS) a v MB/s, kde 1 MB/s = 10^6 bajtů/s.
- Pokud chcete dosáhnout nejlepšího výkonu pro vaše virtuální počítače, měli byste omezit počet datových disků na 2 disky na vCPU.
- **Očekávaná šířka pásma sítě** je maximální agregovaná [Šířka pásma přidělená pro každý typ virtuálního počítače](../virtual-network/virtual-machine-network-throughput.md) v rámci všech síťových adaptérů pro všechna umístění. Horní omezení nejsou garantována, ale slouží jako vodítko pro výběr správného typu virtuálního počítače pro příslušnou aplikaci. Skutečný výkon sítě bude záviset na řadě faktorů, včetně zahlcení sítě, zatížení aplikací a nastavení sítě. Informace o optimalizaci propustnosti sítě najdete v tématu [Optimalizace propustnosti sítě pro Windows a Linux](../virtual-network/virtual-network-optimize-network-bandwidth.md). Pro dosažení očekávaného výkonu sítě na Linuxu nebo ve Windows může být nutné vybrat konkrétní verzi nebo optimalizovat virtuální počítač. Další informace najdete v tématu [Spolehlivé testování propustnosti virtuálního počítače](../virtual-network/virtual-network-bandwidth-testing.md).


## <a name="other-sizes-and-information"></a>Další velikosti a informace

- [Obecné účely](sizes-general.md)
- [Optimalizované pro paměť](sizes-memory.md)
- [Optimalizované pro úložiště](sizes-storage.md)
- [Optimalizované z hlediska GPU](sizes-gpu.md)
- [Vysokovýkonné výpočetní prostředí](sizes-hpc.md)
- [Předchozí generace](sizes-previous-gen.md)

Cenová kalkulačka: [Cenová Kalkulačka](https://azure.microsoft.com/pricing/calculator/)

Další informace o typech disků: [typy disků](./disks-types.md#ultra-disk)


## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám pomůžou porovnat výpočetní výkon napříč SKU Azure.