---
title: 'Azure Table storage: vytvoření webové aplikace Node.js | Microsoft Docs'
description: Kurz, který je založený na webovou aplikaci s Express kurzu přidáním služby Azure Storage a modulu Azure.
services: cosmos-db
documentationcenter: nodejs
author: mimig1
manager: jhubbard
editor: tysonn
ms.assetid: e90959a2-4cb2-4b19-9bfb-aede15b18b1c
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 03/29/2018
ms.author: mimig
ms.openlocfilehash: 3708c4a1bae93682f81d8aad0f3649f6b2381ff5
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="azure-table-storage-nodejs-web-application"></a>Azure Table storage: webové aplikace Node.js
[!INCLUDE [storage-table-cosmos-db-tip-include](../../includes/storage-table-cosmos-db-tip-include.md)]

## <a name="overview"></a>Přehled
V tomto kurzu aplikaci jste vytvořili v [webové aplikace Node.js pomocí Express] kurzu je rozšířeno pomocí knihovny klienta Microsoft Azure pro platformu Node.js pro práci se službami pro správu dat. Vytvoření aplikace založené na webu – seznam úloh, kterou můžete nasadit do Azure můžete rozšířit vaše aplikace. Seznam úloh umožňuje uživateli načíst úlohy, přidat nové úkoly a úkoly označit jako dokončená.

Položky úkolů jsou uloženy v Azure Storage nebo Azure Cosmos DB. Azure Storage a Azure Cosmos DB poskytují nestrukturovaných dat úložiště, které je odolné proti chybám a vysoce dostupné. Azure Storage a Azure Cosmos DB obsahují několik datových struktur, kde můžete ukládat a přistupovat k datům. Můžete použít Azure Cosmos DB služby z rozhraní API zahrnutý v sadě Azure SDK pro Node.js nebo přes rozhraní REST API a služby úložiště. Další informace najdete v tématu [ukládání a přístup k datům v Azure].

V tomto kurzu se předpokládá, že jste dokončili [webové aplikace Node.js] a [Node.js s Express][webové aplikace Node.js pomocí Express] kurzy.

Obsahuje následující informace:

* Jak pracovat s modulem Jade šablony
* Jak pracovat s Azure Data Management services

Na následujícím snímku obrazovky je vidět hotová aplikace:

![Dokončené webové stránky v aplikaci internet explorer](./media/table-storage-cloud-service-nodejs/getting-started-1.png)

## <a name="setting-storage-credentials-in-webconfig"></a>Nastavení přihlašovacích údajů úložiště v souboru Web.Config.
Musíte zadat úložiště pověření pro přístup k Azure Storage nebo Azure Cosmos DB. To se provádí s využitím nastavení web.config aplikace.
Nastavení web.config jsou předány jako proměnné prostředí do uzlu, které jsou pak přečte sadu Azure SDK.

> [!NOTE]
> Přihlašovací údaje úložiště používají jenom při nasazení aplikace do Azure. Při spuštění v emulátoru, aplikace použije emulátor úložiště.
>
>

Proveďte následující kroky k načtení údaje k účtu úložiště a přidat je do souboru web.config nastavení:

1. Pokud již není otevřený, otevřete prostředí Azure PowerShell z **spustit** nabídky rozšířením **všechny programy, Azure**, klikněte pravým tlačítkem na **prostředí Azure PowerShell**a potom vyberte **spustit jako správce**.
2. Změňte adresář na složku obsahující aplikaci. Například C:\\uzlu\\tasklist\\WebRole1.
3. V okně Azure Powershell zadejte následující rutiny můžete načíst informace o účtu úložiště:

    ```powershell
    PS C:\node\tasklist\WebRole1> Get-AzureStorageAccount
    ```

   Předchozí rutina načte seznam účtů úložiště a účtu klíče přidruženého k vaší hostované služby.

   > [!NOTE]
   > Vzhledem k tomu, že sadu Azure SDK vytvoří účet úložiště při nasazení služby, by měl z nasazení aplikace v předchozí příručkách již existuje účet úložiště.
   >
   >
