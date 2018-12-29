---
title: Sestavení aplikace Xamarin.Forms s .NET a rozhraní API služby Azure Cosmos DB pro MongoDB
description: Představuje ukázku kódu Xamarin, které můžete použít k připojení a dotazování pomocí rozhraní API služby Azure Cosmos DB pro MongoDB
services: cosmos-db
author: codemillmatt
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.custom: quickstart, xamarin
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 06/20/2018
ms.author: masoucou
ms.openlocfilehash: 456d35666e1475379b7ec90e8683ed47a1946f67
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2018
ms.locfileid: "53808957"
---
# <a name="quickstart-quickstart-build-a-xamarinforms-app-with-net-and-azure-cosmos-dbs-api-for-mongodb"></a>Rychlý start: Rychlý start: Sestavení aplikace Xamarin.Forms s .NET a rozhraní API služby Azure Cosmos DB pro MongoDB

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Můžete snadno vytvořit a dotazovat databáze dotazů, klíčů/hodnot a grafů, které tak můžou využívat výhody použitelnosti v celosvětovém měřítku a možností horizontálního škálování v jádru databáze Azure Cosmos.

Tento rychlý start ukazuje, jak vytvořit [Cosmos účet byl konfigurován s rozhraním API služby Azure Cosmos DB pro MongoDB](mongodb-introduction.md), databáze dokumentů a kolekce pomocí webu Azure portal. Potom sestavíte aplikaci seznamu úkolů aplikace Xamarin.Forms s použitím [ovladač MongoDB .NET](https://docs.mongodb.com/ecosystem/drivers/csharp/).

## <a name="prerequisites-to-run-the-sample-app"></a>Požadavky na spuštění ukázkové aplikace

Pro spuštění ukázky budete potřebovat sadu [Visual Studio](https://www.visualstudio.com/downloads/) nebo [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/) a platný účet služby Azure Cosmos DB.

Pokud ještě nemáte sadu Visual Studio, stáhněte si sadu [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/), součástí jejíž instalace je i sada funkcí **Vývoj mobilních aplikací pomocí .NET**.

Pokud chcete raději pracovat na Macu, stáhněte si [Visual Studio pro Mac](https://visualstudio.microsoft.com/vs/mac/) a spusťte instalaci.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<a id="create-account"></a>

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

Ukázka popsaná v tom článku je kompatibilní s MongoDB.Driver verze 2.6.1.

## <a name="clone-the-sample-app"></a>Klonování ukázkové aplikace

Nejprve stáhněte ukázkovou aplikaci z Githubu. Tato aplikace implementuje aplikaci seznamu úkolů s modelem úložiště dokumentů MongoDB.

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started.git
    ```

Pokud nechcete použít git, můžete [projekt stáhnout také jako soubor ZIP](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started/archive/master.zip).

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. Pokud chcete zjistit, jak se v kódu vytvářejí prostředky databáze, můžete si prohlédnout následující fragmenty kódu. Jinak můžete přeskočit přímo k části [Aktualizace informací o připojení](#update-your-connection-string).

Následující fragmenty kódu pocházejí ze `MongoService` třídy v následujícím umístění: src/TaskList.Core/Services/MongoService.cs.

* Inicializuje se klient Mongo.
    ```cs
    MongoClientSettings settings = MongoClientSettings.FromUrl(
        new MongoUrl(APIKeys.ConnectionString)
    );

    settings.SslSettings =
        new SslSettings() { EnabledSslProtocols = SslProtocols.Tls12 };

    MongoClient mongoClient = new MongoClient(settings);
    ```

* Načte se odkaz na databázi a kolekci. Sada .NET SDK MongoDB databázi i kolekci automaticky vytvoří, pokud ještě neexistují.
    ```cs
    string dbName = "MyTasks";
    string collectionName = "TaskList";

    var db = mongoClient.GetDatabase(dbName);

    var collectionSettings = new MongoCollectionSettings {
        ReadPreference = ReadPreference.Nearest
    };

    tasksCollection = db.GetCollection<MyTask>(collectionName, collectionSettings);
    ```
* Načtou se všechny dokumenty v podobě seznamu.
    ```cs
    var allTasks = await TasksCollection
                    .Find(new BsonDocument())
                    .ToListAsync();
    ```

* Odešle se dotaz na konkrétní dokumenty.
    ```cs
    public async Task<List<MyTask>> GetIncompleteTasksDueBefore(DateTime date)
    {
        var tasks = await TasksCollection
                        .AsQueryable()
                        .Where(t => t.Complete == false)
                        .Where(t => t.DueDate < date)
                        .ToListAsync();

        return tasks;
    }
    ```

* Vytvoření úlohy a vložte jej do kolekce.
    ```cs
    public async Task CreateTask(MyTask task)
    {
        await TasksCollection.InsertOneAsync(task);
    }
    ```

* Aktualizujte úkol v kolekci.
    ```cs
    public async Task UpdateTask(MyTask task)
    {
        await TasksCollection.ReplaceOneAsync(t => t.Id.Equals(task.Id), task);
    }
    ```

* Odstraňte úkol z kolekce.
    ```cs
    public async Task DeleteTask(MyTask task)
    {
        await TasksCollection.DeleteOneAsync(t => t.Id.Equals(task.Id));
    }
    ```

<a id="update-your-connection-string"></a>

## <a name="update-your-connection-string"></a>Aktualizace připojovacího řetězce

Teď se vraťte zpátky na portál Azure Portal, kde najdete informace o připojovacím řetězci, a zkopírujte je do aplikace.

1. Na webu [Azure Portal](https://portal.azure.com/) klikněte v účtu databáze Azure Cosmos v levém navigačním panelu na možnost **Připojovací řetězec** a potom klikněte na **Klíče pro čtení i zápis**. V dalších krocích pomocí tlačítek pro kopírování na pravé straně obrazovky zkopírujete Primární připojovací řetězec.

2. Otevřete soubor **APIKeys.cs** v adresáři **Helpers** projektu **TaskList.Core**.

3. Z portálu si zkopírujte hodnotu **primárního připojovacího řetězce** (pomocí tlačítka pro kopírování) a nastavte ji jako hodnotu pole **ConnectionString** v souboru **APIKeys.cs**.

Teď jste aktualizovali aplikaci a zadali do ní všechny informace potřebné ke komunikaci s Azure Cosmos DB.

## <a name="run-the-app"></a>Spuštění aplikace

### <a name="visual-studio-2017"></a>Visual Studio 2017

1. V sadě Visual Studio postupně klikněte v **Průzkumníku řešení** pravým tlačítkem na jednotlivé projekty a potom klikněte na **Spravovat balíčky NuGet**.
2. Klikněte na **Obnovit všechny balíčky NuGet**.
3. Klikněte pravým tlačítkem na **TaskList.Android** a vyberte **Nastavit jako spouštěný projekt**.
4. Stisknutím klávesy F5 spusťte ladění aplikace.
5. Pokud chcete aplikaci spustit v iOS, nejprve se váš počítač připojí k Macu (informace o [postupu](https://docs.microsoft.com/xamarin/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio) najdete tady).
6. Klikněte pravým tlačítkem na **TaskList.iOS** a vyberte **Nastavit jako spouštěný projekt**.
7. Stisknutím klávesy F5 spusťte ladění aplikace.

### <a name="visual-studio-for-mac"></a>Visual Studio pro Mac

1. V závislosti na tom, na které platformě chcete aplikaci spustit, vyberte v rozevíracím seznamu platformy TaskList.iOS nebo TaskList.Android.
2. Stisknutím cmd + Enter spusťte ladění aplikace.

## <a name="review-slas-in-the-azure-portal"></a>Ověření podmínek SLA na portálu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste se seznámili s postupem vytvoření účtu služby Azure Cosmos DB a spuštění aplikace Xamarin.Forms pomocí rozhraní API pro MongoDB. Teď můžete do účtu Cosmos DB importovat další data.

> [!div class="nextstepaction"]
> [Import dat do služby Azure Cosmos DB nakonfigurovaný pro MongoDB API služby Azure Cosmos DB](mongodb-migrate.md)
