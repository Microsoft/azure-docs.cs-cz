---
title: Připojení k aplikaci Apache Spark – Azure Event Hubs | Microsoft Docs
description: Tento článek poskytuje informace o tom, jak používat Apache Spark s Azure Event Hubs pro Kafka.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 84184ed3dffee97863b93c592d1cd577df313605
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92913734"
---
# <a name="connect-your-apache-spark-application-with-azure-event-hubs"></a>Připojení aplikace Apache Spark k Azure Event Hubs
Tento kurz vás provede připojením aplikace Spark k Event Hubs pro streamování v reálném čase. Tato integrace umožňuje streamování bez nutnosti měnit klienty protokolu nebo spouštět vlastní clustery Kafka nebo Zookeeper. Tento kurz vyžaduje Apache Spark v 2.4 + a Apache Kafka v 2.0 +.

> [!NOTE]
> Tato ukázka je k dispozici na [GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark/) .

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření oboru názvů služby Event Hubs
> * Naklonování ukázkového projektu
> * Spuštění Sparku
> * Čtení ze služby Event Hubs pro ekosystém Kafka
> * Zápis do služby Event Hubs pro ekosystém Kafka

## <a name="prerequisites"></a>Předpoklady

Než začnete s tímto kurzem, ujistěte se, že máte následující:
-   Předplatné Azure. Pokud žádné nemáte, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/).
-   [Apache Spark verze 2.4](https://spark.apache.org/downloads.html)
-   [Apache Kafka verze 2.0]( https://kafka.apache.org/20/documentation.html)
-   [Git](https://www.git-scm.com/downloads)

> [!NOTE]
> Adaptér Spark-Kafka byl aktualizován tak, aby od Sparku v2.4 podporoval Kafka v2.0. V předchozích verzích Sparku adaptér podporoval Kafka v0.10 a novější, ale spoléhal se konkrétně na rozhraní API Kafka v0.10. Vzhledem k tomu, že Event Hubs pro ekosystém Kafka nepodporuje Kafka v0.10, nepodporuje ani adaptéry Spark-Kafka ze starších verzí Sparku než v2.4.


## <a name="create-an-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs
K odesílání do jakékoli služby Event Hubs a příjmu z ní se vyžaduje obor názvů služby Event Hubs. Pokyny k vytvoření oboru názvů a centra událostí najdete v tématu [vytvoření centra událostí](event-hubs-create.md) . Získejte plně kvalifikovaný název domény a připojovací řetězec služby Event Hubs pro pozdější použití. Pokyny najdete v tématu [Získání připojovacího řetězce služby Event Hubs](event-hubs-get-connection-string.md). 

## <a name="clone-the-example-project"></a>Naklonování ukázkového projektu
Naklonujte úložiště Azure Event Hubs a přejděte do podsložky `tutorials/spark`:

```bash
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/spark
```

## <a name="read-from-event-hubs-for-kafka"></a>Čtení ze služby Event Hubs pro ekosystém Kafka
Stačí několik změn konfigurace a můžete začít číst ze služby Event Hubs pro ekosystém Kafka. Aktualizujte hodnoty **BOOTSTRAP_SERVERS** a **EH_SASL** s použitím podrobností z vašeho oboru názvů a můžete začít streamovat se službou Event Hubs stejně jako s Kafka. Kompletní vzorový kód najdete v souboru sparkConsumer.scala na GitHubu. 

```scala
//Read from your Event Hub!
val df = spark.readStream
    .format("kafka")
    .option("subscribe", TOPIC)
    .option("kafka.bootstrap.servers", BOOTSTRAP_SERVERS)
    .option("kafka.sasl.mechanism", "PLAIN")
    .option("kafka.security.protocol", "SASL_SSL")
    .option("kafka.sasl.jaas.config", EH_SASL)
    .option("kafka.request.timeout.ms", "60000")
    .option("kafka.session.timeout.ms", "30000")
    .option("kafka.group.id", GROUP_ID)
    .option("failOnDataLoss", "true")
    .load()

//Use dataframe like normal (in this example, write to console)
val df_write = df.writeStream
    .outputMode("append")
    .format("console")
    .start()
```

## <a name="write-to-event-hubs-for-kafka"></a>Zápis do služby Event Hubs pro ekosystém Kafka
Můžete také zapisovat do Event Hubs stejným způsobem jako při psaní do Kafka. Nezapomeňte aktualizovat konfiguraci a změnit hodnoty **BOOTSTRAP_SERVERS** a **EH_SASL** s použitím informací z vašeho oboru názvů služby Event Hubs.  Kompletní vzorový kód najdete v souboru sparkProducer.scala na GitHubu. 

```scala
df = /**Dataframe**/

//Write to your Event Hub!
df.writeStream
    .format("kafka")
    .option("topic", TOPIC)
    .option("kafka.bootstrap.servers", BOOTSTRAP_SERVERS)
    .option("kafka.sasl.mechanism", "PLAIN")
    .option("kafka.security.protocol", "SASL_SSL")
    .option("kafka.sasl.jaas.config", EH_SASL)
    .option("checkpointLocation", "./checkpoint")
    .start()
```



## <a name="next-steps"></a>Další kroky
Další informace o Event Hubs a Event Hubs pro Kafka najdete v následujících článcích:  

- [Zrcadlení zprostředkovatele Kafka v centru událostí](event-hubs-kafka-mirror-maker-tutorial.md)
- [Připojení Apache Flinku k centru událostí](event-hubs-kafka-flink-tutorial.md)
- [Integrace Kafka Connect do centra událostí](event-hubs-kafka-connect-tutorial.md)
- [Prozkoumejte ukázky na našem GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Připojení Akka Streams k centru událostí](event-hubs-kafka-akka-streams-tutorial.md)
- [Apache Kafka příručka pro vývojáře pro Azure Event Hubs](apache-kafka-developer-guide.md)

