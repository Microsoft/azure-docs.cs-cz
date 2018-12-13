---
title: Vytvoření účtu rozhraní Cassandra API pomocí aplikace v Javě – Azure Cosmos DB
description: Tento článek ukazuje, jak vytvořit účet rozhraní API Cassandra, přidat databázi (označované také jako prostor klíčů) a tabulku k tomuto účtu pomocí aplikace v Javě.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
services: cosmos-db
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 0bff57d91a777619b825dacef5988dda010c794b
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53138836"
---
# <a name="tutorial-create-an-azure-cosmos-db-cassandra-api-account-by-using-a-java-application"></a>Kurz: Vytvoření účtu Azure Cosmos DB Cassandra API s použitím aplikace v Javě

Tento kurz popisuje způsob použití aplikace v Javě k vytvoření účtu rozhraní API Cassandra ve službě Azure Cosmos DB, přidání databáze (označované také jako prostor klíčů) a přidání tabulky. Aplikace v Javě využívá [ovladač Javy](https://github.com/datastax/java-driver) k vytvoření uživatelské databáze, která obsahuje podrobnosti, jako je ID uživatele, uživatelské jméno, město uživatele.  

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Vytvoření účtu databáze Cassandra
> * Získání připojovacího řetězce účtu
> * Vytvoření projektu a závislostí v Mavenu
> * Přidání databáze a tabulky
> * Spuštění aplikace

## <a name="prerequisites"></a>Požadavky 

* Pokud ještě nemáte předplatné Azure, vytvořte si  [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)  před tím, než začnete. Případně si můžete  [vyzkoušet službu Azure Cosmos DB zdarma](https://azure.microsoft.com/try/cosmosdb/)  bez předplatného Azure, poplatků a závazků. 

* Pořiďte si nejnovější verzi sady [Java Development Kit (JDK)](https://aka.ms/azure-jdks). 

* [Stáhněte si](https://maven.apache.org/download.cgi) a [nainstalujte](https://maven.apache.org/install.html) binární archiv [Maven](https://maven.apache.org/). 
  - Na Ubuntu můžete Maven nainstalovat spuštěním příkazu  `apt-get install maven` . 

## <a name="create-a-database-account"></a>Vytvoření účtu databáze 

1. Přihlaste se na  [Azure Portal](https://portal.azure.com/). 

2. Vyberte  **Vytvořit prostředek** > **Databáze** > **Azure Cosmos DB**. 

3. V podokně  **Nový účet**  zadejte nastavení nového účtu služby Azure Cosmos DB. 

   |Nastavení   |Navrhovaná hodnota  |Popis  |
   |---------|---------|---------|
   |ID   |   Zadejte jedinečný název.    | Zadejte jedinečný název, který identifikuje tento účet služby Azure Cosmos DB. <br/><br/>Vzhledem k tomu, že se váš kontaktní bod vytvoří připojením řetězce cassandra.cosmosdb.azure.com k ID, které zadáte, použijte jedinečné, ale snadno rozpoznatelné ID.         |
   |Rozhraní API    |  Cassandra   |  Rozhraní API určuje typ účtu, který se má vytvořit. <br/> Vyberte **Cassandra**, protože v tomto článku vytvoříte databázi širokých sloupců, která může být dotázána pomocí syntaxe CQL.  |
   |Předplatné    |  Vaše předplatné        |  Vyberte předplatné Azure, které chcete pro tento účet služby Azure Cosmos DB použít.        |
   |Skupina prostředků   | Zadejte název.    |  Vyberte  **Vytvořit nový** a zadejte název nové skupiny prostředků pro váš účet. V zájmu jednoduchosti můžete použít název, který se shoduje s vaším ID.    |
   |Umístění    |  Vyberte oblast nejbližší vašim uživatelům.    |  Vyberte zeměpisné umístění, ve kterém chcete účet služby Azure Cosmos DB hostovat. Použijte umístění, které je vašim uživatelům nejbližší, abyste jim zajistili nejrychlejší přístup k datům.    |

   ![Vytvoření účtu pomocí portálu](./media/create-cassandra-api-account-java/create-account.png)

4. V dalším kroku vyberte  **Vytvořit**. <br/>Vytvoření účtu trvá několik minut. Po vytvoření prostředku uvidíte oznámení **Nasazení bylo úspěšné** v pravém rohu portálu.

## <a name="get-the-connection-details-of-your-account"></a>Získání podrobností o připojení vašeho účtu  

Opatřete si informace o připojovacím řetězci z webu Azure Portal a zkopírujte je do konfiguračního souboru Java. Připojovací řetězec umožňuje vaší aplikaci komunikovat s hostovanou databází. 

1. Na webu  [Azure Portal](https://portal.azure.com/) přejděte do svého účtu služby Cosmos DB. 

2. Otevřete podokno  **Připojovací řetězec**.  

3. Zkopírujte hodnoty **KONTAKTNÍ BOD**, **PORT**, **UŽIVATELSKÉ JMÉNO** a **PRIMÁRNÍ HESLO** pro použití v dalších krocích.

## <a name="create-maven-project-dependencies-and-utility-classes"></a>Vytvoření projektu Maven, závislostí a užitkových tříd 

Ukázkový projekt Java, který používáte v tomto článku, je hostovaný na GitHubu. Můžete si ho stáhnout z úložiště [azure-cosmos-db-cassandra-java-getting-started](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started). 

Po stažení souborů aktualizujte informace o připojovacím řetězci v rámci souboru `java-examples\src\main\resources\config.properties` a potom ho spusťte.  

```java
cassandra_host=<FILLME_with_CONTACT POINT> 
cassandra_port = 10350 
cassandra_username=<FILLME_with_USERNAME> 
cassandra_password=<FILLME_with_PRIMARY PASSWORD> 
```

Případně můžete také sestavit ukázku od začátku.  

1. Z terminálu nebo příkazového řádku vytvořte nový projekt Maven s názvem Cassandra-demo. 

   ```bash
   mvn archetype:generate -DgroupId=com.azure.cosmosdb.cassandra -DartifactId=cassandra-demo -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false 
   ```
 
2. Najděte složku `cassandra-demo`. Pomocí textového editoru otevřete soubor `pom.xml`, který byl vygenerován. 

   Přidejte závislosti Cassandra a vytvořte moduly plug-in požadované vaším projektem, jak je znázorněno v souboru [pom.xml](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/pom.xml).  

3. Ve složce `cassandra-demo\src\main` vytvořte novou složku s názvem `resources`.  Ve složce prostředků přidejte soubory config.properties a log4j.properties:

   - Soubor [config.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/resources/config.properties) uloží koncový bod a klíčové hodnoty připojení rozhraní API Cassandra služby Azure Cosmos DB. 
   
   - Soubor [log4j.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/resources/log4j.properties) definuje úroveň protokolování vyžadovanou při interakci s rozhraním API Cassandra.  

4. Dále přejděte do složky `src/main/java/com/azure/cosmosdb/cassandra/`. Ve složce cassandra vytvořte další složku s názvem `utils`. Nová složka ukládá užitkové třídy potřebné pro připojení k účtu rozhraní API Cassandra. 

   Přidejte třídu [CassandraUtils](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java) k vytvoření clusteru a k otvírání a zavírání relací Cassandra. Cluster se připojí k rozhraní Cassandra API služby Azure Cosmos DB a vrátí relaci pro přístup. Pomocí třídy [Konfigurace](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/util/Configurations.java) si přečtěte informace o připojovacím řetězci ze souboru config.properties. 

5. Ukázka v Javě vytvoří databázi s informacemi o uživateli, jako je uživatelské jméno, ID uživatele, město uživatele. Budete muset definovat metody get a set pro přístup k podrobnostem o uživateli v hlavní funkci.
 
   Vytvořte třídu [User.java](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/User.java) v rámci složky `src/main/java/com/azure/cosmosdb/cassandra/` pomocí metod get a set. 

## <a name="add-a-database-and-a-table"></a>Přidání databáze a tabulky  

Tato část popisuje, jak přidat databázi (prostor klíčů) a tabulku pomocí jazyka Cassandra Query Language (CQL). Další informace o syntaxi CQL pro tyto příkazy najdete v syntaxi dotazu [vytvoření prostoru klíčů](https://docs.datastax.com/en/cql/3.3/cql/cql_reference/cqlCreateKeyspace.html) a [vytvoření tabulky](https://docs.datastax.com/en/cql/3.3/cql/cql_reference/cqlCreateTable.html#cqlCreateTable). 

1. Ve složce `src\main\java\com\azure\cosmosdb\cassandra` vytvořte novou složku s názvem `repository`. 

2. Dále vytvořte třídu Javy `UserRepository` a přidejte do ní následující kód: 

   ```java
   package com.azure.cosmosdb.cassandra.repository; 
   import java.util.List; 
   import com.datastax.driver.core.BoundStatement; 
   import com.datastax.driver.core.PreparedStatement; 
   import com.datastax.driver.core.Row; 
   import com.datastax.driver.core.Session; 
   import org.slf4j.Logger; 
   import org.slf4j.LoggerFactory; 
   
   /** 
    * Create a Cassandra session 
    */ 
   public class UserRepository { 
   
       private static final Logger LOGGER = LoggerFactory.getLogger(UserRepository.class); 
       private Session session; 
       public UserRepository(Session session) { 
           this.session = session; 
       } 
   
       /** 
       * Create keyspace uprofile in cassandra DB 
        */ 
   
       public void createKeyspace() { 
            final String query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 }"; 
           session.execute(query); 
           LOGGER.info("Created keyspace 'uprofile'"); 
       } 
   
       /** 
        * Create user table in cassandra DB 
        */ 
   
       public void createTable() { 
           final String query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)"; 
           session.execute(query); 
           LOGGER.info("Created table 'user'"); 
       } 
   } 
   ```

3. Vyhledejte složku `src\main\java\com\azure\cosmosdb\cassandra` a vytvořte novou podsložku s názvem `examples`.

4. Dále vytvořte třídu jazyka Java `UserProfile`. Tato třída obsahuje hlavní metodu, která volá metody createKeyspace a createTable, které jste definovali dříve: 

   ```java
   package com.azure.cosmosdb.cassandra.examples; 
   import java.io.IOException; 
   import com.azure.cosmosdb.cassandra.repository.UserRepository; 
   import com.azure.cosmosdb.cassandra.util.CassandraUtils; 
   import com.datastax.driver.core.PreparedStatement; 
   import com.datastax.driver.core.Session; 
   import org.slf4j.Logger; 
   import org.slf4j.LoggerFactory; 
   
   /** 
    * Example class which will demonstrate following operations on Cassandra Database on CosmosDB 
    * - Create Keyspace 
    * - Create Table 
    * - Insert Rows 
    * - Select all data from a table 
    * - Select a row from a table 
    */ 
   
   public class UserProfile { 
   
       private static final Logger LOGGER = LoggerFactory.getLogger(UserProfile.class); 
       public static void main(String[] s) throws Exception { 
           CassandraUtils utils = new CassandraUtils(); 
           Session cassandraSession = utils.getSession(); 
   
           try { 
               UserRepository repository = new UserRepository(cassandraSession); 
               //Create keyspace in cassandra database 
               repository.createKeyspace(); 
               //Create table in cassandra database 
               repository.createTable(); 
   
           } finally { 
               utils.close(); 
               LOGGER.info("Please delete your table after verifying the presence of the data in portal or from CQL"); 
           } 
       } 
   } 
   ```
 
## <a name="run-the-app"></a>Spuštění aplikace 

1. Otevřete příkazový řádek nebo okno terminálu. Vložte následující blok kódu. 

   Tento kód změní adresář (cd) na cestu ke složce, ve které jste vytvořili projekt. Pak spustí příkaz `mvn clean install`, který v cílové složce vygeneruje soubor `cosmosdb-cassandra-examples.jar`. A nakonec spustí aplikaci v Javě.

   ```bash
   cd cassandra-demo

   mvn clean install 

   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile 
   ```

   Okno terminálu zobrazí oznámení o vytvoření prostoru klíčů a tabulky. 
   
2. Teď na webu Azure Portal otevřete **Průzkumníka dat** a potvrďte, že byl vytvořen prostor klíčů a tabulka.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili, jak vytvořit účet rozhraní API Cassandra v Azure Cosmos DB, databázi a tabulku pomocí aplikace v Javě. Teď můžete přejít k dalšímu článku:

> [!div class="nextstepaction"]
> [Načtení ukázkových dat do tabulky rozhraní API Cassandra](cassandra-api-load-data.md).
