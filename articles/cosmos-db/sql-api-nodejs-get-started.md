---
title: Kurz Node.js k rozhraní SQL API pro službu Azure Cosmos DB | Microsoft Docs
description: Kurz Node.js ukazující připojení ke službě Azure Cosmos DB a její dotazování pomocí rozhraní SQL API
services: cosmos-db
author: deborahc
editor: monicar
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: dech
ms.openlocfilehash: 219ec7138cb839ec7d0eebb613931a85c752f3c2
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741211"
---
# <a name="tutorial-build-a-nodejs-console-app-with-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Kurz: Vytvoření konzolové aplikace Node.js s využitím sady JavaScript SDK pro správu dat rozhraní SQL API služby Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

V tomto kurzu se dozvíte, jak vytvořit konzolovou aplikaci Node.js pro vytváření prostředků Azure Cosmos DB a jejich dotazování.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Vytvoření účtu služby Azure Cosmos DB a připojení k němu
> * Nastavení aplikace
> * Vytvoření databáze
> * Vytvoření kontejneru
> * Přidání položek do kontejneru
> * Provádění operací CRUD s položkami, kontejnerem a databází

Nemáte čas aplikaci vytvářet? Nevadí! Úplné řešení je k dispozici na [GitHubu](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ). Rychlé pokyny najdete v části [Získání úplného řešení](#GetSolution) tohoto článku.

## <a name="prerequisites"></a>Požadavky 

Ujistěte se, že máte následující prostředky:

* Aktivní účet Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/pricing/free-trial/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js](https://nodejs.org/) verze 6.0.0 nebo novější.

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Krok 1: Vytvoření účtu služby Azure Cosmos DB

Vytvořme účet služby Azure Cosmos DB. Pokud již máte účet, který chcete použít, můžete přeskočit k části [Nastavení aplikace Node.js](#SetupNode). Pokud používáte emulátor služby Azure Cosmos DB, nastavte emulátor pomocí postupu v tématu [Emulátor služby Azure Cosmos DB](local-emulator.md) a přeskočte k části [Nastavení aplikace Node.js](#SetupNode). 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupNode"></a>Krok 2: Nastavení aplikace Node.js

1. Otevřete svůj oblíbený terminál.
2. Vyhledejte složku nebo adresář, do kterého chcete uložit aplikaci Node.js.
3. Vytvořte dva prázdné javascriptové soubory, které budou obsahovat následující příkazy:

   * Windows:
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```

   * Linux nebo OS X:
     * ```touch app.js```
     * ```touch config.js```

4. Nainstalujte přes npm modul @azure/cosmos. Použijte následující příkaz:
   * ```npm install @azure/cosmos --save```

## <a id="Config"></a>Krok 3: Nastavení konfigurací aplikace

1. Ve svém oblíbeném textovém editoru otevřete ```config.js```.

1. Zkopírujte a vložte fragment kódu níže a nastavte vlastnosti ```config.endpoint``` a ```config.primaryKey``` na hodnoty identifikátoru URI a primárního klíče vašeho koncového bodu Azure Cosmos DB. Obě tyto konfigurace je možné najít na webu [Azure Portal](https://portal.azure.com).

   ![Snímek obrazovky se získáním klíčů z webu Azure Portal][keys]

   ```nodejs
   // ADD THIS PART TO YOUR CODE
   var config = {}

   config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
   config.primaryKey = "~your primary key here~";
   ``` 

1. Zkopírujte údaje ```database```, ```container``` a ```items``` a vložte je do objektu ```config``` pod nastavení vlastností ```config.endpoint``` a ```config.primaryKey```. Pokud již máte data, která chcete uložit do databáze, můžete tady místo definování dat použít [nástroj pro migraci dat](import-data.md) služby Azure Cosmos DB.

   ```nodejs
   var config = {}

   config.endpoint = "~your Azure Cosmos DB account endpoint uri here~";
   config.primaryKey = "~your primary key here~";

   config.database = {
      "id": "FamilyDatabase"
   };

   config.container = {
     "id": "FamilyContainer"
   };

   config.items = {
      "Andersen": {
          "id": "Anderson.1",
          "lastName": "Andersen",
          "parents": [{
            "firstName": "Thomas"
        }, {
                "firstName": "Mary Kay"
            }],
        "children": [{
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [{
                "givenName": "Fluffy"
            }]
        }],
        "address": {
            "state": "WA",
            "county": "King",
            "city": "Seattle"
        }
    },
    "Wakefield": {
        "id": "Wakefield.7",
        "parents": [{
            "familyName": "Wakefield",
            "firstName": "Robin"
        }, {
                "familyName": "Miller",
                "firstName": "Ben"
            }],
        "children": [{
            "familyName": "Merriam",
            "firstName": "Jesse",
            "gender": "female",
            "grade": 8,
            "pets": [{
                "givenName": "Goofy"
            }, {
                    "givenName": "Shadow"
                }]
        }, {
                "familyName": "Miller",
                "firstName": "Lisa",
                "gender": "female",
                "grade": 1
            }],
        "address": {
            "state": "NY",
            "county": "Manhattan",
            "city": "NY"
        },
        "isRegistered": false
      }
   };
   ```

   Poznámka: Pokud znáte předchozí verzi sady JavaScript SDK, možná jste zvyklí na používání termínů „kolekce“ a „dokument“. Vzhledem k tomu, že Azure Cosmos DB podporuje [více modelů rozhraní API](https://docs.microsoft.com/azure/cosmos-db/introduction#key-capabilities), ve verzi 2.0 sady JavaScript SDK a novější se používají obecné termíny „kontejner“ a „položka“. Kontejner může být kolekce, graf nebo tabulka. Položka představuje obsah uvnitř kontejneru a může to být dokument, pár hrany a vrcholu nebo řádek. 

1. Nakonec exportujte objekt ```config```, abyste na něj mohli odkazovat ze souboru ```app.js```.

   ```nodejs
        },
        "isRegistered": false
       }
   };

   // ADD THIS PART TO YOUR CODE
   module.exports = config;
   ```

## <a id="Connect"></a>Krok 4: Připojení k účtu služby Azure Cosmos DB

1. V textovém editoru otevřete prázdný soubor ```app.js```. Zkopírováním a vložením kódu níže importujte modul ```@azure/cosmos``` a nově vytvořený modul ```config```.

   ```nodejs
   // ADD THIS PART TO YOUR CODE
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');
   const url = require('url');
   ```

1. Zkopírujte a vložte kód, který použije dříve uložené ```config.endpoint``` a ```config.primaryKey``` k vytvoření nové instance CosmosClient.

   ```nodejs
   const url = require('url');

   // ADD THIS PART TO YOUR CODE
   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });
   ```

Teď, když máte kód pro inicializaci klienta Azure Cosmos DB, se budeme věnovat práci s prostředky Azure Cosmos DB.

## <a name="step-5-create-a-database"></a>Krok 5: Vytvoření databáze

1. Zkopírujte a vložte kód níže, který nastaví ID databáze a ID kontejneru. Pomocí těchto ID klient Azure Cosmos DB najde správnou databázi a kontejner.

   ```nodejs
   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   // ADD THIS PART TO YOUR CODE
   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   ```

   [Databázi](sql-api-resources.md#databases) je možné vytvořit pomocí funkce [createIfNotExists](/javascript/api/%40azure/cosmos/databases) nebo [create](/javascript/api/%40azure/cosmos/databases) z třídy **Databases**. Databáze je logický kontejner položek rozdělených napříč kontejnery. 

2. Zkopírujte metody **createDatabase** a **readDatabase** a vložte je do souboru app.js pod definici ```databaseId``` a ```containerId```. Funkce **createDatabase** vytvoří novou databázi s ID ```FamilyDatabase``` definovaným v objektu ```config```, pokud ještě neexistuje. Funkce **readDatabase** přečte definici databáze a ověří, jestli databáze existuje.

   ```nodejs
   /**
    * Create the database if it does not exist
    */
   async function createDatabase() {
       const { database } = await client.databases.createIfNotExists({ id: databaseId });
       console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
      const { body: databaseDefinition } = await client.database(databaseId).read();
      console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }
   ```

3. Zkopírujte a pod nastavení funkcí **createDatabase** a **readDatabase** vložte následující kód, který přidá pomocnou funkci **exit**, která vypíše závěrečnou zprávu. 

   ```nodejs
   // ADD THIS PART TO YOUR CODE
   function exit(message) {
      console.log(message);
      console.log('Press any key to exit');
      process.stdin.setRawMode(true);
      process.stdin.resume();
      process.stdin.on('data', process.exit.bind(process, 0));
   };
   ```

4. Zkopírujte a pod nastavení funkce **exit** vložte následující kód, který volá funkce **createDatabase** a **readDatabase**.

   ```nodejs
   createDatabase()
     .then(() => readDatabase())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error \${JSON.stringify(error)}`) });
   ```

   V tuto chvíli by váš kód v souboru ```app.js``` měl vypadat jako následující kód:

   ```nodejs
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');
   const url = require('url');

   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;


   /**
    * Create the database if it does not exist
    */
   async function createDatabase() {
     const { database } = await client.databases.createIfNotExists({ id: databaseId });
     console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
     const { body: databaseDefinition } = await client.database(databaseId).read();
    console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }

   /**
   * Exit the app with a prompt
   * @param {message} message - The message to display
   */
   function exit(message) {
     console.log(message);
     console.log('Press any key to exit');
     process.stdin.setRawMode(true);
     process.stdin.resume();
     process.stdin.on('data', process.exit.bind(process, 0));
   }

   createDatabase()
     .then(() => readDatabase())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error) }`) });
   ```

5. V terminálu vyhledejte soubor ```app.js``` a spusťte příkaz: 

   ```bash 
   node app.js
   ```

Blahopřejeme! Úspěšně jste vytvořili databázi Azure Cosmos DB.

## <a id="CreateContainer"></a>Krok 6: Vytvoření kontejneru

> [!WARNING]
> Zavoláním funkce **createContainer** se vytvoří nový kontejner, za který se hradí poplatky. Další podrobnosti najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/cosmos-db/).

Kontejner je možné vytvořit pomocí funkce [createIfNotExists](/javascript/api/%40azure/cosmos/containers) nebo [create](/javascript/api/%40azure/cosmos/containers) z třídy **Containers**.  Kontejner se skládá z položek (v případě rozhraní SQL API to jsou dokumenty JSON) a přidružené logiky javascriptové aplikace.

1. Zkopírujte funkce **createContainer** a **readContainer** a vložte je pod funkci **readDatabase** v souboru app.js. Funkce **createContainer** vytvoří nový kontejner s ID ```containerId``` definovaným v objektu ```config```, pokud ještě neexistuje. Funkce **readContainer** přečte definici kontejneru a ověří, jestli kontejner existuje.

   ```nodejs
   /**
   * Create the container if it does not exist
   */
   async function createContainer() {
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    console.log(`Created container:\n${config.container.id}\n`);
   }

   /**
   * Read the container definition
   */
   async function readContainer() {
     const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
     console.log(`Reading container:\n${containerDefinition.id}\n`);
   }
   ```

1. Zkopírujte a pod volání funkce **readDatabase** vložte následující kód, který spustí funkce **createContainer** a **readContainer**.

   ```nodejs
   createDatabase()
     .then(() => readDatabase())

     // ADD THIS PART TO YOUR CODE
     .then(() => createContainer())
     .then(() => readContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

   V tuto chvíli by váš kód v souboru ```app.js``` měl vypadat přibližně takto:

   ```nodejs
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');
   const url = require('url');

   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;

   /**
   * Create the database if it does not exist
   */
   async function createDatabase() {
     const { database } = await client.databases.createIfNotExists({ id: databaseId });
     console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
     const { body: databaseDefinition } = await client.database(databaseId).read();
     console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }

   /**
   * Create the container if it does not exist
   */
   async function createContainer() {
     const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
     console.log(`Created container:\n${config.container.id}\n`);
   }

   /**
   * Read the container definition
   */
   async function readContainer() {
     const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
     console.log(`Reading container:\n${containerDefinition.id}\n`);
   }

   /**
   * Exit the app with a prompt
   * @param {message} message - The message to display
   */
   function exit(message) {
     console.log(message);
     console.log('Press any key to exit');
     process.stdin.setRawMode(true);
     process.stdin.resume();
     process.stdin.on('data', process.exit.bind(process, 0));
   }

   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. V terminálu vyhledejte soubor ```app.js``` a spusťte příkaz: 

   ```bash 
   node app.js
   ```

Blahopřejeme! Úspěšně jste vytvořili kontejner Azure Cosmos DB.

## <a id="CreateItem"></a>Krok 7: Vytvoření položky

Položku je možné vytvořit pomocí funkce [create](/javascript/api/%40azure/cosmos/items) z třídy **Items**. Při používání rozhraní SQL API jsou položky dokumenty, které představují uživatelem definovaný (libovolný) obsah JSON. Teď můžete do služby Azure Cosmos DB vložit položku.

1. Zkopírujte funkci **createFamilyItem** a vložte ji pod funkci **readContainer**. Funkce **createFamilyItem** vytvoří položky obsahující data JSON uložená v objektu ```config```. Před vytvořením položky zkontrolujeme, že položka se stejným ID ještě neexistuje.

   ```nodejs
   /**
   * Create family item if it does not exist
   */
  async function createFamilyItem(itemBody) {
     try {
         // read the item to see if it exists
         const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).read();
         console.log(`Item with family id ${itemBody.id} already exists\n`);
     }
     catch (error) {
        // create the family item if it does not exist
        if (error.code === HttpStatusCodes.NOTFOUND) {
            const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
            console.log(`Created family item with id:\n${itemBody.id}\n`);
        } else {
            throw error;
        }
     }
   };
   ```

1. Zkopírujte a pod volání funkce **readContainer** vložte následující kód, který spustí funkci **createFamilyItem**.

   ```nodejs
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())

     // ADD THIS PART TO YOUR CODE
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. V terminálu vyhledejte soubor ```app.js``` a spusťte příkaz: 

   ```bash 
   node app.js
   ```

