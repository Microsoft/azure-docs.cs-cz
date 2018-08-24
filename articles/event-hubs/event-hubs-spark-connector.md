---
title: Integrace Apache Sparku s Azure Event Hubs | Dokumentace Microsoftu
description: Integrace s Apache Spark, umožňuje strukturované streamování pomocí Event Hubs
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
ms.date: 08/21/2018
ms.author: shvija
ms.openlocfilehash: 688daedf29bbd68c7451be66b47ac90e404d4093
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2018
ms.locfileid: "42746656"
---
# <a name="integrating-apache-spark-with-azure-event-hubs"></a>Integrace Apache Sparku s Azure Event Hubs

Azure Event Hubs se hladce integrují s [Apache Spark](https://spark.apache.org/) umožňující vytváření distribuovaných aplikací datových proudů. Podporuje tato integrační [Spark Core](http://spark.apache.org/docs/latest/rdd-programming-guide.html), [Spark Streaming](http://spark.apache.org/docs/latest/streaming-programming-guide.html), a [strukturované streamování](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html). Konektor služby Event Hubs pro Apache Spark je k dispozici na [Githubu](https://github.com/Azure/azure-event-hubs-spark). Tato knihovna je také k dispozici pro použití v projektech Maven z [centrálního úložiště Maven](http://search.maven.org/#artifactdetails%7Ccom.microsoft.azure%7Cazure-eventhubs-spark_2.11%7C2.1.6%7C).

Tento článek popisuje postup vytvoření aplikace průběžné v [Azure Databricks](https://azure.microsoft.com/services/databricks/). Tento článek používá Azure Databricks, clusterů Spark jsou k dispozici i [HDInsight](../hdinsight/spark/apache-spark-overview.md).

V příkladu v tomto článku se používá dva jazyků Scala poznámkové bloky: jeden pro vysílání datového proudu událostí z centra událostí a druhý k odesílání událostí do něj.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Instance služby Event Hubs. Pokud nemáte, [vytvořit](event-hubs-create.md).
* [Azure Databricks](https://azure.microsoft.com/services/databricks/) instance. Pokud nemáte, [vytvořit](../azure-databricks/quickstart-create-databricks-workspace-portal.md).
* [Vytvoření knihovny pomocí souřadnic maven](https://docs.databricks.com/user-guide/libraries.html#upload-a-maven-package-or-spark-package): `com.microsoft.azure:azure‐eventhubs‐spark_2.11:2.3.1`.

Stream událostí z centra událostí pomocí následujícího kódu:

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
Následující kód odesílá události do vašeho centra událostí pomocí služby batch Spark rozhraní API. Můžete také napsat streamování dotaz k odesílání událostí do centra událostí:

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

## <a name="next-steps"></a>Další postup

Nyní víte, jak nastavit škálovatelné, odolné proti chybám datového proudu pomocí konektoru Event Hubs pro Apache Spark. Další informace o používání služby Event Hubs pomocí strukturovaného streamování a Spark Streaming pomocí těchto odkazů:

* [Strukturované streamování + Průvodce integrace služby Azure Event Hubs](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)
* [Streamování Sparku + Průvodce integrace Event Hubs](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/spark-streaming-eventhubs-integration.md)
