---
title: Použití Apache Flink pro Apache Kafka – Azure Event Hubs | Dokumenty společnosti Microsoft
description: Tento článek obsahuje informace o tom, jak připojit Apache Flink k centru událostí Azure s povoleným Apache Kafka
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: db877279bcfa7e132841e342cfc25b66bb3ec384
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283595"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Použití Apache Flink se službou Azure Event Hubs pro Apache Kafka
Tento kurz ukazuje, jak připojit Apache Flink k centru událostí bez změny klientů protokolu nebo spuštění vlastních clusterů. Azure Event Hubs podporuje [Apache Kafka verze 1.0.](https://kafka.apache.org/10/documentation.html).

Jednou z klíčových výhod používání Apache Kafka je ekosystém rámců, ke kterému se může připojit. Event Hubs kombinuje flexibilitu Kafky s škálovatelností, konzistencí a podporou ekosystému Azure.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření oboru názvů služby Event Hubs
> * Naklonování ukázkového projektu
> * Spustit výrobce Flink 
> * Spustit příjemce Flink

> [!NOTE]
> Tato ukázka je k dispozici na [GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink)

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit tento kurz, ujistěte se, že máte následující předpoklady:

* Přečtěte si článek [Event Hubs pro Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)
    * Na Ubuntu nainstalujte sadu JDK spuštěním příkazu `apt-get install default-jdk`.
    * Nezapomeňte nastavit proměnnou prostředí JAVA_HOME tak, aby odkazovala na složku, ve které je sada JDK nainstalovaná.
* [Stažení](https://maven.apache.org/download.cgi) a [instalace](https://maven.apache.org/install.html) binárního archivu Maven
    * Na Ubuntu můžete Maven nainstalovat spuštěním příkazu `apt-get install maven`.
* [Git](https://www.git-scm.com/downloads)
    * Na Ubuntu můžete Git nainstalovat spuštěním příkazu `sudo apt-get install git`.

## <a name="create-an-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs

Obor názvů Event Hubs je vyžadován k odesílání nebo přijímání z libovolné služby Event Hubs. Informace o získání koncového bodu Kafka Centers centra událostí najdete v tématu [Vytvoření centra událostí S Povoleno kafka.](event-hubs-create.md) Nezapomeňte zkopírovat připojovací řetězec Event Hubs pro pozdější použití.

## <a name="clone-the-example-project"></a>Naklonování ukázkového projektu

Teď, když máte připojovací řetězec Event Hubs, klonujte Azure Event Hubs `flink` pro úložiště Kafka a přejděte do podsložky:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/flink
```

## <a name="run-flink-producer"></a>Spustit výrobce Flink

Pomocí uvedeného příkladu výrobce Flink odesílejte zprávy službě Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Poskytnutí koncového bodu Kafka centra událostí

#### <a name="producerconfig"></a>producer.config

Aktualizujte `bootstrap.servers` `sasl.jaas.config` hodnoty `producer/src/main/resources/producer.config` a v aplikaci a nasměrujte producenta na koncový bod Event Hubs Kafka se správným ověřováním.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=FlinkExampleProducer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-producer-from-the-command-line"></a>Spustit výrobce z příkazového řádku

Chcete-li spustit výrobce z příkazového řádku, vygenerujte JAR a pak spustit z evnitř Maven (nebo generovat JAR pomocí Maven, pak spustit v Jazyce Java přidáním potřebné Kafka JAR (y) do třídy):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

Výrobce nyní začne odesílat události do centra událostí s `test` povolenou Kafka v tématu a tisk událostí na stdout.

## <a name="run-flink-consumer"></a>Spustit příjemce Flink

Pomocí uvedeného příkladu příjemce přijímat zprávy z Kafka povoleno centra událostí.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Poskytnutí koncového bodu Kafka centra událostí

#### <a name="consumerconfig"></a>consumer.config

Aktualizujte `bootstrap.servers` `sasl.jaas.config` hodnoty `consumer/src/main/resources/consumer.config` a v aplikaci a nasměrujte spotřebitele na koncový bod Event Hubs Kafka se správným ověřováním.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-consumer-from-the-command-line"></a>Spuštění příjemce z příkazového řádku

Chcete-li spustit příjemce z příkazového řádku, vygenerujte JAR a pak spustit z evnitř Maven (nebo generovat JAR pomocí Maven, pak spustit v jazyce Java přidáním potřebné Kafka JAR (y) do třídy):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Pokud centrum událostí obsahuje události (například pokud je spuštěn také váš výrobce), pak `test`spotřebitel nyní začne přijímat události z tématu .

Podrobnější informace o připojení Flink k Kafkovi najdete v [Průvodci konektorem Kafka společnosti Flink.](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html)

## <a name="next-steps"></a>Další kroky
V tomto kurzu se vás naučili, jak připojit Apache Flink k event hubům bez změny klientů protokolu nebo spuštění vlastních clusterů. V rámci tohoto kurzu jste provedli následující kroky: 

> [!div class="checklist"]
> * Vytvoření oboru názvů služby Event Hubs
> * Naklonování ukázkového projektu
> * Spustit výrobce Flink 
> * Spustit příjemce Flink

Další informace o službě Event Hubs a Event Hubs pro ekosystém Kafka najdete v následujícím tématu:  

- [Informace o službě Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs pro Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Vytvoření služby Event Hubs s podporou Kafka](event-hubs-create.md)
- [Streamování do služby Event Hubs z aplikací Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Zrcadlení zprostředkovatele Kafka v centru událostí](event-hubs-kafka-mirror-maker-tutorial.md)
- [Připojení Apache Sparku k centru událostí](event-hubs-kafka-spark-tutorial.md)
- [Integrace aplikace Kafka Connect s centrem událostí](event-hubs-kafka-connect-tutorial.md)
- [Připojení Streamů Akka k centru událostí](event-hubs-kafka-akka-streams-tutorial.md)
- [Prozkoumejte ukázky na našem GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka)
