---
title: Architektura Synapse SQL
description: Přečtěte si, jak Azure synapse SQL kombinuje funkce distribuovaného zpracování dotazů pomocí Azure Storage pro zajištění vysokého výkonu a škálovatelnosti.
services: synapse-analytics
author: mlee3gsd
manager: rothja
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 28940272d39a08d790fe2cd913df808b02e7f426
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102441886"
---
# <a name="azure-synapse-sql-architecture"></a>Architektura Azure Synapse SQL 

Tento článek popisuje součásti architektury nástroje synapse SQL.

## <a name="synapse-sql-architecture-components"></a>Komponenty architektury synapse SQL

Synapse SQL využívá architekturu horizontálního navýšení kapacity k distribuci výpočetního zpracování dat napříč více uzly. Výpočetní prostředky jsou oddělené od úložiště, což umožňuje škálovat výpočetní prostředky nezávisle na datech v systému. 

V případě vyhrazeného fondu SQL je jednotka škálování abstrakcí výpočetní výkon, který se označuje jako [jednotka datového skladu](resource-consumption-models.md). 

U bezserverových fondů SQL se škálování bez serveru provádí automaticky, aby vyhovovalo požadavkům na prostředky dotazů. Změny topologie v průběhu času přidáním, odebráním uzlů nebo převzetí služeb při selhání se přizpůsobí změnám a zajišťují, že dotaz má dostatek prostředků a úspěšně dokončen. Následující obrázek například ukazuje fond SQL bez serveru s využitím 4 výpočetních uzlů pro spuštění dotazu.

