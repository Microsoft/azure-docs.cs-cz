---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/24/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 050308e1c8de160f1671ded991e550087299ae2f
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285722"
---
# <a name="ultra-ssd-preview-managed-disks-for-azure-virtual-machine-workloads"></a>Ultra (preview) spravované disky SSD pro úlohy virtuálních počítačů Azure

Ultra SSD Azure (preview) poskytuje vysokou propustnost, vysoké IOPS a stálá nízká latence diskové úložiště pro virtuální počítače Azure IaaS. Tato nová nabídka poskytuje horní řádek výkon ve stejné úrovně dostupnosti jako naše stávající nabídky disky. Další výhody Ultra SSD patří schopnost dynamicky měnit výkonu disku spolu s vašimi úlohami, aniž byste museli restartovat své virtuální počítače. Ultra SSD, je vhodné pro úlohy náročné na data, jako je SAP HANA, databáze na nejvyšší úrovni a transakce náročné úlohy.

## <a name="ultra-ssd-features"></a>Funkce Ultra SSD

**Spravované disky**: Ultra SSD disků jsou dostupné jenom jako Managed Disks. Ultra vlastnosti jednotek SSD se nedá nasadit jako nespravovaný Disk nebo objekt Blob stránky. Při vytváření spravovaného disku, zadáváte sku disku jako UltraSSD_LRS typ a určení velikosti disku, vstupně-výstupních operací, a propustnost, které potřebujete a Azure vytvoří a spravuje disk za vás.  

**Virtuální počítače**: Ultra jednotky SSD jsou navrženy pro práci s všech skladových položek virtuálních počítačů Azure Premium SSD povoleno; ale protože je momentálně ve verzi preview, virtuální počítače jsou velikost jako ES/DS v3.

**Konfigurace dynamické výkonu**: Ultra SSD umožňují dynamicky měnit výkon (IOPS a propustnost) disku spolu s vaší potřebám provádění úloh bez nutnosti restartování virtuálních počítačů.

## <a name="scalability-and-performance-targets"></a>Škálovatelnost a cíle výkonnosti

Když si zřídíte Ultra SSD, budete mít možnost konfigurovat nezávisle kapacitu a výkon disku. Ultra vlastnosti jednotek SSD v několik pevných velikostech pocházet 4 GB až 64 TiB a funkcí modelu konfigurace flexibilní výkonu, který umožňuje nezávisle na sobě konfigurovat IOPS a propustnost. Ultra vlastnosti jednotek SSD se dá využít pouze jako datové disky. Doporučujeme použít disky Premium SSD jako disky s operačním systémem.

Jsou některé klíčové funkce Ultra SSD:

- Kapacita disku: Ultra SSD nabízí celou řadu různých disky o velikosti od 4 GB až 64 TB.
- VSTUPNĚ-výstupních diskových: Ultra SSD podporu vstupně-výstupních operací omezení 300 IOPS/GiB maximálně 160 kB IOPS na disk. K dosažení vstupně-výstupních operací, kterou jste zřídili, ujistěte se, že jsou vybrané vstupně-výstupních operací disku menší než počet IOPS virtuálních počítačů. Minimální vstupně-výstupních operací disku je 100 vstupně-výstupních operací.
- Propustnost disku: S Ultra disků SSD, propustnost limit jednoho disku je 256 KiB/s pro každý zřízené IOPS, až do maximálního počtu 2000 MB/s disku (kde MB/s = 10 ^ 6 bajtů za sekundu). Propustnost disku minimální je 1 MiB.

Následující tabulka shrnuje různé podporované konfigurace pro různé velikosti disků:  

### <a name="ultra-ssd-managed-disk-offerings"></a>Nabídky služby Ultra SSD Managed Disks

|Velikost disku (GiB)  |Limity vstupně-výstupních operací  |Limit propustnosti (MB/s)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9 600         |2,000         |
|64     |19 200         |2,000         |
|128     |38,400         |2,000         |
|256     |76,800         |2,000         |
|512     |80,000         |2,000         |
|1 024 65 536 (velikosti v tomto rozsahu, zvyšovat v přírůstcích po 1 TB)     |160,000         |2,000         |

## <a name="pricing-and-billing"></a>Ceny a fakturace

Při použití Ultra disků SSD, uplatní se následující fakturační aspekty:

- Spravovaná velikost disku
- Spravovaný Disk zřízené IOPS
- Zřízená propustnost spravovaného disku
- Ultra poplatku za rezervaci SSD virtuálního počítače

### <a name="managed-disk-size"></a>Spravovaná velikost disku

