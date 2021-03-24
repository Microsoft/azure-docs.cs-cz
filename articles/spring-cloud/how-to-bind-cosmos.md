---
title: Kurz – vytvoření vazby Azure Cosmos DB k aplikaci Azure jaře Cloud
description: Přečtěte si, jak vytvořit vazby Azure Cosmos DB k aplikaci Azure jaře Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: e3af90eb00bd3f02fbcc3e59d2fa6ee5a1b3a9e8
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877850"
---
# <a name="tutorial-bind-an-azure-cosmos-db-database-to-your-azure-spring-cloud-application"></a>Kurz: vytvoření vazby databáze Azure Cosmos DB k aplikaci Azure jaře Cloud

**Tento článek se týká:** ✔️ Java

Místo ruční konfigurace aplikací pro spouštění pružiny můžete automaticky navazovat výběr služeb Azure na své aplikace s využitím jarního cloudu Azure. Tento článek ukazuje, jak vytvořit vazby aplikace k databázi Azure Cosmos DB.

Požadavky:

* Nasazená instance cloudu Azure pro jaře. Začněte podle našeho [rychlého startu v části nasazení prostřednictvím Azure CLI](spring-cloud-quickstart.md) .
* Účet Azure Cosmos DB s minimální úrovní oprávnění přispěvatele.

## <a name="bind-azure-cosmos-db"></a>Azure Cosmos DB vazby

Azure Cosmos DB má pět různých typů rozhraní API, které podporují vazby. Následující postup ukazuje, jak je použít:

1. Vytvoří účet databáze Azure Cosmos DB. Nápovědu najdete v rychlém startu o [Vytvoření databáze](../cosmos-db/create-cosmosdb-resources-portal.md) . 

1. Poznamenejte si název vaší databáze. Pro tento postup je název databáze **TestDB**.

1. Přidejte jednu z následujících závislostí do souboru pom.xml svojí cloudové aplikace Azure na jaře. Vyberte závislost, která je vhodná pro váš typ rozhraní API.

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

1. Použijte `az spring-cloud app update` k aktualizaci aktuálního nasazení nebo použijte `az spring-cloud app deployment create` k vytvoření nového nasazení. Tyto příkazy buď aktualizují, nebo vytvoří aplikaci s novou závislostí.

1. V Azure Portal přejdete na stránku služby jarní cloud Azure. Přejít na **řídicí panel aplikace** a vybrat aplikaci, pro kterou chcete vytvořit vazby Azure Cosmos DB. Tato aplikace je stejná jako ta, kterou jste aktualizovali nebo nasadili v předchozím kroku.

1. Vyberte možnost **vazba služby** a vyberte možnost **vytvořit vazbu služby**. Chcete-li vyplnit formulář, vyberte:
   * Hodnota **typu vazby** **Azure Cosmos DB**.
   * Typ rozhraní API.
   * Název databáze.
   * Účet Azure Cosmos DB.

    > [!NOTE]
    > Pokud používáte Cassandra, použijte místo pro název databáze klíč.

1. Restartujte aplikaci tak, že na stránce aplikace vyberete **restartovat** .

1. Chcete-li zajistit správnou vazbu služby, vyberte název vazby a ověřte jeho podrobnosti. `property`Pole by mělo být podobné jako v tomto příkladu:

    ```
    azure.cosmosdb.uri=https://<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak vytvořit datovou aplikaci v Azure pružině v databázi Azure Cosmos DB. Další informace o vázání služeb pro vaši aplikaci najdete v tématu [vazba na mezipaměť Azure pro Redis Cache](spring-cloud-howto-bind-redis.md).