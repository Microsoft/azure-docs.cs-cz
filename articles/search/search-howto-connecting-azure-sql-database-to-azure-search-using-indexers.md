---
title: "Připojení databáze Azure SQL do Azure Search pomocí indexerů | Microsoft Docs"
description: "Naučte se načítat data z databáze SQL Azure do indexu Azure Search pomocí indexerů."
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: e9bbf352-dfff-4872-9b17-b1351aae519f
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 08/12/2018
ms.author: eugenesh
ms.openlocfilehash: 5f85b81e894cba7354fb146d6e9a1aa987be7dc5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="connecting-azure-sql-database-to-azure-search-using-indexers"></a>Připojení k Azure Search pomocí indexerů Azure SQL Database

Předtím, než se můžete dotazovat [indexu Azure Search](search-what-is-an-index.md), musí jeho naplnění vaše data. Pokud data žije v Azure SQL database, **indexer Azure Search pro databázi SQL Azure** (nebo **indexer Azure SQL** pro zkrácení) můžete automatizovat proces indexování, což znamená méně kódu k zápisu a méně infrastrukturu pro zajímají.

Tento článek se zabývá mechanismů pomocí [indexery](search-indexer-overview.md), ale také popisuje funkce, které jsou k dispozici jenom s databází Azure SQL (například integrované sledování změn). 