Spravované disky se účtují na virtuální počítač o velikosti tohoto vám choosed při provisionning nového virtuálního počítače Azure. Azure mapuje zřízenou velikost (zaokrouhlenou nahoru) na nejbližší nabídku velikosti disku. Podrobnosti o velikosti disků nabízejí najdete v tabulce škálovatelnost a cíle výkonnosti výše v části. Každý disk mapuje na podporovaná zřízená velikost disku a bude odpovídajícím způsobem se účtuje po hodinách. Například pokud zřízený 200 GB Ultra Disk SSD na úrovni a odstraní po 20 hodin se namapuje do nabídky velikost disků 256 GB a vám budeme účtovat 256 GB po dobu 20 hodin. Toto účtování byl založen na využití výpočetních hodinách bez ohledu na objem dat ve skutečnosti zapsaných na disk.

### <a name="managed-disk-provisioned-iops"></a>Spravovaný Disk zřízené IOPS

Počet požadavků, které vaše aplikace posílá se vstupně-výstupních operací za sekundu na disky. Vstupně výstupní operace může být sekvenčního čtení nebo zápisu nebo náhodného čtení nebo zápisu. Podle velikosti disku nebo počet disků připojených k virtuálnímu počítači, je průměrný počet vstupně-výstupních operací se účtují po hodinách. Podrobnosti o disku, který nabízí vstupně-výstupních operací najdete v tabulce škálovatelnost a cíle výkonnosti výše v části.

### <a name="managed-disk-provisioned-throughput"></a>Zřízená propustnost spravovaného disku

Propustnost je množství dat, které vaše aplikace odesílá do disky v zadaných intervalech, měřený v bajtech za sekundu. Pokud aplikace provádí velké vstupně výstupní operace, vyžaduje vysokou propustnost.  

Existuje vztah mezi propustnost a vstupně-výstupních operací, jak je znázorněno v následujícím vzorci: IOPS x velikost vstupně-výstupních operací = propustnost

Proto je důležité určit optimální hodnoty vstupně-výstupních operací a propustnosti, které vaše aplikace vyžaduje. Při pokusu o optimalizaci jedné druhé také získá vliv. Doporučujeme, abyste od propustnost odpovídající velikost 16 KiB vstupně-výstupní operace a nastavení v případě potřeby větší propustnost.

Podrobnosti o propustnost disku podporované na Ultra SSD najdete v tabulce škálovatelnost a cíle výkonnosti výše v části. Jako je velikost disku a vstupně-výstupních operací zřízená propustnost se fakturuje po hodinách účtujeme za MB/s, zřízené.

### <a name="ultra-ssd-vm-reservation-fee"></a>Ultra poplatku za rezervaci SSD virtuálního počítače

Představujeme schopností ve virtuálním počítači, který označuje, že váš virtuální počítač je kompatibilní Ultra SSD. Ultra SSD kompatibilní Virtuálním počítači přidělí vyhrazenou kapacitu šířky pásma mezi výpočetní instance virtuálního počítače a blok jednotka škálování úložiště optimalizovat výkon a snižuje latenci. Na virtuální počítač výsledky pod kontrolou rezervace, která je uložena pouze tehdy, pokud jste povolili možnost Ultra SSD ve virtuálním počítači bez připojení k němu Ultra SSD disk přidáte tuto funkci. Když Ultra SSD disk je připojen k virtuálnímu počítači kompatibilní Ultra SSD, tento poplatek se neuplatní. Tento poplatek se za vCPU zřízení na virtuálním počítači.

Odkazovat [stránce s cenami Azure Disks](https://azure.microsoft.com/pricing/details/managed-disks/) nové Ultra disky SSD cenové podrobnosti, které jsou k dispozici ve verzi limited preview.

### <a name="ultra-ssd-preview-scope-and-limitations"></a>Rozsah Ultra SSD ve verzi Preview a omezení

Ve verzi preview, Ultra SSD disky:

- Bude zpočátku podporována v oblastech východní USA 2 v jedné zóně dostupnosti  
- Jde použít jenom se zónami dostupnosti (skupiny dostupnosti a nasazení jeden virtuální počítač mimo zóny budou nemá schopnost připojení Ultra disku SSD)
- Jsou podporovány pouze na virtuálních počítačích ES/DS v3
- Jsou k dispozici pouze jako datové disky a pouze velikost fyzického sektoru 4k podpoře  
- Je možné vytvořit pouze jako prázdné disky  
- Aktuálně lze nasadit pouze pomocí šablony Resource Manageru, rozhraní příkazového řádku a sady SDK pro Python.
- Se (zatím) podpory na disku skupiny dostupnosti snímky, Image virtuálních počítačů, Škálovací sady virtuálních počítačů a Azure Disk Encryption.
- Nebudou (zatím) podporuje integraci s Azure Backup nebo Azure Site Recovery.
- Stejně jako u [většina náhledy](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), tato funkce by neměl být používat pro produkční úlohy až do obecné dostupnosti (GA).
