---
title: 'Kurz: Vytvoření webové aplikace Node.js pomocí sady JavaScript SDK ke správě dat SQL API služby Azure Cosmos DB'
description: Tento kurz Node.js popisuje, jak používat Microsoft Azure Cosmos DB k ukládání a přístup k datům z webové aplikace Node.js Express hostované na funkci Web Apps služby Microsoft Azure App Service.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/10/2018
ms.author: sngun
Customer intent: As a developer, I want to build a Node.js web application to access and manage SQL API account resources in Azure Cosmos DB, so that customers can better use the service.
ms.openlocfilehash: 47a4b3bc1e71c9dacc53ca3ff507768d4a0f20e9
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55510574"
---
# <a name="tutorial-build-a-nodejs-web-app-using-the-javascript-sdk-to-manage-a-sql-api-account-in-azure-cosmos-db"></a>Kurz: Vytvoření webové aplikace Node.js pomocí sady JavaScript SDK ke správě účtu rozhraní SQL API ve službě Azure Cosmos DB 

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Jako vývojář můžete mít aplikace, které používají dat dokumentů typu NoSQL. Účet rozhraní SQL API ve službě Azure Cosmos DB můžete použít k ukládání a přístup k těmto datům dokumentu. V tomto kurzu Node.js se dozvíte, jak ukládat a přístup k datům z účtu rozhraní SQL API ve službě Azure Cosmos DB s použitím aplikace Node.js Express, která je hostována v rámci funkce Web Apps služby Microsoft Azure App Service. V tomto kurzu vytvoříte webové aplikace (aplikace seznamu úkolů), která umožňuje vytvářet, načítat a dokončení úlohy. Úkoly se ve službě Azure Cosmos DB ukládají jako dokumenty JSON. 

Tento kurz ukazuje, jak vytvořit účet rozhraní SQL API ve službě Azure Cosmos DB pomocí webu Azure portal. Potom sestavíte a spusťte webovou aplikaci, která je založená na Node.js SDK k vytvoření databáze a kontejner a přidat položky do kontejneru. V tomto kurzu se používá sada JavaScript SDK verze 2.0.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Vytvoření účtu služby Azure Cosmos DB
> * Vytvoření nové aplikace Node.js
> * Připojení aplikace ke službě Azure Cosmos DB
> * Spuštění a nasazení aplikace v Azure

## <a name="_Toc395783176"></a>Požadavky

