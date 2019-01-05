---
title: 'Kurz: Dotazování dat z účtu rozhraní Cassandra API ve službě Azure Cosmos DB'
description: Tento kurz ukazuje postupy při dotazování na data uživatele z účtu služby Azure Cosmos DB Cassandra API s použitím aplikace v Javě.
ms.service: cosmos-db
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
Customer intent: As a developer, I want to build a Java application to query data stored in a Cassandra API account of Azure Cosmos DB so that customers can manage the key/value data and utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: 69a9bc912f2cd52e52ca6403187f993413539ecd
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54038173"
---
# <a name="tutorial-query-data-from-a-cassandra-api-account-in-azure-cosmos-db"></a>Kurz: Dotazování dat z účtu rozhraní Cassandra API ve službě Azure Cosmos DB

Jako vývojář můžete mít aplikace, které používají páry klíč/hodnota. Účet rozhraní Cassandra API ve službě Azure Cosmos DB můžete použít k ukládání a dotazování dat klíč/hodnota. Tento kurz ukazuje postupy při dotazování na data uživatele z účtu rozhraní Cassandra API ve službě Azure Cosmos DB s použitím aplikace v Javě. Využívá aplikaci v Javě [ovladači Java](https://github.com/datastax/java-driver) a zadávání dotazů na data uživatele, jako je například ID uživatele, uživatelské jméno a město uživatele. 

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Dotazování dat z tabulky Cassandra
> * Spuštění aplikace

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Tento článek je součástí vícedílného kurzu. Než začnete, ujistěte se, že k dokončení předchozích kroků k vytvoření účtu rozhraní Cassandra API, prostor klíčů, tabulky, a [načtení ukázkových dat do tabulky](cassandra-api-load-data.md). 

## <a name="query-data"></a>Dotazování dat

Pomocí následujícího postupu můžete dotazovat data z účtu rozhraní API Cassandra:

1. Otevřete soubor `UserRepository.java` ve složce `src\main\java\com\azure\cosmosdb\cassandra`. Připojte následující blok kódu. Tento kód obsahuje tři metody: 

   * Dotazování všech uživatelů v databázi
   * Dotazování konkrétního uživatele vyfiltrovaného podle ID uživatele
   * Chcete-li odstranit tabulku

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

   Tento kód změní adresář (cd) na cesty ke složce, ve které jste vytvořili projekt. Pak spustí příkaz `mvn clean install`, který v cílové složce vygeneruje soubor `cosmosdb-cassandra-examples.jar`. A nakonec spustí aplikaci v Javě.

   ```bash
   cd "cassandra-demo"
   
   mvn clean install
   
   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
   ```

2. Teď na webu Azure Portal otevřete **Průzkumníka dat** a potvrďte, že uživatelská tabulka je odstraněná.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste už nepotřebujete, můžete odstranit skupinu prostředků, účet služby Azure Cosmos a všechny související prostředky. Uděláte to tak, vyberte skupinu prostředků pro virtuální počítač, vyberte **odstranit**a potom ověřte název skupiny prostředků pro odstranění.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak k dotazování na data z účtu rozhraní Cassandra API ve službě Azure Cosmos DB. Teď můžete přejít k dalšímu článku:

> [!div class="nextstepaction"]
> [Migrace dat do účtu rozhraní API Cassandra](cassandra-import-data.md)


