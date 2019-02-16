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
ms.openlocfilehash: 7f5583bfd6089362aef51285643f5fc920005242
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56331164"
---
# <a name="what-disk-types-are-available-in-azure"></a>Jaké typy disků jsou dostupné v Azure?

Spravované disky Azure nyní nabízí čtyři typy disků, tři, z nichž jsou všeobecně dostupná (GA) a jednu, která je aktuálně ve verzi preview. Těchto čtyř typů disků nevidí každý mít vlastní příslušné cílové scénáře zákazníka.

## <a name="disk-comparison"></a>Porovnání disků

Následující tabulka obsahuje porovnání ultra disků (preview), premium solid-stát jednotky SSD (Solid-State Drive), SSD na úrovni standard, a standardních pevných disků (HDD) pro spravované disky, které vám pomohou rozhodnout, co se má použít.

|   | Ultra disky (preview)   | Premium SSD   | SSD úrovně Standard   | Standard HDD   |
|---------|---------|---------|---------|---------|
|Typ disku   |SSD   |SSD   |SSD   |HDD   |
|Scénář   |Úlohy náročné na vstupně-výstupních operací, jako jsou SAP HANA, databáze na nejvyšší úrovni (třeba SQL, Oracle) a dalších transakcí náročná na výkon úloh.   |Úlohy v produkčním prostředí a úlohy, u kterých záleží na výkonu   |Webové servery, málo používaná podnikové aplikace a pro vývoj/testování   |Zálohování méně náročné úlohy s řídkým přístupem   |
|Velikost disku   |gibibajt 65 536 (GiB) (Preview)   |4095 giB (GA), 32 767 GiB (Preview)    |4095 GiB (GA), 32 767 GiB (Preview)   |4095 giB (GA), 32 767 GiB (Preview)   |
|Maximální propustnost   |2 000 MiB/s (Preview)   |250 (GA) MiB/s, 750 MiB/s (Preview)   |60 MiB/s (GA), 500 MiB/s (Preview)   |60 Mib/s (GA), 500 MiB/s (Preview)   |
|Maximální počet vstupně-výstupních operací za sekundu   |160,000 (preview)   |7500 (GA), 20 000 (Preview)   |500 (GA), 2 000 (Preview)   |500 (GA), 2 000 (Preview)   |

## <a name="ultra-disks-preview"></a>Ultra disky (preview)

Ultra disků v Azure (preview) poskytovat vysokou propustnost, vysoké IOPS a stálá nízká latence diskové úložiště pro virtuální počítače Azure IaaS. Některé další výhody ultra disků patří schopnost dynamicky měnit výkon disku spolu s úlohami, aniž byste museli restartovat své virtuální počítače. Ultra disky jsou vhodné pro úlohy náročné na data, jako je SAP HANA, databáze na nejvyšší úrovni a transakce náročné úlohy. Ultra disky jde použít jenom jako datové disky. Doporučujeme použít disky premium SSD jako disky s operačním systémem.

### <a name="performance"></a>Výkon

Když si zřídíte ultra disku, můžete nezávisle na sobě konfigurovat kapacitu a výkon disku. Ultra disky se dělí na několik pevných velikostech od 4 GB až 64 TiB a funkcí modelu konfigurace flexibilní výkonu, který umožňuje nezávisle na sobě konfigurovat IOPS a propustnost.

Některé klíčové funkce Ultra disků jsou:

- Kapacita disku: Ultra disky kapacity od 4 GiB až 64 TB.
- Vstupně-výstupních operací disku: Ultra disky podporují vstupně-výstupních operací omezení 300 IOPS/GiB maximálně 160 kB IOPS na disk. K dosažení vstupně-výstupních operací, kterou jste zřídili, ujistěte se, že jsou vybrané vstupně-výstupních operací disku menší než počet IOPS virtuálních počítačů. Minimální vstupně-výstupních operací disku jsou 100 vstupně-výstupních operací.
- Propustnost disku: Ultra disky limit propustnosti z jednoho disku je 256 KiB/s pro každý zřízené IOPS, až do maximálního počtu 2000 MB/s disku (kde MB/s = 10 ^ 6 bajtů za sekundu). Propustnost disku minimální je 1 MiB.

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

