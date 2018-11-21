---
title: 'Rychlý start: Ingestování dat s využitím dat Explorer .NET Standard SDK služby Azure (Preview)'
description: V tomto rychlém startu se dozvíte, jak se přijmout data (načíst) do Průzkumníku dat Azure pomocí .NET Standard SDK.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 11/18/2018
ms.openlocfilehash: b0e8c4dabea6aeae8d93d64d97b598ec97b2d18a
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52277010"
---
# <a name="quickstart-ingest-data-using-the-azure-data-explorer-net-standard-sdk-preview"></a>Rychlý start: Ingestování dat s využitím dat Explorer .NET Standard SDK služby Azure (Preview)

Průzkumník Azure dat (ADX) je služba pro zkoumání dat rychlá a vysoce škálovatelné pro data protokolů a telemetrie. ADX nabízí dva klientské knihovny pro .NET Standard: [ingestování knihovny](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Ingest.NETStandard) a [knihovna dat](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard). Tyto knihovny umožňují snadno ingestovat (načíst) data do clusteru a dotazovat se na data z vašeho kódu. V tomto rychlém startu nejdříve vytvoříte mapování tabulky a dat v testovacím clusteru. Pak vytvoříte frontu ingestace do clusteru a ověříte výsledky.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

* [Testovací cluster a databázi](create-cluster-database-portal.md)

## <a name="install-the-ingest-library"></a>Nainstalujte knihovnu ingestu

```
Install-Package Microsoft.Azure.Kusto.Ingest.NETStandard
```

## <a name="authentication"></a>Authentication

