---
title: Aplikace Java s Azure Cosmos DB rozhraní API Cassandra pomocí sady Java 4,0 SDK
description: V tomto rychlém startu se dozvíte, jak pomocí Azure Cosmos DB rozhraní API Cassandra vytvořit profilovou aplikaci s Azure Portal a Java 4,0 SDK.
ms.service: cosmos-db
author: TheovanKraay
ms.author: thvankra
ms.subservice: cosmosdb-cassandra
ms.devlang: java
ms.topic: quickstart
ms.date: 05/18/2020
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: ee30af3f07c8b350393822f01833feec6e3b52e2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93099806"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-cassandra-api-data-v4-driver"></a>Rychlý Start: Vytvoření aplikace Java pro správu dat rozhraní API Cassandra Azure Cosmos DB (v4 Driver)
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
> 

V tomto rychlém startu vytvoříte účet Azure Cosmos DB rozhraní API Cassandra a použijete Cassandra aplikaci Java naklonovaná z GitHubu k vytvoření databáze Cassandra a kontejneru pomocí [ovladačů v4. x Apache Cassandra](https://github.com/datastax/java-driver/tree/4.x) pro Java. Azure Cosmos DB je databázová služba pro více modelů, která umožňuje rychle vytvářet a dotazovat databáze dokumentů, tabulek, klíčových hodnot a grafů s funkcemi globální distribuce a horizontálního škálování.

## <a name="prerequisites"></a>Předpoklady

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started-v4.git
    ```

## <a name="review-the-code"></a>Kontrola kódu

Tento krok je volitelný. Pokud vás zajímá, jak se pomocí kódu vytvoří prostředky databáze, můžete si prohlédnout následující fragmenty kódu. Jinak můžete přeskočit přímo k části [Aktualizace informací o připojení](#update-your-connection-string). Všechny tyto fragmenty kódu jsou pořízeny ze souboru *Src/Main/Java/com/Azure/cosmosdb/Cassandra/util/CassandraUtils. Java* .  

* `CqlSession`Připojení k Azure Cosmos DB rozhraní API Cassandra a vrátí relaci pro přístup ( `Cluster` objekt z ovladače v3 je nyní zastaralý). Cassandra hostitel, port, uživatelské jméno a heslo se nastaví pomocí stránky připojovací řetězec v Azure Portal.

    ```java
        this.session = CqlSession.builder().withSslContext(sc)
                .addContactPoint(new InetSocketAddress(cassandraHost, cassandraPort)).withLocalDatacenter(region)
                .withAuthCredentials(cassandraUsername, cassandraPassword).build();
    ```


Následující fragmenty kódu jsou ze souboru *Src/Main/Java/com/Azure/cosmosdb/Cassandra/úložiště/UserRepository. Java* .

* Vyřaďte místo na disku, pokud už existuje z předchozího běhu.

    ```java
    public void dropKeyspace() {
        String query = "DROP KEYSPACE IF EXISTS "+keyspace+"";
        session.execute(query);
        LOGGER.info("dropped keyspace '"+keyspace+"'");
    } 
    ```
* Vytvoří se nový prostor klíčů.

    ```java
    public void createKeyspace() {
        String query = "CREATE KEYSPACE "+keyspace+" WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 }";
        session.execute(query);
        LOGGER.info("Created keyspace '"+keyspace+"'");
    }
    ```

* Vytvoří se nová tabulka.

   ```java
    public void createTable() {
        String query = "CREATE TABLE "+keyspace+"."+table+" (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        session.execute(query);
        LOGGER.info("Created table '"+table+"'");
    }
   ```

* Entity uživatele jsou vloženy pomocí objektu připraveného příkazu.

    ```java
    public String prepareInsertStatement() {
        final String insertStatement = "INSERT INTO  "+keyspace+"."+table+" (user_id, user_name , user_bcity) VALUES (?,?,?)";
        return insertStatement;
    }

    public void insertUser(String preparedStatement, int id, String name, String city) {
        PreparedStatement prepared = session.prepare(preparedStatement);
        BoundStatement bound = prepared.bind(id, city, name).setIdempotent(true);
        session.execute(bound);
    }
    ```

* Získejte dotaz pro získání informací o všech uživatelích.

    ```java
    public void selectAllUsers() {
        final String query = "SELECT * FROM "+keyspace+"."+table+"";
        List<Row> rows = session.execute(query).all();

        for (Row row : rows) {
            LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
        }
    }
    ```

 * Dotaz pro získání informací o jednom uživateli

    ```java
    public void selectUser(int id) {
        final String query = "SELECT * FROM "+keyspace+"."+table+" where user_id = 3";
        Row row = session.execute(query).one();

        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
    }
    ```

## <a name="update-your-connection-string"></a>Aktualizace připojovacího řetězce

Teď se vraťte zpátky na portál Azure Portal, kde najdete informace o připojovacím řetězci, a zkopírujte je do aplikace. Podrobnosti o připojovacím řetězci umožňují vaší aplikaci komunikovat s hostovanou databází.

1. V Azure Cosmos DB účtu v [Azure Portal](https://portal.azure.com/)vyberte **připojovací řetězec** . 

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

7. Všimněte si, že budete muset pro kontaktní bod přidat výchozí oblast (např. `West US` ), např.

    `region=West US`

    Důvodem je, že ovladač v. 4x umožňuje párování jenom jednoho místního řadiče domény s kontaktním bodem. Pokud chcete přidat jinou oblast než výchozí (což je oblast, která byla zadána při prvním vytvoření Cosmos DBho účtu), budete muset při přidávání kontaktního bodu použít regionální příponu, např. `host-westus.cassandra.cosmos.azure.com` .

8. Uložte soubor *config. Properties* .

## <a name="run-the-java-app"></a>Spuštění aplikace v Javě

1. V okně terminálu Git přejděte pomocí příkazu `cd` do složky `azure-cosmosdb-cassandra-java-getting-started-v4`.

    ```git
    cd "C:\git-samples\azure-cosmosdb-cassandra-java-getting-started-v4"
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

4. Na portálu Azure Portal otevřete **Data Explorer** , abyste se mohli na tato nová data dotazovat, měnit je a pracovat s nimi. 

    :::image type="content" source="./media/create-cassandra-java/view-data-explorer-java-app.png" alt-text="Zobrazení a zkopírování uživatelského jména z webu Azure Portal, stránka Připojovací řetězec":::

## <a name="review-slas-in-the-azure-portal"></a>Ověření smluv SLA na webu Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit účet Azure Cosmos DB pomocí rozhraní API Cassandra a spustit aplikaci v jazyce Java Cassandra, která vytvoří databázi Cassandra a kontejner. Teď můžete do svého účtu Azure Cosmos DB importovat další data. 

> [!div class="nextstepaction"]
> [Import dat Cassandra do služby Azure Cosmos DB](cassandra-import-data.md)
