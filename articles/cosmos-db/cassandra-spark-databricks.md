---
title: Přístup k Azure Cosmos DB Cassandra API z Azure Databricks
description: Tento článek popisuje, jak pracovat s Azure Cosmos DB Cassandra API z Azure Databricks.
services: cosmos-db
author: anagha-microsoft
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.devlang: spark-scala
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ankhanol
ms.openlocfilehash: e1d8f41c55ffd453507804b005d10620665b512c
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47222015"
---
# <a name="access-azure-cosmos-db-cassandra-api-data-from-azure-databricks"></a>Přístup k datům Azure Cosmos DB Cassandra API z Azure Databricks

Tento článek podrobně popisuje postupy workwith Azure Cosmos DB Cassandra API z aplikace Spark na [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks).

## <a name="prerequisites"></a>Požadavky

* [Zřídit účet Azure Cosmos DB Cassandra API](create-cassandra-dotnet.md#create-a-database-account)

* [Přečtěte si základní informace o připojení k Azure Cosmos DB Cassandra API](cassandra-spark-generic.md)

* [Zřízení clusteru služby Azure Databricks](../azure-databricks/quickstart-create-databricks-workspace-portal.md)

* [Projděte si ukázky kódu pro práci s rozhraním Cassandra API](cassandra-spark-generic.md#next-steps)

* [Pokud tedy chcete používat cqlsh pro ověření](cassandra-spark-generic.md#connecting-to-azure-cosmos-db-cassandra-api-from-spark)

* **Konfigurace instance rozhraní Cassandra API pro konektor Cassandra:**

  Konektor pro Apache Cassandra API vyžaduje podrobnosti připojení Cassandra nutné jej inicializovat jako součást kontextu spark. Při spuštění poznámkového bloku Databricks již byl inicializován kontext spark a není vhodné k zastavení a inicializujte ho znovu. Jedním řešením je přidání konfigurace instance rozhraní Cassandra API na úrovni clusteru, v konfiguraci clusteru spark. Toto je jednorázovou aktivitou za cluster. Přidejte následující kód do konfigurace Spark jako mezerou oddělený pár klíč-hodnota:
 
  ```scala
  spark.cassandra.connection.host YOUR_COSMOSDB_ACCOUNT_NAME.cassandra.cosmosdb.azure.com
  spark.cassandra.connection.port 10350
  spark.cassandra.connection.ssl.enabled true
  spark.cassandra.auth.username YOUR_COSMOSDB_ACCOUNT_NAME
  spark.cassandra.auth.password YOUR_COSMOSDB_KEY
  ```

## <a name="add-the-required-dependencies"></a>Přidejte požadované závislosti

* **Konektor Cassandra Spark:** – Pokud chcete integrovat Azure Cosmos DB Cassandra API s využitím rozhraní Cassandra konektoru je třeba připojit k Azure Databricks pro cluster Spark. Připojení ke clusteru:

  * Zkontrolujte verzi modulu runtime Databricks verze Sparku. Vyhledejte [souřadnice maven](https://mvnrepository.com/artifact/com.datastax.spark/spark-cassandra-connector) , které jsou kompatibilní s konektor Cassandra Spark a připojte ho ke clusteru. V tématu ["Nahrát Maven balíček nebo balíček Spark"](https://docs.databricks.com/user-guide/libraries.html) článku knihovna konektorů připojit ke clusteru. Například souřadnice maven pro "Modul Databricks Runtime verze 4.3", "Spark 2.3.1" a "je Scala 2.11" `spark-cassandra-connector_2.11-2.3.1`

* **Azure Cosmos DB Cassandra API konkrétní knihovnu:** – objekt pro vytváření vlastní připojení je potřeba ke konfiguraci zásady opakování z konektoru Cassandra Spark k rozhraní Cassandra API služby Azure Cosmos DB. Přidat `com.microsoft.azure.cosmosdb:azure-cosmos-cassandra-spark-helper:1.0.0` [souřadnice maven](https://search.maven.org/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper/1.0.0/jar) připojení knihovny ke clusteru.

## <a name="sample-notebooks"></a>Ukázkové poznámkové bloky

Seznam Azure Databricks [ukázkové poznámkové bloky](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/notebooks/scala) jsou k dispozici v úložišti Github si můžete stáhnout. Tyto ukázky zahrnují postup připojení k Azure Cosmos DB Cassandra API z aplikace Spark a provádění různých operací CRUD u data. Můžete také [import všech poznámkových bloků](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-notebooks-databricks/tree/master/dbc) do vaší Databricks clusteru pracovního prostoru a spustíme ji. 

## <a name="accessing-azure-cosmos-db-cassandra-api-from-spark-scala-programs"></a>Přístup k rozhraní Cassandra API služby Azure Cosmos DB z aplikací Spark Scala

Programy Spark ke spuštění jako automatizované procesy v Azure Databricks jsou odeslána do clusteru s použitím [skriptu spark-submit](https://spark.apache.org/docs/latest/submitting-applications.html)) a naplánované na spuštění úlohy Azure Databricks.

Níže jsou uvedeny odkazy vám pomůžou začít vytvářet aplikace Spark Scala pro interakci s Azure Cosmos DB Cassandra API.
* [Jak se připojit k Azure Cosmos DB Cassandra API z aplikace Spark Scala](https://github.com/Azure-Samples/azure-cosmos-db-cassandra-api-spark-connector-sample/blob/master/src/main/scala/com/microsoft/azure/cosmosdb/cassandra/SampleCosmosDBApp.scala)
* [Spuštění programu v jazyce Spark Scala jako automatizované úlohy v Azure Databricks](https://docs.azuredatabricks.net/user-guide/jobs.html)
* [Úplný seznam všech ukázek kódu pro práci s rozhraním Cassandra API](cassandra-spark-generic.md#next-steps)

## <a name="next-steps"></a>Další postup

Začínáme s [vytvoření účtu rozhraní Cassandra API, databáze a tabulky](create-cassandra-api-account-java.md) s použitím aplikace v Javě.
