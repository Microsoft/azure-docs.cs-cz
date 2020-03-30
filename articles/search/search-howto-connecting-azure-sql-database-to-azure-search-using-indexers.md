---
title: Hledání přes data Azure SQL
titleSuffix: Azure Cognitive Search
description: Importujte data z Azure SQL Database pomocí indexerů pro fulltextové vyhledávání v Azure Cognitive Search. Tento článek popisuje připojení, konfigurace indexeru a přijím dat.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: c09727e8d92a449b41124eae6ad8381d66cb2619
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113300"
---
# <a name="connect-to-and-index-azure-sql-database-content-using-an-azure-cognitive-search-indexer"></a>Připojení k obsahu Azure SQL Database a indexování pomocí indexeru Azure Cognitive Search

Než budete moci dotaz [ovat index Azure Cognitive Search](search-what-is-an-index.md), musíte ho naplnit daty. Pokud data žijí v databázi Azure SQL, **může indexer Azure Cognitive Search pro Azure SQL Database** (nebo **zkrácený Indexer Azure SQL)** automatizovat proces indexování, což znamená méně kódu pro zápis a méně infrastruktury, na které je třeba se starat.

Tento článek popisuje mechaniky pomocí [indexery](search-indexer-overview.md), ale také popisuje funkce, které jsou k dispozici pouze v databázích Azure SQL (například integrované sledování změn). 

