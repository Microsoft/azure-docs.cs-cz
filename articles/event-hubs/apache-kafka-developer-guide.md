---
title: Apache Kafka příručka pro vývojáře pro Event Hubs
description: Tento článek obsahuje odkazy na články, které popisují integraci aplikací Kafka s využitím Azure Event Hubs.
ms.date: 06/23/2020
ms.topic: article
ms.openlocfilehash: 757db4faf2a933e366bc818d5cf6aab04d0b08ba
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90061729"
---
# <a name="apache-kafka-developer-guide-for-azure-event-hubs"></a>Apache Kafka příručka pro vývojáře pro Azure Event Hubs
Tento článek obsahuje odkazy na články, které popisují integraci vašich Apache Kafkach aplikací s využitím Azure Event Hubs. 

## <a name="overview"></a>Přehled
Event Hubs poskytuje koncový bod Kafka, který můžou stávající aplikace založené na Kafka použít jako alternativu ke spuštění vlastního clusteru Kafka. Event Hubs funguje s řadou vašich stávajících aplikací Kafka. Další informace najdete v tématu [Event Hubs pro Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)

## <a name="quickstarts"></a>Rychlé starty
Rychlé starty najdete na webu GitHub a v této sadě obsahu, které vám pomůžou rychle zjistit Event Hubs pro Kafka.

### <a name="quickstarts-in-github"></a>Rychlé starty v GitHubu
Podívejte se na následující rychlé starty v úložišti **Azure-Event-Center-for-Kafka** : 

