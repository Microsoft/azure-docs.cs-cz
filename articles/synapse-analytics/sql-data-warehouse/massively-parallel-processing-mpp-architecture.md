---
title: Architektura Azure Synapse Analytics (dříve SQL DW)
description: Zjistěte, jak Azure Synapse Analytics (dříve SQL DW) kombinuje masivně paralelní zpracování (MPP) s Azure Storage pro dosažení vysokého výkonu a škálovatelnosti.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: a42ec523bb1f77c48f7382283a52565c9c9273b6
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584503"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-architecture"></a>Architektura Azure Synapse Analytics (dříve SQL DW) 

Azure Synapse je neomezená analytická služba, která spojuje podnikové skladování dat a analýzy velkých objemů dat. Dává vám možnost dotazovat se na data podle toho, jak vám to vyhovuje, s využitím bezserverové architektury na vyžádání, nebo zřízených prostředků, a to ve velkém měřítku. Azure Synapse spojuje tyto dva světy s jednotným prostředím pro ingestování, přípravu, správu a poskytování dat pro okamžité potřeby BI a strojového učení.

 Azure Synapse má čtyři součásti:
- Synapse SQL: Kompletní analýza založená na T-SQL 
    - Fond SQL (platba za dwu zřízené) – obecně dostupné
    - SQL na vyžádání (platba za zpracované TB) – (náhled)
- Jiskra: Hluboce integrovaná Apache Spark (náhled)
- Integrace dat: Hybridní integrace dat (náhled)
- Studio: jednotné uživatelské prostředí.  (Preview)

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="synapse-sql-mpp-architecture-components"></a>Komponenty architektury Synapse SQL MPP

