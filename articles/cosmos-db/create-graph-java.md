---
title: Sestavení databáze grafu pomocí Java v Azure Cosmos DB
description: Představuje ukázku kódu Java, který můžete použít k připojení a dotazování dat grafu Azure Cosmos DB pomocí konzoly Gremlin.
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: java
ms.topic: quickstart
ms.date: 03/26/2019
ms.author: lbosq
ms.custom: seo-java-july2019
ms.openlocfilehash: cea53aefae2e559b7874b1235e4f952fe46ea642
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69509616"
---
# <a name="quickstart-create-a-graph-database-in-azure-cosmos-db-using-the-java-sdk"></a>Rychlý start: Vytvoření databáze grafů v Azure Cosmos DB pomocí sady Java SDK 

> [!div class="op_single_selector"]
> * [Konzola Gremlin](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Pomocí Azure Cosmos DB můžete rychle vytvořit a dotazovat spravované databáze dokumentů, tabulek a grafů. 

V tomto rychlém startu se vytvoří jednoduchá databáze grafů pomocí nástrojů pro Azure Cosmos DB na webu Azure Portal. V tomto rychlém startu se také dozvíte, jak rychle vytvořit konzolovou aplikaci Java, která používá databázi [Gremlin API](graph-introduction.md), pomocí ovladače OSS [Apache TinkerPop](https://tinkerpop.apache.org/). Pokyny v tomto rychlém startu platí pro všechny operační systémy, které podporují Javu. Tento rychlý start vás seznámí s vytvářením a upravováním grafů v uživatelském rozhraní nebo programově podle toho, čemu dáváte přednost. 

## <a name="prerequisites"></a>Požadavky
[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Navíc platí:

* [Java Development Kit (JDK) verze 8](https://aka.ms/azure-jdks)
    * Nezapomeňte nastavit proměnnou prostředí JAVA_HOME tak, aby odkazovala na složku, ve které je sada JDK nainstalovaná.
* [Stáhněte](https://maven.apache.org/download.cgi) a [nainstalujte](https://maven.apache.org/install.html) binární archiv [Maven](https://maven.apache.org/).
    * Na Ubuntu můžete Maven nainstalovat spuštěním příkazu `apt-get install maven`.
* [Git](https://www.git-scm.com/)
    * Na Ubuntu můžete Git nainstalovat spuštěním příkazu `sudo apt-get install git`.

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

Než budete moct vytvořit databázi grafu, je potřeba pomocí služby Azure Cosmos DB vytvořit účet databáze Gremlin (Graph).

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Přidání grafu

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Teď přejděme k práci s kódem. Pojďme naklonovat aplikaci rozhraní Gremlin API z GitHubu, nastavit připojovací řetězec a spustit ji. Přesvědčíte se, jak snadno se pracuje s daty prostřednictvím kódu programu.  

1. Otevřete příkazový řádek, vytvořte novou složku git-samples a potom příkazový řádek zavřete.

    ```bash
    md "C:\git-samples"
    ```

2. Otevřete okno terminálu Git, třeba Git Bash, a pomocí příkazu `cd` přejděte do složky, do které chcete nainstalovat ukázkovou aplikaci.  

    ```bash
    cd "C:\git-samples"
    ```

3. Ukázkové úložiště naklonujete spuštěním následujícího příkazu. Tento příkaz vytvoří na vašem počítači kopii ukázkové aplikace. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. Pokud chcete zjistit, jak se v kódu vytvářejí prostředky databáze, můžete si prohlédnout následující fragmenty kódu. Jinak můžete přeskočit přímo k části [Aktualizace informací o připojení](#update-your-connection-information).

Následující fragmenty kódu pocházejí ze souboru C:\git-samples\azure-cosmos-db-graph-java-getting-started\src\GetStarted\Program.java.

* Klient Gremlin `Client` se inicializuje z konfigurace v souboru C:\git-samples\azure-cosmos-db-graph-java-getting-started\src\remote.yaml.

    ```java
    cluster = Cluster.build(new File("src/remote.yaml")).create();
    ...
    client = cluster.connect();
    ```

* Pomocí metody `client.submit` se provede série kroků Gremlin.

    ```java
    ResultSet results = client.submit(gremlin);

    CompletableFuture<List<Result>> completableFutureResults = results.all();
    List<Result> resultList = completableFutureResults.get();

    for (Result result : resultList) {
        System.out.println(result.toString());
    }
    ```

## <a name="update-your-connection-information"></a>Aktualizace informací o připojení

Teď se vraťte na portál Azure, kde najdete informace o připojení, a zkopírujte je do aplikace. Tato nastavení umožní aplikaci komunikovat s hostovanou databází.

1. V [Azure Portal](https://portal.azure.com/)vyberte **klíče**. 

    Zkopírujte první část hodnoty identifikátoru URI.

    ![Zobrazení a zkopírování přístupového klíče na portálu Azure na stránce Klíče](./media/create-graph-java/keys.png)
2. Otevřete soubor src/remote.yaml a vložte jedinečnou hodnotu ID místo `$name$` v části `hosts: [$name$.graphs.azure.com]`.

    Řádek 1 souboru remote.yaml by teď měl vypadat nějak takto: 

    `hosts: [test-graph.graphs.azure.com]`

3. V `endpoint` změňte `graphs` na `gremlin.cosmosdb`. (Pokud jste účet databáze grafu vytvořili před 20. prosincem, 2017, neprovádějte žádné změny hodnoty koncového bodu a pokračujte dalším krokem.)

    Hodnota koncového bodu by teď měla vypadat takto:

    `"endpoint": "https://testgraphacct.gremlin.cosmosdb.azure.com:443/"`

4. Na webu Azure Portal pomocí tlačítka pro kopírování zkopírujte PRIMÁRNÍ KLÍČ a vložte ho místo `$masterKey$` v části `password: $masterKey$`.

    Řádek 4 souboru remote.yaml by teď měl vypadat nějak takto: 

    `password: 2Ggkr662ifxz2Mg==`

5. Změňte řádek 3 souboru remote.yaml z

    `username: /dbs/$database$/colls/$collection$`

    na 

    `username: /dbs/sample-database/colls/sample-graph`

    Pokud jste pro svou ukázkovou databázi nebo graf použili jedinečný název, aktualizujte hodnoty odpovídajícím způsobem.

6. Uložte soubor remote.yaml.

## <a name="run-the-console-app"></a>Spuštění aplikace konzoly

1. V okně terminálu Git přejděte příkazem `cd` do složky azure-cosmos-db-graph-java-getting-started folder.

    ```git
    cd "C:\git-samples\azure-cosmos-db-graph-java-getting-started"
    ```

2. V okně terminálu Git pomocí následujícího příkazu nainstalujte požadované balíčky Java.

   ```git
   mvn package
   ```

3. V okně terminálu Git pomocí následujícího příkazu spusťte aplikaci v Javě.
    
    ```git
    mvn exec:java -D exec.mainClass=GetStarted.Program
    ```

    V okně terminálu se zobrazí vrcholy, které se přidávají do grafu. 
    
    Pokud dochází k chybám časového limitu, zkontrolujte, jestli jste v části [Aktualizace informací o připojení](#update-your-connection-information) správně aktualizovali informace o připojení, a zkuste poslední příkaz spustit znovu. 
    
    Po zastavení programu vyberte Enter a pak přepněte zpátky na Azure Portal v internetovém prohlížeči. 

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>Kontrola a přidání ukázkových dat

Teď můžete přejít zpět do Průzkumníku dat a zobrazit vrcholy přidané do grafu a přidat další datové body.

1. Vyberte **Průzkumník dat**, rozbalte **Sample-Graph**, vyberte **graf**a pak vyberte **použít filtr**. 

   ![Vytváření nových dokumentů v Průzkumníku dat na portálu Azure Portal](./media/create-graph-java/azure-cosmosdb-data-explorer-expanded.png)

2. V seznamu **Výsledky** si všimněte nových uživatelů přidaných do grafu. Vyberte **ben** a všimněte si, že tento uživatel je propojený s uživatelem robin. Vrcholy můžete přesouvat přetahováním, přibližovat a oddalovat můžete pomocí kolečka myši a zvětšit plochu grafu můžete pomocí obousměrné šipky. 

   ![Nové vrcholy v grafu v Průzkumníku dat na webu Azure Portal](./media/create-graph-java/azure-cosmosdb-graph-explorer-new.png)

3. Teď přidáme několik nových uživatelů. Vyberte **nový vrchol** a přidejte do grafu data.

   ![Vytváření nových dokumentů v Průzkumníku dat na portálu Azure Portal](./media/create-graph-java/azure-cosmosdb-data-explorer-new-vertex.png)

4. Do pole popisku zadejte *person* (osoba).

5. Vyberte **Přidat vlastnost** a přidejte každou z následujících vlastností. Všimněte si, že pro každou osobu v grafu můžete vytvořit jedinečné vlastnosti. Vyžaduje se pouze klíč id.

    key|hodnota|Poznámky
    ----|----|----
    id|ashley|Jedinečný identifikátor pro vrchol. Pokud identifikátor nezadáte, vygeneruje se pro vás.
    gender (pohlaví)|female (žena)| 
    tech (technologie) | java | 

    > [!NOTE]
    > V tomto rychlém startu vytvoříte kolekci bez oddílů. Pokud však vytvoříte dělenou kolekci zadáním klíče oddílu při vytváření kolekce, pak každý nový vrchol bude muset zahrnovat klíč oddílu jako klíč. 

6. Vyberte **OK**. Možná bude nutné zvětšit obrazovku, aby se tlačítko **OK** zobrazilo v dolní části obrazovky.

7. Znovu vyberte **nový vrchol** a přidejte dalšího nového uživatele. 

8. Zadejte popisek *person* (osoba).

9. Vyberte **Přidat vlastnost** a přidejte každou z následujících vlastností:

    key|hodnota|Poznámky
    ----|----|----
    id|rakesh|Jedinečný identifikátor pro vrchol. Pokud identifikátor nezadáte, vygeneruje se pro vás.
    gender (pohlaví)|male (muž)| 
    school (škola)|MIT| 

10. Vyberte **OK**. 

11. ClSelectck tlačítko **použít filtr** s výchozím `g.V()` filtrem pro zobrazení všech hodnot v grafu. Teď se v seznamu **Výsledky** zobrazí všichni uživatelé. 

    S přidáváním dalších dat můžete pomocí filtrů omezit výsledky. Průzkumník dat ve výchozím nastavení pomocí filtru `g.V()` načte všechny vrcholy v grafu. Můžete ho změnit na jiný [dotaz grafu](tutorial-query-graph.md), třeba `g.V().count()`, který vrátí počet všech vrcholů v grafu ve formátu JSON. Pokud jste filtr změnili, změňte filtr zpět na `g.V()` a vyberte **použít filtr** pro zobrazení všech výsledků znovu.

12. Teď můžete propojit uživatele rakesh a ashley. V seznamu **výsledků** ověřte, že je vybraná možnost **Ashley** , ![a potom v grafu klikněte na tlačítko změnit cíl](./media/create-graph-java/edit-pencil-button.png) vrcholu v grafu vedle **cílů** na pravé straně. Možná budete muset rozšířit okno, aby se tlačítko zobrazilo.

    ![Změna cíle vrcholu v grafu](./media/create-graph-java/azure-cosmosdb-data-explorer-edit-target.png)

13. Do pole **cíl** zadejte *Rakesh*, do pole **popisek hrany** zadejte *ví*a potom zaškrtněte políčko.

    ![Přidání propojení mezi uživateli ashley a rakesh v Průzkumníku dat](./media/create-graph-java/azure-cosmosdb-data-explorer-set-target.png)

14. Teď vyberte ze seznamu výsledků uživatele **rakesh** a zobrazí se propojení mezi uživateli ashley a rakesh. 

    ![Dva propojené vrcholy v Průzkumníku dat](./media/create-graph-java/azure-cosmosdb-graph-explorer.png)

    Tím je dokončená část tohoto kurzu věnovaná vytváření prostředků. Můžete pokračovat přidáním vrcholů do grafu, úpravou existujících vrcholů nebo změnou dotazů. Teď si projdeme metriky, které služba Azure Cosmos DB nabízí, a pak vyčistíme prostředky. 

## <a name="review-slas-in-the-azure-portal"></a>Ověření podmínek SLA na portálu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste se seznámili s postupem vytvoření účtu databáze Azure Cosmos, vytvoření grafu pomocí Průzkumníku dat a spuštění aplikace. Teď můžete pomocí konzoly Gremlin vytvářet složitější dotazy a implementovat účinnou logiku procházení grafů. 

> [!div class="nextstepaction"]
> [Dotazování pomocí konzoly Gremlin](tutorial-query-graph.md)

