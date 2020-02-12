---
title: 'Rychlý Start: použití Node. js k dotazování z Azure Cosmos DB účtu rozhraní SQL API'
description: Jak pomocí Node. js vytvořit aplikaci, která se připojuje k Azure Cosmos DB účtu rozhraní SQL API a dotazování na data.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 11/19/2019
ms.author: dech
ms.openlocfilehash: d8b17472bb531ec799be227706261962d7914d68
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134456"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>Rychlý Start: použití Node. js k připojení a dotazování dat z Azure Cosmos DB účtu rozhraní SQL API

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [ROZHRANÍ .NET V4](create-sql-api-dotnet-V4.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

V tomto rychlém startu vytvoříte a spravujete Azure Cosmos DB účet rozhraní SQL API z Azure Portal a pomocí aplikace Node. js naklonované z GitHubu. Azure Cosmos DB je databázová služba pro více modelů, která umožňuje rychle vytvářet a dotazovat databáze dokumentů, tabulek, klíčových hodnot a grafů s funkcemi globální distribuce a horizontálního škálování.

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Nebo [vyzkoušejte Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure. [Emulátor Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) můžete použít také s identifikátorem URI `https://localhost:8081` a klíčovým `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`.
- [Node. js 6.0.0 +](https://nodejs.org/).
- [Git](https://www.git-scm.com/downloads)

## <a name="create-a-database"></a>Vytvoření databáze 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Přidat kontejner

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="add-sample-data"></a>Přidání ukázkových dat

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Dotazování dat

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Teď naklonujte aplikaci Node. js z GitHubu, nastavíme připojovací řetězec a spustíme ji.

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
    ```

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. Pokud vás zajímá, jak se v kódu vytvářejí prostředky databáze Azure Cosmos, můžete zkontrolovat následující fragmenty kódu. Jinak můžete přeskočit přímo k části [Aktualizace připojovacího řetězce](#update-your-connection-string). 

Pokud jste obeznámeni s předchozí verzí sady SQL JavaScript SDK, můžete použít k zobrazení *kolekce* a *dokumentu*. Vzhledem k tomu, že Azure Cosmos DB podporuje [více modelů rozhraní API](introduction.md), [verze 2.0 + sady JavaScript SDK](https://www.npmjs.com/package/@azure/cosmos) používá obecný *kontejner*, což může být kolekce, graf nebo tabulka a *položka* pro popis obsahu kontejneru.

Všechny následující fragmenty kódu pocházejí ze souboru *app.js*.

* Objekt `CosmosClient` je inicializován.

    ```javascript
    const client = new CosmosClient({ endpoint, key });
    ```

* Vytvořte novou databázi Azure Cosmos.

    ```javascript
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    ```

* V rámci databáze se vytvoří nový kontejner (kolekce).

    ```javascript
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    ```

* Vytvoří se položka (dokument).

    ```javascript
    const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
    ```

* Dotaz SQL přes JSON se provádí v databázi rodiny. Dotaz vrátí všechny podřízené položky řady "Anderson". 

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

Teď se vraťte na Azure Portal a Získejte podrobnosti o připojovacím řetězci účtu Azure Cosmos. Zkopírujte připojovací řetězec do aplikace, aby se mohl připojit k vaší databázi.

1. V Azure Cosmos DB účtu v [Azure Portal](https://portal.azure.com/)v levém navigačním panelu vyberte **klíče** a pak vyberte **klíče pro čtení i zápis**. Pomocí tlačítek kopírování na pravé straně obrazovky zkopírujte identifikátor URI a primární klíč do souboru *config. js* v dalším kroku.

    ![Zobrazení a zkopírování přístupového klíče na webu Azure Portal v okně Klíče](./media/create-sql-api-dotnet/keys.png)

2. Otevřete soubor *config. js* . 

3. Z portálu Zkopírujte hodnotu identifikátoru URI (pomocí tlačítka kopírování) a nastavte ji jako hodnotu klíče koncového bodu v *souboru config. js*. 

    `config.endpoint = "<Your Azure Cosmos account URI>"`

4. Pak z portálu Zkopírujte hodnotu primárního klíče a nastavte ji jako hodnotu `config.key` v *souboru config. js*. Teď jste aktualizovali aplikaci a zadali do ní všechny informace potřebné ke komunikaci s databází Azure Cosmos. 

    `config.key = "<Your Azure Cosmos account key>"`
    
## <a name="run-the-app"></a>Spuštění aplikace

1. Spusťte v terminálu `npm install`, aby se nainstalovaly požadované moduly NPM.

2. Spuštění v terminálu `node app.js`, aby se spustila aplikace uzlu.

Nyní se můžete vrátit do Průzkumník dat, upravit a pracovat s těmito novými daty.

## <a name="review-slas-in-the-azure-portal"></a>Ověření podmínek SLA na portálu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak vytvořit účet Azure Cosmos DB, vytvořit kontejner pomocí Průzkumník dat a spustit aplikaci Node. js. Teď můžete do svého účtu služby Azure Cosmos DB importovat další data. 

> [!div class="nextstepaction"]
> [Importování dat do služby Azure Cosmos DB](import-data.md)


