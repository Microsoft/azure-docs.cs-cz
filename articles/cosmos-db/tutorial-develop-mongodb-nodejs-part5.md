---
title: Připojení aplikace Angular k rozhraní API Azure Cosmos DB pro MongoDB pomocí mongoose
description: Tento kurz popisuje, jak vytvořit aplikaci Node.js pomocí Angular a Express pro správu dat uložených v Cosmos DB. V této části použijete Mongoose pro připojení k Azure Cosmos DB.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18
ms.reviewer: sngun
Customer intent: As a developer, I want to build a Node.js application, so that I can manage the data stored in Cosmos DB.
ms.openlocfilehash: ba893eeb8c2560397f3524d1042566dbafee7d1b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75444707"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---use-mongoose-to-connect-to-cosmos-db"></a>Vytvoření úhlové aplikace s rozhraním API Azure Cosmos DB pro MongoDB – připojení k Cosmos DB pomocí mongoose

Tento vícedílný kurz ukazuje, jak vytvořit aplikaci Node.js s Express a Angular a připojit ji k [účtu Cosmos nakonfigurovaném pomocí rozhraní API Cosmos DB pro MongoDB](mongodb-introduction.md). Tento článek popisuje část 5 kurzu a staví na [části 4](tutorial-develop-mongodb-nodejs-part4.md).

V této části tutoriálu budete:

> [!div class="checklist"]
> * Použijte Mongoose pro připojení k Cosmos DB.
> * Získejte připojovací řetězec Cosmos DB.
> * Vytvořte model Hrdina.
> * Vytvořte službu Hero a získejte data hrdiny.
> * Spusťte aplikaci místně.

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

* Před zahájením tohoto kurzu proveďte kroky v [části 4](tutorial-develop-mongodb-nodejs-part4.md).