### <a name="preview-scope-and-limitations"></a>Rozsah ve verzi Preview a omezení

Ve verzi preview, ultra disky:

- Jsou podporovány v oblasti východní USA 2 v jediné zóny dostupnosti  
- Jde použít jenom se zónami dostupnosti (skupiny dostupnosti a jednoho nasazení virtuálních počítačů mimo zóny budou nemá schopnost připojení ultra disku)
- Jsou podporovány pouze na virtuálních počítačích ES/DS v3
- Jsou k dispozici pouze jako datové disky a pouze velikost fyzického sektoru 4k podpoře  
- Je možné vytvořit pouze jako prázdné disky  
- Aktuálně lze nasadit pouze pomocí šablony Azure Resource Manageru, rozhraní příkazového řádku a python SDK.
- Zatím nepodporuje snímky disků, imagí virtuálních počítačů, dostupnosti, škálovací sady virtuálních počítačů a Azure disk encryption.
- Zatím nepodporuje integraci s Azure Backup nebo Azure Site Recovery.
- Stejně jako u [většina náhledy](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), tuto funkci neměli používat pro produkční úlohy až do obecné dostupnosti (GA).

## <a name="premium-ssd"></a>Premium SSD

Azure premium SSD poskytovat podporu vysoce výkonných a s nízkou latencí disků pro virtuální počítače (VM) s vstup/výstup (IO)-náročné úlohy. Pokud chcete využít výhod rychlost a výkon disků premium storage, můžete migrovat existující disky virtuálních počítačů na Premium SSD. SSD disky Premium jsou vhodné pro důležité produkční aplikace.

### <a name="disk-size"></a>Velikost disku

Velikosti s hvězdičkou jsou aktuálně ve verzi preview.

| Velikosti úrovně Premium SSD  | P4               | P6               | P10             | P15 | P20              | S30              | P40              | P50              | P60 *              | P70 *              | P80 *              |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Velikost disku v GB           | 32             | 64             | 128            | 256  | 512            | 1,024    | 2,048     | 4,095    | 8 192     | 16,384     | 32,767     |
| Vstupně-výstupní operace za sekundu / disk       | Až 120 | Až 240              | Až 500              | Až 1100 | Až 2,300              | Až 5 000              | Až 7500             | Až 7500              | Až 12 500              | Až pro 15 000              | Až 20 000              |
| Propustnost / disk | Až pro 25 MiB za sekundu | Až na 50 MiB za sekundu | Až 100 MiB/s | Až 125 MiB za sekundu | Až 150 MiB za sekundu | Až 200 MiB za sekundu | Až 250 MiB za sekundu | Až 250 MiB za sekundu| Až 480 MiB za sekundu | Až 750 MiB za sekundu | Až 750 MiB za sekundu |

## <a name="standard-ssd"></a>SSD úrovně Standard

SSD disky Azure standard je možnost a nákladově efektivní služba storage optimalizované pro úlohy, které je třeba zajistit konzistentní výkon na nižších úrovních vstupně-výstupních operací. SSD na úrovni Standard nabízí prostředí dobrý základní úrovně pro ty, kteří chtějí přesunout do cloudu, zejména v případě, že dochází k problémům s odchylkou úloh běžících na řešení pevný disk v místním prostředí. Standardní jednotky SSD poskytují lepší dostupnosti, konzistence, spolehlivost a latenci v porovnání s HDD disky. SSD disky Standard jsou vhodné pro webové servery, s nízkou vstupně-výstupních operací aplikační servery, málo používaná podnikových aplikací a vývojové a testovací úlohy.

### <a name="disk-size"></a>Velikost disku

Velikosti s hvězdičkou jsou aktuálně ve verzi preview.

