---
title: Sestavení webové aplikace Node.js pro Azure Cosmos DB | Microsoft Docs
description: Tento kurz Node.js popisuje jak používat Microsoft Azure Cosmos databázi k ukládání a přístup k datům z webové aplikace Node.js Express hostované na webech Azure.
keywords: Vývoj aplikací, databázový kurz výuka node.js, kurz k node.js
services: cosmos-db
documentationcenter: nodejs
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: 9da9e63b-e76a-434e-96dd-195ce2699ef3
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 03/23/2018
ms.author: mimig
ms.openlocfilehash: ad6e335c562e52d7e2336dd1f29e5c159fe46589
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="_Toc395783175"></a>Sestavení webové aplikace Node.js využívající službu Azure Cosmos DB
> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Java](sql-api-java-application.md)
> * [Python](sql-api-python-application.md)
> 
> 

Tento kurz Node.js popisuje, jak používat Azure Cosmos DB a rozhraní SQL API k uložení a přístup k datům z aplikace Node.js Express hostované na webech Azure. Vytvoříte jednoduchou webovou aplikaci pro správu úkolů, aplikaci seznamu úkolů, která umožní vytvářet, získávat a dokončovat úkoly. Úkoly se ve službě Azure Cosmos DB ukládají jako dokumenty JSON. Tento kurz vás provede procesem vytvoření a nasazení aplikace a vysvětlí, co se v každé části děje.

![Snímek obrazovky aplikace pro seznam úkolů vytvořené v tomto kurzu k Node.js](./media/sql-api-nodejs-application/cosmos-db-node-js-mytodo.png)

Nemáte čas absolvovat celý tento kurz a chcete jen získat celé řešení? To není problém, celé ukázkové řešení si můžete stáhnout z [GitHubu][GitHub]. Pokud potřebujete pokyny, jak aplikaci spustit, stačí si přečíst soubor [Readme](https://github.com/Azure-Samples/documentdb-node-todo-app/blob/master/README.md).

## <a name="_Toc395783176"></a>Požadavky
> [!TIP]
> V tomto kurzu Node.js se předpokládá, že již máte zkušenosti s používáním Node.js a Webů Azure.
> 
> 

Než budete postupovat podle pokynů tohoto článku, měli byste se ujistit, že máte následující:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js][Node.js] verze 0.10.29 nebo vyšší Doporučujeme, abyste Node.js 6.10 se opakuje nebo vyšší.
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
   
        express todo
4. Otevřete nový adresář **todo** a nainstalujte závislosti.
   
        cd todo
        npm install
5. Spusťte novou aplikaci.
   
        npm start
