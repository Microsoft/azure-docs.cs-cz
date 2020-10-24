---
title: 'Azure Cosmos DB: Vytvoření aplikace seznamu úkolů v prostředí Xamarin'
description: Obsahuje ukázku kódu Xamarin, kterou můžete použít k připojení ke službě Azure Cosmos DB a jejímu dotazování.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/09/2020
ms.author: anfeldma
ms.custom: devx-track-csharp
ms.openlocfilehash: 2d82b7f618757fbf77072bd7147595eec3083885
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92487632"
---
# <a name="quickstart-build-a-todo-app-with-xamarin-using-azure-cosmos-db-sql-api-account"></a>Rychlý Start: Vytvoření aplikace TODO pomocí Azure Cosmos DB účtu rozhraní SQL API pomocí Xamarin

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [ROZHRANÍ .NET V4](create-sql-api-dotnet-V4.md)
> * [Sada Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Můžete snadno vytvořit a dotazovat databáze dotazů, klíčů/hodnot a grafů, které tak můžou využívat výhody použitelnosti v celosvětovém měřítku a možností horizontálního škálování v jádru Azure Cosmos DB.

> [!NOTE]
> Vzorový kód celé ukázkové kanonické aplikace Xamarin ukazující několik nabídek Azure, včetně služby Cosmos DB, najdete [tady](https://github.com/xamarinhq/app-geocontacts) na GitHubu. Tato aplikace ukazuje, jak zobrazit kontakty rozptýlené v různých geografických umístěních a jak těmto kontaktům umožnit aktualizaci svého umístění.

V tomto rychlém startu se dozvíte, jak vytvořit účet rozhraní SQL API služby Azure Cosmos DB, databázi dokumentů a kontejner pomocí webu Azure Portal. Pak sestavíte a nasadíte mobilní aplikaci seznamu úkolů postavenou na [rozhraní SQL .NET API](sql-api-sdk-dotnet.md) a [Xamarin](/xamarin/) s využitím modelu [Xamarin. Forms](/xamarin/) a [architektury MVVM](/xamarin/xamarin-forms/xaml/xaml-basics/data-bindings-to-mvvm).

:::image type="content" source="./media/create-sql-api-xamarin-dotnet/ios-todo-screen.png" alt-text="Aplikace seznamu úkolů Xamarin spuštěná v systému iOS":::

## <a name="prerequisites"></a>Předpoklady

Pokud vyvíjíte ve Windows a ještě nemáte nainstalovanou aplikaci Visual Studio 2019, můžete si stáhnout a použít **bezplatnou** [edici Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Nezapomeňte při instalaci sady Visual Studio povolit sady funkcí **Vývoj pro Azure** a **Vývoj mobilních aplikací pomocí .NET**.

Pokud používáte Mac, můžete si stáhnout **bezplatnou verzi** [Visual Studio pro Mac](https://www.visualstudio.com/vs/mac/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Přidání kontejneru

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Přidání ukázkových dat

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Dotazování dat

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Teď naklonujte aplikaci Xamarin SQL API z GitHubu, Projděte si kód, Získejte klíče rozhraní API a spusťte ho. Přesvědčíte se, jak snadno se pracuje s daty prostřednictvím kódu programu.

1. Otevřete příkazový řádek, vytvořte novou složku git-samples a pak příkazový řádek zavřete.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Otevřete okno terminálu Git, například Git Bash, a pomocí příkazu `cd` přejděte do nové složky, do které chcete nainstalovat ukázkovou aplikaci.

    ```bash
    cd "C:\git-samples"
    ```

3. Ukázkové úložiště naklonujete spuštěním následujícího příkazu. Tento příkaz vytvoří na vašem počítači kopii ukázkové aplikace.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-xamarin-getting-started.git
    ```

4. V aplikaci Visual Studio otevřete **C:\git-samples\azure-Cosmos-DB-SQL-Xamarin-Getting-started\src\ToDoItems.sln** 

## <a name="obtain-your-api-keys"></a>Získání klíčů rozhraní API

Vraťte se na Azure Portal, kde najdete informace o klíčích rozhraní API, a zkopírujte je do aplikace.

1. Na webu [Azure Portal](https://portal.azure.com/) v levém navigačním panelu vašeho účtu rozhraní SQL API služby Azure Cosmos DB klikněte na **Klíče** a pak na **Klíče pro čtení i zápis**. V dalším kroku zkopírujete pomocí tlačítek kopírování na pravé straně obrazovky identifikátor URI a primární klíč do souboru APIKeys.cs.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/keys.png" alt-text="Aplikace seznamu úkolů Xamarin spuštěná v systému iOS":::

2. V aplikaci Visual Studio otevřete **ToDoItems. Core/helps/APIKeys. cs**.

3. Na webu Azure Portal pomocí tlačítka pro kopírování Zkopírujte hodnotu **identifikátoru URI** a nastavte ji jako hodnotu `CosmosEndpointUrl` proměnné v APIKeys.cs.

    ```csharp
    //#error Enter the URL of your Azure Cosmos DB endpoint here
            public static readonly string CosmosEndpointUrl = "[URI Copied from Azure Portal]";
    ```

4. Na webu Azure Portal pomocí tlačítka pro kopírování Zkopírujte hodnotu **primárního klíče** a nastavte ji jako hodnotu `Cosmos Auth Key` v APIKeys.cs.

    ```csharp
    //#error Enter the read/write authentication key of your Azure Cosmos DB endpoint here
            public static readonly string CosmosAuthKey = "[PRIMARY KEY copied from Azure Portal";
    ```

[!INCLUDE [cosmos-db-auth-key-info](../../includes/cosmos-db-auth-key-info.md)]

## <a name="review-the-code"></a>Kontrola kódu

Toto řešení ukazuje, jak vytvořit aplikaci seznamu úkolů s využitím rozhraní SQL API služby Azure Cosmos DB a Xamarin.Forms. Aplikace obsahuje dvě karty. První karta obsahuje zobrazení seznamu ještě nesplněných položek seznamu úkolů. Druhá karta zobrazuje splněné položky seznamu úkolů. Kromě zobrazení nesplněných položek seznamu úkolů můžete na první kartě také přidávat nové položky seznamu úkolů, upravovat stávající a označovat položky jako splněné.

:::image type="content" source="./media/create-sql-api-xamarin-dotnet/android-todo-screen.png" alt-text="Aplikace seznamu úkolů Xamarin spuštěná v systému iOS":::

Kód v řešení ToDoItems obsahuje:

* **ToDoItems. Core**
   * Toto je .NET Standard projekt, který uchovává projekt Xamarin. Forms a kód logiky sdílené aplikace, který uchovává položky ToDo v rámci Azure Cosmos DB.
* **ToDoItems. Android**
  * Tento projekt obsahuje aplikaci pro Android.
* **ToDoItems. iOS**
  * Tento projekt obsahuje aplikaci pro iOS.

Teď se rychle podíváme na to, jak aplikace komunikuje se službou Azure Cosmos DB.

* Do všech projektů je potřeba přidat balíček NuGet [Microsoft.Azure.DocumentDb.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/).
* `ToDoItem`Třída ve složce **ToDoItems. Core/Models** modeluje dokumenty v kontejneru **Items** , které jste vytvořili výše. Mějte na paměti, že v názvech vlastností se rozlišují malá a velká písmena.
* `CosmosDBService`Třída ve složce **ToDoItems. Core/Services** zapouzdřuje komunikaci s Azure Cosmos DB.
* V třídě `CosmosDBService` je proměnná typu `DocumentClient`. `DocumentClient`Slouží ke konfiguraci a provádění požadavků na účet Azure Cosmos DB a je vytvořena instance:

    ```csharp
    docClient = new DocumentClient(new Uri(APIKeys.CosmosEndpointUrl), APIKeys.CosmosAuthKey);
    ```

* Při dotazování kontejneru pro dokumenty se `DocumentClient.CreateDocumentQuery<T>` používá metoda, jak je vidět ve `CosmosDBService.GetToDoItems` funkci:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=GetToDoItems)] 

    `CreateDocumentQuery<T>`Převezme identifikátor URI, který odkazuje na kontejner vytvořený v předchozí části. Můžete také zadat operátory LINQ, jako je například klauzule `Where`. V tomto případě se vrátí pouze nesplněné položky seznamu úkolů.

    Funkce `CreateDocumentQuery<T>` se provádí asynchronně a vrací `IQueryable<T>`. Metoda `AsDocumentQuery` však převede `IQueryable<T>` na objekt `IDocumentQuery<T>`, který je možné spustit asynchronně. Díky tomu se v mobilních aplikacích neblokuje vlákno uživatelského rozhraní.

    Funkce `IDocumentQuery<T>.ExecuteNextAsync<T>` načte stránku výsledků ze služby Azure Cosmos DB a pomocí `HasMoreResults` zkontroluje, jestli zbývá vrátit ještě nějaké výsledky.

> [!TIP]
> Několik funkcí, které fungují v kontejnerech a dokumentech Azure Cosmos, přebírají identifikátor URI jako parametr, který určuje adresu kontejneru nebo dokumentu. Tento identifikátor URI se vytváří pomocí třídy `URIFactory`. Pomocí této třídy lze vytvořit všechny identifikátory URI pro databáze, kontejnery a dokumenty.

* `ComsmosDBService.InsertToDoItem`Funkce ukazuje, jak vložit nový dokument:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=InsertToDoItem)] 

    Je zadán identifikátor URI položky a také položka, která má být vložena.

* `CosmosDBService.UpdateToDoItem`Funkce ukazuje, jak nahradit existující dokument novým:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=UpdateToDoItem)] 

    Tady je potřeba nový identifikátor URI, který jednoznačně identifikuje dokument, který se má nahradit, a je získaný pomocí `UriFactory.CreateDocumentUri` a předáním názvů databáze a kontejneru a ID dokumentu.

    Metoda `DocumentClient.ReplaceDocumentAsync` nahradí dokument určený identifikátorem URI za nový dokument určený jako parametr.

* Odstranění položky je znázorněno ve `CosmosDBService.DeleteToDoItem` funkci:

   [!code-csharp[](~/samples-cosmosdb-xamarin/src/ToDoItems.Core/Services/CosmosDBService.cs?name=DeleteToDoItem)] 

    Nezapomeňte si znovu vytvořit jedinečný identifikátor URI dokumentu, který se vytvoří a předává do `DocumentClient.DeleteDocumentAsync` funkce.

## <a name="run-the-app"></a>Spuštění aplikace

Teď jste aktualizovali aplikaci a zadali do ní všechny informace potřebné ke komunikaci s Azure Cosmos DB.

V následujících krocích se dozvíte, jak aplikaci spustit s použitím ladicího programu sady Visual Studio pro Mac.

> [!NOTE]
> Použití aplikace ve verzi pro Android je naprosto stejné, případné rozdíly budou popsané v následujících krocích. Pokud chcete ladit v sadě Visual Studio ve Windows, přečtěte si návod v příslušné dokumentaci pro [iOS](/xamarin/ios/deploy-test/debugging-in-xamarin-ios?tabs=vswin) a [Android](/xamarin/android/deploy-test/debugging/).

1. Nejprve vyberte platformu, na kterou chcete cílit, kliknutím na zvýrazněnou rozevírací nabídku a výběrem možnosti ToDoItems.iOS pro iOS nebo ToDoItems.Android pro Android.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/ide-select-platform.png" alt-text="Aplikace seznamu úkolů Xamarin spuštěná v systému iOS":::

2. Pokud chcete zahájit ladění aplikace, stiskněte Cmd + Enter nebo klikněte na tlačítko Přehrát.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/ide-start-debug.png" alt-text="Aplikace seznamu úkolů Xamarin spuštěná v systému iOS":::

3. Po spuštění simulátoru iOS nebo emulátoru Android se v dolní (iOS) nebo horní (Android) části obrazovky aplikace zobrazí 2 karty. Na první kartě se zobrazí ještě nesplněné položky seznamu úkolů a na druhé se zobrazí splněné položky seznamu úkolů.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/ios-droid-started.png" alt-text="Aplikace seznamu úkolů Xamarin spuštěná v systému iOS":::

4. Pokud chcete splnit položku seznamu úkolů v iOS, posuňte ji doleva a klepněte na tlačítko **Complete** (Splněno). Pokud chcete splnit položku seznamu úkolů v Androidu, dlouze ji stiskněte a pak klepněte na tlačítko Complete (Splněno).

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/simulator-complete.png" alt-text="Aplikace seznamu úkolů Xamarin spuštěná v systému iOS":::

5. Pokud chcete upravit položku seznamu úkolů, klepněte na ni a zobrazí se nová obrazovka, na které můžete zadat nové hodnoty. Klepnutím na tlačítko Save (Uložit) se změny uloží do služby Azure Cosmos DB.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/simulator-edit.png" alt-text="Aplikace seznamu úkolů Xamarin spuštěná v systému iOS":::

6. Pokud chcete přidat položku seznamu úkolů, klepněte na tlačítko **Add** (Přidat) v pravém horním rohu domovské obrazovky a zobrazí se nová prázdná stránka pro úpravy.

    :::image type="content" source="./media/create-sql-api-xamarin-dotnet/simulator-add.png" alt-text="Aplikace seznamu úkolů Xamarin spuštěná v systému iOS":::

## <a name="review-slas-in-the-azure-portal"></a>Ověření smluv SLA na webu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se seznámili s postupem vytvoření účtu Azure Cosmos, vytvoření kontejneru pomocí Průzkumník dat a sestavení a nasazení aplikace Xamarin. Teď můžete do svého účtu Azure Cosmos importovat další data.

> [!div class="nextstepaction"]
> [Importování dat do služby Azure Cosmos DB](import-data.md)