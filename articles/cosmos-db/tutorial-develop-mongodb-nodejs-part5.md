---
title: Vytvořit aplikaci Angular s rozhraním API služby Azure Cosmos DB pro MongoDB - připojit ke službě Cosmos DB pomocí Mongoose
titleSuffix: Azure Cosmos DB
description: Tento kurz popisuje, jak vytvořit aplikaci Node.js pomocí Angular a službou Express pro správu dat uložených ve službě Cosmos DB. V této části se pro připojení ke službě Azure Cosmos DB pomocí Mongoose.
author: johnpapa
ms.service: cosmos-db
ms.component: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18
Customer intent: As a developer, I want to build a Node.js application, so that I can manage the data stored in Cosmos DB.
ms.openlocfilehash: 03b1f1d5fa1320f08059ed755f6f4f7d1c08d35e
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/27/2018
ms.locfileid: "53793697"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---use-mongoose-to-connect-to-cosmos-db"></a>Vytvořit aplikaci Angular s rozhraním API služby Azure Cosmos DB pro MongoDB - připojit ke službě Cosmos DB pomocí Mongoose

Tento vícedílný kurz ukazuje, jak vytvořit aplikaci Node.js s Express a Angular a připojte ho k němu vaše [Cosmos účtu nakonfigurovanému pro Cosmos DB přes rozhraní API pro MongoDB](mongodb-introduction.md). Tento článek popisuje část 5 tohoto kurzu a staví na [část 4](tutorial-develop-mongodb-nodejs-part4.md).

V této části kurzu provedete následující:

> [!div class="checklist"]
> * Připojení ke službě Cosmos DB pomocí Mongoose.
> * Získejte připojovací řetězec Cosmos DB.
> * Vytvoření modelu Hero.
> * Vytvoření služby Hero pro získání dat hrdinů.
> * Spusťte aplikaci místně.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Než začnete tento kurz, proveďte kroky v [část 4](tutorial-develop-mongodb-nodejs-part4.md).

