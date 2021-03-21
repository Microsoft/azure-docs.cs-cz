---
title: Vyhrazená architektura fondu SQL (dříve SQL DW)
description: Přečtěte si, jak vyhrazený fond SQL (dřív SQL DW) ve službě Azure synapse Analytics kombinuje funkce distribuovaného zpracování dotazů s Azure Storage pro zajištění vysokého výkonu a škálovatelnosti.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 0e87451531750e502f67dc30e6fbd26c8c944d22
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98678589"
---
# <a name="dedicated-sql-pool-formerly-sql-dw-architecture-in-azure-synapse-analytics"></a>Vyhrazená architektura fondu SQL (dříve SQL DW) ve službě Azure synapse Analytics

Azure Synapse Analytics je analytická služba, která spojuje podnikové datové sklady s analýzou velkých objemů dat. Poskytuje vám volnost v dotazování na data podle vašich podmínek.

> [!NOTE]
>Prozkoumejte [dokumentaci ke službě Azure synapse Analytics](../overview-what-is.md).
>


> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="synapse-sql-architecture-components"></a>Komponenty architektury synapse SQL

[Vyhrazený fond SQL (dříve SQL DW)](sql-data-warehouse-overview-what-is.md) využívá architekturu škálování na více instancí k distribuci výpočetního zpracování dat napříč více uzly. Jednotka škálování je abstrakce výpočetního výkonu, který se označuje jako [jednotka datového skladu](what-is-a-data-warehouse-unit-dwu-cdwu.md). Výpočetní prostředky jsou oddělené od úložiště, což umožňuje škálovat výpočetní prostředky nezávisle na datech v systému.

