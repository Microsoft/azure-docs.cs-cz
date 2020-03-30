---
title: Řada LSV2 – virtuální počítače Azure
description: Specifikace pro virtuální chod řady Lsv2.
services: virtual-machines
author: sasha-melamed
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 103e19d6e299956b5ee1ad45b577e25f9f2de1c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164028"
---
# <a name="lsv2-series"></a>Řada Lsv2

Řada Lsv2 je vybavena vysokou propustností, nízkou latencí, přímo mapovaným místním úložištěm NVMe běžícím na [procesoru AMD EPYC<sup>TM</sup> 7551](https://www.amd.com/en/products/epyc-7000-series) s podporou všech jader 2,55 GHz a maximálním nárůstem 3,0 GHz. Virtuální počítače řady Lsv2 jsou dodávány ve velikostech od 8 do 80 virtuálních procesorů v simultánní konfiguraci s více vlákny.  K dispozici je 8 GiB paměti na virtuální procesor a jedno 1.92TB NVMe SSD M.2 zařízení na 8 virtuálních procesorů, s až 19.2TB (10x1.92TB) k dispozici na L80s v2.

> [!NOTE]
> Virtuální počítače řady Lsv2 jsou optimalizované pro použití místního disku na uzlu připojeném přímo k virtuálnímu počítači, nikoli k použití trvalých datových disků. To umožňuje větší iOP / propustnost pro vaše úlohy. Řady LSV2 a Ls nepodporují vytvoření místní mezipaměti pro zvýšení iOs dosažitelné trvalými datovými disky.
>
> Vysoká propustnost a IOP místního disku činí virtuální počítače řady Lsv2 ideální pro úložiště NoSQL, jako je Apache Cassandra a MongoDB, které replikují data napříč více virtuálními počítači, aby bylo dosaženo trvalosti v případě selhání jednoho virtuálního počítače.
>
> Další informace najdete v tématu Optimalizace výkonu na virtuálních počítačích řady Lsv2 pro [Windows](../virtual-machines/windows/storage-performance.md) nebo [Linux](../virtual-machines/linux/storage-performance.md).  

ACU: 150-175

Úložiště Premium: Podporováno

Ukládání do mezipaměti úložiště Premium: Není podporováno

Migrace za provozu: Není podporována

Aktualizace pro zachování paměti: Není podporováno

| Velikost | Virtuální procesory | Paměť (GiB) | Dočasný disk<sup>1</sup> (GiB) | Disky NVMe<sup>2</sup> | Propustnost disku NVMe<sup>3</sup> (čtení IOPS/MB/s) | Maximální propustnost datového disku bez mezipaměti (IOP/MB/s)<sup>4</sup> | Maximální počet datových disků | Maximální počet síťových připojení / Očekávaná šířka pásma sítě (Mb/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_L8s_v2   |  8 |  64 |  80 |  1x1,92 TB  | 400000/2000  | 8000/160   | 16 | 2 / 3200   |
| Standard_L16s_v2  | 16 | 128 | 160 |  2x1,92 TB  | 800000/4000  | 16000/320  | 32 | 4 / 6400   |
| Standard_L32s_v2  | 32 | 256 | 320 |  4x1,92 TB  | 1,5 m/8000    | 32000/640  | 32 | 8 / 12800  |
| Standard_L48s_v2  | 48 | 384 | 480 |  6x1,92 TB  | 2.2M/14000   | 48000/960  | 32 | 8 / 16000+ |
| Standard_L64s_v2  | 64 | 512 | 640 |  8x1,92 TB  | 2.9M/16000   | 64000/1280 | 32 | 8 / 16000+ |
| <sup>5. Standard_L80s_v2</sup> | 80 | 640 | 800 | 10x1,92 TB | 3.8M/20000 | 80000/1400 | 32 | 8 / 16000+ |

<sup>1</sup> Virtuální počítače řady Lsv2 mají standardní disk s dočasným prostředkem založený na SCSI pro použití stránkovacího a odkládacího souboru operačního systému (D: v systému Windows, /dev/sdb na Linuxu). Tento disk poskytuje 80 GIB úložiště, 4 000 IOPS a 80 MB/s přenosovou rychlost pro každých 8 virtuálních procesorů (např. Standard_L80s_v2 poskytuje 800 GiB při 40 000 IOPS a 800 MB/s). Tím je zajištěno, že jednotky NVMe mohou být plně vyhrazeny pro použití aplikací. Tento disk je dočasný a všechna data budou ztracena při stop/reallocate.

<sup>2</sup> Místní NVMe disky jsou dočasné, data budou ztraceny na těchto discích, pokud zastavíte/naalokujete virtuální počítač.

<sup>3</sup> Technologie Hyper-V NVMe Direct poskytuje neomezený přístup k místním jednotkám NVMe, které jsou bezpečně namapovány do prostoru hostovaného virtuálního plynu.  Dosažení maximálního výkonu vyžaduje použití nejnovějšího sestavení WS2019 nebo Ubuntu 18.04 nebo 16.04 z Azure Marketplace.  Výkon zápisu se liší v závislosti na velikosti va, zatížení jednotky a využití kapacity.

<sup>4</sup> Virtuální počítače řady Lsv2 neposkytují mezipaměť hostitele pro datový disk, protože nevyužívají úlohy Lsv2.  Virtuální počítače Lsv2 však mohou pojmout možnost disku dočasného operačního systému Azure (až 30 GiB).

<sup>5</sup> virtuálních počítače s více než 64 virtuálními procesory vyžaduje jeden z těchto podporovaných hostovacích operačních systémů:

- Windows Server 2016 nebo novější
- Ubuntu 16.04 LTS nebo novější, s jádrem vyladěným v Azure (jádro 4.15 nebo novější)
- SLES 12 SP2 nebo novější
- RHEL nebo CentOS verze 6.7 až 6.10, s nainstalovaným balíčkem LIS dodaným společností Microsoft 4.3.1 (nebo novějším)
- RHEL nebo CentOS verze 7.3 s nainstalovaným balíčkem LIS dodaným společností Microsoft 4.2.1 (nebo novějším)
- RHEL nebo CentOS verze 7.6 nebo novější
- Oracle Linux s UEK4 nebo novějším
- Debian 9 s jádrem backports, Debian 10 nebo novější
- CoreOS s jádrem 4.14 nebo novějším

## <a name="size-table-definitions"></a>Definice tabulky velikostí

- Kapacita úložiště je v jednotkách GiB, tj. 1024^3 bajtů. Při porovnávání diskové kapacity měřené v GB (1000^3 B) s kapacitou měřenou v GiB (1024^3) pamatujte, že údaj v GiB je číselně menší. Například 1023 GiB = 1098,4 GB
- Propustnost disku se měří v počtu V/V operací za sekundu (IOPS) a v MB/s, kde 1 MB/s = 10^6 bajtů/s.
- Pokud chcete získat nejlepší výkon pro vaše virtuální počítače, měli byste omezit počet datových disků na 2 disky na virtuální procesor.
- **Očekávaná šířka pásma sítě** je maximální agregovaná [šířka pásma přidělená na typ virtuálního počítače](../virtual-network/virtual-machine-network-throughput.md) ve všech síťových počítačích pro všechny cíle. Horní omezení nejsou garantována, ale slouží jako vodítko pro výběr správného typu virtuálního počítače pro příslušnou aplikaci. Skutečný výkon sítě bude záviset na řadě faktorů, včetně zahlcení sítě, zatížení aplikací a nastavení sítě. Informace o optimalizaci propustnosti sítě najdete v tématu [Optimalizace propustnosti sítě pro Windows a Linux](../virtual-network/virtual-network-optimize-network-bandwidth.md). Pro dosažení očekávaného výkonu sítě na Linuxu nebo ve Windows může být nutné vybrat konkrétní verzi nebo optimalizovat virtuální počítač. Další informace najdete v tématu [Spolehlivé testování propustnosti virtuálního počítače](../virtual-network/virtual-network-bandwidth-testing.md).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [výpočetní jednotky Azure (ACU)](acu.md) vám můžou pomoct porovnat výpočetní výkon napříč virtuálními jednotkami Azure.
