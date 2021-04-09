---
title: Přístup k Azure Cosmos DB rozhraní API Cassandra z Azure Databricks
description: Tento článek popisuje, jak pracovat s Azure Cosmos DB rozhraní API Cassandra z Azure Databricks.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 0a83dd143ae626108fdf8d2645b8cc368a3f3e05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100516562"
---
# <a name="access-azure-cosmos-db-cassandra-api-data-from-azure-databricks"></a>Přístup k datům rozhraní API Cassandra služby Azure Cosmos DB z Azure Databricks
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Tento článek podrobně popisuje, jak pracovat s Azure Cosmos DB rozhraní API Cassandra ze Sparku na [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks).

## <a name="prerequisites"></a>Požadavky

* [Zřídit účet Azure Cosmos DB rozhraní API Cassandra](create-cassandra-dotnet.md#create-a-database-account)

* [Projděte si základy připojení k Azure Cosmos DB rozhraní API Cassandra](cassandra-spark-generic.md)

* [Zřízení clusteru Azure Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal)

* [Přečtěte si ukázky kódu pro práci s rozhraní API Cassandra](cassandra-spark-generic.md#next-steps)

* [Pro ověření použijte cqlsh, pokud to dáváte přednost](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Konfigurace instance rozhraní API Cassandra pro konektor Cassandra:**

  Konektor pro rozhraní API Cassandra vyžaduje, aby byly podrobnosti připojení Cassandra inicializovány jako součást kontextu Spark. Při spuštění poznámkového bloku datacihly už je kontext Spark inicializovaný a není vhodné ho zastavit a znovu inicializovat. Jedním z řešení je Přidat konfiguraci rozhraní API Cassandra instance na úrovni clusteru v konfiguraci nástroje Cluster Spark. Jedná se o jednorázovou aktivitu na cluster. Přidejte následující kód do konfigurace Sparku jako dvojici klíčového čísla odděleného mezerou:
 
  ```scala
  spark.cassandra.connection.host YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com
  spark.cassandra.connection.port 10350
  spark.cassandra.connection.ssl.enabled true
  spark.cassandra.auth.username YOUR_COSMOSDB_ACCOUNT_NAME
  spark.cassandra.auth.password YOUR_COSMOSDB_KEY
  ```

## <a name="add-the-required-dependencies"></a>Přidání požadovaných závislostí

* **Konektor Cassandra Spark:** – aby bylo možné integrovat Azure Cosmos DB rozhraní API Cassandra pomocí Sparku, konektor Cassandra by měl být připojen ke clusteru Azure Databricks. Postup připojení clusteru:

  * Podívejte se na verzi modulu runtime datacihly, verzi Sparku. Pak vyhledejte [souřadnice Maven](https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) , které jsou kompatibilní s konektorem Spark Cassandra, a připojte je ke clusteru. Pokud chcete připojit knihovnu konektorů ke clusteru, přečtěte si článek [odeslání balíčku Maven nebo balíčku Spark](https://docs.databricks.com/user-guide/libraries.html) . Například souřadnice Maven pro "Databricks Runtime verze 4,3", "Spark 2.3.1" a "Scala 2,11" je `spark-cassandra-connector_2.11-2.3.1`

* **Azure Cosmos DB knihovna specifická pro rozhraní API Cassandra:** – ke konfiguraci zásad opakování z konektoru Spark Cassandra na Azure Cosmos DB rozhraní API Cassandra se vyžaduje vlastní továrna připojení. Přidejte `com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0` [souřadnice Maven](https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) pro připojení knihovny ke clusteru.

## <a name="sample-notebooks"></a>Ukázkové poznámkové bloky

Seznam [ukázkových poznámkových bloků](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/main/notebooks/scala) Azure Databricks najdete v úložišti GitHub, abyste si ho stáhli. Tyto ukázky zahrnují, jak se připojit k Azure Cosmos DB rozhraní API Cassandra ze Sparku a provádět různé operace CRUD s daty. Do svého pracovního prostoru clusteru datacihly můžete také [importovat všechny poznámkové bloky](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/main/dbc) a spustit je. 

## <a name="accessing-azure-cosmos-db-cassandra-api-from-spark-scala-programs"></a>Přístup k Azure Cosmos DB rozhraní API Cassandra z programů Spark Scala

Programy Spark, které mají být spuštěny jako automatizované procesy na Azure Databricks jsou odesílány do clusteru pomocí funkce [Spark-Submit](https://spark.apache.org/docs/latest/submitting-applications.html)) a naplánovány ke spuštění prostřednictvím Azure Databricks úloh.

Níže najdete odkazy, které vám pomůžou začít vytvářet programy Spark Scala pro interakci s Azure Cosmos DB rozhraní API Cassandra.
* [Jak se připojit k Azure Cosmos DB rozhraní API Cassandra z programu Spark Scala](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/main/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)
* [Jak spustit program Spark Scala jako automatizovanou úlohu na Azure Databricks](/azure/databricks/jobs)
* [Úplný seznam ukázek kódu pro práci s rozhraní API Cassandra](cassandra-spark-generic.md#next-steps)

## <a name="next-steps"></a>Další kroky

Začněte s [vytvářením účtu rozhraní API Cassandra, databáze a tabulky](create-cassandra-api-account-java.md) pomocí aplikace Java.