Blahopřejeme! Úspěšně jste vytvořili položku Azure Cosmos DB.


## <a id="Query"></a>Krok 8: Dotazování prostředků Azure Cosmos DB
Azure Cosmos DB podporuje [bohaté dotazy](sql-api-sql-query.md) na dokumenty JSON uložené v jednotlivých kontejnerech. Následující vzorový kód ukazuje dotaz, který můžete spouštět proti dokumentům v kontejneru.

1. Zkopírujte funkci **queryContainer** a vložte ji pod funkci **createFamilyItem** v souboru app.js. Jak je vidět níže, Azure Cosmos DB podporuje dotazy podobné jazyku SQL. Další informace o vytváření komplexních dotazů najdete v [Query Playground](https://www.documentdb.com/sql/demo) a [dokumentaci k dotazům](sql-api-sql-query.md).

   ```nodejs
   /**
   * Query the container using SQL
    */
   async function queryContainer() {
     console.log(`Querying container:\n${config.container.id}`);

     // query to return all children in a family
     const querySpec = {
        query: "SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName",
        parameters: [
            {
                name: "@lastName",
                value: "Andersen"
            }
        ]
    };

    const { result: results } = await client.database(databaseId).container(containerId).items.query(querySpec).toArray();
    for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult);
        console.log(`\tQuery returned ${resultString}\n`);
    }
   };
   ```

1. Zkopírujte a pod volání funkce **createFamilyItem** vložte následující kód, který spustí funkci **queryContainer**.

   ```nodejs
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))

     // ADD THIS PART TO YOUR CODE
     .then(() => queryContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. V terminálu vyhledejte soubor ```app.js``` a spusťte příkaz:

   ```bash 
   node app.js
   ```

Blahopřejeme! Úspěšně jste provedli dotaz na položky Azure Cosmos DB.

## <a id="ReplaceItem"></a>Krok 9: Nahrazení položky
Azure Cosmos DB podporuje nahrazování obsahu položek.

1. Zkopírujte funkci **replaceFamilyItem** a vložte ji pod funkci **queryContainer** v souboru app.js. Všimněte si, že jsme změnili vlastnost grade podřízeného prvku z předchozí hodnoty 5 na hodnotu 6.

   ```nodejs
   // ADD THIS PART TO YOUR CODE
   /**
   * Replace the item by ID.
   */
   async function replaceFamilyItem(itemBody) {
      console.log(`Replacing item:\n${itemBody.id}\n`);
      // Change property 'grade'
      itemBody.children[0].grade = 6;
      const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).replace(itemBody);
   };
   ```

1. Zkopírujte a pod volání funkce **queryContainer** vložte následující kód, který spustí funkci **replaceFamilyItem**. Také znovu přidejte kód volání funkce **queryContainer**, aby bylo možné ověřit, že se položka úspěšně změnila.

   ```nodejs
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))
     .then(() => queryContainer())

     // ADD THIS PART TO YOUR CODE
     .then(() => replaceFamilyItem(config.items.Andersen))
     .then(() => queryContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. V terminálu vyhledejte soubor ```app.js``` a spusťte příkaz:

   ```bash 
   node app.js
   ```

Blahopřejeme! Úspěšně jste nahradili položku Azure Cosmos DB.

## <a id="DeleteItem"></a>Krok 10: Odstranění položky

Azure Cosmos DB podporuje odstraňování položek JSON.

1. Zkopírujte funkci **deleteFamilyItem** a vložte ji pod funkci **replaceFamilyItem**.

   ```nodejs
  /**
  * Delete the item by ID.
  */
   async function deleteFamilyItem(itemBody) {
      await client.database(databaseId).container(containerId).item(itemBody.id).delete(itemBody);
      console.log(`Deleted item:\n${itemBody.id}\n`);
   };
   ```

1. Zkopírujte a pod druhé volání funkce **queryContainer** vložte následující kód, který spustí funkci **deleteFamilyItem**.

   ```nodejs
   createDatabase()
      .then(() => readDatabase())
      .then(() => createContainer())
      .then(() => readContainer())
      .then(() => createFamilyItem(config.items.Andersen))
      .then(() => createFamilyItem(config.items.Wakefield))
      .then(() => queryContainer
      ())
      .then(() => replaceFamilyItem(config.items.Andersen))
      .then(() => queryContainer())

    // ADD THIS PART TO YOUR CODE
      .then(() => deleteFamilyItem(config.items.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. V terminálu vyhledejte soubor ```app.js``` a spusťte příkaz: 

   ```bash 
   node app.js
   ```

Blahopřejeme! Úspěšně jste odstranili položku Azure Cosmos DB.

## <a id="DeleteDatabase"></a>Krok 11: Odstranění databáze

Odstraněním vytvořené databáze dojde k odstranění databáze a všech jejích podřízených prostředků (kontejnerů, položek atd.).

1. Zkopírujte a pod funkci **deleteFamilyItem** vložte funkci **cleanup**, která odebere databázi a všechny podřízené prostředky.

   ```nodejs
   /**
   * Cleanup the database and container on completion
   */
   async function cleanup() {
     await client.database(databaseId).delete();
   }
   ```

1. Zkopírujte a pod volání funkce **deleteFamilyItem** vložte následující kód, který spustí funkci **cleanup**.

   ```nodejs
   createDatabase()
      .then(() => readDatabase())
      .then(() => createContainer())
      .then(() => readContainer())
      .then(() => createFamilyItem(config.items.Andersen))
      .then(() => createFamilyItem(config.items.Wakefield))
      .then(() => queryContainer())
      .then(() => replaceFamilyItem(config.items.Andersen))
      .then(() => queryContainer())
      .then(() => deleteFamilyItem(config.items.Andersen))

      // ADD THIS PART TO YOUR CODE
      .then(() => cleanup())
      // ENDS HERE

      .then(() => { exit(`Completed successfully`); })
      .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

## <a id="Run"></a>Krok 12: Spuštění celé aplikace Node.js

Jako celek by váš kód měl vypadat přibližně takto:

```nodejs
const CosmosClient = require('@azure/cosmos').CosmosClient;

const config = require('./config');
const url = require('url');

const endpoint = config.endpoint;
const masterKey = config.primaryKey;

const HttpStatusCodes = { NOTFOUND: 404 };

const databaseId = config.database.id;
const containerId = config.container.id;

const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

/**
 * Create the database if it does not exist
 */
async function createDatabase() {
    const { database } = await client.databases.createIfNotExists({ id: databaseId });
    console.log(`Created database:\n${database.id}\n`);
}

/**
 * Read the database definition
 */
async function readDatabase() {
    const { body: databaseDefinition } = await client.database(databaseId).read();
    console.log(`Reading database:\n${databaseDefinition.id}\n`);
}

/**
 * Create the container if it does not exist
 */
async function createContainer() {
    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId });
    console.log(`Created container:\n${config.container.id}\n`);
}

/**
 * Read the container definition
 */
async function readContainer() {
    const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
    console.log(`Reading container:\n${containerDefinition.id}\n`);
}

/**
 * Create family item if it does not exist
 */
async function createFamilyItem(itemBody) {
    try {
        // read the item to see if it exists
        const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).read();
        console.log(`Item with family id ${itemBody.id} already exists\n`);
    }
    catch (error) {
        // create the family item if it does not exist
        if (error.code === HttpStatusCodes.NOTFOUND) {
            const { item } = await client.database(databaseId).container(containerId).items.create(itemBody);
            console.log(`Created family item with id:\n${itemBody.id}\n`);
        } else {
            throw error;
        }
    }
};

/**
 * Query the container using SQL
 */
async function queryContainer() {
    console.log(`Querying container:\n${config.container.id}`);

    // query to return all children in a family
    const querySpec = {
        query: "SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName",
        parameters: [
            {
                name: "@lastName",
                value: "Andersen"
            }
        ]
    };

    const { result: results } = await client.database(databaseId).container(containerId).items.query(querySpec).toArray();
    for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult);
        console.log(`\tQuery returned ${resultString}\n`);
    }
};

/**
 * Replace the item by ID.
 */
async function replaceFamilyItem(itemBody) {
    console.log(`Replacing item:\n${itemBody.id}\n`);
    // Change property 'grade'
    itemBody.children[0].grade = 6;
    const { item } = await client.database(databaseId).container(containerId).item(itemBody.id).replace(itemBody);
};

/**
 * Delete the item by ID.
 */
async function deleteFamilyItem(itemBody) {
    await client.database(databaseId).container(containerId).item(itemBody.id).delete(itemBody);
    console.log(`Deleted item:\n${itemBody.id}\n`);
};

/**
 * Cleanup the database and container on completion
 */
async function cleanup() {
    await client.database(databaseId).delete();
}

/**
 * Exit the app with a prompt
 * @param {message} message - The message to display
 */
function exit(message) {
    console.log(message);
    console.log('Press any key to exit');
    process.stdin.setRawMode(true);
    process.stdin.resume();
    process.stdin.on('data', process.exit.bind(process, 0));
}

createDatabase()
    .then(() => readDatabase())
    .then(() => createContainer())
    .then(() => readContainer())
    .then(() => createFamilyItem(config.items.Andersen))
    .then(() => createFamilyItem(config.items.Wakefield))
    .then(() => queryContainer())
    .then(() => replaceFamilyItem(config.items.Andersen))
    .then(() => queryContainer())
    .then(() => deleteFamilyItem(config.items.Andersen))
    .then(() => cleanup())
    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
```

V terminálu vyhledejte soubor ```app.js``` a spusťte příkaz: 

```bash 
node app.js
```

Měl by se zobrazit výstup počáteční aplikace. Výstup by měl odpovídat ukázkovému textu níže.

   ```
    Created database:
    FamilyDatabase

    Reading database:
    FamilyDatabase

    Created container:
    FamilyContainer

    Reading container:
    FamilyContainer

    Created family item with id:
    Anderson.1

    Created family item with id:
    Wakefield.7

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing item:
    Anderson.1

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleted item:
    Anderson.1

    Completed successfully
    Press any key to exit
   ```

Blahopřejeme! Dokončili jste kurz Node.js a máte svou první konzolovou aplikaci využívající službu Azure Cosmos DB!

## <a id="GetSolution"></a>Získání úplného řešení kurzu k Node.js

Pokud jste neměli dostatek času k dokončení kroků v tomto kurzu nebo si jenom chcete stáhnout kód, můžete ho získat z [GitHubu](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ).

Pokud chcete spustit řešení Getting Started, které obsahuje veškerý kód v tomto článku, budete muset provést následující kroky:

* [Účet služby Azure Cosmos DB][create-account].
* Řešení [Getting Started](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started), které je k dispozici na GitHubu.

Nainstalujte přes npm modul **@azure/cosmos**. Použijte následující příkaz:

* ```npm install @azure/cosmos --save```

Dále v souboru ```config.js``` aktualizujte hodnoty config.endpoint a config.primaryKey, jak je popsáno v části [Krok 3: Nastavení konfigurací aplikace](#Config). 

Potom v terminálu vyhledejte soubor ```app.js``` a spusťte příkaz: 

```bash 
node app.js
```

A to je vše, máte hotovo. 

## <a name="next-steps"></a>Další kroky

* Hledáte složitější ukázku Node.js? Viz [Sestavení webové aplikace Node.js využívající službu Azure Cosmos DB](sql-api-nodejs-application.md).
* Zjistěte, jak [monitorovat účet služby Azure Cosmos DB](monitor-accounts.md).
* Spouštějte dotazy proti ukázkovým datovým sadám v [Query Playground](https://www.documentdb.com/sql/demo).

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png