---
title: Azure Cosmos DB rozhraní API pro tabulky pomocí sady .NET Standard SDK
description: Naučte se ukládat a dotazovat strukturovaná data v Azure Cosmos DB rozhraní API pro tabulkym účtu.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 12/03/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 760a3a194e181576acb3601aabc07c6900cb664e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91276760"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>Začínáme s rozhraním Azure Cosmos DB API pro tabulky a Azure Table Storage s využitím .NET SDK

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Pomocí Azure Cosmos DB rozhraní API pro tabulky nebo úložiště tabulek Azure můžete ukládat strukturovaná data NoSQL do cloudu a poskytnout tak úložiště klíčů a atributů s návrhem bez schématu. Vzhledem k tomu, že Azure Cosmos DB rozhraní API pro tabulky a úložiště tabulek jsou míň schématu, je snadné přizpůsobit data, jak se vyvíjí vaše aplikace. K ukládání flexibilních datových sad, například uživatelských dat pro webové aplikace, adresářů, informací o zařízení nebo jiných typů metadat, které vaše služba vyžaduje, můžete použít Azure Cosmos DB rozhraní API pro tabulky nebo úložiště tabulek. 

Tento kurz popisuje ukázku, která vám ukáže, jak používat [Microsoft Azure Cosmos DB knihovny tabulek pro .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) s Azure Cosmos DB rozhraní API pro tabulky a scénáři úložiště tabulek Azure. Je nutné použít připojení specifické pro službu Azure. Tyto scénáře jsou prozkoumání pomocí příkladů jazyka C#, které ilustrují vytváření tabulek, vkládání a aktualizaci dat, dotazování dat a odstraňování tabulek.

## <a name="prerequisites"></a>Požadavky

Pro úspěšné dokončení této ukázky potřebujete následující položky:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [Microsoft Azure knihovna tabulek CosmosDB pro .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) – Tato knihovna je aktuálně dostupná pro .NET Standard a rozhraní .NET Framework. 

