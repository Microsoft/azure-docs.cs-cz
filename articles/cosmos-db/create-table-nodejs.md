---
title: 'Rychlý Start: rozhraní API pro tabulky s Node.js-Azure Cosmos DB'
description: Tento rychlý start ukazuje, jak použít rozhraní Azure Cosmos DB Table API k vytvoření aplikace pomocí webu Azure Portal a Node.js.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/28/2020
ms.author: sngun
ms.custom: devx-track-js
ms.openlocfilehash: a9f5a307bfefedf74f884e39b482bd90454fc5c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93096865"
---
# <a name="quickstart-build-a-table-api-app-with-nodejs-and-azure-cosmos-db"></a>Rychlý start: Sestavení aplikace Table API pomocí Node.js a Azure Cosmos DB
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](./table-storage-how-to-use-python.md)
> 

V tomto rychlém startu vytvoříte účet Azure Cosmos DB rozhraní API pro tabulky a pomocí Průzkumník dat a Node.js aplikace naklonované z GitHubu vytvoříte tabulky a entity. Azure Cosmos DB je databázová služba pro více modelů, která umožňuje rychle vytvářet a dotazovat databáze dokumentů, tabulek, klíčových hodnot a grafů s funkcemi globální distribuce a horizontálního škálování.

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Nebo [vyzkoušejte Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure. [Emulátor Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) můžete použít také s identifikátorem URI `https://localhost:8081` a klíčem `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` .
- [Node.js 0.10.29 +](https://nodejs.org/) .
- [Git](https://git-scm.com/downloads).

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
    git clone https://github.com/Azure-Samples/storage-table-node-getting-started.git
    ```

> [!TIP]
> Podrobnější návod k podobnému kódu naleznete v článku ukázkový článek pro [Cosmos DB rozhraní API pro tabulky](table-storage-how-to-use-nodejs.md) . 

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. Pokud chcete zjistit, jak se v kódu vytvářejí prostředky databáze, můžete si prohlédnout následující fragmenty kódu. V opačném případě můžete přeskočit k části [aktualizace připojovacího řetězce](#update-your-connection-string) v tomto dokumentu.

* Následující kód ukazuje, jak vytvořit tabulku v rámci Azure Storage:

  ```javascript
  storageClient.createTableIfNotExists(tableName, function (error, createResult) {
    if (error) return callback(error);

    if (createResult.isSuccessful) {
      console.log("1. Create Table operation executed successfully for: ", tableName);
    }
  }

  ```

* Následující kód ukazuje, jak vložit data do tabulky:

  ```javascript
  var customer = createCustomerEntityDescriptor("Harp", "Walter", "Walter@contoso.com", "425-555-0101");

  storageClient.insertOrMergeEntity(tableName, customer, function (error, result, response) {
    if (error) return callback(error);

    console.log("   insertOrMergeEntity succeeded.");
  }
  ```

* Následující kód ukazuje, jak zadávat dotazy na data z tabulky:

  ```javascript
  console.log("6. Retrieving entities with surname of Smith and first names > 1 and <= 75");

  var storageTableQuery = storage.TableQuery;
  var segmentSize = 10;

  // Demonstrate a partition range query whereby we are searching within a partition for a set of entities that are within a specific range. 
  var tableQuery = new storageTableQuery()
      .top(segmentSize)
      .where('PartitionKey eq ?', lastName)
      .and('RowKey gt ?', "0001").and('RowKey le ?', "0075");
  
  runPageQuery(tableQuery, null, function (error, result) {
  
      if (error) return callback(error);
  
  ```

* Následující kód ukazuje, jak odstranit data z tabulky:

  ```javascript
  storageClient.deleteEntity(tableName, customer, function entitiesQueried(error, result) {
      if (error) return callback(error);
  
      console.log("   deleteEntity succeeded.");
  }
  ```
  
## <a name="update-your-connection-string"></a>Aktualizace připojovacího řetězce

Teď se vraťte zpátky na portál Azure Portal, kde najdete informace o připojovacím řetězci, a zkopírujte je do aplikace. Tím aplikaci umožníte komunikovat s hostovanou databází. 

1. V Azure Cosmos DB účtu v [Azure Portal](https://portal.azure.com/)vyberte **připojovací řetězec**. 

    :::image type="content" source="./media/create-table-nodejs/connection-string.png" alt-text="Zobrazení a zkopírování požadovaných informací o připojovacím řetězci v podokně Připojovací řetězec":::

2. Pomocí tlačítka pro kopírování na pravé straně zkopírujte primární připojovací řetězec.

3. Otevřete soubor *app.config* a vložte hodnotu do řetězce ConnectionString na řádku tři. 

    > [!IMPORTANT]
    > Pokud váš koncový bod používá adresu documents.azure.com, znamená to, že máte účet Preview, a pokud chcete pracovat s obecně dostupnou sadou Table API SDK, musíte si vytvořit [nový účet Table API](#create-a-database-account).
    >

3. Uložte soubor *app.config* .

Teď jste aktualizovali aplikaci a zadali do ní všechny informace potřebné ke komunikaci s Azure Cosmos DB. 

## <a name="run-the-app"></a>Spuštění aplikace

1. V okně terminálu Git přejděte příkazem `cd` do složky storage-table-java-getting-started.

    ```
    cd "C:\git-samples\storage-table-node-getting-started"
    ```

2. Spusťte následující příkaz, který nainstaluje moduly [Azure], [Node-UUID], [NConf] a [Async] místně a uloží položku pro ně do *package.jsv* souboru.

   ```
   npm install azure-storage node-uuid async nconf --save
   ```

2. Spuštěním následujících příkazů v okně terminálu Git spusťte aplikaci Node.js.

    ```
    node ./tableSample.js 
    ```

    V okně konzoly se zobrazí přidávání tabulkových dat do nové databáze tabulek ve službě Azure Cosmos DB.

    Nyní se můžete vrátit na Průzkumník dat a zobrazit, dotazovat, upravit a pracovat s těmito novými daty. 

## <a name="review-slas-in-the-azure-portal"></a>Ověření smluv SLA na webu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit účet Azure Cosmos DB, vytvořit tabulku pomocí Průzkumník dat a spustit Node.js aplikaci pro přidání dat tabulky.  Teď můžete zadávat dotazy na svá data pomocí rozhraní API tabulky.  

> [!div class="nextstepaction"]
> [Import tabulkových dat do rozhraní Table API](table-import.md)