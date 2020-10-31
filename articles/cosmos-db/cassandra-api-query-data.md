---
title: 'Kurz: dotazování dat z rozhraní API Cassandra účtu v Azure Cosmos DB'
description: V tomto kurzu se dozvíte, jak zadávat dotazy na data uživatelů z Azure Cosmos DB rozhraní API Cassandra účet pomocí aplikace Java.
ms.service: cosmos-db
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: 693984ceaef1a71ebfbe4122c18a45a563c9c101
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100421"
---
# <a name="tutorial-query-data-from-a-cassandra-api-account-in-azure-cosmos-db"></a>Kurz: dotazování dat z rozhraní API Cassandra účtu v Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Jako vývojář můžete mít aplikace, které používají páry klíč/hodnota. K ukládání a dotazování dat o klíčích a hodnotách můžete použít účet rozhraní API Cassandra v Azure Cosmos DB. V tomto kurzu se dozvíte, jak zadávat dotazy na data uživatelů z rozhraní API Cassandra účtu v Azure Cosmos DB pomocí aplikace Java. Aplikace Java používá [ovladač Java](https://github.com/datastax/java-driver) a dotazuje se na uživatelská data, jako je ID uživatele, jméno uživatele a město uživatele. 

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Dotazování dat z tabulky Cassandra
> * Spuštění aplikace

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

* Tento článek patří do vícedílného kurzu. Než začnete, ujistěte se, že jste dokončili předchozí kroky, abyste vytvořili rozhraní API Cassandra účet, místo, tabulku a [načetli ukázková data do tabulky](cassandra-api-load-data.md). 

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

   Tento kód změní adresář (CD) na cestu ke složce, ve které jste projekt vytvořili. Pak spustí příkaz `mvn clean install`, který v cílové složce vygeneruje soubor `cosmosdb-cassandra-examples.jar`. A nakonec spustí aplikaci v Javě.

   ```bash
   cd "cassandra-demo"
   
   mvn clean install
   
   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
   ```

2. Teď na webu Azure Portal otevřete **Průzkumníka dat** a potvrďte, že uživatelská tabulka je odstraněná.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už je nepotřebujete, můžete odstranit skupinu prostředků, účet Azure Cosmos a všechny související prostředky. Pokud to chcete udělat, vyberte skupinu prostředků pro virtuální počítač, vyberte **Odstranit** a pak potvrďte název skupiny prostředků, která se má odstranit.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak zadávat dotazy na data z rozhraní API Cassandra účtu v Azure Cosmos DB. Teď můžete přejít k dalšímu článku:

> [!div class="nextstepaction"]
> [Migrace dat do účtu rozhraní API Cassandra](cassandra-import-data.md)


