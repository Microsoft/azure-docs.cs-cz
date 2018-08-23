---
title: Kurz vývoje aplikace Java využívající službu Azure Cosmos DB | Dokumentace Microsoftu
description: Tento kurz vývoje webové aplikace Java ukazuje, jak pomocí služby Azure Cosmos DB a rozhraní SQL API ukládat data a přistupovat k nim z aplikace Java hostované ve službě Azure Websites.
keywords: Application development, database tutorial, java application, java web application tutorial, azure, Microsoft azure
services: cosmos-db
author: tknandu
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 08/22/2017
ms.author: ramkris
ms.openlocfilehash: 87654132a6ad01ad5e100da09ca6426af46f94b3
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2018
ms.locfileid: "41919491"
---
# <a name="build-a-java-web-application-using-azure-cosmos-db-and-the-sql-api"></a>Sestavení webové aplikace Java využívající službu Azure Cosmos DB a rozhraní SQL API
> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Node.js – verze 2.0 Preview](sql-api-nodejs-application-preview.md)
> * [Java](sql-api-java-application.md)
> * [Python](sql-api-python-application.md)
> 
> 

Tento kurz vývoje webové aplikace Java ukazuje, jak pomocí služby [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) ukládat data a přistupovat k nim z aplikace Java hostované ve službě Azure App Service Web Apps. V tomto článku se dozvíte:

