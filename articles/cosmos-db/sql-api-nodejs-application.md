---
title: Sestavení webové aplikace Node.js pro Azure Cosmos DB | Microsoft Docs
description: Tento kurz Node.js popisuje, jak pomocí služby Microsoft Azure Cosmos DB ukládat data a přistupovat k nim z webové aplikace Node.js Express hostované ve službě Azure Websites.
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: sngun
ms.openlocfilehash: 82711ea96f6b3f8544a411ed1b6636c8473ed7e9
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957342"
---
# <a name="_Toc395783175"></a>Vytvoření webové aplikace Node.js s využitím sady JavaScript SDK pro správu dat rozhraní SQL API služby Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Tento kurz Node.js ukazuje, jak pomocí účtu rozhraní SQL API služby Azure Cosmos DB ukládat data a přistupovat k nim z aplikace Node.js Express hostované ve službě Azure Websites. V tomto kurzu vytvoříte jednoduchou webovou aplikaci (aplikace seznamu úkolů) umožňující vytváření, načítání a dokončování úkolů. Úkoly se ve službě Azure Cosmos DB ukládají jako dokumenty JSON. Následující obrázek ukazuje snímek obrazovky aplikace seznamu úkolů:

![Snímek obrazovky aplikace pro seznam úkolů vytvořené v tomto kurzu k Node.js](./media/sql-api-nodejs-application/cosmos-db-node-js-mytodo.png)

V tomto kurzu se dozvíte, jak vytvořit účet rozhraní SQL API služby Azure Cosmos DB pomocí webu Azure Portal. Pak vytvoříte a spustíte webovou aplikaci využívající sadu Node.js SDK k vytvoření databáze a kontejneru a přidání položek do kontejneru. Tento kurz používá sadu JavaScript SDK verze 2.0.

