---
title: Načtení ukázkových dat do tabulky rozhraní API Cassandra pro Azure Cosmos DB pomocí aplikace Java | Microsoft Docs
description: Tento článek ukazuje, jak načíst ukázková data uživatele do tabulky v rozhraní API Cassandra pro Azure Cosmos DB pomocí aplikace Java.
services: cosmos-db
author: kanshiG
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: e116dbf1d49fed1a47b830f9a57cd77a33b7ea9c
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50740716"
---
# <a name="tutorial-load-sample-data-into-an-azure-cosmos-db-cassandra-api-table"></a>Kurz: Načtení ukázkových dat do tabulky rozhraní API Cassandra služby Azure Cosmos DB

V tomto kurzu se dozvíte, jak načíst ukázková data uživatele do tabulky v rozhraní API Cassandra pro Azure Cosmos DB pomocí aplikace Java. Aplikace v Javě využívá [ovladač Java](https://github.com/datastax/java-driver) a načítá data uživatele, jako je například ID uživatele, uživatelské jméno nebo město uživatele. 

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Načtení dat do tabulky Cassandra
> * Spuštění aplikace

## <a name="prerequisites"></a>Požadavky

* Tento článek je součástí vícedílného kurzu. Než s tímto dokumentem začnete pracovat, nezapomeňte si [vytvořit účet rozhraní API Cassandra, prostor klíčů a tabulku](create-cassandra-api-account-java.md).   

## <a name="load-data-into-the-table"></a>Načtení dat do tabulky

Pomocí následujícího postupu můžete načíst data do tabulky rozhraní API Cassandra:

1. Otevřete soubor UserRepository.java ve složce src\main\java\com\azure\cosmosdb\cassandra a připojte kód, který do tabulky vloží pole user_id, user_name a user_bcity:

   ```java
   /**
   * Insert a row into user table
   *
   * @param id   user_id
   * @param name user_name
   * @param city user_bcity
   */
   public void insertUser(PreparedStatement statement, int id, String name, String city) {
        BoundStatement boundStatement = new BoundStatement(statement);
        session.execute(boundStatement.bind(id, name, city));
   }

   /**
   * Create a PrepareStatement to insert a row to user table
   *
   * @return PreparedStatement
   */
   public PreparedStatement prepareInsertStatement() {
      final String insertStatement = "INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (?,?,?)";
   return session.prepare(insertStatement);
   }
   ```
 
2. Otevřete soubor UserProfile.java ve složce src\main\java\com\azure\cosmosdb\cassandra. Tato třída obsahuje hlavní metodu, která volá metody createKeyspace a createTable, které jste definovali dříve. Teď připojte následující kód, aby se vložila nějaká ukázková data do tabulky rozhraní API Cassandra.

   ```java
   //Insert rows into user table
   PreparedStatement preparedStatement = repository.prepareInsertStatement();
     repository.insertUser(preparedStatement, 1, "JohnH", "Seattle");
     repository.insertUser(preparedStatement, 2, "EricK", "Spokane");
     repository.insertUser(preparedStatement, 3, "MatthewP", "Tacoma");
     repository.insertUser(preparedStatement, 4, "DavidA", "Renton");
     repository.insertUser(preparedStatement, 5, "PeterS", "Everett");
   ```

## <a name="run-the-app"></a>Spuštění aplikace

Otevřete příkazový řádek nebo okno terminálu a změňte cestu ke složce na cestu, která vede k vytvořenému projektu. Spusťte příkaz mvn clean install, aby se v cílové složce vygeneroval soubor cosmosdb-cassandra-examples.jar, a spusťte aplikaci. 

```bash
cd "cassandra-demo"

mvn clean install

java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
```

Teď můžete na portálu Azure Portal otevřít Průzkumníka dat a potvrdit, že se informace o uživateli přidaly do tabulky.
    
## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak načítat ukázková data do účtu rozhraní API Cassandra pro Azure Cosmos DB. Teď můžete přejít k dalšímu článku:

> [!div class="nextstepaction"]
> [Dotazování na data z účtu rozhraní API Cassandra](cassandra-api-query-data.md)
