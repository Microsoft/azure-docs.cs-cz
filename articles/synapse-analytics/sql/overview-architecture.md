---
title: Architektura Synapse SQL
description: Přečtěte si, jak Azure synapse SQL kombinuje výkonné paralelní zpracování (MPP) s Azure Storage pro zajištění vysokého výkonu a škálovatelnosti.
services: synapse-analytics
author: mlee3gsd
manager: rothja
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 9f2f3eee12bb8741f6d079f6f081a08f4e2db9b5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87046862"
---
# <a name="azure-synapse-sql-architecture"></a>Architektura SQL Azure synapse 

Tento článek popisuje součásti architektury nástroje synapse SQL.

[!INCLUDE [preview](../includes/note-preview.md)]

## <a name="synapse-sql-architecture-components"></a>Komponenty architektury synapse SQL

Synapse SQL využívá architekturu horizontálního navýšení kapacity k distribuci výpočetního zpracování dat napříč více uzly. Výpočetní prostředky jsou oddělené od úložiště, což umožňuje škálovat výpočetní prostředky nezávisle na datech v systému. 

V případě fondu SQL je jednotka škálování abstrakcí výpočetní výkon, který se označuje jako [jednotka datového skladu](resource-consumption-models.md). 

V případě SQL na vyžádání bez serveru je škálování provedeno automaticky, aby vyhovovalo požadavkům na prostředky dotazů. Změny topologie v průběhu času přidáním, odebráním uzlů nebo převzetí služeb při selhání se přizpůsobí změnám a zajišťují, že dotaz má dostatek prostředků a úspěšně dokončen. Následující obrázek například ukazuje na vyžádání SQL, který využívá 4 výpočetní uzly ke spuštění dotazu.

