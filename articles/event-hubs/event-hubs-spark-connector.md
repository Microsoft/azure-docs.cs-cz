---
title: Integrace Apache Spark s Azure Event Hubs | Microsoft Docs
description: "Integrace s Apache Spark povolit strukturovaných streamování pomocí Event Hubs"
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.service: event-hubs
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: shvija
ms.openlocfilehash: 2dcf390b80c119ab21948b982c0812395e45bc01
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="integrating-apache-spark-with-azure-event-hubs"></a>Integrace Apache Spark s Azure Event Hubs

Azure Event Hubs zajišťuje bezproblémovou integraci s [Apache Spark](https://spark.apache.org/) aby vytváření _začátku do konce_ distribuovány streamování aplikací. Tato integrace podporuje [Spark Core](http://spark.apache.org/docs/latest/rdd-programming-guide.html), [vysílání datového proudu Spark](http://spark.apache.org/docs/latest/streaming-programming-guide.html), [strukturovaných streamování](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html). Konektor centra událostí pro Apache Spark je k dispozici na [Githubu](https://github.com/Azure/azure-event-hubs-spark). Tato knihovna je také k dispozici pro použití v projektech Maven z [Maven centrální úložiště](http://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-eventhubs-spark_2.11%7C2.1.6%7C)

Tento článek vám ukáže, jak provádět průběžnou aplikace v [Azure Databrick](https://azure.microsoft.com/services/databricks/). Tento článek používá [Azure Databricks](https://azure.microsoft.com/services/databricks/), jsou také k dispozici s clustery Spark [HDInsight](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-overview).

Následující příklad používá dva Scala notebooky, jeden pro vysílání datového proudu událostí z centra událostí a druhý k odesílání událostí na jiné místo.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte, [vytvořit bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* Instance služby Event Hubs. Pokud nemáte, [vytvořit](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)
* [Azure Databricks](https://azure.microsoft.com/services/databricks/) instance. Pokud nemáte, [vytvořit](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)
* [Vytvoření knihovny pomocí maven souřadnice](https://docs.databricks.com/user-guide/libraries.html#upload-a-maven-package-or-spark-package): `com.microsoft.azure:azure‐eventhubs‐spark_2.11:2.3.0`

Použijte následující fragment kódu v poznámkovém bloku na datový proud události z centra událostí.

```scala
import org.apache.spark.eventhubs._

// To connect to an Event Hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build 
val ehConf = EventHubsConf(connectionString)
  .setStartingPosition(EventPosition.fromEndOfStream)

// Create a stream that reads data from the specified Event Hub.
val reader = spark.readStream
  .format("eventhubs")
  .options(ehConf.toMap)
  .load()

// Select the body column and cast it to a string.
val eventhubs = reader
  .select("CAST (body AS STRING)")
  .as[String]
```
Následující fragment kódu se používá k odesílání událostí do vašeho centra událostí pomocí rozhraní API pro dávkové Spark. Můžete také vytvořit streamování dotaz odesílat události do centra událostí.

```scala
import org.apache.spark.eventhubs._
import org.apache.spark.sql.functions._

// To connect to an Event Hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build

val eventHubsConf = EventHubsConf(connectionString)

// Create a column representing the partitionKey.
val partitionKeyColumn = (col("id") % 5).cast("string").as("partitionKey")
// Create random strings as the body of the message.
val bodyColumn = concat(lit("random nunmber: "), rand()).as("body")

// Write 200 rows to the specified Event Hub.
val df = spark.range(200).select(partitionKeyColumn, bodyColumn)
df.write
  .format("eventhubs")
  .options(eventHubsConf.toMap)
  .save() 

```

Tento článek poskytuje přehled toho, jak funguje konektor centra událostí sestavení v reálném čase, odolnost proti chybám streamování řešení. Další informace o strukturovaných streamování a Event Hubs integrovaného konektoru podle dalších kroků.

## <a name="next-steps"></a>Další postup

* [Strukturované streamování + Azure Event Hubs Integration Guide](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)
* [Vysílání datového proudu Spark + Integration Guide centra událostí](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/spark-streaming-eventhubs-integration.md)
