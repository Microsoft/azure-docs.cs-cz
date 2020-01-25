---
title: Ingestování dat ve formátu JSON do Azure Průzkumník dat
description: Přečtěte si, jak ingestovat formátovaná data JSON do Azure Průzkumník dat.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: ef5c7de782d833aad96516d3e5357a0ed575a781
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722873"
---
# <a name="ingest-json-formatted-sample-data-into-azure-data-explorer"></a>Ingestování ukázkových dat ve formátu JSON do Azure Průzkumník dat

V tomto článku se dozvíte, jak ingestovat formátovaná data JSON do databáze Azure Průzkumník dat. Začnete s jednoduchými příklady nezpracovaných a mapovaných JSON, budete pokračovat na víceřádkové JSON a pak se rozhodnete komplexnější schémata JSON obsahující pole a slovníky. 

## <a name="prerequisites"></a>Požadavky

[Testovací cluster a databázi](create-cluster-database-portal.md)

## <a name="the-json-format"></a>Formát JSON

Azure Průzkumník dat podporuje dva formáty souborů JSON:
* `json`: řádek JSON oddělený čárkou. Každý řádek ve vstupních datech obsahuje přesně jeden záznam JSON.
* `multijson`: víceřádkové JSON. Analyzátor ignoruje oddělovače řádků a přečte záznam z předchozí pozice na konec platného formátu JSON.

### <a name="ingest-and-map-json-formatted-data"></a>Ingestování a mapování dat ve formátu JSON

