---
title: Kurz – vytvoření vazby Azure Cosmos DB k aplikaci Azure jaře Cloud
description: V tomto kurzu se naučíte vytvořit vazby Azure Cosmos DB k aplikaci Azure pro jarní Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: brendm
ms.openlocfilehash: 1566b6ab59e858217adcf6818e1d62f851f37eb1
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277571"
---
# <a name="bind-an-azure-cosmos-db-database-to-your-azure-spring-cloud-application"></a>Vytvoření vazby databáze Azure Cosmos DB k aplikaci Azure jaře Cloud

Místo ruční konfigurace aplikací pro spouštění pružiny můžete automaticky navazovat výběr služeb Azure na své aplikace s využitím jarního cloudu Azure. Tento článek ukazuje, jak vytvořit vazby aplikace k databázi Azure Cosmos DB.

Požadavky:

* Nasazená instance cloudu Azure pro jaře. Začněte podle našeho [rychlého startu v části nasazení prostřednictvím Azure CLI](spring-cloud-quickstart-launch-app-cli.md) .
* Účet Azure Cosmos DB s minimální úrovní oprávnění přispěvatele.

## <a name="bind-azure-cosmos-db"></a>Azure Cosmos DB vazby

Azure Cosmos DB má pět různých typů rozhraní API, které podporují vazby. Následující postup ukazuje, jak je použít:

1. Vytvoří účet databáze Azure Cosmos DB. Nápovědu najdete v rychlém startu o [Vytvoření databáze](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal) . 

1. Poznamenejte si název vaší databáze. Pro tento postup je název databáze **TestDB**.

1. Přidejte jednu z následujících závislostí do souboru pom. XML vaší aplikace vaší jarní cloudové služby Azure. Vyberte závislost, která je vhodná pro váš typ rozhraní API.

    * Typ rozhraní API: jádro (SQL)

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

    * Typ rozhraní API: Gremlin (Graph)

      ```xml
      <dependency>
          <groupId>com.microsoft.spring.data.gremlin</groupId>
          <artifactId>spring-data-gremlin</artifactId>
          <version>2.1.7</version>
      </dependency>
      ```

    * Typ rozhraní API: tabulka Azure

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-storage-spring-boot-starter</artifactId>
          <version>2.0.5</version>
      </dependency>
      ```

1. K aktualizaci aktuálního nasazení použijte `az spring-cloud app update`, nebo pomocí `az spring-cloud app deployment create` vytvořit nové nasazení. Tyto příkazy buď aktualizují, nebo vytvoří aplikaci s novou závislostí.

1. V Azure Portal přejdete na stránku služby jarní cloud Azure. Přejít na **řídicí panel aplikace** a vybrat aplikaci, pro kterou chcete vytvořit vazby Azure Cosmos DB. Tato aplikace je stejná jako ta, kterou jste aktualizovali nebo nasadili v předchozím kroku.

1. Vyberte možnost **vazba služby**a vyberte možnost **vytvořit vazbu služby**. Chcete-li vyplnit formulář, vyberte:
   * Hodnota **typu vazby** **Azure Cosmos DB**.
   * Typ rozhraní API.
   * Název databáze.
   * Účet Azure Cosmos DB.

    > [!NOTE]
    > Pokud používáte Cassandra, použijte místo pro název databáze klíč.

1. Restartujte aplikaci tak, že na stránce aplikace vyberete **restartovat** .

1. Chcete-li zajistit správnou vazbu služby, vyberte název vazby a ověřte jeho podrobnosti. Pole `property` by mělo být podobné jako v tomto příkladu:

    ```
    azure.cosmosdb.uri=https://<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak vytvořit datovou aplikaci v Azure pružině v databázi Azure Cosmos DB. Další informace o tom, jak vytvořit propojení aplikace s mezipamětí Azure cache pro Redis Cache, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Naučte se vytvořit službu Azure cache pro Redis Cache.](spring-cloud-tutorial-bind-redis.md)
