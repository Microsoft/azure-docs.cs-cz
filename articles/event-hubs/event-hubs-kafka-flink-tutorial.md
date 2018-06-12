---
title: Použití Apache Flink s Azure Event Hubs pro ekosystému Kafka | Microsoft Docs
description: Připojování Apache Flink k Kafka povoleno centra událostí
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: mvc
ms.date: 06/06/2018
ms.author: bahariri
ms.openlocfilehash: cb7ef0e9b6a612e3f4116cb626903770e4035368
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35303369"
---
# <a name="apache-flink-with-event-hubs-for-the-kafka-ecosystem"></a>Apache Flink službou Event Hubs pro ekosystému Kafka

Jedním z klíčových výhod používání Apache Kafka je ekosystému rozhraní, které se může připojit k. Kafka povolená služba Event Hubs kombinuje flexibilitu Kafka s škálovatelnost, konzistence a podpora ekosystému Azure.

V tomto kurzu se dozvíte, jak připojit Apache Flink do centra událostí povoleno Kafka bez změna vaši klienti protokolu nebo vlastní clustery spuštěná. Azure Event Hubs pro Kafka ekosystém podporuje [Apache Kafka verze 1.0.](https://kafka.apache.org/10/documentation.html)

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu, ujistěte se, že máte následující požadavky:

* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
    * Na Ubuntu nainstalujte sadu JDK spuštěním příkazu `apt-get install default-jdk`.
    * Nezapomeňte nastavit proměnnou prostředí JAVA_HOME tak, aby odkazovala na složku, ve které je sada JDK nainstalovaná.
* [Stáhněte si](http://maven.apache.org/download.cgi) a [nainstalovat](http://maven.apache.org/install.html) Maven binární archivu
    * Na Ubuntu můžete Maven nainstalovat spuštěním příkazu `apt-get install maven`.
* [Git](https://www.git-scm.com/downloads)
    * Na Ubuntu můžete Git nainstalovat spuštěním příkazu `sudo apt-get install git`.

## <a name="create-an-event-hubs-namespace"></a>Vytvoření oboru názvů Event Hubs

Obor názvů Event Hubs je potřeba odesílat nebo přijímat z jakékoli služby Event Hubs. V tématu [vytvořit Kafka povoleno Event Hubs](event-hubs-create-kafka-enabled.md) informace o získání koncový bod Kafka centra událostí. Ujistěte se, že zkopírujte připojovací řetězec centra událostí pro pozdější použití.

## <a name="clone-the-example-project"></a>Clone – příklad projektu

Teď, když máte povolené Kafka Event Hubs připojovací řetězec, klonovat úložiště v Azure Event Hubs a přejděte do `flink` podsložky:

```shell
git clone https://github.com/Azure/azure-event-hubs.git
cd azure-event-hubs/samples/kafka/flink
```

## <a name="flink-producer"></a>Producent Flink

Pomocí zadané příklad producent Flink, odesílání zpráv do služby Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Zadejte koncový bod Kafka centra událostí

#### <a name="producerconfig"></a>Producer.config

Aktualizace `bootstrap.servers` a `sasl.jaas.config` hodnoty v `producer/src/main/resources/producer.config` směrovat producent ke koncovému bodu Kafka centra událostí s správné ověření.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=FlinkExampleProducer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-producer-from-the-command-line"></a>Spustit z příkazového řádku

Autor spustit z příkazového řádku, generovat JAR a pak spustit z Maven (nebo generovat JAR pomocí nástroje Maven, spusťte v jazyce Java do cesty pro třídy přidávat i potřeby Kafka JAR(s)):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

Autor nyní zahájí odesílání událostí do Kafka povolená centra událostí v tématu `test` a tisku události, které stdout.

## <a name="flink-consumer"></a>Flink příjemce

Pomocí zadané příjemce příkladu příjem zpráv z povoleno Kafka Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Zadejte koncový bod Kafka centra událostí

#### <a name="consumerconfig"></a>Consumer.config

Aktualizace `bootstrap.servers` a `sasl.jaas.config` hodnoty v `consumer/src/main/resources/consumer.config` pro přímý příjemce ke koncovému bodu Kafka centra událostí s správné ověření.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-consumer-from-the-command-line"></a>Spusťte příjemce z příkazového řádku

K příjemce spustit z příkazového řádku, generovat JAR a pak spustit z Maven (nebo generovat JAR pomocí nástroje Maven, spusťte v jazyce Java do cesty pro třídy přidávat i potřeby Kafka JAR(s)):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Pokud centra událostí povoleno Kafka události (například pokud vaše producent také běží), pak příjemce nyní začne přijímat události z tématu `test`.

Podívejte se na [na Flink Kafka konektor průvodce](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html) podrobné informace o připojení k Kafka Flink.

## <a name="next-steps"></a>Další postup

* [Informace o službě Event Hubs](event-hubs-what-is-event-hubs.md)
* [Informace o službě Event Hubs pro ekosystém Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* Použití [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) k [datového proudu událostí z Kafka místní Kafka povoleno Event Hubs v cloudu.](event-hubs-kafka-mirror-maker-tutorial.md)
* Zjistěte, jak k vysílání datového proudu do Kafka povoleno použití služby Event Hubs [nativních aplikací Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md) nebo [datové proudy Akka](event-hubs-kafka-akka-streams-tutorial.md).
