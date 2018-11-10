---
title: 'Rychlý start: Ingestování dat pomocí knihovny Node Azure Data Exploreru'
description: V tomto rychlém startu se dozvíte, jak ingestovat (načíst) data do Azure Data Exploreru pomocí Node.js.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 10/25/2018
ms.openlocfilehash: d5385ad5142c402a04bb6d5272573917b830754b
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142601"
---
# <a name="quickstart-ingest-data-using-the-azure-data-explorer-node-library"></a>Rychlý start: Ingestování dat pomocí knihovny Node Azure Data Exploreru

Azure Data Explorer je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. Azure Data Explorer nabízí dvě klientské knihovny pro Node: [knihovnu ingestování](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-ingest) a [knihovnu dat](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-data). Tyto knihovny umožňují snadno ingestovat (načíst) data do clusteru a dotazovat se na data z vašeho kódu. V tomto rychlém startu nejdříve vytvoříte mapování tabulky a dat v testovacím clusteru. Pak vytvoříte frontu ingestace do clusteru a ověříte výsledky.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Kromě předplatného Azure budete k dokončení tohoto rychlého startu potřebovat:

* [Testovací cluster a databázi](create-cluster-database-portal.md)

* [Node.js](https://nodejs.org/en/download/) nainstalované na počítači pro vývoj

## <a name="install-the-data-and-ingest-libraries"></a>Instalace knihovny dat a knihovny ingestů

Nainstalujte *azure-kusto-ingest* a *azure-kusto-data*.

```bash
npm i --save azure-kusto-ingest azure-kusto-data
```

## <a name="add-import-statements-and-constants"></a>Přidání příkazů a konstant pro import

Importujte třídy z knihoven.

```javascript
const KustoClient = require('azure-kusto-data').KustoClient;
const KustoIngestClient = require('azure-kusto-ingest').KustoIngestClient;
const KustoConnectionStringBuilder = require('azure-kusto-ingest').KustoConnectionStringBuilder;
```

K ověření aplikace používá Azure Data Explorer ID vašeho tenanta Azure Active Directory. Pokud chcete zjistit ID svého tenanta, postupujte podle pokynů k [vyhledání ID tenanta Office 365](https://docs.microsoft.com/onedrive/find-your-office-365-tenant-id).

Před spuštěním tohoto kódu nastavte hodnoty `authorityId`, `kustoUri`, `kustoIngestUri` a `kustoDatabase`.

```javascript
const authorityId = "<TenantId>";
const kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
const kustoIngestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/"
const kustoDatabase  = "<DatabaseName>"
```

Teď sestavte připojovací řetězec. V tomto příkladu se pro přístup ke clusteru používá ověřování pomocí zařízení. Můžete také použít certifikát, klíč a uživatelské jméno a heslo aplikace Azure Active Directory.

Cílovou tabulku a mapování vytvoříte v pozdějším kroku.

```javascript
const kcsbIngest = KustoConnectionStringBuilder.withAadDeviceAuthentication(kustoIngestUri, authorityId);

const kcsbData = KustoConnectionStringBuilder.withAadDeviceAuthentication(kustoUri, authorityId);

const destTable = "StormEvents";
const destTableMapping = "StormEvents_CSV_Mapping";
```

## <a name="set-source-file-information"></a>Nastavení informací o zdrojovém souboru

Importujte další třídy a nastavte konstanty pro soubor zdroje dat. Tento příklad používá ukázkový soubor hostovaný v Azure Blob Storage. Ukázková datová sada **StormEvents** obsahuje data týkající se počasí od [National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/).

```javascript
from azure.storage.blob import BlockBlobService
from azure.kusto.ingest import KustoIngestClient, IngestionProperties, FileDescriptor, BlobDescriptor, DataFormat, ReportLevel, ReportMethod

const container = "samplefiles";
const account = "kustosamplefiles";
const sas = "?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D"
const filePath = "StormEvents.csv";
const fileSize = 64158321    # in bytes

const blobPath = `https://${account}.blob.core.windows.net/${container}/${filePath}${sas}";
```

## <a name="create-a-table-on-your-test-cluster"></a>Vytvoření tabulky v testovacím clusteru

Vytvořte tabulku, která odpovídá schématu dat v souboru `StormEvents.csv`. Když se tento kód spustí, vrátí podobnou zprávu: *Pokud se chcete přihlásit, otevřete ve webovém prohlížeči stránku https://microsoft.com/devicelogin a zadejte kód XXXXXXXXX k ověření*. Podle pokynů se přihlaste a pak se vraťte a spusťte další blok kódu. Následující bloky kódu, které provedou připojení, budou vyžadovat, abyste se znovu přihlásili.

```javascript
const kustoClient = new KustoClient(kcsbData);
const createTableCommand = ".create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)"

kustoClient.executeMgmt(kustoDatabase, createTableCommand, (err, results) => {
    console.log(result.primaryResults[0]);
});
```

## <a name="define-ingestion-mapping"></a>Definování mapování ingestace

Namapujte příchozí data CSV na názvy sloupců a datové typy použité při vytváření tabulky.

```javascript
const createMappingCommand = `.create table StormEvents ingestion csv mapping ${destTableMapping} '[{\"Name\":\"StartTime\",\"datatype\":\"datetime\",\"Ordinal\":0}, {\"Name\":\"EndTime\",\"datatype\":\"datetime\",\"Ordinal\":1},{\"Name\":\"EpisodeId\",\"datatype\":\"int\",\"Ordinal\":2},{\"Name\":\"EventId\",\"datatype\":\"int\",\"Ordinal\":3},{\"Name\":\"State\",\"datatype\":\"string\",\"Ordinal\":4},{\"Name\":\"EventType\",\"datatype\":\"string\",\"Ordinal\":5},{\"Name\":\"InjuriesDirect\",\"datatype\":\"int\",\"Ordinal\":6},{\"Name\":\"InjuriesIndirect\",\"datatype\":\"int\",\"Ordinal\":7},{\"Name\":\"DeathsDirect\",\"datatype\":\"int\",\"Ordinal\":8},{\"Name\":\"DeathsIndirect\",\"datatype\":\"int\",\"Ordinal\":9},{\"Name\":\"DamageProperty\",\"datatype\":\"int\",\"Ordinal\":10},{\"Name\":\"DamageCrops\",\"datatype\":\"int\",\"Ordinal\":11},{\"Name\":\"Source\",\"datatype\":\"string\",\"Ordinal\":12},{\"Name\":\"BeginLocation\",\"datatype\":\"string\",\"Ordinal\":13},{\"Name\":\"EndLocation\",\"datatype\":\"string\",\"Ordinal\":14},{\"Name\":\"BeginLat\",\"datatype\":\"real\",\"Ordinal\":16},{\"Name\":\"BeginLon\",\"datatype\":\"real\",\"Ordinal\":17},{\"Name\":\"EndLat\",\"datatype\":\"real\",\"Ordinal\":18},{\"Name\":\"EndLon\",\"datatype\":\"real\",\"Ordinal\":19},{\"Name\":\"EpisodeNarrative\",\"datatype\":\"string\",\"Ordinal\":20},{\"Name\":\"EventNarrative\",\"datatype\":\"string\",\"Ordinal\":21},{\"Name\":\"StormSummary\",\"datatype\":\"dynamic\",\"Ordinal\":22}]'`;

kustoClient.executeMgmt(kustoDatabase, createMappingCommand, (err, results) => {
    console.log(result.primaryResults[0]);
});
```

## <a name="queue-a-message-for-ingestion"></a>Přidání zprávy do fronty pro ingestaci

Přidejte zprávu do fronty k získání dat z úložiště objektů blob a tato data ingestujte do Průzkumníka dat Azure.

```javascript
const { DataFormat, JsonColumnMapping } =  require("azure-kusto-ingest").IngestionPropertiesEnums;
const { BlobDescriptor } = require("azure-kusto-ingest").Descriptors;
const ingestClient = new KustoIngestClient(kcsbIngest);


// All ingestion properties are documented here: https://docs.microsoft.com/azure/kusto/management/data-ingest#ingestion-properties
const ingestionProps  = new IngestionProperties(kustoDatabase, destTable, DataFormat.csv, null,destTableMapping, {'ignoreFirstRecord': 'true'});
const blobDesc = new BlobDescriptor(blobPath, 10);
ingestClient.ingestFromBlob(blobDesc,ingestionProps, (err) => {
    if (err) throw new Error(err);
});
```

## <a name="validate-that-table-contains-data"></a>Ověření, že tabulka obsahuje data

Ověřte, že se data ingestovala do tabulky. Počkejte pět až deset minut, než ingestace ve frontě naplánuje ingestování a načte data do Průzkumníka dat Azure. Pak spuštěním následujícího kódu získejte počet záznamů v tabulce `StormEvents`.

```javascript
const query = "StormEvents | count";

kustoClient.execute(kustoDatabse, query, (err, results) => {
    if (err) throw new Error(err);  
    console.log(results.primaryResults[0].toString());
});
```

## <a name="run-troubleshooting-queries"></a>Spuštění dotazů pro řešení potíží

Přihlaste se k [https://dataexplorer.azure.com](https://dataexplorer.azure.com) a připojte se k vašemu clusteru. Spuštěním následujícího příkazu ve vaší databázi zjistíte, jestli za poslední čtyři hodiny došlo k chybám ingestování. Přes spuštěním nahraďte název databáze.
    
```Kusto
    .show ingestion failures
    | where FailedOn > ago(4h) and Database == "<DatabaseName>"
```

Spuštěním následujícího příkazu zobrazíte stav všech operací ingestace za poslední čtyři hodiny. Přes spuštěním nahraďte název databáze.

```Kusto
.show operations
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud plánujete postupovat podle našich dalších rychlých startů a kurzů, vytvořené prostředky zachovejte. Pokud ne, spuštěním následujícího příkazu v databázi tabulku `StormEvents` vyčistěte.

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zápis dotazů](write-queries.md)