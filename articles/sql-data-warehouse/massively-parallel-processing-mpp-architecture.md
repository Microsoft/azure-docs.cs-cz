---
title: Architektura Azure synapse Analytics (dříve SQL DW)
description: Přečtěte si, jak Azure synapse Analytics (dřív SQL DW) kombinuje výkonné paralelní zpracování (MPP) s Azure Storage pro zajištění vysokého výkonu a škálovatelnosti.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 1d808210861d971b2915206e7be0fe9b955616c5
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720312"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-architecture"></a>Architektura Azure synapse Analytics (dříve SQL DW) 

Azure Synapse je neomezená analytická služba, která spojuje podnikové skladování dat a analýzy velkých objemů dat. Dává vám možnost dotazovat se na data podle toho, jak vám to vyhovuje, s využitím bezserverové architektury na vyžádání, nebo zřízených prostředků, a to ve velkém měřítku. Azure synapse přináší tyto dvě světů společně s jednotným prostředím pro ingestování, přípravu, správu a poskytování dat pro potřeby okamžitého BI a strojového učení.

 Azure synapse má čtyři součásti:
- Analýza SQL: kompletní analýzy založené na T-SQL 
    - Fond SQL (placený za DWU zřízený) – všeobecně dostupné
    - SQL na vyžádání (platba za TB zpracovaná) – (Preview)
- Spark: hluboce integrované Apache Spark (Preview) 
- Integrace dat: integrace hybridních dat (Preview)
- Studio: jednotné uživatelské prostředí.  (Preview)

