---
title: Azure SQL Data Warehouse zpráva k vydání verze. září 2018 | Dokumentace Microsoftu
description: Zpráva k vydání verze pro Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 10/08/2018
ms.author: mausher
ms.reviewer: twounder
ms.openlocfilehash: f8073fd746bc02c23efab914c45c61830942a677
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2018
ms.locfileid: "51577953"
---
# <a name="whats-new-in-azure-sql-data-warehouse-september-2018"></a>Co je nového ve službě Azure SQL Data Warehouse? Září 2018
Azure SQL Data Warehouse neustále obdrží vylepšení. Tento článek popisuje nové funkce a změny, které byly zavedeny v září 2018.

## <a name="new-lower-entry-point-for-sql-data-warehouse-gen2"></a>Nové nižší vstupní bod pro SQL Data Warehouse Gen2
V dubnu 2018 [Microsoft annouced](https://azure.microsoft.com/blog/turbocharge-cloud-analytics-with-azure-sql-data-warehouse/) Azure SQL Data Warehouse Gen2, která nabízí 5 × výkon, 5 x rozsah výpočetních prostředků, 4 x souběžnosti s neomezeným úložištěm. Jak je uvedeno v [datového skladu v cloudu srovnávací test](https://gigaom.com/report/data-warehouse-in-the-cloud-benchmark/) ve společnosti Gigaom, SQL Data Warehouse Gen2 **lepší výkon než Amazon Redshift 42 %**.

Gen2 je teď obecně k dispozici na nižší vstupní bod z DWU500c díky tomu můžete řídit menší velikosti datového skladu nebo vývojové/testovací prostředí se všemi nejnovější vylepšení služby. Nový vstupní bod uchovává všechny funkce Gen2 včetně [adaptivní ukládání do mezipaměti](https://azure.microsoft.com/blog/adaptive-caching-powers-azure-sql-data-warehouse-performance-gains/), [osvětlení rychle přesouvání dat](https://azure.microsoft.com/blog/lightning-fast-query-performance-with-azure-sql-data-warehouse/)a jeho podpora [skladu dat v reálném čase](https://azure.microsoft.com/blog/enabling-real-time-data-warehousing-with-azure-sql-data-warehouse/).

## <a name="improved-availability-with-query-restartability"></a>Lepší dostupnost s restartability dotazu
Při provádění dotazu, může dojít, libovolný počet problémů, které může způsobit selhání dotazu. K výpadku sítě, selhání hardwaru nebo jiných odpojení může způsobit narušení. SQL Data Warehouse teď podporuje restartability dotazu pro krok nebo příkaz úrovni dotazů SELECT. 

Pomocí dotazu Restartability dotaz, který je v letu, který naruší se z důvodu chyby automaticky restartuje. V závislosti na počtu kroků, tvar dotazu, nebo pokud dotazu bylo zastaveno během provádění, modul SQL Data Warehouse restartuje celý dotaz bude pokračovat od posledního kroku dokončené dotazu. Z hlediska koncovým uživatelem právě dokončení dotazu. 

## <a name="maintenance-scheduling-preview"></a>Údržba plánování (Preview)
Azure SQL Data Warehouse údržby plánování je teď ve verzi preview. Tato nová funkce bez problémů integruje plánované údržby oznámení o stavu služby, zkontrolujte monitorování stavu prostředků a plánování služby Azure SQL Data Warehouse údržby. Plánování údržby umožňuje naplánovat časový interval, kdy je vhodné pro příjem nových funkcí, upgrady a opravy.

Plánování údržby využívá Azure Monitor a umožňuje zákazníkům určíte, jak chtějí informováni o nadcházejícím události údržby a které automatizované toky aktivované ke správě prostoje a minimalizovat dopad na jejich operace. Oznámení můžete zahrnout e-mailu nebo text. 

## <a name="wide-row-support-in-polybase"></a>Podpora široké řádek v Polybase
Při načítání dat do SQL Data Warehouse, je použití obecné pokyny [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/design-elt-data-loading#options-for-loading-with-polybase) podporu pro paralelní načítání dat je. Tato verze umožňuje podporu pro širší sloupce z 32 kB na 1MB – díky tomu umožňuje načíst tabulky s velikost široké řádku. Podporu pro širokou řádky zjednodušuje načítání pro tabulky s širokou řádky dat.

> [!Note]
> Celkový počet řádků velikost nesmí překročit 1 MB. velikost.

## <a name="additional-language-support"></a>Podpora dalších jazyků
Tato verze přináší podporu pro následující elementy jazyka T-SQL:

### <a name="stringsplit"></a>STRING_SPLIT
[STRING_SPLIT](https://docs.microsoft.com/sql/t-sql/functions/string-split-transact-sql) funkce rozdělí řetězec znaků pomocí zadaného oddělovače. Je užitečné ve scénářích, kde sloupec může mít víc hodnot pro analýzu a vložit do jiné tabulky pro načítání dat STRING_SPLIT.

#### <a name="example"></a>Příklad:
```sql
DECLARE @tags NVARCHAR(400) = 'clothing,road,,touring,bike';

SELECT
    value
FROM
    STRING_SPLIT(@tags, ',')
WHERE
    RTRIM(value) <> '';
```

### <a name="compressdecompress-functions"></a>Funkce komprese/DECOMPRESS
[KOMPRIMOVAT](https://docs.microsoft.com/sql/t-sql/functions/compress-transact-sql) / [DECOMPRESS](https://docs.microsoft.com/sql/t-sql/functions/decompress-transact-sql) funkce umožňují kompresi a dekompresi řetězec vstup pomocí algoritmu GZIP.

#### <a name="example"></a>Příklad:

```sql
SELECT
    name [name_original]
    , COMPRESS(name) [name_compressed]
    , CAST(DECOMPRESS(COMPRESS(name)) AS NVARCHAR(MAX)) [name_decompressed]
FROM
    sys.objects;
```

### <a name="if-exists-clause-for-dropping-views"></a>IF EXISTS klauzule pro vyřazení zobrazení
Přidání klauzuli IF existuje v [VYŘAĎTE zobrazení](https://docs.microsoft.com/sql/t-sql/statements/drop-view-transact-sql) příkaz zjednodušuje kód T-SQL, které jsou nutné k odstranění zobrazení z datového skladu. Pokud existuje syntaxe, při použití příkazu DROP zobrazení vyřadí zobrazení, pokud existuje, nebo příkaz bude ignorovat, pokud zobrazení neexistuje.

#### <a name="example"></a>Příklad:
```sql
DROP VIEW IF EXISTS dbo.TestView;
```
```
Message
--------------------------------------------------
Commands completed successfully.

```

## <a name="improved-compilation-time-for-singleton-inserts-and-ddl-statements"></a>Čas kompilace vylepšené pro jednotlivý prvek vloží a příkazy DDL 
Po tradičního modelu extrakce, transformace a načítání (ETL) pro vkládání dat často vede ke spuštěnému singleton vkládat ([Dotaz INSERT VALUES](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql)) do tabulky v databázi. Tato verze zlepšuje výkon operací vložení typu singleton díky zkrácení doby kompilace potřebných k provedení příkazu tohoto typu. V některých případech zlepšování zjištěnou kompilace je až 3 x rychlejší. Toto vylepšení se zkrátit dobu potřebnou k provedení příkazu insert typu singleton. 

Zlepšení taky další výhody datové sklady s velkým množstvím objektů podobně snížením čas kompilace dotazu pro příkazy jazyka DDL (Data Definition) včetně CREATE, ALTER a operace odstranění. 

A konečně zlepšení snižuje celkový provádění příkazů, které provádějí nad širokých tabulek - tabulek, které mají velký počet sloupců. Zlepšení je snížení času v kompilační krok dotazu, který snižuje celkový čas spuštění pro dotazy.

## <a name="bug-fixes"></a>Opravy chyb

| Titul | Popis |
|:---|:---|
| **Oprava při vytváření statistik v distribucích pro unikátních omezení** | Tato oprava řeší chybu, která uživatelé dojde-li zadána spuštěné UPDATE STATISTICS se pouze v tabulce, tabulka, kdy byl definován jedinečné omezení. |
| **Oprava při kompilaci dotazů na externí tabulky** | Tato oprava řeší vadu vliv na čas kompilace pro dotazy zahrnující externí tabulky.|
| **Oprava při provádění příkazu s velké typy** | Řešení chyb v kompilaci připravený příkaz s parametry deklarovanými jako jeden z *velké* typy (nvarchar(max), varchar(max) a varbinary(max)). |
| **Opravit, když dojde k chybě pro hluboce vnořené dotazy** | Poskytuje jasný chybovou zprávu, když hluboce vnořený dotaz přesahuje omezení systému.|
| **Oprava chyby při kompilaci Pokud příkaz obsahuje poddotaz korelační a konstantu doba spuštění** |Řeší chybu v době kompilace pro dotazy s určitou kombinaci korelační poddotazy a konstanty z doby spuštění (např. GETDATE()).|
| **Řešení pro získávání zámků objekt PDW a souběžnosti slot pro autostats vypršení časového limitu** |Oprava přidá časový limit zámku zabránit autostats žádostí blokují původní žádosti dlouhou dobu.|

## <a name="next-steps"></a>Další postup
Teď, když už víte o něco o SQL Data Warehouse, zjistěte, jak rychle [vytvořit SQL Data Warehouse][create a SQL Data Warehouse]. Pokud s Azure začínáte, může vám být užitečný [Glosář Azure][Azure glossary], kde najdete potřebnou terminologii. Můžete se také podívat na některé z těchto dalších zdrojů ke službě SQL Data Warehouse.  

* [Úspěšné zákaznické implementace]
* [Blogy]
* [Žádosti o funkce]
* [Videa]
* [Blogy zákaznického poradního týmu]
* [Fórum Stack Overflow]
* [Twitter]


[Blogy]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogy zákaznického poradního týmu]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Úspěšné zákaznické implementace]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Žádosti o funkce]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videa]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