K ověření aplikace používá Azure Data Explorer ID vašeho tenanta AAD. ID tenanta zjistíte pomocí následující adresy URL, ve které *YourDomain* nahradíte svou doménou.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Pokud je vaše doména například *contoso.com*, je adresa URL [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Kliknutím na tuto adresu URL zobrazte výsledky. První řádek vypadá jako v následujícím příkladu. 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

ID tenanta je v tomto případě `6babcaad-604b-40ac-a9d7-9fd97c0b779f`.

Tento příklad používá pro přístup ke clusteru AAD uživatele a heslo pro ověřování. Můžete také použít certifikát aplikace AAD a klíč aplikace AAD. Nastavit hodnoty `tenantId`, `user`, a `password` před spuštěním tohoto kódu.

```csharp
var tenantId = "<TenantId>";
var user = "<User>";
var password = "<Password>";
```

## <a name="construct-the-connection-string"></a>Vytvoření připojovacího řetězce
Teď sestavte připojovací řetězec. Cílovou tabulku a mapování vytvoříte v pozdějším kroku.

```csharp
var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
var database = "<DatabaseName>";

var kustoConnectionStringBuilder =
    new KustoConnectionStringBuilder(kustoUri)
    {
        FederatedSecurity = true,
        InitialCatalog = database,
        UserID = user,
        Password = password,
        Authority = tenantId
    };
```

## <a name="set-source-file-information"></a>Nastavení informací o zdrojovém souboru

Nastavit konstanty pro datový zdroj soubor. Tento příklad používá ukázkový soubor hostovaný v Azure Blob Storage. Ukázková datová sada **StormEvents** obsahuje data týkající se počasí od [National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/).

```csharp
var blobPath = "https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
```

## <a name="create-a-table-on-your-test-cluster"></a>Vytvoření tabulky v testovacím clusteru
Vytvořte tabulku, která odpovídá schématu dat v souboru `StormEvents.csv`. Když se tento kód spustí, vrátí podobnou zprávu: *Pokud se chcete přihlásit, otevřete ve webovém prohlížeči stránku https://microsoft.com/devicelogin a zadejte kód F3W4VWZDM k ověření*. Podle pokynů se přihlaste a pak se vraťte a spusťte další blok kódu. Následující bloky kódu, které provedou připojení, vyžadují, abyste se znovu přihlásili.

```csharp
var table = "StormEvents";
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("StartTime", "System.DateTime"),
                Tuple.Create("EndTime", "System.DateTime"),
                Tuple.Create("EpisodeId", "System.Int32"),
                Tuple.Create("EventId", "System.Int32"),
                Tuple.Create("State", "System.String"),
                Tuple.Create("EventType", "System.String"),
                Tuple.Create("InjuriesDirect", "System.Int32"),
                Tuple.Create("DeathsDirect", "System.Int32"),
                Tuple.Create("DeathsIndirect", "System.Int32"),
                Tuple.Create("DamageProperty", "System.Int32"),
                Tuple.Create("DamageCrops", "System.Int32"),
                Tuple.Create("Source", "System.String"),
                Tuple.Create("BeginLocation", "System.String"),
                Tuple.Create("EndLocation", "System.String"),
                Tuple.Create("BeginLat", "System.Double"),
                Tuple.Create("BeginLon", "System.Double"),
                Tuple.Create("EndLat", "System.Double"),
                Tuple.Create("EndLon", "System.Double"),
                Tuple.Create("EpisodeNarrative", "System.String"),
                Tuple.Create("EventNarrative", "System.String"),
                Tuple.Create("StormSummary", "System.Object"),
            });

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="define-ingestion-mapping"></a>Definování mapování ingestace

Namapujte příchozí data CSV na názvy sloupců a datové typy použité při vytváření tabulky.
Zřízení [sdíleného svazku clusteru sloupce mapování objektu](/azure/kusto/management/tables#create-ingestion-mapping) v této tabulce

```csharp
var tableMapping = "StormEvents_CSV_Mapping";
using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCsvMappingCreateCommand(
            table,
            tableMapping,
            new[]
            {
                new CsvColumnMapping { ColumnName = "StartTime", Ordinal = 0 },
                new CsvColumnMapping { ColumnName = "EndTime", Ordinal = 1 },
                new CsvColumnMapping { ColumnName = "EventId", Ordinal = 3 },
                new CsvColumnMapping { ColumnName = "State", Ordinal = 4 },
                new CsvColumnMapping { ColumnName = "InjuriesDirect", Ordinal = 6 },
                new CsvColumnMapping { ColumnName = "InjuriesIndirect", Ordinal = 7 },
                new CsvColumnMapping { ColumnName = "DeathsDirect", Ordinal = 8 },
                new CsvColumnMapping { ColumnName = "DeathsIndirect", Ordinal = 9 },
                new CsvColumnMapping { ColumnName = "DamageProperty", Ordinal = 10 },
                new CsvColumnMapping { ColumnName = "DamageCrops", Ordinal = 11 },
                new CsvColumnMapping { ColumnName = "Source", Ordinal = 12 },
                new CsvColumnMapping { ColumnName = "BeginLocation", Ordinal = 13 },
                new CsvColumnMapping { ColumnName = "EndLocation", Ordinal = 14 },
                new CsvColumnMapping { ColumnName = "BeginLat", Ordinal = 16 },
                new CsvColumnMapping { ColumnName = "BeginLon", Ordinal = 17 },
                new CsvColumnMapping { ColumnName = "EndLat", Ordinal = 18 },
                new CsvColumnMapping { ColumnName = "EndLon", Ordinal = 19 },
                new CsvColumnMapping { ColumnName = "EpisodeNarrative", Ordinal = 20 },
                new CsvColumnMapping { ColumnName = "EventNarrative", Ordinal = 21 },
                new CsvColumnMapping { ColumnName = "StormSummary", Ordinal = 22 },
            });

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="queue-a-message-for-ingestion"></a>Přidání zprávy do fronty pro ingestaci

Zprávy do fronty k získání dat z úložiště objektů blob a ingestovat data do ADX.

```csharp
var ingestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/";
var ingestConnectionStringBuilder =
    new KustoConnectionStringBuilder(ingestUri)
    {
        FederatedSecurity = true,
        InitialCatalog = database,
        UserID = user,
        Password = password,
        Authority = tenantId
    };

using (var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(ingestConnectionStringBuilder))
{
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.csv,
            CSVMappingReference = tableMapping,
            IgnoreFirstRecord = true
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
}
```

## <a name="validate-data-was-ingested-into-the-table"></a>Ověřit data se ingestují do tabulky

Počkejte pět až deset minut, než se tak ve frontě příjem dat k plánování ingestování a načtení dat do ADX. Pak spuštěním následujícího kódu získejte počet záznamů v tabulce StormEvents.

```csharp
using (var cslQueryProvider = KustoClientFactory.CreateCslQueryProvider(kustoConnectionStringBuilder))
{
    var query = "StormEvents | count";

    var results = cslQueryProvider.ExecuteQuery<long>(query);
    Console.WriteLine(results.Single());
}
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

Pokud plánujete postupovat podle našich dalších rychlých startů a kurzů, vytvořené prostředky zachovejte. Pokud ne, spuštěním následujícího příkazu v databázi tabulku StormEvents vyčistěte.

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zápis dotazů](write-queries.md)