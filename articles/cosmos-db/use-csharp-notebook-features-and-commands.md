---
title: Používejte integrované příkazy a funkce poznámkového bloku v Azure Cosmos DB poznámkových blocích C# (Preview).
description: Naučte se používat integrované příkazy a funkce k provádění běžných operací pomocí integrovaných poznámkových bloků jazyka C# Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/19/2020
ms.author: dech
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 67fe71d8e2d6ab239989cb30e9bf5a1b4d731037
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340470"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-c-notebooks-preview"></a>Používejte integrované příkazy a funkce poznámkového bloku v Azure Cosmos DB poznámkových blocích C# (Preview).
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Integrované poznámkové bloky Jupyter v Azure Cosmos DB umožňují analyzovat a vizualizovat data z Azure Portal. Tento článek popisuje, jak používat integrované příkazy a funkce poznámkového bloku k provádění běžných operací v poznámkových blocích C#.

## <a name="install-a-new-nuget-package"></a>Nainstalovat nový balíček NuGet
Po povolení podpory poznámkových bloků pro účty Azure Cosmos můžete otevřít nový Poznámkový blok a nainstalovat balíček.

V nové buňce kódu vložte a spusťte následující kód, nahraďte ``PackageToBeInstalled`` požadovaným balíčkem NuGet a ``optionalVersion`` v případě potřeby v konkrétní verzi balíčku. 

```csharp
#r "nuget: PackageToBeInstalled, optionalVersion"
```

Do stejné buňky můžete nainstalovat více balíčků NuGet. Balíčky budou dostupné pro použití z libovolného poznámkového bloku v pracovním prostoru účet Azure Cosmos. 

V současné době pracovní prostor poznámkových bloků C# nepodporuje rekurzivní rozlišení balíčků NuGet. Pokud má balíček NuGet závislosti na dalších balíčcích NuGet, které nejsou aktuálně nainstalované, musíte je explicitně propojit s nadřazeným balíčkem.

