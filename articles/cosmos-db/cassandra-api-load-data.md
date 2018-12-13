---
title: 'Kurz: Načtení ukázkových dat do tabulky Cassandra API ve službě Azure Cosmos DB s použitím aplikace v Javě'
description: Tento kurz ukazuje, jak načíst ukázková data uživatele do tabulky Cassandra API ve službě Azure Cosmos DB s použitím aplikace v Javě.
author: kanshiG
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 12/03/2018
ms.author: govindk
ms.reviewer: sngun
Customer intent: As a developer, I want to build a Java application to load data to a Cassandra API table in Azure Cosmos DB so that customers can store and manage the key/value data and utilize the global distribution, elastic scaling, multi-master, and other capabilities offered by Azure Cosmos DB.
ms.openlocfilehash: fad6d34a3d3712a036e0275717754f75d5314aae
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52863649"
---
# <a name="tutorial-load-sample-data-into-a-cassandra-api-table-in-azure-cosmos-db"></a>Kurz: Načtení ukázkových dat do tabulky Cassandra API ve službě Azure Cosmos DB

Jako vývojář můžete mít aplikace, které používají páry klíč/hodnota. Účet rozhraní Cassandra API ve službě Azure Cosmos DB můžete použít k ukládání a správě dat klíč/hodnota. Tento kurz ukazuje, jak načíst ukázková data uživatele do tabulky v účtu rozhraní Cassandra API ve službě Azure Cosmos DB s použitím aplikace v Javě. Využívá aplikaci v Javě [ovladači Java](https://github.com/datastax/java-driver) a načte data uživatele, jako je například ID uživatele, uživatelské jméno a město uživatele. 

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Načtení dat do tabulky Cassandra
> * Spuštění aplikace

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Tento článek je součástí vícedílného kurzu. Než s tímto dokumentem začnete pracovat, nezapomeňte si [vytvořit účet rozhraní API Cassandra, prostor klíčů a tabulku](create-cassandra-api-account-java.md).   

## <a name="load-data-into-the-table"></a>Načtení dat do tabulky

Pomocí následujícího postupu můžete načíst data do tabulky rozhraní API Cassandra:

1. Otevřete soubor "UserRepository.java" ve složce "src\main\java\com\azure\cosmosdb\cassandra" a přidat kód pro vložení user_id, user_name a user_bcity pole do tabulky:

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
 
2. Otevřete soubor "UserProfile.java" ve složce "src\main\java\com\azure\cosmosdb\cassandra". Tato třída obsahuje hlavní metodu, která volá metody createKeyspace a createTable, které jste definovali dříve. Teď připojte následující kód, aby se vložila nějaká ukázková data do tabulky rozhraní API Cassandra.

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

Otevřete příkazový řádek nebo okno terminálu a cestu ke složce kde byl vytvořen projektu. Spuštěním příkazu "mvn čisté instalace" Vytvoření souboru cosmosdb-cassandra-examples.jar do cílové složky a spusťte aplikaci. 

```bash
cd "cassandra-demo"

mvn clean install

java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
```

Teď můžete na portálu Azure Portal otevřít Průzkumníka dat a potvrdit, že se informace o uživateli přidaly do tabulky.
    
## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak načíst ukázková data do účtu rozhraní Cassandra API ve službě Azure Cosmos DB. Teď můžete přejít k dalšímu článku:

> [!div class="nextstepaction"]
> [Dotazování na data z účtu rozhraní API Cassandra](cassandra-api-query-data.md)