* Jak vytvořit základní aplikaci JSP (JavaServer Pages) v prostředí Eclipse.
* Jak pracovat se službou Azure Cosmos DB pomocí sady [Azure Cosmos DB Java SDK](https://github.com/Azure/azure-documentdb-java).

Tento kurz o aplikaci Java vám ukáže, jak vytvořit webovou aplikaci pro správu úkolů, která umožňuje vytvářet a získávat úkoly a označovat je jako dokončené, jak ilustruje následující obrázek. Každý z úkolů v seznamu se ve službě Azure Cosmos DB ukládá jako dokument JSON.

![Aplikace pro seznam úkolů v jazyce Java](./media/sql-api-java-application/image1.png)

> [!TIP]
> V tomto kurzu vývoje aplikace se předpokládá, že již máte zkušenosti s jazykem Java. Pokud je pro vás Java nebo některý z [požadovaných nástrojů](#Prerequisites) nový, doporučujeme stáhnout úplný ukázkový projekt [todo](https://github.com/Azure-Samples/documentdb-java-todo-app) z GitHubu a postupovat podle [pokynů na konci tohoto článku](#GetProject). Až jej budete mít sestavený, můžete se k tomuto článku vrátit, abyste kódu lépe porozuměli v kontextu projektu.  
> 
> 

## <a id="Prerequisites"></a>Předpoklady pro tento kurz webové aplikace Java
Než zahájíte tento kurz vývoje aplikace, musíte mít následující:

*  Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Java Development Kit (JDK) 7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Integrované vývojové prostředí Eclipse pro vývojáře v jazyce Java EE](http://www.eclipse.org/downloads/packages/release/luna/sr1/eclipse-ide-java-ee-developers)
* [Web Azure se zapnutou platformou Java Runtime Environment (např. Tomcat nebo Jetty)](../app-service/app-service-web-get-started-java.md)

Pokud tyto nástroje instalujete poprvé, coreservlets.com poskytuje k procesu instalace návod v části Quick Start článku [Tutorial: Installing TomCat7 and Using it with Eclipse](http://www.coreservlets.com/Apache-Tomcat-Tutorial/tomcat-7-with-eclipse.html) (Kurz: Instalace TomCat7 a jeho použití s Eclipse).

## <a id="CreateDB"></a>Krok 1: Vytvoření účtu služby Azure Cosmos DB
Začněme vytvořením účtu služby Azure Cosmos DB. Pokud již účet máte nebo pokud používáte pro účely tohoto kurzu emulátor služby Azure Cosmos DB, můžete přeskočit na [Krok 2: Vytvoření aplikace Java JSP](#CreateJSP).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

## <a id="CreateJSP"></a>Krok 2: Vytvoření aplikace Java JSP
Vytvoření aplikace JSP:

1. Nejdříve začneme vytvořením projektu Java. Spusťte Eclipse, klikněte na **File** (Soubor), pak na **New** (Nový) a nakonec na **Dynamic Web Project** (Dynamický webový projekt). Pokud se **Dynamic Web Project** v seznamu dostupných projektů nenachází, udělejte následující: klikněte na **File**, pak na **New**, dále na **Project** (Projekt), rozbalte **Web**, klikněte na **Dynamic Web Project** a nakonec na **Next** (Další). 
   
    ![Vývoj aplikace Java JSP](./media/sql-api-java-application/image10.png)
2. Zadejte název projektu do pole **Project name** (Název projektu), volitelně v rozevírací nabídce **Target Runtime** (Cílový modul runtime) vyberte hodnotu (např. Apache Tomcat v7.0) a klikněte na **Finish** (Dokončit). Pokud vyberete cílový modul runtime, budete moci spouštět projekt místně přes Eclipse.
3. V prostředí Eclipse v zobrazení Project Explorer (Průzkumník projektů) rozbalte projekt. Klikněte pravým tlačítkem na **WebContent**, pak na **New** (Nový) a nakonec na **JSP File** (Soubor JSP).
4. V dialogovém okně **New JSP File** (Nový soubor JSP) pojmenujte soubor **index.jsp**. Nadřazený adresář ponechte na **WebContent**, jak ukazuje následující ilustrace, a klikněte na **Next** (Další).
   
    ![Vytvoření nového souboru JSP – kurz vývoje aplikace Java](./media/sql-api-java-application/image11.png)
5. V dialogovém okně **Select JSP Template** (Výběr šablony JSP) vyberte pro účely tohoto kurzu možnost **New JSP File (html)** (Nový soubor JSP (HTML)) a klikněte na **Finish** (Dokončit).
6. Až se soubor index.jsp otevře v prostředí Eclipse, přidejte text pro zobrazení **Hello World!** do existujícího elementu <body>. Aktualizovaný obsah <body> by se měl podobat následujícímu kódu:
   
        <body>
            <% out.println("Hello World!"); %>
        </body>
7. Uložte soubor index.jsp.
8. Pokud v kroku 2 nastavíte cílový modul runtime, můžete kliknout na **Project** a pomocí příkazu **Run** (Spustit) aplikaci JSP místně spustit:
   
    ![Hello World – kurz aplikace Java](./media/sql-api-java-application/image12.png)

## <a id="InstallSDK"></a>Krok 3: Instalace sady SQL Java SDK
Nejjednodušším způsobem, jak stáhnout sadu SQL Java SDK a její závislosti, je použít [Apache Maven](http://maven.apache.org/).

K tomu bude nutné převést projekt na projekt Maven. K tomu slouží následující kroky:

1. Klikněte pravým tlačítkem na projekt v Project Exploreru, pak klikněte na **Configure** (Konfigurovat) a následně na **Convert to Maven Project** (Převést na projekt Maven).
2. V okně **Create new POM** (Vytvořit nový POM) přijměte výchozí hodnoty a klikněte na **Finish** (Dokončit).
3. V **Project Exploreru** otevřete soubor pom.xml.
4. Na kartě **Dependencies** (Závislosti) v podokně **Dependencies** klikněte na **Add** (Přidat).
5. V okně **Select Dependency** (Vybrat závislost) udělejte následující:
   
   * Do pole **Group Id** (ID skupiny) zadejte com.microsoft.azure.
   * Do pole **Artifact Id** (ID artefaktu) zadejte azure-documentdb.
   * Do pole **Version** (Verze) zadejte 1.5.1.
     
   ![Instalace sady SQL Java Application SDK](./media/sql-api-java-application/image13.png)
     
   * Nebo přidejte XML závislosti pro GroupId a ArtifactId přímo do souboru pom.xml pomocí textového editoru:
     
        <dependency> <groupId>com.microsoft.azure</groupId> <artifactId>azure-documentdb</artifactId> <version>1.9.1</version> </dependency>
6. Klikněte na **OK** a Maven nainstaluje sadu SQL Java SDK.
7. Uložte soubor pom.xml.

## <a id="UseService"></a>Krok 4: Využití služby Azure Cosmos DB v aplikaci Java
1. Nejdříve definujme objekt TodoItem v souboru TodoItem.java:
   
        @Data
        @Builder
        public class TodoItem {
            private String category;
            private boolean complete;
            private String id;
            private String name;
        }
   
    V tomto projektu používáte [Project Lombok](http://projectlombok.org/), pomocí kterého generujeme konstruktor, metody getter a setter a tvůrce (builder). Alternativně můžete tento kód napsat ručně nebo jej vygenerovat pomocí rozhraní IDE.
2. Abyste mohli vyvolat službu Azure Cosmos DB, musíte vytvořit novou instanci **DocumentClient**. Obecně je lépe opakovaně používat **DocumentClient** než pro každý další požadavek vytvářet nového klienta. Klienta můžeme opakovaně používat tak, že jej zabalíme do **DocumentClientFactory**. Do souboru DocumentClientFactory.java je zapotřebí vložit hodnotu URI a PRIMARY KEY, kterou jste uložili do schránky v [kroku 1](#CreateDB). Nahraďte [YOUR\_ENDPOINT\_HERE] hodnotou URI a [YOUR\_KEY\_HERE] hodnotou PRIMARY KEY.
   
        private static final String HOST = "[YOUR_ENDPOINT_HERE]";
        private static final String MASTER_KEY = "[YOUR_KEY_HERE]";
   
        private static DocumentClient documentClient = new DocumentClient(HOST, MASTER_KEY,
                        ConnectionPolicy.GetDefault(), ConsistencyLevel.Session);
   
        public static DocumentClient getDocumentClient() {
            return documentClient;
        }
3. Nyní vytvořme objekt pro přístup k datům (DAO), pomocí kterého zajistíme abstrakci uchovávání položek ToDo ve službě Azure Cosmos DB.
   
    Abychom mohli položky ToDo ukládat do kolekce, klient musí vědět, která databáze nebo kolekce se má k uchovávání použít (podle odkazů na sebe sama). Obecně je nejlépe uložit databázi a kolekci do mezipaměti, kdykoli je to možné, aby se zamezilo nadbytečným přístupům do databáze.
   
    Následující kód ukazuje, jak získat databázi a kolekci, pokud existuje, nebo vytvořit novou, pokud neexistuje:
   
        public class DocDbDao implements TodoDao {
            // The name of our database.
            private static final String DATABASE_ID = "TodoDB";
   
            // The name of our collection.
            private static final String COLLECTION_ID = "TodoCollection";
   
            // The Azure Cosmos DB Client
            private static DocumentClient documentClient = DocumentClientFactory
                    .getDocumentClient();
   
            // Cache for the database object, so we don't have to query for it to
            // retrieve self links.
            private static Database databaseCache;
   
            // Cache for the collection object, so we don't have to query for it to
            // retrieve self links.
            private static DocumentCollection collectionCache;
   
            private Database getTodoDatabase() {
                if (databaseCache == null) {
                    // Get the database if it exists
                    List<Database> databaseList = documentClient
                            .queryDatabases(
                                    "SELECT * FROM root r WHERE r.id='" + DATABASE_ID
                                            + "'", null).getQueryIterable().toList();
   
                    if (databaseList.size() > 0) {
                        // Cache the database object so we won't have to query for it
                        // later to retrieve the selfLink.
                        databaseCache = databaseList.get(0);
                    } else {
                        // Create the database if it doesn't exist.
                        try {
                            Database databaseDefinition = new Database();
                            databaseDefinition.setId(DATABASE_ID);
   
                            databaseCache = documentClient.createDatabase(
                                    databaseDefinition, null).getResource();
                        } catch (DocumentClientException e) {
                            // TODO: Something has gone terribly wrong - the app wasn't
                            // able to query or create the collection.
                            // Verify your connection, endpoint, and key.
                            e.printStackTrace();
                        }
                    }
                }
   
                return databaseCache;
            }
   
            private DocumentCollection getTodoCollection() {
                if (collectionCache == null) {
                    // Get the collection if it exists.
                    List<DocumentCollection> collectionList = documentClient
                            .queryCollections(
                                    getTodoDatabase().getSelfLink(),
                                    "SELECT * FROM root r WHERE r.id='" + COLLECTION_ID
                                            + "'", null).getQueryIterable().toList();
   
                    if (collectionList.size() > 0) {
                        // Cache the collection object so we won't have to query for it
                        // later to retrieve the selfLink.
                        collectionCache = collectionList.get(0);
                    } else {
                        // Create the collection if it doesn't exist.
                        try {
                            DocumentCollection collectionDefinition = new DocumentCollection();
                            collectionDefinition.setId(COLLECTION_ID);
   
                            collectionCache = documentClient.createCollection(
                                    getTodoDatabase().getSelfLink(),
                                    collectionDefinition, null).getResource();
                        } catch (DocumentClientException e) {
                            // TODO: Something has gone terribly wrong - the app wasn't
                            // able to query or create the collection.
                            // Verify your connection, endpoint, and key.
                            e.printStackTrace();
                        }
                    }
                }
   
                return collectionCache;
            }
        }
4. Dalším krokem je napsat kód, který uchová TodoItems v kolekci. V tomto příkladu použijeme [Gson](https://code.google.com/p/google-gson/), pomocí kterého serializujeme a deserializujeme objekty TodoItem Plain Old Java Object (POJO) do dokumentů JSON.
   
        // We'll use Gson for POJO <=> JSON serialization for this example.
        private static Gson gson = new Gson();
   
        @Override
        public TodoItem createTodoItem(TodoItem todoItem) {
            // Serialize the TodoItem as a JSON Document.
            Document todoItemDocument = new Document(gson.toJson(todoItem));
   
            // Annotate the document as a TodoItem for retrieval (so that we can
            // store multiple entity types in the collection).
            todoItemDocument.set("entityType", "todoItem");
   
            try {
                // Persist the document using the DocumentClient.
                todoItemDocument = documentClient.createDocument(
                        getTodoCollection().getSelfLink(), todoItemDocument, null,
                        false).getResource();
            } catch (DocumentClientException e) {
                e.printStackTrace();
                return null;
            }
   
            return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
        }
5. Podobně jako databáze a kolekce Azure Cosmos DB se i dokumenty odkazují pomocí odkazů na sebe sama. Následující pomocná funkce nám umožní získat dokumenty podle jiného atributu (např. id) namísto odkazu na sebe sama:
   
        private Document getDocumentById(String id) {
            // Retrieve the document using the DocumentClient.
            List<Document> documentList = documentClient
                    .queryDocuments(getTodoCollection().getSelfLink(),
                            "SELECT * FROM root r WHERE r.id='" + id + "'", null)
                    .getQueryIterable().toList();
   
            if (documentList.size() > 0) {
                return documentList.get(0);
            } else {
                return null;
            }
        }
6. Pomocnou metodu z kroku 5 můžete využít k získání dokumentu JSON objektu TodoItem podle id a jeho následné deserializaci na objekt POJO:
   
        @Override
        public TodoItem readTodoItem(String id) {
            // Retrieve the document by id using our helper method.
            Document todoItemDocument = getDocumentById(id);
   
            if (todoItemDocument != null) {
                // De-serialize the document in to a TodoItem.
                return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
            } else {
                return null;
            }
        }
7. Také můžeme použít DocumentClient k získání kolekce nebo seznamu objektů TodoItem pomocí SQL:
   
        @Override
        public List<TodoItem> readTodoItems() {
            List<TodoItem> todoItems = new ArrayList<TodoItem>();
   
            // Retrieve the TodoItem documents
            List<Document> documentList = documentClient
                    .queryDocuments(getTodoCollection().getSelfLink(),
                            "SELECT * FROM root r WHERE r.entityType = 'todoItem'",
                            null).getQueryIterable().toList();
   
            // De-serialize the documents in to TodoItems.
            for (Document todoItemDocument : documentList) {
                todoItems.add(gson.fromJson(todoItemDocument.toString(),
                        TodoItem.class));
            }
   
            return todoItems;
        }
8. Existuje mnoho způsobů, jak pomocí DocumentClient aktualizovat dokument. V naší aplikaci seznamu úkolů chceme mít možnost určovat, zda je položka TodoItem dokončená. Toho lze dosáhnout aktualizací atributu complete v dokumentu.
   
        @Override
        public TodoItem updateTodoItem(String id, boolean isComplete) {
            // Retrieve the document from the database
            Document todoItemDocument = getDocumentById(id);
   
            // You can update the document as a JSON document directly.
            // For more complex operations - you could de-serialize the document in
            // to a POJO, update the POJO, and then re-serialize the POJO back in to
            // a document.
            todoItemDocument.set("complete", isComplete);
   
            try {
                // Persist/replace the updated document.
                todoItemDocument = documentClient.replaceDocument(todoItemDocument,
                        null).getResource();
            } catch (DocumentClientException e) {
                e.printStackTrace();
                return null;
            }
   
            return gson.fromJson(todoItemDocument.toString(), TodoItem.class);
        }
9. Nakonec chcete mít možnost odstranit TodoItem ze seznamu. K tomu můžeme využít pomocnou metodu, kterou jsme napsali dříve, abychom získali odkaz na sebe sama a pak dali pokyn klientovi, aby jej odstranil:
   
        @Override
        public boolean deleteTodoItem(String id) {
            // Azure Cosmos DB refers to documents by self link rather than id.
   
            // Query for the document to retrieve the self link.
            Document todoItemDocument = getDocumentById(id);
   
            try {
                // Delete the document by self link.
                documentClient.deleteDocument(todoItemDocument.getSelfLink(), null);
            } catch (DocumentClientException e) {
                e.printStackTrace();
                return false;
            }
   
            return true;
        }

## <a id="Wire"></a>Krok 5: Vzájemné propojení zbytku projektu vývoje aplikace Java
Nyní když jsme dokončili ty zábavné části, zbývá již jen vytvořit rychlé uživatelské rozhraní a propojit je s objektem DAO.

1. Nejdříve začneme vytvořením kontroleru, který bude náš objekt DAO volat:
   
        public class TodoItemController {
            public static TodoItemController getInstance() {
                if (todoItemController == null) {
                    todoItemController = new TodoItemController(TodoDaoFactory.getDao());
                }
                return todoItemController;
            }
   
            private static TodoItemController todoItemController;
   
            private final TodoDao todoDao;
   
            TodoItemController(TodoDao todoDao) {
                this.todoDao = todoDao;
            }
   
            public TodoItem createTodoItem(@NonNull String name,
                    @NonNull String category, boolean isComplete) {
                TodoItem todoItem = TodoItem.builder().name(name).category(category)
                        .complete(isComplete).build();
                return todoDao.createTodoItem(todoItem);
            }
   
            public boolean deleteTodoItem(@NonNull String id) {
                return todoDao.deleteTodoItem(id);
            }
   
            public TodoItem getTodoItemById(@NonNull String id) {
                return todoDao.readTodoItem(id);
            }
   
            public List<TodoItem> getTodoItems() {
                return todoDao.readTodoItems();
            }
   
            public TodoItem updateTodoItem(@NonNull String id, boolean isComplete) {
                return todoDao.updateTodoItem(id, isComplete);
            }
        }
   
    Ve složitější aplikaci může kontroler obsahovat komplikovanou obchodní logiku pro práci s objektem DAO.
2. Dále vytvoříme servlet pro směrování požadavků HTTP na kontroler:
   
        public class TodoServlet extends HttpServlet {
            // API Keys
            public static final String API_METHOD = "method";
   
            // API Methods
            public static final String CREATE_TODO_ITEM = "createTodoItem";
            public static final String GET_TODO_ITEMS = "getTodoItems";
            public static final String UPDATE_TODO_ITEM = "updateTodoItem";
   
            // API Parameters
            public static final String TODO_ITEM_ID = "todoItemId";
            public static final String TODO_ITEM_NAME = "todoItemName";
            public static final String TODO_ITEM_CATEGORY = "todoItemCategory";
            public static final String TODO_ITEM_COMPLETE = "todoItemComplete";
   
            public static final String MESSAGE_ERROR_INVALID_METHOD = "{'error': 'Invalid method'}";
   
            private static final long serialVersionUID = 1L;
            private static final Gson gson = new Gson();
   
            @Override
            protected void doGet(HttpServletRequest request,
                    HttpServletResponse response) throws ServletException, IOException {
   
                String apiResponse = MESSAGE_ERROR_INVALID_METHOD;
   
                TodoItemController todoItemController = TodoItemController
                        .getInstance();
   
                String id = request.getParameter(TODO_ITEM_ID);
                String name = request.getParameter(TODO_ITEM_NAME);
                String category = request.getParameter(TODO_ITEM_CATEGORY);
                boolean isComplete = StringUtils.equalsIgnoreCase("true",
                        request.getParameter(TODO_ITEM_COMPLETE)) ? true : false;
   
                switch (request.getParameter(API_METHOD)) {
                case CREATE_TODO_ITEM:
                    apiResponse = gson.toJson(todoItemController.createTodoItem(name,
                            category, isComplete));
                    break;
                case GET_TODO_ITEMS:
                    apiResponse = gson.toJson(todoItemController.getTodoItems());
                    break;
                case UPDATE_TODO_ITEM:
                    apiResponse = gson.toJson(todoItemController.updateTodoItem(id,
                            isComplete));
                    break;
                default:
                    break;
                }
   
                response.getWriter().println(apiResponse);
            }
   
            @Override
            protected void doPost(HttpServletRequest request,
                    HttpServletResponse response) throws ServletException, IOException {
                doGet(request, response);
            }
        }
3. Budeme potřebovat webové uživatelské rozhraní, které se zobrazí uživateli. Přepišme soubor index.jsp, který jsme vytvořili dříve:
    ```html
        <html>
        <head>
          <meta http-equiv="Content-Type" content="text/html; charset=ISO-8859-1">
          <meta http-equiv="X-UA-Compatible" content="IE=edge;" />
          <title>Azure Cosmos DB Java Sample</title>
   
          <!-- Bootstrap -->
          <link href="//ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/css/bootstrap.min.css" rel="stylesheet">
   
          <style>
            /* Add padding to body for fixed nav bar */
            body {
              padding-top: 50px;
            }
          </style>
        </head>
        <body>
          <!-- Nav Bar -->
          <div class="navbar navbar-inverse navbar-fixed-top" role="navigation">
            <div class="container">
              <div class="navbar-header">
                <a class="navbar-brand" href="#">My Tasks</a>
              </div>
            </div>
          </div>
   
          <!-- Body -->
          <div class="container">
            <h1>My ToDo List</h1>
   
            <hr/>
   
            <!-- The ToDo List -->
            <div class = "todoList">
              <table class="table table-bordered table-striped" id="todoItems">
                <thead>
                  <tr>
                    <th>Name</th>
                    <th>Category</th>
                    <th>Complete</th>
                  </tr>
                </thead>
                <tbody>
                </tbody>
              </table>
   
              <!-- Update Button -->
              <div class="todoUpdatePanel">
                <form class="form-horizontal" role="form">
                  <button type="button" class="btn btn-primary">Update Tasks</button>
                </form>
              </div>
   
            </div>
   
            <hr/>
   
            <!-- Item Input Form -->
            <div class="todoForm">
              <form class="form-horizontal" role="form">
                <div class="form-group">
                  <label for="inputItemName" class="col-sm-2">Task Name</label>
                  <div class="col-sm-10">
                    <input type="text" class="form-control" id="inputItemName" placeholder="Enter name">
                  </div>
                </div>
   
                <div class="form-group">
                  <label for="inputItemCategory" class="col-sm-2">Task Category</label>
                  <div class="col-sm-10">
                    <input type="text" class="form-control" id="inputItemCategory" placeholder="Enter category">
                  </div>
                </div>
   
                <button type="button" class="btn btn-primary">Add Task</button>
              </form>
            </div>
   
          </div>
   
          <!-- Placed at the end of the document so the pages load faster -->
          <script src="//ajax.aspnetcdn.com/ajax/jQuery/jquery-2.1.1.min.js"></script>
          <script src="//ajax.aspnetcdn.com/ajax/bootstrap/3.2.0/bootstrap.min.js"></script>
          <script src="assets/todo.js"></script>
        </body>
        </html>
    ```
4. A nakonec napišme kód JavaScript na straně klienta, který prováže webové uživatelské rozhraní se servletem:
   
        var todoApp = {
          /*
           * API methods to call Java backend.
           */
          apiEndpoint: "api",
   
          createTodoItem: function(name, category, isComplete) {
            $.post(todoApp.apiEndpoint, {
                "method": "createTodoItem",
                "todoItemName": name,
                "todoItemCategory": category,
                "todoItemComplete": isComplete
              },
              function(data) {
                var todoItem = data;
                todoApp.addTodoItemToTable(todoItem.id, todoItem.name, todoItem.category, todoItem.complete);
              },
              "json");
          },
   
          getTodoItems: function() {
            $.post(todoApp.apiEndpoint, {
                "method": "getTodoItems"
              },
              function(data) {
                var todoItemArr = data;
                $.each(todoItemArr, function(index, value) {
                  todoApp.addTodoItemToTable(value.id, value.name, value.category, value.complete);
                });
              },
              "json");
          },
   
          updateTodoItem: function(id, isComplete) {
            $.post(todoApp.apiEndpoint, {
                "method": "updateTodoItem",
                "todoItemId": id,
                "todoItemComplete": isComplete
              },
              function(data) {},
              "json");
          },
   
          /*
           * UI Methods
           */
          addTodoItemToTable: function(id, name, category, isComplete) {
            var rowColor = isComplete ? "active" : "warning";
   
            todoApp.ui_table().append($("<tr>")
              .append($("<td>").text(name))
              .append($("<td>").text(category))
              .append($("<td>")
                .append($("<input>")
                  .attr("type", "checkbox")
                  .attr("id", id)
                  .attr("checked", isComplete)
                  .attr("class", "isComplete")
                ))
              .addClass(rowColor)
            );
          },
   
          /*
           * UI Bindings
           */
          bindCreateButton: function() {
            todoApp.ui_createButton().click(function() {
              todoApp.createTodoItem(todoApp.ui_createNameInput().val(), todoApp.ui_createCategoryInput().val(), false);
              todoApp.ui_createNameInput().val("");
              todoApp.ui_createCategoryInput().val("");
            });
          },
   
          bindUpdateButton: function() {
            todoApp.ui_updateButton().click(function() {
              // Disable button temporarily.
              var myButton = $(this);
              var originalText = myButton.text();
              $(this).text("Updating...");
              $(this).prop("disabled", true);
   
              // Call api to update todo items.
              $.each(todoApp.ui_updateId(), function(index, value) {
                todoApp.updateTodoItem(value.name, value.value);
                $(value).remove();
              });
   
              // Re-enable button.
              setTimeout(function() {
                myButton.prop("disabled", false);
                myButton.text(originalText);
              }, 500);
            });
          },
   
          bindUpdateCheckboxes: function() {
            todoApp.ui_table().on("click", ".isComplete", function(event) {
              var checkboxElement = $(event.currentTarget);
              var rowElement = $(event.currentTarget).parents('tr');
              var id = checkboxElement.attr('id');
              var isComplete = checkboxElement.is(':checked');
   
              // Toggle table row color
              if (isComplete) {
                rowElement.addClass("active");
                rowElement.removeClass("warning");
              } else {
                rowElement.removeClass("active");
                rowElement.addClass("warning");
              }
   
              // Update hidden inputs for update panel.
              todoApp.ui_updateForm().children("input[name='" + id + "']").remove();
   
              todoApp.ui_updateForm().append($("<input>")
                .attr("type", "hidden")
                .attr("class", "updateComplete")
                .attr("name", id)
                .attr("value", isComplete));
   
            });
          },
   
          /*
           * UI Elements
           */
          ui_createNameInput: function() {
            return $(".todoForm #inputItemName");
          },
   
          ui_createCategoryInput: function() {
            return $(".todoForm #inputItemCategory");
          },
   
          ui_createButton: function() {
            return $(".todoForm button");
          },
   
          ui_table: function() {
            return $(".todoList table tbody");
          },
   
          ui_updateButton: function() {
            return $(".todoUpdatePanel button");
          },
   
          ui_updateForm: function() {
            return $(".todoUpdatePanel form");
          },
   
          ui_updateId: function() {
            return $(".todoUpdatePanel .updateComplete");
          },
   
          /*
           * Install the TodoApp
           */
          install: function() {
            todoApp.bindCreateButton();
            todoApp.bindUpdateButton();
            todoApp.bindUpdateCheckboxes();
   
            todoApp.getTodoItems();
          }
        };
   
        $(document).ready(function() {
          todoApp.install();
        });
5. Skvělé! Nyní již zbývá aplikaci jen otestovat. Spusťte aplikaci místně a zadáním názvů a kategorie položek a kliknutím na **Add Task** (Přidat úkol) přidejte několik položek Todo.
6. Až se položka zobrazí, můžete aktualizovat, zda je dokončená, přepínáním zaškrtávacího políčka a kliknutím na **Update Tasks** (Aktualizovat úkoly).

## <a id="Deploy"></a>Krok 6: Nasazení aplikace Java do Azure Websites
Díky Azure Websites je nasazování aplikací Java stejně snadné jako export aplikace jako souboru WAR a jeho nahrání buď přes správu zdrojových kódů (např. Git), nebo FTP.

1. Pokud chcete aplikaci exportovat jako soubor WAR, klikněte pravým tlačítkem na projekt v **Project Exploreru**, pak levým na **Export** a nakonec na **WAR File** (Soubor WAR).
2. V okně **WAR Export** udělejte následující:
   
   * Do pole Web project (Webový projekt) zadejte azure-documentdb-java-sample.
   * V poli Destination (Cíl) vyberte cíl, do kterého se uloží soubor WAR.
   * Klikněte na **Dokončit**.
3. Nyní když máte k dispozici soubor WAR, můžete tento soubor jednoduše nahrát do adresáře **webapps** Azure Websites. Pokyny, jak soubor nahrát, najdete v tématu o [přidání aplikace Java do Azure App Service Web Apps](../app-service/web-sites-java-add-app.md).
   
    Až bude soubor WAR nahrán do adresáře webapps, běhové prostředí zjistí, že jste jej přidali, a automaticky ho načte.
4. Chcete-li zobrazit hotový produkt, přejděte na http://YOUR\_SITE\_NAME.azurewebsites.net/azure-java-sample/ a začněte přidávat úkoly.

## <a id="GetProject"></a>Získání projektu z Githubu
Všechny ukázky v tomto kurzu jsou součástí projektu [todo](https://github.com/Azure-Samples/documentdb-java-todo-app) na GitHubu. Pokud chcete importovat projekt todo do prostředí Eclipse, ujistěte se, že máte software a prostředky uvedené v části [Předpoklady](#Prerequisites), a udělejte následující:

1. Nainstalujte [Project Lombok](http://projectlombok.org/). Lombok slouží ke generování konstruktorů a metod getter a setter v projektu. Jakmile budete mít stažen soubor lombok.jar, dvakrát na něj klikněte, aby se nainstaloval, nebo jej nainstalujte z příkazového řádku.
2. Pokud je prostředí Eclipse otevřené, zavřete ho a znovu ho spusťte, aby se načetl Lombok.
3. V prostředí Eclipse v nabídce **File** (Soubor) klikněte na **Import**.
4. V okně **Import** klikněte na **Git**, pak na **Projects from Git** (Projekty z Gitu) a nakonec na **Next** (Další).
5. Na obrazovce **Select Repository Source** (Výběr zdroje úložiště) klikněte na **Clone URI** (Klonovat URI).
6. Na obrazovce **Source Git Repository** (Zdrojové úložiště Git) v poli **URI** zadejte https://github.com/Azure-Samples/documentdb-java-todo-app.git a potom klikněte na **Next** (Další).
7. Na obrazovce **Branch Selection** (Výběr větve) se ujistěte, že je zvolena možnost **master** (hlavní), a klikněte na **Next**.
8. Na obrazovce **Local Destination** (Místní cíl) klikněte na **Browse** (Procházet), vyberte složku, do které lze úložiště zkopírovat, a pak klikněte na **Next**.
9. Na obrazovce **Select a wizard to use for importing projects** (Výběr průvodce, který se použije k importování projektů) se ujistěte, že je vybrána možnost **Import existing projects** (Import existujících projektů) a klikněte na **Next**.
10. Na obrazovce **Import Projects** (Import projektů) zrušte výběr projektu **DocumentDB** a klikněte na **Finish** (Dokončit). Projekt Azure Cosmos DB obsahuje sadu Azure Cosmos DB Java SDK, kterou přidáme jako závislost.
11. V **Project Exploreru** přejděte na azure-documentdb-java-sample\src\com.microsoft.azure.documentdb.sample.dao\DocumentClientFactory.java a nahraďte hodnoty HOST a MASTER_KEY hodnotami URI a PRIMARY KEY pro účet Azure Cosmos DB. Pak soubor uložte. Další informace najdete v části [Krok 1. Vytvoření účtu databáze Azure Cosmos DB](#CreateDB).
12. V **Project Exploreru** klikněte pravým tlačítkem na **azure-documentdb-java-sample**, pak levým na **Build Path** (Cesta sestavení) a nakonec na **Configure Build Path** (Konfigurovat cestu sestavení).
13. Na obrazovce **Java Build Path** (Cesta sestavení Java) v pravém podokně vyberte kartu **Libraries** (Knihovny) a klikněte na **Add External JARs** (Přidat externí balíčky JAR). Přejděte na umístění souboru lombok.jar, klikněte na **Open** (Otevřít) a pak na **OK**.
14. Pomocí kroku 12 otevřete znovu okno **Properties** (Vlastnosti) a v levém podokně klikněte na **Targeted Runtimes** (Cílené moduly runtime).
15. Na obrazovce **Targeted Runtimes** klikněte na **New** (Nový), vyberte **Apache Tomcat v7.0** a klikněte na **OK**.
16. Pomocí kroku 12 otevřete znovu okno **Properties** a v levém podokně klikněte na **Project Facets** (Omezující vlastnosti projektu).
17. Na obrazovce **Project Facets** vyberte **Dynamic Web Module** (Dynamický webový modul) a **Java** a klikněte na **OK**.
18. Na kartě **Servers** (Servery) v dolní části obrazovky klikněte pravým tlačítkem na **Tomcat v7.0 Server at localhost** a pak levým na **Add and Remove** (Přidat a odstranit).
19. V okně **Add and Remove** přesuňte **azure-documentdb-java-sample** do pole **Configured** (Nakonfigurováno) a klikněte na **Finish** (Dokončit).
20. Na kartě **Servers** (Servery) klikněte pravým tlačítkem na **Tomcat v7.0 Server at localhost** a klikněte na **Restart** (Restartovat).
21. V prohlížeči přejděte na adresu http://localhost:8080/azure-documentdb-java-sample/ a začněte přidávat položky do seznamu úkolů. Poznámka: Pokud jste změnili výchozí hodnoty portů, změňte 8080 na hodnotu, kterou jste si vybrali.
22. Postup nasazení projektu na web Azure najdete v části [Krok 6. Nasazení aplikace do Azure Websites](#Deploy).

