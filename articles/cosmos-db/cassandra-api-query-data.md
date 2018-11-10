---
title: Dotazování na data z účtu rozhraní API Cassandra služby Azure Cosmos DB
description: Tento článek ukazuje, jak se dotazovat na data uživatele z účtu rozhraní API Cassandra služby Azure Cosmos DB s použitím aplikace v Javě.
services: cosmos-db
ms.service: cosmos-db
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.component: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: a06e7e6159953bfeffa966759d29b91bbcbafd37
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50739205"
---
# <a name="tutorial-query-data-from-an-azure-cosmos-db-cassandra-api-account"></a>Kurz: Dotazování dat z účtu rozhraní API Cassandra služby Azure Cosmos DB

Tento kurz ukazuje, jak se dotazovat na data uživatele z účtu rozhraní API Cassandra služby Azure Cosmos DB s použitím aplikace v Javě. Aplikace v Javě využívá [ovladač Java](https://github.com/datastax/java-driver) a dotazuje se na data uživatele, jako je například ID uživatele, uživatelské jméno, město uživatele. 

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Dotazování na data z tabulky Cassandra
> * Spuštění aplikace

## <a name="prerequisites"></a>Požadavky

* Tento článek je součástí vícedílného kurzu. Než začnete, je nutné provést předchozí kroky a vytvořit účet rozhraní API Cassandra, prostor klíčů a tabulku a [načíst do tabulky ukázková data](cassandra-api-load-data.md). 

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

   Tento kód změní adresář (cd) na cestu ke složce, ve které jste vytvořili projekt. Pak spustí příkaz `mvn clean install`, který v cílové složce vygeneruje soubor `cosmosdb-cassandra-examples.jar`. A nakonec spustí aplikaci v Javě.

   ```bash
   cd "cassandra-demo"
   
   mvn clean install
   
   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
   ```

2. Teď na webu Azure Portal otevřete **Průzkumníka dat** a potvrďte, že uživatelská tabulka je odstraněná.

## <a name="next-steps"></a>Další kroky

* V tomto kurzu jste se naučili, jak se dotazovat na data z účtu rozhraní API Cassandra služby Azure Cosmos DB. Teď můžete přejít k dalšímu článku:

> [!div class="nextstepaction"]
> [Migrace dat do účtu rozhraní API Cassandra](cassandra-import-data.md)