6. Je-li zobrazit novou aplikaci, přejděte v prohlížeči na [ http://localhost:3000 ](http://localhost:3000).
   
    ![Výuka Node.js – snímek obrazovky aplikace Hello World v okně prohlížeče](./media/sql-api-nodejs-application/cosmos-db-node-js-express.png)

    Potom zastavte aplikaci, stiskněte kombinaci kláves CTRL + C v okno terminálu a klikněte na počítačích Windows **y** ukončit dávkovou úlohu.

## <a name="_Toc395783179"></a>Krok 3: Instalace dalších modulů
Soubor **package.json** je jedním ze souborů vytvořených v kořenu projektu. Tento soubor obsahuje seznam dalších modulů, které aplikace Node.js vyžaduje. Později při nasazení této aplikace na weby Azure, tento soubor se používá k určení, které moduly musí být nainstalovaný na Azure pro podporu vaší aplikace. Pro tento kurz je ještě nutné nainstalovat další dva balíčky.

1. Po návratu na terminál nainstalujte pomocí npm modul **async**.
   
        npm install async --save
2. Přes npm nainstalujte modul **documentdb**. To je modul, kde se stane všechny magic Azure Cosmos DB.
   
        npm install documentdb --save

## <a name="_Toc395783180"></a>Krok 4: Využití služby Azure Cosmos DB v aplikaci Node.js
Tím je hotovo veškeré počáteční nastavování a konfigurace – nyní se budeme zabývat tím, proč tu jsme, tedy psaním kódu s využitím služby Azure Cosmos DB.

### <a name="create-the-model"></a>Vytvoření modelu
1. V adresáři projektu vytvořte nový adresář s názvem **models** ve stejném adresáři jako soubor package.json.
2. V **modely** adresáře, vytvořte nový soubor s názvem **úloh model.js**. Tento soubor bude obsahovat model pro úkoly vytvořené v aplikaci.
3. Ve stejném **modely** adresáře, vytvořte další nový soubor s názvem **cosmosdb manager.js**. V tomto souboru bude obsažen užitečný, opakovaně použitelný kód, který budeme používat v celé aplikaci. 
4. Zkopírujte následující kód do **cosmosdb manager.js**
    ```nodejs
    let DocumentDBClient = require('documentdb').DocumentClient;

    module.exports = {
    getOrCreateDatabase: (client, databaseId, callback) => {
        let querySpec = {
        query: 'SELECT * FROM root r WHERE r.id = @id',
        parameters: [{ name: '@id', value: databaseId }]
        };

        client.queryDatabases(querySpec).toArray((err, results) => {
        if (err) {
            callback(err);
        } else {
            if (results.length === 0) {
            let databaseSpec = { id: databaseId };
            client.createDatabase(databaseSpec, (err, created) => {
                callback(null, created);
            });
            } else {
            callback(null, results[0]);
            }
        }
        });
    },

    getOrCreateCollection: (client, databaseLink, collectionId, callback) => {
        let querySpec = {
        query: 'SELECT * FROM root r WHERE r.id=@id',
        parameters: [{ name: '@id', value: collectionId }]
        };

        client.queryCollections(databaseLink, querySpec).toArray((err, results) => {
        if (err) {
            callback(err);
        } else {
            if (results.length === 0) {
            let collectionSpec = { id: collectionId };
            client.createCollection(databaseLink, collectionSpec, (err, created) => {
                callback(null, created);
            });
            } else {
            callback(null, results[0]);
            }
        }
        });
    }
    };
    ```
5. Uložte a zavřete **cosmosdb manager.js** souboru.
6. Na začátku **úloh model.js** soubor, přidejte následující kód tak, aby odkazovaly **DocumentDBClient** a **cosmosdb manager.js** jsme vytvořili výše: 

    ```nodejs
    let DocumentDBClient = require('documentdb').DocumentClient;
    let docdbUtils = require('./cosmosdb-manager.js');
    ```
7. Dále přidáte kód, který definuje export objektu Task. Ten má na starosti inicializaci objektu Task a nastavení databáze a kolekce dokumentů, které budeme používat.  

    ```nodejs
    function TaskModel(documentDBClient, databaseId, collectionId) {
      this.client = documentDBClient;
      this.databaseId = databaseId;
      this.collectionId = collectionId;
   
      this.database = null;
      this.collection = null;
    }
   
    module.exports = TaskModel;
    ```
8. Dále přidejte následující kód, který definuje další metody objektu Task. Ty umožňují pracovat s daty uloženými ve službě Azure Cosmos.

    ```nodejs
    let DocumentDBClient = require('documentdb').DocumentClient;
    let docdbUtils = require('./cosmosdb-manager');

    function TaskModel(documentDBClient, databaseId, collectionId) {
    this.client = documentDBClient;
    this.databaseId = databaseId;
    this.collectionId = collectionId;

    this.database = null;
    this.collection = null;
    }

    TaskModel.prototype = {
    init: function(callback) {
        let self = this;

        docdbUtils.getOrCreateDatabase(self.client, self.databaseId, function(err, db) {
        if (err) {
            callback(err);
        } else {
            self.database = db;
            docdbUtils.getOrCreateCollection(self.client, self.database._self, self.collectionId, function(err, coll) {
            if (err) {
                callback(err);
            } else {
                self.collection = coll;
            }
            });
        }
        });
    },

    find: function(querySpec, callback) {
        let self = this;

        self.client.queryDocuments(self.collection._self, querySpec).toArray(function(err, results) {
        if (err) {
            callback(err);
        } else {
            callback(null, results);
        }
        });
    },

    addItem: function(item, callback) {
        let self = this;

        item.date = Date.now();
        item.completed = false;

        self.client.createDocument(self.collection._self, item, function(err, doc) {
        if (err) {
            callback(err);
        } else {
            callback(null, doc);
        }
        });
    },

    updateItem: function(itemId, callback) {
        let self = this;

        self.getItem(itemId, function(err, doc) {
        if (err) {
            callback(err);
        } else {
            doc.completed = true;

            self.client.replaceDocument(doc._self, doc, function(err, replaced) {
            if (err) {
                callback(err);
            } else {
                callback(null, replaced);
            }
            });
        }
        });
    },

    getItem: function(itemId, callback) {
        let self = this;
        let querySpec = {
        query: 'SELECT * FROM root r WHERE r.id = @id',
        parameters: [{ name: '@id', value: itemId }]
        };

        self.client.queryDocuments(self.collection._self, querySpec).toArray(function(err, results) {
        if (err) {
            callback(err);
        } else {
            callback(null, results[0]);
        }
        });
    }
    };

    module.exports = TaskModel;
    ```
9. Uložte a zavřete **úloh model.js** souboru. 

### <a name="create-the-controller"></a>Vytvoření kontroleru
1. V adresáři **routes** projektu vytvořte nový soubor s názvem **tasklist.js**. 
2. Do souboru **tasklist.js** přidejte následující kód: Tento kód načte moduly DocumentDBClient a async, které budou používány v souboru **tasklist.js**. Také v něm je definována funkce **TaskList**, která přijímá instanci objektu **Task** definovaného dříve:
   
    ```nodejs
    let DocumentDBClient = require('documentdb').DocumentClient;
    let async = require('async');

    function TaskList(taskModel) {
    this.taskModel = taskModel;
    }

    module.exports = TaskList;
    ```
3. Dále do souboru **tasklist.js** přidejte metody **showTasks, addTask** a **completeTask**:
   
   ```nodejs
    TaskList.prototype = {
    showTasks: function(req, res) {
        let self = this;

        let querySpec = {
        query: 'SELECT * FROM root r WHERE r.completed=@completed',
        parameters: [
            {
            name: '@completed',
            value: false
            }
        ]
        };

        self.taskModel.find(querySpec, function(err, items) {
        if (err) {
            throw err;
        }

        res.render('index', {
            title: 'My ToDo List ',
            tasks: items
        });
        });
    },

    addTask: function(req, res) {
        let self = this;
        let item = req.body;

        self.taskModel.addItem(item, function(err) {
        if (err) {
            throw err;
        }

        res.redirect('/');
        });
    },

    completeTask: function(req, res) {
        let self = this;
        let completedTasks = Object.keys(req.body);

        async.forEach(
        completedTasks,
        function taskIterator(completedTask, callback) {
            self.taskModel.updateItem(completedTask, function(err) {
            if (err) {
                callback(err);
            } else {
                callback(null);
            }
            });
        },
        function goHome(err) {
            if (err) {
            throw err;
            } else {
            res.redirect('/');
            }
        }
        );
    }
    };
    ```        
4. Uložte a zavřete soubor **tasklist.js**.

### <a name="add-configjs"></a>Přidání souboru config.js
1. V adresáři projektu vytvořte nový soubor s názvem **config.js**.
2. Do souboru **config.js** přidejte následující: Tento kód definuje nastavení konfigurace a hodnoty, které aplikace potřebuje.
   
    ```nodejs
    let config = {}
   
    config.host = process.env.HOST || "[the URI value from the Azure Cosmos DB Keys page on http://portal.azure.com]";
    config.authKey = process.env.AUTH_KEY || "[the PRIMARY KEY value from the Azure Cosmos DB Keys page on http://portal.azure.com]";
    config.databaseId = "ToDoList";
    config.collectionId = "Items";
   
    module.exports = config;
    ```
3. V **config.js** souboru, aktualizujte hodnoty HOST a AUTH_KEY na hodnoty zjištěné v na stránce klíčů účtu Azure Cosmos DB [portálu Microsoft Azure](https://portal.azure.com).
4. Uložte a zavřete soubor **config.js**.

### <a name="modify-appjs"></a>Úprava souboru app.js
1. V adresáři projektu otevřete soubor **app.js**. Tento soubor byl vytvořen již dříve, při vytváření webové aplikace Express.
2. Přidejte následující kód do horní části **app.js**:
   
    ```nodejs
    var DocumentDBClient = require('documentdb').DocumentClient;
    var config = require('./config');
    var TaskList = require('./routes/tasklist');
    var TaskModel = require('./models/taskModel');
    ```
3. Tento kód definuje konfigurační soubor, který se má použít, a načte z něj do proměnných hodnoty, které brzy využijeme.
4. Nahraďte v souboru **app.js** následující dva řádky:
   
    ```nodejs
    app.use('/', index);
    app.use('/users', users); 
    ```
   
    následujícím fragmentem kódu:
   
    ```nodejs
    let docDbClient = new DocumentDBClient(config.host, {
        masterKey: config.authKey
    });
    let taskModel = new TaskModel(docDbClient, config.databaseId, config.collectionId);
    let taskList = new TaskList(taskModel);
    taskModel.init();
   
    app.get('/', taskList.showTasks.bind(taskList));
    app.post('/addtask', taskList.addTask.bind(taskList));
    app.post('/completetask', taskList.completeTask.bind(taskList));
    app.set('view engine', 'jade');
    ```
5. Tyto řádky definují novou instanci třídy Naše **TaskModel** objektu s novým připojením k databázi Cosmos Azure (pomocí hodnot přečtených ze **config.js**), inicializují objekt úkolů a svážou akce formuláře s metody pro naše **TaskList** řadiče. 
6. Nakonec soubor **app.js** uložte a zavřete a jsme téměř hotovi.

## <a name="_Toc395783181"></a>Krok 5: Vytvoření uživatelského rozhraní
Nyní se zaměřme na vytvoření uživatelského rozhraní, aby uživatelé mohli s aplikací pracovat. Aplikace Express, kterou jsme vytvořili, používá jako zobrazovací modul **Jade**. Další informace o Jade naleznete [ http://jade-lang.com/ ](http://jade-lang.com/).

1. Soubor **layout.jade** v adresáři **views** slouží jako globální šablona pro ostatní soubory **.jade**. V tomto kroku jej upravíte tak, aby používal [Twitter Bootstrap](https://github.com/twbs/bootstrap). To je sada nástrojů, které usnadňují návrh pěkných webů. 
2. Otevřete soubor **layout.jade** umístěný v adresáři **views** a nahraďte jeho obsah tímto:

    ```
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
                       input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
             button.btn.btn-primary(type="submit") Update tasks
           hr
           form.well(action="/addtask", method="post")
             .form-group
               label(for="name") Item Name:
               input.form-control(name="name", type="textbox")
             .form-group
               label(for="category") Item Category:
               input.form-control(name="category", type="textbox")
             br
             button.btn(type="submit") Add item
   

Tento kód rozšiřuje rozložení a poskytuje obsah pro zástupný symbol **content**, který jsme viděli v souboru **layout.jade** výše.
   
V tomto rozložení jsme vytvořili dva formuláře HTML.

První formulář obsahuje tabulku našich dat a tlačítko, které umožňuje aktualizovat položky odesláním informací metodě kontroleru **/completetask**.
    
Druhý formulář obsahuje dvě vstupní pole a tlačítko, které umožňuje vytvořit novou položku odesláním informací metodě kontroleru **/addtask**.

To by mělo být vše, co potřebujeme ke zprovoznění aplikace.

## <a name="_Toc395783181"></a>Krok 6: Místní spuštění aplikace
1. Otestujte aplikace na místním počítači spustit `npm start` v terminálu spuštění vaší aplikace, pak aktualizujte vaše [ http://localhost:3000 ](http://localhost:3000) stránku prohlížeče. Stránka by teď měla vypadat jako na obrázku níže:
   
    ![Snímek obrazovky aplikace Seznam úkolů v okně prohlížeče](./media/sql-api-nodejs-application/cosmos-db-node-js-localhost.png)

    > [!TIP]
    > Pokud se vám zobrazí chyba týkající se odsazení v souboru layout.jade nebo index.jade, ujistěte se, že jsou první dva řádky v obou souborech zarovnané vlevo a nejsou tam žádné mezery. Pokud jsou před prvními dvěma řádky mezery, odeberte je, oba soubory uložte a pak aktualizujte okno prohlížeče. 

2. Pro zadání nového úkolu použijte pole Položka, Název položky a Kategorie a pak klikněte na **Přidat položku**. Tak se ve službě Azure Cosmos DB vytvoří dokument s těmito vlastnostmi. 
3. Stránka by se měla aktualizovat, aby se v seznamu úkolů zobrazila nově vytvořená položka.
   
    ![Snímek obrazovky aplikace s novou položkou v seznamu úkolů](./media/sql-api-nodejs-application/cosmos-db-node-js-added-task.png)
4. Pokud chcete dokončit úkol, stačí zaškrtnout políčko ve sloupci Complete (Dokončeno) a kliknout na **Update tasks** (Aktualizovat úkoly). Tím se aktualizuje dokumentu jste již vytvořili a odstraní ji ze zobrazení.

5. Pokud chcete aplikaci zastavit, stiskněte kombinace kláves CTRL + C v okně terminálu a pak ukončete dávkovou úlohu kliknutím na **y**.

## <a name="_Toc395783182"></a>Krok 7: Nasazení vývojového projektu aplikace na Azure Websites
1. Pokud jste tak ještě neučinili, povolte úložiště Git pro Weby Azure. Pokyny, jak máte postupovat, najdete v tématu [Místní nasazení přes Git do Azure App Service](../app-service/app-service-deploy-local-git.md).
2. Přidejte svůj web Azure jako vzdálené úložiště Git.
   
        git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
3. Nasazení provedete odesláním na vzdálené úložiště.
   
        git push azure master
4. Během pár sekund bude git dokončí publikování webové aplikace a spustí prohlížeč, kde uvidíte vaše handiwork běžící v Azure!

    Blahopřejeme! Právě jste vytvořili svou první webovou aplikaci Node.js Express využívající službu Azure Cosmos DB a publikovali jste ji ve službě Azure Websites.

    Pokud chcete stáhnout nebo odkazovat na úplnou aplikaci referencí tohoto kurzu, můžete ji stáhnout z [GitHubu][GitHub].

## <a name="_Toc395637775"></a>Další kroky

* Chcete testovat škálování a výkon pomocí služby Azure Cosmos DB? Viz [Testování výkonu a škálování pomocí služby Azure Cosmos DB](performance-testing.md).
* Zjistěte, jak [monitorovat účet služby Azure Cosmos DB](monitor-accounts.md).
* Spouštějte dotazy proti ukázkovým datovým sadám v [Query Playground](https://www.documentdb.com/sql/demo).
* Prozkoumejte [dokumentaci ke službě Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/).

[Node.js]: http://nodejs.org/
[Git]: http://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/documentdb-node-todo-app

