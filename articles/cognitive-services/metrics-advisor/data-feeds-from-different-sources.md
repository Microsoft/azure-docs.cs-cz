---
title: Postup přidání datových kanálů z různých zdrojů do Poradce pro metriky
titleSuffix: Azure Cognitive Services
description: Přidání různých datových kanálů do poradce metrik
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: mbullwin
ms.openlocfilehash: c4d1d23da5fd9678cc5b9477ddeed0daf4f5ac36
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96348615"
---
# <a name="add-data-feeds-from-different-data-sources-to-metrics-advisor"></a>Přidání datových kanálů z různých zdrojů dat do Poradce pro metriky

V tomto článku najdete nastavení a požadavky pro propojení různých typů zdrojů dat s poradcem pro metriky. Nezapomeňte si přečíst [svoje data](how-tos/onboard-your-data.md) a Naučte se, jak se naučit klíčové pojmy pro používání vašich dat pomocí Poradce metrik. 

## <a name="supported-authentication-types"></a>Podporované typy ověřování

| Typy ověřování | Description |
| ---------------------|-------------|
|**Basic** | Budete muset být schopni zadat základní parametry pro přístup ke zdrojům dat. Například připojovací řetězec nebo klíč. Správci datového kanálu můžou tyto přihlašovací údaje zobrazit. |
| **AzureManagedIdentity** | [Spravované identity](../../active-directory/managed-identities-azure-resources/overview.md) prostředků Azure je funkce Azure Active Directory. Poskytuje služby Azure s automaticky spravovanou identitou ve službě Azure AD. Identitu můžete použít k ověření pro libovolnou službu, která podporuje ověřování Azure AD.|
| **AzureSQLConnectionString**| Uložte připojovací řetězec AzureSQL jako **entitu přihlašovacích údajů** v poradci metriky a použijte ji přímo při připojování dat metrik. Pouze správci entity pověření mohou zobrazit tyto přihlašovací údaje, ale umožňují oprávněným divákům vytvářet datové kanály, aniž by museli znát podrobnosti o přihlašovacích údajích. |
| **DataLakeGen2SharedKey**| Svůj klíč účtu Data Lake uložte jako **entitu s přihlašovacími** údaji v poradci metriky a použijte ji přímo při připojování dat metrik. Pouze správci entity pověření mohou zobrazit tyto přihlašovací údaje, ale umožňují oprávněným divákům vytvořit datový kanál, aniž by museli znát podrobnosti přihlašovacích údajů.|
| **Instanční objekt**| Uložte instanční objekt jako **entitu přihlašovacích údajů** v poradci metriky a použijte ji přímo při připojování dat metrik. Přihlašovací údaje můžou zobrazit jenom správci entit přihlašovacích údajů, ale povolují oprávněným divákům vytvářet datový kanál, aniž by museli znát podrobnosti přihlašovacích údajů.|
| **Instanční objekt z trezoru klíčů**|Instanční objekt uložte do trezoru klíčů jako **entitu s přihlašovacími** údaji v Advisoru a použijte ji přímo při připojování dat metrik. Přihlašovací údaje můžou zobrazit jenom správci **entity s přihlašovacími** údaji, ale můžou také opustit možnosti vytváření datových kanálů, aniž by museli znát podrobné přihlašovací údaje. |

## <a name="data-sources-supported-and-corresponding-authentication-types"></a>Podporované zdroje dat a odpovídající typy ověřování


