---
title: Přístup k rozhraní API Azure Cosmos DB Cassandra z Azure Databricks
description: Tento článek popisuje, jak pracovat s rozhraním API Azure Cosmos DB Cassandra z Azure Databricks.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 37a06b19285c1196b5d87830ea176d4bd0d4eade
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60894007"
---
# <a name="access-azure-cosmos-db-cassandra-api-data-from-azure-databricks"></a>Přístup k datům rozhraní API Azure Cosmos DB Cassandra z Azure Databricks

Tento článek podrobně popisuje, jak pracovat s rozhraním API Azure Cosmos DB Cassandra ze Spark na [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks).

## <a name="prerequisites"></a>Požadavky

* [Zřízení účtu rozhraní API Azure Cosmos DB Cassandra](create-cassandra-dotnet.md#create-a-database-account)

* [Projděte si základy připojení k rozhraní API Azure Cosmos DB Cassandra](cassandra-spark-generic.md)

* [Zřízení clusteru Azure Databricks](../azure-databricks/quickstart-create-databricks-workspace-portal.md)

* [Zkontrolujte ukázky kódu pro práci s rozhraním CASSANDRA API](cassandra-spark-generic.md#next-steps)

* [Použijte cqlsh pro ověření, pokud dáváte přednost](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Konfigurace instance rozhraní CASSANDRA API pro konektor Cassandra:**

  Konektor pro rozhraní API Cassandra vyžaduje podrobnosti o připojení Cassandra, které mají být inicializovány jako součást kontextu jiskry. Když spustíte poznámkový blok Databricks, kontext jiskry je již inicializován a není vhodné jej zastavit a znovu inicializovat. Jedním z řešení je přidání konfigurace instance rozhraní CASSANDRA API na úrovni clusteru v konfiguraci jiskry clusteru. Jedná se o jednorázovou aktivitu na cluster. Přidejte do konfigurace Spark následující kód jako dvojici hodnot klíče oddělených prostory:
 
  ```scala
  spark.cassandra.connection.host YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com
  spark.cassandra.connection.port 10350
  spark.cassandra.connection.ssl.enabled true
  spark.cassandra.auth.username YOUR_COSMOSDB_ACCOUNT_NAME
  spark.cassandra.auth.password YOUR_COSMOSDB_KEY
  ```

## <a name="add-the-required-dependencies"></a>Přidání požadovaných závislostí

* **Konektor Cassandra Spark:** – K integraci rozhraní API Azure Cosmos DB Cassandra se Sparkem by měl být konektor Cassandra připojený ke clusteru Azure Databricks. Připojení clusteru:

  * Zkontrolujte verzi za běhu Databricks, verzi Spark. Pak najděte [maven souřadnice,](https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) které jsou kompatibilní s konektorem Cassandra Spark a připojte jej ke clusteru. Viz ["Nahrát balíček Maven nebo balíček Spark"](https://docs.databricks.com/user-guide/libraries.html) článek připojit knihovnu konektorů ke clusteru. Například maven souřadnice pro "Databricks Runtime verze 4.3", "Spark 2.3.1" a "Scala 2.11" je`spark-cassandra-connector_2.11-2.3.1`

* **Azure Cosmos DB Cassandra api specifické knihovny:** - vlastní vytvoření připojení je nutné nakonfigurovat zásady opakování z konektoru Cassandra Spark na Azure Cosmos DB Cassandra rozhraní API. `com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0`Přidejte [maven souřadnice](https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) připojit knihovnu ke clusteru.

## <a name="sample-notebooks"></a>Ukázkové poznámkové bloky

Seznam [ukázkových poznámkových bloků](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/notebooks/scala) Azure Databricks je k dispozici v úložišti GitHub, které si můžete stáhnout. Tyto ukázky zahrnují, jak se připojit k rozhraní API Azure Cosmos DB Cassandra ze Sparku a provádět různé operace CRUD s daty. Můžete také [importovat všechny poznámkové bloky](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/dbc) do pracovního prostoru clusteru Databricks a spustit jej. 

## <a name="accessing-azure-cosmos-db-cassandra-api-from-spark-scala-programs"></a>Přístup k rozhraní API Azure Cosmos DB Cassandra z programů Spark Scala

Spark programy, které mají být spuštěny jako automatizované procesy na Azure Databricks se odesílají do clusteru pomocí [spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html)) a naplánované spuštění úloh Azure Databricks.

Následují odkazy, které vám pomůžou začít vytvářet programy Spark Scala pro interakci s rozhraním API Azure Cosmos DB Cassandra.
* [Jak se připojit k rozhraní AZURE Cosmos DB Cassandra API z programu Spark Scala](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)
* [Jak spustit program Spark Scala jako automatizovanou úlohu na Azure Databricks](https://docs.azuredatabricks.net/user-guide/jobs.html)
* [Kompletní seznam ukázek kódu pro práci s rozhraním CASSANDRA API](cassandra-spark-generic.md#next-steps)

## <a name="next-steps"></a>Další kroky

Můžete začít s [vytvářením účtu, databáze a tabulky Cassandra API](create-cassandra-api-account-java.md) pomocí aplikace Java.
