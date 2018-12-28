---
title: Vytvoření webové aplikace pomocí rozhraní API služby Azure Cosmos DB pro MongoDB a sady .NET SDK
description: Představuje ukázku kódu .NET, které můžete použít k připojení a dotazování pomocí rozhraní API služby Azure Cosmos DB pro MongoDB.
services: cosmos-db
author: rimman
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.custom: quickstart
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 12/26/2018
ms.author: rimman
ms.openlocfilehash: c6ae9fa46e9dd3abc1a526d5c7121c86d6e9a08c
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53791213"
---
# <a name="build-a-web-app-using-azure-cosmos-dbs-api-for-mongodb-and-net-sdk"></a>Vytvoření webové aplikace pomocí rozhraní API služby Azure Cosmos DB pro MongoDB a sady .NET SDK

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Můžete rychle vytvořit a dotazování databází dokument, klíč/hodnota a grafů, které můžou využívat výhody globální distribuce a možností horizontálního škálování v jádru služby Cosmos DB. 

Tento rychlý start ukazuje, jak vytvořit účet Cosmos s [rozhraní API služby Azure Cosmos DB pro MongoDB](mongodb-introduction.md). Potom budete moct sestavit a nasadit webovou aplikaci seznamu úkolů vyvíjené [ovladač MongoDB .NET](https://docs.mongodb.com/ecosystem/drivers/csharp/).

## <a name="prerequisites-to-run-the-sample-app"></a>Požadavky na spuštění ukázkové aplikace

Ke spuštění ukázky, budete potřebovat [sady Visual Studio](https://www.visualstudio.com/downloads/) a platný účet služby Azure Cosmos DB.

Pokud ještě nemáte sadu Visual Studio, stáhněte si sadu [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/), součástí jejíž instalace je i sada funkcí **Vývoj pro ASP.NET a web**.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Vytvoření účtu databáze

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

Ukázka popsaná v tom článku je kompatibilní s MongoDB.Driver verze 2.6.1.

## <a name="clone-the-sample-app"></a>Klonování ukázkové aplikace

Nejprve stáhněte ukázkovou aplikaci z Githubu. 

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started.git
    ```

Pokud nechcete použít git, můžete [projekt stáhnout také jako soubor ZIP](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started/archive/master.zip).

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. Pokud chcete zjistit, jak se v kódu vytvářejí prostředky databáze, můžete si prohlédnout následující fragmenty kódu. Jinak můžete přeskočit přímo k části [Aktualizace informací o připojení](#update-your-connection-string). 

Všechny následující fragmenty kódu pocházejí ze souboru Dal.cs v adresáři DAL.

* Inicializace klienta.

    ```cs
        MongoClientSettings settings = new MongoClientSettings();
        settings.Server = new MongoServerAddress(host, 10255);
        settings.UseSsl = true;
        settings.SslSettings = new SslSettings();
        settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;

        MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
        MongoIdentityEvidence evidence = new PasswordEvidence(password);

        settings.Credential = new MongoCredential("SCRAM-SHA-1", identity, evidence);

        MongoClient client = new MongoClient(settings);
    ```

* Načte se databáze a kolekce.

    ```cs
    private string dbName = "Tasks";
    private string collectionName = "TasksList";

    var database = client.GetDatabase(dbName);
    var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
    ```

* Načtou se všechny dokumenty.

    ```cs
    collection.Find(new BsonDocument()).ToList();
    ```

Vytvoření úlohy a vložte jej do kolekce

   ```csharp
    public void CreateTask(MyTask task)
    {
        var collection = GetTasksCollectionForEdit();
        try
        {
            collection.InsertOne(task);
        }
        catch (MongoCommandException ex)
        {
            string msg = ex.Message;
        }
    }
   ```
   Podobně můžete dokumenty aktualizovat a odstraňovat pomocí metod [collection.UpdateOne()](https://docs.mongodb.com/stitch/mongodb/actions/collection.updateOne/index.html) a [collection.DeleteOne()](https://docs.mongodb.com/stitch/mongodb/actions/collection.deleteOne/index.html). 

## <a name="update-your-connection-string"></a>Aktualizace připojovacího řetězce

Teď se vraťte zpátky na portál Azure Portal, kde najdete informace o připojovacím řetězci, a zkopírujte je do aplikace.

1. V [webu Azure portal](https://portal.azure.com/), ve vašem účtu Cosmos v levém navigačním panelu klikněte na tlačítko **připojovací řetězec**a potom klikněte na tlačítko **klíče pro čtení i zápis**. V dalším kroku zkopírujete pomocí tlačítek kopírování na pravé straně obrazovky uživatelské jméno, heslo a hostitele do souboru Dal.cs.

2. Otevřete soubor **Dal.cs** v adresáři **DAL**. 

3. Z portálu zkopírujte hodnotu **username** (pomocí tlačítka kopírování) a nastavte ji jako hodnotu položky **username** v souboru **Dal.cs**. 

4. Potom z portálu zkopírujte hodnotu **host** a nastavte ji jako hodnotu **host** v souboru **Dal.cs**. 

5. Nakonec z portálu zkopírujte hodnotu **password** a nastavte ji jako hodnotu **password** v souboru **Dal.cs**. 

Aplikaci jste teď aktualizovaný o všechny potřebné informace týkající se potřebuje ke komunikaci s Cosmos DB. 
    
## <a name="run-the-web-app"></a>Spuštění webové aplikace

1. V sadě Visual Studio klikněte v **Průzkumníku řešení** pravým tlačítkem myši na projekt a potom klikněte na **Spravovat balíčky NuGet**. 

2. Do pole **Procházet** v NuGetu zadejte *MongoDB.Driver*.

3. Z výsledků nainstalujte knihovnu **MongoDB.Driver**. Tím se nainstaluje balíček MongoDB.Driver a všechny závislosti.

4. Spusťte aplikaci stisknutím CTRL+F5. Aplikace se zobrazí v prohlížeči. 

5. V prohlížeči klikněte na **Vytvořit** a vytvořte v aplikaci seznamu úkolů několik nových úloh.

## <a name="review-slas-in-the-azure-portal"></a>Ověření podmínek SLA na portálu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste zjistili, jak vytvořit účet Cosmos, vytvoření kolekce a spustíte aplikaci konzoly. Teď můžete importovat další data k databázi Cosmos. 

> [!div class="nextstepaction"]
> [Importování dat MongoDB do služby Azure Cosmos DB](mongodb-migrate.md)