> [!VIDEO https://www.youtube.com/embed/PlyQ8yOb8kc]

## <a name="sql-analytics-mpp-architecture-components"></a>Komponenty architektury SQL Analytics MPP

[SQL Analytics](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse) využívá architekturu pro horizontální navýšení kapacity k distribuci výpočetního zpracování dat napříč více uzly. Jednotka škálování je abstrakce výpočetního výkonu, který se označuje jako [jednotka datového skladu](what-is-a-data-warehouse-unit-dwu-cdwu.md). Výpočetní prostředky jsou oddělené od úložiště, což umožňuje škálovat výpočetní prostředky nezávisle na datech v systému.

![Architektura analýzy SQL](media/massively-parallel-processing-mpp-architecture/massively-parallel-processing-mpp-architecture.png)

SQL Analytics používá architekturu založenou na uzlech. Aplikace připojují a vydávají příkazy T-SQL do řídicího uzlu, který je jediným bodem záznamu pro SQL Analytics. Řídicí uzel spouští modul MPP, který optimalizuje dotazy pro paralelní zpracování a pak předává operace do výpočetních uzlů, aby fungovaly paralelně. 

Výpočetní uzly ukládají všechna uživatelská data v Azure Storage a spouštějí paralelní dotazy. Služba pro přesun dat (DMS) je interní služba na úrovni systému, která přesouvá data mezi uzly, jak je to nutné pro paralelní spouštění dotazů a vracení přesných výsledků. 

S odděleným úložištěm a výpočetním prostředím může při použití SQL Analytics jednu z těchto akcí:

* Bez ohledu na požadavky na úložiště se výpočetní výkon nezávisle mění.
* Zvětšete nebo zmenšete výpočetní výkon v rámci fondu SQL (datový sklad) bez přesouvání dat.
* Pozastaví výpočetní kapacitu a zachová data beze změny, takže platíte jenom za úložiště.
* Obnovit výpočetní kapacitu za provozu

### <a name="azure-storage"></a>Azure Storage

SQL Analytics využívá Azure Storage k zabezpečení vašich uživatelských dat.  Vzhledem k tomu, že vaše data jsou uložená a spravovaná pomocí Azure Storage, pro vaši spotřebu úložiště se účtuje samostatně. Data se horizontálně dělené do **distribucí** za účelem optimalizace výkonu systému. Můžete zvolit, který vzor horizontálního dělení se má použít k distribuci dat při definování tabulky. Jsou podporovány tyto horizontálního dělení vzory:

* Hodnota hash
* Kruhové dotazování.
* Replikace

### <a name="control-node"></a>Řídicí uzel

Řídicí uzel je mozek architektury. Jde o prvek front-end, který komunikuje se všemi aplikacemi a připojeními. Modul MPP běží v uzlu Control pro optimalizaci a koordinaci paralelních dotazů. Když odešlete dotaz T-SQL do analýzy SQL, řídicí uzel ho transformuje na dotazy, které se spouštějí proti každé distribuci paralelně.

### <a name="compute-nodes"></a>Výpočetní uzly

Výpočetní uzly poskytují výpočetní výkon. Distribuce se mapují na výpočetní uzly pro zpracování. Když platíte za další výpočetní prostředky, SQL Analytics znovu mapuje distribuce na dostupné výpočetní uzly. Počet výpočetních uzlů je v rozsahu od 1 do 60 a určuje se podle úrovně služby pro SQL Analytics.

Každý výpočetní uzel má ID uzlu, které je viditelné v systémových zobrazeních. ID výpočetního uzlu můžete zobrazit tak, že vyhledáte sloupec node_id v systémových zobrazeních, jejichž názvy začínají řetězcem sys. pdw_nodes. Seznam těchto systémových zobrazení najdete v tématu [Systémová zobrazení MPP](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views?view=aps-pdw-2016-au7).

### <a name="data-movement-service"></a>Služba pro přesun dat
Služba pro přesun dat (DMS) je technologie pro přenos dat, která koordinuje pohyb dat mezi výpočetními uzly. Některé dotazy vyžadují přesun dat, aby se zajistilo, že paralelní dotazy vrátí přesné výsledky. Když se vyžaduje přesun dat, DMS zajistí, aby se správná data dostala do správného umístění. 

## <a name="distributions"></a>Distribuce

Distribuce je základní jednotkou úložiště a zpracování pro paralelní dotazy, které běží na distribuovaných datech. Když SQL Analytics spustí dotaz, bude práce rozdělena do 60 menších dotazů, které běží paralelně. 

Každý z těchto 60 dotazů běží v jedné z distribucí dat. Každý výpočetní uzel spravuje jednu nebo více distribucí 60. Fond SQL s maximálními výpočetními prostředky má jednu distribuci na výpočetní uzel. Fond SQL s minimálními výpočetními prostředky má všechny distribuce v jednom výpočetním uzlu.  

## <a name="hash-distributed-tables"></a>Hodnoty hash-distribuované tabulky
Distribuovaná tabulka hash může poskytovat nejvyšší výkon dotazu pro spojení a agregace ve velkých tabulkách. 

K horizontálních oddílů dat do tabulky distribuované pomocí algoritmu hash používá SQL Analytics funkci hash k deterministickému přiřazení každého řádku k jedné distribuci. V definici tabulky je jeden ze sloupců označený jako distribuční sloupec. Funkce hash používá hodnoty ve sloupci distribuce k přiřazení každého řádku k distribuci.

Následující diagram znázorňuje, jak se celá (nedistribuovaná tabulka) uloží jako tabulka distribuovaná pomocí algoritmu hash. 

![Distribuovaná tabulka](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "Distribuovaná tabulka")  

* Každý řádek patří k jedné distribuci.  
* Deterministický algoritmus hash přiřadí jednotlivé řádky do jedné distribuce.  
* Počet řádků tabulky na distribuci se liší, jak je znázorněno v různých velikostech tabulek.

Pro výběr distribučního sloupce, jako je například odlišnosti, zkosení dat a typy dotazů, které jsou spuštěny v systému, dochází k důležitým informacím o výkonu.

## <a name="round-robin-distributed-tables"></a>Distribuované tabulky kruhového dotazování
Tabulka kruhového dotazování je nejjednodušší tabulka k vytvoření a poskytuje rychlý výkon při použití jako pracovní tabulka pro zatížení.

Distribuovaná tabulka kruhového dotazování rovnoměrně distribuuje data napříč tabulkou, ale bez další optimalizace. Nejprve se vybere distribuce s náhodným rozsahem a potom se do mezipamětí řádků přiřadí distribuce postupně. Data je možné rychle načíst do tabulky kruhového dotazování, ale výkon dotazů je často lepší díky distribuovaným tabulkám hash. Spojení s tabulkami kruhového dotazování vyžadují rozmísení dat, což trvá další čas.


## <a name="replicated-tables"></a>Replikované tabulky
Replikovaná tabulka poskytuje nejrychlejší výkon dotazů pro malé tabulky.

Replikovaná tabulka ukládá do mezipaměti úplnou kopii tabulky na každém výpočetním uzlu. V důsledku toho replikování tabulky odstraní nutnost přenášet data mezi výpočetními uzly před spojením nebo agregací. Replikované tabulky se nejlépe využívají v malých tabulkách. Dodatečné úložiště je potřeba a existuje další režie, která vzniká při psaní dat, což je nepraktické pro velké tabulky.  

Následující diagram znázorňuje replikovanou tabulku uloženou v mezipaměti při první distribuci na každém výpočetním uzlu.  

![Replikovaná tabulka](media/sql-data-warehouse-distributed-data/replicated-table.png "Replikovaná tabulka") 

## <a name="next-steps"></a>Další kroky
Teď, když víte o Azure synapse, se dozvíte, jak rychle [vytvořit fond SQL](./sql-data-warehouse-get-started-provision.md) a [načíst ukázková data](./sql-data-warehouse-load-sample-databases.md). Pokud s Azure začínáte, můžete využít [Glosář Azure](../azure-glossary-cloud-terminology.md), kde najdete potřebnou terminologii. Nebo se podívejte na některé z těchto dalších prostředků Azure synapse.  

* [Úspěšné zákaznické implementace](https://azure.microsoft.com/case-studies/?service=sql-data-warehouse)
* [Blogy](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/)
* [Žádosti o funkce](https://feedback.azure.com/forums/307516-sql-data-warehouse)
* [Videa](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse)
* [Vytvoření lístku podpory](./sql-data-warehouse-get-started-create-support-ticket.md)
* [Fórum MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureSQLDataWarehouse)
* [Fórum Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw)
* [Twitter](https://twitter.com/hashtag/SQLDW)

