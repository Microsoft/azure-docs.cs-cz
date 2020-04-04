---
title: Průvodce vývojáři Apache Kafka pro Centra událostí
description: Tento článek obsahuje odkazy na články, které popisují, jak integrovat vaše aplikace Kafka s Azure Event Hubs.
services: event-hubs
author: spelluru
manager: ''
ms.author: spelluru
ms.date: 03/31/2020
ms.topic: article
ms.service: event-hubs
ms.openlocfilehash: 8a72d20101aacaf59b4be5c4a231b132237113f3
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633915"
---
# <a name="apache-kafka-developer-guide-for-azure-event-hubs"></a>Průvodce vývojáři Apache Kafka pro Azure Event Hubs
Tento článek obsahuje odkazy na články, které popisují, jak integrovat vaše aplikace Apache Kafka s Azure Event Hubs. 

## <a name="overview"></a>Přehled
Event Hubs poskytuje koncový bod Kafka, který může být použit pro vaše stávající aplikace založené na Kafka jako alternativu ke spuštění vlastního clusteru Kafka. Event Hubs podporuje protokol Apache Kafka 1.0 a novější a pracuje s vašimi stávajícími aplikacemi Kafka, včetně MirrorMakeru. Další informace najdete [v tématu Event Hubs for Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)

## <a name="quickstarts"></a>Rychlé starty
Rychlé starty najdete na GitHubu a v této sadě obsahu, která vám pomůže rychle rozjet centra událostí pro Kafku.

### <a name="quickstarts-in-github"></a>Rychlé starty v GitHubu
Podívejte se na následující rychlé starty v **azure-event-hubs-for-kafka** repo: 