* [Účet Azure Cosmos DB rozhraní API pro tabulky](create-table-dotnet.md#create-a-database-account).

## <a name="create-an-azure-cosmos-db-table-api-account"></a>Vytvoření účtu rozhraní Table API služby Azure Cosmos DB

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>Vytvoření projektu konzoly .NET

V aplikaci Visual Studio vytvořte novou konzolovou aplikaci .NET. Následující kroky ukazují, jak vytvořit konzolovou aplikaci v aplikaci Visual Studio 2019. Můžete použít Azure Cosmos DB knihovny tabulek v jakémkoli typu aplikace .NET, včetně cloudové služby Azure nebo webové aplikace a desktopových a mobilních aplikací. V této příručce použijeme konzolovou aplikaci kvůli zjednodušení.

1. Vyberte **soubor**  >  **Nový**  >  **projekt**.

1. Zvolte **Konzolová aplikace (.NET Core)** a pak vyberte **Další**.

1. Do pole **název projektu** zadejte název vaší aplikace, například **CosmosTableSamples**. (V případě potřeby můžete zadat jiný název.)

1. Vyberte **Vytvořit**.

Všechny příklady kódu v této ukázce lze přidat do metody Main () souboru **program.cs** vaší konzolové aplikace.

## <a name="install-the-required-nuget-package"></a>Instalace požadovaného balíčku NuGet

K získání balíčku NuGet použijte tento postup:

1. Klikněte v **Průzkumníku řešení** pravým tlačítkem na projekt a vyberte **Spravovat balíčky NuGet**.

1. Vyhledejte v online [`Microsoft.Azure.Cosmos.Table`](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) režimu [`Microsoft.Extensions.Configuration`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) , [`Microsoft.Extensions.Configuration.Json`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json) , [`Microsoft.Extensions.Configuration.Binder`](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder) a vyberte **nainstalovat** a nainstalujte Microsoft Azure Cosmos DB knihovnu tabulek.

## <a name="configure-your-storage-connection-string"></a>Konfigurace připojovacího řetězce úložiště

1. V [Azure Portal](https://portal.azure.com/)přejděte k účtu Azure Cosmos nebo k účtu Table Storage. 

1. Otevřete okno **připojovací řetězec** nebo **přístupové klávesy** . Pomocí tlačítka pro kopírování na pravé straně okna zkopírujte **PRIMÁRNÍ PŘIPOJOVACÍ ŘETĚZEC**.

   :::image type="content" source="./media/create-table-dotnet/connection-string.png" alt-text="Zobrazení a zkopírování PRIMÁRNÍHO PŘIPOJOVACÍHO ŘETĚZCE v podokně Připojovací řetězec":::
   
1. Pokud chcete nakonfigurovat připojovací řetězec, klikněte pravým tlačítkem ze sady Visual Studio na projekt **CosmosTableSamples**.

1. Vyberte **Přidat** a poté **Nová položka**. Vytvoří nový soubor **Settings.js** s typem souboru jako konfigurační soubor **TypeScript JSON** . 

1. Nahraďte kód v Settings.jssouboru následujícím kódem a přiřaďte k primárnímu připojovacímu řetězci:

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. Klikněte pravým tlačítkem na projekt **CosmosTableSamples**. Vyberte **Přidat**, **Nová položka** a přidejte třídu s názvem **appSettings.cs**.

1. Do souboru AppSettings.cs přidejte následující kód. Tento soubor přečte připojovací řetězec z Settings.jsv souboru a přiřadí ho ke konfiguračnímu parametru:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/AppSettings.cs":::

## <a name="parse-and-validate-the-connection-details"></a>Analyzovat a ověřit podrobnosti připojení

1. Klikněte pravým tlačítkem na projekt **CosmosTableSamples**. Vyberte **Přidat**, **Nová položka** a přidejte třídu s názvem **Common.cs**. Budete psát kód, který ověří podrobnosti připojení a vytvoří tabulku v rámci této třídy.

1. Definujte metodu `CreateStorageAccountFromConnectionString` , jak je znázorněno níže. Tato metoda analyzuje podrobnosti připojovacího řetězce a ověří, zda jsou platné podrobnosti o názvu účtu a klíči účtu uvedené v souboru Settings.json.

   :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Common.cs" id="createStorageAccount":::

## <a name="create-a-table"></a>Vytvoření tabulky 

Třída [CloudTableClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtableclient) vám umožňuje načítat tabulky a entity, které jsou uložené ve službě Table Storage. Vzhledem k tomu, že v účtu Cosmos DB rozhraní API pro tabulky nejsou žádné tabulky, přidejte `CreateTableAsync` metodu do třídy **Common.cs** a vytvořte tabulku:

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Common.cs" id="CreateTable":::

Pokud se zobrazí chyba "nedostupná výjimka služby 503", je možné, že brána firewall zablokuje požadované porty pro režim připojení. Pokud chcete tento problém vyřešit, otevřete požadované porty nebo použijte připojení režimu brány, jak je znázorněno v následujícím kódu:

```csharp
tableClient.TableClientConfiguration.UseRestExecutorForCosmosEndpoint = true;
```

## <a name="define-the-entity"></a>Definovat entitu 

Entity se mapují na objekty jazyka C# pomocí vlastní třídy odvozené z [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity). Když budete chtít do tabulky přidat entitu, vytvořte třídu, která definuje vlastnosti vaší entity.

Klikněte pravým tlačítkem na projekt **CosmosTableSamples**. Vyberte **Přidat**, **Nová složka** a pojmenujte ji jako **model**. Do složky modelu přidejte třídu s názvem **CustomerEntity.cs** a přidejte do ní následující kód.

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Model/CustomerEntity.cs":::

Tento kód definuje třídu entity, která používá křestní jméno zákazníka jako klíč řádku a příjmení jako klíč oddílu. V tabulce ji pak jednoznačně identifikuje kombinace klíče oddílu a řádku entity. Na entity se stejným klíčem oddílu je možné zadávat dotazy rychleji než entity s různými klíči oddílů, ale pomocí různých klíčů oddílu můžete dosáhnout větší škálovatelnosti paralelních operací. Entity, které se mají ukládat do tabulek, musí být podporovaného typu, například odvozené ze třídy [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity). Vlastnosti entity, které chcete uložit do tabulky, musí být veřejné vlastnosti typu a musí podporovat získávání i nastavování hodnot. Typ entity navíc musí zveřejňovat konstruktor bez parametrů.

## <a name="insert-or-merge-an-entity"></a>Vložení nebo sloučení entity

Následující příklad kódu vytvoří objekt entity a přidá jej do tabulky. Metoda InsertOrMerge v rámci třídy [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) se používá k vložení nebo sloučení entity. Pro provedení operace je volána metoda [CloudTable.ExecuteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtable.executeasync?view=azure-dotnet&preserve-view=true) . 

Klikněte pravým tlačítkem na projekt **CosmosTableSamples**. Vyberte **Přidat**, **Nová položka** a přidejte třídu s názvem **SamplesUtils.cs**. Tato třída ukládá veškerý kód potřebný k provádění operací CRUD u entit. 

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="InsertItem":::

## <a name="get-an-entity-from-a-partition"></a>Získat entitu z oddílu

Entitu můžete získat z oddílu pomocí metody načíst pod třídou [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) . Následující příklad kódu získá klíč řádku klíč oddílu, e-mail a telefonní číslo entity zákazníka. Tento příklad také vytiskne jednotky žádosti spotřebované pro dotazování na entitu. Chcete-li zadat dotaz na entitu, přidejte následující kód do souboru **SamplesUtils.cs** :

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="QueryData":::

## <a name="delete-an-entity"></a>Odstranění entity

Entitu můžete po jejím načtení snadno odstranit, a to pomocí stejného vzoru zobrazovaného pro aktualizaci entity. Následující kód načte a odstraní entitu zákazníka. Pokud chcete entitu odstranit, přidejte do souboru **SamplesUtils.cs** následující kód: 

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="QueryData":::

## <a name="execute-the-crud-operations-on-sample-data"></a>Provádění operací CRUD pro ukázková data

Po definování metod pro vytvoření tabulky, vložení nebo sloučení entit spusťte tyto metody pro ukázková data. Provedete to tak, že kliknete pravým tlačítkem na projekt **CosmosTableSamples**. Vyberte **Přidat**, **Nová položka** a přidejte třídu s názvem **BasicSamples.cs** a přidejte do ní následující kód. Tento kód vytvoří tabulku a přidá do ní entity.

Pokud nechcete entitu a tabulku na konci projektu odstranit, přikomentujte `await table.DeleteIfExistsAsync()` `SamplesUtils.DeleteEntityAsync(table, customer)` metody a z následujícího kódu. Před odstraněním tabulky je nejlepší tyto metody opatřit poznámkami a ověřit data.

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/BasicSamples.cs":::

Předchozí kód vytvoří tabulku, která začíná "demo" a vygenerovaný identifikátor GUID se připojí k názvu tabulky. Pak přidá entitu Customer s křestním jménem a příjmení jako "Harpa Waltera" a později aktualizuje telefonní číslo tohoto uživatele. 

V tomto kurzu jste vytvořili kód, který provádí základní operace CRUD s daty uloženými v rozhraní API pro tabulkym účtu. Můžete také provádět pokročilé operace, jako je například dávkové vkládání dat, dotazování všech dat v rámci oddílu, dotazování na rozsah dat v rámci oddílu, seznam tabulek v účtu, jejichž názvy začínají zadanou předponou. Můžete si stáhnout kompletní ukázkový formulář [Azure-Cosmos-Table-dotnet-Core-Začínáme](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started) s úložištěm GitHub. Třída [AdvancedSamples.cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/master/CosmosTableSamples/AdvancedSamples.cs) má více operací, které můžete s daty provádět.  

## <a name="run-the-project"></a>Spuštění projektu

Z projektu **CosmosTableSamples**. Otevřete třídu s názvem **program.cs** a přidejte do ní následující kód pro volání BasicSamples při spuštění projektu.

:::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Program.cs":::

Nyní Sestavte řešení a stisknutím klávesy F5 spusťte projekt. Při spuštění projektu se zobrazí následující výstup na příkazovém řádku:

:::image type="content" source="./media/tutorial-develop-table-standard/output-from-sample.png" alt-text="Výstup z příkazového řádku":::

Pokud se zobrazí chybová zpráva oznamující, že při spuštění projektu nelze najít Settings.jspro soubor, můžete ho vyřešit přidáním následující položky XML do nastavení projektu. Klikněte pravým tlačítkem na CosmosTableSamples, vyberte Upravit CosmosTableSamples. csproj a přidejte následující položku: 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
Nyní se můžete přihlásit k Azure Portal a ověřit, zda data v tabulce existují. 

:::image type="content" source="./media/tutorial-develop-table-standard/results-in-portal.png" alt-text="Výsledky na portálu":::

## <a name="next-steps"></a>Další kroky

Teď můžete přejít k dalšímu kurzu a Naučte se migrovat data na účet Azure Cosmos DB rozhraní API pro tabulky. 

> [!div class="nextstepaction"]
>[Migrace dat do služby Azure Comsos DB rozhraní API pro tabulky](../cosmos-db/table-import.md)
