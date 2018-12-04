---
title: 'Rychlý start: Table API s využitím .NET – Azure Cosmos DB'
description: Tento rychlý start ukazuje, jak použít rozhraní Azure Cosmos DB Table API k vytvoření aplikace pomocí webu Azure Portal a .NET.
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.custom: quickstart connect, mvc
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 08/17/2018
ms.author: sngun
ms.openlocfilehash: a36c1e68ddd68e497966d50122e9f27c72609948
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52834447"
---
# <a name="quickstart-build-a-table-api-app-with-net-and-azure-cosmos-db"></a>Rychlý start: Sestavení aplikace Table API pomocí .NET a Azure Cosmos DB 

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](create-table-python.md)
>  

Tento rychlý start ukazuje, jak pomocí .NET a rozhraní [Table API](table-introduction.md) služby Azure Cosmos DB sestavit aplikaci naklonováním příkladu z GitHubu. Tento rychlý start také ukazuje, jak vytvořit účet služby Azure Cosmos DB a jak pomocí Průzkumníku dat vytvářet tabulky a entity na webu Azure Portal.

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Můžete snadno vytvořit a dotazovat databáze dotazů, klíčů/hodnot a grafů, které tak můžou využívat výhody použitelnosti v celosvětovém měřítku a možností horizontálního škálování v jádru databáze Azure Cosmos. 

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte nainstalovanou sadu Visual Studio 2017, můžete stáhnout a použít **bezplatnou verzi** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit možnost **Azure Development**.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

> [!IMPORTANT] 
> Abyste mohli pracovat s obecně dostupnými sadami Table API SDK, musíte si vytvořit nový účet Table API. Obecně dostupné sady SDK nepodporují účty Table API vytvořené během období Preview.
>

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Přidání tabulky

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Přidání ukázkových dat

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Teď naklonujeme aplikaci Table z GitHubu, nastavíme připojovací řetězec a spustíme ji. Přesvědčíte se, jak snadno se pracuje s daty prostřednictvím kódu programu. 

1. Otevřete příkazový řádek, vytvořte novou složku git-samples a potom příkazový řádek zavřete.

    ```bash
    md "C:\git-samples"
    ```

2. Otevřete okno terminálu Git, například Git Bash, a pomocí příkazu `cd` přejděte do nové složky, do které chcete nainstalovat ukázkovou aplikaci.

    ```bash
    cd "C:\git-samples"
    ```

3. Ukázkové úložiště naklonujete spuštěním následujícího příkazu. Tento příkaz vytvoří na vašem počítači kopii ukázkové aplikace.

    ```bash
    git clone https://github.com/Azure-Samples/storage-table-dotnet-getting-started.git
    ```
## <a name="open-the-sample-application-in-visual-studio"></a>Otevření ukázkové aplikace v sadě Visual Studio

1. V sadě Visual Studio z nabídky **Soubor** zvolte **Otevřít** a pak **Projekt nebo řešení**. 

   ![Otevření řešení](media/create-table-dotnet/azure-cosmosdb-open-solution.png) 

2. Přejděte do složky, do které jste naklonovali ukázkovou aplikaci, a otevřete soubor TableStorage.sln.

   ![Otevření naklonované aplikace](media/create-table-dotnet/azure-cosmos-db-open-clone.png) 

## <a name="update-your-connection-string"></a>Aktualizace připojovacího řetězce

Teď se vraťte zpátky na portál Azure Portal, kde najdete informace o připojovacím řetězci, a zkopírujte je do aplikace. Tím aplikaci umožníte komunikovat s hostovanou databází. 

