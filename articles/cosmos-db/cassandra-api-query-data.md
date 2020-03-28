---
title: 'Kurz: Dotazujte se na data z účtu cassandrova rozhraní API v Azure Cosmos DB'
description: Tento kurz ukazuje, jak dotazovat uživatelská data z účtu rozhraní API Azure Cosmos DB Cassandra pomocí aplikace Java.
ms.service: cosmos-db
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
Customer intent: As a developer, I want to build a Java application to query data stored in a Cassandra API account of Azure Cosmos DB so that customers can manage the key/value data and utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: 69a9bc912f2cd52e52ca6403187f993413539ecd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "60899895"
---
# <a name="tutorial-query-data-from-a-cassandra-api-account-in-azure-cosmos-db"></a>Kurz: Dotazujte se na data z účtu cassandrova rozhraní API v Azure Cosmos DB

Jako vývojář můžete mít aplikace, které používají páry klíč/hodnota. Účet rozhraní API Cassandra v Azure Cosmos DB můžete použít k ukládání a dotazování na data klíče a hodnoty. Tento kurz ukazuje, jak dotazovat uživatelská data z účtu rozhraní CASSAndRo API v Azure Cosmos DB pomocí aplikace Java. Aplikace Java používá [ovladač Java](https://github.com/datastax/java-driver) a dotazuje se na uživatelská data, jako je ID uživatele, uživatelské jméno a uživatelské město. 

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Dotaz na data z tabulky Cassandra
> * Spuštění aplikace

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

* Tento článek patří do vícedílného kurzu. Než začnete, nezapomeňte dokončit předchozí kroky k vytvoření účtu rozhraní CASSAndSRo API, keyspace, tabulky a [načtení ukázkových dat do tabulky](cassandra-api-load-data.md). 

## <a name="query-data"></a>Dotazování dat

Pomocí následujícího postupu můžete dotazovat data z účtu rozhraní API Cassandra:

1. Otevřete soubor `UserRepository.java` ve složce `src\main\java\com\azure\cosmosdb\cassandra`. Připojte následující blok kódu. Tento kód obsahuje tři metody: 

   * Dotazování všech uživatelů v databázi
   * Dotazování konkrétního uživatele vyfiltrovaného podle ID uživatele
   * Odstranění tabulky

   ```java
   /**
   * Select all rows from user table
   */
   public void selectAllUsers() {

     final String query = "SELECT * FROM uprofile.user";
     List<Row> rows = session.execute(query).all();

     for (Row row : rows) {
        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
     }
   }

   /**
   * Select a row from user table
   *
   * @param id user_id
   */
   public void selectUser(int id) {
      final String query = "SELECT * FROM uprofile.user where user_id = 3";
      Row row = session.execute(query).one();

      LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
   }

   /**
   * Delete user table.
   */
   public void deleteTable() {
     final String query = "DROP TABLE IF EXISTS uprofile.user";
     session.execute(query);
   }
   ```

2. Otevřete soubor `UserProfile.java` ve složce `src\main\java\com\azure\cosmosdb\cassandra`. Tato třída obsahuje hlavní metodu, která volá createKeyspace a createTable, tj. metody vložení dat, které jste definovali dříve. Teď přidejte následující kód, který se dotazuje na všechny uživatele nebo na konkrétního uživatele:

   ```java
   LOGGER.info("Select all users");
   repository.selectAllUsers();

   LOGGER.info("Select a user by id (3)");
   repository.selectUser(3);

   LOGGER.info("Delete the users profile table");
   repository.deleteTable();
   ```

## <a name="run-the-java-app"></a>Spuštění aplikace v Javě
1. Otevřete příkazový řádek nebo okno terminálu. Vložte následující blok kódu. 

   Tento kód změní adresář (cd) na cestu ke složce, kde jste projekt vytvořili. Pak spustí příkaz `mvn clean install`, který v cílové složce vygeneruje soubor `cosmosdb-cassandra-examples.jar`. A nakonec spustí aplikaci v Javě.

   ```bash
   cd "cassandra-demo"
   
   mvn clean install
   
   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
   ```

2. Teď na webu Azure Portal otevřete **Průzkumníka dat** a potvrďte, že uživatelská tabulka je odstraněná.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nejsou potřeba, můžete odstranit skupinu prostředků, účet Azure Cosmos a všechny související prostředky. Chcete-li tak učinit, vyberte skupinu prostředků pro virtuální počítač, vyberte **Odstranit**a potvrďte název skupiny prostředků, kterou chcete odstranit.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak dotazovat data z účtu rozhraní API Cassandra v Azure Cosmos DB. Teď můžete přejít k dalšímu článku:

> [!div class="nextstepaction"]
> [Migrace dat do účtu rozhraní API Cassandra](cassandra-import-data.md)