![Vyhrazená architektura fondu SQL (dříve SQL DW)](./media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

Vyhrazený fond SQL (dříve SQL DW) používá architekturu založenou na uzlech. Aplikace připojují a vydávají příkazy T-SQL do řídicího uzlu. Řídicí uzel je hostitelem distribuovaného dotazovacího stroje, který optimalizuje dotazy pro paralelní zpracování, a poté předává operace do výpočetních uzlů, aby fungovaly paralelně.

Výpočetní uzly ukládají veškerá data uživatelů ve službě Azure Storage a spouští paralelní dotazy. DMS (Data Movement Service) je interní služba na úrovni systému, která podle potřeby přesunuje data mezi uzly, aby bylo možné spouštět dotazy paralelně a získat přesné výsledky.

S odděleným úložištěm a výpočetním prostředím může při použití vyhrazeného fondu SQL (dříve SQL DW) jedna:

- Bez ohledu na požadavky na úložiště se výpočetní výkon nezávisle mění.
- Zvětšete nebo zmenšete výpočetní výkon v rámci vyhrazeného fondu SQL (dříve SQL DW) bez přesouvání dat.
- pozastavit výpočetní kapacitu a zachovat neporušená data, zatímco platíte pouze za úložiště,
- obnovit výpočetní kapacitu za provozu.

### <a name="azure-storage"></a>Azure Storage

Vyhrazený fond SQL (dřív SQL DW) využívá Azure Storage k zabezpečení vašich uživatelských dat.  Vzhledem k tomu, že vaše data jsou uložená a spravovaná pomocí Azure Storage, pro vaši spotřebu úložiště se účtuje samostatně. Data se horizontálně dělené do **distribucí** za účelem optimalizace výkonu systému. Při definování tabulky můžete zvolit, který model horizontálního dělení se má pro distribuci dat použít. Jsou podporovány tyto horizontálního dělení vzory:

- Hodnoty hash
- Kruhové dotazování
- Replikace

### <a name="control-node"></a>Řídicí uzel

Mozkem této architektury je řídicí uzel. Jde o front-end, který komunikuje se všemi aplikacemi a připojeními. Distribuovaný dotazovací modul běží v uzlu Control pro optimalizaci a koordinaci paralelních dotazů. Když odešlete dotaz T-SQL, řídicí uzel ho transformuje na dotazy, které se spouštějí proti každé distribuci paralelně.

### <a name="compute-nodes"></a>Výpočetní uzly

Výpočetní uzly poskytují výpočetní výkon. Distribuce se mapují na výpočetní uzly pro zpracování. Když platíte za více výpočetních prostředků, distribuce se přemapují na dostupné výpočetní uzly. Počet výpočetních uzlů je rozsah od 1 do 60 a je určen úrovní služeb pro synapse SQL.

Každý výpočetní uzel má ID uzlu, které je viditelné v systémových zobrazeních. ID výpočetního uzlu můžete zobrazit tak, že vyhledáte sloupec node_id v systémových zobrazeních, jejichž názvy začínají na sys.pdw_nodes. Seznam těchto systémových zobrazení najdete v tématu [synapse SQL System views](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

### <a name="data-movement-service"></a>Data Movement Service

Služba pro přesun dat (DMS) je technologie pro přenos dat, která koordinuje pohyb dat mezi výpočetními uzly. Některé dotazy vyžadují přesun dat, aby se zajistilo, že paralelní dotazy vrátí přesné výsledky. Když se vyžaduje přesun dat, DMS zajistí, aby se správná data dostala do správného umístění.

## <a name="distributions"></a>Distribuce

Distribuce představuje základní jednotku úložiště a zpracování paralelních dotazů, které se spouští u distribuovaných dat. Když SQL synapse spustí dotaz, bude tato práce rozdělená na 60 menších dotazů, které běží paralelně.

Každý z těchto 60 dotazů běží v jedné z distribucí dat. Každý výpočetní uzel spravuje jednu nebo více distribucí 60. Vyhrazený fond SQL (dříve SQL DW) s maximálními výpočetními prostředky má jednu distribuci na výpočetní uzel. Vyhrazený fond SQL (dříve SQL DW) s minimálními výpočetními prostředky má všechny distribuce v jednom výpočetním uzlu.  

## <a name="hash-distributed-tables"></a>Distribuované zatřiďovací tabulky (distribuce hodnot hash)

Distribuovaná zatřiďovací tabulka (distribuce hodnot hash) může přinést nejvyšší výkon dotazů pro spojení a agregaci u velkých tabulek.

Pokud chcete data horizontálních oddílů do tabulky distribuované pomocí algoritmu hash, použije se funkce hash k deterministickému přiřazení každého řádku k jedné distribuci. V definici tabulky je jeden ze sloupců určený jako sloupec distribuce. Funkce hash používá hodnoty ve sloupci distribuce k přiřazení jednotlivých řádků k distribuci.

Následující diagram znázorňuje, jak se celá (nedistribuovaná tabulka) uloží jako tabulka distribuovaná pomocí algoritmu hash.

![Distribuovaná tabulka](./media/massively-parallel-processing-mpp-architecture/hash-distributed-table.png "Distribuovaná tabulka")  

- Každý řádek patří k jedné distribuci.  
- Deterministický algoritmus hash přiřadí jednotlivé řádky do jedné distribuce.  
- Počet řádků tabulky na distribuci se liší, jak je znázorněno v různých velikostech tabulek.

Pro výběr distribučního sloupce, jako je například odlišnosti, zkosení dat a typy dotazů, které jsou spuštěny v systému, dochází k důležitým informacím o výkonu.

## <a name="round-robin-distributed-tables"></a>Distribuované tabulky s kruhovým dotazováním

Tabulka kruhového dotazování je nejjednodušší tabulka k vytvoření a poskytuje rychlý výkon při použití jako pracovní tabulka pro zatížení.

Distribuovaná tabulka s kruhovým dotazováním distribuuje data v tabulce rovnoměrně bez jakékoli další optimalizace. Nejprve se vybere distribuce s náhodným rozsahem a potom se do mezipamětí řádků přiřadí distribuce postupně. Načtení dat do tabulky s kruhovým dotazováním je rychlé, ale výkon dotazu je často možné zlepšit pomocí distribuovaných zatřiďovacích tabuelk (distribucí hodnot hash). Spojení s tabulkami kruhového dotazování vyžadují rozmísení dat, což trvá další čas.

## <a name="replicated-tables"></a>Replikované tabulky

Replikovaná tabulka poskytuje nejrychlejší výkon dotazů u malých tabulek.

Replikovaná tabulka ukládá do mezipaměti úplnou kopii tabulky na každém výpočetním uzlu. V důsledku toho při replikaci tabulky odpadne nutnost před propojením nebo agregací přenášet data mezi výpočetními uzly. Replikované tabulky jsou nejlépe využitelné u malých tabulek. Dodatečné úložiště je potřeba a existuje další režie, která vzniká při psaní dat, což je nepraktické pro velké tabulky.  

Následující diagram znázorňuje replikovanou tabulku uloženou v mezipaměti při první distribuci na každém výpočetním uzlu.  

![Replikovaná tabulka](./media/massively-parallel-processing-mpp-architecture/replicated-table.png "Replikovaná tabulka")

## <a name="next-steps"></a>Další kroky

Teď, když víte o službě Azure synapse, se dozvíte, jak rychle [vytvořit vyhrazený fond SQL (dřív SQL DW)](create-data-warehouse-portal.md) a [načíst ukázková data](./load-data-from-azure-blob-storage-using-copy.md). Pokud s Azure začínáte, můžete využít [Glosář Azure](../../azure-glossary-cloud-terminology.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json), kde najdete potřebnou terminologii. Nebo se podívejte na některé z těchto dalších prostředků Azure synapse.  

- [Úspěšné zákaznické implementace](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
- [Blogy](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
- [Žádosti o funkce](https://feedback.azure.com/forums/307516-sql-data-warehouse)
- [Videa](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
- [Vytvoření lístku podpory](sql-data-warehouse-get-started-create-support-ticket.md)
- [Stránka s otázkou Microsoft Q&](/answers/topics/azure-synapse-analytics.html)
- [Fórum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw)
- [Twitter](https://twitter.com/hashtag/SQLDW)