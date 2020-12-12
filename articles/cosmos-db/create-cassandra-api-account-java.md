---
title: 'Kurz: sestavení aplikace Java pro vytvoření účtu Azure Cosmos DB rozhraní API Cassandra'
description: V tomto kurzu se dozvíte, jak vytvořit účet rozhraní API Cassandra, jak přidat databázi (označuje se také jako prostor klíčů) a přidá do tohoto účtu tabulku pomocí aplikace Java.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18, devx-track-java
ms.openlocfilehash: fe452f61d17f0b2014957e3b458ef1ad1b3c539d
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2020
ms.locfileid: "97357621"
---
# <a name="tutorial-create-a-cassandra-api-account-in-azure-cosmos-db-by-using-a-java-application-to-store-keyvalue-data"></a>Kurz: vytvoření účtu rozhraní API Cassandra v Azure Cosmos DB pomocí aplikace Java k ukládání dat o klíčích a hodnotách
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Jako vývojář můžete mít aplikace, které používají páry klíč/hodnota. K uložení dat o klíčích a hodnotách můžete použít účet rozhraní API Cassandra v Azure Cosmos DB. V tomto kurzu se dozvíte, jak pomocí aplikace v jazyce Java vytvořit účet rozhraní API Cassandra v Azure Cosmos DB, přidat databázi (označuje se také jako místo) a přidat tabulku. Aplikace Java používá [ovladač Java](https://github.com/datastax/java-driver) k vytvoření uživatelské databáze, která obsahuje podrobnosti, jako je ID uživatele, jméno uživatele a město uživatele.  

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Vytvoření účtu databáze Cassandra
> * Získání připojovacího řetězce účtu
> * Vytvoření projektu a závislostí Maven
> * Přidání databáze a tabulky
> * Spuštění aplikace

## <a name="prerequisites"></a>Požadavky 

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete. 

* Získejte nejnovější verzi sady [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable&preserve-view=true). 

* [Stáhněte](https://maven.apache.org/download.cgi) a [nainstalujte](https://maven.apache.org/install.html) binární archiv [Maven](https://maven.apache.org/) . 
  - Na Ubuntu můžete Maven nainstalovat spuštěním příkazu `apt-get install maven`. 

## <a name="create-a-database-account"></a>Vytvoření účtu databáze 

1. Přihlaste se na [Azure Portal](https://portal.azure.com/). 

2. Vyberte **vytvořit**  >  **databáze** prostředků  >  **Azure Cosmos DB**. 

3. V podokně **nový účet** zadejte nastavení pro nový účet Azure Cosmos. 

   |Nastavení   |Navrhovaná hodnota  |Popis  |
   |---------|---------|---------|
   |ID   |   Zadejte jedinečný název.    | Zadejte jedinečný název, který identifikuje tento účet Azure Cosmos. <br/><br/>Vzhledem k tomu, že se váš kontaktní bod vytvoří připojením řetězce cassandra.cosmosdb.azure.com k ID, které zadáte, použijte jedinečné, ale snadno rozpoznatelné ID.         |
   |rozhraní API    |  Cassandra   |  Rozhraní API určuje typ účtu, který se má vytvořit. <br/> Vyberte **Cassandra**, protože v tomto článku vytvoříte databázi se sloupcem ve světě, na kterou se dá dotazovat pomocí syntaxe CQL (Cassandra Query Language).  |
   |Předplatné    |  Vaše předplatné        |  Vyberte předplatné Azure, které chcete použít pro tento účet Azure Cosmos.        |
   |Skupina prostředků   | Zadejte název.    |  Vyberte **Vytvořit novou** a zadejte název nové skupiny prostředků pro váš účet. V zájmu jednoduchosti můžete použít název, který se shoduje s vaším ID.    |
   |Umístění    |  Vyberte oblast nejbližší vašim uživatelům.    |  Vyberte geografické umístění, ve kterém chcete účet Azure Cosmos hostovat. Použijte umístění, které je nejblíže vašim uživatelům, a poskytněte jim tak nejrychlejší přístup k datům.    |

   :::image type="content" source="./media/create-cassandra-api-account-java/create-account.png" alt-text="Vytvoření účtu pomocí portálu":::

4. Vyberte **Vytvořit**. <br/>Vytvoření účtu trvá několik minut. Po vytvoření prostředku uvidíte na pravé straně portálu oznámení o **úspěšném nasazení** .

## <a name="get-the-connection-details-of-your-account"></a>Získání podrobností o připojení vašeho účtu  

Získejte informace o připojovacím řetězci z Azure Portal a zkopírujte je do konfiguračního souboru Java. Připojovací řetězec umožňuje vaší aplikaci komunikovat s hostovanou databází. 

1. Z [Azure Portal](https://portal.azure.com/)přejít na účet Azure Cosmos. 

2. Otevřete podokno **připojovací řetězec** .  

3. Zkopírujte hodnoty **KONTAKTNÍ BOD**, **PORT**, **UŽIVATELSKÉ JMÉNO** a **PRIMÁRNÍ HESLO** pro použití v dalších krocích.

## <a name="create-the-project-and-the-dependencies"></a>Vytvoření projektu a závislostí 

Ukázkový projekt Java, který používáte v tomto článku, je hostovaný na GitHubu. Můžete spustit kroky v tomto dokumentu nebo si stáhnout ukázku z úložiště [Azure-Cosmos-DB-Cassandra-Java-Začínáme](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started) . 

Po stažení souborů aktualizujte informace připojovacího řetězce v rámci `java-examples\src\main\resources\config.properties` souboru a spusťte jej.  

```java
cassandra_host=<FILLME_with_CONTACT POINT> 
cassandra_port = 10350 
cassandra_username=<FILLME_with_USERNAME> 
cassandra_password=<FILLME_with_PRIMARY PASSWORD> 
```

Pomocí následujících kroků sestavíte ukázku od začátku: 

1. Z terminálu nebo příkazového řádku vytvořte nový projekt Maven s názvem Cassandra-demo. 

   ```bash
   mvn archetype:generate -DgroupId=com.azure.cosmosdb.cassandra -DartifactId=cassandra-demo -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false 
   ```
 
2. Najděte složku `cassandra-demo`. Pomocí textového editoru otevřete soubor `pom.xml`, který byl vygenerován. 

   Přidejte závislosti Cassandra a sestavování modulů plug-in vyžadovaných vaším projektem, jak je znázorněno v souboru [pom.xml](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/pom.xml) .  

3. Ve složce `cassandra-demo\src\main` vytvořte novou složku s názvem `resources`.  Ve složce prostředků přidejte soubory config.properties a log4j.properties:

   - V souboru [config. Properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/resources/config.properties) se ukládá koncový bod připojení a hodnoty klíče rozhraní API Cassandra účtu. 
   
   - Soubor [log4j. Properties](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/resources/log4j.properties) definuje úroveň protokolování potřebný pro interakci s rozhraní API Cassandra.  

4. Přejděte do `src/main/java/com/azure/cosmosdb/cassandra/` složky. Ve složce cassandra vytvořte další složku s názvem `utils`. Nová složka ukládá užitkové třídy potřebné pro připojení k účtu rozhraní API Cassandra. 

   Přidejte třídu [CassandraUtils](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/java/com/azure/cosmosdb/cassandra/util/CassandraUtils.java) k vytvoření clusteru a k otvírání a zavírání relací Cassandra. Cluster se připojí k účtu rozhraní API Cassandra v Azure Cosmos DB a vrátí relaci pro přístup. Pomocí třídy [Konfigurace](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/java/com/azure/cosmosdb/cassandra/util/Configurations.java) si přečtěte informace o připojovacím řetězci ze souboru config.properties. 

5. Ukázka Java vytvoří databázi s informacemi o uživateli, jako je uživatelské jméno, ID uživatele a město uživatele. Budete muset definovat metody get a set pro přístup k podrobnostem o uživateli v hlavní funkci.
 
   Ve složce vytvořte třídu [User. Java](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started/blob/main/src/main/java/com/azure/cosmosdb/cassandra/examples/UserProfile.java) `src/main/java/com/azure/cosmosdb/cassandra/` s metodami Get a set. 

## <a name="add-a-database-and-a-table"></a>Přidání databáze a tabulky  

Tato část popisuje, jak přidat databázi (místo) a tabulku pomocí CQL.

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

   Tento kód změní adresář (CD) na cestu ke složce, ve které jste projekt vytvořili. Pak spustí příkaz `mvn clean install`, který v cílové složce vygeneruje soubor `cosmosdb-cassandra-examples.jar`. A nakonec spustí aplikaci v Javě.

   ```bash
   cd cassandra-demo

   mvn clean install 

   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile 
   ```

   Okno terminálu zobrazí oznámení o vytvoření prostoru klíčů a tabulky. 
   
2. Teď na webu Azure Portal otevřete **Průzkumníka dat** a potvrďte, že byl vytvořen prostor klíčů a tabulka.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak vytvořit účet rozhraní API Cassandra v Azure Cosmos DB, databázi a tabulce pomocí aplikace Java. Teď můžete přejít k dalšímu článku:

> [!div class="nextstepaction"]
> [Načtení ukázkových dat do tabulky rozhraní API Cassandra](cassandra-api-load-data.md).
