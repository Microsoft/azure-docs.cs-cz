---
title: 'Kurz: Vytvoření aplikace Java pro vytvoření účtu API Azure Cosmos DB Cassandra'
description: Tento kurz ukazuje, jak vytvořit účet rozhraní CASSAndSRo API, přidat databázi (označovanou také jako keyspace) a přidat tabulku k tomuto účtu pomocí aplikace Java.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18
Customer intent: As a developer, I want to build a Java application to access and manage Azure Cosmos DB resources so that customers can store key/value data and utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: b0103f7b827de77c522f78000c8d28683ac85f4b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75441901"
---
# <a name="tutorial-create-a-cassandra-api-account-in-azure-cosmos-db-by-using-a-java-application-to-store-keyvalue-data"></a>Kurz: Vytvoření účtu rozhraní CASSAndS API v Azure Cosmos DB pomocí aplikace Java pro ukládání dat klíče a hodnoty

Jako vývojář můžete mít aplikace, které používají páry klíč/hodnota. Účet rozhraní API Cassandra v Azure Cosmos DB můžete použít k ukládání dat klíč/hodnota. Tento kurz popisuje, jak pomocí aplikace Java vytvořit účet rozhraní API Cassandra v Azure Cosmos DB, přidat databázi (označovanou také jako keyspace) a přidat tabulku. Aplikace Java používá [ovladač Java](https://github.com/datastax/java-driver) k vytvoření uživatelské databáze, která obsahuje podrobnosti, jako je ID uživatele, uživatelské jméno a uživatelské město.  

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Vytvoření účtu databáze Cassandra
> * Získání připojovacího řetězce účtu
> * Vytvoření projektu Maven a závislostí
> * Přidání databáze a tabulky
> * Spuštění aplikace

## <a name="prerequisites"></a>Požadavky 

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) než začnete. 

