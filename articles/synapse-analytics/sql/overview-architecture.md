---
title: Architektura Azure Synapse SQL
description: Zjistěte, jak Azure Synapse SQL kombinuje masivně paralelní zpracování (MPP) s Azure Storage pro dosažení vysokého výkonu a škálovatelnosti.
services: synapse-analytics
author: mlee3gsd
manager: rothja
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 570e84b3a545736aad6983c7f0d8c0f0296ca589
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431810"
---
# <a name="azure-synapse-sql-architecture"></a>Architektura Azure Synapse SQL 

Tento článek popisuje součásti architektury Synapse SQL.

[!INCLUDE [preview](../includes/note-preview.md)]

## <a name="synapse-sql-architecture-components"></a>Komponenty architektury SYNAPSE SQL

Synapse SQL využívá škálovatelnou architekturu k distribuci výpočetního zpracování dat mezi více uzly. Výpočetní výkon je oddělený od úložiště, což umožňuje škálovat výpočetní prostředky nezávisle na datech ve vašem systému. 

Pro fond SQL je jednotka škálování abstrakcí výpočetního výkonu, která se označuje jako [jednotka datového skladu](resource-consumption-models.md). 

Pro SQL na vyžádání, které jsou bez serveru, škálování se provádí automaticky tak, aby vyhovovaly požadavky na prostředky dotazu. Jako topologie změny v průběhu času přidáním, odebrání množin nebo převzetí služeb při selhání, přizpůsobí se změnám a zajišťuje, že dotaz má dostatek prostředků a dokončí úspěšně. Například obrázek níže ukazuje SQL na vyžádání s využitím 4 výpočetní uzly pro spuštění dotazu.

