---
title: Vytvoření databáze dokumentů Azure Cosmos DB pomocí Javy | Dokumentace Microsoftu
description: Tento článek představuje vzorový kód Java, který můžete použít k připojení a dotazování služby Azure Cosmos DB přes rozhraní SQL API.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.custom: quick start connect, mvc, devcenter
ms.devlang: java
ms.topic: quickstart
ms.date: 03/26/2018
ms.author: sngun
ms.openlocfilehash: 85a7a6f5b1224c732f5a385789aef13e1d7bd1db
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46961242"
---
# <a name="azure-cosmos-db-create-a-document-database-using-java-and-the-azure-portal"></a>Azure Cosmos DB: Vytvoření databáze dokumentů pomocí Javy a webu Azure Portal

> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Azure Cosmos DB je globálně distribuovaná databázová služba Microsoftu pro více modelů. Pomocí Azure Cosmos DB můžete rychle vytvořit a dotazovat spravované databáze dokumentů, tabulek a grafů.

V tomto rychlém startu se vytvoří databáze dokumentů pomocí nástrojů pro [SQL API](sql-api-introduction.md) služby Azure Cosmos DB na webu Azure Portal. V tomto rychlém startu se také dozvíte, jak rychle vytvořit konzolovou aplikaci Java pomocí [rozhraní SQL Java API](sql-api-sdk-java.md). Pokyny v tomto rychlém startu platí pro všechny operační systémy, které podporují Javu. Po dokončení tohoto rychlého startu budete vědět, jak vytvořit a upravit prostředky databáze dokumentů v uživatelském rozhraní nebo programově podle toho, čemu dáváte přednost.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Navíc platí: 

* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
    * Na Ubuntu nainstalujte sadu JDK spuštěním příkazu `apt-get install default-jdk`.
    * Nezapomeňte nastavit proměnnou prostředí JAVA_HOME tak, aby odkazovala na složku, ve které je sada JDK nainstalovaná.
