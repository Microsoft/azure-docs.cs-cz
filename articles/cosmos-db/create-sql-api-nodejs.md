---
title: 'Quickstart: Use Node.js to query from Azure Cosmos DB SQL API account'
description: How to use Node.js to create an app that connects to Azure Cosmos DB SQL API account and queries data.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/19/2019
ms.author: dech
ms.openlocfilehash: 44cdd4307be56d864afb45d619958cc59a3fa978
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74220518"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>Quickstart: Use Node.js to connect and query data from Azure Cosmos DB SQL API account

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

This quickstart demonstrates how to use a Node.js app to connect to the [SQL API](sql-api-introduction.md) account in Azure Cosmos DB. You can then use Azure Cosmos DB SQL queries to query and manage data. The Node.js app you build in this article uses the [SQL JavaScript SDK](sql-api-sdk-node.md). V tomto rychlém startu se používá verze 2.0 sady [JavaScript SDK](https://www.npmjs.com/package/@azure/cosmos).

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Dále musí být splněny všechny tyto podmínky:
    * [Node.js](https://nodejs.org/en/) verze 6.0.0 nebo novější
    * [Git](https://git-scm.com/)

## <a name="create-a-database"></a>Vytvoření databáze 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Add a container

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Přidání ukázkových dat

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Dotazování dat

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Now let's clone a Node.js app from GitHub, set the connection string, and run it.

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
    ```

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. If you're interested in learning how the Azure Cosmos database resources are created in the code, you can review the following snippets. Jinak můžete přeskočit přímo k části [Aktualizace připojovacího řetězce](#update-your-connection-string). 

Poznámka: Pokud znáte předchozí verzi sady JavaScript SDK, možná jste zvyklí na používání termínů „kolekce“ a „dokument“. Vzhledem k tomu, že Azure Cosmos DB podporuje [více modelů rozhraní API](https://docs.microsoft.com/azure/cosmos-db/introduction), ve verzi 2.0 rozhraní JavaScript SDK a novější se používají termíny „kontejner“, což může být kolekce, graf nebo tabulka, a „položka“ popisující obsah kontejneru.

Všechny následující fragmenty kódu pocházejí ze souboru **app.js**.

* The `CosmosClient` object is initialized.

    ```javascript
    const client = new CosmosClient({ endpoint, key });
    ```

* Create a new Azure Cosmos database.

    ```javascript
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    ```

* A new container (collection) is created within the database.

    ```javascript
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    ```

* Vytvoří se položka (dokument).

    ```javascript
    const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
    ```

* A SQL query over JSON is performed on the family database. The query returns all the children of the "Anderson" family. 

    ```javascript
      const querySpec = {
        query: 'SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName',
        parameters: [
          {
            name: '@lastName',
            value: 'Andersen'
          }
        ]
      }

      const { resources: results } = await client
        .database(databaseId)
        .container(containerId)
        .items.query(querySpec)
        .fetchAll()
      for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult)
        console.log(`\tQuery returned ${resultString}\n`)
      }
    ```    

## <a name="update-your-connection-string"></a>Aktualizace připojovacího řetězce

Now go back to the Azure portal to get the connection string details of your Azure Cosmos account. Copy the connection string into the app so that it can connect to your database.

1. In the [Azure portal](https://portal.azure.com/), in your Azure Cosmos account, in the left navigation click **Keys**, and then click **Read-write Keys**. V dalším kroku zkopírujete pomocí tlačítek kopírování na pravé straně obrazovky identifikátor URI a primární klíč do souboru `config.js`.

    ![Zobrazení a zkopírování přístupového klíče na portálu Azure Portal v okně Klíče](./media/create-sql-api-dotnet/keys.png)

2. Otevřete soubor `config.js`. 

3. Z portálu zkopírujte hodnotu identifikátoru URI (pomocí tlačítka kopírování) a nastavte ji jako hodnotu klíče koncového bodu v souboru `config.js`. 

    `config.endpoint = "https://FILLME.documents.azure.com"`

4. Potom z portálu zkopírujte hodnotu PRIMÁRNÍHO KLÍČE a nastavte ji jako hodnotu `config.key` v souboru `config.js`. Teď jste aktualizovali aplikaci a zadali do ní všechny informace potřebné ke komunikaci s databází Azure Cosmos. 

    `config.key = "FILLME"`
    
## <a name="run-the-app"></a>Spusťte aplikaci

1. Spusťte v terminálu `npm install`, aby se nainstalovaly požadované moduly NPM.

2. Spuštění v terminálu `node app.js`, aby se spustila aplikace uzlu.

You can now go back to Data Explorer, modify, and work with this new data.

## <a name="review-slas-in-the-azure-portal"></a>Ověření podmínek SLA na portálu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

In this quickstart, you've learned how to create an Azure Cosmos account, create a container using the data explorer, and run an app. You can now import additional data to your Azure Cosmos database. 

> [!div class="nextstepaction"]
> [Importování dat do služby Azure Cosmos DB](import-data.md)