4. Otevřete **ServiceDefinition.csdef** souboru, který obsahuje nastavení prostředí, které se používají při nasazení aplikace do Azure:

    ```powershell
    PS C:\node\tasklist> notepad ServiceDefinition.csdef
    ```

5. Vložte následující blok v části **prostředí** elementu, nahraďte {účet úložiště} a {přístupový klíč k ÚLOŽIŠTI} s názvem účtu a primární klíč pro účet úložiště, kterou chcete použít pro nasazení:

  <Variable name="AZURE_STORAGE_ACCOUNT" value="{STORAGE ACCOUNT}" />
  <Variable name="AZURE_STORAGE_ACCESS_KEY" value="{STORAGE ACCESS KEY}" />

   ![Obsah souboru web.cloud.config](./media/table-storage-cloud-service-nodejs/node37.png)

6. Soubor uložte a zavřete poznámkový blok.

### <a name="install-additional-modules"></a>Instalace dalších modulů
1. Použijte následující příkaz k instalaci [azure,] [uzlu uuid], [nconf] a [asynchronní] moduly místně i tak, aby mohly uložit položku **package.json** souboru:

  ```powershell
  PS C:\node\tasklist\WebRole1> npm install azure-storage node-uuid async nconf --save
  ```

  Výstup tohoto příkazu by měl vypadat přibližně takto:

  ```
  node-uuid@1.4.1 node_modules\node-uuid

  nconf@0.6.9 node_modules\nconf
  ├── ini@1.1.0
  ├── async@0.2.9
  └── optimist@0.6.0 (wordwrap@0.0.2, minimist@0.0.8)

  azure-storage@0.1.0 node_modules\azure-storage
  ├── extend@1.2.1
  ├── xmlbuilder@0.4.3
  ├── mime@1.2.11
  ├── underscore@1.4.4
  ├── validator@3.1.0
  ├── node-uuid@1.4.1
  ├── xml2js@0.2.7 (sax@0.5.2)
  └── request@2.27.0 (json-stringify-safe@5.0.0, tunnel-agent@0.3.0, aws-sign@0.3.0, forever-agent@0.5.2, qs@0.6.6, oauth-sign@0.3.0, cookie-jar@0.3.0, hawk@1.0.0, form-data@0.1.3, http-signature@0.10.0)
  ```

## <a name="using-the-table-service-in-a-node-application"></a>Pomocí služby Table v aplikaci node
V této části základní aplikace vytvořené **express** příkaz je rozšířit přidáním **task.js** souboru, který obsahuje model pro úkoly. Upravit existující **app.js** souboru a vytvořte novou **tasklist.js** soubor, který používá model.

### <a name="create-the-model"></a>Vytvoření modelu
1. V **WebRole1** adresáře, vytvořte nový adresář s názvem **modely**.
2. V **modely** adresáře, vytvořte nový soubor s názvem **task.js**. Tento soubor obsahuje model pro úkoly vytvořené v aplikaci.
3. Na začátku **task.js** soubor, přidejte následující kód k odkazování požadované knihovny:

    ```nodejs
    var azure = require('azure-storage');
    var uuid = require('node-uuid');
    var entityGen = azure.TableUtilities.entityGenerator;
    ```

4. Dál přidejte kód, který definuje export objektu Task. Objekt úlohy je zodpovědná za připojení do tabulky.

    ```nodejs
    module.exports = Task;

    function Task(storageClient, tableName, partitionKey) {
      this.storageClient = storageClient;
      this.tableName = tableName;
      this.partitionKey = partitionKey;
      this.storageClient.createTableIfNotExists(tableName, function tableCreated(error) {
        if(error) {
          throw error;
        }
      });
    };
    ```

