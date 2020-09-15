---
title: Aplikace Java s Azure Cosmos DB rozhraní API Cassandra pomocí sady Java 3,0 SDK
description: V tomto rychlém startu se dozvíte, jak pomocí Azure Cosmos DB rozhraní API Cassandra vytvořit profilovou aplikaci s Azure Portal a Java 3,0 SDK.
ms.service: cosmos-db
author: TheovanKraay
ms.author: thvankra
ms.subservice: cosmosdb-cassandra
ms.devlang: java
ms.topic: quickstart
ms.date: 05/18/2020
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 59f3bf1cfda7bf26f63c1ec1352a5a231ee07995
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90526778"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-cassandra-api-data-v3-driver"></a>Rychlý Start: Vytvoření aplikace Java pro správu dat rozhraní API Cassandra Azure Cosmos DB (ovladač v3)

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

V tomto rychlém startu vytvoříte účet Azure Cosmos DB rozhraní API Cassandra a použijete Cassandra aplikaci Java naklonovaná z GitHubu k vytvoření databáze Cassandra a kontejneru pomocí [ovladačů v3. x Apache Cassandra](https://github.com/datastax/java-driver/tree/3.x) pro Java. Azure Cosmos DB je databázová služba pro více modelů, která umožňuje rychle vytvářet a dotazovat databáze dokumentů, tabulek, klíčových hodnot a grafů s funkcemi globální distribuce a horizontálního škálování.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si ho zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Nebo [vyzkoušejte Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/) bez předplatného Azure.
- [Java Development Kit (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Najeďte `JAVA_HOME` proměnnou prostředí na složku, ve které je nainstalovaná JDK.
- [Binární archiv Maven](https://maven.apache.org/download.cgi) Na Ubuntu spusťte `apt-get install maven` instalaci Maven.
- [Git](https://www.git-scm.com/downloads). Na Ubuntu spusťte `sudo apt-get install git` instalaci Gitu.

## <a name="create-a-database-account"></a>Vytvoření účtu databáze

Než budete moct vytvořit databázi dokumentů, je potřeba pomocí služby Azure Cosmos DB vytvořit účet Cassandra.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Teď přejděme k práci s kódem. Teď naklonujeme aplikaci Cassandra z GitHubu, nastavíme připojovací řetězec a spustíme ji. Přesvědčíte se, jak snadno se pracuje s daty prostřednictvím kódu programu. 

1. Otevřete příkazový řádek. Vytvořte novou složku s názvem `git-samples`. Pak zavřete příkazový řádek.

    ```bash
    md "C:\git-samples"
    ```

2. Otevřete okno terminálu Git, například Git Bash, a pomocí příkazu `cd` přejděte do nové složky, do které chcete nainstalovat ukázkovou aplikaci.

    ```bash
    cd "C:\git-samples"
    ```

3. Ukázkové úložiště naklonujete spuštěním následujícího příkazu. Tento příkaz vytvoří na vašem počítači kopii ukázkové aplikace.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started.git
    ```

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. Pokud vás zajímá, jak se pomocí kódu vytvoří prostředky databáze, můžete si prohlédnout následující fragmenty kódu. Jinak můžete přeskočit přímo k části [Aktualizace informací o připojení](#update-your-connection-string). Všechny tyto fragmenty kódu jsou pořízeny ze souboru *Src/Main/Java/com/Azure/cosmosdb/Cassandra/util/CassandraUtils. Java* .  

* Jsou nastavené možnosti hostitel Cassandra, port, uživatelské jméno, heslo a TLS/SSL. Informace o připojovacím řetězci pocházejí ze stránky připojovacího řetězce na webu Azure Portal.

   ```java
   cluster = Cluster.builder().addContactPoint(cassandraHost).withPort(cassandraPort).withCredentials(cassandraUsername, cassandraPassword).withSSL(sslOptions).build();
   ```

* Příkaz `cluster` se připojí k rozhraní Cassandra API služby Azure Cosmos DB a vrátí relaci pro přístup.

    ```java
    return cluster.connect();
    ```

Následující fragmenty kódu jsou ze souboru *Src/Main/Java/com/Azure/cosmosdb/Cassandra/úložiště/UserRepository. Java* .

* Vytvořte nový prostor klíčů.

    ```java
    public void createKeyspace() {
        final String query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {'class': 'SimpleStrategy', 'replication_factor': '3' } ";
        session.execute(query);
        LOGGER.info("Created keyspace 'uprofile'");
    }
    ```

* Vytvořte novou tabulku.

   ```java
   public void createTable() {
        final String query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        session.execute(query);
        LOGGER.info("Created table 'user'");
   }
   ```

* Vložte entity uživatelů pomocí objektu připravených příkazů.

    ```java
    public PreparedStatement prepareInsertStatement() {
        final String insertStatement = "INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (?,?,?)";
        return session.prepare(insertStatement);
    }

    public void insertUser(PreparedStatement statement, int id, String name, String city) {
        BoundStatement boundStatement = new BoundStatement(statement);
        session.execute(boundStatement.bind(id, name, city));
    }
    ```

* Použijte dotaz pro získání informací o všech uživatelích.

    ```java
   public void selectAllUsers() {
        final String query = "SELECT * FROM uprofile.user";
        List<Row> rows = session.execute(query).all();

        for (Row row : rows) {
            LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
        }
    }
    ```

* Použijte dotaz pro získání informací o jednom uživateli.

    ```java
    public void selectUser(int id) {
        final String query = "SELECT * FROM uprofile.user where user_id = 3";
        Row row = session.execute(query).one();

        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
    }
    ```

## <a name="update-your-connection-string"></a>Aktualizace připojovacího řetězce

Teď se vraťte zpátky na portál Azure Portal, kde najdete informace o připojovacím řetězci, a zkopírujte je do aplikace. Podrobnosti o připojovacím řetězci umožňují vaší aplikaci komunikovat s hostovanou databází.

1. V Azure Cosmos DB účtu v [Azure Portal](https://portal.azure.com/)vyberte **připojovací řetězec**. 

    :::image type="content" source="./media/create-cassandra-java/copy-username-connection-string-azure-portal.png" alt-text="Zobrazení a zkopírování uživatelského jména z webu Azure Portal, stránka Připojovací řetězec":::

2. Pomocí :::image type="icon" source="./media/create-cassandra-java/copy-button-azure-portal.png"::: tlačítka na pravé straně obrazovky Zkopírujte hodnotu kontaktní bod. 

3. Otevřete soubor *config. Properties* ze složky *C:\git-samples\azure-cosmosdb-Cassandra-Java-Getting-started\java-examples\src\main\resources* . 

3. Vložte hodnotu KONTAKTNÍ BOD z portálu místo `<Cassandra endpoint host>` na řádku 2.

    Řádek 2 *souboru config. Properties* by teď měl vypadat nějak podobně jako 

    `cassandra_host=cosmos-db-quickstart.cassandra.cosmosdb.azure.com`

3. Vraťte se na portál a zkopírujte hodnotu uživatelské jméno. Vložte hodnotu UŽIVATELSKÉ JMÉNO z portálu místo `<cassandra endpoint username>` na řádku 4.

    Řádek 4 *souboru config. Properties* by teď měl vypadat nějak podobně jako 

    `cassandra_username=cosmos-db-quickstart`

4. Vraťte se na portál a zkopírujte hodnotu heslo. Vložte hodnotu HESLO z portálu místo `<cassandra endpoint password>` na řádku 5.

    Řádek 5 *souboru config. Properties* by teď měl vypadat nějak podobně jako 

    `cassandra_password=2Ggkr662ifxz2Mg...==`

5. Pokud na řádku 6 chcete použít konkrétní certifikát TLS/SSL, nahraďte ho `<SSL key store file location>` umístěním certifikátu TLS/SSL. Pokud není uvedená hodnota, použije se certifikát JDK nainstalovaný v <JAVA_HOME>/jre/lib/security/cacerts. 

6. Pokud jste změnili řádek 6 na použití konkrétního certifikátu TLS/SSL, aktualizujte řádek 7 na použití hesla pro tento certifikát. 

7. Uložte soubor *config. Properties* .

## <a name="run-the-java-app"></a>Spuštění aplikace v Javě

1. V okně terminálu Git přejděte pomocí příkazu `cd` do složky `azure-cosmosdb-cassandra-java-getting-started`.

    ```git
    cd "C:\git-samples\azure-cosmosdb-cassandra-java-getting-started"
    ```

2. V okně terminálu Git vygenerujte pomocí následujícího příkazu soubor `cosmosdb-cassandra-examples.jar`.

    ```git
    mvn clean install
    ```

3. V okně terminálu Git pomocí následujícího příkazu spusťte aplikaci v Javě.

    ```git
    java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
    ```

    Okno terminálu zobrazí oznámení o vytvoření prostoru klíčů a tabulky. Potom vybere a vrátí všechny uživatele v tabulce, zobrazí výstup a potom vybere řádek podle ID a zobrazí příslušnou hodnotu.  

    Stisknutím kombinace kláves CTRL + C ukončete provádění programu a zavřete okno konzoly.

4. Na portálu Azure Portal otevřete **Data Explorer**, abyste se mohli na tato nová data dotazovat, měnit je a pracovat s nimi. 

    :::image type="content" source="./media/create-cassandra-java/view-data-explorer-java-app.png" alt-text="Zobrazení dat v Průzkumník dat-Azure Cosmos DB":::

## <a name="review-slas-in-the-azure-portal"></a>Ověření smluv SLA na webu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit účet Azure Cosmos DB pomocí rozhraní API Cassandra a spustit aplikaci v jazyce Java Cassandra, která vytvoří databázi Cassandra a kontejner. Teď můžete do svého účtu Azure Cosmos DB importovat další data. 

> [!div class="nextstepaction"]
> [Import dat Cassandra do služby Azure Cosmos DB](cassandra-import-data.md)