Ingestování dat ve formátu JSON vyžaduje zadání *formátu* pomocí [vlastnosti](/azure/kusto/management/data-ingestion/index#ingestion-properties)ingestování. Ingestování dat JSON vyžaduje [mapování](/azure/kusto/management/mappings), které mapuje zdrojovou položku JSON na cílový sloupec. Při ingestování dat použijte vlastnost ingestovat `jsonMappingReference` nebo zadejte vlastnost ingestování `jsonMapping`. Tento článek bude používat vlastnost ingestování `jsonMappingReference`, která je předem definovaná v tabulce používané pro přijímání. V níže uvedených příkladech začne ingestování záznamů JSON jako nezpracovaných dat do tabulky s jedním sloupcem. Pak použijeme mapování k ingestování jednotlivých vlastností do mapovaného sloupce. 

### <a name="simple-json-example"></a>Příklad jednoduchého formátu JSON

Následující příklad je jednoduchý kód JSON s plochou strukturou. Data obsahují informace o teplotě a vlhkosti shromážděné několika zařízeními. Každý záznam je označený ID a časovým razítkem.

```json
{
    "timestamp": "2019-05-02 15:23:50.0369439",
    "deviceId": "2945c8aa-f13e-4c48-4473-b81440bb5ca2",
    "messageId": "7f316225-839a-4593-92b5-1812949279b3",
    "temperature": 31.0301639051317,
    "humidity": 62.0791099602725
}
```

## <a name="ingest-raw-json-records"></a>Ingestovat nezpracované záznamy JSON 

V tomto příkladu budete ingestovat záznamy JSON jako nezpracované data do tabulky s jedním sloupcem. Manipulace s daty pomocí dotazů a zásad aktualizace se provádí po ingestování dat.

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

Použijte dotazovací jazyk Kusto k ingestování dat ve formátu RAW JSON.

1. Přihlaste se k webu [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. Vyberte **Přidat cluster**.

1. V dialogovém okně **Přidat cluster** zadejte adresu URL clusteru ve formuláři `https://<ClusterName>.<Region>.kusto.windows.net/`a pak vyberte **Přidat**.

1. Vložte následující příkaz a vyberte **Spustit** pro vytvoření tabulky.

    ```Kusto
    .create table RawEvents (Event: dynamic)
    ```

    Tento dotaz vytvoří tabulku s jedním `Event` sloupcem s [dynamickým](/azure/kusto/query/scalar-data-types/dynamic) datovým typem.

1. Vytvořte mapování JSON.

    ```Kusto
    .create table RawEvents ingestion json mapping 'RawEventMapping' '[{"column":"Event","path":"$"}]'
    ```

    Tento příkaz vytvoří mapování a namapuje kořenovou cestu JSON `$` do sloupce `Event`.

1. Ingestování dat do `RawEvents` tabulky

    ```Kusto
    .ingest into table RawEvents h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=RawEventMapping)
    ```

    > [!NOTE]
    > To ukazuje příkazy ovládacího prvku `ingest` spouštěny přímo do koncového bodu stroje. V produkčních scénářích se pro službu Správa dat spouští ingestování pomocí klientských knihoven nebo datových připojení. Přečtěte si data ingestování [pomocí knihovny Pythonu v azure Průzkumník dat](/azure/data-explorer/python-ingest-data) a ingestujte [data pomocí sady Azure Průzkumník dat .NET Standard SDK](/azure/data-explorer/net-standard-ingest-data) , kde najdete podrobné informace o ingestování dat pomocí těchto klientských knihoven.

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

Slouží C# k ingestování dat ve formátu RAW JSON.

1. Vytvořte tabulku `RawEvents`.

    ```C#
    var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
    var kustoConnectionStringBuilder =
        new KustoConnectionStringBuilder(ingestUri)
        {
            FederatedSecurity = true,
            InitialCatalog = database,
            UserID = user,
            Password = password,
            Authority = tenantId
        };
    var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder);

    var table = "RawEvents";
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("Events", "System.Object"),
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. Vytvořte mapování JSON.
    
    ```C#
    var tableMapping = "RawEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                new JsonColumnMapping {ColumnName = "Events", JsonPath = "$"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```
    Tento příkaz vytvoří mapování a namapuje kořenovou cestu JSON `$` do sloupce `Event`.

1. Ingestování dat do `RawEvents` tabulky

    ```C#
    var ingestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/";
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var ingestConnectionStringBuilder =
        new KustoConnectionStringBuilder(ingestUri)
        {
            FederatedSecurity = true,
            InitialCatalog = database,
            UserID = user,
            Password = password,
            Authority = tenantId
        };
    var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(ingestConnectionStringBuilder);
    
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.json,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

> [!NOTE]
> Data se agregují podle [zásad dávkového](/azure/kusto/concepts/batchingpolicy)zpracování, což vede k latenci během několika minut.

# <a name="pythontabpython"></a>[Python](#tab/python)

Použijte Python k ingestování dat ve formátu RAW JSON.

1. Vytvořte tabulku `RawEvents`.

    ```Python
    KUSTO_URI = "https://<ClusterName>.<Region>.kusto.windows.net:443/"
    KCSB_DATA = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_URI, AAD_TENANT_ID)
    KUSTO_CLIENT = KustoClient(KCSB_DATA)
    TABLE = "RawEvents"

    CREATE_TABLE_COMMAND = ".create table " + TABLE + " (Events: dynamic)"
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_TABLE_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Vytvořte mapování JSON.

    ```Python
    MAPPING = "RawEventMapping"
    CREATE_MAPPING_COMMAND = ".create table " + TABLE + " ingestion json mapping '" + MAPPING + """' '[{"column":"Event","path":"$"}]'"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Ingestování dat do `RawEvents` tabulky

    ```Python
    INGEST_URI = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/"
    KCSB_INGEST = KustoConnectionStringBuilder.with_aad_device_authentication(INGEST_URI, AAD_TENANT_ID)
    INGESTION_CLIENT = KustoIngestClient(KCSB_INGEST)
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    > [!NOTE]
    > Data se agregují podle [zásad dávkového](/azure/kusto/concepts/batchingpolicy)zpracování, což vede k latenci během několika minut.

---

## <a name="ingest-mapped-json-records"></a>Ingestování mapovaných záznamů JSON

V tomto příkladu ingestují data záznamů JSON. Každá vlastnost JSON je namapována na jeden sloupec v tabulce. 

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

1. Vytvoří novou tabulku s podobným schématem pro vstupní data JSON. Tuto tabulku použijeme pro všechny následující příklady a příkazy pro přijímání příkazů. 

    ```Kusto
    .create table Events (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)
    ```

1. Vytvořte mapování JSON.

    ```Kusto
    .create table Events ingestion json mapping 'FlatEventMapping' '[{"column":"Time","path":"$.timestamp"},{"column":"Device","path":"$.deviceId"},{"column":"MessageId","path":"$.messageId"},{"column":"Temperature","path":"$.temperature"},{"column":"Humidity","path":"$.humidity"}]'
    ```

    V tomto mapování, jak je definováno ve schématu tabulky, se položky `timestamp` ingestují do sloupce `Time` jako `datetime` datových typů.

1. Ingestování dat do `Events` tabulky

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=FlatEventMapping)
    ```

    Soubor ' Simple. JSON ' obsahuje několik záznamů JSON oddělených řádky. Formát je `json`a mapování použité v příkazu ingestování je `FlatEventMapping`, které jste vytvořili.

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

1. Vytvoří novou tabulku s podobným schématem pro vstupní data JSON. Tuto tabulku použijeme pro všechny následující příklady a příkazy pro přijímání příkazů. 

    ```C#
    var table = "Events";
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("Time", "System.DateTime"),
                Tuple.Create("Device", "System.String"),
                Tuple.Create("MessageId", "System.String"),
                Tuple.Create("Temperature", "System.Double"),
                Tuple.Create("Humidity", "System.Double"),
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. Vytvořte mapování JSON.

    ```C#
    var tableMapping = "FlatEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                        new JsonColumnMapping {ColumnName = "Time", JsonPath = "$.timestamp"},
                        new JsonColumnMapping {ColumnName = "Device", JsonPath = "$.deviceId"},
                        new JsonColumnMapping {ColumnName = "MessageId", JsonPath = "$.messageId"},
                        new JsonColumnMapping {ColumnName = "Temperature", JsonPath = "$.temperature"},
                        new JsonColumnMapping {ColumnName = "Humidity", JsonPath = "$.humidity"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```

    V tomto mapování, jak je definováno ve schématu tabulky, se položky `timestamp` ingestují do sloupce `Time` jako `datetime` datových typů.    