5. Dál přidejte následující kód, který definuje další metody objektu Task ty umožňují pracovat s daty uloženými v tabulce:

    ```nodejs
    Task.prototype = {
      find: function(query, callback) {
        self = this;
        self.storageClient.queryEntities(this.tablename, query, null, null, function entitiesQueried(error, result) {
          if(error) {
            callback(error);
          } else {
            callback(null, result.entries);
          }
        });
      },

      addItem: function(item, callback) {
        self = this;
        // use entityGenerator to set types
        // NOTE: RowKey must be a string type, even though
        // it contains a GUID in this example.
        var itemDescriptor = {
          PartitionKey: entityGen.String(self.partitionKey),
          RowKey: entityGen.String(uuid()),
          name: entityGen.String(item.name),
          category: entityGen.String(item.category),
          completed: entityGen.Boolean(false)
        };

        self.storageClient.insertEntity(self.tableName, itemDescriptor, function entityInserted(error) {
          if(error){
            callback(error);
          }
          callback(null);
        });
      },

      updateItem: function(rKey, callback) {
        self = this;
        self.storageClient.retrieveEntity(self.tableName, self.partitionKey, rKey, function entityQueried(error, entity) {
          if(error) {
            callback(error);
          }
          entity.completed._ = true;
          self.storageClient.replaceEntity(self.tableName, entity, function entityUpdated(error) {
            if(error) {
              callback(error);
            }
            callback(null);
          });
        });
      }
    }
    ```

6. Uložte a zavřete **task.js** souboru.

### <a name="create-the-controller"></a>Vytvoření kontroleru
1. V **WebRole1 a směruje** adresáře, vytvořte nový soubor s názvem **tasklist.js** a otevřete v textovém editoru.
2. Do souboru **tasklist.js** přidejte následující kód: Tento kód načte moduly azure a async, které jsou používány **tasklist.js** a definuje **TaskList** funkce, která se předá instance **úloh** jsme objektu definovaného dříve:

    ```nodejs
    var azure = require('azure-storage');
    var async = require('async');

    module.exports = TaskList;

    function TaskList(task) {
      this.task = task;
    }
    ```

3. Pokračovat v přidání do **tasklist.js** souboru přidáním metody použité k **showTasks**, **addTask**, a **Completetask**:

    ```nodejs
    TaskList.prototype = {
      showTasks: function(req, res) {
        self = this;
        var query = new azure.TableQuery()
          .where('completed eq ?', false);
        self.task.find(query, function itemsFound(error, items) {
          res.render('index',{title: 'My ToDo List ', tasks: items});
        });
      },

      addTask: function(req,res) {
        var self = this
        var item = {
            name: req.body.name, 
            category: req.body.category
        };
        self.task.addItem(item, function itemAdded(error) {
          if(error) {
            throw error;
          }
          res.redirect('/');
        });
      },

      completeTask: function(req,res) {
        var self = this;
        var completedTasks = Object.keys(req.body);
        async.forEach(completedTasks, function taskIterator(completedTask, callback) {
          self.task.updateItem(completedTask, function itemsUpdated(error) {
            if(error){
              callback(error);
            } else {
              callback(null);
            }
          });
        }, function goHome(error){
          if(error) {
            throw error;
          } else {
            res.redirect('/');
          }
        });
      }
    }
    ```

4. Uložit **tasklist.js** souboru.

### <a name="modify-appjs"></a>Úprava souboru app.js
1. V **WebRole1** adresáře, otevřete **app.js** soubor v textovém editoru.
2. Na začátek souboru přidejte následující příkaz pro načtení modulu azure a nastavte název a oddíl klíč tabulky:

    ```nodejs
    var azure = require('azure-storage');
    var tableName = 'tasks';
    var partitionKey = 'hometasks';
    ```

