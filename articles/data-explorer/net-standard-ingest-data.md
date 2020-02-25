---
title: Ingestování dat pomocí sady Azure Průzkumník dat .NET Standard SDK (Preview)
description: V tomto článku se dozvíte, jak pomocí sady .NET Standard SDK ingestovat (načítat) data do Azure Průzkumník dat.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 9b6eda60f0b0cb1b697560cccc2cffe719d58536
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562458"
---
# <a name="ingest-data-using-the-azure-data-explorer-net-standard-sdk-preview"></a>Ingestování dat pomocí sady Azure Průzkumník dat .NET Standard SDK (Preview)

Azure Průzkumník dat (ADX) je rychlá a vysoce škálovatelná služba pro zkoumání dat pro data protokolů a telemetrie. ADX poskytuje dvě klientské knihovny pro .NET Standard: [knihovnu](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Ingest.NETStandard) ingest a [knihovnu dat](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard). Tyto knihovny umožňují snadno ingestovat (načíst) data do clusteru a dotazovat se na data z vašeho kódu. V tomto článku vytvoříte nejprve tabulku a mapování dat v testovacím clusteru. Potom do fronty zařadíte ingestování do clusteru a ověříte výsledky.

## <a name="prerequisites"></a>Požadavky

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet Azure](https://azure.microsoft.com/free/) před tím, než začnete.

* [Testovací cluster a databáze](create-cluster-database-portal.md)

## <a name="install-the-ingest-library"></a>Instalace knihovny ingestování

```
Install-Package Microsoft.Azure.Kusto.Ingest.NETStandard
```

## <a name="authentication"></a>Ověřování

K ověření aplikace používá Průzkumník dat Azure ID vašeho tenanta AAD. ID tenanta zjistíte pomocí následující adresy URL, ve které *YourDomain* nahradíte svou doménou.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Pokud je vaše doména například *contoso.com*, je adresa URL [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Kliknutím na tuto adresu URL zobrazte výsledky. První řádek vypadá jako v následujícím příkladu. 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

V tomto případě je ID tenanta `6babcaad-604b-40ac-a9d7-9fd97c0b779f`.

V tomto příkladu se k přístupu ke clusteru používá uživatel a heslo AAD pro ověřování. Můžete také použít certifikát aplikace AAD a klíč aplikace AAD. Před spuštěním tohoto kódu nastavte hodnoty pro `tenantId`, `user`a `password`.

```csharp
var tenantId = "<TenantId>";
var user = "<User>";
var password = "<Password>";
```

## <a name="construct-the-connection-string"></a>Vytvoření připojovacího řetězce
Teď vytvořte připojovací řetězec. Cílovou tabulku a mapování vytvoříte v pozdějším kroku.

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

Nastavte cestu ke zdrojovému souboru. Tento příklad používá ukázkový soubor hostovaný v Azure Blob Storage. Ukázková datová sada **StormEvents** obsahuje data týkající se počasí od [National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/).

```csharp
var blobPath = "https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
```

## <a name="create-a-table-on-your-test-cluster"></a>Vytvoření tabulky v testovacím clusteru
Vytvořte tabulku s názvem `StormEvents`, která odpovídá schématu dat v souboru `StormEvents.csv`.

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
                Tuple.Create("InjuriesIndirect", "System.Int32"),
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

Data příchozích sdílených svazků clusteru namapujte na názvy sloupců používané při vytváření tabulky.
Zřízení [objektu mapování sloupců sdíleného svazku clusteru](/azure/kusto/management/create-ingestion-mapping-command) v této tabulce

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
                new CsvColumnMapping { ColumnName = "EpisodeId", Ordinal = 2 },
                new CsvColumnMapping { ColumnName = "EventId", Ordinal = 3 },
                new CsvColumnMapping { ColumnName = "State", Ordinal = 4 },
                new CsvColumnMapping { ColumnName = "EventType", Ordinal = 5 },
                new CsvColumnMapping { ColumnName = "InjuriesDirect", Ordinal = 6 },
                new CsvColumnMapping { ColumnName = "InjuriesIndirect", Ordinal = 7 },
                new CsvColumnMapping { ColumnName = "DeathsDirect", Ordinal = 8 },
                new CsvColumnMapping { ColumnName = "DeathsIndirect", Ordinal = 9 },
                new CsvColumnMapping { ColumnName = "DamageProperty", Ordinal = 10 },
                new CsvColumnMapping { ColumnName = "DamageCrops", Ordinal = 11 },
                new CsvColumnMapping { ColumnName = "Source", Ordinal = 12 },
                new CsvColumnMapping { ColumnName = "BeginLocation", Ordinal = 13 },
                new CsvColumnMapping { ColumnName = "EndLocation", Ordinal = 14 },
                new CsvColumnMapping { ColumnName = "BeginLat", Ordinal = 15 },
                new CsvColumnMapping { ColumnName = "BeginLon", Ordinal = 16 },
                new CsvColumnMapping { ColumnName = "EndLat", Ordinal = 17 },
                new CsvColumnMapping { ColumnName = "EndLon", Ordinal = 18 },
                new CsvColumnMapping { ColumnName = "EpisodeNarrative", Ordinal = 19 },
                new CsvColumnMapping { ColumnName = "EventNarrative", Ordinal = 20 },
                new CsvColumnMapping { ColumnName = "StormSummary", Ordinal = 21 },
            });

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="queue-a-message-for-ingestion"></a>Přidání zprávy do fronty pro ingestaci

Zařadí zprávu do fronty pro vyžádání dat z úložiště objektů BLOB a ingestování těchto dat do ADX.

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

## <a name="validate-data-was-ingested-into-the-table"></a>Ověření dat bylo ingestované do tabulky.

Počkejte pět až deset minut, než se ingestuje do fronty, aby se naplánovala příjem a načetla se data do ADX. Pak spuštěním následujícího kódu získejte počet záznamů v tabulce `StormEvents`.

```csharp
using (var cslQueryProvider = KustoClientFactory.CreateCslQueryProvider(kustoConnectionStringBuilder))
{
    var query = $"{table} | count";

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

Pokud plánujete postupovat podle našich dalších článků, ponechejte prostředky, které jste vytvořili. Pokud ne, spuštěním následujícího příkazu v databázi tabulku `StormEvents` vyčistěte.

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>Další kroky

* [Zápis dotazů](write-queries.md)