![Architektura Synapse SQL](./media//overview-architecture/sql-architecture.png)

Synapse SQL používá architekturu založenou na uzlech. Aplikace připojují a vydávají příkazy T-SQL do řídicího uzlu, který je jediným bodem záznamu pro synapse SQL. 

Řídicí uzel SQL Azure synapse využívá distribuovaný modul dotazů k optimalizaci dotazů na paralelní zpracování a pak předává operace do výpočetních uzlů, aby fungovaly paralelně. 

Uzel řízení fondu SQL bez serveru využívá modul DQP (Distributed Query Processing) k optimalizaci a orchestraci distribuovaného spouštění dotazů uživatele rozdělením na menší dotazy, které se spustí na výpočetních uzlech. Každý malý dotaz se nazývá Task a představuje distribuovanou jednotku spuštění. Čte soubory ze služby Storage, spojuje výsledky jiných úkolů, skupin nebo objednávek načtených z jiných úloh. 

Výpočetní uzly ukládají veškerá data uživatelů ve službě Azure Storage a spouští paralelní dotazy. DMS (Data Movement Service) je interní služba na úrovni systému, která podle potřeby přesunuje data mezi uzly, aby bylo možné spouštět dotazy paralelně a získat přesné výsledky. 

Díky oddělenému úložišti a výpočetnímu prostředí může při použití synapse SQL One těžit z nezávislé velikosti výpočetního výkonu bez ohledu na vaše požadavky na úložiště. U vyhrazeného fondu SQL serveru se provádí automaticky, ale pro vyhrazený fond SQL může to:

* Zvětšete nebo zmenšete výpočetní výkon v rámci vyhrazeného fondu SQL bez přesouvání dat.
* pozastavit výpočetní kapacitu a zachovat neporušená data, zatímco platíte pouze za úložiště,
* obnovit výpočetní kapacitu za provozu.

## <a name="azure-storage"></a>Azure Storage

Synapse SQL využívá Azure Storage k zabezpečení vašich uživatelských dat. Vzhledem k tomu, že vaše data jsou uložená a spravovaná pomocí Azure Storage, pro vaši spotřebu úložiště se účtuje samostatně. 

Fond SQL bez serveru umožňuje dotazování souborů Data Lake, zatímco vyhrazený fond SQL umožňuje dotazování a ingestování dat ze souborů Data Lake. Když se data ingestují do vyhrazeného fondu SQL, data se horizontálně dělené do **distribucí** za účelem optimalizace výkonu systému. Při definování tabulky můžete zvolit, který model horizontálního dělení se má pro distribuci dat použít. Jsou podporovány tyto horizontálního dělení vzory:

* Hodnoty hash
* Kruhové dotazování
* Replikace

## <a name="control-node"></a>Řídicí uzel

Mozkem této architektury je řídicí uzel. Jde o front-end, který komunikuje se všemi aplikacemi a připojeními. 

V synapse SQL je distribuovaný dotazovací modul spuštěný v uzlu Control pro optimalizaci a koordinaci paralelních dotazů. Při odeslání dotazu T-SQL do vyhrazeného fondu SQL ho řídicí uzel transformuje na dotazy, které se spouštějí souběžně s každou distribucí.

Ve fondu SQL bez serveru se modul DQP spouští na řídicím uzlu za účelem optimalizace a koordinace distribuovaného spouštění dotazů uživatele rozdělením na menší dotazy, které se spustí na výpočetních uzlech. Také přiřadí sady souborů, které mají být zpracovány každým uzlem.

## <a name="compute-nodes"></a>Výpočetní uzly

Výpočetní uzly poskytují výpočetní výkon. 

Ve vyhrazeném fondu SQL se distribuce mapují na výpočetní uzly ke zpracování. Když platíte za více výpočetních prostředků, fond přemapuje distribuci k dostupným výpočetním uzlům. Počet výpočetních uzlů je rozsah od 1 do 60 a je určen úrovní služeb pro vyhrazený fond SQL. Každý výpočetní uzel má ID uzlu, které je viditelné v systémových zobrazeních. ID výpočetního uzlu můžete zobrazit tak, že vyhledáte sloupec node_id v systémových zobrazeních, jejichž názvy začínají na sys.pdw_nodes. Seznam těchto systémových zobrazení najdete v tématu [synapse SQL System views](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=azure-sqldw-latest&preserve-view=true).

V neserverovém fondu SQL je každému výpočetnímu uzlu přiřazen úkol a sada souborů pro spuštění úlohy. Úkol je jednotka provádění distribuovaného dotazu, která je ve skutečnosti součástí odeslaného uživatele dotazu. Automatické škálování je v platnosti, aby se zajistilo, že se k provádění dotazů uživatele využívají dost výpočetních uzlů.

## <a name="data-movement-service"></a>Data Movement Service

Služba pro přesun dat (DMS) je technologie pro přenos dat ve vyhrazeném fondu SQL, která koordinuje pohyb dat mezi výpočetními uzly. Některé dotazy vyžadují přesun dat, aby se zajistilo, že paralelní dotazy vrátí přesné výsledky. Když se vyžaduje přesun dat, DMS zajistí, aby se správná data dostala do správného umístění.

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="distributions"></a>Distribuce

Distribuce je základní jednotkou úložiště a zpracování pro paralelní dotazy, které běží na distribuovaných datech ve vyhrazeném fondu SQL. Když vyhrazený fond SQL spouští dotaz, je tato práce rozdělená na 60 menších dotazů, které běží paralelně. 

Každý z těchto 60 dotazů běží v jedné z distribucí dat. Každý výpočetní uzel spravuje jednu nebo více distribucí 60. Vyhrazený fond SQL s maximálními výpočetními prostředky má jednu distribuci na výpočetní uzel. Vyhrazený fond SQL s minimálními výpočetními prostředky má všechny distribuce v jednom výpočetním uzlu. 

## <a name="hash-distributed-tables"></a>Distribuované zatřiďovací tabulky (distribuce hodnot hash)
Distribuovaná zatřiďovací tabulka (distribuce hodnot hash) může přinést nejvyšší výkon dotazů pro spojení a agregaci u velkých tabulek. 

Aby horizontálních oddílů data do tabulky distribuované pomocí algoritmu hash, používá vyhrazený fond SQL funkci hash k deterministickému přiřazení každého řádku k jedné distribuci. V definici tabulky je jeden ze sloupců určený jako sloupec distribuce. Funkce hash používá hodnoty ve sloupci distribuce k přiřazení jednotlivých řádků k distribuci.

Následující diagram znázorňuje, jak se celá (nedistribuovaná tabulka) uloží jako tabulka distribuovaná pomocí algoritmu hash. 

![Distribuovaná tabulka](media//overview-architecture/hash-distributed-table.png "Distribuovaná tabulka") 

* Každý řádek patří k jedné distribuci. 
* Deterministický algoritmus hash přiřadí jednotlivé řádky do jedné distribuce. 
* Počet řádků tabulky na distribuci se liší, jak je znázorněno v různých velikostech tabulek.

Pro výběr distribučního sloupce, jako je například odlišnosti, zkosení dat a typy dotazů, které jsou spuštěny v systému, dochází k důležitým informacím o výkonu.

## <a name="round-robin-distributed-tables"></a>Distribuované tabulky s kruhovým dotazováním

Tabulka kruhového dotazování je nejjednodušší tabulka k vytvoření a poskytuje rychlý výkon při použití jako pracovní tabulka pro zatížení.

Distribuovaná tabulka s kruhovým dotazováním distribuuje data v tabulce rovnoměrně bez jakékoli další optimalizace. Nejprve se vybere distribuce s náhodným rozsahem a potom se do mezipamětí řádků přiřadí distribuce postupně. Načtení dat do tabulky s kruhovým dotazováním je rychlé, ale výkon dotazu je často možné zlepšit pomocí distribuovaných zatřiďovacích tabuelk (distribucí hodnot hash). Spojení s tabulkami kruhového dotazování vyžadují rozmísení dat, což trvá další čas.

## <a name="replicated-tables"></a>Replikované tabulky
Replikovaná tabulka poskytuje nejrychlejší výkon dotazů u malých tabulek.

Replikovaná tabulka ukládá do mezipaměti úplnou kopii tabulky na každém výpočetním uzlu. Replikování tabulky proto odstraní nutnost přenosu dat mezi výpočetními uzly před spojením nebo agregací. Replikované tabulky jsou nejlépe využitelné u malých tabulek. Dodatečné úložiště je potřeba a existuje další režie, která vzniká při psaní dat, což je nepraktické pro velké tabulky. 

Následující diagram znázorňuje replikovanou tabulku uloženou v mezipaměti při první distribuci na každém výpočetním uzlu. 

![Replikovaná tabulka](media/overview-architecture/replicated-table.png "Replikovaná tabulka") 

## <a name="next-steps"></a>Další kroky

Teď, když víte o synapse SQL, se dozvíte, jak rychle [vytvořit vyhrazený fond SQL](../quickstart-create-sql-pool-portal.md) a [načíst ukázková data](../sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) (./SQL-Data-Warehouse-Load-Sample-databases.MD). Nebo začněte [používat fond SQL bez serveru](../quickstart-sql-on-demand.md). Pokud s Azure začínáte, můžete využít [Glosář Azure](../../azure-glossary-cloud-terminology.md), kde najdete potřebnou terminologii. 