* Získejte nejnovější verzi [Java Development Kit (JDK)](https://aka.ms/azure-jdks). 

* [Stáhněte](https://maven.apache.org/download.cgi) a [nainstalujte](https://maven.apache.org/install.html) binární archiv [Maven.](https://maven.apache.org/) 
  - Na Ubuntu můžete Maven nainstalovat spuštěním příkazu `apt-get install maven`. 

## <a name="create-a-database-account"></a>Vytvoření účtu databáze 

1. Přihlaste se k [portálu Azure](https://portal.azure.com/). 

2. Vyberte **Vytvořit** > **databáze** > prostředků**Azure Cosmos DB**. 

3. V podokně **Nový účet** zadejte nastavení nového účtu Azure Cosmos. 

   |Nastavení   |Navrhovaná hodnota  |Popis  |
   |---------|---------|---------|
   |ID   |   Zadejte jedinečný název.    | Zadejte jedinečný název k identifikaci tohoto účtu Azure Cosmos. <br/><br/>Vzhledem k tomu, že se váš kontaktní bod vytvoří připojením řetězce cassandra.cosmosdb.azure.com k ID, které zadáte, použijte jedinečné, ale snadno rozpoznatelné ID.         |
   |rozhraní API    |  Cassandra   |  Rozhraní API určuje typ účtu, který se má vytvořit. <br/> Vyberte **Cassandra**, protože v tomto článku vytvoříte databázi se širokými sloupci, na kterou lze dotazovat pomocí syntaxe dotazovacího jazyka Cassandra (CQL).  |
   |Předplatné    |  Vaše předplatné        |  Vyberte předplatné Azure, které chcete použít pro tento účet Azure Cosmos.        |
   |Skupina prostředků   | Zadejte název.    |  Vyberte**Vytvořit novou** a zadejte název nové skupiny prostředků pro váš účet. V zájmu jednoduchosti můžete použít název, který se shoduje s vaším ID.    |
   |Umístění    |  Vyberte oblast nejbližší vašim uživatelům.    |  Vyberte geografickou polohu, ve které chcete hostovat svůj účet Azure Cosmos. Použijte umístění, které je vašim uživatelům nejblíže, abyste jim poskytli nejrychlejší přístup k datům.    |

   ![Vytvoření účtu pomocí portálu](./media/create-cassandra-api-account-java/create-account.png)

4. Vyberte **Vytvořit**. <br/>Vytvoření účtu trvá několik minut. Po vytvoření prostředku se zobrazí oznámení **o úspěšném nasazení** na pravé straně portálu.

## <a name="get-the-connection-details-of-your-account"></a>Získání podrobností o připojení vašeho účtu  

Získejte informace o připojovacím řetězci z portálu Azure a zkopírujte je do konfiguračního souboru Java. Připojovací řetězec umožňuje vaší aplikaci komunikovat s hostovanou databází. 

1. Na [webu Azure portal](https://portal.azure.com/)přejděte na svůj účet Azure Cosmos. 

2. Otevřete podokno **Připojovací řetězec.**  

3. Zkopírujte hodnoty **KONTAKTNÍ BOD**, **PORT**, **UŽIVATELSKÉ JMÉNO** a **PRIMÁRNÍ HESLO** pro použití v dalších krocích.

## <a name="create-the-project-and-the-dependencies"></a>Vytvoření projektu a závislostí 

Ukázkový projekt Java, který používáte v tomto článku je hostovaný v GitHubu. Můžete spustit kroky v tomto dokumentu nebo stáhnout ukázku z [azure-cosmos-db-cassandra-java-getting-started](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started) repozitáře. 

Po stažení souborů aktualizujte informace o `java-examples\src\main\resources\config.properties` připojovacím řetězci v souboru a spusťte je.  

```java
cassandra_host=<FILLME_with_CONTACT POINT> 
cassandra_port = 10350 
cassandra_username=<FILLME_with_USERNAME> 
cassandra_password=<FILLME_with_PRIMARY PASSWORD> 
```

K vytvoření ukázky od začátku použijte následující kroky: 

1. Z terminálu nebo příkazového řádku vytvořte nový projekt Maven s názvem Cassandra-demo. 

   ```bash
   mvn archetype:generate -DgroupId=com.azure.cosmosdb.cassandra -DartifactId=cassandra-demo -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false 
   ```
 
2. Najděte složku `cassandra-demo`. Pomocí textového editoru otevřete soubor `pom.xml`, který byl vygenerován. 

   Přidejte závislosti Cassandra a vytvářejte pluginy vyžadované vaším projektem, jak je znázorněno v souboru [pom.xml.](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/pom.xml)  

3. Ve složce `cassandra-demo\src\main` vytvořte novou složku s názvem `resources`.  Ve složce prostředků přidejte soubory config.properties a log4j.properties:

   - Soubor [config.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/resources/config.properties) ukládá koncový bod připojení a hodnoty klíčů účtu cassandra API. 
   
   - Soubor [log4j.properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/resources/log4j.properties) definuje úroveň protokolování potřebného pro interakci s rozhraním CASSANDRA API.  

4. Přejděte `src/main/java/com/azure/cosmosdb/cassandra/` do složky. Ve složce cassandra vytvořte další složku s názvem `utils`. Nová složka ukládá užitkové třídy potřebné pro připojení k účtu rozhraní API Cassandra. 

   Přidejte třídu [CassandraUtils](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java) k vytvoření clusteru a k otvírání a zavírání relací Cassandra. Cluster se připojí k účtu rozhraní CASSAndRo API v Azure Cosmos DB a vrátí relaci pro přístup. Pomocí třídy [Konfigurace](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/util/Configurations.java) si přečtěte informace o připojovacím řetězci ze souboru config.properties. 

5. Ukázka javy vytvoří databázi s informacemi o uživateli, jako je uživatelské jméno, ID uživatele a město uživatele. Budete muset definovat metody get a set pro přístup k podrobnostem o uživateli v hlavní funkci.
 
   Vytvořte třídu [User.java](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/master/java-examples/src/main/java/com/azure/cosmosdb/cassandra/User.java) pod `src/main/java/com/azure/cosmosdb/cassandra/` složkou s metodami get a set. 

## <a name="add-a-database-and-a-table"></a>Přidání databáze a tabulky  

Tato část popisuje, jak přidat databázi (keyspace) a tabulku pomocí CQL.

1. Ve složce `src\main\java\com\azure\cosmosdb\cassandra` vytvořte novou složku s názvem `repository`. 

2. Vytvořte `UserRepository` třídu Java a přidejte do ní následující kód: 

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

4. Vytvořte `UserProfile` třídu Java. Tato třída obsahuje hlavní metodu, která volá metody createKeyspace a createTable, které jste definovali dříve: 

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

   Tento kód změní adresář (cd) na cestu ke složce, kde jste projekt vytvořili. Pak spustí příkaz `mvn clean install`, který v cílové složce vygeneruje soubor `cosmosdb-cassandra-examples.jar`. A nakonec spustí aplikaci v Javě.

   ```bash
   cd cassandra-demo

   mvn clean install 

   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile 
   ```

   Okno terminálu zobrazí oznámení o vytvoření prostoru klíčů a tabulky. 
   
2. Teď na webu Azure Portal otevřete **Průzkumníka dat** a potvrďte, že byl vytvořen prostor klíčů a tabulka.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak vytvořit účet rozhraní API Cassandra v Azure Cosmos DB, databázi a tabulku pomocí aplikace Java. Teď můžete přejít k dalšímu článku:

> [!div class="nextstepaction"]
> [Načtení ukázkových dat do tabulky rozhraní API Cassandra](cassandra-api-load-data.md).
