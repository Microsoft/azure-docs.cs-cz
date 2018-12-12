---
title: Použití Apache Flink pro Apache Kafka – Azure Event Hubs | Dokumentace Microsoftu
description: Tento článek obsahuje informace o tom, jak se připojit k Apache Kafka Apache Flink povolené Centrum událostí Azure
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: cb964451d25bb381ee9cca84628852a61e4be9b6
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090903"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Použití Apache Flink s Azure Event Hubs pro Apache Kafka
V tomto kurzu se dozvíte, jak připojit Apache Flink Kafka povolené event hubs, aniž by změna vašim klientům protokolu nebo spuštění vlastní clustery. Podporuje Azure Event Hubs [Apache Kafka verze 1.0.](https://kafka.apache.org/10/documentation.html).

Jednou z klíčových výhod používání Apache Kafka je v ekosystému rozhraní se může připojit k. Kafka povolená služba Event Hubs kombinuje flexibilitu Kafka s škálovatelnosti, konzistenci a podporu ekosystému Azure.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření oboru názvů služby Event Hubs
> * Naklonování ukázkového projektu
> * Spustit Flink výrobce 
> * Spusťte Flink příjemce

> [!NOTE]
> Tato ukázka je k dispozici na [GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu, nezapomeňte, že jsou splněné následující požadavky:

* Přečtěte si článek [Event Hubs pro Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)
    * Na Ubuntu nainstalujte sadu JDK spuštěním příkazu `apt-get install default-jdk`.
    * Nezapomeňte nastavit proměnnou prostředí JAVA_HOME tak, aby odkazovala na složku, ve které je sada JDK nainstalovaná.
* [Stáhněte si](http://maven.apache.org/download.cgi) a [nainstalovat](http://maven.apache.org/install.html) binární archiv Maven
    * Na Ubuntu můžete Maven nainstalovat spuštěním příkazu `apt-get install maven`.
* [Git](https://www.git-scm.com/downloads)
    * Na Ubuntu můžete Git nainstalovat spuštěním příkazu `sudo apt-get install git`.

## <a name="create-an-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs

Obor názvů služby Event Hubs je potřeba odeslat nebo přijmout z jakékoli služby Event Hubs. Zobrazit [vytvořit Kafka povolena Služba Event Hubs](event-hubs-create-kafka-enabled.md) informace o tom, jak koncový bod Event Hubs Kafka. Ujistěte se, že zkopírujte připojovací řetězec služby Event Hubs pro pozdější použití.

## <a name="clone-the-example-project"></a>Naklonování ukázkového projektu

Teď, když máte připojovací řetězec systému Kafka s podporou služby Event Hubs, Azure Event Hubs, Kafka úložiště klonování a přejděte do `flink` podsložky:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/flink
```

## <a name="run-flink-producer"></a>Spustit Flink výrobce

Pomocí zadaného příklad producenta Flink, odesílání zpráv do služby Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Zadejte koncový bod Event Hubs Kafka

#### <a name="producerconfig"></a>Producer.config

Aktualizace `bootstrap.servers` a `sasl.jaas.config` hodnoty v `producer/src/main/resources/producer.config` ke směrování producent ke koncovému bodu Event Hubs Kafka s správné ověření.

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

Producent spustit z příkazového řádku, vygenerujte soubor JAR a potom spustit z Mavenu (nebo generovat soubor JAR pomocí nástroje Maven, spusťte v jazyce Java tak, že přidáte nezbytné JAR(s) Kafka do cesty pro třídy):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

Nyní zahájí producent odesílání událostí do Kafka povoleno tématu Event Hub `test` a tisk události do stdout.

## <a name="run-flink-consumer"></a>Spusťte Flink příjemce

Použijeme příklad zadaná příjemce přijímat zprávy z Kafka povolena Služba Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Zadejte koncový bod Event Hubs Kafka

#### <a name="consumerconfig"></a>Consumer.config

Aktualizace `bootstrap.servers` a `sasl.jaas.config` hodnoty v `consumer/src/main/resources/consumer.config` ke směrování uživatelů do koncového bodu Event Hubs Kafka s správné ověření.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-consumer-from-the-command-line"></a>Spuštěním příjemce z příkazového řádku

Příjemce spustit z příkazového řádku, vygenerujte soubor JAR a potom spustit z Mavenu (nebo generovat soubor JAR pomocí nástroje Maven, spusťte v jazyce Java tak, že přidáte nezbytné JAR(s) Kafka do cesty pro třídy):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Má-li povolena Kafka eventhub událostí (například, pokud vaše producent běží), potom příjemce nyní začne přijímat události z tématu `test`.

Podívejte se na [Průvodce pro konektor pro Flink Kafka](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html) podrobné informace o připojení Flink Kafka.

## <a name="next-steps"></a>Další postup
V tomto kurzu se vaše zkušenosti jak připojit Apache Flink Kafka povolené event hubs, aniž by změna vašim klientům protokolu nebo spuštění vlastní clustery. Jako součást v tomto kurzu jste provedli následující kroky: 

> [!div class="checklist"]
> * Vytvoření oboru názvů služby Event Hubs
> * Naklonování ukázkového projektu
> * Spustit Flink výrobce 
> * Spusťte Flink příjemce

Další informace o službě Event Hubs a Event Hubs pro ekosystém Kafka najdete v následujícím tématu:  

- [Informace o službě Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs pro Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Vytvoření služby Event Hubs s podporou Kafka](event-hubs-create-kafka-enabled.md)
- [Streamování do služby Event Hubs z aplikací Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Zrcadlení zprostředkovatele Kafka v centru událostí s podporou Kafka](event-hubs-kafka-mirror-maker-tutorial.md)
- [Připojení Apache Sparku k centru událostí s podporou Kafka](event-hubs-kafka-spark-tutorial.md)
- [Integrace připojení Kafka s centrem událostí s podporou Kafka](event-hubs-kafka-connect-tutorial.md)
- [Připojení Akka Streams k centru událostí s podporou Kafka](event-hubs-kafka-akka-streams-tutorial.md)
- [Prozkoumejte ukázky na našem GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka)