![Architektura Synapse SQL](./media//overview-architecture/sql-architecture.png)

Synapse SQL používá architekturu založenou na uzlu. Aplikace se připojují a vydávají příkazy T-SQL k řídicímu uzlu, což je jediný vstupní bod synapse SQL. 

Uzel SQL pool Control využívá modul MPP k optimalizaci dotazů pro paralelní zpracování a pak předá operace výpočetním uzlům, aby mohly pracovat paralelně. 

Uzel sql na vyžádání ovládacího prvku využívá modul zpracování distribuovaných dotazů (DQP) k optimalizaci a orchestraci distribuovaného provádění uživatelského dotazu rozdělením na menší dotazy, které budou provedeny na výpočetních uzlech. Každý malý dotaz se nazývá úkol a představuje distribuovanou prováděcí jednotku. Čte soubory z úložiště, spojuje výsledky z jiných úkolů, skupin nebo dat objednávek načtených z jiných úkolů. 

Výpočetní uzly ukládají veškerá data uživatelů ve službě Azure Storage a spouští paralelní dotazy. DMS (Data Movement Service) je interní služba na úrovni systému, která podle potřeby přesunuje data mezi uzly, aby bylo možné spouštět dotazy paralelně a získat přesné výsledky. 

S odděleným úložištěm a výpočetními prostředky můžete při použití Synapse SQL těžit z nezávislého nastavení výpočetního výkonu bez ohledu na potřeby úložiště. Pro sql na vyžádání škálování se provádí automaticky, zatímco pro fond SQL jeden může:

* Růst nebo zmenšit výpočetní výkon v rámci fondu SQL (datový sklad), bez přesunutí dat.
* pozastavit výpočetní kapacitu a zachovat neporušená data, zatímco platíte pouze za úložiště,
* Obnovit výpočetní kapacitu za provozu

## <a name="azure-storage"></a>Azure Storage

Synapse SQL využívá Azure Storage k zajištění bezpečnosti vašich uživatelských dat. Vzhledem k tomu, že vaše data jsou uložená a spravovaná službou Azure Storage, je za spotřebu úložiště účtován samostatný poplatek. 

SQL na vyžádání umožňuje dotazovat soubory v datovém jezeře způsobem jen pro čtení, zatímco fond SQL umožňuje ingestovat data také. Při požití dat do fondu SQL jsou data rozdělena do **distribucí,** aby se optimalizoval výkon systému. Můžete zvolit, který vzor rozdělení se má použít k distribuci dat při definování tabulky. Tyto vzory střepů jsou podporovány:

* Hodnota hash
* Kruhové dotazování.
* Replikace

## <a name="control-node"></a>Řídicí uzel

Řídicí uzel je mozek architektury. Jde o prvek front-end, který komunikuje se všemi aplikacemi a připojeními. 

Ve fondu SQL modul MPP běží na uzlu Control optimalizovat a koordinovat paralelní dotazy. Při odeslání dotazu T-SQL do fondu SQL, řídicí uzel transformuje do dotazů, které běží proti každé distribuci paralelně.

V SQL na vyžádání modul DQP běží na řídicím uzlu k optimalizaci a koordinaci distribuovaného provádění uživatelského dotazu rozdělením na menší dotazy, které budou provedeny na výpočetních uzlech. Také přiřadí sady souborů, které mají být zpracovány každý uzel.

## <a name="compute-nodes"></a>Výpočetní uzly

Výpočetní uzly poskytují výpočetní výkon. 

Ve fondu SQL distribuce mapovat výpočetní uzly pro zpracování. Když platíte za další výpočetní prostředky, fond přemapuje distribuce na dostupné výpočetní uzly. Počet výpočetních uzlů se pohybuje od 1 do 60 a je určen úrovní služeb pro fond SQL. Každý výpočetní uzel má ID uzlu, který je viditelný v systémových zobrazeních. ID výpočetního uzlu můžete zobrazit tak, že v systémových zobrazeních, jejichž názvy začínají sys.pdw_nodes, se zobrazí sloupec node_id. Seznam těchto systémových zobrazení naleznete v tématu [Zobrazení systému MPP](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=azure-sqldw-latest).

V SQL na vyžádání je každému výpočetnímu uzlu přiřazena úloha a sada souborů, na kterých se má úloha provádět. Úkol je distribuovaná jednotka provádění dotazu, která je ve skutečnosti součástí odeslaného uživatele dotazu. Automatické škálování je v platnosti, abyste se ujistili, že se k spuštění uživatelského dotazu použije dostatek výpočetních uzlů.

## <a name="data-movement-service"></a>Data Movement Service

Služba přesunu dat (DMS) je technologie přenosu dat ve fondu SQL, která koordinuje přesun dat mezi výpočetními uzly. Některé dotazy vyžadují přesun dat, aby bylo zajištěno, že paralelní dotazy vrátí přesné výsledky. Pokud je vyžadován přesun dat, DMS zajišťuje, že správná data se dostanou do správného umístění.

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="distributions"></a>Distribuce

Distribuce je základní jednotkou úložiště a zpracování pro paralelní dotazy, které běží na distribuovaných datech ve fondu SQL. Při spuštění dotazu fondu SQL je práce rozdělena do 60 menších dotazů, které běží paralelně. 

Každý z 60 menších dotazů běží na jednom z distribuce dat. Každý výpočetní uzel spravuje jednu nebo více z 60 distribucí. Fond SQL s maximálnívýpočetní prostředky má jednu distribuci na výpočetní uzel. Fond SQL s minimálnívýpočetní prostředky má všechny distribuce na jednom výpočetním uzlu. 

## <a name="hash-distributed-tables"></a>Distribuované zatřiďovací tabulky (distribuce hodnot hash)
Distribuovaná zatřiďovací tabulka (distribuce hodnot hash) může přinést nejvyšší výkon dotazů pro spojení a agregaci u velkých tabulek. 

Chcete-li rozdělit data do tabulky distribuované hash, fond SQL používá funkci hash deterministicky přiřadit každý řádek k jedné distribuci. V definici tabulky je jeden ze sloupců určený jako sloupec distribuce. Funkce hash používá hodnoty ve sloupci distribuce k přiřazení jednotlivých řádků k distribuci.

Následující diagram znázorňuje, jak se úplná (nedistribuovaná tabulka) uloží jako tabulka distribuovaná hash. 

![Distribuovaná tabulka](media//overview-architecture/hash-distributed-table.png "Distribuovaná tabulka") 

* Každý řádek patří k jedné distribuci. 
* Deterministický algoritmus hash přiřadí každý řádek k jedné distribuci. 
* Počet řádků tabulky na distribuci se liší podle různých velikostí tabulek.

Existují důležité informace o výkonu pro výběr sloupce distribuce, jako je například odlišnost, zkosení dat a typy dotazů, které běží v systému.

## <a name="round-robin-distributed-tables"></a>Distribuované tabulky s kruhovým dotazováním

Tabulka kruhového dotazování je nejjednodušší tabulka pro vytvoření a poskytuje rychlý výkon při použití jako pracovní tabulka pro zatížení.

Distribuovaná tabulka s kruhovým dotazováním distribuuje data v tabulce rovnoměrně bez jakékoli další optimalizace. Rozdělení je nejprve vybráno náhodně a pak vyrovnávací paměti řádků jsou přiřazeny k rozdělení postupně. Načtení dat do tabulky s kruhovým dotazováním je rychlé, ale výkon dotazu je často možné zlepšit pomocí distribuovaných zatřiďovacích tabuelk (distribucí hodnot hash). Spojení na stoly kruhovédotazování vyžadují přemísťování dat, což trvá déle.

## <a name="replicated-tables"></a>Replikované tabulky
Replikovaná tabulka poskytuje nejrychlejší výkon dotazů u malých tabulek.

Tabulka, která je replikována ukládá do mezipaměti úplnou kopii tabulky na každém výpočetním uzlu. V důsledku toho při replikaci tabulky odpadne nutnost před propojením nebo agregací přenášet data mezi výpočetními uzly. Replikované tabulky jsou nejlépe využitelné u malých tabulek. Je vyžadováno další úložiště a je další režie, která vzniká při zápisu dat, které činí velké tabulky nepraktické. 

Následující diagram ukazuje replikovanou tabulku, která je uložena v mezipaměti na první distribuci v každém výpočetním uzlu. 

![Replikovaná tabulka](media/overview-architecture/replicated-table.png "Replikovaná tabulka") 

## <a name="next-steps"></a>Další kroky

Teď, když víte něco o Synapse SQL, naučte se rychle [vytvořit fond SQL](../quickstart-create-sql-pool.md) a [načíst ukázková data](../sql-data-warehouse/sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md) (./sql-data-warehouse-load-sample-databases.md). Nebo začnete [používat SQL na vyžádání](../quickstart-sql-on-demand.md). Pokud s Azure začínáte, můžete využít [Glosář Azure](../../azure-glossary-cloud-terminology.md), kde najdete potřebnou terminologii. 