[Synapse SQL](sql-data-warehouse-overview-what-is.md#synapse-sql-pool-in-azure-synapse) využívá horizontální navýšení kapacity architektury k distribuci výpočetnízpracování dat mezi více uzlů. Jednotka měřítka je abstrakce výpočetního výkonu, která se označuje jako [jednotka datového skladu](what-is-a-data-warehouse-unit-dwu-cdwu.md). Výpočetní výkon je oddělený od úložiště, což umožňuje škálovat výpočetní prostředky nezávisle na datech ve vašem systému.

![Architektura Synapse SQL](./media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

Synapse SQL používá architekturu založenou na uzlu. Aplikace se připojují a vydávají příkazy T-SQL k řídicímu uzlu, což je jediný vstupní bod fondu Synapse SQL. Uzel Control spustí modul MPP, který optimalizuje dotazy pro paralelní zpracování a pak předá operace výpočetním uzlům, aby mohly pracovat paralelně. 

Výpočetní uzly ukládají veškerá data uživatelů ve službě Azure Storage a spouští paralelní dotazy. DMS (Data Movement Service) je interní služba na úrovni systému, která podle potřeby přesunuje data mezi uzly, aby bylo možné spouštět dotazy paralelně a získat přesné výsledky. 

S odděleným úložištěm a výpočetními prostředky lze při použití fondu Synapse SQL:

* Nezávisle velikost výpočetního výkonu bez ohledu na potřeby úložiště.
* Růst nebo zmenšit výpočetní výkon v rámci fondu SQL (datový sklad), bez přesunutí dat.
* pozastavit výpočetní kapacitu a zachovat neporušená data, zatímco platíte pouze za úložiště,
* Obnovit výpočetní kapacitu za provozu

### <a name="azure-storage"></a>Azure Storage

Synapse SQL využívá Azure Storage k zajištění bezpečnosti vašich uživatelských dat.  Vzhledem k tomu, že vaše data jsou uložená a spravovaná službou Azure Storage, je za spotřebu úložiště účtován samostatný poplatek. Data jsou rozdělena do **distribucí** pro optimalizaci výkonu systému. Můžete zvolit, který vzor rozdělení se má použít k distribuci dat při definování tabulky. Tyto vzory střepů jsou podporovány:

* Hodnota hash
* Kruhové dotazování.
* Replikace

### <a name="control-node"></a>Řídicí uzel

Řídicí uzel je mozek architektury. Jde o prvek front-end, který komunikuje se všemi aplikacemi a připojeními. Modul MPP běží na řídicím uzlu, aby se zajistila optimalizace a koordinace paralelních dotazů. Při odeslání dotazu T-SQL, řídicí uzel transformuje do dotazů, které běží proti každé distribuci paralelně.

### <a name="compute-nodes"></a>Výpočetní uzly

Výpočetní uzly poskytují výpočetní výkon. Distribuce mapovat výpočetní uzly pro zpracování. Při platbě za další výpočetní prostředky se distribuce přemísťují na dostupné výpočetní uzly. Počet výpočetních uzlů se pohybuje od 1 do 60 a je určen úrovní služby pro Synapse SQL.

Každý výpočetní uzel má ID uzlu, který je viditelný v systémových zobrazeních. ID výpočetního uzlu můžete zobrazit tak, že v systémových zobrazeních, jejichž názvy začínají sys.pdw_nodes, se zobrazí sloupec node_id. Seznam těchto systémových zobrazení naleznete v tématu [Zobrazení systému MPP](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=aps-pdw-2016-au7).

### <a name="data-movement-service"></a>Data Movement Service

Služba přesunu dat (DMS) je technologie přenosu dat, která koordinuje pohyb dat mezi výpočetními uzly. Některé dotazy vyžadují přesun dat, aby bylo zajištěno, že paralelní dotazy vrátí přesné výsledky. Pokud je vyžadován přesun dat, DMS zajišťuje, že správná data se dostanou do správného umístění. 

## <a name="distributions"></a>Distribuce

Distribuce představuje základní jednotku úložiště a zpracování paralelních dotazů, které se spouští u distribuovaných dat. Při spuštění dotazu je práce rozdělena na 60 menších dotazů, které běží paralelně. 

Každý z 60 menších dotazů běží na jednom z distribuce dat. Každý výpočetní uzel spravuje jednu nebo více z 60 distribucí. Fond SQL s maximálnívýpočetní prostředky má jednu distribuci na výpočetní uzel. Fond SQL s minimálnívýpočetní prostředky má všechny distribuce na jednom výpočetním uzlu.  

## <a name="hash-distributed-tables"></a>Distribuované zatřiďovací tabulky (distribuce hodnot hash)

Distribuovaná zatřiďovací tabulka (distribuce hodnot hash) může přinést nejvyšší výkon dotazů pro spojení a agregaci u velkých tabulek. 

Chcete-li rozdělit data do tabulky distribuované hash, hash funkce se používá k deterministicky přiřadit každý řádek k jedné distribuci. V definici tabulky je jeden ze sloupců určený jako sloupec distribuce. Funkce hash používá hodnoty ve sloupci distribuce k přiřazení jednotlivých řádků k distribuci.

Následující diagram znázorňuje, jak se úplná (nedistribuovaná tabulka) uloží jako tabulka distribuovaná hash. 

![Distribuovaná tabulka](./media/massively-parallel-processing-mpp-architecture/hash-distributed-table.png "Distribuovaná tabulka")  

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

![Replikovaná tabulka](./media/massively-parallel-processing-mpp-architecture/replicated-table.png "Replikovaná tabulka") 

## <a name="next-steps"></a>Další kroky

Teď, když víte něco o Azure Synapse, zjistěte, jak rychle [vytvořit fond SQL](create-data-warehouse-portal.md) a [načíst ukázková data](load-data-from-azure-blob-storage-using-polybase.md). Pokud s Azure začínáte, můžete využít [Glosář Azure](../../azure-glossary-cloud-terminology.md), kde najdete potřebnou terminologii. Nebo se podívejte na některé z těchto dalších prostředků Azure Synapse.  