* Tento kurz vyžaduje, abyste spustili Azure CLI místně. Musíte mít nainstalovanou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku Azure, najdete v článku [nainstalovat rozhraní příkazového řádku Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Tento kurz vás provede kroky k vytvoření aplikace krok za krokem. Pokud si chcete stáhnout dokončený projekt, v [úložišti angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) na GitHubu můžete získat hotovou aplikaci.

## <a name="use-mongoose-to-connect"></a>Připojení pomocí Mongoose

Mongoose je objektu data modelování knihovny (ODM) pro MongoDB a Node.js. Připojení k vašemu účtu Azure Cosmos DB můžete pomocí Mongoose. Pomocí následujících kroků nainstalujte Mongoose a připojte se ke službě Azure Cosmos DB:

1. Nainstalujte modul npm mongoose, což je rozhraní API, který se používá ke komunikaci s MongoDB.

    ```bash
    npm i mongoose --save
    ```

1. V **server** složce vytvořte soubor s názvem **mongo.js**. Podrobnosti připojení účtu služby Azure Cosmos DB přidáte do tohoto souboru.

1. Zkopírujte následující kód do **mongo.js** souboru. Kód poskytuje následující funkce:

    * Vyžaduje Mongoose.
    * Přepíše příslib Mongo tak používal základní příslib, která je integrována do ES6/ES2015 a novějších verzích.
    * Volání soubor env, který umožňuje nastavit určité na základě toho, jestli jste v pracovní produkčního prostředí a vývoj. Tento soubor vytvoříte v další části.
    * Obsahuje připojovací řetězec MongoDB, jež je nastavena v souboru env.
    * Vytvoří funkci connect, která volá Mongoose.

    ```javascript
    const mongoose = require('mongoose');
    /**
     * Set to Node.js native promises
     * Per http://mongoosejs.com/docs/promises.html
     */
    mongoose.Promise = global.Promise;

    const env = require('./env/environment');

    // eslint-disable-next-line max-len
    const mongoUri = `mongodb://${env.accountName}:${env.key}@${env.accountName}.documents.azure.com:${env.port}/${env.databaseName}?ssl=true`;

    function connect() {
     mongoose.set('debug', true);
     return mongoose.connect(mongoUri, { useMongoClient: true });
    }

    module.exports = {
      connect,
      mongoose
    };
    ```
    
1. V podokně Průzkumník pod **server**, vytvořte složku s názvem **prostředí**. V **prostředí** složce vytvořte soubor s názvem **environment.js**.

1. Ze souboru mongo.js musíme zahrnout hodnoty `dbName`, `key`a `cosmosPort` parametry. Zkopírujte následující kód do **environment.js** souboru:

    ```javascript
    // TODO: replace if yours are different
    module.exports = {
      accountName: 'your-cosmosdb-account-name-goes-here',
      databaseName: 'admin', 
      key: 'your-key-goes-here',
      port: 10255
    };
    ```

## <a name="get-the-connection-string"></a>Získání připojovacího řetězce

Pro připojení aplikace ke službě Azure Cosmos DB, musíte aktualizovat nastavení konfigurace pro aplikaci. Pomocí následujících kroků aktualizujte nastavení: 

1. Na webu Azure Portal získejte číslo portu, název účtu služby Azure Cosmos DB a hodnoty primárního klíče pro váš účet Azure Cosmos DB.

1. V **environment.js** souboru, změňte hodnotu vlastnosti `port` na 10255. 

    ```javascript
    const port = 10255;
    ```

1. V **environment.js** souboru, změňte hodnotu vlastnosti `accountName` k názvu účtu Azure Cosmos DB, který jste vytvořili v [část 4](tutorial-develop-mongodb-nodejs-part4.md) tohoto kurzu. 

1. Načtěte primární klíč pro účet služby Azure Cosmos DB pomocí následujícího příkazu rozhraní příkazového řádku v okně terminálu: 

    ```azure-cli-interactive
    az cosmosdb list-keys --name <cosmosdb-name> -g myResourceGroup
    ```    
    
    \<cosmosdb-name > je název účtu Azure Cosmos DB, který jste vytvořili v [část 4](tutorial-develop-mongodb-nodejs-part4.md) tohoto kurzu.

1. Zkopírujte primární klíč do **environment.js** soubor jako `key` hodnotu.

Aplikace teď má všechny informace potřebné k připojení ke službě Azure Cosmos DB. 

## <a name="create-a-hero-model"></a>Vytvoření modelu Hero

Dále je třeba definovat schéma data uložená ve službě Azure Cosmos DB tak, že definujete soubor modelu. Pomocí následujících kroků vytvořte _Hero model_ , který definuje schéma dat:

1. V podokně Průzkumník pod **server** složce vytvořte soubor s názvem **hero.model.js**.

1. Zkopírujte následující kód do **hero.model.js** souboru. Kód poskytuje následující funkce:

   * Vyžaduje Mongoose.
   * Vytvoří nové schéma s ID, jménem (name) a slavným výrokem (saying).
   * Vytvoří model s použitím schématu.
   * Vyexportuje model. 
   * Pojmenuje kolekci **hrdinů** (místo **Heros**, což je výchozí název kolekce podle pravidel pojmenování množném čísle Mongoose).

   ```javascript
   const mongoose = require('mongoose');

   const Schema = mongoose.Schema;

   const heroSchema = new Schema(
     {
       id: { type: Number, required: true, unique: true },
       name: String,
       saying: String
     },
     {
       collection: 'Heroes'
     }
   );

   const Hero = mongoose.model('Hero', heroSchema);

   module.exports = Hero;
   ```

## <a name="create-a-hero-service"></a>Vytvoření služby Hero

Po vytvoření modelu hero, třeba definovat službu, která tato data číst a provést seznamu, vytvářet, odstranit a operace aktualizace. Pomocí následujících kroků vytvořte _službu Hero_ , zadávání dotazů na data ze služby Azure Cosmos DB:

1. V podokně Průzkumník pod **server** složce vytvořte soubor s názvem **hero.service.js**.

1. Zkopírujte následující kód do **hero.service.js** souboru. Kód poskytuje následující funkce:

   * Získá model, který jste vytvořili.
   * Se připojí k databázi.
   * Vytvoří `docquery` proměnné, která se používá `hero.find` metoda definovat dotaz, který vrátí všechny hrdiny.
   * Spustí se dotaz `docquery.exec` funkce se příslib k získání seznamu všech hrdinů, kde je stav odpovědi 200. 
   * Pokud je stav 500 odešle zpět chybovou zprávu.
   * Protože používáme moduly, získá hrdiny. 

   ```javascript
   const Hero = require('./hero.model');

   require('./mongo').connect();

   function getHeroes() {
     const docquery = Hero.find({});
     docquery
       .exec()
       .then(heroes => {
         res.status(200).json(heroes);
       })
       .catch(error => {
         res.status(500).send(error);
         return;
       });
   }

   module.exports = {
     getHeroes
   };
   ```

## <a name="configure-routes"></a>Nakonfigurujte trasy

V dalším kroku potřebujete nastavit trasy pro zpracování adresy URL pro get, vytváření, čtení a odstranit zastaralé požadavky. Zadejte funkce zpětného volání metody směrování (také nazývané _funkce obslužné rutiny_). Tyto funkce se volá se, když aplikace obdrží požadavek na určeném koncovém bodě a metodou HTTP. K přidání služby Hero a definování trasy, postupujte následovně:

1. Ve Visual Studio Code v **routes.js** souboru, okomentujte `res.send` funkci, která odesílá ukázková data hrdinů. Přidejte řádek pro zavolání `heroService.getHeroes` namísto toho funkci.

    ```javascript
    router.get('/heroes', (req, res) => {
      heroService.getHeroes(req, res);
    //  res.send(200, [
    //      {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    //  ])
    });
    ```

1. V **routes.js** souboru `require` službu hero:

    ```javascript
    const heroService = require('./hero.service'); 
    ```

1. V **hero.service.js** soubor, aktualizovat `getHeroes` funkci `req` a `res` parametry následujícím způsobem:

    ```javascript
    function getHeroes(req, res) {
    ```

Pojďme si krátce ke kontrole a provede předchozí kód. Nejprve přejdeme do souboru index.js, který nastaví node server. Všimněte si, že nastaví a definuje trasy. V dalším kroku souboru routes.js hovoří o službě hero a že má získat vaše funkce, jako je **getHeroes**a předat žádost a odpověď. Soubor hero.service.js získá model a připojí se k Mongo. Potom se provede **getHeroes** když jsme volat, a vrátí zpět odpověď 200. 

## <a name="run-the-app"></a>Spuštění aplikace

V dalším kroku spusťte aplikaci pomocí následujících kroků:

1. Ve Visual Studio Code uložte všechny provedené změny. Na levé straně, vyberte **ladění** tlačítko ![ikona ladit ve Visual Studio Code](./media/tutorial-develop-mongodb-nodejs-part5/debug-button.png)a pak vyberte **spustit ladění** tlačítko ![ikona ladit ve Visual Studio Code ](./media/tutorial-develop-mongodb-nodejs-part5/start-debugging-button.png).

1. Nyní přejděte v prohlížeči. Otevřít **vývojářské nástroje** a **kartu Síť**. Přejděte na http://localhost:3000, a zjistíte, že naši aplikaci.

    ![Nový účet služby Azure Cosmos DB na webu Azure Portal](./media/tutorial-develop-mongodb-nodejs-part5/azure-cosmos-db-heroes-app.png)

Neexistují žádní hrdinové ještě v aplikaci. V další části tohoto kurzu přidáme put, push a funkci odstraňování. Pak můžeme přidat, aktualizovat a odstraňovat hrdiny z uživatelského rozhraní pomocí připojení Mongoose k naší databázi Azure Cosmos DB. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud prostředky už nepotřebujete, můžete odstranit skupinu prostředků, účet služby Azure Cosmos DB a všech souvisejících prostředků. Odstranit skupinu prostředků, použijte následující kroky:

 1. Přejděte do skupiny prostředků, ve které jste vytvořili účet Azure Cosmos DB.
 1. Vyberte **Odstranit skupinu prostředků**.
 1. Potvrďte název skupiny prostředků, které chcete odstranit a vyberte **odstranit**.

## <a name="next-steps"></a>Další postup

Přejděte na část 6 tohoto kurzu přidáte Post, Put a Delete do aplikace funkce:

> [!div class="nextstepaction"]
> [6. část: Přidat Post, Put a Delete do aplikace funkce](tutorial-develop-mongodb-nodejs-part6.md)