1. Na webu [Azure Portal](http://portal.azure.com/) klikněte na **Připojovací řetězec**. 

    Pomocí tlačítka pro kopírování na pravé straně okna zkopírujte **PRIMÁRNÍ PŘIPOJOVACÍ ŘETĚZEC**.

    ![Zobrazení a zkopírování PRIMÁRNÍHO PŘIPOJOVACÍHO ŘETĚZCE v podokně Připojovací řetězec](./media/create-table-dotnet/connection-string.png)

2. V sadě Visual Studio otevřete soubor App.config. 

3. Vzhledem k tomu, že v tomto kurzu se nepoužívá emulátor úložiště Azure SDK, zrušte komentář u StorageConnectionString na řádku 8 a přidejte komentář u StorageConnectionString na řádku 7. Řádky 7 a 8 by teď měly vypadat takto:

    ```
    <!--key="StorageConnectionString" value="UseDevelopmentStorage=true;" />-->
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=[AccountName];AccountKey=[AccountKey]" />
    ```

4. Vložte **PRIMÁRNÍ PŘIPOJOVACÍ ŘETĚZEC** z portálu do hodnoty StorageConnectionString na řádku 8. Řetězec vložte do uvozovek. 

    > [!IMPORTANT]
    > Pokud váš koncový bod používá adresu documents.azure.com, znamená to, že máte účet Preview, a pokud chcete pracovat s obecně dostupnou sadou Table API SDK, musíte si vytvořit [nový účet Table API](#create-a-database-account). 
    > 

    Řádek 8 by teď měl vypadat nějak takto:

    ```
    <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account-key>;TableEndpoint=https://<account name>.table.cosmosdb.azure.com;" />
    ```

5. Stisknutím CTRL+S soubor App.config uložte.

Teď jste aktualizovali aplikaci a zadali do ní všechny informace potřebné ke komunikaci s Azure Cosmos DB. 

## <a name="build-and-deploy-the-app"></a>Sestavení a nasazení aplikace

1. V sadě Visual Studio klikněte v **Průzkumníku řešení** pravým tlačítkem na projekt **TableStorage** a pak klikněte na **Spravovat balíčky NuGet**. 

   ![Správa balíčků NuGet](media/create-table-dotnet/azure-cosmosdb-manage-nuget.png)
2. Do pole **Procházet** NuGet zadejte *Microsoft.Azure.CosmosDB.Table*. Vyhledá se klientská knihovna rozhraní Cosmos DB Table API. Poznámka: Tato knihovna je aktuálně dostupná pouze pro .NET Standard, pro .NET Core ještě dostupná není.
   
   ![Karta Procházet NuGet](media/create-table-dotnet/azure-cosmosdb-nuget-browse.png)

3. Klikněte na **Nainstalovat** a nainstalujte knihovnu **Microsoft.Azure.CosmosDB.Table**. Tím se nainstaluje balíček rozhraní Azure Cosmos DB Table API a všechny závislosti.

    ![Kliknutí na Nainstalovat](media/create-table-dotnet/azure-cosmosdb-nuget-install.png)

4. Otevřete soubor BasicSamples.cs. Klikněte pravým tlačítkem na řádek 52, vyberte **Zarážka** a pak vyberte **Vložit zarážku**. Vložte další zarážku na řádku 55.

   ![Přidání zarážky](media/create-table-dotnet/azure-cosmosdb-breakpoint.png) 

5. Stisknutím klávesy F5 spusťte aplikaci.

    V okně konzoly se zobrazí název nové databáze tabulek (v tomto případě demo91ab4) ve službě Azure Cosmos DB. 
    
    ![Výstup konzoly](media/create-table-dotnet/azure-cosmosdb-console.png)

    Pokud dojde k chybě v souvislosti se závislostmi, přečtěte si [Řešení potíží](table-sdk-dotnet.md#troubleshooting).

    Až se dostanete k první zarážce, přejděte zpět do Průzkumníku dat na webu Azure Portal. Klikněte na tlačítko **Aktualizovat**, rozbalte tabulku demo* a klikněte na **Entity**. Na kartě **Entity** na pravé straně se zobrazí nově přidaná entita pro Waltera Harpa. Všimněte si, že telefonní číslo nové entity je 425-555-0101.

    ![Nová entita](media/create-table-dotnet/azure-cosmosdb-entity.png)
    
6. Zavřete kartu **Entity** v Průzkumníku dat.
    
7. Stisknutím klávesy F5 spusťte aplikaci až k další zarážce. 

    Až se dostanete k zarážce, přejděte zpět na web Azure Portal, znovu klikněte na **Entity** a otevřete kartu **Entity**. Všimněte si, že se telefonní číslo aktualizovalo na 425-555-0105.

8. Stisknutím klávesy F5 spusťte aplikaci. 
 
   Aplikace přidá entity pro použití v pokročilejší ukázkové aplikaci, kterou rozhraní Table API v současné době nepodporuje. Aplikace pak odstraní tabulku vytvořenou ukázkovou aplikací.

9. V okně konzoly stiskněte Enter a ukončete aplikaci. 
  

## <a name="review-slas-in-the-azure-portal"></a>Ověření podmínek SLA na portálu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste se seznámili s postupem vytvoření databázového účtu Azure Cosmos DB, vytvoření tabulky pomocí Průzkumníka dat a spuštění aplikace.  Teď můžete zadávat dotazy na svá data pomocí rozhraní API tabulky.  

> [!div class="nextstepaction"]
> [Import tabulkových dat do rozhraní Table API](table-import.md)