Hotovou ukázku můžete také získat z [GitHubu][GitHub]. Pokud potřebujete pokyny, jak aplikaci spustit, stačí si přečíst soubor [Readme](https://github.com/Azure-Samples/documentdb-node-todo-app/blob/master/README.md).

## <a name="_Toc395783176"></a>Požadavky

Než budete postupovat podle pokynů tohoto článku, měli byste se ujistit, že máte následující:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js][Node.js] verze 6.10 nebo novější.
* [Generátor Express](http://www.expressjs.com/starter/generator.html) (lze nainstalovat prostřednictvím `npm install express-generator -g`)
* [Git][Git]

## <a name="_Toc395637761"></a>Krok 1: Vytvoření účtu databáze Azure Cosmos DB
Začněme vytvořením účtu služby Azure Cosmos DB. Pokud již účet máte nebo pokud používáte pro účely tohoto kurzu emulátor služby Azure Cosmos DB, můžete přeskočit na [Krok 2: Vytvoření nové aplikace Node.js](#_Toc395783178).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [cosmos-db-keys](../../includes/cosmos-db-keys.md)]

## <a name="_Toc395783178"></a>Krok 2: Vytvoření nové aplikace Node.js
Nyní se naučíme, jak vytvořit základní projekt Node.js Hello World pomocí rozhraní [Express](http://expressjs.com/).

1. Otevřete svůj oblíbený terminál, jako je třeba příkazový řádek Node.js.
2. Přejděte do adresáře, do kterého chcete novou aplikaci uložit.
3. Pomocí generátoru Express vygenerujte novou aplikaci s názvem **todo**.

   ```bash
   express todo
   ```
4. Otevřete nový adresář **todo** a nainstalujte závislosti.

   ```bash
   cd todo
   npm install
   ```
5. Spusťte novou aplikaci.

   ```bash
   npm start
   ```

6. Pokud si chcete zobrazit novou aplikaci, přejděte v prohlížeči na adresu [http://localhost:3000](http://localhost:3000).
   
    ![Výuka Node.js – snímek obrazovky aplikace Hello World v okně prohlížeče](./media/sql-api-nodejs-application/cosmos-db-node-js-express.png)

 Zastavte aplikaci stisknutím kombinace kláves CTRL + C v okně terminálu a kliknutím na **y** ukončete dávkovou úlohu.

## <a name="_Toc395783179"></a>Krok 3: Instalace požadovaných modulů

Soubor **package.json** je jedním ze souborů vytvořených v kořenu projektu. Tento soubor obsahuje seznam dalších modulů, které aplikace Node.js vyžaduje. Později, až budete tuto aplikaci nasazovat na Azure Websites, se tento soubor použije k vyhodnocení, které moduly musí být v Azure pro podporu vaší aplikace nainstalovány. Pro tento kurz je potřeba nainstalovat další dva balíčky.

1. Otevřete terminál a přes npm nainstalujte modul **async**.

   ```bash
   npm install async --save
   ```

2. Nainstalujte přes npm modul **@azure/cosmos**. 

   ```bash
   npm install @azure/cosmos
   ```

## <a name="_Toc395783180"></a>Krok 4: Využití služby Azure Cosmos DB v aplikaci Node.js
Dokončili jste počáteční nastavení a konfiguraci a teď napíšete kód, který potřebuje aplikace seznamu úkolů ke komunikaci se službou Azure Cosmos DB.

### <a name="create-the-model"></a>Vytvoření modelu
1. V kořenovém adresáři projektu vytvořte nový adresář **models**.  

2. V adresáři **models** vytvořte nový soubor s názvem **taskDao.js**. Tento soubor obsahuje kód potřebný k vytvoření databáze a kontejneru a definuje metody pro čtení, aktualizaci, vytváření a vyhledávání úkolů ve službě Azure Cosmos DB. 

3. Do souboru **taskDao.js** zkopírujte následující kód.

   ```nodejs
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
   
   ```nodejs
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
   
   ```nodejs
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

3. V souboru **config.js** aktualizujte hodnoty HOST a AUTH_KEY s použitím hodnot, které získáte na stránce Klíče účtu služby Azure Cosmos DB na webu [Microsoft Azure Portal](https://portal.azure.com). 

4. Uložte a zavřete soubor **config.js**.

### <a name="modify-appjs"></a>Úprava souboru app.js
1. V adresáři projektu otevřete soubor **app.js**. Tento soubor byl vytvořen již dříve, při vytváření webové aplikace Express.  

2. Do souboru **app.js** přidejte následující kód. Tento kód definuje konfigurační soubor, který se má použít, a načte z něj do proměnných hodnoty, které brzy využijeme. 
   
   ```nodejs
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
       console.error("Shutting down because there was an error settinig up the database.");
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

3. Nakonec soubor **app.js** uložte a zavřete a jsme téměř hotovi.

## <a name="_Toc395783181"></a>Krok 5: Vytvoření uživatelského rozhraní
Nyní se zaměřme na vytvoření uživatelského rozhraní, aby uživatelé mohli s aplikací pracovat. Aplikace Express, kterou jsme vytvořili, používá jako zobrazovací modul **Jade**. Další informace o Jade najdete na stránce [http://jade-lang.com/](http://jade-lang.com/).

1. Soubor **layout.jade** v adresáři **views** slouží jako globální šablona pro ostatní soubory **.jade**. V tomto kroku jej upravíte tak, aby používal [Twitter Bootstrap](https://github.com/twbs/bootstrap). To je sada nástrojů, které usnadňují návrh pěkných webů.  

2. Otevřete soubor **layout.jade** umístěný v adresáři **views** a nahraďte jeho obsah tímto:

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

    Tímto modul **Jade** dostává instrukci vykreslovat pro naši aplikaci některý kód HTML a vytvoří **block** s názvem **content**, do kterého můžeme zadat rozložení stránek s obsahem.

    Uložte a zavřete tento soubor **layout.jade**.

3. Nyní otevřete soubor **index.jade**, který představuje zobrazení používané v naší aplikaci a nahraďte jeho obsah následujícím kódem:

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

Tento kód rozšiřuje rozložení a poskytuje obsah pro zástupný symbol **content**, který jsme viděli v souboru **layout.jade** výše.
   
V tomto rozložení jsme vytvořili dva formuláře HTML.

První formulář obsahuje tabulku našich dat a tlačítko, které umožňuje aktualizovat položky odesláním informací do metody **/completeTask** našeho kontroleru.
    
Druhý formulář obsahuje dvě vstupní pole a tlačítko, které umožňuje vytvořit novou položku odesláním informací metodě kontroleru **/addtask**.

To by mělo být vše, co potřebujeme ke zprovoznění aplikace.

## <a name="_Toc395783181"></a>Krok 6: Místní spuštění aplikace
1. Pokud chcete aplikaci otestovat na místním počítači, spusťte aplikaci spuštěním příkazu `npm start` v terminálu a pak aktualizujte stránku prohlížeče [http://localhost:3000](http://localhost:3000). Stránka by teď měla vypadat jako na obrázku níže:
   
    ![Snímek obrazovky aplikace Seznam úkolů v okně prohlížeče](./media/sql-api-nodejs-application/cosmos-db-node-js-localhost.png)

    > [!TIP]
    > Pokud se vám zobrazí chyba týkající se odsazení v souboru layout.jade nebo index.jade, ujistěte se, že jsou první dva řádky v obou souborech zarovnané vlevo a nejsou tam žádné mezery. Pokud jsou před prvními dvěma řádky mezery, odeberte je, oba soubory uložte a pak aktualizujte okno prohlížeče. 

2. Pro zadání nového úkolu použijte pole Položka, Název položky a Kategorie a pak klikněte na **Přidat položku**. Tak se ve službě Azure Cosmos DB vytvoří dokument s těmito vlastnostmi. 
3. Stránka by se měla aktualizovat, aby se v seznamu úkolů zobrazila nově vytvořená položka.
   
    ![Snímek obrazovky aplikace s novou položkou v seznamu úkolů](./media/sql-api-nodejs-application/cosmos-db-node-js-added-task.png)
4. Pokud chcete dokončit úkol, stačí zaškrtnout políčko ve sloupci Complete (Dokončeno) a kliknout na **Update tasks** (Aktualizovat úkoly). Takto se vámi vytvořený dokument aktualizuje a odebere ze zobrazení.

5. Pokud chcete aplikaci zastavit, stiskněte kombinace kláves CTRL + C v okně terminálu a pak ukončete dávkovou úlohu kliknutím na **y**.

## <a name="_Toc395783182"></a>Krok 7: Nasazení vývojového projektu aplikace na Azure Websites
1. Pokud jste tak ještě neučinili, povolte úložiště Git pro Weby Azure. Pokyny, jak máte postupovat, najdete v tématu [Místní nasazení přes Git do Azure App Service](../app-service/app-service-deploy-local-git.md).
2. Přidejte svůj web Azure jako vzdálené úložiště Git.
   
        git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
3. Nasazení provedete odesláním na vzdálené úložiště.
   
        git push azure master
4. Za několik sekund Git dokončí publikování webové aplikace a spustí prohlížeč, kde se můžete podívat, jak vaše práce běží v Azure!

    Blahopřejeme! Právě jste vytvořili svou první webovou aplikaci Node.js Express využívající službu Azure Cosmos DB a publikovali jste ji ve službě Azure Websites.

    Pokud chcete stáhnout nebo odkazovat na úplnou aplikaci referencí tohoto kurzu, můžete ji stáhnout z [GitHubu][GitHub].

## <a name="_Toc395637775"></a>Další kroky

* Chcete testovat škálování a výkon pomocí služby Azure Cosmos DB? Viz [Testování výkonu a škálování pomocí služby Azure Cosmos DB](performance-testing.md).
* Zjistěte, jak [monitorovat účet služby Azure Cosmos DB](monitor-accounts.md).
* Spouštějte dotazy proti ukázkovým datovým sadám v [Query Playground](https://www.documentdb.com/sql/demo).
* Prozkoumejte [dokumentaci ke službě Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/).

[Node.js]: http://nodejs.org/
[Git]: http://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-todo-app