![Architektura Synapse SQL](./media//overview-architecture/sql-architecture.png)

Synapse SQL používá architekturu založenou na uzlech. Aplikace připojují a vydávají příkazy T-SQL do řídicího uzlu, který je jediným bodem záznamu pro synapse SQL. 

Uzel řízení fondu SQL používá modul MPP k optimalizaci dotazů pro paralelní zpracování a pak předává operace do výpočetních uzlů, aby fungovaly paralelně. 

Uzel řízení na vyžádání SQL využívá modul DQP (Distributed Query Processing) k optimalizaci a orchestraci distribuovaného spouštění dotazů uživatele rozdělením na menší dotazy, které se spustí na výpočetních uzlech. Každý malý dotaz se nazývá Task a představuje distribuovanou jednotku spuštění. Čte soubory z úložiště, spojuje výsledky z jiných úkolů, skupin nebo objednávek načtených z jiných úloh. 

Výpočetní uzly ukládají veškerá data uživatelů ve službě Azure Storage a spouští paralelní dotazy. DMS (Data Movement Service) je interní služba na úrovni systému, která podle potřeby přesunuje data mezi uzly, aby bylo možné spouštět dotazy paralelně a získat přesné výsledky. 

Díky oddělenému úložišti a výpočetnímu prostředí může při použití synapse SQL One těžit z nezávislé velikosti výpočetního výkonu bez ohledu na vaše požadavky na úložiště. Pro škálování SQL na vyžádání se provádí automaticky, zatímco pro fond SQL může:

* Zvětšete nebo zmenšete výpočetní výkon v rámci fondu SQL (datový sklad) bez přesouvání dat.
* pozastavit výpočetní kapacitu a zachovat neporušená data, zatímco platíte pouze za úložiště,
* obnovit výpočetní kapacitu za provozu.

## <a name="azure-storage"></a>Azure Storage

Synapse SQL využívá Azure Storage k zabezpečení vašich uživatelských dat. Vzhledem k tomu, že vaše data jsou uložená a spravovaná pomocí Azure Storage, pro vaši spotřebu úložiště se účtuje samostatně. 

SQL na vyžádání umožňuje dotazování souborů ve službě Data Lake způsobem jen pro čtení, zatímco fond SQL umožňuje také ingestovat data. Když se data ingestují do fondu SQL, data se horizontálně dělené do **distribucí** za účelem optimalizace výkonu systému. Můžete zvolit, který vzor horizontálního dělení se má použít k distribuci dat při definování tabulky. Jsou podporovány tyto horizontálního dělení vzory:

* Hodnoty hash
* Kruhové dotazování
* Replikace

## <a name="control-node"></a>Řídicí uzel

Mozkem této architektury je řídicí uzel. Jde o front-end, který komunikuje se všemi aplikacemi a připojeními. 

V rámci fondu SQL je modul MPP spuštěn v uzlu Control pro optimalizaci a koordinaci paralelních dotazů. Když odešlete dotaz T-SQL do fondu SQL, řídicí uzel ho transformuje na dotazy, které se spouštějí proti každé distribuci paralelně.

V SQL na vyžádání se modul DQP spouští na ovládacím uzlu za účelem optimalizace a koordinace distribuovaného spouštění dotazů uživatele rozdělením na menší dotazy, které se spustí na výpočetních uzlech. Také přiřadí sady souborů, které mají být zpracovány každým uzlem.

## <a name="compute-nodes"></a>Výpočetní uzly

Výpočetní uzly poskytují výpočetní výkon. 

Ve fondu SQL se distribuce mapují na výpočetní uzly ke zpracování. Když platíte za více výpočetních prostředků, fond přemapuje distribuci k dostupným výpočetním uzlům. Počet výpočetních uzlů je rozsah od 1 do 60 a je určen úrovní služeb pro fond SQL. Každý výpočetní uzel má ID uzlu, které je viditelné v systémových zobrazeních. ID výpočetního uzlu můžete zobrazit tak, že vyhledáte sloupec node_id v systémových zobrazeních, jejichž názvy začínají na sys.pdw_nodes. Seznam těchto systémových zobrazení najdete v tématu [Systémová zobrazení MPP](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=azure-sqldw-latest).

V SQL na vyžádání je každému výpočetnímu uzlu přiřazen úkol a sada souborů, na kterých se má úloha spustit. Úkol je jednotka provádění distribuovaného dotazu, která je ve skutečnosti součástí odeslaného uživatele dotazu. Automatické škálování je v platnosti, aby se zajistilo, že se k provádění dotazů uživatele využívají dost výpočetních uzlů.

## <a name="data-movement-service"></a>Data Movement Service

Služba pro přesun dat (DMS) je technologie pro přenos dat ve fondu SQL, která koordinuje pohyb dat mezi výpočetními uzly. Některé dotazy vyžadují přesun dat, aby se zajistilo, že paralelní dotazy vrátí přesné výsledky. Když se vyžaduje přesun dat, DMS zajistí, aby se správná data dostala do správného umístění.

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="distributions"></a>Distribuce

Distribuce je základní jednotkou úložiště a zpracování pro paralelní dotazy, které běží na distribuovaných datech ve fondu SQL. Když fond SQL spouští dotaz, bude práce rozdělena do 60 menších dotazů, které běží paralelně. 

Každý z těchto 60 dotazů běží v jedné z distribucí dat. Každý výpočetní uzel spravuje jednu nebo více distribucí 60. Fond SQL s maximálními výpočetními prostředky má jednu distribuci na výpočetní uzel. Fond SQL s minimálními výpočetními prostředky má všechny distribuce v jednom výpočetním uzlu. 

## <a name="hash-distributed-tables"></a>Distribuované zatřiďovací tabulky (distribuce hodnot hash)
Distribuovaná zatřiďovací tabulka (distribuce hodnot hash) může přinést nejvyšší výkon dotazů pro spojení a agregaci u velkých tabulek. 

Aby horizontálních oddílů data do tabulky distribuované pomocí algoritmu hash, používá fond SQL funkci hash k deterministickému přiřazení každého řádku k jedné distribuci. V definici tabulky je jeden ze sloupců určený jako sloupec distribuce. Funkce hash používá hodnoty ve sloupci distribuce k přiřazení jednotlivých řádků k distribuci.

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

Replikovaná tabulka ukládá do mezipaměti úplnou kopii tabulky na každém výpočetním uzlu. V důsledku toho při replikaci tabulky odpadne nutnost před propojením nebo agregací přenášet data mezi výpočetními uzly. Replikované tabulky jsou nejlépe využitelné u malých tabulek. Dodatečné úložiště je potřeba a existuje další režie, která vzniká při psaní dat, což je nepraktické pro velké tabulky. 

Následující diagram znázorňuje replikovanou tabulku uloženou v mezipaměti při první distribuci na každém výpočetním uzlu. 

![Replikovaná tabulka](media/overview-architecture/replicated-table.png "Replikovaná tabulka") 

## <a name="next-steps"></a>Další kroky

Teď, když víte o synapse SQL, zjistíte, jak rychle [vytvořit fond SQL](../quickstart-create-sql-pool-portal.md) a [načíst ukázková data](../sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) (./SQL-Data-Warehouse-Load-Sample-databases.MD). Nebo začnete [používat SQL na vyžádání](../quickstart-sql-on-demand.md). Pokud s Azure začínáte, můžete využít [Glosář Azure](../../azure-glossary-cloud-terminology.md), kde najdete potřebnou terminologii. 
