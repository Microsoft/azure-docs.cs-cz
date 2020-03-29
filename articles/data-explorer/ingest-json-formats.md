---
title: Ingestování dat formátovaných JSON do Průzkumníka dat Azure
description: Přečtěte si, jak ingestovat data formátovaná JSON do Průzkumníka dat Azure.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: d293b76e004d693813a074cb8551a86cb3c0bec2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76772333"
---
# <a name="ingest-json-formatted-sample-data-into-azure-data-explorer"></a>Ingestování ukázkových dat formátovaných JSON do Průzkumníka dat Azure

Tento článek ukazuje, jak ingestovat json formátovaná data do databáze Azure Data Explorer. Začnete s jednoduchými příklady raw a mapované JSON, pokračovat v multi-lined JSON a pak řešit složitější schémata JSON obsahující pole a slovníky.  Příklady podrobně popisují proces ingestování dat ve formátu JSON pomocí dotazovacího jazyka Kusto (KQL), C#nebo Pythonu. Příkazy ovládacího `ingest` prvku dotazovacího jazyka Kusto jsou spouštěny přímo do koncového bodu motoru. V produkčních scénářích se ingestování provádí do služby Správa dat pomocí klientských knihoven nebo datových připojení. Čtení [dat ingestování pomocí knihovny Pythonu Průzkumníka dat Azure](/azure/data-explorer/python-ingest-data) a [ingestování dat pomocí Azure Data Explorer .NET Standard SDK](/azure/data-explorer/net-standard-ingest-data) pro ochoz u procházení týkající se ingestování dat s těmito klientskými knihovnami.

## <a name="prerequisites"></a>Požadavky

[Testovací cluster a databázi](create-cluster-database-portal.md)

## <a name="the-json-format"></a>Formát JSON

Azure Data Explorer podporuje dva formáty souborů JSON:
* `json`: Linka oddělena JSON. Každý řádek ve vstupních datech má přesně jeden záznam JSON.
* `multijson`: Vícelematý JSON. Analyzátor ignoruje oddělovače řádků a přečte záznam z předchozí pozice na konec platného JSON.

### <a name="ingest-and-map-json-formatted-data"></a>Ingestování a mapování dat formátovaných JSON

