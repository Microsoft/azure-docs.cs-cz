---
title: 'Úvodní příručka: Rozhraní API tabulky s rozhraním .NET – Azure Cosmos DB'
description: Tento rychlý start ukazuje, jak použít rozhraní Azure Cosmos DB Table API k vytvoření aplikace pomocí webu Azure Portal a .NET.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/21/2019
ms.author: sngun
ms.openlocfilehash: df26021a3718c17d72f0fdb25588487043918732
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084103"
---
# <a name="quickstart-build-a-table-api-app-with-net-sdk-and-azure-cosmos-db"></a>Úvodní příručka: Vytvoření aplikace rozhraní TABLE API s .NET SDK a Azure Cosmos DB 

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](create-table-python.md)
>  

Tento rychlý start ukazuje, jak pomocí .NET a rozhraní [Table API](table-introduction.md) služby Azure Cosmos DB sestavit aplikaci naklonováním příkladu z GitHubu. Tento rychlý start také ukazuje, jak vytvořit účet služby Azure Cosmos DB a jak pomocí Průzkumníku dat vytvářet tabulky a entity na webu Azure Portal.

## <a name="prerequisites"></a>Požadované součásti

Pokud ještě nemáte nainstalovaný Visual Studio 2019, můžete si stáhnout a použít **bezplatnou** [visual studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development**.

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

> ! To je v pořádku. Podrobnější návod podobného kódu najdete v článku ukázkový článek [rozhraní API tabulky Cosmos DB.](table-storage-how-to-use-dotnet.md)

## <a name="open-the-sample-application-in-visual-studio"></a>Otevření ukázkové aplikace v sadě Visual Studio

1. V sadě Visual Studio z nabídky **Soubor** zvolte **Otevřít** a pak **Projekt nebo řešení**. 

   ![Otevření řešení](media/create-table-dotnet/azure-cosmosdb-open-solution.png) 

2. Přejděte do složky, do které jste klonovali ukázkovou aplikaci, a otevřete soubor TableStorage.sln.

## <a name="update-your-connection-string"></a>Aktualizace připojovacího řetězce

Teď se vraťte zpátky na portál Azure Portal, kde najdete informace o připojovacím řetězci, a zkopírujte je do aplikace. Tím aplikaci umožníte komunikovat s hostovanou databází. 

1. Na webu [Azure Portal](https://portal.azure.com/) klikněte na **Připojovací řetězec**. Pomocí tlačítka pro kopírování na pravé straně okna zkopírujte **PRIMÁRNÍ PŘIPOJOVACÍ ŘETĚZEC**.

   ![Zobrazení a zkopírování PRIMÁRNÍHO PŘIPOJOVACÍHO ŘETĚZCE v podokně Připojovací řetězec](./media/create-table-dotnet/connection-string.png)

2. V sadě Visual Studio otevřete soubor **Settings.json.** 

3. Vložte **primární připojovací řetězec** z portálu do hodnoty StorageConnectionString. Řetězec vložte do uvozovek.

   ```csharp
   {
      "StorageConnectionString": "<Primary connection string from Azure portal>"
   }
   ```

4. Stisknutím kláves CTRL+S uložte soubor **Settings.json.**

Teď jste aktualizovali aplikaci a zadali do ní všechny informace potřebné ke komunikaci s Azure Cosmos DB. 

## <a name="build-and-deploy-the-app"></a>Sestavení a nasazení aplikace

1. V sadě Visual Studio klikněte pravým tlačítkem myši na projekt **CosmosTableSamples** v **Průzkumníku řešení** a potom klikněte na **spravovat balíčky NuGet**. 

   ![Správa balíčků NuGet](media/create-table-dotnet/azure-cosmosdb-manage-nuget.png)

2. Do pole NuGet **Browse** zadejte Microsoft.Azure.Cosmos.Table. Vyhledá se klientská knihovna rozhraní Cosmos DB Table API. Všimněte si, že tato knihovna je aktuálně k dispozici pro rozhraní .NET Framework a .NET Standard. 
   
   ![Karta Procházet NuGet](media/create-table-dotnet/azure-cosmosdb-nuget-browse.png)

3. Kliknutím na **Instalovat** nainstalujte knihovnu **Microsoft.Azure.Cosmos.Table.** Tím se nainstaluje balíček rozhraní Azure Cosmos DB Table API a všechny závislosti.

4. Když spustíte celou aplikaci, ukázková data se vloží do entity tabulky a na konci se odstraní, takže při spuštění celé ukázky neuvidíte žádná data. Můžete však vložit některé zarážky pro zobrazení dat. Otevřete BasicSamples.cs souboru a klikněte pravým tlačítkem na řádek 52, vyberte **Zarážka**a pak vyberte **Vložit zarážku**. Vložte další zarážku na řádku 55.

   ![Přidání zarážky](media/create-table-dotnet/azure-cosmosdb-breakpoint.png) 

5. Stisknutím klávesy F5 spusťte aplikaci. Okno konzoly zobrazí název nové databáze tabulek (v tomto případě demoa13b1) v Azure Cosmos DB. 
    
   ![Výstup konzoly](media/create-table-dotnet/azure-cosmosdb-console.png)

   Až se dostanete k první zarážce, přejděte zpět do Průzkumníku dat na webu Azure Portal. Klikněte na tlačítko **Aktualizovat**, rozbalte tabulku demo* a klikněte na **Entity**. Na kartě **Entity** na pravé straně se zobrazí nově přidaná entita pro Waltera Harpa. Všimněte si, že telefonní číslo nové entity je 425-555-0101.

   ![Nová entita](media/create-table-dotnet/azure-cosmosdb-entity.png)
    
   Pokud se zobrazí chyba, která říká, že soubor Settings.json nelze při spuštění projektu najít, můžete jej vyřešit přidáním následující položky XML do nastavení projektu. Klikněte pravým tlačítkem myši na CosmosTableSamples, vyberte upravit cosmostablesamples.csproj a přidejte následující položkuSkupina: 

   ```csharp
     <ItemGroup>
       <None Update="Settings.json">
         <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
       </None>
     </ItemGroup>
   ```

6. Zavřete kartu **Entity** v Průzkumníku dat.
    
7. Stisknutím klávesy F5 spusťte aplikaci až k další zarážce. 

    Až se dostanete k zarážce, přejděte zpět na web Azure Portal, znovu klikněte na **Entity** a otevřete kartu **Entity**. Všimněte si, že se telefonní číslo aktualizovalo na 425-555-0105.

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

