---
title: Kurz – vytvoření vazby Azure Cosmos DB k aplikaci Azure jaře Cloud
description: V tomto kurzu se naučíte vytvořit vazby Azure Cosmos DB k aplikaci Azure pro jarní Cloud.
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 7e796c6f8b2ae17ba267a19da1d909087163d99c
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708833"
---
# <a name="tutorial-bind-an-azure-cosmos-db-to-your-azure-spring-cloud-application"></a>Kurz: vytvoření vazby Azure Cosmos DB k aplikaci Azure jaře Cloud

Pomocí Azure jaře cloudu můžete automaticky navazovat vybrané služby Azure na vaše aplikace místo ruční konfigurace aplikace pro spouštění pružiny. Tento článek ukazuje, jak vytvořit propojení aplikace s Azure Cosmos DB.

Požadavky:
* Nasazená instance cloudu Azure pro jaře.  Začněte podle našeho [rychlého](spring-cloud-quickstart-launch-app-cli.md) startu.
* Účet Azure Cosmos DB s minimální úrovní oprávnění přispěvatele.

## <a name="bind-azure-cosmos-db"></a>Azure Cosmos DB vazby

Azure Cosmos DB má pět různých typů rozhraní API, které podporují vazby:

1. Vytvoří účet databáze Azure Cosmos DB. Nápovědu k vytvoření databáze [najdete v tomto článku](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal) . Poznamenejte si název vaší databáze. Náš název je pojmenován `testdb`.

1. Do `pom.xml` vaší jarní cloudové aplikace přidejte jednu z následujících závislostí podle vašeho typu rozhraní API.
    
    #### <a name="api-type-core-sql"></a>Typ rozhraní API: jádro (SQL)

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
        <version>2.1.6</version>
    </dependency>
    ```
    
    #### <a name="api-type-mongodb"></a>Typ rozhraní API: MongoDB

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-mongodb</artifactId>
    </dependency>
    ```

    #### <a name="api-type-cassandra"></a>Typ rozhraní API: Cassandra

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-cassandra</artifactId>
    </dependency>
    ```

    #### <a name="api-type-gremlin-graph"></a>Typ rozhraní API: Gremlin (Graph)

    ```xml
    <dependency>
        <groupId>com.microsoft.spring.data.gremlin</groupId>
        <artifactId>spring-data-gremlin</artifactId>
        <version>2.1.7</version>
    </dependency>
    ```

    #### <a name="api-type-azure-table"></a>Typ rozhraní API: tabulka Azure

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-storage-spring-boot-starter</artifactId>
        <version>2.0.5</version>
    </dependency>
    ```

1. Aktualizujte aktuální nasazení pomocí `az spring-cloud app update` nebo vytvořte nové nasazení pro tuto změnu pomocí `az spring-cloud app deployment create`.  Tyto příkazy buď aktualizují, nebo vytvoří aplikaci s novou závislostí.

1. V Azure Portal přejdete na stránku služby jarní cloud Azure. Jedná se o stejnou aplikaci, kterou jste aktualizovali nebo nasadili v předchozím kroku. Najděte **řídicí panel aplikace** a vyberte aplikaci, kterou chcete vytvořit pomocí Cosmos DB. V dalším kroku vyberte `Service binding` a vyberte tlačítko `Create service binding`. Vyplňte formulář, vyberte **typ vazby** `Azure Cosmos DB`, typ rozhraní API, název databáze a účet Azure Cosmos DB.

    > [!NOTE]
    > Pokud používáte Cassandra, použijte místo pro název databáze klíč.

1. Restartujte aplikaci tak, že na stránce aplikace vyberete tlačítko **restartovat** .

1. Chcete-li zajistit správnou vazbu služby, vyberte název vazby a ověřte jeho podrobnosti. Pole `property` by mělo být podobné následujícímu:

    ```
    azure.cosmosdb.uri=https:/<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak vytvořit datovou aplikaci v Azure jaře pro CosmosDB.  Pokud se chcete dozvědět, jak navázat aplikaci na Azure Redis Cache, pokračujte k dalšímu kurzu.

> [!div class="nextstepaction"]
> [Připojte k Azure Redis Cache cloudovou aplikaci Azure pružiny](spring-cloud-tutorial-bind-redis.md).
