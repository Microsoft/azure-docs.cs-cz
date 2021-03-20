---
title: 'Kurz: aplikace Java pro načtení ukázkových dat do rozhraní API Cassandra tabulky v Azure Cosmos DB'
description: V tomto kurzu se dozvíte, jak načíst ukázková uživatelská data do tabulky rozhraní API Cassandra v Azure Cosmos DB pomocí aplikace Java.
author: kanshiG
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: ad3dfc8c86d22e58377ebfbdb94b2beb004ded56
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93092462"
---
# <a name="tutorial-load-sample-data-into-a-cassandra-api-table-in-azure-cosmos-db"></a>Kurz: načtení ukázkových dat do rozhraní API Cassandra tabulky v Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Jako vývojář můžete mít aplikace, které používají páry klíč/hodnota. K ukládání a správě dat o klíčích a hodnotách můžete použít účet rozhraní API Cassandra v Azure Cosmos DB. V tomto kurzu se dozvíte, jak načíst ukázková uživatelská data do tabulky v rozhraní API Cassandra účtu v Azure Cosmos DB pomocí aplikace Java. Aplikace Java používá [ovladač Java](https://github.com/datastax/java-driver) a načítá uživatelská data, jako je ID uživatele, jméno uživatele a město uživatele. 

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Načtení dat do tabulky Cassandra
> * Spuštění aplikace

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Předpoklady

* Tento článek patří do vícedílného kurzu. Než začnete s tímto dokumentem, ujistěte se, že jste [vytvořili rozhraní API Cassandra účet, prostor a tabulku](create-cassandra-api-account-java.md).   

## <a name="load-data-into-the-table"></a>Načtení dat do tabulky

Pomocí následujícího postupu můžete načíst data do tabulky rozhraní API Cassandra:

1. Otevřete soubor "UserRepository. Java" ve složce "src\main\java\com\azure\cosmosdb\cassandra" a přidejte kód pro vložení user_id, user_name a user_bcity pole do tabulky:

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
 
2. Ve složce src\main\java\com\azure\cosmosdb\cassandra otevřete soubor UserProfile. Java. Tato třída obsahuje hlavní metodu, která volá metody createKeyspace a createTable, které jste definovali dříve. Teď připojte následující kód, aby se vložila nějaká ukázková data do tabulky rozhraní API Cassandra.

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

Otevřete příkazový řádek nebo okno terminálu a změňte cestu ke složce na místo, kde jste projekt vytvořili. Spusťte příkaz "MVN Clean Install", který vygeneruje soubor cosmosdb-Cassandra-Examples. jar v cílové složce a spustí aplikaci. 

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
