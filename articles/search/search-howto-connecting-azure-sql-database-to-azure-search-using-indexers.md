---
title: Hledání ve službě Azure SQL data
titleSuffix: Azure Cognitive Search
description: Import dat z Azure SQL Database nebo spravované instance SQL pomocí indexerů pro fulltextové vyhledávání ve službě Azure Kognitivní hledání. Tento článek se zabývá připojeními, konfigurací indexeru a přijímáním dat.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/12/2020
ms.openlocfilehash: 631f5afbac4337cd0852f46ac4a336107f042397
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91331637"
---
# <a name="connect-to-and-index-azure-sql-content-using-an-azure-cognitive-search-indexer"></a>Připojení a indexování obsahu Azure SQL pomocí indexeru Azure Kognitivní hledání

Než budete moct zadat dotaz na [index služby Azure kognitivní hledání](search-what-is-an-index.md), musíte ho naplnit Vašimi daty. Pokud data umístěná v Azure SQL Database nebo spravované instanci SQL **kognitivní hledání indexer Azure pro Azure SQL Database** (nebo **Azure SQL indexer** pro krátké) může automatizovat proces indexování, což znamená méně kódu pro zápis a méně infrastruktury pro péči o.

Tento článek se věnuje mechanismu použití [indexerů](search-indexer-overview.md), ale také popisuje funkce, které jsou k dispozici pouze pro Azure SQL Database nebo spravované instance SQL (například integrované sledování změn). 

