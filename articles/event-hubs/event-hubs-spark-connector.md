---
title: Integrace s Apache Spark – Azure Event Hubs | Dokumenty společnosti Microsoft
description: Tento článek ukazuje, jak integrovat s Apache Spark povolit strukturované streamování s event huby.
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
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 4c4fd74e9123e1310be297a15090433d365d24cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76311678"
---
# <a name="integrating-apache-spark-with-azure-event-hubs"></a>Integrace Apache Spark s Azure Event Hubs

Azure Event Hubs se bezproblémově integruje s [Apache Spark,](https://spark.apache.org/) aby bylo možné vytvářet distribuované streamovací aplikace. Tato integrace podporuje [Spark Core](https://spark.apache.org/docs/latest/rdd-programming-guide.html), [Spark Streaming](https://spark.apache.org/docs/latest/streaming-programming-guide.html)a [Structured Streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html). Konektor Event Hubs pro Apache Spark je k dispozici na [GitHubu](https://github.com/Azure/azure-event-hubs-spark). Tato knihovna je také k dispozici pro použití v projektech Maven z [Centrálního úložiště Maven](https://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-eventhubs-spark_2.11%7C2.1.6%7C).

Tento článek popisuje, jak vytvořit souvislou aplikaci v [Azure Databricks](https://azure.microsoft.com/services/databricks/). Zatímco tento článek používá Azure Databricks, clustery Spark jsou k dispozici také s [HDInsight](../hdinsight/spark/apache-spark-overview.md).

Příklad v tomto článku používá dva poznámkové bloky Scala: jeden pro streamování událostí z centra událostí a druhý pro odesílání událostí zpět do něj.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ho nemáte, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Instance Centra událostí. Pokud ho nemáte, [vytvořte si ho](event-hubs-create.md).
* Instance [Azure Databricks.](https://azure.microsoft.com/services/databricks/) Pokud ho nemáte, [vytvořte si ho](../azure-databricks/quickstart-create-databricks-workspace-portal.md).
* [Vytvořte knihovnu pomocí maven souřadnic](https://docs.databricks.com/user-guide/libraries.html#upload-a-maven-package-or-spark-package): `com.microsoft.azure:azure‐eventhubs‐spark_2.11:2.3.1`.

Streamujte události z centra událostí pomocí následujícího kódu:

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
Následující kód odesílá události do centra událostí pomocí dávkových api Spark. Můžete také napsat dotaz pro streamování pro odesílání událostí do centra událostí:

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
  .options(ehConf.toMap)
  .save() 
```

## <a name="next-steps"></a>Další kroky

Teď víte, jak nastavit škálovatelný datový proud odolný proti chybám pomocí konektoru Event Hubs pro Apache Spark. Další informace o používání centra událostí se strukturovaným streamováním a streamováním Spark najdete na následujících odkazech:

* [Průvodce strukturovaným streamováním + centrem událostí Azure](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)
* [Průvodce integrací centra pro streamování a události](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/spark-streaming-eventhubs-integration.md)
