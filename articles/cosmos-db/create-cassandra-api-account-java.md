---
title: 'Kurz: Vytvořit účet Cassandra API s použitím aplikace v Javě – Azure Cosmos DB'
description: Tento kurz ukazuje, jak vytvořit účet Cassandra API a přidejte databáze (také nazývané prostor klíčů), přidejte do tohoto účtu tabulku s použitím aplikace v Javě.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18
Customer intent: As a developer, I want to build a Java application to access and manage Azure Cosmos DB resources so that customers can store key/value data and utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: 7ef34ad17dbfbbadde7442fdcba2d02d25a6e4ad
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54037494"
---
# <a name="tutorial-create-a-cassandra-api-account-in-azure-cosmos-db-by-using-a-java-application-to-store-keyvalue-data"></a>Kurz: Vytvořit účet Cassandra API ve službě Azure Cosmos DB s použitím aplikace v Javě pro ukládání dat klíč/hodnota

Jako vývojář můžete mít aplikace, které používají páry klíč/hodnota. Účet rozhraní Cassandra API ve službě Azure Cosmos DB můžete použít k ukládání dat klíč/hodnota. Tento kurz popisuje způsob použití aplikace v Javě ve službě Azure Cosmos DB vytvořit účet Cassandra API, přidejte databáze (také nazývané prostor klíčů) a přidejte tabulku. Využívá aplikaci v Javě [ovladači Java](https://github.com/datastax/java-driver) vytvořit uživatelskou databázi, která obsahuje podrobnosti, jako je ID uživatele, uživatelské jméno a město uživatele.  

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Vytvoření účtu databáze Cassandra
> * Získání připojovacího řetězce účtu
> * Vytvořte projekt Maven a závislosti
> * Přidání databáze a tabulky
> * Spuštění aplikace

## <a name="prerequisites"></a>Požadavky 

* Pokud ještě nemáte předplatné Azure, vytvořte si  [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)  před tím, než začnete. 

* Získat nejnovější verzi [Java Development Kit (JDK)](https://aka.ms/azure-jdks). 

* [Stáhněte si](https://maven.apache.org/download.cgi) a [nainstalovat](https://maven.apache.org/install.html) [Maven](https://maven.apache.org/) binární archiv. 
  - Na Ubuntu můžete Maven nainstalovat spuštěním příkazu  `apt-get install maven` . 

## <a name="create-a-database-account"></a>Vytvoření účtu databáze 

1. Přihlaste se na  [Azure Portal](https://portal.azure.com/). 

2. Vyberte  **Vytvořit prostředek** > **Databáze** > **Azure Cosmos DB**. 

3. V **nový účet** podokně zadejte nastavení pro nový účet Azure Cosmos. 

   |Nastavení   |Navrhovaná hodnota  |Popis  |
   |---------|---------|---------|
   |ID   |   Zadejte jedinečný název.    | Zadejte jedinečný název pro identifikaci tohoto účtu Azure Cosmos. <br/><br/>Vzhledem k tomu, že se váš kontaktní bod vytvoří připojením řetězce cassandra.cosmosdb.azure.com k ID, které zadáte, použijte jedinečné, ale snadno rozpoznatelné ID.         |
   |Rozhraní API    |  Cassandra   |  Rozhraní API určuje typ účtu, který se má vytvořit. <br/> Vyberte **Cassandra**, protože v tomto článku vytvoříte databázi širokých sloupců, která může být dotázán pomocí syntaxe jazyka dotazů Cassandra (CQL).  |
   |Předplatné    |  Vaše předplatné        |  Vyberte předplatné Azure, kterou chcete použít pro tento účet Azure Cosmos.        |
   |Skupina prostředků   | Zadejte název.    |  Vyberte **vytvořit nový**a pak zadejte nový název skupiny prostředků pro váš účet. V zájmu jednoduchosti můžete použít název, který se shoduje s vaším ID.    |
   |Umístění    |  Vyberte oblast nejbližší vašim uživatelům.    |  Vyberte zeměpisné umístění, ve kterém chcete účet Azure Cosmos hostovat. Použijte umístění co nejblíže vašim uživatelům, abyste jim zajistili nejrychlejší přístup k data.    |

   ![Vytvoření účtu pomocí portálu](./media/create-cassandra-api-account-java/create-account.png)

4. Vyberte **vytvořit**. <br/>Vytvoření účtu trvá několik minut. Po vytvoření prostředku můžete zobrazit **nasazení bylo úspěšné** oznámení na pravé straně portálu.

## <a name="get-the-connection-details-of-your-account"></a>Získání podrobností o připojení vašeho účtu  

Získání informací o připojovacím řetězci z webu Azure portal a zkopírujte je do konfiguračního souboru Java. Připojovací řetězec umožňuje vaší aplikaci komunikovat s hostovanou databází. 

1. Z [webu Azure portal](https://portal.azure.com/), přejděte ke svému účtu Azure Cosmos. 

2. Otevřete podokno  **Připojovací řetězec**.  

3. Zkopírujte hodnoty **KONTAKTNÍ BOD**, **PORT**, **UŽIVATELSKÉ JMÉNO** a **PRIMÁRNÍ HESLO** pro použití v dalších krocích.

## <a name="create-the-project-and-the-dependencies"></a>Vytvoření projektu a závislostí 

Ukázkový projekt Java, který používáte v tomto článku je hostovaná na Githubu. Můžete spustit kroky v tomto dokumentu nebo stáhnout ukázku z [azure-cosmos-db-cassandra-java-getting-started](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started) úložiště. 

Po stažení souborů aktualizace informací o připojovacím řetězci v rámci `java-examples\src\main\resources\config.properties` souboru a potom ho spusťte.  

```java
cassandra_host=<FILLME_with_CONTACT POINT> 
cassandra_port = 10350 
cassandra_username=<FILLME_with_USERNAME> 
cassandra_password=<FILLME_with_PRIMARY PASSWORD> 
```

Použijte následující postup k vytvoření vzorku úplně od začátku: 

1. Z terminálu nebo příkazového řádku vytvořte nový projekt Maven s názvem Cassandra-demo. 

   ```bash
   mvn archetype:generate -DgroupId=com.azure.cosmosdb.cassandra -DartifactId=cassandra-demo -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false 
   ```
 
2. Najděte složku `cassandra-demo`. Pomocí textového editoru otevřete soubor `pom.xml`, který byl vygenerován. 

   Přidat závislosti Cassandra a vytvářet moduly plug-in, které vyžaduje váš projekt, jak je znázorněno [pom.xml](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/pom.xml) souboru.  

3. Ve složce `cassandra-demo\src\main` vytvořte novou složku s názvem `resources`.  Ve složce prostředků přidejte soubory config.properties a log4j.properties:

   - [Config.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/resources/config.properties) soubor uloží hodnoty připojení koncového bodu a klíče účtu rozhraní Cassandra API. 
   
   - [Log4j.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/resources/log4j.properties) soubor definuje úroveň protokolování, vyžaduje se pro interakci s rozhraním Cassandra API.  

4. Přejděte `src/main/java/com/azure/cosmosdb/cassandra/` složky. Ve složce cassandra vytvořte další složku s názvem `utils`. Nová složka ukládá užitkové třídy potřebné pro připojení k účtu rozhraní API Cassandra. 

   Přidejte třídu [CassandraUtils](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java) k vytvoření clusteru a k otvírání a zavírání relací Cassandra. Cluster se připojí k rozhraní Cassandra API účtu ve službě Azure Cosmos DB a vrátí relaci pro přístup. Pomocí třídy [Konfigurace](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/util/Configurations.java) si přečtěte informace o připojovacím řetězci ze souboru config.properties. 

5. Java vzorovým kódem se vytvoří databázi s informace o uživateli, jako je například uživatelské jméno, ID uživatele a uživatele město. Budete muset definovat metody get a set pro přístup k podrobnostem o uživateli v hlavní funkci.
 
   Vytvoření [User.java](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/User.java) třídy v rámci `src/main/java/com/azure/cosmosdb/cassandra/` složky pomocí get a set metod. 

## <a name="add-a-database-and-a-table"></a>Přidání databáze a tabulky  

Tato část popisuje, jak přidat databázi (prostor klíčů) a tabulku, s použitím CQL.

1. Ve složce `src\main\java\com\azure\cosmosdb\cassandra` vytvořte novou složku s názvem `repository`. 

2. Vytvořte `UserRepository` Java třídu a přidejte do ní následující kód: 

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

4. Vytvořte `UserProfile` třídy jazyka Java. Tato třída obsahuje hlavní metodu, která volá metody createKeyspace a createTable, které jste definovali dříve: 

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

   Tento kód změní adresář (cd) na cesty ke složce, ve které jste vytvořili projekt. Pak spustí příkaz `mvn clean install`, který v cílové složce vygeneruje soubor `cosmosdb-cassandra-examples.jar`. A nakonec spustí aplikaci v Javě.

   ```bash
   cd cassandra-demo

   mvn clean install 

   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile 
   ```

   Okno terminálu zobrazí oznámení o vytvoření prostoru klíčů a tabulky. 
   
2. Teď na webu Azure Portal otevřete **Průzkumníka dat** a potvrďte, že byl vytvořen prostor klíčů a tabulka.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak vytvořit účet Cassandra API ve službě Azure Cosmos DB, databázi a tabulku s použitím aplikace v Javě. Teď můžete přejít k dalšímu článku:

> [!div class="nextstepaction"]
> [Načtení ukázkových dat do tabulky rozhraní API Cassandra](cassandra-api-load-data.md).