1. Ingestování dat do `Events` tabulky

    ```C#
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.json,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

    Soubor ' Simple. JSON ' obsahuje několik záznamů JSON oddělených řádky. Formát je `json`a mapování použité v příkazu ingestování je `FlatEventMapping`, které jste vytvořili.

# <a name="pythontabpython"></a>[Python](#tab/python)

1. Vytvoří novou tabulku s podobným schématem pro vstupní data JSON. Tuto tabulku použijeme pro všechny následující příklady a příkazy pro přijímání příkazů. 

    ```Python
    TABLE = "RawEvents"
    CREATE_TABLE_COMMAND = ".create table " + TABLE + " (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)"
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_TABLE_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Vytvořte mapování JSON.

    ```Python
    MAPPING = "FlatEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.timestamp"},{"column":"Device","path":"$.deviceId"},{"column":"MessageId","path":"$.messageId"},{"column":"Temperature","path":"$.temperature"},{"column":"Humidity","path":"$.humidity"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Ingestování dat do `Events` tabulky

    ```Python
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    Soubor ' Simple. JSON ' obsahuje několik záznamů JSON oddělených řádky. Formát je `json`a mapování použité v příkazu ingestování je `FlatEventMapping`, které jste vytvořili.    
---

## <a name="ingest-multi-lined-json-records"></a>Ingestování záznamů JSON s více řádky

V tomto příkladu budete přijímat víceřádkové záznamy JSON. Každá vlastnost JSON je namapována na jeden sloupec v tabulce. Soubor ' víceřádkovd. JSON ' obsahuje několik odsazených záznamů JSON. `multijson` formátu dává pokyn stroji ke čtení záznamů pomocí struktury JSON.

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

Ingestování dat do `Events` tabulky

```Kusto
.ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=FlatEventMapping)
```

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

Ingestování dat do `Events` tabulky

```C#
var tableMapping = "FlatEventMapping";
var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
var properties =
    new KustoQueuedIngestionProperties(database, table)
    {
        Format = DataSourceFormat.multijson,
        IngestionMappingReference = tableMapping
    };

ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
```

# <a name="pythontabpython"></a>[Python](#tab/python)

Ingestování dat do `Events` tabulky

```Python
MAPPING = "FlatEventMapping"
BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.JSON?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
INGESTION_CLIENT.ingest_from_blob(
    BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
```

