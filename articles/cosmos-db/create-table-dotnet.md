---
title: 'Rychlý Start: rozhraní API pro tabulky s využitím rozhraní .NET Azure Cosmos DB'
description: Tento rychlý start ukazuje, jak použít rozhraní Azure Cosmos DB Table API k vytvoření aplikace pomocí webu Azure Portal a .NET.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/28/2020
ms.author: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 513af71384fd1fa5d38ee3ec367a42892a662444
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096933"
---
# <a name="quickstart-build-a-table-api-app-with-net-sdk-and-azure-cosmos-db"></a>Rychlý Start: Vytvoření aplikace rozhraní API pro tabulky pomocí sady .NET SDK a Azure Cosmos DB 
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](./table-storage-how-to-use-python.md)
>  

Tento rychlý start ukazuje, jak pomocí .NET a rozhraní [Table API](table-introduction.md) služby Azure Cosmos DB sestavit aplikaci naklonováním příkladu z GitHubu. Tento rychlý start také ukazuje, jak vytvořit účet služby Azure Cosmos DB a jak pomocí Průzkumníku dat vytvářet tabulky a entity na webu Azure Portal.

## <a name="prerequisites"></a>Předpoklady

Pokud ještě nemáte nainstalovanou aplikaci Visual Studio 2019, můžete si stáhnout a použít **bezplatnou** [edici Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development** .

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Přidání tabulky

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Přidání ukázkových dat

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Teď naklonujeme aplikaci Table z GitHubu, nastavíme připojovací řetězec a spustíme ji. Přesvědčíte se, jak snadno se pracuje s daty prostřednictvím kódu programu. 

1. Otevřete příkazový řádek, vytvořte novou složku git-samples a pak příkazový řádek zavřete.

   ```bash
   md "C:\git-samples"
   ```

2. Otevřete okno terminálu Git, například Git Bash, a pomocí příkazu `cd` přejděte do nové složky, do které chcete nainstalovat ukázkovou aplikaci.

   ```bash
   cd "C:\git-samples"
   ```

3. Ukázkové úložiště naklonujete spuštěním následujícího příkazu. Tento příkaz vytvoří na vašem počítači kopii ukázkové aplikace.

   ```bash
   git clone https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started.git
   ```

> [!TIP]
> Podrobnější návod k podobnému kódu naleznete v článku ukázkový článek pro [Cosmos DB rozhraní API pro tabulky](./tutorial-develop-table-dotnet.md) .

## <a name="open-the-sample-application-in-visual-studio"></a>Otevření ukázkové aplikace v sadě Visual Studio

1. V sadě Visual Studio z nabídky **Soubor** zvolte **Otevřít** a pak **Projekt nebo řešení** . 

   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-open-solution.png" alt-text="Otevření řešení"::: 

2. Přejděte do složky, do které jste naklonováni ukázkovou aplikaci, a otevřete soubor TableStorage. sln.

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. Pokud chcete zjistit, jak se v kódu vytvářejí prostředky databáze, můžete si prohlédnout následující fragmenty kódu. V opačném případě můžete přeskočit k části [aktualizace připojovacího řetězce](#update-your-connection-string) v tomto dokumentu.

* Následující kód ukazuje, jak vytvořit tabulku v rámci Azure Storage:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/Common.cs" id="CreateTable":::

* Následující kód ukazuje, jak vložit data do tabulky:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="InsertItem":::

* Následující kód ukazuje, jak zadávat dotazy na data z tabulky:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="QueryData":::

* Následující kód ukazuje, jak odstranit data z tabulky:

  :::code language="csharp" source="~/azure-cosmosdb-dotnet-table/CosmosTableSamples/SamplesUtils.cs" id="DeleteItem":::

## <a name="update-your-connection-string"></a>Aktualizace připojovacího řetězce

Teď se vraťte zpátky na portál Azure Portal, kde najdete informace o připojovacím řetězci, a zkopírujte je do aplikace. Tím aplikaci umožníte komunikovat s hostovanou databází. 

1. Na webu [Azure Portal](https://portal.azure.com/) klikněte na **Připojovací řetězec** . Pomocí tlačítka pro kopírování na pravé straně okna zkopírujte **PRIMÁRNÍ PŘIPOJOVACÍ ŘETĚZEC** .

   :::image type="content" source="./media/create-table-dotnet/connection-string.png" alt-text="Otevření řešení"
   }
   ```

4. Stisknutím kombinace kláves CTRL + S uložte **Settings.jsdo** souboru.

Teď jste aktualizovali aplikaci a zadali do ní všechny informace potřebné ke komunikaci s Azure Cosmos DB. 

## <a name="build-and-deploy-the-app"></a>Sestavení a nasazení aplikace

1. V aplikaci Visual Studio klikněte pravým tlačítkem na projekt **CosmosTableSamples** v **Průzkumník řešení** a pak klikněte na **Spravovat balíčky NuGet** . 

   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-manage-nuget.png" alt-text="Otevření řešení":::

2. Do pole **Procházet** NuGet zadejte Microsoft. Azure. Cosmos. Table. Vyhledá se klientská knihovna rozhraní Cosmos DB Table API. Všimněte si, že tato knihovna je aktuálně dostupná pro .NET Framework a .NET Standard. 
   
   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-nuget-browse.png" alt-text="Otevření řešení":::

3. Kliknutím na **nainstalovat** nainstalujte knihovnu **Microsoft. Azure. Cosmos. Table** . Tím se nainstaluje balíček rozhraní Azure Cosmos DB Table API a všechny závislosti.

4. Když spustíte celou aplikaci, ukázková data se vloží do entity tabulky a odstraní se na konci, takže se žádná data nevloží, pokud spustíte celou ukázku. Můžete však vložit některé zarážky k zobrazení dat. Otevřete soubor BasicSamples.cs a klikněte pravým tlačítkem na řádek 52, vyberte **zarážku** a pak vyberte **Vložit zarážku** . Vložte další zarážku na řádku 55.

   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-breakpoint.png" alt-text="Otevření řešení"::: 

5. Stisknutím klávesy F5 spusťte aplikaci. V okně konzoly se zobrazí název nové databáze tabulek (v tomto případě demoa13b1) v Azure Cosmos DB. 
    
   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-console.png" alt-text="Otevření řešení":::

   Až se dostanete k první zarážce, přejděte zpět do Průzkumníku dat na webu Azure Portal. Klikněte na tlačítko **Aktualizovat** , rozbalte tabulku demo* a klikněte na **Entity** . Na kartě **Entity** na pravé straně se zobrazí nově přidaná entita pro Waltera Harpa. Všimněte si, že telefonní číslo nové entity je 425-555-0101.

   :::image type="content" source="media/create-table-dotnet/azure-cosmosdb-entity.png" alt-text="Otevření řešení":::
    
   Pokud se zobrazí chybová zpráva oznamující, že při spuštění projektu nelze najít Settings.jspro soubor, můžete ho vyřešit přidáním následující položky XML do nastavení projektu. Klikněte pravým tlačítkem na CosmosTableSamples, vyberte Upravit CosmosTableSamples. csproj a přidejte následující položku: 

   ```csharp
     <ItemGroup>
       <None Update="Settings.json">
         <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
       </None>
     </ItemGroup>
   ```

6. Zavřete kartu **Entity** v Průzkumníku dat.
    
7. Stisknutím klávesy F5 spusťte aplikaci až k další zarážce. 

    Až se dostanete k zarážce, přejděte zpět na web Azure Portal, znovu klikněte na **Entity** a otevřete kartu **Entity** . Všimněte si, že se telefonní číslo aktualizovalo na 425-555-0105.

8. Stisknutím klávesy F5 spusťte aplikaci. 
 
   Aplikace přidá entity pro použití v pokročilejší ukázkové aplikaci, kterou rozhraní Table API v současné době nepodporuje. Aplikace pak odstraní tabulku vytvořenou ukázkovou aplikací.

9. V okně konzoly stiskněte Enter a ukončete aplikaci. 
  

## <a name="review-slas-in-the-azure-portal"></a>Ověření smluv SLA na webu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se seznámili s postupem vytvoření databázového účtu Azure Cosmos DB, vytvoření tabulky pomocí Průzkumníka dat a spuštění aplikace.  Teď můžete zadávat dotazy na svá data pomocí rozhraní API tabulky.  

> [!div class="nextstepaction"]
> [Import tabulkových dat do rozhraní Table API](table-import.md)