Kromě Azure SQL Database a SQL Managed instance poskytuje Azure Kognitivní hledání indexery pro [Azure Cosmos DB](search-howto-index-cosmosdb.md), [úložiště objektů BLOB v Azure](search-howto-indexing-azure-blob-storage.md)a [úložiště tabulek Azure](search-howto-indexing-azure-tables.md). Pokud chcete požádat o podporu pro jiné zdroje dat, zadejte svůj názor na [Fórum Azure kognitivní hledání Feedback](https://feedback.azure.com/forums/263029-azure-search/).

## <a name="indexers-and-data-sources"></a>Indexery a zdroje dat

**Zdroj dat** určuje, která data se mají indexovat, přihlašovací údaje pro přístup k datům a zásady, které efektivně identifikují změny v datech (nové, upravené nebo odstraněné řádky). Je definován jako nezávislý prostředek, aby jej bylo možné použít více indexery.

**Indexer** je prostředek, který spojuje jeden zdroj dat s cíleným indexem vyhledávání. Indexer se používá následujícími způsoby:

* Proveďte jednorázovou kopii dat pro naplnění indexu.
* Aktualizuje index o změny ve zdroji dat podle plánu.
* Podle potřeby aktualizujte index spuštěním na vyžádání.

Jeden indexer může využívat pouze jednu tabulku nebo zobrazení, ale pokud chcete naplnit více indexů vyhledávání, můžete vytvořit více indexerů. Další informace o konceptech najdete v tématu [operace indexeru: Typický pracovní postup](/rest/api/searchservice/Indexer-operations#typical-workflow).

Můžete nastavit a nakonfigurovat indexer Azure SQL pomocí:

* Průvodce importem dat v [Azure Portal](https://portal.azure.com)
* Sada Azure Kognitivní hledání [.NET SDK](/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)
* [REST API](/rest/api/searchservice/indexer-operations) kognitivní hledání Azure

V tomto článku použijeme REST API k vytváření **indexerů** a **zdrojů dat**.

## <a name="when-to-use-azure-sql-indexer"></a>Kdy použít službu Azure SQL indexer
V závislosti na několika faktorech týkajících se vašich dat může být použití služby Azure SQL indexer možná nebo nevhodné. Pokud vaše data vyhovují následujícím požadavkům, můžete použít službu Azure SQL indexer.

| Kritéria | Podrobnosti |
|----------|---------|
| Data pocházejí z jedné tabulky nebo zobrazení. | Pokud jsou data rozptýlená napříč více tabulkami, můžete vytvořit jedno zobrazení dat. Pokud ale použijete zobrazení, nebudete moct pomocí SQL Server integrovaného zjišťování změn aktualizovat index pomocí přírůstkových změn. Další informace najdete v tématu [zachytávání změněných a odstraněných řádků](#CaptureChangedRows) níže. |
| Datové typy jsou kompatibilní. | Většina typů SQL se ale v indexu Azure Kognitivní hledání nepodporuje. Seznam najdete v tématu [mapování datových typů](#TypeMapping). |
| Synchronizace dat v reálném čase není nutná. | Indexer může tabulku znovu indexovat každých pět minut. Pokud se data často mění a změny se musí projevit v indexu během několika sekund nebo v jednom minutách, doporučujeme použít sadu [REST API](/rest/api/searchservice/AddUpdate-or-Delete-Documents) nebo [.NET SDK](./search-get-started-dotnet.md) k přímému nabízení aktualizovaných řádků. |
| Je možné přírůstkové indexování. | Pokud máte rozsáhlou sadu dat a plánujete spustit indexer podle plánu, Azure Kognitivní hledání musí být schopný efektivně identifikovat nové, změněné nebo odstraněné řádky. Nepřírůstkové indexování je povolené jenom v případě, že indexování provádíte na vyžádání (ne podle plánu) nebo je vyplněné méně než 100 000 řádků. Další informace najdete v tématu [zachytávání změněných a odstraněných řádků](#CaptureChangedRows) níže. |

> [!NOTE] 
> Azure Kognitivní hledání podporuje pouze ověřování SQL Server. Pokud požadujete podporu pro Azure Active Directory ověřování hesla, Hlasujte prosím pro tento [Návrh UserVoice](https://feedback.azure.com/forums/263029-azure-search/suggestions/33595465-support-azure-active-directory-password-authentica).

## <a name="create-an-azure-sql-indexer"></a>Vytvoření indexeru Azure SQL

1. Vytvoření zdroje dat:

   ```
    POST https://myservice.search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "Server=tcp:<your server>.database.windows.net,1433;Database=<your database>;User ID=<your user name>;Password=<your password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;" },
        "container" : { "name" : "name of the table or view that you want to index" }
    }
   ```

   Připojovací řetězec může následovat po jednom z následujících formátů:
    1. Připojovací řetězec můžete získat z [Azure Portal](https://portal.azure.com); použijte `ADO.NET connection string` možnost.
    1. Spravovaný připojovací řetězec identity, který neobsahuje klíč účtu v následujícím formátu: `Initial Catalog|Database=<your database name>;ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Sql/servers/<your SQL Server name>/;Connection Timeout=connection timeout length;` . Pokud chcete použít tento připojovací řetězec, postupujte podle pokynů pro [nastavení připojení indexeru k Azure SQL Database pomocí spravované identity](search-howto-managed-identities-sql.md).

2. Pokud ho ještě nemáte, vytvořte cílový index Azure Kognitivní hledání. Index můžete vytvořit pomocí [portálu](https://portal.azure.com) nebo [rozhraní API pro vytvoření indexu](/rest/api/searchservice/Create-Index). Ujistěte se, že schéma cílového indexu je kompatibilní se schématem zdrojové tabulky – viz [mapování mezi datovými typy SQL a SQL rozpoznávání vyhledávacích dat v Azure](#TypeMapping).

3. Vytvořte indexer tak, že mu udělíte název a odkazujete na zdroj dat a cílový index:

   ```
    POST https://myservice.search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "name" : "myindexer",
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name"
    }
   ```

Indexer vytvořený tímto způsobem nemá plán. Automaticky se spustí při vytvoření. Můžete ji kdykoli znovu spustit pomocí žádosti **indexeru Run** :

```
    POST https://myservice.search.windows.net/indexers/myindexer/run?api-version=2020-06-30
    api-key: admin-key
```

Můžete přizpůsobit několik aspektů chování indexeru, jako je velikost dávky, a počet dokumentů, které je možné přeskočit předtím, než se spuštění indexeru nezdařilo. Další informace najdete v tématu [Vytvoření rozhraní API pro indexer](/rest/api/searchservice/Create-Indexer).

Možná budete muset službě Azure dovolit připojení k vaší databázi. Pokyny k tomu, jak to udělat, najdete v tématu [připojení z Azure](../azure-sql/database/firewall-configure.md) .

Pokud chcete monitorovat stav indexeru a historii spouštění (počet položek indexovaných, selhání atd.), použijte požadavek na **stav indexeru** :

```
    GET https://myservice.search.windows.net/indexers/myindexer/status?api-version=2020-06-30
    api-key: admin-key
```

Odpověď by měla vypadat nějak takto:

```
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
```

Historie spouštění obsahuje až 50 posledních dokončených provedení, která jsou seřazena v obráceném chronologickém pořadí (takže se poslední spuštění v odpovědi zařadí jako první).
Další informace o odpovědi najdete v části [získání stavu indexeru](/rest/api/searchservice/get-indexer-status) .

## <a name="run-indexers-on-a-schedule"></a>Spustit indexery podle plánu
Indexer je také možné uspořádat tak, aby běžel pravidelně podle plánu. Chcete-li to provést, přidejte při vytváření nebo aktualizaci indexeru vlastnost **Schedule** . Následující příklad ukazuje požadavek PUT na aktualizaci indexeru:

```
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }
```

Parametr **interval** je povinný. Tento interval odkazuje na čas mezi začátkem dvou po sobě jdoucích spuštění indexeru. Nejmenší povolený interval je 5 minut. nejdelší je jeden den. Musí být formátován jako hodnota XSD "dayTimeDuration" (omezená podmnožina hodnoty [Duration ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Vzor pro tuto hodnotu je: `P(nD)(T(nH)(nM))` . Příklady: `PT15M` každých 15 minut každé `PT2H` 2 hodiny.

Další informace o definování plánů indexerů najdete v tématu [postup plánování indexerů pro Azure kognitivní hledání](search-howto-schedule-indexers.md).

<a name="CaptureChangedRows"></a>

## <a name="capture-new-changed-and-deleted-rows"></a>Zaznamenání nových, změněných a odstraněných řádků

Azure Kognitivní hledání používá **přírůstkové indexování** k tomu, aby nemusela znovu indexovat celou tabulku nebo zobrazit při každém spuštění indexeru. Azure Kognitivní hledání poskytuje dvě zásady zjišťování změn pro podporu přírůstkového indexování. 

### <a name="sql-integrated-change-tracking-policy"></a>Zásady integrovaného Change Tracking SQL
Pokud vaše databáze SQL podporuje [sledování změn](/sql/relational-databases/track-changes/about-change-tracking-sql-server), doporučujeme použít **integrované zásady Change Tracking SQL**. Toto je nejúčinnější zásada. Kromě toho umožňuje službě Azure Kognitivní hledání identifikovat odstraněné řádky, aniž byste museli do tabulky přidat explicitní sloupec "obnovitelné odstranění".

#### <a name="requirements"></a>Požadavky 

+ Požadavky na verzi databáze:
  * Pokud používáte SQL Server na virtuálních počítačích Azure, SQL Server 2012 SP3 a novější.
  * Azure SQL Database nebo spravované instance SQL
+ Pouze tabulky (žádná zobrazení). 
+ V databázi [Povolte sledování změn](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server) pro tabulku. 
+ V tabulce není žádný složený primární klíč (primární klíč, který obsahuje více než jeden sloupec).  

#### <a name="usage"></a>Využití

Chcete-li použít tuto zásadu, vytvořte nebo aktualizujte zdroj dat takto:

```
    {
        "name" : "myazuresqldatasource",
        "type" : "azuresql",
        "credentials" : { "connectionString" : "connection string" },
        "container" : { "name" : "table or view name" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SqlIntegratedChangeTrackingPolicy"
      }
    }
```

Pokud používáte zásady pro sledování změn integrované v SQL, nezadávejte samostatné zásady zjišťování odstranění dat – tato zásada má integrovanou podporu pro identifikaci odstraněných řádků. Aby se ale u odstranění zjistila možnost automagic, klíč dokumentu v indexu hledání musí být stejný jako primární klíč v tabulce SQL. 

> [!NOTE]  
> Při použití [Truncate Table](/sql/t-sql/statements/truncate-table-transact-sql) k odebrání velkého počtu řádků z tabulky SQL musí být indexer [resetován](/rest/api/searchservice/reset-indexer) , aby obnovil stav sledování změn, aby bylo možné vybrat odstranění řádků.

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>Zásada pro detekci změny vysokého měřítka

Tato zásada detekce změn spoléhá na sloupec horní meze, ve kterém se zachytí verze nebo čas poslední aktualizace řádku. Pokud používáte zobrazení, je nutné použít zásady vysoké značky. Sloupec horních značek musí splňovat následující požadavky.

#### <a name="requirements"></a>Požadavky 

* Všechna vložení určují hodnotu sloupce.
* Všechny aktualizace položky také změní hodnotu sloupce.
* Hodnota tohoto sloupce se zvětšuje s každým vložením nebo aktualizací.
* Dotazy s následujícími klauzulemi WHERE a ORDER BY mohou být provedeny efektivně: `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

> [!IMPORTANT] 
> Důrazně doporučujeme používat datový typ [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) pro sloupec horních značek. Pokud se použije jiný datový typ, sledování změn není zaručené zachytit všechny změny v přítomnosti transakcí prováděných souběžně s dotazem indexeru. Pokud používáte **rowversion** v konfiguraci s replikami jen pro čtení, je nutné, aby indexer odkazoval na primární repliku. Pro scénáře synchronizace dat lze použít pouze primární repliku.

#### <a name="usage"></a>Využití

Chcete-li použít zásady vysoké značky, vytvořte nebo aktualizujte zdroj dat takto:

```
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
```

> [!WARNING]
> Pokud zdrojová tabulka neobsahuje index ve sloupci horních značek, můžou vyprší časový limit dotazů používaných indexerem SQL. Konkrétně `ORDER BY [High Water Mark Column]` klauzule vyžaduje, aby se rejstřík spouštěl efektivně, pokud tabulka obsahuje mnoho řádků.
>
>

<a name="convertHighWaterMarkToRowVersion"></a>

##### <a name="converthighwatermarktorowversion"></a>convertHighWaterMarkToRowVersion

Pokud používáte datový typ [rowversion](/sql/t-sql/data-types/rowversion-transact-sql) pro sloupec horních značek, zvažte použití `convertHighWaterMarkToRowVersion` nastavení konfigurace indexeru. `convertHighWaterMarkToRowVersion` provede dvě věci:

* V dotazu SQL indexeru použijte datový typ rowversion pro sloupec horních značek. Použití správného datového typu vylepšuje výkon dotazů indexeru.
* Odečíst 1 z hodnoty rowversion před spuštěním dotazu indexeru. Zobrazení s 1 a mnoha spojeními mohou mít řádky s duplicitními rowversion hodnotami. Odečtení 1 zajistí, že dotaz indexeru nenalezne tyto řádky.

Pokud chcete tuto funkci povolit, vytvořte nebo aktualizujte indexer s následující konfigurací:

```
    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "convertHighWaterMarkToRowVersion" : true } }
    }
```

<a name="queryTimeout"></a>

##### <a name="querytimeout"></a>queryTimeout

Pokud dojde k chybám časového limitu, můžete použít `queryTimeout` nastavení konfigurace indexeru k nastavení časového limitu dotazu na hodnotu vyšší, než je výchozí časový limit 5 minut. Chcete-li například nastavit časový limit na 10 minut, vytvořte nebo aktualizujte indexer s následující konfigurací:

```
    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }
```

<a name="disableOrderByHighWaterMarkColumn"></a>

##### <a name="disableorderbyhighwatermarkcolumn"></a>disableOrderByHighWaterMarkColumn

Můžete také zakázat `ORDER BY [High Water Mark Column]` klauzuli. Nicméně to nedoporučujeme, protože pokud je provádění indexeru přerušeno chybou, indexer musí znovu zpracovat všechny řádky, pokud se spustí později – a to i v případě, že indexer již zpracoval téměř všechny řádky v době přerušení. K zakázání této `ORDER BY` klauzule použijte `disableOrderByHighWaterMarkColumn` nastavení v definici indexeru:  

```
    {
     ... other indexer definition properties
     "parameters" : {
            "configuration" : { "disableOrderByHighWaterMarkColumn" : true } }
    }
```

### <a name="soft-delete-column-deletion-detection-policy"></a>Zásady detekce nepodmíněného odstranění sloupce
Pokud jsou řádky odstraněny ze zdrojové tabulky, pravděpodobně budete chtít odstranit tyto řádky i z indexu vyhledávání. Pokud používáte zásady pro sledování změn integrovaného se systémem SQL, je to pro vás velmi důležité. Zásady pro sledování změn s vysokou vodou ale neumožňují odstraňovat řádky. Co dělat?

Pokud jsou řádky fyzicky odebrány z tabulky, Azure Kognitivní hledání nemá žádný způsob, jak odvodit přítomnost záznamů, které již neexistují.  Pomocí techniky "obnovitelného odstranění" ale můžete logicky odstranit řádky, aniž byste je museli odebírat z tabulky. Umožňuje přidat sloupec do tabulky nebo zobrazit a označit řádky jako odstraněné pomocí tohoto sloupce.

Při použití techniky obnovitelného odstranění můžete při vytváření nebo aktualizaci zdroje dat určit zásady obnovitelného odstranění následujícím způsobem:

```
    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }
```

**SoftDeleteMarkerValue** musí být řetězec – použijte řetězcovou reprezentaci vaší skutečné hodnoty. Například pokud máte sloupec s celými čísly, kde jsou odstraněné řádky označeny hodnotou 1, použijte `"1"` . Pokud máte BITOVÝ sloupec, ve kterém jsou odstraněné řádky označeny logickou hodnotou true, použijte řetězcový literál `True` nebo `true` nezáleží na velikosti písmen.

<a name="TypeMapping"></a>

## <a name="mapping-between-sql-and-azure-cognitive-search-data-types"></a>Mapování mezi datovými typy SQL a Azure Kognitivní hledání
| Datový typ SQL | Povolené typy polí indexu cíle | Poznámky |
| --- | --- | --- |
| bit |EDM. Boolean, Edm. String | |
| int, smallint, tinyint |EDM. Int32, Edm. Int64, Edm. String | |
| bigint |EDM. Int64, Edm. String | |
| Real, float |EDM. Double, Edm. String | |
| smallmoney, desetinné číslo v penězích |Edm.String |Azure Kognitivní hledání nepodporuje převod desetinných typů na EDM. Double, protože by došlo ke ztrátě přesnosti. |
| char, nchar, varchar, nvarchar |Edm.String<br/>Collection(Edm.String) |Řetězec SQL lze použít k naplnění pole Collection (EDM. String), pokud řetězec představuje pole JSON řetězců: `["red", "white", "blue"]` |
| smalldatetime, DateTime, datetime2, Date, DateTimeOffset |EDM. DateTimeOffset, Edm. String | |
| uniqueidentifer |Edm.String | |
| geografické |Edm.GeographyPoint |Podporují se jenom geografické instance typu POINT s SRID 4326 (což je výchozí nastavení). |
| rowversion |Není k dispozici |Sloupce verze řádku nelze uložit do indexu hledání, ale lze je použít ke sledování změn. |
| čas, TimeSpan, binární, varbinary, image, XML, geometrie, typy CLR |Není k dispozici |Nepodporováno |

## <a name="configuration-settings"></a>Nastavení konfigurace
SQL indexer zpřístupňuje několik nastavení konfigurace:

| Nastavení | Datový typ | Účel | Výchozí hodnota |
| --- | --- | --- | --- |
| queryTimeout |řetězec |Nastaví časový limit pro spuštění dotazu SQL. |5 minut ("00:05:00") |
| disableOrderByHighWaterMarkColumn |bool |Způsobí, že dotaz SQL, který používá zásada vysoké značky, k vynechání klauzule ORDER BY. Zobrazit [zásady vysoké značky](#HighWaterMarkPolicy) |false (nepravda) |

Tato nastavení se používají v `parameters.configuration` objektu v definici indexeru. Chcete-li například nastavit časový limit dotazu na 10 minut, vytvořte nebo aktualizujte indexer s následující konfigurací:

```
    {
      ... other indexer definition properties
     "parameters" : {
            "configuration" : { "queryTimeout" : "00:10:00" } }
    }
```

## <a name="faq"></a>Časté otázky

**Otázka: můžu použít službu Azure SQL indexer s databázemi SQL běžícími na virtuálních počítačích s IaaS v Azure?**

Ano. Je ale potřeba, abyste službě Search povolili připojení k vaší databázi. Další informace najdete v tématu [Konfigurace připojení ze služby azure kognitivní hledání indexer pro SQL Server na virtuálním počítači Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md).

**Otázka: můžu použít službu Azure SQL indexer s databázemi SQL běžícími místně?**

Ne přímo. Nedoporučujeme ani podporovat přímé připojení, protože by to vyžadovalo otevření vašich databází pro internetový provoz. Zákazníci s tímto scénářem úspěšně nastavili pomocí přemostění technologií, jako je Azure Data Factory. Další informace najdete v tématu [nabízení dat do indexu služby Azure kognitivní hledání pomocí Azure Data Factory](../data-factory/v1/data-factory-azure-search-connector.md).

**Otázka: můžu použít službu Azure SQL indexer s jinými databázemi než SQL Server spuštěnou v IaaS v Azure?**

No. Tento scénář nepodporujeme, protože nebyl testován indexer na jiné databáze než SQL Server.  

**Otázka: je možné vytvořit více indexerů, které jsou spuštěny podle plánu?**

Ano. V jednom uzlu ale může běžet jenom jeden indexer. Pokud potřebujete více indexerů současně spuštěných, zvažte možnost škálovat službu vyhledávání na více než jednu jednotku vyhledávání.

**Otázka: spouští indexer vliv na moje úlohy dotazů?**

Ano. Indexer běží na jednom z uzlů ve službě vyhledávání a prostředky tohoto uzlu se sdílejí mezi indexováním a obsluhou přenosů dotazů a dalších požadavků na rozhraní API. Pokud spouštíte náročné úlohy indexování a dotazování a dojde k vysoké míře 503 chyb nebo zvýšení doby odezvy, zvažte možnost [škálování služby vyhledávání](search-capacity-planning.md).

**Otázka: je možné použít sekundární repliku v [clusteru s podporou převzetí služeb při selhání](../azure-sql/database/auto-failover-group-overview.md) jako zdroj dat?**

To závisí na okolnostech. Pro úplné indexování tabulky nebo zobrazení můžete použít sekundární repliku. 

Pro přírůstkové indexování podporuje Azure Kognitivní hledání dvě zásady detekce změn: integrované sledování změn SQL a horní mez.

V replikách jen pro čtení SQL Database nepodporuje integrované sledování změn. Proto je nutné použít zásady vysokého měřítka. 

Naše standardní doporučení je použití datového typu rowversion pro sloupec horních značek. Použití rowversion ale spoléhá na `MIN_ACTIVE_ROWVERSION` funkci, která není podporovaná v replikách jen pro čtení. Proto je nutné, aby indexer naodkazoval na primární repliku, pokud používáte rowversion.

Pokud se pokusíte použít rowversion v replice, která je jen pro čtení, zobrazí se následující chyba: 

"Použití sloupce rowversion pro sledování změn není podporováno u sekundárních replik dostupnosti (jen pro čtení). Aktualizujte zdroj dat a zadejte připojení k primární replice dostupnosti. Vlastnost ' aktualizovatelné ' aktuální databáze je ' READ_ONLY '.

**Otázka: mohu použít alternativní sloupec, který není rowversion pro sledování změn ve vysokém měřítku?**

Nedoporučuje se. Jenom **rowversion** umožňuje spolehlivou synchronizaci dat. V závislosti na logice aplikace ale může být bezpečná, pokud:

+ Můžete zajistit, aby se při spuštění indexeru nenašly žádné nedokončené transakce v tabulce, která je indexovaná (například všechny aktualizace tabulky se stanou dávkou v plánu, a plán služby Azure Kognitivní hledání indexer je nastaven tak, aby se předešlo překrývání s plánem aktualizace tabulky).  

+ Pravidelně provedete celý index, který vybírá všechny zmeškané řádky.