---

## <a name="ingest-json-records-containing-arrays"></a>Ingestování záznamů JSON obsahující pole

Datové typy pole jsou seřazené kolekce hodnot. Ingestování pole JSON se provádí pomocí [zásad aktualizace](/azure/kusto/management/update-policy). KÓD JSON se ingestuje jako mezilehlá tabulka. Zásada aktualizace spustí předdefinovanou funkci v tabulce `RawEvents` a znovu ingestuje výsledky do cílové tabulky. Budeme ingestovat data s následující strukturou:

```json
{
    "records": 
    [
        {
            "timestamp": "2019-05-02 15:23:50.0000000",
            "deviceId": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71",
            "messageId": "7f316225-839a-4593-92b5-1812949279b3",
            "temperature": 31.0301639051317,
            "humidity": 62.0791099602725
        },
        {
            "timestamp": "2019-05-02 15:23:51.0000000",
            "deviceId": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71",
            "messageId": "57de2821-7581-40e4-861e-ea3bde102364",
            "temperature": 33.7529423105311,
            "humidity": 75.4787976739364
        }
    ]
}
```

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

1. Vytvořte funkci `update policy`, která rozšíří kolekci `records` tak, aby každá hodnota v kolekci přijímala samostatný řádek pomocí operátoru `mv-expand`. Jako zdrojovou tabulku použijeme tabulku `RawEvents` a jako cílovou tabulku `Events`.

    ```Kusto
    .create function EventRecordsExpand() {
        RawEvents
        | mv-expand records = Event
        | project
            Time = todatetime(records["timestamp"]),
            Device = tostring(records["deviceId"]),
            MessageId = tostring(records["messageId"]),
            Temperature = todouble(records["temperature"]),
            Humidity = todouble(records["humidity"])
    }
    ```

1. Schéma přijaté funkcí musí odpovídat schématu cílové tabulky. Ke kontrole schématu použijte operátor `getschema`.

    ```Kusto
    EventRecordsExpand() | getschema
    ```

1. Přidejte zásadu aktualizace do cílové tabulky. Tato zásada automaticky spustí dotaz u všech nově zpracovaných dat v `RawEvents` zprostředkující tabulce a ingestuje výsledky do tabulky `Events`. Definujte zásadu s nulovým uchováváním, aby se předešlo zachování přechodné tabulky.

    ```Kusto
    .alter table Events policy update @'[{"Source": "RawEvents", "Query": "EventRecordsExpand()", "IsEnabled": "True"}]'
    ```

1. Ingestování dat do `RawEvents` tabulky

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=RawEventMapping)
    ```

1. Zkontrolujte data v `Events` tabulce.

    ```Kusto
    Events
    ```

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

1. Vytvořte funkci Update, která rozšíří kolekci `records` tak, aby každá hodnota v kolekci přijímala samostatný řádek pomocí operátoru `mv-expand`. Jako zdrojovou tabulku použijeme tabulku `RawEvents` a jako cílovou tabulku `Events`.   

    ```C#
    var command =
        CslCommandGenerator.GenerateCreateFunctionCommand(
            "EventRecordsExpand",
            "UpdateFunctions",
            string.Empty,
            null,
            @"RawEvents
                | mv-expand records = Event
                | project
                    Time = todatetime(records['timestamp']),
                    Device = tostring(records['deviceId']),
                    MessageId = tostring(records['messageId']),
                    Temperature = todouble(records['temperature']),
                    Humidity = todouble(records['humidity'])",
            ifNotExists: false);

    kustoClient.ExecuteControlCommand(command);
    ```

    > [!NOTE]
    > Schéma přijaté funkcí musí odpovídat schématu cílové tabulky.

1. Přidejte zásadu aktualizace do cílové tabulky. Tato zásada automaticky spustí dotaz u všech nově zpracovaných dat v `RawEvents` zprostředkující tabulce a ingestuje výsledky do tabulky `Events`. Definujte zásadu s nulovým uchováváním, aby se předešlo zachování přechodné tabulky.

    ```C#
    var command =
        ".alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]";

    kustoClient.ExecuteControlCommand(command);
    ```

1. Ingestování dat do `RawEvents` tabulky

    ```C#
    var table = "RawEvents";
    var tableMapping = "RawEventMapping";
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.multijson,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```
    
1. Zkontrolujte data v `Events` tabulce.

# <a name="pythontabpython"></a>[Python](#tab/python)

1. Vytvořte funkci Update, která rozšíří kolekci `records` tak, aby každá hodnota v kolekci přijímala samostatný řádek pomocí operátoru `mv-expand`. Jako zdrojovou tabulku použijeme tabulku `RawEvents` a jako cílovou tabulku `Events`.   

    ```Python
    CREATE_FUNCTION_COMMAND = 
        '''.create function EventRecordsExpand() { 
            RawEvents
            | mv-expand records = Event
            | project
                Time = todatetime(records["timestamp"]),
                Device = tostring(records["deviceId"]),
                MessageId = tostring(records["messageId"]),
                Temperature = todouble(records["temperature"]),
                Humidity = todouble(records["humidity"])
            }'''
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_FUNCTION_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

    > [!NOTE]
    > Schéma přijaté funkcí musí odpovídat schématu cílové tabulky.

