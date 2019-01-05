---
title: 'Rychlý start: Sestavení webové aplikace ASP.NET s Azure Cosmos DB pomocí rozhraní SQL API a webu Azure portal'
description: V tomto rychlém startu pomocí SQL API služby Azure Cosmos DB a webu Azure portal k vytvoření webové aplikace ASP.NET
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 12/01/2018
ms.author: dech
ms.openlocfilehash: 5c416b6db5cc7708c5523d6da12af651d706811e
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54037646"
---
# <a name="quickstart-build-an-aspnet-web-app-with-azure-cosmos-db-using-the-sql-api-and-the-azure-portal"></a>Rychlý start: Sestavení webové aplikace ASP.NET s Azure Cosmos DB pomocí rozhraní SQL API a webu Azure portal

> [!div class="op_single_selector"]
> * [.NET (preview)](create-sql-api-dotnet-preview.md)
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  
> 

Azure Cosmos DB je globálně distribuovaná, vysoce dostupné, vícemodelová databázová služba od Microsoftu. Můžete snadno vytvořit a dotazovat databáze dotazů, klíčů/hodnot a grafů, které tak můžou využívat výhody použitelnosti v celosvětovém měřítku a možností horizontálního škálování v jádru databáze Azure Cosmos. 

Tento rychlý start popisuje způsob vytvoření služby Azure Cosmos DB [rozhraní SQL API](sql-api-introduction.md) účtu, databáze a kontejnerů pomocí webu Azure portal. Potom budete moct sestavit a nasadit webovou aplikaci seznamu úkolů v ASP.NET založená na [SQL .NET API](sql-api-sdk-dotnet.md), jak je znázorněno na následujícím snímku obrazovky. 

Tento rychlý start používá verzi 3.0 + .NET SDK služby Azure Cosmos DB. 

![Aplikace seznamu úkolů s ukázkovými daty](./media/create-sql-api-dotnet/azure-comosdb-todo-app-list-preview.png)

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte nainstalovanou sadu Visual Studio 2017, můžete stáhnout a použít **bezplatnou verzi** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Ujistěte se, že povolíte **vývoj pro Azure** zatížení při instalaci sady Visual Studio.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Vytvoření účtu databáze

[!INCLUDE [cosmos-db-create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount-preview.md)]

<a id="create-collection"></a>
## <a name="add-a-collection"></a>Přidání kolekce

