---
title: Připojte úhlovou aplikaci k rozhraní API Azure Cosmos DB pro MongoDB pomocí Mongoose
description: V tomto kurzu se dozvíte, jak vytvořit aplikaci Node. js pomocí úhlů a Express ke správě dat uložených v Cosmos DB. V této části použijete Mongoose k připojení k Azure Cosmos DB.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18
ms.reviewer: sngun
ms.openlocfilehash: fb0e08c9d85f4472a2fa13001e71edd12149d430
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858736"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---use-mongoose-to-connect-to-cosmos-db"></a>Vytvoření úhlové aplikace s rozhraním API Azure Cosmos DB pro MongoDB – použití Mongoose pro připojení k Cosmos DB

Tento výukový kurz ukazuje, jak vytvořit aplikaci v Node. js s expresním a úhlovým připojením k vašemu [účtu Cosmos nakonfigurovanému pomocí rozhraní API služby Cosmos DB pro MongoDB](mongodb-introduction.md). Tento článek popisuje část 5 kurzu a sestavuje se na [část 4](tutorial-develop-mongodb-nodejs-part4.md).

V této části kurzu budete:

> [!div class="checklist"]
> * Pomocí Mongoose se připojte k Cosmos DB.
> * Získejte připojovací řetězec Cosmos DB.
> * Vytvořte Hero model.
> * Vytvořte službu Hero, která získá data Hero.
> * Spusťte aplikaci místně.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Než začnete s tímto kurzem, proveďte kroky v [části 4](tutorial-develop-mongodb-nodejs-part4.md).