3. V souboru app.js posuňte se dolů, kde uvidíte následující řádek:

    ```nodejs
    app.use('/', routes);
    app.use('/users', users);
    ```

    Předchozí řádky nahraďte následujícím kódem. Tento kód inicializuje instanci <strong>úloh</strong> s připojením k účtu úložiště. <strong>Úloh</strong> je předán <strong>TaskList</strong>, který používá ke komunikaci se službou tabulky:

    ```nodejs
    var TaskList = require('./routes/tasklist');
    var Task = require('./models/task');
    var task = new Task(azure.createTableService(), tableName, partitionKey);
    var taskList = new TaskList(task);

    app.get('/', taskList.showTasks.bind(taskList));
    app.post('/addtask', taskList.addTask.bind(taskList));
    app.post('/completetask', taskList.completeTask.bind(taskList));
    ```

4. Uložit **app.js** souboru.

### <a name="modify-the-index-view"></a>Upravit zobrazení indexu
1. Přejděte do adresáře **zobrazení** adresář a otevřete **index.jade** soubor v textovém editoru.
2. Nahraďte obsah **index.jade** soubor s následujícím kódem. Tento kód definuje zobrazení pro zobrazení stávající úlohy a definuje formuláře pro přidání nové úlohy a označit stávajících jako dokončenou.

    ```
    extends layout

    block content
      h1= title
      br

      form(action="/completetask", method="post")
        table.table.table-striped.table-bordered
          tr
            td Name
            td Category
            td Date
            td Complete
          if tasks == []
            tr
              td
          else
            each task in tasks
              tr
                td #{task.name._}
                td #{task.category._}
                - var day   = task.Timestamp._.getDate();
                - var month = task.Timestamp._.getMonth() + 1;
                - var year  = task.Timestamp._.getFullYear();
                td #{month + "/" + day + "/" + year}
                td
                  input(type="checkbox", name="#{task.RowKey._}", value="#{!task.completed._}", checked=task.completed._)
        button.btn(type="submit") Update tasks
      hr
      form.well(action="/addtask", method="post")
        label Item Name:
        input(name="name", type="textbox")
        label Item Category:
        input(name="category", type="textbox")
        br
        button.btn(type="submit") Add item
    ```

3. Uložte a zavřete **index.jade** souboru.