| Zdroje dat | Typy ověřování |
|-------------| ---------------------|
|[**Application Insights Azure**](#appinsights)|  Basic |
|[**Azure Blob Storage (JSON)**](#blob) | Basic<br>Spravovaná identita|
|[**Azure Cosmos DB (SQL)**](#cosmosdb) | Basic |
|[**Azure Data Explorer (Kusto)**](#kusto) | Basic<br>Spravovaná identita|
|[**Azure Data Lake Storage Gen2**](#adl) | Basic<br>DataLakeGen2SharedKey<br>Instanční objekt<br>Instanční objekt z trezoru klíčů<br> |
|[**Azure SQL Database/SQL Server**](#sql) | Basic<br>Spravovaná identita<br>Instanční objekt<br>Instanční objekt z trezoru klíčů<br>AzureSQLConnectionString
|[**Table Storage Azure**](#table) | Basic | 
|[**ElasticSearch**](#es) | Basic |
|[**Požadavek http**](#http) | Basic | 
|[**InfluxDB (InfluxQL)**](#influxdb) | Basic |
|[**MongoDB**](#mongodb) | Basic |
|[**MySQL**](#mysql) | Basic |
|[**PostgreSQL**](#pgsql)| Basic|

Vytvořte **entitu přihlašovacích údajů** a použijte ji k ověřování vašich zdrojů dat. Následující oddíly určují parametry požadované pro *základní* ověřování. 

## <a name="span-idappinsightsazure-application-insightsspan"></a><span id="appinsights">Application Insights Azure</span>

* **ID aplikace**: používá se k identifikaci této aplikace při použití rozhraní Application Insights API. Chcete-li získat ID aplikace, postupujte následovně:

    1. Z prostředku Application Insights klikněte na přístup přes rozhraní API.

    2. Zkopírujte ID aplikace generované do pole **ID aplikace** v poradci metriky. 
    
    Další informace najdete v [dokumentaci k Azure bot Service](/azure/bot-service/bot-service-resources-app-insights-keys#application-id) .

* **Klíč rozhraní API**: pro přístup k tomuto prostředku používají aplikace mimo prohlížeč klíče rozhraní API. Klíč rozhraní API získáte následujícím způsobem:

    1. V prostředku Application Insights klikněte na přístup přes rozhraní API.

    2. Klikněte na vytvořit klíč rozhraní API.

    3. Zadejte krátký popis, zaškrtněte možnost Read telemetrie a klikněte na tlačítko vygenerovat klíč.

    4. Zkopírujte klíč rozhraní API do pole **klíč rozhraní API** v poradci metriky.

* **Dotaz**: protokoly Azure Application Insights jsou postavené na Azure Průzkumník dat a Azure monitor dotazy protokolu používají verzi stejného dotazovacího jazyka Kusto. [Dokumentace jazyka dotazu Kusto](/azure/data-explorer/kusto/query/) má všechny podrobnosti o jazyku a měl by být vaším primárním prostředkem pro zápis dotazu na Application Insights. 


## <a name="span-idblobazure-blob-storage-jsonspan"></a><span id="blob">Azure Blob Storage (JSON)</span>

* **Připojovací řetězec**: informace o načtení tohoto řetězce najdete v článku [připojovacího řetězce](../../storage/common/storage-configure-connection-string.md#configure-a-connection-string-for-an-azure-storage-account) služby Azure Blob Storage.

* **Container**: Poradce pro metriky očekává data časové řady uložená jako soubory objektů BLOB (jeden objekt blob na časové razítko) v rámci jednoho kontejneru. Toto je pole s názvem kontejneru.

* **Šablona objektu BLOB**: Jedná se o šablonu názvů souborů objektů BLOB. Příklad: `/%Y/%m/X_%Y-%m-%d-%h-%M.json`. Podporovány jsou následující parametry:
  * `%Y` je rok formátovaný jako `yyyy`
  * `%m` je měsíc formátovaný jako `MM`
  * `%d` je den formátovaný jako `dd`
  * `%h` je hodina formátovaná jako `HH`
  * `%M` je minuta formátovaná jako `mm`

* **Verze formátu JSON**: definuje schéma dat v souborech JSON. V současné době podporuje poradce metriky dvě verze:
  
  * V1 (výchozí hodnota)

      Akceptují se jenom *hodnoty* a *název* metriky. Například:
    
      ``` JSON
      {"count":11, "revenue":1.23}
      ```

  * v2

      Jsou také přijaty *dimenze* metriky a *časové razítko* . Například:
      
      ``` JSON
      [
        {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
        {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
      ]
      ```

Pro každý soubor JSON je povoleno pouze jedno časové razítko. 

## <a name="span-idcosmosdbazure-cosmos-db-sqlspan"></a><span id="cosmosdb">Azure Cosmos DB (SQL)</span>

* **Připojovací řetězec**: připojovací řetězec pro přístup k vašemu Azure Cosmos DB. Dá se najít v prostředku Cosmos DB v části **klíče**. 
* **Databáze**: databáze, pro kterou se má dotazovat. Najdete ho na stránce **Procházet** v části **kontejnery** .
* **ID kolekce**: ID kolekce, vůči které se má dotazovat. Najdete ho na stránce **Procházet** v části **kontejnery** .
* **Dotaz SQL**: dotaz SQL, který umožňuje získat a formulovat data do multidimenzionálních dat časových řad. V dotazu můžete použít `@StartTime` `@EndTime` proměnné a. Měly by být naformátované: `yyyy-MM-dd HH:mm:ss` .

    Vzorový dotaz:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = @StartTime
    ```
    
    Vzorový dotaz na datový řez z 2019/12/12:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = '2019-12-12 00:00:00'
    ```

## <a name="span-idkustoazure-data-explorer-kustospan"></a><span id="kusto">Azure Data Explorer (Kusto)</span>

* **Připojovací řetězec**: Poradce metrik podporuje přístup k Azure Průzkumník dat (Kusto) pomocí ověřování aplikací Azure AD. Budete muset vytvořit a zaregistrovat aplikaci Azure AD a potom ji autorizovat pro přístup k databázi Azure Průzkumník dat. Postup získání připojovacího řetězce najdete v dokumentaci k [Azure Průzkumník dat](/azure/data-explorer/provision-azure-ad-app) .

* **Dotaz**: informace o tom, jak získat a formulovat data do multidimenzionálních dat časových řad, najdete v tématu [dotazovací jazyk Kusto](/azure/data-explorer/kusto/query) . V dotazu můžete použít `@StartTime` `@EndTime` proměnné a. Měly by být naformátované: `yyyy-MM-dd HH:mm:ss` .

## <a name="span-idadlazure-data-lake-storage-gen2span"></a><span id="adl">Azure Data Lake Storage Gen2</span>

* **Název účtu**: název účtu vašeho Azure Data Lake Storage Gen2. Dá se najít v prostředku účtu Azure Storage (Azure Data Lake Storage Gen2) v **přístupových klíčích**.

* **Klíč účtu**: Zadejte prosím název účtu pro přístup k vašemu Azure Data Lake Storage Gen2. Najdete ho v nastavení **přístupových klíčů** v prostředku Azure Storage účtu (Azure Data Lake Storage Gen2).

* **Název systému souborů (kontejner)**: Poradce pro metriky očekává, že vaše data časových řad budou uložená jako soubory objektů BLOB (jeden objekt blob na časové razítko) v rámci jednoho kontejneru. Toto je pole s názvem kontejneru. Najdete ho v instanci vašeho účtu Azure Storage (Azure Data Lake Storage Gen2) a klikněte na kontejnery v části Služba BLOB Service.

* **Šablona adresáře**: Jedná se o šablonu adresáře souboru objektu BLOB. Například: */%Y/%m/%d*. Podporovány jsou následující parametry:
  * `%Y` je rok formátovaný jako `yyyy`
  * `%m` je měsíc formátovaný jako `MM`
  * `%d` je den formátovaný jako `dd`
  * `%h` je hodina formátovaná jako `HH`
  * `%M` je minuta formátovaná jako `mm`

* **Šablona souboru**: Jedná se o šablonu souboru objektu BLOB. Například: *X_% Y-% m-% d-% h-% M.jsna*. Podporovány jsou následující parametry:
  * `%Y` je rok formátovaný jako `yyyy`
  * `%m` je měsíc formátovaný jako `MM`
  * `%d` je den formátovaný jako `dd`
  * `%h` je hodina formátovaná jako `HH`
  * `%M` je minuta formátovaná jako `mm`

V současné době metrika podporuje schéma dat v souborech JSON následujícím způsobem. Například:

``` JSON
[
  {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
  {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
]
```
<!--
## <span id="eventhubs">Azure Event Hubs</span>

* **Connection String**: This can be found in 'Shared access policies' in your Event Hubs instance. Also for the 'EntityPath', it could be found by clicking into your Event Hubs instance and clicking at 'Event Hubs' in 'Entities' blade. Items that listed can be input as EntityPath. 

* **Consumer Group**: A [consumer group](../../event-hubs/event-hubs-features.md#consumer-groups) is a view (state, position, or offset) of an entire event hub.
Event Hubs use the latest offset of a consumer group to consume (subscribe from) the data from data source. Therefore a dedicated consumer group should be created for one data feed in your Metrics Advisor instance.

* **Timestamp**: Metrics Advisor uses the Event Hubs timestamp as the event timestamp if the user data source does not contain a timestamp field.
The timestamp field must match one of these two formats:

    * "YYYY-MM-DDTHH:MM:SSZ" format;

    * Number of seconds or milliseconds from the epoch of 1970-01-01T00:00:00Z.

    No matter which timestamp field it left aligns to granularity.For example, if timestamp is "2019-01-01T00:03:00Z", granularity is 5 minutes, then Metrics Advisor aligns the timestamp to "2019-01-01T00:00:00Z". If the event timestamp is "2019-01-01T00:10:00Z",  Metrics Advisor uses the timestamp directly without any alignment.
-->
## <a name="span-idsqlazure-sql-database--sql-serverspan"></a><span id="sql">Azure SQL Database | SQL Server</span>

* **Připojovací řetězec**: Poradce pro metriky přijímá [připojovací řetězec ve stylu ADO.NET](/dotnet/framework/data/adonet/connection-string-syntax) pro zdroj dat SQL serveru.

    Vzorový připojovací řetězec:

    ```
    Data Source=db-server.database.windows.net:[port];initial catalog=[database];User ID=[username];Password=[password];Connection Timeout=10ms;
    ```

* **Dotaz**: dotaz SQL, který umožňuje získat a formulovat data do multidimenzionálních dat časových řad. V dotazu můžete použít `@StartTime` proměnnou, která vám umožní získat očekávanou hodnotu metrik.

  * `@StartTime`: datum a čas ve formátu `yyyy-MM-dd HH:mm:ss`

    Vzorový dotaz:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = @StartTime
    ```
    
    Skutečný dotaz spuštěný pro datový řez 2019/12/12:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = '2019-12-12 00:00:00'
    ```

## <a name="span-idtableazure-table-storagespan"></a><span id="table">Table Storage Azure</span>

* **Připojovací řetězec**: Přečtěte si téma [zobrazení a zkopírování připojovacího řetězce](../../storage/common/storage-account-keys-manage.md?tabs=azure-portal&toc=%2fazure%2fstorage%2ftables%2ftoc.json#view-account-access-keys) , kde najdete informace o tom, jak načíst připojovací řetězec z Azure Table Storage.

* **Název tabulky**: Určete tabulku, u které se má dotazovat. Tato možnost je k dispozici ve vaší instanci účtu Azure Storage. V části **služba Table Service** klikněte na **tabulka** .

* **Dotaz na** V dotazu můžete použít `@StartTime` . `@StartTime` je nahrazen řetězcem formátu RRRR-MM-ddTHH: mm: ss ve skriptu.

    ``` mssql
    let StartDateTime = datetime(@StartTime); let EndDateTime = StartDateTime + 1d; 
    SampleTable | where Timestamp >= StartDateTime and Timestamp < EndDateTime | project Timestamp, Market, RPM
    ```

## <a name="span-ideselasticsearchspan"></a><span id="es">Elasticsearch</span>

* **Hostitel**: zadejte hlavního hostitele clusteru Elasticsearch.
* **Port**: Zadejte hlavní port clusteru Elasticsearch.
* **Autorizační hlavička**: zadejte hodnotu autorizační hlavičky clusteru Elasticsearch.
* **Dotaz**: zadejte dotaz pro získání dat. Zástupný symbol @StartTime je podporován. ( například když se data 2020-06-21T00:00:00Z ingestují, @StartTime = 2020-06-21T00:00:00)

## <a name="span-idhttphttp-requestspan"></a><span id="http">Požadavek HTTP</span>

* **Adresa URL požadavku**: adresa URL protokolu HTTP, která může vracet JSON. Zástupné symboly% Y,% m,% d,% h,% M jsou podporovány:% Y = rok ve formátu RRRR,% m = měsíc ve formátu MM,% d = den ve formátu DD,% h = hodina ve formátu HH,% M = minute ve formátu mm. Příklad: `http://microsoft.com/ProjectA/%Y/%m/X_%Y-%m-%d-%h-%M`.
* **Request – metoda HTTP**: použijte Get nebo post.
* **Hlavička žádosti**: může přidat základní ověřování. 
* **Datová část požadavku**: je podporována pouze datová část JSON. Zástupný symbol @StartTime je v datové části podporován. Odpověď by měla být v následujícím formátu JSON: [{"timestamp": "2018-01-01T00:00:00Z", "trh": "en-US", "Count": 11, "výnosy": 1,23}, {"časové razítko": "2018-01-01T00:00:00Z", "trh": "zh-CN", "Count": 22, "výnosy": 4,56}]. (např. když se data z 2020-06-21T00:00:00Z ingestují, @StartTime = 2020-06-21T00:00:00.0000000 + 00:00)

## <a name="span-idinfluxdbinfluxdb-influxqlspan"></a><span id="influxdb">InfluxDB (InfluxQL)</span>

* **Připojovací řetězec**: připojovací řetězec pro přístup k vašemu InfluxDB.
* **Databáze**: databáze, pro kterou se má dotazovat.
* **Dotaz**: dotaz pro získání a formulaci dat do multidimenzionálních dat časových řad pro přijímání.
* **Uživatelské jméno**: Toto je volitelné pro ověřování. 
* **Heslo**: Toto je volitelné pro ověřování. 

## <a name="span-idmongodbmongodbspan"></a><span id="mongodb">MongoDB</span>

* **Připojovací řetězec**: připojovací řetězec pro přístup k vašemu MongoDB.
* **Databáze**: databáze, pro kterou se má dotazovat.
* **Příkaz**: příkaz pro získání a formulaci dat do multidimenzionálních dat časových řad pro přijímání.

## <a name="span-idmysqlmysqlspan"></a><span id="mysql">MySQL</span>

* **Připojovací řetězec**: připojovací řetězec pro přístup k databázi MySQL.
* **Dotaz**: dotaz pro získání a formulaci dat do multidimenzionálních dat časových řad pro přijímání.

## <a name="span-idpgsqlpostgresqlspan"></a><span id="pgsql">PostgreSQL</span>

* **Připojovací řetězec**: připojovací řetězec pro přístup ke službě PostgreSQL DB.
* **Dotaz**: dotaz pro získání a formulaci dat do multidimenzionálních dat časových řad pro přijímání.

## <a name="next-steps"></a>Další kroky

* Při čekání na ingestování dat metrik do systému si přečtěte informace o [tom, jak spravovat konfigurace datového kanálu](how-tos/manage-data-feeds.md).
* Když se data metriky ingestují, můžete [nakonfigurovat metriky a jemně vyladit konfiguraci detekce](how-tos/configure-metrics.md).