1. Přidejte zásadu aktualizace do cílové tabulky. Tato zásada automaticky spustí dotaz u všech nově zpracovaných dat v `RawEvents` zprostředkující tabulce a ingestuje výsledky do tabulky `Events`. Definujte zásadu s nulovým uchováváním, aby se předešlo zachování přechodné tabulky.

    ```Python
    CREATE_UPDATE_POLICY_COMMAND = 
        """.alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_UPDATE_POLICY_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Ingestování dat do `RawEvents` tabulky

    ```Python
    TABLE = "RawEvents"
    MAPPING = "RawEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

1. Zkontrolujte data v `Events` tabulce.

---    

## <a name="ingest-json-records-containing-dictionaries"></a>Ingestování záznamů JSON obsahujících slovníky

Slovník strukturovaného formátu JSON obsahuje páry klíč-hodnota. Záznamy JSON přecházejí z mapování ingestování pomocí logického výrazu v `JsonPath`. Data můžete ingestovat s následující strukturou:

```json
{
    "event": 
    [
        {
            "Key": "timestamp",
            "Value": "2019-05-02 15:23:50.0000000"
        },
        {
            "Key": "deviceId",
            "Value": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71"
        },
        {
            "Key": "messageId",
            "Value": "7f316225-839a-4593-92b5-1812949279b3"
        },
        {
            "Key": "temperature",
            "Value": 31.0301639051317
        },
        {
            "Key": "humidity",
            "Value": 62.0791099602725
        }
    ]
}
```

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

1. Vytvořte mapování JSON.

    ```Kusto
    .create table Events ingestion json mapping 'KeyValueEventMapping' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'
    ```

1. Ingestování dat do `Events` tabulky

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=KeyValueEventMapping)
    ```

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

1. Vytvořte mapování JSON.

    ```C#
    var tableName = "Events";
    var tableMapping = "KeyValueEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                        new JsonColumnMapping {ColumnName = "Time", JsonPath = "$.event[?(@.Key == 'timestamp')]"},
                        new JsonColumnMapping {ColumnName = "Device", JsonPath = "$.event[?(@.Key == 'deviceId')]"},
                        new JsonColumnMapping {ColumnName = "MessageId", JsonPath = "$.event[?(@.Key == 'messageId')]"},
                        new JsonColumnMapping {ColumnName = "Temperature", JsonPath = "$.event[?(@.Key == 'temperature')]"},
                        new JsonColumnMapping {ColumnName = "Humidity", JsonPath = "$.event[?(@.Key == 'humidity')]"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. Ingestování dat do `Events` tabulky

    ```C#
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.multijson,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

# <a name="pythontabpython"></a>[Python](#tab/python)

1. Vytvořte mapování JSON.

    ```Python
    MAPPING = "KeyValueEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Ingestování dat do `Events` tabulky

     ```Python
    MAPPING = "KeyValueEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)u
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

---    

## <a name="next-steps"></a>Další kroky

* [Přehled příjmu dat](ingest-data-overview.md)
* [Zápis dotazů](write-queries.md)