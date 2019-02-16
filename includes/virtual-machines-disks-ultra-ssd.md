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
ms.openlocfilehash: 4b5d2de2e9ccd44517e083a435e127bd5678f002
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56331169"
---
# <a name="ultra-disks-preview-managed-disks-for-azure-virtual-machine-workloads"></a>Ultra disky (preview) spravovaných disků pro úlohy virtuálních počítačů Azure

Ultra disků v Azure (preview) poskytuje vysokou propustnost, vysoké IOPS a stálá nízká latence diskové úložiště pro virtuální počítače Azure IaaS. Tato nová nabídka poskytuje horní řádek výkon ve stejné úrovně dostupnosti jako naše stávající nabídky disky. Další výhody ultra disků patří schopnost dynamicky měnit výkonu disku spolu s vašimi úlohami, aniž byste museli restartovat své virtuální počítače. Ultra disky jsou vhodné pro úlohy náročné na data, jako je SAP HANA, databáze na nejvyšší úrovni a transakce náročné úlohy.

## <a name="ultra-disk-features"></a>Funkce Ultra disků

**Spravované disky**: Ultra disky jsou dostupné jenom jako spravované disky. Ultra disky nejde nasadit jako nespravovaný disk nebo objekt blob stránky. Při vytváření spravovaného disku, zadáváte sku disku jako UltraSSD_LRS typ a určení velikosti disku, vstupně-výstupních operací, a propustnost, které potřebujete a Azure vytvoří a spravuje disk za vás.  

**Virtual Machines**: Ultra disky jsou navrženy pro práci s všechny Premium SSD povolené skladové položky virtuálních počítačů Azure; ale protože je momentálně ve verzi preview, virtuální počítače velikosti jako ES/DS v3.

**Konfigurace dynamické výkonu**: Ultra disky umožňují dynamicky měnit výkon (IOPS a propustnost) disku spolu s vaší potřebám provádění úloh bez nutnosti restartování virtuálních počítačů.

## <a name="scalability-and-performance-targets"></a>Škálovatelnost a cíle výkonnosti

Když si zřídíte ultra disky, budete mít možnost konfigurovat nezávisle kapacitu a výkon disku. Ultra disky v několik pevných velikostech pocházet 4 GB až 64 TiB a funkcí modelu konfigurace flexibilní výkonu, který umožňuje nezávisle na sobě konfigurovat IOPS a propustnost. Ultra disky se dá využít pouze jako datové disky. Doporučujeme použít disky Premium SSD jako disky s operačním systémem.

Některé klíčové funkce ultra disků jsou:

- Kapacita disku: Ultra disky nabízí celou řadu různých disky o velikosti od 4 GB až 64 TiB.
- Vstupně-výstupních operací disku: Ultra disky podporují vstupně-výstupních operací omezení 300 IOPS/GiB maximálně 160 kB IOPS na disk. K dosažení vstupně-výstupních operací, kterou jste zřídili, ujistěte se, že jsou vybrané vstupně-výstupních operací disku menší než počet IOPS virtuálních počítačů. Minimální vstupně-výstupních operací disku je 100 vstupně-výstupních operací.
- Propustnost disku: Ultra disky limit propustnosti z jednoho disku je 256 KiB/s pro každý zřízené IOPS, až do maximálního počtu 2000 MB/s disku (kde MB/s = 10 ^ 6 bajtů za sekundu). Propustnost disku minimální je 1 MiB.

Následující tabulka shrnuje různé podporované konfigurace pro různé velikosti disků:  

### <a name="ultra-disks-managed-disk-offerings"></a>Ultra disky spravovaného disku nabídky

|Velikost disku (GiB)  |Limity vstupně-výstupních operací  |Limit propustnosti (MB/s)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9,600         |2 000         |
|64     |19,200         |2 000         |
|128     |38,400         |2 000         |
|256     |76,800         |2 000         |
|512     |80,000         |2 000         |
|1 024 65 536 (velikosti v tomto rozsahu, zvyšovat v přírůstcích po 1 TB)     |160,000         |2 000         |

## <a name="pricing-and-billing"></a>Ceny a fakturace

Při používání ultra disků, se použijí následující fakturační aspekty:

- Spravovaná velikost disku
- Spravovaný Disk zřízené IOPS
- Zřízená propustnost spravovaného disku
- Poplatek za rezervaci Ultra disku virtuálního počítače

### <a name="managed-disk-size"></a>Spravovaná velikost disku

