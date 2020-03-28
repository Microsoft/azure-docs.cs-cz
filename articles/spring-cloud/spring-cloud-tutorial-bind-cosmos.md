---
title: Kurz – svázání databáze Azure Cosmos s vaší aplikací Azure Spring Cloud
description: V tomto kurzu se dozvíte, jak svázat Azure Cosmos DB s vaší aplikací Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: brendm
ms.openlocfilehash: 1566b6ab59e858217adcf6818e1d62f851f37eb1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76277571"
---
# <a name="bind-an-azure-cosmos-db-database-to-your-azure-spring-cloud-application"></a>Vytvoření svázání databáze Azure Cosmos DB s vaší aplikací Azure Spring Cloud

Místo ruční konfigurace aplikací spring boot, můžete automaticky vázat vybrané služby Azure na vaše aplikace pomocí Azure Spring Cloud. Tento článek ukazuje, jak svázat aplikaci s databází Azure Cosmos DB.

Požadavky:

* Nasazená instance Azure Spring Cloud. Začněte s [naším rychlým startem při nasazování pomocí azure cli](spring-cloud-quickstart-launch-app-cli.md) a začněte.
* Účet Azure Cosmos DB s minimální úrovní oprávnění přispěvatele.

## <a name="bind-azure-cosmos-db"></a>Vazba služby Azure Cosmos DB

Azure Cosmos DB má pět různých typů rozhraní API, které podporují vazbu. Následující postup ukazuje, jak je používat:

1. Vytvoří účet databáze Azure Cosmos DB. Nápovědu naleznete v rychlém startu [při vytváření databáze.](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal) 

1. Zaznamenejte název databáze. Pro tento postup je název databáze **testdb**.

1. Přidejte jednu z následujících závislostí do souboru pom.xml aplikace Azure Spring Cloud. Zvolte závislost, která je vhodná pro váš typ rozhraní API.

    * Typ rozhraní API: Jádro (SQL)

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
          <version>2.1.6</version>
      </dependency>
      ```

    * Typ rozhraní API: MongoDB

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-mongodb</artifactId>
      </dependency>
      ```

    * Typ rozhraní API: Cassandra

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-cassandra</artifactId>
      </dependency>
      ```

    * Typ rozhraní API: Gremlin (graf)

      ```xml
      <dependency>
          <groupId>com.microsoft.spring.data.gremlin</groupId>
          <artifactId>spring-data-gremlin</artifactId>
          <version>2.1.7</version>
      </dependency>
      ```

    * Typ rozhraní API: Tabulka Azure

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-storage-spring-boot-starter</artifactId>
          <version>2.0.5</version>
      </dependency>
      ```

1. Slouží `az spring-cloud app update` k aktualizaci aktuálního `az spring-cloud app deployment create` nasazení nebo k vytvoření nového nasazení. Tyto příkazy budou aktualizovat nebo vytvořit aplikaci s novou závislostí.

1. Přejděte na stránku služby Azure Spring Cloud na webu Azure Portal. Přejděte na **řídicí panel aplikace** a vyberte aplikaci, která se má vázat na Azure Cosmos DB. Tato aplikace je stejná jako ta, kterou jste aktualizovali nebo nasadili v předchozím kroku.

1. Vyberte **Vazby služby**a vyberte **Vytvořit vazbu služby**. Chcete-li formulář vyplnit, vyberte:
   * Hodnota **typu vazby** **Azure Cosmos DB**.
   * Typ rozhraní API.
   * Název databáze.
   * Účet Azure Cosmos DB.

    > [!NOTE]
    > Pokud používáte Cassandra, použijte místo klíče pro název databáze.

1. Restartujte aplikaci výběrem **možnosti Restartovat** na stránce aplikace.

1. Chcete-li zajistit, aby služba byla správně vázána, vyberte název vazby a ověřte její podrobnosti. Pole `property` by mělo být podobné tomuto příkladu:

    ```
    azure.cosmosdb.uri=https://<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak svázat vaši aplikaci Azure Spring Cloud s databází Azure Cosmos DB. Chcete-li se dozvědět, jak svázat aplikaci do mezipaměti Azure Cache pro Redis, pokračujte dalším kurzem.

> [!div class="nextstepaction"]
> [Přečtěte si, jak vytvořit vazbu na mezipaměť Azure Cache for Redis.](spring-cloud-tutorial-bind-redis.md)
