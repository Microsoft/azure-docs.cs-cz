---
title: Integrace Apache Spark s Azure Event Hubs | Microsoft Docs
description: Integrace s Apache Spark povolit strukturovaných streamování pomocí Event Hubs
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: shvija;sethm;sagrewal
ms.openlocfilehash: b430b731bdb38f6fe8af347e082fdfb1ef36a945
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="integrating-apache-spark-with-azure-event-hubs"></a>Integrace Apache Spark s Azure Event Hubs

Azure Event Hubs zajišťuje bezproblémovou integraci s [Apache Spark](https://spark.apache.org/) aby vytváření distribuovány streamování aplikací. Tato integrace podporuje [Spark Core](http://spark.apache.org/docs/latest/rdd-programming-guide.html), [vysílání datového proudu Spark](http://spark.apache.org/docs/latest/streaming-programming-guide.html), [strukturovaných streamování](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html). Konektor služby Event Hubs pro Apache Spark je k dispozici na [Githubu](https://github.com/Azure/azure-event-hubs-spark). Tato knihovna je také k dispozici pro použití v projektech Maven z [Maven centrálním úložišti](http://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-eventhubs-spark_2.11%7C2.1.6%7C).

Tento článek ukazuje, jak provádět průběžnou aplikace v [Azure Databricks](https://azure.microsoft.com/services/databricks/). Tento článek používá [Azure Databricks](https://azure.microsoft.com/services/databricks/), jsou také k dispozici s clustery Spark [HDInsight](../hdinsight/spark/apache-spark-overview.md).

Následující příklad používá dva poznámkových bloků Scala: jeden pro vysílání datového proudu událostí z centra událostí a druhý k odesílání událostí na jiné místo.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte, [vytvořit bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
* Instance služby Event Hubs. Pokud nemáte, [vytvořit](event-hubs-create.md)
* [Azure Databricks](https://azure.microsoft.com/services/databricks/) instance. Pokud nemáte, [vytvořit](../azure-databricks/quickstart-create-databricks-workspace-portal.md)
* [Vytvoření knihovny pomocí maven souřadnice](https://docs.databricks.com/user-guide/libraries.html#upload-a-maven-package-or-spark-package): `com.microsoft.azure:azure‐eventhubs‐spark_2.11:2.3.1`

Datový proud událostí z vašeho centra událostí pomocí následujícího kódu:

```scala
import org.apache.spark.eventhubs._

// To connect to an event hub, EntityPath is required as part of the connection string.
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
val eventhubs = reader.select($"body" cast "string")

eventhubs.writeStream
  .format("console")
  .outputMode("append")
  .start()
  .awaitTermination()
```
Následující příklad kódu odesílá události do vašeho centra událostí pomocí služby batch Spark rozhraní API. Můžete také vytvořit streamování dotaz odesílat události do centra událostí.

```scala
import org.apache.spark.eventhubs._
import org.apache.spark.sql.functions._

// To connect to an event hub, EntityPath is required as part of the connection string.
// Here, we assume that the connection string from the Azure portal does not have the EntityPath part.
val connectionString = ConnectionStringBuilder("{EVENT HUB CONNECTION STRING FROM AZURE PORTAL}")
  .setEventHubName("{EVENT HUB NAME}")
  .build
val ehConf = EventHubsConf(connectionString)

// Create random strings as the body of the message.
val bodyColumn = concat(lit("random nunmber: "), rand()).as("body")

// Write 200 rows to the specified event hub.
val df = spark.range(200).select(bodyColumn)
df.write
  .format("eventhubs")
  .options(eventHubsConf.toMap)
  .save() 
```

## <a name="next-steps"></a>Další postup

Teď víte, jak nastavit škálovatelné, odolné proti chybám datového proudu pomocí konektoru centra událostí pro Apache Spark. Další informace o používání služby Event Hubs s strukturovaných streamování a vysílání datového proudu Spark pomocí následujících tyto odkazy:

* [Strukturované streamování + Azure Event Hubs Integration Guide](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)
* [Vysílání datového proudu Spark + Integration Guide centra událostí](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/spark-streaming-eventhubs-integration.md)