| Standardní velikosti SSD  | E10               | E15               | E20             | E30 | E40              | E50              | E60*              | E70*              | E80*              |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Velikost disku v GB           | 128             | 256             | 512            | 1,024  | 2,048            | 4,095     | 8 192     | 16,384     | 32,767    |
| Vstupně-výstupní operace za sekundu / disk       | Až 500              | Až 500              | Až 500              | Až 500 | Až 500              | Až 500              | Až 500             | Až 500              | 1 až 300              | Až 2 000              | Až 2 000              |
| Propustnost / disk | Až 60 MiB za sekundu | Až 60 MiB za sekundu | Až 60 MiB za sekundu | Až 60 MiB za sekundu | Až 60 MiB za sekundu | Až 60 MiB za sekundu | Až 60 MiB za sekundu | Až 60 MiB za sekundu| Až 300 MiB za sekundu |  Až 500 MiB/s | Až 500 MiB/s |

## <a name="standard-hdd"></a>Standard HDD

Azure standardní pevné disky poskytovat podporu spolehlivé, úsporné disků pro virtuální počítače běžící úlohu kterému latence nevadí. Také podporuje objekty BLOB, tabulky, fronty a soubory. Se standardním úložiště jsou data uložená na pevných disků (HDD). Při práci s virtuálními počítači, můžete použít standardní disky SSD a HDD, pro scénáře vývoje/testování a méně důležité úlohy. Storage úrovně Standard je k dispozici ve všech oblastech Azure.

### <a name="disk-size"></a>Velikost disku

Velikosti s hvězdičkou jsou aktuálně ve verzi preview.

| Disk typu Standard  | S4               | S6               | S10             | S15 | S20              | S30              | S40              | S50              | S60*              | S70*              | S80*              |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Velikost disku v GB          | 32             | 64             | 128            | 256  | 512            | 1,024    | 2,048     | 4,095    | 8 192     | 16,384     | 32,767     |
| Vstupně-výstupní operace za sekundu / disk       | Až 500              | Až 500              | Až 500              | Až 500 | Až 500              | Až 500              | Až 500             | Až 500              | 1 až 300              | Až 2 000              | Až 2 000              |
| Propustnost / disk | Až 60 MiB za sekundu | Až 60 MiB za sekundu | Až 60 MiB za sekundu | Až 60 MiB za sekundu | Až 60 MiB za sekundu | Až 60 MiB za sekundu | Až 60 MiB za sekundu | Až 60 MiB za sekundu| Až 300 MiB za sekundu | Až 500 MiB/s | Až 500 MiB/s |

## <a name="billing"></a>Fakturace

Při použití spravovaných disků, platí následující aspekty fakturace:

- Typ disku
- Velikost spravovaného disku
- Snímky
- Přenosy odchozích dat
- Počet transakcí

**Spravovaná velikost disku**: spravované disky se účtují na zřízená velikost. Azure maps zřízená velikost (zaokrouhleno) na nejbližší velikost nabízený disku. Podrobnosti o velikosti disků nabízejí najdete v předchozích tabulkách. Každý disk mapuje na nabídku velikost zřízeného disku podporované a odpovídajícím způsobem se účtuje. Například pokud jste zřídili 200 GB SSD na úrovni Standard, mapuje se na nabídky velikost disku E15 (256 GB). Za všechny zajišťovaným diskem se fakturuje po hodinách pomocí cenu za měsíc pro nabídku služby Premium Storage. Například pokud zřízené disk s E10 a odstraní ji po 20 hodin, bude se vám účtovat pro nabídky E10 nebo jeho poměrnou část 20 hodin. To je bez ohledu na velikost skutečných dat zapsaných na disk.

**Snímky**: Snímky se účtuje podle velikosti použít. Například pokud vytvoříte snímku spravovaného disku s zřízená kapacita 64 GiB a skutečné používaných dat velikosti 10 GB, snímku se mu fakturuje jenom používaných dat velikosti 10 GB.