* Tento kurz vyžaduje, abyste místní spouštění příkazového příkazového odložení příkazového odlokala. Musíte mít nainstalovanou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete nainstalovat nebo upgradovat azure cli, najdete [v tématu instalace Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Tento kurz vás provede kroky k vytvoření aplikace krok za krokem. Pokud si chcete stáhnout dokončený projekt, v [úložišti angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) na GitHubu můžete získat hotovou aplikaci.

## <a name="use-mongoose-to-connect"></a>Použití Mongoose pro připojení

Mongoose je knihovna modelování dat objektů (ODM) pro MongoDB a Node.js. Mongoose můžete použít k připojení k účtu Azure Cosmos DB. Pomocí následujících kroků nainstalujte Mongoose a připojte se k Azure Cosmos DB:

1. Nainstalujte modul mongoose npm, což je API, které se používá k rozhovoru s MongoDB.

    ```bash
    npm i mongoose --save
    ```

1. Ve složce **server** vytvořte soubor s názvem **mongo.js**. Do tohoto souboru přidáte podrobnosti o připojení vašeho účtu Azure Cosmos DB.

1. Zkopírujte následující kód do souboru **mongo.js.** Kód poskytuje následující funkce:

   * Vyžaduje Mongoose.
   * Přepíše slib Mongo, že použije základní slib, který je součástí ES6/ES2015 a novějších verzí.
   * Volá soubor env, který umožňuje nastavit určité věci na základě toho, zda jste v inscenaci, výrobě nebo vývoji. Tento soubor vytvoříte v další části.
   * Zahrnuje připojovací řetězec MongoDB, který je nastaven v env souboru.
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
    
1. V podokně Průzkumník a v části **Server**vytvořte složku s názvem **environment**. Ve složce **Prostředí** vytvořte soubor s názvem **environment.js**.

1. Ze souboru mongo.js musíme zahrnout `dbName`hodnoty `key`pro , `cosmosPort` a parametry. Do souboru **environment.js** zkopírujte následující kód:

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

Chcete-li připojit aplikaci k Azure Cosmos DB, je třeba aktualizovat nastavení konfigurace pro aplikaci. Nastavení můžete aktualizovat pomocí následujících kroků: 

1. Na webu Azure Portal získejte číslo portu, název účtu Azure Cosmos DB a hodnoty primárního klíče pro váš účet Azure Cosmos DB.

1. V souboru **environment.js** změňte `port` hodnotu na 10255. 

    ```javascript
    const port = 10255;
    ```

1. V souboru **environment.js** změňte `accountName` hodnotu na název účtu Azure Cosmos DB, který jste vytvořili v [části 4](tutorial-develop-mongodb-nodejs-part4.md) kurzu. 

1. Načtěte primární klíč pro účet služby Azure Cosmos DB pomocí následujícího příkazu rozhraní příkazového řádku v okně terminálu: 

    ```azure-cli-interactive
    az cosmosdb list-keys --name <cosmosdb-name> -g myResourceGroup
    ```    
    
    \<cosmosdb název> je název účtu Azure Cosmos DB, který jste vytvořili v [části 4](tutorial-develop-mongodb-nodejs-part4.md) kurzu.

1. Zkopírujte primární klíč do souboru **environment.js** jako hodnotu. `key`

Teď vaše aplikace má všechny potřebné informace pro připojení k Azure Cosmos DB. 

## <a name="create-a-hero-model"></a>Vytvoření modelu Hero

Dále je třeba definovat schéma dat pro uložení v Azure Cosmos DB definováním souboru modelu. Pomocí následujících kroků vytvořte _model hrdiny,_ který definuje schéma dat:

1. V podokně Průzkumník a pod složkou **serveru** vytvořte soubor s názvem **hero.model.js**.

1. Zkopírujte následující kód do souboru **hero.model.js.** Kód poskytuje následující funkce:

   * Vyžaduje Mongoose.
   * Vytvoří nové schéma s ID, jménem (name) a slavným výrokem (saying).
   * Vytvoří model pomocí schématu.
   * Vyexportuje model. 
   * Pojmenuje kolekci **Heroes** (místo **Heros**, což je výchozí název kolekce na základě pravidel pojmenování mongoose množného čísla).

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

Po vytvoření modelu hrdiny je třeba definovat službu pro čtení dat a provádění operací v seznamu, vytváření, odstraňování a aktualizaci. Pomocí následujících kroků vytvořte _službu Hero,_ která se dotazuje na data z Azure Cosmos DB:

1. V podokně Průzkumník a pod složkou **serveru** vytvořte soubor s názvem **hero.service.js**.

1. Zkopírujte následující kód do souboru **hero.service.js.** Kód poskytuje následující funkce:

   * Získá model, který jste vytvořili.
   * Připojí se k databázi.
   * Vytvoří `docquery` proměnnou, `hero.find` která používá metodu k definování dotazu, který vrací všechny hrdiny.
   * Spustí dotaz s `docquery.exec` funkcí se slibem získat seznam všech hrdinů, kde je stav odpovědi 200. 
   * Odešle zpět chybovou zprávu, pokud je stav 500.
   * Získá hrdiny, protože používáme moduly. 

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

Dále je třeba nastavit trasy pro zpracování adres URL pro získání, vytvoření, čtení a odstranění požadavků. Metody směrování určují funkce zpětného volání (nazývané také _funkce obslužné rutiny)._ Tyto funkce jsou volány, když aplikace obdrží požadavek na zadaný koncový bod a metodu HTTP. Pomocí následujících kroků přidejte službu Hero a definujte trasy:

1. V kódu sady Visual Studio v souboru **routes.js** zakomentujte `res.send` funkci, která odesílá data ukázkového hrdiny. Přidejte linku pro `heroService.getHeroes` volání funkce.

    ```javascript
    router.get('/heroes', (req, res) => {
      heroService.getHeroes(req, res);
    //  res.send(200, [
    //      {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    //  ])
    });
    ```

1. V souboru **routes.js** služba `require` hrdiny:

    ```javascript
    const heroService = require('./hero.service'); 
    ```

1. V souboru **hero.service.js** `getHeroes` aktualizujte funkci `req` `res` tak, aby převzala parametry a následujícím způsobem:

    ```javascript
    function getHeroes(req, res) {
    ```

Podívejme se na chvíli na to, abychom si prohlédli a prošli předchozí kód. Nejprve jsme se dostali do souboru index.js, který nastavuje uzel serveru. Všimněte si, že nastavuje a definuje trasy. Dále, vaše routes.js soubor mluví s hrdinou služby a řekne, aby si své funkce, jako **getHeroes**, a předat žádost a odpověď. Soubor hero.service.js získá model a připojí se k Mongo. Pak to provede **getHeroes,** když to nazýváme, a vrátí zpět odpověď 200. 

## <a name="run-the-app"></a>Spuštění aplikace

Dále spusťte aplikaci pomocí následujících kroků:

1. V kódu sady Visual Studio uložte všechny změny. Na levé straně vyberte ikonu Ladění **ladicího** tlačítka ![v kódu](./media/tutorial-develop-mongodb-nodejs-part5/debug-button.png)sady Visual ![Studio a potom vyberte ikonu Ladění tlačítka](./media/tutorial-develop-mongodb-nodejs-part5/start-debugging-button.png) **Start** v kódu sady Visual Studio .

1. Nyní přepněte do prohlížeče. Otevřete **nástroje pro vývojáře** a **kartu Síť**. Přejděte `http://localhost:3000`na , a tam vidíte naši aplikaci.

    ![Nový účet služby Azure Cosmos DB na webu Azure Portal](./media/tutorial-develop-mongodb-nodejs-part5/azure-cosmos-db-heroes-app.png)

V aplikaci zatím nejsou uloženi žádní hrdinové. V další části tohoto kurzu přidáme funkci put, push a delete. Pak můžeme přidat, aktualizovat a odstranit hrdiny z ui pomocí připojení Mongoose do naší databáze Azure Cosmos. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už prostředky nepotřebujete, můžete odstranit skupinu prostředků, účet Azure Cosmos DB a všechny související prostředky. Skupinu prostředků odstraňte pomocí následujících kroků:

 1. Přejděte do skupiny prostředků, kde jste vytvořili účet Azure Cosmos DB.
 1. Vyberte **Odstranit skupinu prostředků**.
 1. Potvrďte název skupiny prostředků, kterou chcete odstranit, a vyberte **Odstranit**.

## <a name="next-steps"></a>Další kroky

Pokračujte do části 6 kurzu přidat post, Put, a odstranit funkce do aplikace:

> [!div class="nextstepaction"]
> [Část 6: Přidání funkcí Příspěvku, Put a Delete do aplikace](tutorial-develop-mongodb-nodejs-part6.md)