* [Stáhněte](http://maven.apache.org/download.cgi) a [nainstalujte](http://maven.apache.org/install.html) binární archiv [Maven](http://maven.apache.org/).
    * Na Ubuntu můžete Maven nainstalovat spuštěním příkazu `apt-get install maven`.
* [Git](https://www.git-scm.com/)
    * Na Ubuntu můžete Git nainstalovat spuštěním příkazu `sudo apt-get install git`.

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

Než budete moci vytvořit databázi dokumentů, je potřeba pomocí služby Azure Cosmos DB vytvořit účet rozhraní SQL API.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>Přidání kolekce

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Přidání ukázkových dat

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Dotazování dat

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Teď přejděme k práci s kódem. Naklonujeme aplikaci SQL API z GitHubu, nastavíme připojovací řetězec a spustíme ji. Přesvědčíte se, jak snadno se pracuje s daty prostřednictvím kódu programu. 

1. Otevřete příkazový řádek, vytvořte novou složku git-samples a potom příkazový řádek zavřete.

    ```bash
    md "C:\git-samples"
    ```

2. Otevřete okno terminálu Git, například Git Bash, a pomocí příkazu `cd` přejděte do nové složky, do které chcete nainstalovat ukázkovou aplikaci. 

    ```bash
    cd "C:\git-samples"
    ```

3. Ukázkové úložiště naklonujete spuštěním následujícího příkazu. Tento příkaz vytvoří na vašem počítači kopii ukázkové aplikace.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-documentdb-java-getting-started.git
    ```

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. Pokud chcete zjistit, jak se v kódu vytvářejí prostředky databáze, můžete si prohlédnout následující fragmenty kódu. Jinak můžete přeskočit přímo k části [Aktualizace informací o připojení](#update-your-connection-string). 

Následující fragmenty kódu pocházejí ze souboru C:\git-samples\azure-cosmos-db-documentdb-java-getting-started\src\GetStarted\Program.java.

* Inicializace klienta `DocumentClient`. [DocumentClient](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client) představuje logickou reprezentaci databázové služby Azure Cosmos DB na straně serveru. Tento klient slouží ke konfiguraci a provádění požadavků na službu. Části `FILLME` tohoto kódu se aktualizují později v tomto rychlém startu.

    ```java
    this.client = new DocumentClient("https://FILLME.documents.azure.com",
            "FILLME", 
            new ConnectionPolicy(),
            ConsistencyLevel.Session);
    ```

* Vytvoření [databáze](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._database).

    ```java
    Database database = new Database();
    database.setId(databaseName);
    
    this.client.createDatabase(database, null);
    ```

* Vytvoření kolekce [DocumentCollection](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_collection).

    ```java
    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId(collectionName);

    ...

    this.client.createCollection(databaseLink, collectionInfo, requestOptions);
    ```

* Vytvoření dokumentu pomocí metody [createDocument](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.createdocument).

    ```java
    // Any Java object within your code can be serialized into JSON and written to Azure Cosmos DB
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen");
    // More properties

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    this.client.createDocument(collectionLink, family, new RequestOptions(), true);
    ```

* Příkazy jazyka SQL přes JSON se provádějí pomocí metody [queryDocuments](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb._document_client.querydocuments).

    ```java
    FeedOptions queryOptions = new FeedOptions();
    queryOptions.setPageSize(-1);
    queryOptions.setEnableCrossPartitionQuery(true);

    String collectionLink = String.format("/dbs/%s/colls/%s", databaseName, collectionName);
    FeedResponse<Document> queryResults = this.client.queryDocuments(
        collectionLink,
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", queryOptions);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }
    ```    

## <a name="update-your-connection-string"></a>Aktualizace připojovacího řetězce

Teď se vraťte zpátky na portál Azure Portal, kde najdete informace o připojovacím řetězci, a zkopírujte je do aplikace. Tím aplikaci umožníte komunikovat s hostovanou databází.

1. Na webu [Azure Portal](http://portal.azure.com/) klikněte na **Klíče**. 

    Pomocí tlačítka pro kopírování na pravé straně obrazovky zkopírujte horní hodnotu identifikátoru URI.

    ![Zobrazení a zkopírování přístupového klíče na portálu Azure na stránce Klíče](./media/create-sql-api-java/keys.png)

2. Otevřete soubor `Program.java` ve složce C:\git-samples\azure-cosmos-db-documentdb-java-getting-started\src\GetStarted. 

3. Vložte hodnotu identifikátoru URI z portálu místo `https://FILLME.documents.azure.com` na řádku 45.

4. Přejděte zpět na portál a zkopírujte hodnotu PRIMÁRNÍHO KLÍČE, jak je znázorněno na snímku obrazovky. Vložte hodnotu PRIMÁRNÍHO KLÍČE z portálu místo `FILLME` na řádku 46.

    Metoda getStartedDemo by teď měla vypadat nějak takto: 
    
    ```java
    private void getStartedDemo() throws DocumentClientException, IOException {
        this.client = new DocumentClient("https://youraccountname.documents.azure.com:443/",
                "your-primary-key...RJhQrqQ5QQ==", 
                new ConnectionPolicy(),
                ConsistencyLevel.Session);
    ```

5. Uložte soubor Program.java.

## <a name="run-the-app"></a>Spuštění aplikace

1. V okně terminálu Git přejděte příkazem `cd` do složky azure-cosmos-db-documentdb-java-getting-started.

    ```git
    cd "C:\git-samples\azure-cosmos-db-documentdb-java-getting-started"
    ```

2. V okně terminálu Git pomocí následujícího příkazu nainstalujte požadované balíčky Java.

    ```
    mvn package
    ```

3. V okně terminálu Git pomocí následujícího příkazu spusťte aplikaci v Javě.

    ```
    mvn exec:java -D exec.mainClass=GetStarted.Program
    ```

    V okně terminálu se zobrazí oznámení o vytvoření databáze FamilyDB. 
    
4. Stisknutím jakékoli klávesy vytvořte databázi a pak opět stisknutím jakékoli klávesy vytvořte kolekci. 

    Na konci programu se všechny prostředky odstraní, takže ve svém prohlížeči přepněte zpět do Průzkumníka dat, ve kterém se teď zobrazí databáze FamilyDB a kolekce FamilyCollection.

5. Přepněte do okna konzoly a stisknutím jakékoli klávesy vytvořte první dokument a pak opět stisknutím jakékoli klávesy vytvořte druhý dokument. Pak přepněte zpět do Průzkumníka dat a zobrazte je. 

6. Stisknutím jakékoli klávesy spusťte dotaz a podívejte se na výstup v okně konzoly. 

7. Dalším stisknutím jakékoli klávesy prostředky odstraníte. Pokud chcete prostředky zachovat, můžete program ukončit stisknutím Ctrl + C v okně konzoly. Jinak stisknutím jakékoli klávesy odstraňte prostředky ze svého účtu, aby se vám neúčtovaly poplatky. 

    ![Výstup konzoly](./media/create-sql-api-java/console-output.png)


## <a name="review-slas-in-the-azure-portal"></a>Ověření podmínek SLA na portálu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se seznámili s postupem vytvoření účtu Azure Cosmos DB, databáze dokumentů a kolekce pomocí Průzkumníku dat a spuštění aplikace, která to samé udělá programově. Teď můžete do kolekce Azure Cosmos DB importovat další data. 

> [!div class="nextstepaction"]
> [Importování dat do služby Azure Cosmos DB](import-data.md)