Kromě databází Azure SQL poskytuje Azure Cognitive Search indexery pro [Azure Cosmos DB](search-howto-index-cosmosdb.md), [azure blob storage](search-howto-indexing-azure-blob-storage.md)a úložiště [tabulek Azure](search-howto-indexing-azure-tables.md). Chcete-li požádat o podporu pro jiné zdroje dat, zadejte svůj názor na [fórum pro zpětnou vazbu Azure Cognitive Search](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="indexers-and-data-sources"></a>Indexery a zdroje dat

**Zdroj dat určuje,** která data mají být indexována, pověření pro přístup k datům a zásady, které efektivně identifikují změny v datech (nové, upravené nebo odstraněné řádky). Je definovánjako nezávislý prostředek tak, aby jej lze použít více indexery.

**Indexer** je prostředek, který spojuje jeden zdroj dat s indexem cíleného vyhledávání. Indexer se používá následujícími způsoby:

* Proveďte jednorázovou kopii dat k naplnění indexu.
* Aktualizujte index se změnami ve zdroji dat podle plánu.
* Spusťte na vyžádání aktualizovat index podle potřeby.

Jeden indexer může spotřebovat pouze jednu tabulku nebo zobrazení, ale můžete vytvořit více indexerů, pokud chcete naplnit více indexů hledání. Další informace o konceptech naleznete v tématu [Indexer Operations: Typical workflow](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations#typical-workflow).

Indexer Azure SQL můžete nastavit a nakonfigurovat pomocí:

* Průvodce importem dat na [webu Azure Portal](https://portal.azure.com)
* Azure Cognitive Search [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)
* [Rozhraní REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) azure kognitivního vyhledávání

V tomto článku použijeme rozhraní REST API k vytvoření **indexerů** a **zdrojů dat**.

## <a name="when-to-use-azure-sql-indexer"></a>Kdy použít Azure SQL Indexer
V závislosti na několika faktorech týkajících se vašich dat může nebo nemusí být vhodné použití indexeru Azure SQL. Pokud vaše data odpovídají následujícím požadavkům, můžete použít Azure SQL indexer.

| Kritéria | Podrobnosti |
|----------|---------|
| Data pocházejí z jedné tabulky nebo zobrazení | Pokud jsou data rozptýlena ve více tabulkách, můžete vytvořit jedno zobrazení dat. Pokud však použijete zobrazení, nebudete moci použít integrované zjišťování změn sql serveru k aktualizaci indexu s přírůstkovými změnami. Další informace naleznete [v tématu Zachycení změněných a odstraněných řádků](#CaptureChangedRows) níže. |
| Datové typy jsou kompatibilní | Většina, ale ne všechny typy SQL jsou podporovány v indexu Azure Cognitive Search. Seznam naleznete v tématu [Mapování datových typů](#TypeMapping). |
| Synchronizace dat v reálném čase není vyžadována. | Indexer může přeindexovat tabulku maximálně každých pět minut. Pokud se data často mění a změny se musí projevit v indexu během několika sekund nebo jednoho minuty, doporučujeme použít [rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) nebo [.NET SDK](search-import-data-dotnet.md) k přímému nabízení aktualizovaných řádků. |
| Inkrementální indexování je možné | Pokud máte velkou sadu dat a plánujete spustit indexer podle plánu, azure kognitivní vyhledávání musí být schopen efektivně identifikovat nové, změněné nebo odstraněné řádky. Nepřírůstkové indexování je povoleno pouze v případě, že indexujete na vyžádání (ne podle plánu) nebo indexujete méně než 100 000 řádků. Další informace naleznete [v tématu Zachycení změněných a odstraněných řádků](#CaptureChangedRows) níže. |

> [!NOTE] 
> Azure Cognitive Search podporuje jenom ověřování serveru SQL Server. Pokud potřebujete podporu pro ověřování hesla služby Azure Active Directory, hlaste pro tento [návrh UserVoice](https://feedback.azure.com/forums/263029-azure-search/suggestions/33595465-support-azure-active-directory-password-authentica).

## <a name="create-an-azure-sql-indexer"></a>Vytvoření Azure SQL Indexer

1. Vytvořte zdroj dat:

   ```
    POST https://myservice.search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }
   ```

   Připojovací řetězec můžete získat z [webu Azure Portal](https://portal.azure.com); použijte `ADO.NET connection string` tuto možnost.

2. Vytvořte cílový index Azure Cognitive Search, pokud ho ještě nemáte. Index můžete vytvořit pomocí [portálu](https://portal.azure.com) nebo [rozhraní CREATE Index API](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Ujistěte se, že schéma cílového indexu je kompatibilní se schématem zdrojové tabulky – viz [mapování mezi datovými typy sql a Azure Cognitive search](#TypeMapping).

3. Vytvořte indexer tak, že mu dáte název a odkazujete na zdroj dat a cílový index:

    ```
    POST https://myservice.search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }
    ```

Indexer vytvořený tímto způsobem nemá plán. Automaticky se spustí jednou, když je vytvořen. Můžete jej spustit znovu kdykoli pomocí **spustit indexer** požadavek:

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2019-05-06
    api-key: admin-key

Můžete přizpůsobit několik aspektů chování indexeru, jako je například velikost dávky a kolik dokumentů lze přeskočit před selháním spuštění indexeru. Další informace naleznete v [tématu Create Indexer API](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

Možná budete muset povolit služby Azure pro připojení k databázi. Pokyny, jak to udělat, najdete v tématu [Připojení z Azure.](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)

Chcete-li sledovat stav indexeru a historii spuštění (počet indexovaných položek, selhání atd.), použijte požadavek na **stav indexeru:**

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2019-05-06
    api-key: admin-key

Odpověď by měla vypadat podobně jako následující:

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

Historie spuštění obsahuje až 50 naposledy dokončených spuštění, které jsou seřazeny v obráceném chronologickém pořadí (tak, aby poslední spuštění je na prvním místě v odpovědi).
Další informace o odpovědi naleznete ve [stavu získat indexer](https://go.microsoft.com/fwlink/p/?LinkId=528198)

## <a name="run-indexers-on-a-schedule"></a>Spuštění indexerů podle plánu
Můžete také uspořádat indexer pravidelně spustit podle plánu. Chcete-li to provést, přidejte vlastnost **plánu** při vytváření nebo aktualizaci indexeru. Následující příklad ukazuje požadavek PUT na aktualizaci indexeru:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

Je vyžadován parametr **intervalu.** Interval odkazuje na čas mezi začátkem dvou po sobě jdoucích indexeru spuštění. Nejmenší povolený interval je 5 minut; nejdelší je jeden den. Musí být formátován jako hodnota XSD "dayTimeDuration" (omezená podmnožina hodnoty [trvání ISO 8601).](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) Vzor pro toto `P(nD)(T(nH)(nM))`je: . Příklady: `PT15M` za každých `PT2H` 15 minut, za každé 2 hodiny.

Další informace o definování plánů indexeru naleznete v [tématu Jak naplánovat indexery pro Azure Cognitive Search](search-howto-schedule-indexers.md).

<a name="CaptureChangedRows"></a>

## <a name="capture-new-changed-and-deleted-rows"></a>Zachycení nových, změněných a odstraněných řádků

Azure Cognitive Search používá **přírůstkové indexování,** aby nemuseli přeindexovat celou tabulku nebo zobrazit při každém spuštění indexeru. Azure Cognitive Search poskytuje dvě zásady zjišťování změn pro podporu přírůstkového indexování. 

### <a name="sql-integrated-change-tracking-policy"></a>Integrované zásady sledování změn SQL
Pokud databáze SQL podporuje [sledování změn](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server), doporučujeme použít **integrované zásady sledování změn SQL**. Jedná se o nejúčinnější politiku. Kromě toho umožňuje Azure Cognitive Search identifikovat odstraněné řádky, aniž byste museli přidat explicitní "obnovitelné odstranění" sloupec do tabulky.

#### <a name="requirements"></a>Požadavky 

+ Požadavky na verzi databáze:
  * SQL Server 2012 SP3 a novější, pokud používáte SQL Server na virtuálních počítačích Azure.
  * Azure SQL Database V12, pokud používáte Azure SQL Database.
+ Pouze tabulky (bez zobrazení). 
+ V databázi [povolte sledování změn](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server) pro tabulku. 
+ Žádný složený primární klíč (primární klíč obsahující více než jeden sloupec) v tabulce.  

#### <a name="usage"></a>Využití

Chcete-li použít tuto zásadu, vytvořte nebo aktualizujte zdroj dat takto:

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }

Při použití zásad sledování změn integrované sql nezadávejte samostatné zásady detekce odstranění dat - tato zásada má integrovanou podporu pro identifikaci odstraněných řádků. Však pro odstranění, které mají být detekovány "automagicky", klíč dokumentu v indexu vyhledávání musí být stejný jako primární klíč v tabulce SQL. 

> [!NOTE]  
> Při použití [zkrátit tabulka](https://docs.microsoft.com/sql/t-sql/statements/truncate-table-transact-sql) odebrat velký počet řádků z tabulky SQL, indexer musí být [resetován](https://docs.microsoft.com/rest/api/searchservice/reset-indexer) obnovit stav sledování změn vyzvednout odstranění řádků.

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>Zásady detekce změn značek vysoké hladiny

Tato zásada zjišťování změn závisí na sloupci "vysoké vodoznak" zachycující verzi nebo čas, kdy byl řádek naposledy aktualizován. Pokud používáte zobrazení, musíte použít zásady vysoké vodoznaku. Sloupec s vysokou vodoznakem musí splňovat následující požadavky.

#### <a name="requirements"></a>Požadavky 

* Všechny vloží určují hodnotu sloupce.
* Všechny aktualizace položky také změnit hodnotu sloupce.
* Hodnota tohoto sloupce se zvyšuje s každou vložit nebo aktualizovat.
* Dotazy s následujícími klauzulemi WHERE a ORDER BY lze provádět efektivně:`WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

> [!IMPORTANT] 
> Důrazně doporučujeme použít datový typ [rowversion](https://docs.microsoft.com/sql/t-sql/data-types/rowversion-transact-sql) pro sloupec vysoké vodoznaky. Pokud je použit jiný datový typ, sledování změn není zaručeno zachytit všechny změny v přítomnosti transakcí provádění souběžně s dotazem indexeru. Při použití **rowversion** v konfiguraci s replikami jen pro čtení, musíte bod indexeru na primární repliky. Pro scénáře synchronizace dat lze použít pouze primární repliku.

#### <a name="usage"></a>Využití

Chcete-li použít zásady vysoké vodoznaku, vytvořte nebo aktualizujte zdroj dat takto:

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
> Pokud zdrojová tabulka nemá index ve sloupci vysoké vodoznak, dotazy používané indexeru SQL může časový mzda. Zejména `ORDER BY [High Water Mark Column]` klauzule vyžaduje index spustit efektivně, pokud tabulka obsahuje mnoho řádků.
>
>

Pokud narazíte na chyby časového `queryTimeout` limitu, můžete použít nastavení konfigurace indexeru k nastavení časového limitu dotazu na hodnotu vyšší než výchozí časový limit 5 minut. Chcete-li například nastavit časový čas na 10 minut, vytvořte nebo aktualizujte indexer s následující konfigurací:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

Můžete také zakázat klauzuli. `ORDER BY [High Water Mark Column]` To se však nedoporučuje, protože pokud je spuštění indexeru přerušeno chybou, indexer musí znovu zpracovat všechny řádky, pokud je spuštěn později - i v případě, že indexer již zpracoval téměř všechny řádky v době, kdy byl přerušen. Chcete-li `ORDER BY` klauzuli `disableOrderByHighWaterMarkColumn` zakázat, použijte nastavení v definici indexeru:  

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Zásady detekce odstranění odstraňovacího sloupce s měkkým odstraněním
Při odstranění řádků ze zdrojové tabulky pravděpodobně budete chtít odstranit tyto řádky také z indexu vyhledávání. Pokud používáte zásady sledování změn integrované SQL, je to postaráno za vás. Zásady sledování změn horní mezí však nepomáhají s odstraněnými řádky. Co dělat?

Pokud řádky jsou fyzicky odebrány z tabulky, Azure Cognitive Search nemá žádný způsob, jak odvodit přítomnost záznamů, které již neexistují.  Můžete však použít techniku "obnovitelného odstranění" k logickému odstranění řádků bez jejich odstranění z tabulky. Přidejte sloupec do tabulky nebo zobrazení a označte řádky jako odstraněné pomocí tohoto sloupce.

Při použití techniky obnovitelného odstranění můžete při vytváření nebo aktualizaci zdroje dat určit zásady obnovitelného odstranění následujícím způsobem:

    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }

**SoftDeleteMarkerValue** musí být řetězec – použijte řetězcovou reprezentaci skutečné hodnoty. Pokud máte například celý sloupec, ve kterém jsou odstraněné řádky `"1"`označeny hodnotou 1, použijte . Pokud máte sloupec BIT, kde jsou odstraněné řádky označeny logickou `True` `true`hodnotou true, použijte literál řetězce nebo na případu nezáleží.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-and-azure-cognitive-search-data-types"></a>Mapování mezi datovými typy SQL a Azure Cognitive Search
| Datový typ SQL | Povolené typy cílových indexových polí | Poznámky |
| --- | --- | --- |
| bitové |Edm.Boolean, Edm.String | |
| int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String | |
| bigint |Edm.Int64, Edm.String | |
| skutečné, plovák |Edm.Double, Edm.String | |
| smallmoney, peníze desítkové číselné |Edm.String |Azure Cognitive Search nepodporuje převod desetinných typů na Edm.Double, protože by to ztratilo přesnost |
| char, nchar, varchar, nvarchar |Edm.String<br/>Collection(Edm.String) |Řetězec SQL lze použít k naplnění pole Collection(Edm.String), pokud řetězec představuje pole JSON řetězců:`["red", "white", "blue"]` |
| smalldatetime, datetime, datetime, datetime2, date, datetimeoffset |Edm.DateTimeOffset, Edm.String | |
| uniqueidentifer |Edm.String | |
| Geografie |Edm.GeographyPoint |Podporovány jsou pouze geografické instance typu POINT s SRID 4326 (což je výchozí hodnota). |
| Rowversion |Není dostupné. |Sloupce řádkové verze nelze uložit do indexu vyhledávání, ale lze je použít pro sledování změn. |
| time, timespan, binární, varbinary, image, xml, geometrie, CLR typy |Není dostupné. |Nepodporuje se |

## <a name="configuration-settings"></a>Nastavení konfigurace
Sql indexer zveřejňuje několik nastavení konfigurace:

| Nastavení | Datový typ | Účel | Výchozí hodnota |
| --- | --- | --- | --- |
| queryTimeout |řetězec |Nastaví časový limit pro spuštění dotazu SQL. |5 minut ("00:05:00") |
| značka disableOrderByHighWaterMarkColumn |bool |Způsobí, že dotaz SQL používá zásady vysoké vodoznak vynechat order by klauzule. Viz [zásady vysoké vodní známky](#HighWaterMarkPolicy) |false (nepravda) |

Tato nastavení se `parameters.configuration` používají v objektu v definici indexeru. Chcete-li například nastavit časový limit dotazu na 10 minut, vytvořte nebo aktualizujte indexer s následující konfigurací:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

## <a name="faq"></a>Nejčastější dotazy

**Otázka: Můžu použít Azure SQL indexer s databázemi SQL spuštěnými na virtuálních počítačích IaaS v Azure?**

Ano. Je však nutné povolit vyhledávací službě připojení k databázi. Další informace najdete [v tématu Konfigurace připojení z indexeru Azure Cognitive Search na SQL Server na virtuálním počítači Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md).

**Otázka: Můžu používat Indexer Azure SQL s databázemi SQL spuštěnými místně?**

Ne přímo. Přímé připojení nedoporučujeme ani nepodporujeme, protože by to vyžadovalo otevření databází internetovému provozu. Zákazníci uspěli s tímto scénářem pomocí mostu technologií, jako je Azure Data Factory. Další informace najdete v tématu [Nabízená data do indexu Azure Cognitive Search pomocí Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-azure-search-connector).

**Otázka: Můžu používat Indexer Azure SQL s jinými databázemi než SQL Server spuštěnými v IaaS v Azure?**

Ne. Nepodporujeme tento scénář, protože jsme netestovali indexer s žádnou databází než SQL Server.  

**Otázka: Lze vytvořit více indexerů spuštěných podle plánu?**

Ano. Však pouze jeden indexer může být spuštěn na jednom uzlu najednou. Pokud potřebujete více indexerů spuštěné současně, zvažte škálování vyhledávací služby na více než jednu vyhledávací jednotku.

**Otázka: Má spuštění indexeru vliv na úlohu dotazu?**

Ano. Indexer běží na jednom z uzlů ve vyhledávací službě a prostředky uzlu jsou sdíleny mezi indexováním a obsluhováním dotazování a dalšími požadavky rozhraní API. Pokud spustíte intenzivní indexování a úlohy dotazů a setkáte se s vysokou mírou 503 chyb nebo prodlužte dobu odezvy, [zvažte navýšení měřítka vyhledávací služby](search-capacity-planning.md).

**Otázka: Lze použít sekundární repliku v [clusteru s podporou převzetí služeb při selhání](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) jako zdroj dat?**

To závisí na okolnostech. Pro úplné indexování tabulky nebo zobrazení můžete použít sekundární repliku. 

Pro přírůstkové indexování Azure Cognitive Search podporuje dvě zásady zjišťování změn: integrované sledování změn SQL a vysoké vodoznaky.

U replik jen pro čtení databáze SQL nepodporuje integrované sledování změn. Proto je nutné použít zásady vysoké vodní značky. 

Naše standardní doporučení je použít datový typ rowversion pro sloupec vysoké vodoznak. Použití rowversion však závisí na `MIN_ACTIVE_ROWVERSION` funkci SQL Database, která není podporována na repliky jen pro čtení. Proto je nutné nasměrovat indexer na primární repliku, pokud používáte rowversion.

Pokud se pokusíte použít rowversion na repliku jen pro čtení, zobrazí se následující chyba: 

    "Using a rowversion column for change tracking is not supported on secondary (read-only) availability replicas. Please update the datasource and specify a connection to the primary availability replica.Current database 'Updateability' property is 'READ_ONLY'".

**Otázka: Mohu použít alternativní sloupec bez verze řádku pro sledování změn s vysokou vodoznakem?**

To se nedoporučuje. Pouze **rowversion** umožňuje spolehlivou synchronizaci dat. V závislosti na logice aplikace však může být bezpečné, pokud:

+ Můžete zajistit, že při spuštění indexeru nejsou v tabulce žádné nevyřízené transakce, které se indexují (například všechny aktualizace tabulky probíhají podle plánu jako dávka a plán indexeru Azure Cognitive Search je nastaven tak, aby se zabránilo překrývání s tabulkou plán aktualizace).  

+ Pravidelně provádíte úplný reindex, abyste vyzvedli všechny zmeškané řádky. 