Spravované disky se účtují o velikostech virtuálních počítačů, které můžete vybrat při zřizování nového virtuálního počítače Azure. Azure mapuje zřízenou velikost (zaokrouhlenou nahoru) na nejbližší nabídku velikosti disku. Podrobnosti o velikosti disků nabízejí najdete v tabulce škálovatelnost a cíle výkonnosti výše v části. Každý disk mapuje na podporovaná zřízená velikost disku a bude odpovídajícím způsobem se účtuje po hodinách. Například pokud zřízené ultra disk 200 GB a odstraní po 20 hodin se namapuje do nabídky velikost disků 256 GB a vám budeme účtovat 256 GB po dobu 20 hodin. Toto účtování byl založen na využití výpočetních hodinách bez ohledu na objem dat ve skutečnosti zapsaných na disk.

### <a name="managed-disk-provisioned-iops"></a>Spravovaný disk zřízené IOPS

Počet požadavků, které vaše aplikace posílá se vstupně-výstupních operací za sekundu na disky. Vstupně výstupní operace může být sekvenčního čtení nebo zápisu nebo náhodného čtení nebo zápisu. Podle velikosti disku nebo počet disků připojených k virtuálnímu počítači, je průměrný počet vstupně-výstupních operací se účtují po hodinách. Podrobnosti o disku, který nabízí vstupně-výstupních operací najdete v tabulce škálovatelnost a cíle výkonnosti výše v části.

### <a name="managed-disk-provisioned-throughput"></a>Spravovat zřízenou propustnost disku

Propustnost je množství dat, které vaše aplikace odesílá do disky v zadaných intervalech, měřený v bajtech za sekundu. Pokud aplikace provádí velké vstupně výstupní operace, vyžaduje vysokou propustnost.  

Existuje vztah mezi propustnost a vstupně-výstupních operací, jak je znázorněno v následujícím vzorci:  Vstupně-výstupních operací x velikost vstupně-výstupních operací = propustnost

Proto je důležité určit optimální hodnoty vstupně-výstupních operací a propustnosti, které vaše aplikace vyžaduje. Při pokusu o optimalizaci jedné druhé také získá vliv. Doporučujeme, abyste od propustnost odpovídající velikost 16 KiB vstupně-výstupní operace a nastavení v případě potřeby větší propustnost.

Podrobnosti o propustnost disku podporované na ultra disky najdete v tabulce škálovatelnost a cíle výkonnosti výše v části. Jako je velikost disku a vstupně-výstupních operací zřízená propustnost se fakturuje po hodinách účtujeme za MB/s, zřízené.

### <a name="ultra-disk-vm-reservation-fee"></a>Poplatek za rezervaci Ultra disku virtuálního počítače

Představujeme schopností ve virtuálním počítači, který označuje, že váš virtuální počítač je ultra disku kompatibilní. Disk s ultra kompatibilní virtuální počítače přidělí vyhrazenou kapacitu šířky pásma mezi výpočetní instance virtuálního počítače a blok jednotka škálování úložiště optimalizovat výkon a snižuje latenci. Přidání této funkce na výsledky do virtuálního počítače rezervace poplatek, která je uložena pouze tehdy, pokud jste povolili možnost ultra disk ve virtuálním počítači bez připojení k němu ultra disku. Když ultra disk je připojený k ultra kompatibilní virtuální počítač, tento poplatek se neuplatní. Tento poplatek se za vCPU zřízení na virtuálním počítači.

Odkazovat [stránce s cenami Azure Disks](https://azure.microsoft.com/pricing/details/managed-disks/) pro nové ultra disky cena podrobnosti k dispozici ve verzi limited preview.

### <a name="ultra-disk-preview-scope-and-limitations"></a>Rozsah Ultra disku ve verzi preview a omezení

Ve verzi preview, ultra disky:

- Bude zpočátku podporována v oblastech východní USA 2 v jedné zóně dostupnosti  
- Jde použít jenom se zónami dostupnosti (skupiny dostupnosti a nasazení jeden virtuální počítač mimo zóny budou nemá schopnost připojení ultra disku)
- Jsou podporovány pouze na virtuálních počítačích ES/DS v3
- Jsou k dispozici pouze jako datové disky a pouze velikost fyzického sektoru 4k podpoře  
- Je možné vytvořit pouze jako prázdné disky  
- Aktuálně lze nasadit pouze pomocí šablony Resource Manageru, rozhraní příkazového řádku a sady SDK pro Python.
- Se (zatím) podpory na disku skupiny dostupnosti snímky, Image virtuálních počítačů, Škálovací sady virtuálních počítačů a Azure Disk Encryption.
- Nebudou (zatím) podporuje integraci s Azure Backup nebo Azure Site Recovery.
- Stejně jako u [většina náhledy](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), tato funkce by neměl být používat pro produkční úlohy až do obecné dostupnosti (GA).