### <a name="modify-the-global-layout"></a>Upravit globální rozložení
Soubor **layout.jade** v adresáři **views** slouží jako globální šablona pro ostatní soubory **.jade**. V tomto kroku změnit **layout.jade** soubor [Twitter Bootstrap](https://github.com/twbs/bootstrap), což je sada nástrojů, který usnadňuje návrh pěkných webů.

1. Stažení a extrakci souborů pro [Twitter Bootstrap](http://getbootstrap.com/). Kopírování **bootstrap.min.css** souboru z **bootstrap\\dist\\šablon stylů css** složku pro **veřejné\\předlohy se styly** adresář tasklist aplikace.
2. Z **zobrazení** složku, otevřete **layout.jade** soubor v textovém editoru a nahraďte jeho obsah následujícím kódem:
 
```jade
    doctype html
    html
      head
        title= title
        link(rel='stylesheet', href='/stylesheets/bootstrap.min.css')
        link(rel='stylesheet', href='/stylesheets/style.css')
      body.app
        nav.navbar.navbar-default
          div.navbar-header
            a.navbar-brand(href='/') My Tasks
        block content
```
3. Uložit **layout.jade** souboru.

### <a name="running-the-application-in-the-emulator"></a>Spuštění aplikace v emulátoru
Použijte následující příkaz a spusťte aplikaci v emulátoru.

```powershell
PS C:\node\tasklist\WebRole1> start-azureemulator -launch
```

Otevře se prohlížeč a zobrazí následující stránka:

![Webové stránkovaného fondu s názvem Moje seznam úkolů v tabulce obsahující pole pro přidání nové úlohy a úlohy.](./media/table-storage-cloud-service-nodejs/node44.png)

Formulář slouží k přidání položek nebo odebrat existující položky, můžete je označit jako dokončená.

## <a name="publishing-the-application-to-azure"></a>Publikování aplikace do Azure
V okně prostředí Windows PowerShell zavolejte následující rutiny můžete znovu nasadit vaší hostované služby Azure.

```powershell
PS C:\node\tasklist\WebRole1> Publish-AzureServiceProject -name myuniquename -location datacentername -launch
```

Nahraďte **myuniquename** s jedinečný název pro tuto aplikaci. Nahraďte **datovém centru** s názvem datové centrum Azure, jako například **západní USA**.

Po dokončení nasazení byste měli vidět odpověď podobná následující:

```
  PS C:\node\tasklist> publish-azureserviceproject -servicename tasklist -location "West US"
  WARNING: Publishing tasklist to Microsoft Azure. This may take several minutes...
  WARNING: 2:18:42 PM - Preparing runtime deployment for service 'tasklist'
  WARNING: 2:18:42 PM - Verifying storage account 'tasklist'...
  WARNING: 2:18:43 PM - Preparing deployment for tasklist with Subscription ID: 65a1016d-0f67-45d2-b838-b8f373d6d52e...
  WARNING: 2:19:01 PM - Connecting...
  WARNING: 2:19:02 PM - Uploading Package to storage service larrystore...
  WARNING: 2:19:40 PM - Upgrading...
  WARNING: 2:22:48 PM - Created Deployment ID: b7134ab29b1249ff84ada2bd157f296a.
  WARNING: 2:22:48 PM - Initializing...
  WARNING: 2:22:49 PM - Instance WebRole1_IN_0 of role WebRole1 is ready.
  WARNING: 2:22:50 PM - Created Website URL: http://tasklist.cloudapp.net/.
```

Zadáním **– spusťte** možnost v předchozí rutiny v prohlížeči se otevře a zobrazí vaši aplikaci běžící v Azure po dokončení publikování.

![Okno prohlížeče zobrazující stránku Moje seznamu úloh. Adresa URL značí, že stránky je teď hostované v Azure.](./media/table-storage-cloud-service-nodejs/getting-started-1.png)

## <a name="stopping-and-deleting-your-application"></a>Zastavení a odstranění vaší aplikace
Po nasazení aplikace, můžete ji zakázat, takže můžete vyhnout nákladům nebo sestavení a nasazení dalších aplikací v rámci bezplatné zkušební období.

Azure účtuje poplatky za instance webových rolí podle času (v hodinách), který na serveru spotřebují.
Čas serveru se spotřebovává, jakmile je vaše aplikace nasazena, i když jsou instance zrovna zastaveny a neběží.

Následující kroky ukazují, jak zastavení a odstranění vaší aplikace.

1. V okně prostředí Windows PowerShell pomocí následující rutiny ukončete nasazení služby vytvořené v předchozí části:

    ```powershell
    PS C:\node\tasklist\WebRole1> Stop-AzureService
    ```

   Zastavování služby může trvat několik minut. Až bude služba zastavená, obdržíte zprávu s oznámením, že se tak stalo.

2. Pokud chcete službu odstranit, zavolejte následující rutinu:

    ```powershell
    PS C:\node\tasklist\WebRole1> Remove-AzureService contosotasklist
    ```

   Po zobrazení výzvy zadejte **Y**, a službu tak odstraňte.

   Odstraňování služby může trvat několik minut. Po odstranění služby obdržíte zprávu s upozorněním, že se odstranila služba.

[webové aplikace Node.js pomocí Express]: http://azure.microsoft.com/develop/nodejs/tutorials/web-app-with-express/
[ukládání a přístup k datům v Azure]: https://docs.microsoft.com/azure/storage/
[webové aplikace Node.js]: http://azure.microsoft.com/develop/nodejs/tutorials/getting-started/


