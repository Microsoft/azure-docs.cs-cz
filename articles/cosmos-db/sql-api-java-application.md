---
title: 'Kurz: Vytvoření webové aplikace v jazyce Java pomocí Azure Cosmos DB a rozhraní SQL API'
description: 'Kurz: Tento kurz webové aplikace v jazyce Java vám ukáže, jak používat Azure Cosmos DB a rozhraní SQL API k ukládání a přístupu k datům z aplikace Java hostované na Azure websites.'
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 05/12/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 913bc4373785d9341064e505ddce84fe43f727b1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91801592"
---
# <a name="tutorial-build-a-java-web-application-using-azure-cosmos-db-and-the-sql-api"></a>Kurz: Vytvoření webové aplikace v jazyce Java pomocí Azure Cosmos DB a rozhraní SQL API

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Tento kurz vývoje webové aplikace Java ukazuje, jak pomocí služby [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) ukládat data a přistupovat k nim z aplikace Java hostované ve službě Azure App Service Web Apps. V tomto článku se dozvíte:

* Jak vytvořit základní aplikaci JSP (JavaServer Pages) v prostředí Eclipse.
* Jak pracovat se službou Azure Cosmos DB pomocí sady [Azure Cosmos DB Java SDK](https://github.com/Azure/azure-documentdb-java).

Tento kurz o aplikaci Java vám ukáže, jak vytvořit webovou aplikaci pro správu úkolů, která umožňuje vytvářet a získávat úkoly a označovat je jako dokončené, jak ilustruje následující obrázek. Každý z úkolů v seznamu se ve službě Azure Cosmos DB ukládá jako dokument JSON.

:::image type="content" source="./media/sql-api-java-application/image1.png" alt-text="Aplikace pro seznam úkolů v jazyce Java":::

> [!TIP]
> V tomto kurzu vývoje aplikace se předpokládá, že již máte zkušenosti s jazykem Java. Pokud je pro vás Java nebo některý z [požadovaných nástrojů](#Prerequisites) nový, doporučujeme stáhnout úplný ukázkový projekt [todo](https://github.com/Azure-Samples/documentdb-java-todo-app) z GitHubu a postupovat podle [pokynů na konci tohoto článku](#GetProject). Až jej budete mít sestavený, můžete se k tomuto článku vrátit, abyste kódu lépe porozuměli v kontextu projektu.  
>

## <a name="prerequisites-for-this-java-web-application-tutorial"></a><a id="Prerequisites"></a>Předpoklady pro tento kurz webové aplikace Java

Než zahájíte tento kurz vývoje aplikace, musíte mít následující:

* Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Java Development Kit (JDK) 7+](/java/azure/jdk/?view=azure-java-stable&preserve-view=true)
* [Integrované vývojové prostředí Eclipse pro vývojáře v jazyce Java EE](https://www.eclipse.org/downloads/packages/release/luna/sr1/eclipse-ide-java-ee-developers)
* [Web Azure se zapnutou platformou Java Runtime Environment (např. Tomcat nebo Jetty)](../app-service/quickstart-java.md)

Při prvním instalaci těchto nástrojů coreservlets.com poskytuje návod k procesu instalace v části rychlý Start v [kurzu: instalace TomCat7 a jeho použití v článku s](http://www.coreservlets.com/Apache-Tomcat-Tutorial/tomcat-7-with-eclipse.html) poznámkou.

## <a name="create-an-azure-cosmos-db-account"></a><a id="CreateDB"></a>Vytvoření účtu služby Azure Cosmos DB

Začněme vytvořením účtu služby Azure Cosmos DB. Pokud již účet máte nebo pokud používáte pro účely tohoto kurzu emulátor služby Azure Cosmos DB, můžete přeskočit na [Krok 2: Vytvoření aplikace Java JSP](#CreateJSP).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

## <a name="create-the-java-jsp-application"></a><a id="CreateJSP"></a>Vytvoření aplikace Java JSP

Vytvoření aplikace JSP:

1. Nejdříve začneme vytvořením projektu Java. Spusťte Eclipse, klikněte na **File** (Soubor), pak na **New** (Nový) a nakonec na **Dynamic Web Project** (Dynamický webový projekt). Pokud nevidíte **dynamický webový projekt** uvedený jako dostupný projekt, udělejte toto: klikněte na **soubor**, klikněte na **Nový**, klikněte na **projekt**..., rozbalte **Web**, klikněte na **dynamický webový projekt**a klikněte na **Další**.
   
    :::image type="content" source="./media/sql-api-java-application/image10.png" alt-text="Aplikace pro seznam úkolů v jazyce Java":::

1. Zadejte název projektu do pole **Project name** (Název projektu), volitelně v rozevírací nabídce **Target Runtime** (Cílový modul runtime) vyberte hodnotu (např. Apache Tomcat v7.0) a klikněte na **Finish** (Dokončit). Pokud vyberete cílový modul runtime, budete moci spouštět projekt místně přes Eclipse.

1. V prostředí Eclipse v zobrazení Project Explorer (Průzkumník projektů) rozbalte projekt. Klikněte pravým tlačítkem na **WebContent**, pak na **New** (Nový) a nakonec na **JSP File** (Soubor JSP).

1. V dialogovém okně **New JSP File** (Nový soubor JSP) pojmenujte soubor **index.jsp**. Nadřazený adresář ponechte na **WebContent**, jak ukazuje následující ilustrace, a klikněte na **Next** (Další).
   
    :::image type="content" source="./media/sql-api-java-application/image11.png" alt-text="Aplikace pro seznam úkolů v jazyce Java":::

1. V dialogovém okně **Select JSP Template** (Výběr šablony JSP) vyberte pro účely tohoto kurzu možnost **New JSP File (html)** (Nový soubor JSP (HTML)) a klikněte na **Finish** (Dokončit).

1. Pokud se soubor *index.jsp* otevře v části zatmění, přidejte text, který se zobrazí **Hello World!** do existujícího elementu `<body>`. Aktualizovaný obsah `<body>` by se měl podobat následujícímu kódu:

   ```html
   <body>
     <% out.println("Hello World!"); %>
   </body>
   ```

1. Uložte soubor *index.jsp* .

1. Pokud v kroku 2 nastavíte cílový modul runtime, můžete kliknout na **Project** a pomocí příkazu **Run** (Spustit) aplikaci JSP místně spustit:

   :::image type="content" source="./media/sql-api-java-application/image12.png" alt-text="Aplikace pro seznam úkolů v jazyce Java":::

## <a name="install-the-sql-java-sdk"></a><a id="InstallSDK"></a>Instalace sady SQL Java SDK

Nejjednodušším způsobem, jak stáhnout sadu SQL Java SDK a její závislosti, je použít [Apache Maven](https://maven.apache.org/). K tomu je potřeba převést projekt na projekt Maven pomocí následujících kroků:

1. Klikněte pravým tlačítkem na projekt v Project Exploreru, pak klikněte na **Configure** (Konfigurovat) a následně na **Convert to Maven Project** (Převést na projekt Maven).

1. V okně **vytvořit nové pom** přijměte výchozí hodnoty a klikněte na **Dokončit**.

1. V **Project Exploreru** otevřete soubor pom.xml.

1. Na kartě **Dependencies** (Závislosti) v podokně **Dependencies** klikněte na **Add** (Přidat).

1. V okně **Select Dependency** (Vybrat závislost) udělejte následující:
   
   * Do pole **ID skupiny** zadejte `com.azure` .
   * Do pole **ID artefaktu** zadejte `azure-cosmos` .
   * Do pole **verze** zadejte `4.0.1-beta.1` .
  
   Nebo můžete přidat XML závislosti pro ID skupiny a ID artefaktu přímo do souboru *pom.xml* :

   ```xml
   <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-cosmos</artifactId>
      <version>4.0.1-beta.1</version>
   </dependency>
   ```

1. Klikněte na **OK** a Maven nainstaluje sadu SQL Java SDK nebo soubor pom.xml uložte.

## <a name="use-the-azure-cosmos-db-service-in-your-java-application"></a><a id="UseService"></a>Používání služby Azure Cosmos DB v aplikaci Java

Nyní přidáme do vaší webové aplikace modely, zobrazení a řadiče.

### <a name="add-a-model"></a>Přidání modelu

Nejdřív nadefinujte model v rámci nového souboru *TodoItem. Java*. `TodoItem`Třída definuje schéma položky spolu s metodami getter a setter:

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/model/TodoItem.java":::

### <a name="add-the-data-access-objectdao-classes"></a>Přidání tříd objektů pro přístup k datům (DAO)

Vytvořte objekt pro přístup k datům (DAO), který bude zachovávat položky ToDo na Azure Cosmos DB. Abychom mohli položky ToDo ukládat do kolekce, klient musí vědět, která databáze nebo kolekce se má k uchovávání použít (podle odkazů na sebe sama). Obecně je nejlépe uložit databázi a kolekci do mezipaměti, kdykoli je to možné, aby se zamezilo nadbytečným přístupům do databáze.

1. Chcete-li vyvolat službu Azure Cosmos DB, je nutné vytvořit instanci nového `cosmosClient` objektu. Obecně je vhodné znovu použít `cosmosClient` objekt spíše než vytvořit nového klienta pro každou následnou žádost. Klienta můžete znovu použít tak, že ho definujete v rámci `cosmosClientFactory` třídy. Aktualizujte hodnoty HOST a MASTER_KEY, které jste uložili v [kroku 1](#CreateDB). Nahraďte proměnnou hostitele pomocí identifikátoru URI a nahraďte MASTER_KEY PRIMÁRNÍm klíčem. Použijte následující kód k vytvoření `CosmosClientFactory` třídy v souboru *CosmosClientFactory. Java* :

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/dao/CosmosClientFactory.java":::

1. Vytvořte nový soubor *TodoDao. Java* a přidejte `TodoDao` třídu pro vytváření, aktualizaci, čtení a odstranění položek TODO:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/dao/TodoDao.java":::

1. Vytvořte nový soubor *MockDao. Java* a přidejte `MockDao` třídu, tato třída implementuje `TodoDao` třídu pro provádění operací CRUD pro položky:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/dao/MockDao.java":::

1. Vytvořte nový soubor *DocDbDao. Java* a přidejte `DocDbDao` třídu. Tato třída definuje kód pro uchování TodoItems do kontejneru, načte databázi a kolekci, pokud existuje, nebo vytvořte novou, pokud neexistuje. V tomto příkladu se používá [gson](https://code.google.com/p/google-gson/) k serializaci a deserializaci TodoItemch starších objektů Java (POJO) do dokumentů JSON. Abychom mohli položky ToDo ukládat do kolekce, klient musí vědět, která databáze nebo kolekce se má k uchovávání použít (podle odkazů na sebe sama). Tato třída také definuje pomocnou funkci pro načtení dokumentů jiným atributem (např. "ID") místo odkazu na sebe. Pomocnou metodu můžete použít k načtení dokumentu JSON TodoItem podle ID a jeho deserializaci na POJO.

   Objekt klienta můžete také použít `cosmosClient` k získání kolekce nebo seznamu TodoItems pomocí dotazu SQL. Nakonec definujete metodu DELETE pro odstranění TodoItem ze seznamu. Následující kód ukazuje obsah `DocDbDao` třídy:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/dao/DocDbDao.java":::

1. Dále vytvořte nový soubor *TodoDaoFactory. Java* a přidejte `TodoDaoFactory` třídu, která vytvoří nový objekt DocDbDao:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/dao/TodoDaoFactory.java":::

### <a name="add-a-controller"></a>Přidání kontroleru

Přidejte do své aplikace kontroler *TodoItemController* . V tomto projektu používáte [Project Lombok](https://projectlombok.org/), pomocí kterého generujeme konstruktor, metody getter a setter a tvůrce (builder). Alternativně můžete tento kód napsat ručně nebo ho rozhraní IDE vygenerovat.:

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/controller/TodoItemController.java":::

### <a name="create-a-servlet"></a>Vytvoření servlet

Dále vytvořte servlet pro směrování požadavků HTTP do kontroleru. Vytvořte soubor *ApiServlet. Java* a definujte pod ním následující kód:

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/documentdb/sample/ApiServlet.java":::

## <a name="wire-the-rest-of-the-of-java-app-together"></a><a id="Wire"></a>Propojení zbytku aplikace Java

Teď, když jsme dokončili zábavné služby, je to vše, co zbývá k vytvoření rychlého uživatelského rozhraní a jeho vedení do vašeho rozhraní DAO.

1. Potřebujete webové uživatelské rozhraní, které se zobrazí uživateli. Pojďme znovu zapsat *index.jsp* , kterou jsme vytvořili dříve, pomocí následujícího kódu:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/WebContent/index.jsp":::

1. Nakonec zapište nějaký JavaScript na straně klienta, který bude propojen s webovým uživatelským rozhraním a servlet:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/WebContent/assets/todo.js":::

1. Nyní již zbývá aplikaci jen otestovat. Spusťte aplikaci místně a zadáním názvů a kategorie položek a kliknutím na **Add Task** (Přidat úkol) přidejte několik položek Todo. Po zobrazení položky můžete aktualizovat, zda je to hotové, přepnutím zaškrtávacího políčka a kliknutím na **aktualizovat úkoly**.

## <a name="deploy-your-java-application-to-azure-web-sites"></a><a id="Deploy"></a>Nasazení aplikace Java na weby Azure

Díky Azure Websites je nasazování aplikací Java stejně snadné jako export aplikace jako souboru WAR a jeho nahrání buď přes správu zdrojových kódů (např. Git), nebo FTP.

1. Pokud chcete aplikaci exportovat jako soubor WAR, klikněte pravým tlačítkem na projekt v **Project Exploreru**, pak levým na **Export** a nakonec na **WAR File** (Soubor WAR).

1. V okně **WAR Export** udělejte následující:
   
   * Do pole Web project (Webový projekt) zadejte azure-documentdb-java-sample.
   * V poli Destination (Cíl) vyberte cíl, do kterého se uloží soubor WAR.
   * Klikněte na **Finish** (Dokončit).

1. Nyní když máte k dispozici soubor WAR, můžete tento soubor jednoduše nahrát do adresáře **webapps** Azure Websites. Pokyny, jak soubor nahrát, najdete v tématu o [přidání aplikace Java do Azure App Service Web Apps](../app-service/web-sites-java-add-app.md). Po nahrání souboru WAR do adresáře webapps zjistí běhové prostředí, že jste ho přidali, a automaticky ho načte.

1. Chcete-li zobrazit hotový produkt, přejděte na `http://YOUR\_SITE\_NAME.azurewebsites.net/azure-java-sample/` a začněte přidávat úkoly.

## <a name="get-the-project-from-github"></a><a id="GetProject"></a>Získání projektu z Githubu

Všechny ukázky v tomto kurzu jsou součástí projektu [todo](https://github.com/Azure-Samples/documentdb-java-todo-app) na GitHubu. Pokud chcete importovat projekt todo do prostředí Eclipse, ujistěte se, že máte software a prostředky uvedené v části [Předpoklady](#Prerequisites), a udělejte následující:

1. Nainstalujte [Project Lombok](https://projectlombok.org/). Lombok slouží ke generování konstruktorů a metod getter a setter v projektu. Jakmile budete mít stažen soubor lombok.jar, dvakrát na něj klikněte, aby se nainstaloval, nebo jej nainstalujte z příkazového řádku.

1. Pokud je prostředí Eclipse otevřené, zavřete ho a znovu ho spusťte, aby se načetl Lombok.

1. V prostředí Eclipse v nabídce **File** (Soubor) klikněte na **Import**.

1. V okně **Import** klikněte na **Git**, pak na **Projects from Git** (Projekty z Gitu) a nakonec na **Next** (Další).

1. Na obrazovce **Select Repository Source** (Výběr zdroje úložiště) klikněte na **Clone URI** (Klonovat URI).

1. Na obrazovce **Source Git Repository** (Zdrojové úložiště Git) v poli **URI** zadejte https://github.com/Azure-Samples/documentdb-java-todo-app.git a potom klikněte na **Next** (Další).

1. Na obrazovce **Branch Selection** (Výběr větve) se ujistěte, že je zvolena možnost **master** (hlavní), a klikněte na **Next**.

1. Na obrazovce **Local Destination** (Místní cíl) klikněte na **Browse** (Procházet), vyberte složku, do které lze úložiště zkopírovat, a pak klikněte na **Next**.

1. Na obrazovce **Select a wizard to use for importing projects** (Výběr průvodce, který se použije k importování projektů) se ujistěte, že je vybrána možnost **Import existing projects** (Import existujících projektů) a klikněte na **Next**.

1. Na obrazovce **Import Projects** (Import projektů) zrušte výběr projektu **DocumentDB** a klikněte na **Finish** (Dokončit). Projekt Azure Cosmos DB obsahuje sadu Azure Cosmos DB Java SDK, kterou přidáme jako závislost.

1. V **Project Exploreru** přejděte na azure-documentdb-java-sample\src\com.microsoft.azure.documentdb.sample.dao\DocumentClientFactory.java a nahraďte hodnoty HOST a MASTER_KEY hodnotami URI a PRIMARY KEY pro účet Azure Cosmos DB. Pak soubor uložte. Další informace najdete v části [Krok 1. Vytvořte účet databáze Azure Cosmos](#CreateDB).

1. V **Project Exploreru** klikněte pravým tlačítkem na **azure-documentdb-java-sample**, pak levým na **Build Path** (Cesta sestavení) a nakonec na **Configure Build Path** (Konfigurovat cestu sestavení).

1. Na obrazovce **Java Build Path** (Cesta sestavení Java) v pravém podokně vyberte kartu **Libraries** (Knihovny) a klikněte na **Add External JARs** (Přidat externí balíčky JAR). Přejděte na umístění souboru lombok.jar, klikněte na **Open** (Otevřít) a pak na **OK**.

1. Pomocí kroku 12 otevřete znovu okno **Properties** (Vlastnosti) a v levém podokně klikněte na **Targeted Runtimes** (Cílené moduly runtime).

1. Na obrazovce **Targeted Runtimes** klikněte na **New** (Nový), vyberte **Apache Tomcat v7.0** a klikněte na **OK**.

1. Pomocí kroku 12 otevřete znovu okno **Properties** a v levém podokně klikněte na **Project Facets** (Omezující vlastnosti projektu).

1. Na obrazovce **Project Facets** vyberte **Dynamic Web Module** (Dynamický webový modul) a **Java** a klikněte na **OK**.

1. Na kartě **Servers** (Servery) v dolní části obrazovky klikněte pravým tlačítkem na **Tomcat v7.0 Server at localhost** a pak levým na **Add and Remove** (Přidat a odstranit).

1. V okně **Add and Remove** přesuňte **azure-documentdb-java-sample** do pole **Configured** (Nakonfigurováno) a klikněte na **Finish** (Dokončit).

1. Na kartě **Servers** (Servery) klikněte pravým tlačítkem na **Tomcat v7.0 Server at localhost** a klikněte na **Restart** (Restartovat).

1. V prohlížeči přejděte na adresu `http://localhost:8080/azure-documentdb-java-sample/` a začněte přidávat položky do seznamu úkolů. Poznámka: Pokud jste změnili výchozí hodnoty portů, změňte 8080 na hodnotu, kterou jste si vybrali.

1. Postup nasazení projektu na web Azure najdete v [kroku 6. Nasaďte aplikaci na weby Azure](#Deploy).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Sestavení aplikace node.js s využitím Azure Cosmos DB](sql-api-nodejs-application.md)