* Tento kurz vyžaduje, abyste spouštěli Azure CLI místně. Musíte mít nainstalovanou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete nainstalovat nebo upgradovat rozhraní příkazového řádku Azure CLI, přečtěte si téma [instalace Azure cli 2,0](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Tento kurz vás provede jednotlivými kroky vytvoření aplikace krok za krokem. Pokud si chcete stáhnout dokončený projekt, v [úložišti angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) na GitHubu můžete získat hotovou aplikaci.

## <a name="use-mongoose-to-connect"></a>Použití Mongoose k připojení

Mongoose je knihovna ODM (Object data Modeling) pro MongoDB a Node. js. Pomocí Mongoose se můžete připojit k účtu Azure Cosmos DB. K instalaci Mongoose a připojení k Azure Cosmos DB použijte následující postup:

1. Nainstalujte modul Mongoose NPM, což je rozhraní API, které se používá ke komunikaci s MongoDB.

    ```bash
    npm i mongoose --save
    ```

1. Ve složce na **serveru** vytvořte soubor s názvem **Mongo. js**. Do tohoto souboru přidáte podrobnosti o připojení Azure Cosmos DB účtu.

1. Zkopírujte následující kód do souboru **Mongo. js** . Kód poskytuje následující funkce:

   * Vyžaduje Mongoose.
   * Přepíše Mongo příslib, aby používal základní příslib, který je integrovaný v ES6/ES2015 a novějších verzích.
   * Zavolá soubor ENV, který vám umožní nastavit určité věci na základě toho, jestli jste v přípravě, výrobě nebo vývoji. Tento soubor vytvoříte v další části.
   * Zahrnuje připojovací řetězec MongoDB, který je nastavený v souboru env.
   * Vytvoří funkci connect, která volá Mongoose.

     ```javascript
     const mongoose = require('mongoose');
     /**
     * Set to Node.js native promises
     * Per https://mongoosejs.com/docs/promises.html
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
    
1. V podokně Průzkumník v části **Server**vytvořte složku s názvem **prostředí**. Ve složce **prostředí** vytvořte soubor s názvem **Environment. js**.

1. V souboru Mongo. js musíme zahrnout hodnoty `dbName` `key` `cosmosPort` parametrů, a. Do souboru **Environment. js** zkopírujte následující kód:

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

Chcete-li připojit aplikaci k Azure Cosmos DB, je nutné aktualizovat nastavení konfigurace aplikace. K aktualizaci nastavení použijte následující postup: 

1. V Azure Portal Získejte číslo portu, Azure Cosmos DB název účtu a hodnoty primárního klíče pro váš účet Azure Cosmos DB.

1. V souboru **Environment. js** změňte hodnotu `port` na 10255. 

    ```javascript
    const port = 10255;
    ```

1. V souboru **Environment. js** změňte hodnotu `accountName` na název účtu Azure Cosmos DB, který jste vytvořili v [části 4](tutorial-develop-mongodb-nodejs-part4.md) tohoto kurzu. 

1. Načtěte primární klíč pro účet služby Azure Cosmos DB pomocí následujícího příkazu rozhraní příkazového řádku v okně terminálu: 

    ```azure-cli-interactive
    az cosmosdb list-keys --name <cosmosdb-name> -g myResourceGroup
    ```    
    
    \<cosmosdb-Name> je název účtu Azure Cosmos DB, který jste vytvořili v [části 4](tutorial-develop-mongodb-nodejs-part4.md) tohoto kurzu.

1. Zkopírujte primární klíč do souboru **Environment. js** jako `key` hodnotu.

Vaše aplikace teď obsahuje všechny nezbytné informace pro připojení k Azure Cosmos DB. 

## <a name="create-a-hero-model"></a>Vytvoření modelu Hero

Dále musíte definovat schéma dat, která se mají uložit v Azure Cosmos DB definováním souboru modelu. Pomocí následujících kroků vytvořte _model Hero_ , který definuje schéma dat:

1. V podokně Průzkumník ve složce **Server** vytvořte soubor s názvem **Hero. model. js**.

1. Zkopírujte následující kód do souboru **Hero. model. js** . Kód poskytuje následující funkce:

   * Vyžaduje Mongoose.
   * Vytvoří nové schéma s ID, jménem (name) a slavným výrokem (saying).
   * Vytvoří model pomocí schématu.
   * Vyexportuje model. 
   * Pojmenuje kolekci **Heroes** (místo **heros**, což je výchozí název kolekce na základě pravidel pojmenování Mongoose v množném čísle).

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

Po vytvoření modelu Hero je třeba definovat službu pro čtení dat a provádět operace vypisovat, vytvořit, odstranit a aktualizovat. Pomocí následujících kroků vytvořte _službu Hero_ , která se dotazuje na data z Azure Cosmos DB:

1. V podokně Průzkumník ve složce **Server** vytvořte soubor s názvem **Hero. Service. js**.

1. Zkopírujte následující kód do souboru **Hero. Service. js** . Kód poskytuje následující funkce:

   * Získá model, který jste vytvořili.
   * Připojí se k databázi.
   * Vytvoří `docquery` proměnnou, která používá `hero.find` metodu k definování dotazu, který vrátí všechny Heroes.
   * Spustí dotaz s `docquery.exec` funkcí se příslibem k získání seznamu všech Heroes, ve kterých je stav odpovědi 200. 
   * Pošle zpět chybovou zprávu, pokud je stav 500.
   * Získá Heroes, protože používáme moduly. 

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

## <a name="configure-routes"></a>Konfigurace tras

Dál je potřeba nastavit trasy, které budou zpracovávat adresy URL pro žádosti získat, vytvořit, číst a odstranit. Metody směrování určují funkce zpětného volání (označované také jako _funkce obslužné rutiny_). Tyto funkce jsou volány, když aplikace obdrží požadavek na zadaný koncový bod a metodu HTTP. Pomocí následujících kroků přidejte službu Hero a definujte trasy:

1. V Visual Studio Code v souboru **Routes. js** přidejte komentář k `res.send` funkci, která odesílá ukázková data Hero. Místo toho přidejte řádek pro volání `heroService.getHeroes` funkce.

    ```javascript
    router.get('/heroes', (req, res) => {
      heroService.getHeroes(req, res);
    //  res.send(200, [
    //      {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    //  ])
    });
    ```

1. V `require` souboru **Routes. js** služba Hero:

    ```javascript
    const heroService = require('./hero.service'); 
    ```

1. V souboru **Hero. Service. js** aktualizujte `getHeroes` funkci tak, aby převzala parametry `req` a `res` , jak je znázorněno níže:

    ```javascript
    function getHeroes(req, res) {
    ```

Pojďme si projít několik minut a projít si předchozí kód. Nejprve se dorazíme do souboru index. js, který nastaví server Node. Všimněte si, že nastavuje a definuje vaše trasy. Dále váš soubor Routes. js mluví se službou Hero a oznamuje mu, aby získal vaše funkce, jako je **getHeroes**, a předal požadavek a odpověď. Soubor Hero. Service. js získá model a připojí se k Mongo. Pak se při volání spustí **getHeroes** a vrátí zpět odpověď 200. 

## <a name="run-the-app"></a>Spuštění aplikace

Dále spusťte aplikaci pomocí následujících kroků:

1. V Visual Studio Code uložte všechny změny. Na levé straně vyberte tlačítko ![](./media/tutorial-develop-mongodb-nodejs-part5/debug-button.png) **ladit** ikona ladění v Visual Studio Code a pak vyberte tlačítko ![](./media/tutorial-develop-mongodb-nodejs-part5/start-debugging-button.png) **Spustit ladění** ikona ladění v Visual Studio Code.

1. Teď přejděte do prohlížeče. Otevřete **Nástroje pro vývojáře** a **kartu síť**. Přejděte na `http://localhost:3000`a zobrazí se naše aplikace.

    ![Nový účet služby Azure Cosmos DB na webu Azure Portal](./media/tutorial-develop-mongodb-nodejs-part5/azure-cosmos-db-heroes-app.png)

V aplikaci ještě nejsou uložené žádné Heroes. V další části tohoto kurzu přidáme funkce PUT, push a DELETE. Pak můžeme přidat, aktualizovat a odstranit Heroes z uživatelského rozhraní pomocí připojení Mongoose k naší databázi Azure Cosmos. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už prostředky nepotřebujete, můžete odstranit skupinu prostředků, účet Azure Cosmos DB a všechny související prostředky. Chcete-li odstranit skupinu prostředků, použijte následující postup:

 1. Přejít do skupiny prostředků, ve které jste vytvořili účet Azure Cosmos DB.
 1. Vyberte **Odstranit skupinu prostředků**.
 1. Potvrďte název skupiny prostředků, kterou chcete odstranit, a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

Pokud chcete do aplikace přidat funkce post, PUT a DELETE, přejděte k části 6 tohoto kurzu:

> [!div class="nextstepaction"]
> [Část 6: Přidání funkcí post, PUT a DELETE do aplikace](tutorial-develop-mongodb-nodejs-part6.md)