[!INCLUDE [cosmos-db-create-collection-preview](../../includes/cosmos-db-create-collection-preview.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Přidání ukázkových dat

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Dotazování dat

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Teď přejděme k práci s kódem. Naklonujeme [aplikaci SQL API z GitHubu](https://github.com/Azure-Samples/cosmos-dotnet-todo-app), nastavíme připojovací řetězec a spustíme ji. 

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
    git clone https://github.com/Azure-Samples/cosmos-dotnet-todo-app.git
    ```

4. Otevřít **todo.sln** soubor řešení v sadě Visual Studio. 

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. Pokud chcete zjistit, jak se v kódu vytvářejí prostředky databáze, můžete si prohlédnout následující fragmenty kódu. Jinak můžete přeskočit přímo k části [Aktualizace informací o připojení](#update-your-connection-string). 

Mějte na paměti, pokud jste se seznámili s předchozí verzí sady .NET SDK, můžou sloužit k zobrazení podmínek kolekce a dokumentu. Protože Azure Cosmos DB podporuje několik modelů rozhraní API, verze 3.0 a sady .NET SDK používá obecné podmínky "kontejner" a "položka". Kontejner může být kolekce, graf nebo tabulka. Položka představuje obsah uvnitř kontejneru a může to být dokument, pár hrany a vrcholu nebo řádek. [Další informace o databází, kontejnerů a položek.](databases-containers-items.md)

Následující fragmenty kódu jsou převzaty ze souboru ToDoItemService.cs.

* CosmosClient je inicializován na řádcích 68 69.

    ```csharp
    CosmosConfiguration config = new CosmosConfiguration(Endpoint, PrimaryKey);
    client = new CosmosClient(config);
    ```

* Na řádku 71 se vytvoří nová databáze.

    ```csharp
    CosmosDatabase database = await client.Databases.CreateDatabaseIfNotExistsAsync(DatabaseId);
    ```

* Na řádku 72 s klíčem oddílu se vytvoří nový kontejner "/ category."

    ```csharp
    CosmosContainer container = await database.Containers.CreateContainerIfNotExistsAsync(ContainerId, "/category");
    ```

## <a name="update-your-connection-string"></a>Aktualizace připojovacího řetězce

Teď se vraťte zpátky na portál Azure Portal, kde najdete informace o připojovacím řetězci, a zkopírujte je do aplikace.

1. Na webu [Azure Portal](https://portal.azure.com/) v účtu služby Azure Cosmos DB v levém navigačním panelu vyberte **Klíče** a pak **Klíče pro čtení i zápis**. V dalším kroku zkopírujte pomocí tlačítek kopírování na pravé straně obrazovky identifikátor URI a primární klíč do souboru web.config.

    ![Zobrazení a zkopírování přístupového klíče na webu Azure Portal v okně Klíče](./media/create-sql-api-dotnet/keys.png)

2. V sadě Visual Studio 2017 otevřete **web.config** souboru. 

3. Zkopírujte hodnotu identifikátoru URI z portálu (pomocí tlačítka kopírování) a nastavte ji jako hodnotu ``endpoint`` klíčů v souboru web.config. 

    `<add key="endpoint" value="FILLME" />`

4. Potom z portálu zkopírujte hodnotu PRIMÁRNÍHO klíče a nastavte ji jako hodnotu ``primarykey`` v souboru web.config. 

    `<add key="primaryKey" value="FILLME" />`
    
5. Pak aktualizujte hodnotu databáze a kontejneru tak, aby odpovídaly názvu databáze a kontejner, který jste vytvořili dříve. Teď jste aktualizovali aplikaci a zadali do ní všechny informace potřebné ke komunikaci s Azure Cosmos DB. 

    `<add key="database" value="Tasks" />`

    `<add key="container" value="Items" />`
    
## <a name="run-the-web-app"></a>Spuštění webové aplikace

1. V **Průzkumníka řešení**, klikněte pravým tlačítkem na váš nový projekt konzolové aplikace, která je v rámci řešení sady Visual Studio, a potom klikněte na tlačítko **spravovat balíčky NuGet...**
    
    ![Snímek obrazovky místní nabídky projektu](./media/create-sql-api-dotnet/manage-nuget-package.png)
1. V **NuGet** klikněte na tlačítko **Procházet**a typ **Microsoft.Azure.Cosmos** do vyhledávacího pole.
1. Najděte ve výsledcích **Microsoft.Azure.Cosmos** a klikněte na tlačítko **nainstalovat**.
   ID balíčku s klientskou knihovnou rozhraní Azure Cosmos DB SQL API je [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).

   ![Snímek obrazovky nabídky NuGet pro vyhledání Azure Cosmos DB Client SDK](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget-2.png)

    Pokud se vám zobrazí zpráva týkající se kontroly změn řešení, klikněte na **OK**. Pokud se vám zobrazí zpráva týkající se přijetí licence, klikněte na **Souhlasím**.

1. Spusťte aplikaci stisknutím CTRL + F5. Aplikace se zobrazí v prohlížeči. 

1. V prohlížeči vyberte **Create New** (Vytvořit nový) a vytvořte v aplikaci seznamu úkolů několik nových úloh. Měli byste taky vidět úlohy, kterou jste vytvořili v [Přidání ukázkových dat](#add-sample-data)

   ![Aplikace seznamu úkolů s ukázkovými daty](./media/create-sql-api-dotnet/azure-comosdb-todo-app-list-preview.png)

Vám může nyní přejděte zpět do Průzkumníku dat naleznete v tématu, dotazování, upravit a pracovat s těmito novými daty. 

## <a name="review-slas-in-the-azure-portal"></a>Ověření podmínek SLA na portálu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste zjistili, jak vytvořit účet služby Azure Cosmos DB, vytvoření kontejneru a přidat položky do něj pomocí Průzkumníku dat a spuštění webové aplikace. Teď můžete do účtu Cosmos DB importovat další data. 

> [!div class="nextstepaction"]
> [Importování dat do služby Azure Cosmos DB](import-data.md)