| Klientský jazyk/rámec | Popis | 
| ------------------------- | ----------- | 
| [.NET](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/dotnet) | <p>Tento rychlý start ukáže, jak vytvořit a připojit se ke koncovému bodu Event Hubs Kafka pomocí ukázkového výrobce a spotřebitele napsaného v c# pomocí rozhraní .NET Core 2.0.</p><p>Tato ukázka je založena na [klientu Apache Kafka .NET](https://github.com/confluentinc/confluent-kafka-dotnet)společnosti Confluent , upraveném pro použití s event huby pro Kafka.</p> | 
| [Java](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java) | Tento rychlý start ukáže, jak vytvořit a připojit se ke koncovému bodu Event Hubs Kafka pomocí ukázkového producenta a spotřebitele napsaného v Jazyce Java. |
| [Node.js](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/node) | <p>Tento rychlý start ukáže, jak vytvořit a připojit se ke koncovému bodu Event Hubs Kafka pomocí ukázkového výrobce a spotřebitele napsaného v uzlu.</p><p>Tato ukázka používá knihovnu [node-rdkafka.](https://github.com/Blizzard/node-rdkafka) </p>| 
| [Python](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/python) | <p>Tento rychlý start ukáže, jak vytvořit a připojit se ke koncovému bodu Event Hubs Kafka pomocí ukázkového producenta a spotřebitele napsaného v Pythonu.</p><p>Tato ukázka je založena na [klientovi Apache Kafka Python](https://github.com/confluentinc/confluent-kafka-python)společnosti Confluent , upraveném pro použití s event huby pro Kafka.</p>|
| [Přejít](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go) | <p>Tento rychlý start ukáže, jak vytvořit a připojit se ke koncovému bodu Event Hubs Kafka pomocí ukázkového výrobce a spotřebitele napsaného v go.</p><p>Tato ukázka je založena na [klientu Apache Kafka Golang](https://github.com/confluentinc/confluent-kafka-go)společnosti Confluent , upraveném pro použití s event huby pro Kafka.</p>| 
| [Sarama kafka Jít](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go-sarama-client) | Tento rychlý start ukáže, jak vytvořit a připojit ke koncovému bodu Event Hubs Kafka pomocí ukázkového producenta a spotřebitele napsaného v go pomocí klientské knihovny [Sarama Kafka.](https://github.com/Shopify/sarama) |
| [Kafka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafka-cli) | Tento rychlý start ukáže, jak vytvořit a připojit se ke koncovému bodu Event Hubs Kafka pomocí cli, které je dodáváno s distribucí Apache Kafka.| 
| [Kafkacat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) | kafkacat je non-JVM příkaz-line spotřebitele a výrobce založený na librdkkafka, populární díky své rychlosti a malé stopy. Tento rychlý start obsahuje ukázkovou konfiguraci a několik jednoduchých příkazů kafkacat. | 
 
### <a name="quickstarts-in-docs"></a>Rychlé starty v DOCS
Podívejte se na rychlý start: [Streamování dat pomocí centra událostí pomocí protokolu Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md) v této sadě obsahu, která poskytuje podrobné pokyny, jak streamovat do centra událostí. Naučíte se, jak používat výrobce a spotřebitele k rozhovoru s event huby s jenom změnou konfigurace ve vašich aplikacích. 


## <a name="tutorials"></a>Kurzy 

### <a name="tutorials-in-github"></a>Výukové programy na GitHubu
Podívejte se na následující výukové programy na GitHubu:

| Kurz | Popis | 
| ------------------------- | ----------- | 
| [Akka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java) | Tento kurz ukazuje, jak připojit Streamy Akka k centru událostí s podporou Kafka bez změny klientů protokolu nebo spuštění vlastních clusterů. Existují dva samostatné výukové programy používající programovací jazyky **Java** a **Scala.** | 
| [Připojit](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) | Tento dokument vás provede integrací Kafka Connect s Azure Event Hubs a nasazením základních konektorů FileStreamSource a FileStreamSink. Zatímco tyto konektory nejsou určeny pro produkční použití, ukazují komplexní scénář Připojení Kafka, kde se Centra událostí Azure maskují jako zprostředkovatele Kafka.| 
| [Souborbeat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/filebeat) | Tento dokument vás provede integrací Filebeat a Event Hubs prostřednictvím výstupu Filebeat Kafka. | 
| [Flink](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink) | Tento kurz vám ukáže, jak připojit Apache Flink k kafkovým event hubům bez změny klientů protokolu nebo spuštění vlastních clusterů. | 
| [Plynutí](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/fluentd) | Tento dokument vás provede integrací Fluentd `out_kafka` a Event Hubs pomocí výstupního pluginu pro Fluentd. |
| [Interop](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/interop) | Tento kurz ukazuje, jak vyměňovat události mezi spotřebiteli a výrobci pomocí různých protokolů. |
| [Logstash](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/logstash) | Tento výukový program vás provede integrací Logstash s kafkovými event huby pomocí vstupních a výstupních pluginů Logstash Kafka. | 
| [MirrorMaker](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker) | Tento kurz ukazuje, jak centrum událostí a Kafka MirrorMaker můžete integrovat existující kanál Kafka do Azure zrcadlením vstupní datový proud Kafka ve službě Event Hubs. |
| [NiFi](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/nifi) | Tento kurz ukáže, jak připojit Apache NiFi k oboru názvů Event Hubs. | 
| [Oauth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth) | Rychlé starty vám ukážou, jak vytvořit koncový bod Event Hubs Kafka a připojit se k němu pomocí ukázkového producenta a spotřebitele napsaného v programovacích jazycích Go a Java. |
| [Registr schématspolečnosti Confluent](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/schema-registry) | Tento kurz vás provede integrací registru schématu a event hubů pro Kafku. | 
| [Spark](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark) | Tento kurz vám ukáže, jak připojit aplikaci Spark k centru událostí bez změny klientů protokolu nebo spuštění vlastních clusterů Kafka. | 

### <a name="tutorials-in-docs"></a>Výukové programy v DOCS
Přečtěte si taky kurz: [Zpracování událostí Apache Kafka for Event Hubs pomocí analýzy Stream](event-hubs-kafka-stream-analytics.md) v této sadě obsahu, která ukazuje, jak streamovat data do event hubů a zpracovat je pomocí Azure Stream Analytics.

## <a name="how-to-guides"></a>Návody
Podívejte se na následující návody v naší dokumentaci:

| Článek | Popis | 
| ------- | ----------- | 
| [Zrcadlení zprostředkovatele Kafka v centru událostí](event-hubs-kafka-mirror-maker-tutorial.md) | Ukazuje, jak zrcadlit zprostředkovatele Kafka v centru událostí pomocí Kafka MirrorMaker. |
| [Připojení Apache Sparku k centru událostí](event-hubs-kafka-spark-tutorial.md) | Provede vás připojením aplikace Spark k event hubům pro streamování v reálném čase. |
| [Připojení Apache Flinku k centru událostí](event-hubs-kafka-flink-tutorial.md) | Ukazuje, jak připojit Apache Flink k centru událostí bez změny klientů protokolu nebo spuštění vlastních clusterů. |
| [Integrace Apache Kafka Connect s centrem událostí (Preview)](event-hubs-kafka-connect-tutorial.md) | Provede vás integrací kafka connect s centrem událostí a nasazením základních konektorů FileStreamSource a FileStreamSink. |
| [Připojení Streamů Akka k centru událostí](event-hubs-kafka-akka-streams-tutorial.md) | Ukazuje, jak připojit Akka Streams k centru událostí bez změny klientů protokolu nebo spuštění vlastních clusterů. |
| [Použití startéru jarního startu pro Apache Kafka s Azure Event Hubs](/azure/java/spring-framework/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub) | Ukazuje, jak nakonfigurovat java-založené Spring Cloud Stream Binder vytvořené pomocí jarní spuštění Inicializátor používat Apache Kafka s Azure Event Hubs. |

## <a name="next-steps"></a>Další kroky
Projděte si ukázky v [azure-event-hubs-for-kafka v azure-event-hubs-for-kafka](https://github.com/Azure/azure-event-hubs-for-kafka) v rámci složek rychlého startu a kurzů.

