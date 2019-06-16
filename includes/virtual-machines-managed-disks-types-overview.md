---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/22/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4abf50e11070f2060309ae9b9cd045c874a2c52e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133224"
---
# <a name="what-disk-types-are-available-in-azure"></a>Jaké typy disků jsou dostupné v Azure?

Spravované disky Azure nyní nabízí čtyři typy disků, tři, z nichž jsou všeobecně dostupná (GA) a jednu, která je aktuálně ve verzi preview. Těchto čtyř typů disků nevidí každý mít vlastní příslušné cílové scénáře zákazníka.

## <a name="disk-comparison"></a>Porovnání disků

Následující tabulka obsahuje porovnání ultra solid-stát disky (SSD) (preview), premium SSD, SSD na úrovni standard a standardních pevných disků (HDD) pro spravované disky, které vám pomůžou při rozhodování, co se má použít.

|   | Ultra SSD (preview)   | Premium SSD   | SSD úrovně Standard   | Standard HDD   |
|---------|---------|---------|---------|---------|
|Typ disku   |SSD   |SSD   |SSD   |HDD   |
|Scénář   |Úlohy náročné na vstupně-výstupních operací, jako jsou SAP HANA, databáze na nejvyšší úrovni (třeba SQL, Oracle) a dalších transakcí náročná na výkon úloh.   |Úlohy v produkčním prostředí a úlohy, u kterých záleží na výkonu   |Webové servery, málo používaná podnikové aplikace a pro vývoj/testování   |Zálohování méně náročné úlohy s řídkým přístupem   |
|Velikost disku   |gibibajt 65 536 (GiB) (Preview)   |32 767 giB    |32 767 giB   |32 767 giB   |
|Maximální propustnost   |2 000 MiB/s (Preview)   |900 MiB/s   |750 MiB/s   |500 MiB/s   |
|Maximální počet vstupně-výstupních operací   |160,000 (preview)   |20,000   |6,000   |2 000   |

## <a name="ultra-ssd-preview"></a>Ultra SSD (preview)

Ultra SSD Azure (preview) poskytuje vysokou propustnost, vysoké IOPS a stálá nízká latence diskové úložiště pro virtuální počítače Azure IaaS. Některé další výhody ultra SSD patří schopnost dynamicky měnit výkon disku spolu s úlohami, aniž byste museli restartovat své virtuální počítače. Ultra SSD disky jsou vhodné pro úlohy náročné na data, jako je SAP HANA, databáze na nejvyšší úrovni a transakce náročné úlohy. Ultra SSD jde použít jenom jako datové disky. Doporučujeme použít disky premium SSD jako disky s operačním systémem.

### <a name="performance"></a>Výkon

Když si zřídíte ultra disku, můžete nezávisle na sobě konfigurovat kapacitu a výkon disku. Ultra SSD se dělí na několik pevných velikostech od 4 GB až 64 TiB a funkcí modelu konfigurace flexibilní výkonu, který umožňuje nezávisle na sobě konfigurovat IOPS a propustnost.

Jsou některé klíčové funkce Ultra SSD:

- Kapacita disku: Ultra rozsahy kapacity SSD z 4 GiB až 64 TB.
- Vstupně-výstupních operací disku: Ultra SSD podporu vstupně-výstupních operací omezení 300 IOPS/GiB maximálně 160 kB IOPS na disk. K dosažení vstupně-výstupních operací, kterou jste zřídili, ujistěte se, že jsou vybrané vstupně-výstupních operací disku menší než počet IOPS virtuálních počítačů. Minimální vstupně-výstupních operací disku jsou 100 vstupně-výstupních operací.
- Propustnost disku: S ultra SSD, propustnost limit jednoho disku je 256 KiB/s pro každý zřízené IOPS, až do maximálního počtu 2000 MB/s disku (kde MB/s = 10 ^ 6 bajtů za sekundu). Propustnost disku minimální je 1 MiB.
- Ultra SSD disky podporují nastavení vlastnosti výkonu disku (IOPS a propustnost) za běhu bez odpojení disku od virtuálního počítače. Jakmile se operace změny velikosti disku výkonu se vystavil na disku, může trvat až hodinu, tato změna se skutečně projeví.

### <a name="disk-size"></a>Velikost disku

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

### <a name="transactions"></a>Transakce

Ultra disků SSD každý vstupně-výstupní operace menší než nebo rovna až 256 KiB propustnosti se nepovažuje za jeden vstupně-výstupní operace. Vstupně-výstupní operace větší než 256 KiB propustnosti se považují za více vstupně-výstupních operací, o velikosti 256 KiB.

### <a name="preview-scope-and-limitations"></a>Rozsah ve verzi Preview a omezení

Ve verzi preview, ultra SSD:

- Jsou podporovány v oblasti východní USA 2 v jediné zóny dostupnosti  
- Jde použít jenom se zónami dostupnosti (skupiny dostupnosti a jednoho nasazení virtuálních počítačů mimo zóny budou nemá schopnost připojení ultra disku)
- Jsou podporovány pouze na virtuálních počítačích ES/DS v3
- Jsou k dispozici pouze jako datové disky a pouze velikost fyzického sektoru 4k podpoře  
- Je možné vytvořit pouze jako prázdné disky  
- Aktuálně lze nasadit pouze pomocí šablony Azure Resource Manageru, rozhraní příkazového řádku, Powershellu a sadě Python SDK.
- Nelze nasadit pomocí webu Azure portal (ještě).
- Zatím nepodporuje snímky disků, imagí virtuálních počítačů, dostupnosti, škálovací sady virtuálních počítačů a Azure disk encryption.
- Zatím nepodporuje integraci s Azure Backup nebo Azure Site Recovery.
- Stejně jako u [většina náhledy](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), tuto funkci neměli používat pro produkční úlohy až do obecné dostupnosti (GA).
