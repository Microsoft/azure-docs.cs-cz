---
title: 'Kurz: Aplikace Java pro načtení ukázkových dat do tabulky rozhraní CASSANDRA API v Azure Cosmos DB'
description: Tento kurz ukazuje, jak načíst ukázková uživatelská data do tabulky rozhraní API Cassandra v Azure Cosmos DB pomocí java aplikace.
author: kanshiG
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: govindk
ms.reviewer: sngun
Customer intent: As a developer, I want to build a Java application to load data to a Cassandra API table in Azure Cosmos DB so that customers can store and manage the key/value data and utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: 66c292bcb02e3b2b215cabe4968fa30a45422cef
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75445650"
---
# <a name="tutorial-load-sample-data-into-a-cassandra-api-table-in-azure-cosmos-db"></a>Kurz: Načtení ukázkových dat do tabulky rozhraní API Cassandra v Azure Cosmos DB

Jako vývojář můžete mít aplikace, které používají páry klíč/hodnota. Účet rozhraní CASSAndRo API v Azure Cosmos DB můžete použít k ukládání a správě dat klíče a hodnoty. Tento kurz ukazuje, jak načíst ukázková uživatelská data do tabulky v účtu rozhraní API Cassandra v Azure Cosmos DB pomocí aplikace Java. Aplikace Java používá [ovladač Java](https://github.com/datastax/java-driver) a načte uživatelská data, jako je ID uživatele, uživatelské jméno a uživatelské město. 

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Načtení dat do tabulky Cassandra
> * Spuštění aplikace

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

* Tento článek patří do vícedílného kurzu. Než začnete s tímto [dokumentem,](create-cassandra-api-account-java.md)vytvořte účet cassandra API, keyspace a tabulku .   

## <a name="load-data-into-the-table"></a>Načtení dat do tabulky

Pomocí následujícího postupu můžete načíst data do tabulky rozhraní API Cassandra:

1. Otevřete soubor UserRepository.java ve složce "src\main\java\com\azure\cosmosdb\cassandra" a přidejte kód pro vložení polí user_id, user_name a user_bcity do tabulky:

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
 
2. Otevřete soubor UserProfile.java ve složce "src\main\java\com\azure\cosmosdb\cassandra". Tato třída obsahuje hlavní metodu, která volá metody createKeyspace a createTable, které jste definovali dříve. Teď připojte následující kód, aby se vložila nějaká ukázková data do tabulky rozhraní API Cassandra.

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

Otevřete příkazový řádek nebo okno terminálu a změňte cestu ke složce, kde jste projekt vytvořili. Spusťte příkaz "mvn clean install" a vygenerujte soubor cosmosdb-cassandra-examples.jar v cílové složce a spusťte aplikaci. 

```bash
cd "cassandra-demo"

mvn clean install

java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
```

Teď můžete na portálu Azure Portal otevřít Průzkumníka dat a potvrdit, že se informace o uživateli přidaly do tabulky.
    
## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak načíst ukázková data do účtu rozhraní API Cassandra v Azure Cosmos DB. Teď můžete přejít k dalšímu článku:

> [!div class="nextstepaction"]
> [Dotazování na data z účtu rozhraní API Cassandra](cassandra-api-query-data.md)