Kromě databází Azure SQL, Azure Search nabízí indexery pro [Azure Cosmos DB](search-howto-index-cosmosdb.md), [úložiště objektů Azure Blob](search-howto-indexing-azure-blob-storage.md), a [úložiště tabulek Azure](search-howto-indexing-azure-tables.md). Chcete-li požádat o podporu pro další datové zdroje, zadejte svůj názor na [fóru pro zpětnou vazbu Azure Search](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="indexers-and-data-sources"></a>Indexery a zdroje dat.

A **zdroj dat** Určuje, která data do indexu, přihlašovací údaje pro přístup k datům a zásady, které efektivně identifikovat změny v datech (nové, modified nebo deleted řádky). Je definována jako prostředek nezávisle tak, aby ji můžete použít několik indexerů.

**Indexer** je na prostředek, který připojí jeden zdroj dat s indexem cílové vyhledávání. Indexer se používá následujícími způsoby:

* Proveďte jednorázové kopii dat k naplnění indexu.
* Aktualizujte index změnami ve zdroji dat podle plánu.
* Spusťte na vyžádání se aktualizovat index podle potřeby.

Jeden indexeru můžete využívat pouze jednu tabulku nebo zobrazení, ale můžete vytvořit několik indexerů, pokud chcete k naplnění více indexů vyhledávání. Další informace o konceptech najdete v tématu [Indexer Operations: obvyklý pracovní postup](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations#typical-workflow).

Můžete nastavit a nakonfigurovat indexer Azure SQL pomocí:

* Průvodce importem dat v [portálu Azure](https://portal.azure.com)
* Služba Azure Search [sady .NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)
* Služba Azure Search [rozhraní REST API](https://docs.microsoft.com/rest/api/searchservice/indexer-operations)

V tomto článku pomocí REST API pro vytvoření **indexery** a **zdroje dat**.

## <a name="when-to-use-azure-sql-indexer"></a>Kdy použít Azure SQL Indexer
V závislosti na několika různými faktory týkající se dat používání Azure SQL indexer může nebo nemusí být vhodné. Pokud vaše data splňuje následující požadavky, můžete použít indexer Azure SQL.

| Kritéria | Podrobnosti |
|----------|---------|
| Data pocházejí z jedné tabulky nebo zobrazení | Pokud data je rozmístěny na několika tabulkám, můžete vytvořit jediné zobrazení data. Ale pokud používáte zobrazení, nebudete moci používat SQL Server integrovaného detekce změn aktualizovat index s přírůstkové změny položku. Další informace najdete v tématu [zaznamenávání změnit a odstranit řádky](#CaptureChangedRows) níže. |
| Datové typy jsou kompatibilní | Většina, ale ne všechny typy SQL jsou podporovány v indexu Azure Search. Seznam najdete v tématu [mapování datové typy](#TypeMapping). |
| Synchronizace dat v reálném čase není vyžadováno | Indexer znovu mohou indexu tabulku maximálně každých pět minut. Pokud potřebujete projeví v indexu během několika sekund nebo minut jeden data často mění a změny, doporučujeme používat [REST API](https://docs.microsoft.com/rest/api/searchservice/AddUpdate-or-Delete-Documents) nebo [.NET SDK](search-import-data-dotnet.md) tak, aby nabízel aktualizované řádky přímo. |
| Přírůstkové indexování je možné | Pokud máte velké sady dat a plán spuštění indexeru podle plánu, musí být schopni efektivně identifikovat nové, změněné nebo odstraněné řádky Azure Search. Bez přírůstkové indexování je povoleno pouze pokud jste indexu na vyžádání (ne podle plánu), nebo indexování méně než 100 000 řádků. Další informace najdete v tématu [zaznamenávání změnit a odstranit řádky](#CaptureChangedRows) níže. |

> [!NOTE] 
> Vyhledávání systému Azure se podporuje jenom ověřování systému SQL Server. Pokud budete potřebovat podporu pro ověřování Azure Active Directory hesla, hlasovat prosím pro tento [UserVoice návrhu](https://feedback.azure.com/forums/263029-azure-search/suggestions/33595465-support-azure-active-directory-password-authentica).

## <a name="create-an-azure-sql-indexer"></a>Vytvořením indexeru. Azure SQL

1. Vytvoření zdroje dat:

   ```
    POST https://myservice.search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }
   ```

   Můžete získat připojovací řetězec z [portál Azure](https://portal.azure.com); použít `ADO.NET connection string` možnost.

2. Pokud již nemáte, vytvořte cílový index Azure Search. Můžete vytvořit index pomocí [portál](https://portal.azure.com) nebo [vytvořit Index rozhraní API](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Zkontrolujte, zda je schéma cílový index kompatibilní se schématem zdrojové tabulky – viz [mapování mezi SQL a služba Azure search datové typy](#TypeMapping).

3. Vytvořte indexeru tak, že ho pojmenujete a odkazování na index zdrojové a cílové dat:

    ```
    POST https://myservice.search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }
    ```

Indexer vytvořené v tomto případě nemá plán. Automaticky spustí, až když je vytvořena. Můžete ho spustit znovu v současně pomocí **spustit indexer** žádost:

    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2016-09-01
    api-key: admin-key

Můžete přizpůsobit několik aspektů indexer chování, například velikost dávky a kolik dokumentů mohou být přeskočeny, než se nezdaří spuštění indexeru. Další informace najdete v tématu [vytvoření rozhraní API Indexer](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

Budete muset povolit službám Azure připojit k vaší databázi. V tématu [připojení z Azure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) pokyny o tom, jak to udělat.

Ke sledování indexer stavu a provádění historii (počet položek indexed, selhání atd.), použijte **indexer stav** žádost:

    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2016-09-01
    api-key: admin-key

Odpověď by měl vypadat takto:

    {
        "@odata.context":"https://myservice.search.windows.net/$metadata#Microsoft.Azure.Search.V2015_02_28.IndexerExecutionInfo",
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

Historie provádění obsahuje až 50 nedávno provedených spuštěních, které jsou seřazeny v opačném chronologickém pořadí (tak, aby poslední spuštění bude první v odpovědi).
Další informace o odpovědi můžete najít v [získání stavu indexeru](http://go.microsoft.com/fwlink/p/?LinkId=528198)

## <a name="run-indexers-on-a-schedule"></a>Indexery spouštět podle plánu
Můžete také uspořádat indexeru pravidelně spouštět podle plánu. Chcete-li to provést, přidejte **plán** vlastnost při vytváření nebo aktualizaci indexeru. Následující příklad ukazuje požadavek PUT aktualizovat indexer:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2016-09-01
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

**Interval** parametr je povinný. Interval odkazuje na čas mezi začátkem spuštěních dvě po sobě jdoucích indexer. Nejkratší povolený interval je 5 minut; nejdelší je jeden den. Musí být naformátovaná jako hodnota "hodnoty doby podle" XSD (omezená podmnožina [ISO 8601 trvání](http://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) hodnotu). Je tento vzor: `P(nD)(T(nH)(nM))`. Příklady: `PT15M` pro každých 15 minut, `PT2H` pro každé 2 hodiny.

Volitelné **startTime** Určuje, kdy by měla být zahájena naplánované spuštění. Pokud je vynechaný, použije se aktuální čas UTC. Tento čas může být v minulosti – naplánoval případ první spuštění jakoby indexeru je spuštěn nepřetržitě od čas spuštění.  

Najednou můžete spustit pouze jeden spuštění indexeru. Pokud indexeru je spuštěn, když její spuštění je naplánováno, odloží se provádění, dokud dalším naplánovaném čase.

Zvažte příklad udělat konkrétnější. Předpokládejme, že jsme následující hodinový plán nakonfigurovat:

    "schedule" : { "interval" : "PT1H", "startTime" : "2015-03-01T00:00:00Z" }

Stane se toto:

1. První spuštění indexeru spustí na nebo přibližně 1. března 2015 12:00 v noci UTC.
2. Předpokládejme, že spuštění tohoto trvá 20 minut (nebo kdykoli menší než 1 hodina).
3. Druhý provádění spustí na nebo přibližně 1. března 2015 1:00 ráno
4. Nyní předpokládejme, že spuštění tohoto trvá víc než jednu hodinu – například 70 minut – tak, aby jeho dokončení přibližně 2:10:00
5. Je teď 2:00:00, čas pro třetí provádění spustit. Ale protože druhý provádění z ráno 1 je stále spuštěna, třetí provedení je přeskočeno. Třetí provádění spustí ve 3 hodiny.

Můžete přidat, změnit nebo odstranit plán pro existujícího indexeru pomocí **PUT indexer** požadavku.

<a name="CaptureChangedRows"></a>

## <a name="capture-new-changed-and-deleted-rows"></a>Zachycení nové, změny a odstranění řádků

Služba Azure Search používá **přírůstkové indexování** -li se vyhnout opětovné indexování celou tabulku nebo zobrazení pokaždé, když spustí indexer. Vyhledávání systému Azure nabízí že dva změnit zásady detekce pro podporu přírůstkové indexování. 

### <a name="sql-integrated-change-tracking-policy"></a>Zásady sledování změn s integrací SQL
Pokud vaše databáze SQL podporuje [sledování změn](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server), doporučujeme používat **SQL integrované změnit způsob sledování**. Toto je zásada maximální efektivitou. Kromě toho umožňuje Azure Search k identifikaci odstraněných řádků bez nutnosti do tabulky přidat sloupec explicitní "obnovitelného odstranění".

#### <a name="requirements"></a>Požadavky 

+ Požadavky na databázi verze:
  * SQL Server 2012 SP3 nebo novější, pokud používáte systém SQL Server na virtuálních počítačích Azure.
  * Azure SQL Database verze 12, pokud používáte Azure SQL Database.
+ Pouze tabulky (žádná zobrazení). 
+ V databázi [povolit sledování změn](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server) pro tabulku. 
+ Žádný složené primární klíč (primární klíč obsahující více než jeden sloupec) v tabulce.  

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

Při použití zásady, sledování změn SQL integrované nezadávejte zásadami detekce odstraňování oddělení dat – tato zásada má integrovanou podporu pro identifikaci odstranit řádky. Ale pro odstranění být zjištěné "automagically", klíč dokumentu v indexu vyhledávání musí být stejný jako primární klíč v tabulce SQL. 

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>Zásady detekce změn horní meze

Tyto zásady detekce změn spoléhá na sloupec "horní mez" zaznamenávání verze nebo čas poslední aktualizace řádek. Pokud používáte zobrazení, musíte použít zásadu horní mez. Horní mez sloupec musí splňovat následující požadavky.

#### <a name="requirements"></a>Požadavky 

* Vloží všechny zadejte hodnotu pro sloupec.
* Všechny aktualizace k položce také změnit hodnotu pro sloupec.
* Hodnota v tomto sloupci se zvyšuje s každou insert nebo update.
* Dotazy s následující kde a klauzule ORDER BY se dají efektivně provádět: `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

> [!IMPORTANT] 
> Důrazně doporučujeme pomocí [rowversion](https://docs.microsoft.com/sql/t-sql/data-types/rowversion-transact-sql) datový typ sloupce horní mez. Pokud se používá jiný typ dat sledování změn není zaručeno zaznamenat všechny změny případě transakce provádění souběžně dotaz indexer. Při použití **rowversion** v konfiguraci s replikami jen pro čtení, musí odkazovat na primární replice indexeru. Scénáře synchronizace dat lze použít pouze primární repliku.

#### <a name="usage"></a>Využití

Použít zásadu horní mez, vytvořit nebo aktualizovat zdroj dat takto:

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
> Pokud zdrojová tabulka nemá index na sloupci horní mez, dotazy, které používá indexeru SQL může vypršení časového limitu. Konkrétně `ORDER BY [High Water Mark Column]` klauzule vyžaduje index efektivně spouštět, když tabulka obsahuje mnoho řádků.
>
>

Pokud dojde k vypršení časového limitu, můžete použít `queryTimeout` nastavení konfigurace indexer nastavit časový limit dotazu na hodnotu vyšší než 5 minut výchozí časový limit. Nastavit časový limit na 10 minut, například vytvořit nebo aktualizovat indexer s následující konfigurací:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

Můžete také zakázat `ORDER BY [High Water Mark Column]` klauzule. To však není doporučeno, protože pokud se spuštění indexeru je přerušen kvůli chybě, indexeru je znovu zpracovat všechny řádky Pokud později - běží, i v případě indexeru již zpracována téměř všechny řádky v čase, který byl přerušen. Chcete-li zakázat `ORDER BY` klauzule, použijte `disableOrderByHighWaterMarkColumn` nastavení v definici indexer:  

    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }

### <a name="soft-delete-column-deletion-detection-policy"></a>Softwarové zásady odstranit detekce odstranění sloupce
Při odstranění řádků ze zdrojové tabulky, pravděpodobně chcete odstranit tyto řádky z také indexu vyhledávání. Pokud chcete použít zásady sledování změn SQL integrované, to se stará za vás. Ale zásady sledování změn horní meze není vám pomůžou s odstraněných řádků. Co dělat?

Pokud se fyzicky odebrání řádky z tabulky Azure Search nemá žádný způsob, jak odvození přítomnost záznamy, které už existují.  Můžete však technika "soft odstranění" logicky odstranění řádků bez odstranění z tabulky. Přidá sloupec na vaše řádky tabulky nebo zobrazení a označit jako odstranit pomocí sloupce.

Při použití konfigurace soft odstranění techniku, můžete určit zásadu obnovitelného odstranění takto při vytváření nebo aktualizaci zdroje dat:

    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }

**SoftDeleteMarkerValue** musí být řetězec – použít řetězcovou reprezentaci skutečnou hodnotu. Například pokud máte sloupec celé číslo, které jsou označeny odstraněných řádků s hodnotou 1, použijte `"1"`. Pokud BIT sloupec, kde jsou označené odstraněných řádků logickou hodnotu true, použijte `"True"`.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-and-azure-search-data-types"></a>Mapování mezi datové typy SQL a Azure Search
| Datový typ SQL. | Cílový index povolené typy polí | Poznámky |
| --- | --- | --- |
| Bit |Edm.Boolean, Edm.String | |
| int, smallint, tinyint |Edm.Int32, Edm.Int64, Edm.String | |
| bigint |Edm.Int64, Edm.String | |
| skutečné, float |Edm.Double, Edm.String | |
| Smallmoney peníze desítková číslice |Edm.String |Vyhledávání systému Azure nepodporuje převod decimal typy do Edm.Double, protože by to ztratit přesnost |
| Char, nchar, varchar, nvarchar |Edm.String<br/>Collection(Edm.String) |Řetězec SQL lze použít k naplnění Collection(Edm.String) pole, pokud řetězec představuje pole JSON řetězců: `["red", "white", "blue"]` |
| smalldatetime, datetime, datetime2, date, datetimeoffset |Edm.DateTimeOffset, Edm.String | |
| uniqueidentifer |Edm.String | |
| Geography |Edm.GeographyPoint |Jsou podporovány pouze geography instance typu bodu s SRID 4326 (což je výchozí hodnota) |
| ROWVERSION |neuvedeno |Verze řádku sloupce nelze uložit do indexu vyhledávání, ale mohou být použity pro sledování změn |
| Doba, časový interval, binary, varbinary, image, xml, geometry, typy CLR |neuvedeno |Nepodporuje se |

## <a name="configuration-settings"></a>Nastavení konfigurace
SQL indexer zpřístupňuje několik nastavení konfigurace:

| Nastavení | Typ dat | Účel | Výchozí hodnota |
| --- | --- | --- | --- |
| queryTimeout |řetězec |Nastaví časový limit pro spuštění dotazu SQL |5 minut ("00: 05:00") |
| disableOrderByHighWaterMarkColumn |BOOL |Způsobí, že příkaz jazyka SQL, které používají zásady horní mez k vynechejte klauzuli ORDER by. V tématu [zásad horní mez](#HighWaterMarkPolicy) |false (nepravda) |

Toto nastavení se použije v `parameters.configuration` objektu v definici indexer. Pokud chcete nastavit časový limit dotazu na 10 minut, například vytvořit nebo aktualizovat indexer s následující konfigurací:

    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }

## <a name="faq"></a>Nejčastější dotazy

**Otázka: je možné použít indexer Azure SQL s databází SQL spuštěn na virtuální počítače IaaS v Azure?**

Ano. Však musíte povolit svoji službu vyhledávání pro připojení k vaší databázi. Další informace najdete v tématu [konfigurovat spojení z indexer Azure Search na SQL Server na virtuálním počítači Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md).

**Otázka: je možné použít indexer Azure SQL s databází SQL místní aplikace?**

Ne přímo. Není doporučujeme nebo podporu přímé připojení, jak je to proto se vyžaduje otevření databází pro provoz sítě Internet. Zákazníci proběhlo úspěšně tento scénář použití most technologií, jako je Azure Data Factory. Další informace najdete v tématu [nabízet data do indexu Azure Search pomocí Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-azure-search-connector).

**Otázka: je možné použít indexer Azure SQL s databázemi než SQL Server spuštěnou v IaaS v Azure?**

Ne. Tento scénář nepodporujeme, protože nebyly testovány indexeru se žádné databáze než SQL Server.  

**Otázka: je možné vytvořit několik indexerů spuštěna podle plánu?**

Ano. Ale pouze jeden indexer může být spuštěn v jednom uzlu najednou. Pokud budete potřebovat k současnému spuštění více indexery, vezměte v úvahu škálování služby vyhledávání k více než jednu jednotku vyhledávání.

**Otázka: funguje s vliv indexer pracovním vytížením můj dotaz?**

Ano. Spuštění indexeru na jednom z uzlů v vaši službu vyhledávání a tento uzel prostředky jsou sdílené mezi indexování a obsluhuje přenosy dotazu a další žádosti o rozhraní API. Pokud spustíte zatížení s intenzivním indexování a dotazů a dojde k vysokou míru 503 chyby nebo odezvy roste, vezměte v úvahu [škálování služby vyhledávání](search-capacity-planning.md).

**Otázka: je možné používat sekundární repliku v [clusteru převzetí služeb při selhání](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview) jako zdroj dat?**

To záleží na okolnostech. Pro úplnou indexování tabulku nebo zobrazení, můžete použít sekundární repliku. 

Pro přírůstkové indexování změn podporuje Azure Search, dvě zásady detekce: SQL integrované změnit sledováním a horní mez.

U replik, jen pro čtení databáze SQL nepodporuje sledování integrované změn. Proto je nutné použít zásady horní mez. 

Standardní doporučujeme použít rowversion datový typ pro sloupec horní mez. Pomocí rowversion však spoléhá na SQL Database `MIN_ACTIVE_ROWVERSION` funkci, která není podporována u replik, jen pro čtení. Indexer proto musí odkazovat na primární repliku, pokud používáte rowversion.

Pokud se pokusíte použít rowversion na repliku jen pro čtení, zobrazí se následující chyba: 

    "Using a rowversion column for change tracking is not supported on secondary (read-only) availability replicas. Please update the datasource and specify a connection to the primary availability replica.Current database 'Updateability' property is 'READ_ONLY'".

**Otázka: je možné použít jako alternativní, bez rowversion sloupec pro sledování změn horní meze?**

Není doporučeno. Pouze **rowversion** umožňuje pro synchronizaci dat spolehlivé. Nicméně, v závislosti na logice aplikace může být bezpečné pokud:

+ Můžete zajistit, že při spuštění indexeru neexistují nezpracovaných transakce v tabulce, který je právě indexovaný (například všechny tabulky, které aktualizace dojít, protože batch na plán a plán indexer Azure Search je nastaven na překrývající se k aktualizaci tabulky naplánovat).  

+ Pravidelně provedením úplné nové indexování a pokračovat tam zmeškaných řádky. 