> [!TIP]
> Pokud váš Poznámkový blok vyžaduje vlastní balíček, doporučujeme přidat do poznámkového bloku buňku pro instalaci balíčku a jeho první buňku. To snižuje riziko konfliktu s jinými balíčky, které se ve výchozím nastavení Azure Cosmos DB načítají. Pokud [resetujete pracovní prostor](#reset-notebooks-workspace)a odeberete všechny balíčky, je také snadné balíčky znovu nainstalovat. 

## <a name="use-the-built-in-azure-cosmos-db-net-sdk"></a>Použití integrované sady Azure Cosmos DB .NET SDK
Verze 3 sady [Azure Cosmos DB .NET SDK pro SQL API](https://github.com/Azure/azure-cosmos-dotnet-v3) je nainstalovaná a zahrnutá v prostředí poznámkového bloku pro účet Azure Cosmos.

Vytvořte instanci ``CosmosClient`` pro spuštění jakékoli operace sady SDK. 

Například:

```csharp
// Include usings
using System;
using Microsoft.Azure.Cosmos; //namespace for Azure Cosmos DB .NET V3 SDK
using System.Collections;

// Initialize a new instance of CosmosClient using the built-in account endpoint and key parameters
CosmosClient cosmosClient = new CosmosClient(Cosmos.Endpoint, Cosmos.Key);

// Create a new database and container with 400 RU/s
Database database = await cosmosClient.CreateDatabaseIfNotExistsAsync("DatabaseName");
Container container = await database.CreateContainerIfNotExistsAsync("ContainerName", "/partitionKey", 400);
```
Další informace najdete v [ukázkách sady .NET V3 SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage). 

> [!IMPORTANT]
> Integrovaná sada Azure Cosmos DB .NET SDK je podporována pouze pro účty rozhraní SQL (Core) API. U jiných rozhraní API budete muset [nainstalovat příslušný ovladač .NET](#install-a-new-nuget-package) , který odpovídá rozhraní API. 

## <a name="set-custom-options-using-cosmosclientoptions"></a>Nastavení vlastních možností pomocí ``CosmosClientOptions``
Pro větší flexibilitu můžete nastavit vlastní ``CosmosClientOptions`` vlastnost a předat ji do své ``CosmosClient`` instance. Tuto vlastnost můžete použít k těmto akcím:

- Nastavte název aplikace v příponě User-Agent tak, aby se zahrnula do každého požadavku.
- Nastavte upřednostňovanou oblast, která se použije při spouštění operací na službě.
- Nastavte vlastní zásady opakování, které budou zpracovávat požadavky omezené na rychlost.

Všechna podporovaná nastavení najdete v článku [referenční informace k rozhraní CosmosClientOptions API](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions) . Následuje příklad, který ukazuje, jak nastavit `cosmosClientOptions` vlastnost:

```csharp
using Microsoft.Azure.Cosmos;

// Configure CosmosClientOptions
var cosmosClientOptions = new CosmosClientOptions
{
    ApplicationName = "ContosoNotebookAppName",
    ApplicationRegion = Regions.RegionName, // By default, this is the region you first created your account in
    MaxRetryAttemptsOnRateLimitedRequests = 9, // By default, this value is 9
};

var client = new CosmosClient(Cosmos.Endpoint, Cosmos.Key, cosmosClientOptions);
```

## <a name="access-the-account-endpoint-and-primary-key-variables"></a>Přístup k proměnným koncovým bodem účtu a primárním klíčům
Můžete získat přístup k předdefinovanému koncovému bodu a klíči účtu Azure Cosmos, kde se nachází váš Poznámkový blok.

```csharp
var key = Cosmos.Key;
var endpoint = Cosmos.Endpoint;
```

> [!IMPORTANT]
> ``Cosmos.Key``Proměnné a ``Cosmos.Endpoint`` se vztahují pouze na rozhraní SQL API. V případě jiných rozhraní API Najděte koncový bod a klíč v okně **připojovací řetězce** nebo **klíče** v účtu Azure Cosmos.  

## <a name="print-console-output-in-c-code"></a>Výstup tiskové konzoly v kódu jazyka C#
V kódu jazyka C# můžete použít syntaxi Display. AsMarkdown () s [interpolací řetězce](/dotnet/csharp/language-reference/tokens/interpolated) k tisku výstupu konzoly, která se zobrazí při spuštění buňky. 

Například: 

```csharp
// Print text in the output
Display.AsMarkdown($"Hello world!");

// Print a variable in the output
for (int i = 0; i < 5; i++) {
    Display.AsMarkdown($"Printing out variable: {i}");
}
```
> [!IMPORTANT]
> Syntaxe Console. WriteLine () není v poznámkových blocích C# aktuálně podporována. K vytištění výstupu konzoly z programu použijte Display. AsMarkdown. 

## <a name="use-built-in-nteract-data-explorer"></a>Použití integrovaného Průzkumníka dat nteract
K filtrování a vizualizaci kolekce položek můžete použít vestavěný [nteract data Explorer](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897) . V buňce vložte proměnnou, kterou chcete vizualizovat, do posledního řádku, který se automaticky zobrazí v nteract při spuštění buňky.

Například v příkladu *GetingStarted_Csharp. ipynb* můžeme vyfiltrovat proměnnou s naším výsledkem, a ``telemetryEvents`` . Celou ukázku najdete v [poznámkovém bloku GettingStarted_Csharp. ipynb](https://github.com/Azure-Samples/cosmos-notebooks/blob/master/CSharp_quickstarts/GettingStarted_CSharp.ipynb) . 

:::image type="content" source="media/use-notebook-features-and-commands/csharp-query-cell.png" alt-text="Buňka dotazu CSharp":::

:::image type="content" source="media/use-notebook-features-and-commands/csharp-nteract-built-in-chart.png" alt-text="Průzkumník dat nteract":::

## <a name="use-built-in-dictionary-viewer"></a>Použít vestavěný prohlížeč slovníku
K zobrazení proměnné můžete použít vestavěný prohlížeč slovníku. V buňce vložte proměnnou, kterou chcete vizualizovat, do posledního řádku, který se automaticky zobrazí při spuštění buňky.

:::image type="content" source="media/use-notebook-features-and-commands/csharp-built-in-dictionary-viewer.png" alt-text="Vestavěný prohlížeč slovníku":::

## <a name="upload-json-items-to-a-container"></a>Nahrání položek JSON do kontejneru
Pomocí ``%%upload`` příkazu Magic můžete nahrávat data ze souboru JSON do zadaného kontejneru Azure Cosmos. Následující příkaz slouží k nahrání položek:

```csharp
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- Nahraďte ``{database_id}`` a ``{container_id}`` názvem databáze a kontejneru v účtu Azure Cosmos. 
- Nahraďte ``{url_location_of_file}`` umístěním souboru JSON. Tento soubor musí být pole platných objektů JSON a měl by být přístupný prostřednictvím veřejného Internetu.

Například:

```csharp
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```

S využitím výstupních statistik můžete vypočítat efektivní RU/s, který se použije k nahrání položek. Například pokud 25 000 ru bylo spotřebováno více než 38 sekund, efektivní RU/s je 25 000 ru/38 sekund = 658 RU/s.

## <a name="run-another-notebook-in-current-notebook"></a>Spustit jiný Poznámkový blok v aktuálním poznámkovém bloku 
Pomocí ``%%run`` příkazu Magic můžete v pracovním prostoru spustit jiný Poznámkový blok z aktuálního poznámkového bloku. Použijte syntaxi:

```csharp
%%run ./path/to/{notebookName}.ipynb
```
Nahraďte ``{notebookName}`` názvem poznámkového bloku, který chcete spustit. Poznámkový blok musí být ve vašem aktuálním pracovním prostoru "moje poznámkové bloky". 

## <a name="reset-notebooks-workspace"></a>Resetovat pracovní prostor poznámkových bloků
Pokud chcete resetovat pracovní prostor poznámkových bloků na výchozí nastavení, na panelu příkazů vyberte **resetovat pracovní prostor** . Tím se odeberou všechny vlastní nainstalované balíčky a restartuje se server Jupyter. Vaše poznámkové bloky, soubory a prostředky Azure Cosmos nebudou ovlivněny.  

:::image type="content" source="media/use-notebook-features-and-commands/reset-workspace.png" alt-text="Resetovat pracovní prostor poznámkových bloků":::

## <a name="next-steps"></a>Další kroky

- Přečtěte si o výhodách [Azure Cosmos DB poznámkových blocích Jupyter](cosmosdb-jupyter-notebooks.md)
- Další informace o sadě [Azure Cosmos DB .NET SDK pro SQL API](https://github.com/Azure/azure-cosmos-dotnet-v3)
