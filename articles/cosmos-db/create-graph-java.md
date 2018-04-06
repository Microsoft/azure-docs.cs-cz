---
title: Vytvoření databáze grafu Azure Cosmos DB pomocí Javy | Dokumentace Microsoftu
description: Představuje ukázku kódu Java, který můžete použít k připojení a dotazování dat grafu Azure Cosmos DB pomocí konzoly Gremlin.
services: cosmos-db
documentationcenter: ''
author: luisbosquez
manager: jhubbard
editor: ''
ms.assetid: daacbabf-1bb5-497f-92db-079910703046
ms.service: cosmos-db
ms.custom: quick start connect, mvc
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/26/2018
ms.author: lbosq
ms.openlocfilehash: a5c3a0cdef488e4c4788a22976d78db72bdd55fc
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-create-a-graph-database-using-java-and-the-azure-portal"></a>Azure Cosmos DB: Vytvoření databáze grafu pomocí Javy a webu Azure Portal

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Pomocí Azure Cosmos DB můžete rychle vytvořit a dotazovat spravované databáze dokumentů, tabulek a grafů. 

V tomto rychlém startu se vytvoří jednoduchá databáze grafů pomocí nástrojů pro Azure Cosmos DB na webu Azure Portal. V tomto rychlém startu se také dozvíte, jak rychle vytvořit konzolovou aplikaci Java, která používá databázi grafu, pomocí ovladače OSS [Apache TinkerPop](http://tinkerpop.apache.org/). Pokyny v tomto rychlém startu platí pro všechny operační systémy, které podporují Javu. Tento rychlý start vás seznámí s vytvářením a upravováním grafů v uživatelském rozhraní nebo programově podle toho, čemu dáváte přednost. 

## <a name="prerequisites"></a>Požadavky
[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Navíc platí:

* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
    * Na Ubuntu nainstalujte sadu JDK spuštěním příkazu `apt-get install default-jdk`.
    * Nezapomeňte nastavit proměnnou prostředí JAVA_HOME tak, aby odkazovala na složku, ve které je sada JDK nainstalovaná.
* [Stáhněte](http://maven.apache.org/download.cgi) a [nainstalujte](http://maven.apache.org/install.html) binární archiv [Maven](http://maven.apache.org/).
    * Na Ubuntu můžete Maven nainstalovat spuštěním příkazu `apt-get install maven`.
* [Git](https://www.git-scm.com/)
    * Na Ubuntu můžete Git nainstalovat spuštěním příkazu `sudo apt-get install git`.

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

Než budete moct vytvořit databázi grafu, je potřeba pomocí služby Azure Cosmos DB vytvořit účet databáze Gremlin (Graph).

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Přidání grafu

Teď můžete pomocí nástroje Průzkumník dat na webu Azure Portal vytvořit databázi grafu. 

1. Klikněte na **Průzkumník dat** > **Nový graf**.

    Úplně vpravo se zobrazí oblast **Přidat graf**. Pokud ji nevidíte, možná se budete muset posunout doprava.

    ![Průzkumník dat na portálu Azure – stránka Přidat graf](./media/create-graph-java/azure-cosmosdb-data-explorer-graph.png)

2. Na stránce **Přidat graf** zadejte nastavení pro nový graf.

    Nastavení|Navrhovaná hodnota|Popis
    ---|---|---
    ID databáze|sample-database|Jako název nové databáze zadejte *sample-database*. Názvy databází musí mít délku 1 až 255 znaků a nesmí obsahovat znaky `/ \ # ?` ani koncové mezery.
    ID grafu|sample-graph|Jako název nové kolekce zadejte *sample-graph*. Názvy grafů mají stejné požadavky na znaky jako ID databází.
    Kapacita úložiště|Pevná (10 GB)|Nechte zadanou výchozí hodnotu **Pevná (10 GB)**. Tato hodnota je kapacita úložiště databáze.
    Propustnost|400 RU/s|Změňte propustnost na 400 jednotek žádostí za sekundu (RU/s). Pokud budete chtít snížit latenci, můžete propustnost později navýšit.

3. Po vyplnění formuláře klikněte na **OK**.

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Teď přejděme k práci s kódem. Naklonujeme aplikaci Graph API z GitHubu, nastavíme připojovací řetězec a spustíme ji. Přesvědčíte se, jak snadno se pracuje s daty prostřednictvím kódu programu.  

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

* Pomocí metody `client.submit` se provede série kroků konzoly Gremlin.

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

1. Na [portálu Azure](http://portal.azure.com/) klikněte na **Klíče**. 

    Zkopírujte první část hodnoty identifikátoru URI.

    ![Zobrazení a zkopírování přístupového klíče na portálu Azure na stránce Klíče](./media/create-graph-java/keys.png)
2. Otevřete soubor src/remote.yaml a vložte jedinečnou hodnotu ID místo `$name$` v části `hosts: [$name$.graphs.azure.com]`.

    Řádek 1 souboru remote.yaml by teď měl vypadat nějak takto: 

    `hosts: [test-graph.graphs.azure.com]`

3. V `endpoint` změňte `graphs` na `gremlin.cosmosdb`. (Pokud jste účet databáze grafů vytvořili před 20. prosincem 2017, neprovádějte žádné změny hodnoty koncového bodu a pokračujte dalším krokem.)

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

   ```
   mvn package
   ```

3. V okně terminálu Git pomocí následujícího příkazu spusťte aplikaci v Javě.
    
    ```
    mvn exec:java -D exec.mainClass=GetStarted.Program
    ```

    V okně terminálu se zobrazí vrcholy, které se přidávají do grafu. 
    
    Pokud dochází k chybám časového limitu, zkontrolujte, jestli jste v části [Aktualizace informací o připojení](#update-your-connection-information) správně aktualizovali informace o připojení, a zkuste poslední příkaz spustit znovu. 
    
    Po zastavení programu stiskněte Enter a pak v internetovém prohlížeči přejděte zpátky na portál Azure. 

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>Kontrola a přidání ukázkových dat

Teď můžete přejít zpět do Průzkumníku dat a zobrazit vrcholy přidané do grafu a přidat další datové body.

1. Klikněte na **Průzkumník dat**, rozbalte **sample-graph**, klikněte na **Graf** a potom klikněte na **Použít filtr**. 

   ![Vytváření nových dokumentů v Průzkumníku dat na portálu Azure Portal](./media/create-graph-java/azure-cosmosdb-data-explorer-expanded.png)

2. V seznamu **Výsledky** si všimněte nových uživatelů přidaných do grafu. Vyberte uživatele **ben** a všimněte si, že je propojený s uživatelem robin. Vrcholy můžete přesouvat přetahováním, přibližovat a oddalovat můžete pomocí kolečka myši a zvětšit plochu grafu můžete pomocí obousměrné šipky. 

   ![Nové vrcholy v grafu v Průzkumníku dat na webu Azure Portal](./media/create-graph-java/azure-cosmosdb-graph-explorer-new.png)

3. Teď přidáme několik nových uživatelů. Klikněte na tlačítko **Nový vrchol** a přidejte do grafu data.

   ![Vytváření nových dokumentů v Průzkumníku dat na portálu Azure Portal](./media/create-graph-java/azure-cosmosdb-data-explorer-new-vertex.png)

4. Do pole popisku zadejte *person* (osoba).

5. Kliknutím na **Přidat vlastnost** přidejte následující vlastnosti. Všimněte si, že pro každou osobu v grafu můžete vytvořit jedinečné vlastnosti. Vyžaduje se pouze klíč id.

    key|hodnota|Poznámky
    ----|----|----
    id|ashley|Jedinečný identifikátor pro vrchol. Pokud identifikátor nezadáte, vygeneruje se pro vás.
    gender (pohlaví)|female (žena)| 
    tech (technologie) | java | 

    > [!NOTE]
    > V tomto rychlém startu vytváříme kolekci bez oddílů. Pokud však vytvoříte dělenou kolekci zadáním klíče oddílu při vytváření kolekce, pak každý nový vrchol bude muset zahrnovat klíč oddílu jako klíč. 

6. Klikněte na **OK**. Možná bude nutné zvětšit obrazovku, aby se tlačítko **OK** zobrazilo v dolní části obrazovky.

7. Znovu klikněte na **Nový vrchol** a přidejte dalšího nového uživatele. 

8. Zadejte popisek *person* (osoba).

9. Kliknutím na **Přidat vlastnost** přidejte následující vlastnosti:

    key|hodnota|Poznámky
    ----|----|----
    id|rakesh|Jedinečný identifikátor pro vrchol. Pokud identifikátor nezadáte, vygeneruje se pro vás.
    gender (pohlaví)|male (muž)| 
    school (škola)|MIT| 

10. Klikněte na **OK**. 

11. Klikněte na tlačítko **Použít filtr** s výchozím filtrem `g.V()` a zobrazte v grafu všechny hodnoty. Teď se v seznamu **Výsledky** zobrazí všichni uživatelé. 

    S přidáváním dalších dat můžete pomocí filtrů omezit výsledky. Průzkumník dat ve výchozím nastavení pomocí filtru `g.V()` načte všechny vrcholy v grafu. Můžete ho změnit na jiný [dotaz grafu](tutorial-query-graph.md), třeba `g.V().count()`, který vrátí počet všech vrcholů v grafu ve formátu JSON. Pokud jste filtr změnili, změňte ho zpátky na `g.V()`, klikněte na **Použít filtr** a znovu zobrazte všechny výsledky.

12. Teď můžeme propojit uživatele rakesh a ashley. Ujistěte se, že v seznamu **Výsledky** je vybraný uživatel **ashley**, a potom klikněte na ![Změna cíle vrcholu v grafu](./media/create-graph-java/edit-pencil-button.png) vedle položky **Cíle** vpravo dole. Možná budete muset rozšířit okno, aby se tlačítko zobrazilo.

   ![Změna cíle vrcholu v grafu](./media/create-graph-java/azure-cosmosdb-data-explorer-edit-target.png)

13. Do pole **Cíl** zadejte *rakesh*, do pole **Popisek hrany** zadejte *knows* (zná) a potom klikněte na symbol zaškrtnutí.

   ![Přidání propojení mezi uživateli ashley a rakesh v Průzkumníku dat](./media/create-graph-java/azure-cosmosdb-data-explorer-set-target.png)

14. Teď vyberte ze seznamu výsledků uživatele **rakesh** a zobrazí se propojení mezi uživateli ashley a rakesh. 

   ![Dva propojené vrcholy v Průzkumníku dat](./media/create-graph-java/azure-cosmosdb-graph-explorer.png)

   Tím je dokončená část tohoto kurzu věnovaná vytváření prostředků. Můžete pokračovat přidáním vrcholů do grafu, úpravou existujících vrcholů nebo změnou dotazů. Teď si projdeme metriky, které služba Azure Cosmos DB nabízí, a pak vyčistíme prostředky. 

## <a name="review-slas-in-the-azure-portal"></a>Ověření podmínek SLA na portálu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se seznámili s postupem vytvoření účtu databáze Azure Cosmos, vytvoření grafu pomocí Průzkumníku dat a spuštění aplikace. Teď můžete pomocí konzoly Gremlin vytvářet složitější dotazy a implementovat účinnou logiku procházení grafů. 

> [!div class="nextstepaction"]
> [Dotazování pomocí konzoly Gremlin](tutorial-query-graph.md)