Než budete postupovat podle pokynů v tomto článku, ujistěte se, že máte následující prostředky:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js][Node.js] verze 6.10 nebo novější.
* [Generátor Express](https://www.expressjs.com/starter/generator.html) (Express můžete nainstalovat prostřednictvím příkazu `npm install express-generator -g`)
* Na místní pracovní stanici nainstalujte [Git][Git].

## <a name="_Toc395637761"></a>Vytvoření účtu služby Azure Cosmos DB
Začněme vytvořením účtu služby Azure Cosmos DB. Pokud již účet máte nebo pokud používáte pro účely tohoto kurzu emulátor služby Azure Cosmos DB, můžete přeskočit na [krok 2: Vytvoření nové aplikace Node.js](#_Toc395783178).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [cosmos-db-keys](../../includes/cosmos-db-keys.md)]

## <a name="_Toc395783178"></a>Vytvoření nové aplikace Node.js
Nyní naučíme, jak vytvořit základní projekt Hello World Node.js pomocí rozhraní Express.

1. Otevřete svůj oblíbený terminál, jako je třeba příkazový řádek Node.js.

1. Přejděte do adresáře, do kterého chcete novou aplikaci uložit.

1. Pomocí generátoru Express vygenerujte novou aplikaci s názvem **todo**.

   ```bash
   express todo
   ```

1. Otevřete adresář **todo** a nainstalujte závislosti.

   ```bash
   cd todo
   npm install
   ```

1. Spusťte novou aplikaci.

   ```bash
   npm start
   ```

1. Pokud si chcete zobrazit novou aplikaci, přejděte v prohlížeči na adresu [http://localhost:3000](http://localhost:3000).
   
   ![Výuka Node.js – snímek obrazovky aplikace Hello World v okně prohlížeče](./media/sql-api-nodejs-application/cosmos-db-node-js-express.png)

 Zastavte aplikaci s použitím kombinace kláves CTRL + C v okně terminálu a vyberte **y** ukončete dávkovou úlohu.

## <a name="_Toc395783179"></a>Nainstalujte požadované moduly.

Soubor **package.json** je jedním ze souborů vytvořených v kořenu projektu. Tento soubor obsahuje seznam dalších modulů, které aplikace Node.js vyžaduje. Až budete tuto aplikaci nasazovat v Azure, tento soubor se použije k vyhodnocení, které moduly se musí v Azure pro podporu vaší aplikace nainstalovat. Pro účely tohoto kurzu nainstalujte další dva balíčky.

1. Otevřete terminál a nainstalujte **asynchronní** pomocí npm modul.

   ```bash
   npm install async --save
   ```

2. Nainstalujte přes npm modul **@azure/cosmos**. 

   ```bash
   npm install @azure/cosmos
   ```

## <a name="_Toc395783180"></a>Připojit aplikaci Node.js do služby Azure Cosmos DB
Dokončili jste počáteční nastavení a konfiguraci a teď napíšete kód, který potřebuje aplikace seznamu úkolů ke komunikaci se službou Azure Cosmos DB.

### <a name="create-the-model"></a>Vytvoření modelu
1. V kořenovém adresáři projektu vytvořte nový adresář s názvem **modely**.  

2. V adresáři **models** vytvořte nový soubor s názvem **taskDao.js**. Tento soubor obsahuje kód potřebný k vytvoření databáze a kontejneru. Rovněž definuje metody pro čtení, aktualizaci, vytvoření a vyhledání úkolů ve službě Azure Cosmos DB. 

3. Zkopírujte následující kód do **taskDao.js** souboru:

   ```javascript
   // @ts-check
   const CosmosClient = require("@azure/cosmos").CosmosClient;
   const debug = require("debug")("todo:taskDao");
   class TaskDao {
     /**
      * Manages reading, adding, and updating Tasks in Cosmos DB
      * @param {CosmosClient} cosmosClient
      * @param {string} databaseId
      * @param {string} containerId
      */
     constructor(cosmosClient, databaseId, containerId) {
       this.client = cosmosClient;
       this.databaseId = databaseId;
       this.collectionId = containerId;

       this.database = null;
       this.container = null;
     }

     async init() {
       debug("Setting up the database...");
       const dbResponse = await this.client.databases.createIfNotExists({
         id: this.databaseId
       });
       this.database = dbResponse.database;
       debug("Setting up the database...done!");
       debug("Setting up the container...");
       const coResponse = await this.database.containers.createIfNotExists({
         id: this.collectionId
       });
       this.container = coResponse.container;
       debug("Setting up the container...done!");
     }

     async find(querySpec) {
       debug("Querying for items from the database");
       if (!this.container) {
         throw new Error("Collection is not initialized.");
       }
       const { result: results } = await this.container.items
        .query(querySpec)
        .toArray();
      return results;
    }

    async addItem(item) {
      debug("Adding an item to the database");
      item.date = Date.now();
      item.completed = false;
      const { body: doc } = await this.container.items.create(item);
      return doc;
    }

    async updateItem(itemId) {
      debug("Update an item in the database");
      const doc = await this.getItem(itemId);
      doc.completed = true;

      const { body: replaced } = await this.container.item(itemId).replace(doc);
      return replaced;
    }

    async getItem(itemId) {
      debug("Getting an item from the database");
      const { body } = await this.container.item(itemId).read();
      return body;
    }
  }

   module.exports = TaskDao;
   ```
4. Uložte a zavřete soubor **taskDao.js**.  

### <a name="create-the-controller"></a>Vytvoření kontroleru

1. V adresáři **routes** projektu vytvořte nový soubor s názvem **tasklist.js**.  

2. Do souboru **tasklist.js** přidejte následující kód: Tento kód načte moduly CosmosClient a async, které se používají v souboru **tasklist.js**. Tento kód také definuje třídu **TaskList**, která se předává jako instance objektu **TaskDao**, který jsme definovali dříve:
   
   ```javascript
   const TaskDao = require("../models/TaskDao");

   class TaskList {
     /**
      * Handles the various APIs for displaying and managing tasks
      * @param {TaskDao} taskDao
     */
    constructor(taskDao) {
    this.taskDao = taskDao;
    }
    async showTasks(req, res) {
      const querySpec = {
        query: "SELECT * FROM root r WHERE r.completed=@completed",
        parameters: [
          {
            name: "@completed",
            value: false
          }
        ]
      };

      const items = await this.taskDao.find(querySpec);
      res.render("index", {
        title: "My ToDo List ",
        tasks: items
      });
    }

    async addTask(req, res) {
      const item = req.body;

      await this.taskDao.addItem(item);
      res.redirect("/");
    }

    async completeTask(req, res) {
      const completedTasks = Object.keys(req.body);
      const tasks = [];

      completedTasks.forEach(task => {
        tasks.push(this.taskDao.updateItem(task));
      });

      await Promise.all(tasks);

      res.redirect("/");
    }
  }

  module.exports = TaskList;
   ```

3. Uložte a zavřete soubor **tasklist.js**.

### <a name="add-configjs"></a>Přidání souboru config.js

1. V kořenovém adresáři projektu vytvořte nový soubor **config.js**. 

2. Do souboru **config.js** přidejte následující kód. Tento kód definuje nastavení konfigurace a hodnoty, které aplikace potřebuje.
   
   ```javascript
   const config = {};

   config.host = process.env.HOST || "[the endpoint URI of your Azure Cosmos DB account]";
   config.authKey =
     process.env.AUTH_KEY || "[the PRIMARY KEY value of your Azure Cosmos DB account";
   config.databaseId = "ToDoList";
   config.containerId = "Items";

   if (config.host.includes("https://localhost:")) {
     console.log("Local environment detected");
     console.log("WARNING: Disabled checking of self-signed certs. Do not have this code in production.");
     process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
     console.log(`Go to http://localhost:${process.env.PORT || '3000'} to try the sample.`);
   }

   module.exports = config;
   ```

3. V **config.js** souboru, aktualizujte hodnoty HOST a AUTH_KEY hodnotami, na stránce klíče účtu služby Azure Cosmos DB na [webu Azure portal](https://portal.azure.com). 

4. Uložte a zavřete soubor **config.js**.

### <a name="modify-appjs"></a>Úprava souboru app.js

1. V adresáři projektu otevřete soubor **app.js**. Tento soubor byl vytvořen již dříve, při vytváření webové aplikace Express.  

2. Do souboru **app.js** přidejte následující kód. Tento kód definuje konfigurační soubor, který se má použít, a načte hodnoty několika proměnných, které použijete v dalších částech. 
   
   ```javascript
   const CosmosClient = require("@azure/cosmos").CosmosClient;
   const config = require("./config");
   const TaskList = require("./routes/tasklist");
   const TaskDao = require("./models/taskDao");

   const express = require("express");
   const path = require("path");
   const logger = require("morgan");
   const cookieParser = require("cookie-parser");
   const bodyParser = require("body-parser");

   const app = express();

   // view engine setup
   app.set("views", path.join(__dirname, "views"));
   app.set("view engine", "jade");

   // uncomment after placing your favicon in /public
   //app.use(favicon(path.join(__dirname, 'public', 'favicon.ico')));
   app.use(logger("dev"));
   app.use(bodyParser.json());
   app.use(bodyParser.urlencoded({ extended: false }));
   app.use(cookieParser());
   app.use(express.static(path.join(__dirname, "public")));

   //Todo App:
   const cosmosClient = new CosmosClient({
     endpoint: config.host,
     auth: {
       masterKey: config.authKey
     }
   });
   const taskDao = new TaskDao(cosmosClient, config.databaseId, config.containerId);
   const taskList = new TaskList(taskDao);
   taskDao
     .init(err => {
       console.error(err);
     })
     .catch(err => {
       console.error(err);
       console.error("Shutting down because there was an error setting up the database.");
       process.exit(1);
     });

   app.get("/", (req, res, next) => taskList.showTasks(req, res).catch(next));
   app.post("/addtask", (req, res, next) => taskList.addTask(req, res).catch(next));
   app.post("/completetask", (req, res, next) => taskList.completeTask(req, res).catch(next));
   app.set("view engine", "jade");

   // catch 404 and forward to error handler
   app.use(function(req, res, next) {
     const err = new Error("Not Found");
     err.status = 404;
     next(err);
   });

   // error handler
   app.use(function(err, req, res, next) {
     // set locals, only providing error in development
     res.locals.message = err.message;
     res.locals.error = req.app.get("env") === "development" ? err : {};

     // render the error page
     res.status(err.status || 500);
     res.render("error");
   });

   module.exports = app;
   ```

3. Nakonec soubor **app.js** uložte a zavřete.

## <a name="_Toc395783181"></a>Vytvoření uživatelského rozhraní

Teď vytvoříme uživatelské rozhraní, aby uživatelé mohli s aplikací pracovat. Aplikace Express, kterou jsme vytvořili v předchozích částech, používá jako zobrazovací modul **Jade**.

1. Soubor **layout.jade** v adresáři **views** slouží jako globální šablona pro ostatní soubory **.jade**. V tomto kroku jej upravíte použít architekturu Twitter Bootstrap, což je sada nástrojů pro návrh webu.  

2. Otevřete soubor **layout.jade** umístěný ve složce **views** a nahraďte jeho obsah následujícím kódem:

   ```html
   doctype html
   html
     head
       title= title
       link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
       link(rel='stylesheet', href='/stylesheets/style.css')
     body
       nav.navbar.navbar-inverse.navbar-fixed-top
         div.navbar-header
           a.navbar-brand(href='#') My Tasks
       block content
       script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
       script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')
   ```

    Tento kód říká **Jade** modul vykreslovat kód HTML pro naši aplikaci a vytvoří **bloku** volá **obsah** kterého můžeme zadat rozložení stránek s obsahem. Uložte a zavřete soubor **layout.jade**.

3. Teď otevřete soubor **index.jade**, který představuje zobrazení používané v naší aplikaci, a nahraďte jeho obsah následujícím kódem:

   ```html
   extends layout
   block content
        h1 #{title}
        br
        
        form(action="/completetask", method="post")
         table.table.table-striped.table-bordered
            tr
              td Name
              td Category
              td Date
              td Complete
            if (typeof tasks === "undefined")
              tr
                td
            else
              each task in tasks
                tr
                  td #{task.name}
                  td #{task.category}
                  - var date  = new Date(task.date);
                  - var day   = date.getDate();
                  - var month = date.getMonth() + 1;
                  - var year  = date.getFullYear();
                  td #{month + "/" + day + "/" + year}
                  td
                   if(task.completed) 
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
                   else
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
          button.btn.btn-primary(type="submit") Update tasks
        hr
        form.well(action="/addtask", method="post")
          label Item Name:
          input(name="name", type="textbox")
          label Item Category:
          input(name="category", type="textbox")
          br
          button.btn(type="submit") Add item
   ```

Tento kód rozšiřuje rozložení a poskytuje obsah pro zástupný symbol **content**, který jsme viděli v souboru **layout.jade** výše. V tomto rozložení jsme vytvořili dva formuláře HTML.

První formulář obsahuje tabulku dat a tlačítko, které umožňuje aktualizovat položky odesláním informací do metody kontroleru **/completeTask**.
    
Druhý formulář obsahuje dvě vstupní pole a tlačítko, které umožňuje vytvořit novou položku odesláním informací do metody kontroleru **/addtask**. To je vše, co potřebujeme, aby aplikace fungovala.

## <a name="_Toc395783181"></a>Místní spuštění aplikace

Teď, když jste vytvořili aplikaci, můžete ji spustit místně pomocí následujících kroků:  

1. Chcete-li aplikaci otestovat na místním počítači, spusťte `npm start` v terminálu spusťte aplikaci a pak aktualizujte [ http://localhost:3000 ](http://localhost:3000) stránku prohlížeče. Stránka by teď měla vypadat jako na následujícím snímku obrazovky:
   
    ![Snímek obrazovky aplikace Seznam úkolů v okně prohlížeče](./media/sql-api-nodejs-application/cosmos-db-node-js-localhost.png)

    > [!TIP]
    > Pokud se zobrazí chyba týkající se odsazení v souboru layout.jade nebo index.Jade, ujistěte se, že jsou první dva řádky v obou souborech zarovnané vlevo, bez mezer. Pokud jsou před prvními dvěma řádky mezery, odeberte je, oba soubory uložte a pak aktualizujte okno prohlížeče. 

2. Použijte pole Položka, název položky a kategorie k zadání nového úkolu a pak vyberte **přidat položku**. Ve službě Azure Cosmos DB se vytvoří dokument s těmito vlastnostmi. 

3. Stránka by se měla aktualizovat, aby se v seznamu úkolů zobrazila nově vytvořená položka.
   
    ![Snímek obrazovky aplikace s novou položkou v seznamu úkolů](./media/sql-api-nodejs-application/cosmos-db-node-js-added-task.png)

4. K dokončení úkolu, zaškrtněte políčko ve sloupci dokončení a potom vyberte **aktualizovat úkoly**. Vytvořený dokument se aktualizuje a odebere ze zobrazení.

5. Pokud chcete aplikaci zastavit, stiskněte CTRL + C v okně terminálu a pak ukončete dávkovou úlohu výběrem **Y**.

## <a name="_Toc395783182"></a>Nasazení aplikace do webové aplikace

Po vaše aplikace úspěšně místně, můžete ji nasadit do Azure pomocí následujících kroků:

1. Pokud jste tak již neučinili, povolte úložiště git pro vaši aplikaci Web Apps.

2. Přidejte svoji aplikaci webových aplikací jako vzdálené úložiště git.
   
   ```bash
   git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
   ```

3. Odesláním aplikace do vzdáleného úložiště ji nasaďte.
   
   ```bash
   git push azure master
   ```

4. Během několika sekund se vaše webová aplikace publikuje a spustí v prohlížeči.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud tyto prostředky už nepotřebujete, můžete odstranit skupinu prostředků, účet služby Azure Cosmos DB a všech souvisejících prostředků. Uděláte to tak, vyberte skupinu prostředků, který jste použili pro účet služby Azure Cosmos DB, vyberte **odstranit**a potom ověřte název skupiny prostředků pro odstranění.

## <a name="_Toc395637775"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytváření mobilních aplikací s Xamarinem a Azure Cosmos DB](mobile-apps-with-xamarin.md)


[Node.js]: https://nodejs.org/
[Git]: https://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-todo-app