Požití dat ve formátu JSON vyžaduje zadání *formátu* pomocí [vlastnosti ingestování](/azure/kusto/management/data-ingestion/index#ingestion-properties). Požití dat JSON vyžaduje [mapování](/azure/kusto/management/mappings), které mapuje zdrojovou položku JSON na cílový sloupec. Při ingestování dat použijte předdefinovanou `jsonMappingReference` vlastnost ingestování `jsonMapping`nebo zadejte vlastnost ingestování. Tento článek bude `jsonMappingReference` používat vlastnost ingestování, která je předem definována v tabulce používané pro ingestování. V níže uvedených příkladech začneme ingestováním záznamů JSON jako nezpracovaných dat do tabulky jednoho sloupce. Pak použijeme mapování k ingestování každé vlastnosti do jejího namapovaného sloupce. 

### <a name="simple-json-example"></a>Jednoduchý příklad JSON

Následující příklad je jednoduchý JSON s plochou strukturou. Data mají informace o teplotě a vlhkosti, shromážděné několika zařízeními. Každý záznam je označen ID a časovým razítkem.

```json
{
    "timestamp": "2019-05-02 15:23:50.0369439",
    "deviceId": "2945c8aa-f13e-4c48-4473-b81440bb5ca2",
    "messageId": "7f316225-839a-4593-92b5-1812949279b3",
    "temperature": 31.0301639051317,
    "humidity": 62.0791099602725
}
```

## <a name="ingest-raw-json-records"></a>Ingestovat surové záznamy JSON 

V tomto příkladu ingestujete záznamy JSON jako nezpracovaná data do tabulky jednoho sloupce. Manipulace s daty, pomocí dotazů a zásady aktualizace se provádí po požití dat.

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

Pomocí dotazovacího jazyka Kusto můžete ingestovat data ve formátu JSON.

1. Přihlaste [https://dataexplorer.azure.com](https://dataexplorer.azure.com)se do .

1. Vyberte **Přidat cluster**.

1. V dialogovém okně **Přidat cluster** zadejte `https://<ClusterName>.<Region>.kusto.windows.net/`adresu URL clusteru ve formuláři a vyberte **Přidat**.

1. Vložit do následujícího příkazu a kliknutím na **Spustit** tabulku vytvořte.

    ```Kusto
    .create table RawEvents (Event: dynamic)
    ```

    Tento dotaz vytvoří tabulku `Event` s jedním sloupcem [dynamického](/azure/kusto/query/scalar-data-types/dynamic) datového typu.

1. Vytvořte mapování JSON.

    ```Kusto
    .create table RawEvents ingestion json mapping 'RawEventMapping' '[{"column":"Event","path":"$"}]'
    ```

    Tento příkaz vytvoří mapování a mapuje kořenovou `$` `Event` cestu JSON na sloupec.

1. Ingestovat data `RawEvents` do tabulky.

    ```Kusto
    .ingest into table RawEvents h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=RawEventMapping)
    ```

# <a name="c"></a>[C #](#tab/c-sharp)

Použití Jazyka C# k ingestování dat ve formátu JSON.

1. Vytvořte `RawEvents` tabulku.

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
    Tento příkaz vytvoří mapování a mapuje kořenovou `$` `Event` cestu JSON na sloupec.

1. Ingestovat data `RawEvents` do tabulky.

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
> Data jsou agregována podle [zásad dávkování](/azure/kusto/concepts/batchingpolicy), což má za následek latenci několika minut.

# <a name="python"></a>[Python](#tab/python)

Pomocí Pythonu můžete ingestovat data ve formátu JSON.

1. Vytvořte `RawEvents` tabulku.

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

1. Ingestovat data `RawEvents` do tabulky.

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
    > Data jsou agregována podle [zásad dávkování](/azure/kusto/concepts/batchingpolicy), což má za následek latenci několika minut.

---

## <a name="ingest-mapped-json-records"></a>Ingestování mapovaných záznamů JSON

V tomto příkladu ingestujete data záznamů JSON. Každá vlastnost JSON je mapována na jeden sloupec v tabulce. 

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

1. Vytvořte novou tabulku s podobným schématem jako vstupní data JSON. Tuto tabulku použijeme pro všechny následující příklady a příkazy ingestování. 

    ```Kusto
    .create table Events (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)
    ```

1. Vytvořte mapování JSON.

    ```Kusto
    .create table Events ingestion json mapping 'FlatEventMapping' '[{"column":"Time","path":"$.timestamp"},{"column":"Device","path":"$.deviceId"},{"column":"MessageId","path":"$.messageId"},{"column":"Temperature","path":"$.temperature"},{"column":"Humidity","path":"$.humidity"}]'
    ```

    V tomto mapování, jak je definováno schématem tabulky, budou `timestamp` položky `Time` `datetime` přijaty do sloupce jako datové typy.

1. Ingestovat data `Events` do tabulky.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=FlatEventMapping)
    ```

    Soubor 'simple.json' má několik řádků oddělených záznamů JSON. Formát je `json`a mapování použité v příkazu `FlatEventMapping` ingestovat je vámi vytvořené.

# <a name="c"></a>[C #](#tab/c-sharp)

1. Vytvořte novou tabulku s podobným schématem jako vstupní data JSON. Tuto tabulku použijeme pro všechny následující příklady a příkazy ingestování. 

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

    V tomto mapování, jak je definováno schématem tabulky, budou `timestamp` položky `Time` `datetime` přijaty do sloupce jako datové typy.    

1. Ingestovat data `Events` do tabulky.

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

    Soubor 'simple.json' má několik řádků oddělených záznamů JSON. Formát je `json`a mapování použité v příkazu `FlatEventMapping` ingestovat je vámi vytvořené.

# <a name="python"></a>[Python](#tab/python)

1. Vytvořte novou tabulku s podobným schématem jako vstupní data JSON. Tuto tabulku použijeme pro všechny následující příklady a příkazy ingestování. 

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

1. Ingestovat data `Events` do tabulky.

    ```Python
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    Soubor 'simple.json' má několik řádků oddělených záznamů JSON. Formát je `json`a mapování použité v příkazu `FlatEventMapping` ingestovat je vámi vytvořené.    
---

## <a name="ingest-multi-lined-json-records"></a>Ingestování vícelemařovaných záznamů JSON

V tomto příkladu ingestujete vícelematé záznamy JSON. Každá vlastnost JSON je mapována na jeden sloupec v tabulce. Soubor multilined.json má několik odsazených záznamů JSON. Formát `multijson` říká motoru číst záznamy podle struktury JSON.

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

Ingestovat data `Events` do tabulky.

```Kusto
.ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=FlatEventMapping)
```

# <a name="c"></a>[C #](#tab/c-sharp)

Ingestovat data `Events` do tabulky.

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

# <a name="python"></a>[Python](#tab/python)

Ingestovat data `Events` do tabulky.

```Python
MAPPING = "FlatEventMapping"
BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.JSON?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
INGESTION_CLIENT.ingest_from_blob(
    BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
```

---

## <a name="ingest-json-records-containing-arrays"></a>Ingestování záznamů JSON obsahujících pole

Datové typy pole jsou uspořádanou kolekcí hodnot. Požití pole JSON se provádí pomocí [zásad aktualizace](/azure/kusto/management/update-policy). JSON je požitý jako-je na mezilehlé tabulky. Zásady aktualizace spustí předdefinovanou `RawEvents` funkci v tabulce, převádí výsledky do cílové tabulky. Budeme ingestovat data s následující strukturou:

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

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

1. Vytvořte `update policy` funkci, která rozšiřuje `records` kolekci tak, aby každá hodnota v `mv-expand` kolekci obdrží samostatný řádek pomocí operátoru. Tabulku použijeme `RawEvents` jako zdrojovou `Events` tabulku a jako cílovou tabulku.

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

1. Schéma přijaté funkcí musí odpovídat schématu cílové tabulky. Pomocí `getschema` operátoru zkontrolujte schéma.

    ```Kusto
    EventRecordsExpand() | getschema
    ```

1. Přidejte zásady aktualizace do cílové tabulky. Tato zásada automaticky spustí dotaz na všechna nově `RawEvents` požitá data v `Events` zprostředkující tabulce a výsledky ingestuje do tabulky. Definujte zásady uchovávání informací s nulovým uchováním, abyste zabránili zachování zprostředkující tabulky.

    ```Kusto
    .alter table Events policy update @'[{"Source": "RawEvents", "Query": "EventRecordsExpand()", "IsEnabled": "True"}]'
    ```

1. Ingestovat data `RawEvents` do tabulky.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=RawEventMapping)
    ```

1. Zkontrolujte data `Events` v tabulce.

    ```Kusto
    Events
    ```

# <a name="c"></a>[C #](#tab/c-sharp)

1. Vytvořte funkci aktualizace, která `records` rozšiřuje kolekci tak, aby každá hodnota v `mv-expand` kolekci obdrží samostatný řádek pomocí operátoru. Tabulku použijeme `RawEvents` jako zdrojovou `Events` tabulku a jako cílovou tabulku.   

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

1. Přidejte zásady aktualizace do cílové tabulky. Tato zásada automaticky spustí dotaz na všechna nově `RawEvents` požitá data v `Events` zprostředkující tabulce a ingestuje jeho výsledky do tabulky. Definujte zásady uchovávání informací s nulovým uchováním, abyste zabránili zachování zprostředkující tabulky.

    ```C#
    var command =
        ".alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]";

    kustoClient.ExecuteControlCommand(command);
    ```

1. Ingestovat data `RawEvents` do tabulky.

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
    
1. Zkontrolujte data `Events` v tabulce.

# <a name="python"></a>[Python](#tab/python)

1. Vytvořte funkci aktualizace, která `records` rozšiřuje kolekci tak, aby každá hodnota v `mv-expand` kolekci obdrží samostatný řádek pomocí operátoru. Tabulku použijeme `RawEvents` jako zdrojovou `Events` tabulku a jako cílovou tabulku.   

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

1. Přidejte zásady aktualizace do cílové tabulky. Tato zásada automaticky spustí dotaz na všechna nově `RawEvents` požitá data v `Events` zprostředkující tabulce a ingestuje jeho výsledky do tabulky. Definujte zásady uchovávání informací s nulovým uchováním, abyste zabránili zachování zprostředkující tabulky.

    ```Python
    CREATE_UPDATE_POLICY_COMMAND = 
        """.alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_UPDATE_POLICY_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Ingestovat data `RawEvents` do tabulky.

    ```Python
    TABLE = "RawEvents"
    MAPPING = "RawEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

1. Zkontrolujte data `Events` v tabulce.

---    

## <a name="ingest-json-records-containing-dictionaries"></a>Ingestování záznamů JSON obsahujících slovníky

Slovník strukturované JSON obsahuje dvojice klíč-hodnota. Záznamy Společnosti JSon procházejí mapováním ingestování pomocí logického výrazu v . `JsonPath` Data můžete ingestovat s následující strukturou:

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

# <a name="kql"></a>[KQL](#tab/kusto-query-language)

1. Vytvořte mapování JSON.

    ```Kusto
    .create table Events ingestion json mapping 'KeyValueEventMapping' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'
    ```

1. Ingestovat data `Events` do tabulky.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=KeyValueEventMapping)
    ```

# <a name="c"></a>[C #](#tab/c-sharp)

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

1. Ingestovat data `Events` do tabulky.

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

# <a name="python"></a>[Python](#tab/python)

1. Vytvořte mapování JSON.

    ```Python
    MAPPING = "KeyValueEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. Ingestovat data `Events` do tabulky.

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

* [Přehled ingestování dat](ingest-data-overview.md)
* [Zápis dotazů](write-queries.md)