| Jazyk nebo architektura klienta | Description | 
| ------------------------- | ----------- | 
| [.NET](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/dotnet) | <p>V tomto rychlém startu se dozvíte, jak vytvořit a připojit se k Event Hubs koncovému bodu Kafka pomocí ukázkového výrobce a příjemce napsaného v jazyce C# pomocí .NET Core 2,0.</p><p>Tato ukázka vychází z [Apache Kafka klienta .NET](https://github.com/confluentinc/confluent-kafka-dotnet), který se změnil pro použití s Event Hubs pro Kafka.</p> | 
| [Java](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java) | V tomto rychlém startu se dozvíte, jak vytvořit a připojit se k Event Hubs koncovému bodu Kafka pomocí ukázkového výrobce a příjemce napsaného v jazyce Java. |
| [Node.js](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/node) | <p>V tomto rychlém startu se dozvíte, jak vytvořit a připojit se k Event Hubs koncovému bodu Kafka pomocí ukázkového výrobce a příjemce napsaného v uzlu.</p><p>Tato ukázka používá knihovnu [Node-rdkafka](https://github.com/Blizzard/node-rdkafka) . </p>| 
| [Python](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/python) | <p>V tomto rychlém startu se dozvíte, jak vytvořit a připojit se k Event Hubs koncovému bodu Kafka pomocí ukázkového výrobce a příjemce napsaného v Pythonu.</p><p>Tato ukázka vychází z [Apache Kafka klienta Pythonu](https://github.com/confluentinc/confluent-kafka-python), který se změnil pro použití s Event Hubs pro Kafka.</p>|
| [Přejít](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go) | <p>V tomto rychlém startu se dozvíte, jak vytvořit a připojit se k Event Hubs koncovému bodu Kafka pomocí ukázkového výrobce a příjemce, který jste napsali v cestách</p><p>Tato ukázka je založená na [klientovi Apache Kafka golang](https://github.com/confluentinc/confluent-kafka-go), který je upravený pro použití s Event Hubs pro Kafka.</p>| 
| [Sarama Kafka přejít](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go-sarama-client) | V tomto rychlém startu se dozvíte, jak vytvořit a připojit se k Event Hubs koncovému bodu Kafka pomocí ukázkového výrobce a příjemce, který jste napsali v tématu, pomocí [klientské knihovny Sarama Kafka](https://github.com/Shopify/sarama) . |
| [Kafka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafka-cli) | V tomto rychlém startu se dozvíte, jak vytvořit a připojit se k Event Hubs koncovému bodu Kafka pomocí rozhraní příkazového řádku, které je součástí Apache Kafka distribuce.| 
| [Kafkacat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) | kafkacat je spotřebitel a producent příkazového řádku, který není JVM, a to na základě librdkafka, oblíbeného z důvodu jeho rychlosti a malých nároků. Tento rychlý Start obsahuje ukázkovou konfiguraci a několik jednoduchých vzorových příkazů kafkacat. | 
 
### <a name="quickstarts-in-docs"></a>Rychlé starty v DOCS
Přečtěte si rychlý Start: [streamování dat pomocí Event Hubs](event-hubs-quickstart-kafka-enabled-event-hubs.md) v této sadě obsahu pomocí protokolu Kafka, který poskytuje podrobné pokyny pro streamování do Event Hubs. Naučíte se, jak používat vaše producenty a spotřebitelé ke komunikaci s Event Hubs jenom se změnou konfigurace v aplikacích. 


## <a name="tutorials"></a>Kurzy 

### <a name="tutorials-in-github"></a>Kurzy v GitHubu
Další informace najdete v následujících kurzech na GitHubu:

| Kurz | Description | 
| ------------------------- | ----------- | 
| [Akka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java) | V tomto kurzu se dozvíte, jak propojit datové proudy Akka s povoleným Kafkam Event Hubs bez změny klientů protokolu nebo spuštěním vlastních clusterů. K dispozici jsou dva samostatné kurzy s použitím programovacích jazyků **Java** a **Scala** . | 
| [Připojit](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) | Tento dokument vás provede integrací Kafka Connect s Azure Event Hubs a nasazením základních konektorů FileStreamSource a FileStreamSink. I když tyto konektory nejsou určené pro použití v produkčním prostředí, ukazují kompletní scénář Kafka Connect, kde se Azure Event Hubs maskuje jako zprostředkovatel Kafka.| 
| [Prezenční signál](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/filebeat) | Tento dokument vás provede integrací souboru. Kafka a Event Hubs ve výstupu souboru. | 
| [Flink](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink) | V tomto kurzu se dozvíte, jak připojit Apache Flink k Event Hubs s povoleným Kafka bez změny klientů protokolu nebo spuštění vlastních clusterů. | 
| [Fluent](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/fluentd) | Tento dokument vás provede integrací Fluent a Event Hubs s využitím `out_kafka` výstupního modulu plug-in. |
| [Zprostředkovatel komunikace](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/interop) | V tomto kurzu se dozvíte, jak vyměňovat události mezi spotřebiteli a producenty pomocí různých protokolů. |
| [Logstash](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/logstash) | Tento kurz vás provede integrací Logstash s Event Hubs podporou Kafka pomocí modulů plug-in Logstash Kafka Input/Output. | 
| [Nástroje MirrorMaker](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker) | V tomto kurzu se dozvíte, jak může centrum událostí a Kafka nástroje MirrorMaker integrovat existující kanál Kafka do Azure tak, že by měl v Event Hubs službě zrcadlit vstupní datový proud Kafka. |
| [NiFi](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/nifi) | V tomto kurzu se dozvíte, jak připojit Apache NiFi k oboru názvů Event Hubs. | 
| [OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth) | V rychlých startech se dozvíte, jak vytvořit a připojit se k Event Hubs koncovému bodu Kafka pomocí ukázkového výrobce a zákazníka napsaného v prostředích pro jazyky a Java. |
| [Registr schématu pro influent](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/schema-registry) | Tento kurz vás provede integrací registru schématu a Event Hubs pro Kafka. | 
| [Spark](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark) | V tomto kurzu se dozvíte, jak připojit aplikaci Spark k centru událostí bez změny klientů protokolu nebo spustit vlastní clustery Kafka. | 

### <a name="tutorials-in-docs"></a>Kurzy v DOCS
Podívejte se také na kurz: [Apache Kafka procesu pro Event Hubs události pomocí služby Stream Analytics](event-hubs-kafka-stream-analytics.md) v této sadě obsahu, která ukazuje, jak streamovat data do Event Hubs a zpracovat je pomocí Azure Stream Analytics.

## <a name="how-to-guides"></a>Návody
V naší dokumentaci se podívejte na následující Příručky k návodům:

| Článek | Description | 
| ------- | ----------- | 
| [Zrcadlení zprostředkovatele Kafka v centru událostí](event-hubs-kafka-mirror-maker-tutorial.md) | Ukazuje, jak zrcadlit zprostředkovatele Kafka v centru událostí pomocí Kafka nástroje MirrorMaker. |
| [Připojení Apache Sparku k centru událostí](event-hubs-kafka-spark-tutorial.md) | Provede vás připojením aplikace Spark k Event Hubs pro streamování v reálném čase. |
| [Připojení Apache Flinku k centru událostí](event-hubs-kafka-flink-tutorial.md) | Ukazuje, jak připojit Apache Flink k centru událostí bez změny klientů protokolu nebo spuštění vlastních clusterů. |
| [Integrace Apache Kafka připojení k centru událostí (Preview)](event-hubs-kafka-connect-tutorial.md) | Provede vás integrací Kafka připojení k centru událostí a nasazení základních konektorů FileStreamSource a FileStreamSink. |
| [Připojení Akka Streams k centru událostí](event-hubs-kafka-akka-streams-tutorial.md) | Ukazuje, jak připojit Akka datové proudy k centru událostí bez změny klientů protokolu nebo spuštění vlastních clusterů. |
| [Použití jaře Boot Starter pro Apache Kafka s využitím Azure Event Hubs](/azure/developer/java/spring-framework/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub) | Ukazuje, jak nakonfigurovat pořadač založené na jarním cloudovém streamu založeném na jazyce Java, který byl vytvořen pomocí inicializátoru pružinového spouštění pro použití Apache Kafka s Azure Event Hubs. |

## <a name="next-steps"></a>Další kroky
Seznamte se s ukázkami v úložišti GitHub [Azure-Event-for-Kafka](https://github.com/Azure/azure-event-hubs-for-kafka) v části rychlý Start a výukové složky.

Přečtěte si také následující články:

- [Průvodce odstraňováním potíží s Apache Kafka pro Event Hubs](apache-kafka-troubleshooting-guide.md)
- [Nejčastější dotazy – Event Hubs pro Apache Kafka](apache-kafka-frequently-asked-questions.md)
- [Průvodce migrací Apache Kafka pro Event Hubs](apache-kafka-migration-guide.md)



