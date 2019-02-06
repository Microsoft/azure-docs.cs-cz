---
title: Připojení a indexování Azure SQL Database obsahu pomocí indexerů – Azure Search
description: Zjistěte, jak k procházení dat ve službě Azure SQL Database pro fulltextové vyhledávání ve službě Azure Search pomocí indexerů. Tento článek se týká připojení, konfigurace indexeru a přijímat data.
ms.date: 10/17/2018
author: mgottein
manager: cgronlun
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 7df785d1493ad2df698ff197d72824ceb15d39ad
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55752888"
---
# <a name="connect-to-and-index-azure-sql-database-content-using-azure-search-indexers"></a>Připojte se k a indexování Azure SQL Database obsahu pomocí indexerů Azure Search

Předtím, než můžete zadávat dotazy [index Azure Search](search-what-is-an-index.md), je nutné ho naplnit svými daty. Pokud se data nacházejí ve službě Azure SQL database **indexeru Azure Search pro službu Azure SQL Database** (nebo **indexer pro Azure SQL** zkráceně) můžete automatizovat proces indexování, což znamená, že menší kód pro zápis a méně Infrastruktura o kterého vám jde.

Tento článek se týká použití mechanismu [indexery](search-indexer-overview.md), ale také popisuje funkce, které jsou k dispozici pouze s databází Azure SQL (například integrované sledování změn). 

Kromě databází Azure SQL, Azure Search poskytuje indexery pro [služby Azure Cosmos DB](search-howto-index-cosmosdb.md), [úložiště objektů Blob v Azure](search-howto-indexing-azure-blob-storage.md), a [úložiště tabulek v Azure](search-howto-indexing-azure-tables.md). Chcete-li požádat o podporu u jiných zdrojů dat, poskytněte zpětnou vazbu na [fóru pro zpětnou vazbu Azure Search](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="indexers-and-data-sources"></a>Indexery a zdroje dat.

A **zdroj dat** Určuje, jaká data do indexu, přihlašovací údaje pro přístup k datům a zásady, které efektivně identifikovat změny v datech (nové, změněné nebo odstraněné řádky). Jako nezávislý prostředek je definován tak, aby ho můžete použít několik indexerů.

**Indexer** je prostředek, který připojí jeden zdroj dat s cílovou vyhledávací index. Indexer je používán následujícími způsoby:

* Proveďte jednorázové kopii dat k naplnění indexu.
* Aktualizace indexu se změnami ve zdroji dat podle plánu.
* Spouštět na vyžádání na aktualizace indexu, podle potřeby.

Jeden indexer může využívat pouze jednu tabulku nebo zobrazení, ale pokud chcete naplnit pole více vyhledávacích indexů, můžete vytvořit několik indexerů. Další informace o konceptech najdete v tématu [operace indexeru: Typický pracovní postup](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations#typical-workflow).

Můžete nastavit a nakonfigurovat indexer Azure SQL pomocí:

* Průvodce importem dat v [webu Azure portal](https://portal.azure.com)
* Služba Azure Search [sady .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)
* Služba Azure Search [rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)

V tomto článku jsme pomocí rozhraní REST API vytvoříte **indexery** a **zdroje dat**.

## <a name="when-to-use-azure-sql-indexer"></a>Kdy použít Indexer pro Azure SQL
V závislosti na několika různými faktory týkající se vašich dat použijte indexer pro Azure SQL může nebo nemusí být vhodné. Pokud se vaše data vejde následující požadavky, můžete použít indexer pro Azure SQL.

| Kritéria | Podrobnosti |
|----------|---------|
| Data pocházejí z jedné tabulky nebo zobrazení | Pokud data je rozmístěny napříč více tabulek, můžete vytvořit jedno zobrazení data. Ale pokud používáte zobrazení, nebude moct používat SQL Server integrovaného detekce změn aktualizovat index s přírůstkové změny. Další informace najdete v tématu [zachytávání změnit a odstranit řádky](#CaptureChangedRows) níže. |
| Datové typy jsou kompatibilní | Většinu, ale ne všechny typy SQL jsou podporovány v indexu Azure Search. Seznam najdete v tématu [mapování datových typů](#TypeMapping). |
| Synchronizace dat v reálném čase se nevyžaduje | Indexer můžete přeindexování tabulce maximálně každých pět minut. Pokud data často mění a změny se projeví v indexu během několika sekund nebo minut jeden, doporučujeme použít [rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) nebo [sady .NET SDK](search-import-data-dotnet.md) aktualizované řádky vložit přímo. |
| Přírůstkové indexování je možné | Pokud máte velké sady dat a chcete indexer spouštět podle plánu, musí být schopen efektivně identifikovat nové, změněné nebo odstraněné řádky Azure Search. Non-incremental indexování se povoluje jenom Pokud indexování na vyžádání (ne podle plánu), nebo indexování méně než 100 000 řádků. Další informace najdete v tématu [zachytávání změnit a odstranit řádky](#CaptureChangedRows) níže. |

> [!NOTE] 
> Služba Azure Search podporuje jenom ověřování systému SQL Server. Pokud potřebujete podporu pro ověřování Azure Active Directory hesla, prosím hlasovat pro tuto [návrhu UserVoice](https://feedback.azure.com/forums/263029-azure-search/suggestions/33595465-support-azure-active-directory-password-authentica).

## <a name="create-an-azure-sql-indexer"></a>Vytvořit Indexer Azure SQL

1. Vytvoření zdroje dat:

   ```
    POST https://myservice.search.windows.net/datasources?api-version=2017-11-11
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }
   ```

   Můžete získat připojovací řetězec z [webu Azure portal](https://portal.azure.com); použít `ADO.NET connection string` možnost.

2. Pokud již nemáte, vytvořte cílový index Azure Search. Můžete vytvořit index pomocí [portál](https://portal.azure.com) nebo [vytvořit Index API](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Ujistěte se, že schéma cílový index kompatibilní s schématu zdrojové tabulky – viz [mapování mezi SQL a službu Azure search datové typy](#TypeMapping).

3. Vytvoření indexeru tak, že ho pojmenujete a odkazuje na index zdroj a cíl dat:

    ```
    POST https://myservice.search.windows.net/indexers?api-version=2017-11-11
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }
    ```

Indexer vytvořené v tomto případě nemá plán. Automaticky spustí, až když je vytvořena. Můžete ji spustit znovu kdykoliv **spustit indexer** žádosti:

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2017-11-11
    api-key: admin-key

Můžete přizpůsobit několik aspektů indexer chování, jako je například velikost dávky a kolik dokumentů mohou být přeskočeny, předtím, než se nezdaří spuštění indexeru. Další informace najdete v tématu [vytvořit Indexer API](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

Budete muset povolit službám Azure pro připojení k vaší databázi. Zobrazit [připojení z Azure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) pokyny, jak to provést.

K monitorování indexer stavu a spouštění historie (počet položek indexovaných, chyby atd.), použijte **indexer stav** žádosti:

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2017-11-11
    api-key: admin-key

Odpověď by měl vypadat nějak takto:

    {
        "\@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2015-02-21T00:23:24.957Z",
            "endTime":"2015-02-21T00:36:47.752Z",
            "errors":[],
            "itemsProcessed":1599501,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        },
        "executionHistory":
        [
            {
                "status":"success",
                "errorMessage":null,
                "startTime":"2015-02-21T00:23:24.957Z",
                "endTime":"2015-02-21T00:36:47.752Z",
                "errors":[],
                "itemsProcessed":1599501,
                "itemsFailed":0,
                "initialTrackingState":null,
                "finalTrackingState":null
            },
            ... earlier history items
        ]
    }

Historie provádění obsahuje až 50 nedávno dokončené spuštění, které jsou seřazeny v opačném pořadí chronologickém (tak, aby poslední spuštění je dodávána první v odpovědi).
Další informace o odpovědi můžete najít v [získání stavu indexeru](https://go.microsoft.com/fwlink/p/?LinkId=528198)

## <a name="run-indexers-on-a-schedule"></a>Indexery spouštět podle plánu
Můžete také uspořádat indexer pravidelné spouštění podle plánu. Chcete-li to provést, přidejte **plán** vlastnost při vytváření nebo aktualizaci indexeru. Následující příklad ukazuje požadavek PUT aktualizovat indexer:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2017-11-11
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

**Interval** parametr je povinný. Interval označuje čas mezi začátkem dvě po sobě jdoucích indexer spuštění. Nejkratší povolený interval je 5 minut; nejdelší sekvenci je za jeden den. Musí být naformátovaná jako hodnotu "dayTimeDuration" XSD (omezená podmnožina [ISO 8601 trvání](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) hodnota). Tento vzor je: `P(nD)(T(nH)(nM))`. Příklady: `PT15M` pro každých 15 minut `PT2H` pro každé dvě hodiny.

Volitelný **startTime** označuje, kdy by měla začít naplánované spuštění. Pokud ji vynecháte, je použit aktuální čas UTC. Tento čas může být v minulosti – ve kterém případě dojde k prvnímu spuštění plánu jako v případě, že indexer je spuštěné nepřetržitě od má položka StartTime hodnotu.  

Najednou lze spustit pouze jedno provedení indexeru. Pokud indexer je spuštěn, když je naplánováno spuštění, provádění se odložit, dokud dalším naplánovaném čase.

Zvažte následující příklad to lze provést konkrétnější. Předpokládejme, že jsme následující hodinový plán nakonfigurovali:

    "schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

Stane se toto:

1. Dojde k prvnímu spuštění indexeru začíná na nebo okolo 1. března 2015 12:00 dop. UTC.
2. Předpokládejme, že toto spuštění trvá 20 minut (nebo vždy menší než 1 hodina).
3. Spuštění druhého začíná na nebo okolo 1. března 2015 1:00 dop.
4. Nyní předpokládejme, že toto spuštění trvá déle než hodinu – například 70 minut – tak, aby nedokončí přibližně 2:10:00
5. Je nyní 2:00:00, čas pro třetí spuštění spustit. Ale protože spuštění druhého z 1: 00 ráno je pořád spuštěný, třetí provedení se přeskočilo. Třetí spuštění ve 3 hodiny.

Můžete přidat, změnit nebo odstranit plán pro existujícího indexeru pomocí **PUT indexer** požadavku.

<a name="CaptureChangedRows"></a>

## <a name="capture-new-changed-and-deleted-rows"></a>Zachycení nové, změny a odstranění řádků

Služba Azure Search používá **přírůstkové indexování** vyhnout se přeindexování celou tabulku nebo zobrazení pokaždé, když spustí indexeru. Služba Azure Search poskytuje že dvě zásady detekce pro podporu přírůstkové indexování změn. 

### <a name="sql-integrated-change-tracking-policy"></a>Zásady sledování změn s integrací SQL
Pokud vaše databáze SQL podporuje [sledování změn](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server), doporučujeme používat **SQL integrované změnit způsob sledování**. Toto je nejefektivnější zásady. Kromě toho umožňuje Azure Search k identifikaci odstraněné řádky, aniž by bylo nutné přidat do tabulky sloupec explicitní "obnovitelné odstranění".

#### <a name="requirements"></a>Požadavky 

+ Požadavky na verzi databáze:
  * SQL Server 2012 SP3 nebo novější, pokud používáte systém SQL Server na virtuálních počítačích Azure.
  * Azure SQL Database V12, pokud používáte Azure SQL Database.
+ Pouze tabulky (k dispozici žádná zobrazení). 
+ V databázi [povolit řešení change tracking](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server) pro tabulku. 
+ Složený primární klíč (primární klíč obsahující více než jeden sloupec) v tabulce.  

#### <a name="usage"></a>Využití

Pokud chcete tuto zásadu použít, vytvořit nebo aktualizovat zdroj dat takto:

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }

Při použití SQL integrované řešení change tracking zásad, nezadávejte zásadu samostatná data pro odstranění duplicit – má integrovanou podporu pro identifikaci tuto zásadu odstranit řádky. Ale pro odstranění, který bude zjištěná "automagically", klíč dokumentu v indexu vyhledávání musí být stejná jako primární klíč v tabulce SQL. 

> [!NOTE]  
> Při použití [TRUNCATE TABLE](https://docs.microsoft.com/sql/t-sql/statements/truncate-table-transact-sql) odebrat velký počet řádků z tabulky SQL, musí být indexeru [resetování](https://docs.microsoft.com/rest/api/searchservice/reset-indexer) resetovat stav ke sbírání odstranění řádku sledování změn.

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>Zásady detekce změn vysoká označuje jako horní mez

Tato zásada zjišťování změn spoléhá na sloupec "vysoká označuje jako horní mez" zachytávání verze nebo čas poslední aktualizace řádku. Pokud používáte zobrazení, musíte použít zásadu vysoce mezí. Sloupce mezí vysoce musí splňovat následující požadavky.

#### <a name="requirements"></a>Požadavky 

* Všechny operace vložení zadejte hodnotu pro sloupec.
* Všechny aktualizace položky také změnit hodnotu sloupce.
* Hodnota v tomto sloupci Hodnota se zvyšuje s každou insert nebo update.
* Dotazy s následujícími kde a efektivně mohou být provedeny klauzule ORDER BY: `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

> [!IMPORTANT] 
> Důrazně doporučujeme používat [rowversion](https://docs.microsoft.com/sql/t-sql/data-types/rowversion-transact-sql) datový typ pro sloupec vysoce mezí. Pokud se používá k jakýmkoli jiným datovým typem, sledování změn není zaručeno zachytit všechny změny za přítomnosti transakce provádění současně dotazu indexeru. Při použití **rowversion** v konfiguraci s replikami jen pro čtení, musí odkazovat na primární replice indexeru. Pro scénáře synchronizace dat je možné pouze primární repliku.

#### <a name="usage"></a>Využití

Použití zásad vysoce mezí, vytvořit nebo aktualizovat zdroj dat takto:

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[a rowversion or last_updated column name]"
      }
    }

> [!WARNING]
> Pokud zdrojová tabulka nemá žádné index na sloupci vysoce mezí, dotazy, pomocí kterých indexer SQL může vypršení časového limitu. Zejména v případě `ORDER BY [High Water Mark Column]` klauzule vyžaduje indexu umožňuje efektivně spouštět, pokud tabulka obsahuje mnoho řádků.
>
>

Pokud narazíte na chyby časového limitu, můžete použít `queryTimeout` nastavení konfigurace indexeru nastavit časový limit dotazu na hodnotu vyšší než výchozí časový limit 5 minut. Nastavit časový limit na 10 minut, třeba vytvořit nebo aktualizovat indexer s následující konfigurací:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

Můžete také zakázat `ORDER BY [High Water Mark Column]` klauzuli. To však není doporučeno, protože pokud se spuštění indexeru je přerušeno s chybou, indexer musí znovu zpracovat všechny řádky novější – poběží i v případě indexeru už zpracovala téměř všechny řádky v době, kdy bylo přerušeno. Chcete-li zakázat `ORDER BY` klauzule, použijte `disableOrderByHighWaterMarkColumn` nastavení v definici indexer:  

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Zásady obnovitelné odstranění detekce odstranění sloupce
Při odstranění řádků z tabulky zdroje, pravděpodobně chcete odstranit řádky z vyhledávacího indexu. Pokud používáte zásady sledování změn SQL integrované, to postará za vás. Ale zásady sledování změn jako horní mez vysoce vám nepomohly s odstraněnými řádky. Co dělat?

Pokud fyzicky odebrání řádků z tabulky Azure Search nemá žádný způsob, jak odvodit přítomnost záznamy, které už existují.  Můžete však použít techniku "obnovitelného odstranění" logicky odstraňovat řádky, aniž byste je odebrali z tabulky. Přidání sloupce pro tabulku nebo zobrazení a označit řádky jako odstraněné pomocí tohoto sloupce.

Při použití obnovitelného odstranění techniku, můžete určit, zásadu obnovitelného odstranění následujícím způsobem při vytváření nebo aktualizaci zdroje dat:

    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }

**SoftDeleteMarkerValue** musí být řetězcový – použijte řetězcovou reprezentaci skutečnou hodnotu. Například pokud máte sloupec celých čísel, kde odstraněné řádky jsou označeny hodnotou 1 použít `"1"`. Pokud máte sloupec BIT, kde odstraněné řádky jsou označeny logická hodnota true, pomocí řetězcového literálu `True` nebo `true`, tak nezáleží.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-and-azure-search-data-types"></a>Mapování mezi datovými typy SQL a Azure Search
| Datový typ SQL. | Povolené typy pole cílový index | Poznámky |
| --- | --- | --- |
| Bit |Edm.Boolean, Edm.String | |
| int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String | |
| bigint |Edm.Int64, Edm.String | |
| skutečné, float |Edm.Double, Edm.String | |
| Smallmoney peníze desítková číslice |Edm.String |Služba Azure Search nepodporuje převod desítkové typy do Edm.Double, protože tím byste ztratit přesnost |
| Char, nchar, varchar, nvarchar |Edm.String<br/>Collection(Edm.String) |K vyplnění pole Collection(Edm.String), pokud řetězec představuje pole JSON řetězců, které je možné řetězec SQL: `["red", "white", "blue"]` |
| smalldatetime, data a času, datetime2, date, datetimeoffset |Edm.DateTimeOffset, Edm.String | |
| uniqueidentifer |Edm.String | |
| Zeměpisné oblasti |Edm.GeographyPoint |Jsou podporovány pouze zeměpisné oblasti instance typu bodu SRID 4326 (což je výchozí hodnota) |
| ROWVERSION |neuvedeno |Verze řádku sloupce nelze ukládat v indexu vyhledávání, ale mohou být použity pro sledování změn |
| čas, časový interval, binární soubor, varbinary, image, xml, geometry, typy CLR |neuvedeno |Nepodporuje se |

## <a name="configuration-settings"></a>Nastavení konfigurace
SQL indexer poskytuje několik nastavení konfigurace:

| Nastavení | Typ dat | Účel | Výchozí hodnota |
| --- | --- | --- | --- |
| queryTimeout |řetězec |Nastaví časový limit pro provedení dotazu SQL. |5 minut ("00: 05:00") |
| disableOrderByHighWaterMarkColumn |BOOL |Způsobí, že dotaz SQL použitý zásadami vysoce mezí Pokud chcete vynechat, nechte klauzule ORDER by. Zobrazit [vysoce mezí zásad](#HighWaterMarkPolicy) |false (nepravda) |

Tato nastavení se používají v `parameters.configuration` objektu v definice indexeru. Například pokud chcete nastavit časový limit dotazu na 10 minut, vytvořit nebo aktualizovat indexer s následující konfigurací:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

## <a name="faq"></a>Nejčastější dotazy

**Otázka: Můžete použít indexer pro Azure SQL s SQL Database běžící na virtuálních počítačích IaaS v Azure?**

Ano. Ale budete muset povolit vaší vyhledávací služby pro připojení k vaší databázi. Další informace najdete v tématu [konfigurace připojení indexeru Azure Search k systému SQL Server na Virtuálním počítači Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md).

**Otázka: Můžete použít indexer pro Azure SQL s databází SQL, které jsou spuštěné místně?**

Ne přímo. Společnost Microsoft nedoporučuje ani nepodporuje přímé připojení, jako to uděláte tak by vyžadovalo otevření databází pro provoz sítě Internet. Zákazníci mají proběhlo úspěšně, v tomto scénáři použití most technologií, jako je Azure Data Factory. Další informace najdete v tématu [vložení dat do indexu Azure Search pomocí Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-azure-search-connector).

**Otázka: Můžete použít indexer pro Azure SQL s databázemi než SQL Server běžící na IaaS v Azure?**

Ne. Nepodporujeme tento scénář, protože nebyly testovány indexeru se žádné databáze než SQL Server.  

**Otázka: Můžete vytvořit několik indexerů spouštění podle plánu?**

Ano. Nicméně pouze indexerů může běžet na jednom uzlu najednou. Pokud budete potřebovat několik indexerů, které jsou spuštěny souběžně, zvažte možnost škálování služby vyhledávání pro více než jedna jednotka služby search.

**Otázka: Spuštění indexeru dotazu úlohy ovlivní?**

Ano. Spuštění indexeru na jednom z uzlů ve vaší vyhledávací služby a prostředky daného uzlu jsou sdílené mezi indexování a obsluhující provoz dotazu a další požadavky rozhraní API. Pokud spuštění úlohy náročné na indexování a dotazování a dojde k vysoké rychlosti 503 chyby nebo zvýšení doby odezvy, zvažte [vertikálně kapacitu vaší služby search](search-capacity-planning.md).

**Otázka: Můžete použít v sekundární replice [clusteru převzetí služeb při selhání](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) jako zdroj dat?**

To záleží na okolnostech. Pro úplné indexování tabulky nebo zobrazení, můžete použít na sekundární repliku. 

Azure Search pro přírůstkové indexování podporuje dvě zásady detekce změn: S integrací SQL je sledování změn a vysoce mezí.

Na repliky jen pro čtení SQL database nepodporuje sledování integrované změn. Proto musíte použít zásad vysoce mezí. 

Naše standardní doporučuje používat rowversion datový typ pro sloupec vysoce mezí. Pomocí rowversion však závisí na SQL Database `MIN_ACTIVE_ROWVERSION` funkce, která není podporována u replik jen pro čtení. Indexer proto musí odkazovat na primární replice, pokud používáte rowversion.

Pokud se pokusíte použít rowversion repliky jen pro čtení, zobrazí se následující chyba: 

    "Using a rowversion column for change tracking is not supported on secondary (read-only) availability replicas. Please update the datasource and specify a connection to the primary availability replica.Current database 'Updateability' property is 'READ_ONLY'".

**Otázka: Můžete použít alternativní, sloupci bez rowversion pro vysoce mezí change tracking**

Není doporučeno. Pouze **rowversion** umožňuje synchronizaci dat spolehlivé. Nicméně, v závislosti na vaší aplikace logiky může být bezpečné pokud:

+ Můžete zajistit, že po spuštění indexeru, že nejsou žádné nevyřízené transakce v tabulce se indexuje zpětně (například všechny aktualizace tabulky dojít v dávce, podle plánu, a plán indexeru Azure Search je nastavená na překrývající se aktualizace tabulky plán).  

+ Pravidelně provádět úplnou reindex ke sbírání všechny chybějící řádky. 