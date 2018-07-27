---
title: Datové proudy Akka pomocí služby Azure Event Hubs pro Apache Kafka | Dokumentace Microsoftu
description: Připojení Akka datové proudy Kafka povolené centra událostí
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: mvc
ms.date: 06/06/2018
ms.author: bahariri
ms.openlocfilehash: f6671e05c53b8be2e06eb969adc8a4d5736aff37
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284434"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>Datové proudy Akka pomocí služby Event Hubs pro Apache Kafka

Jednou z klíčových výhod používání Apache Kafka je v ekosystému rozhraní se může připojit k. Kafka s podporou služby Event Hubs kombinuje flexibilitu Kafka s škálovatelnosti, konzistenci a podporu ekosystému Azure.

V tomto kurzu se dozvíte, jak připojit Akka datové proudy Kafka povolené event hubs, aniž by změna vašim klientům protokolu nebo spuštění vlastní clustery. Podporuje Azure Event Hubs pro Kafka [Apache Kafka verze 1.0.](https://kafka.apache.org/10/documentation.html)

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu, nezapomeňte, že jsou splněné následující požadavky:

* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Java Development Kit (JDK) 1.8 +](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
    * Na Ubuntu nainstalujte sadu JDK spuštěním příkazu `apt-get install default-jdk`.
    * Nezapomeňte nastavit proměnnou prostředí JAVA_HOME tak, aby odkazovala na složku, ve které je sada JDK nainstalovaná.
* [Stáhněte si](http://maven.apache.org/download.cgi) a [nainstalovat](http://maven.apache.org/install.html) binární archiv Maven
    * Na Ubuntu můžete Maven nainstalovat spuštěním příkazu `apt-get install maven`.
* [Git](https://www.git-scm.com/downloads)
    * Na Ubuntu můžete Git nainstalovat spuštěním příkazu `sudo apt-get install git`.

## <a name="create-an-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs

Obor názvů služby Event Hubs je potřeba odeslat nebo přijmout z jakékoli služby Event Hubs. Zobrazit [vytvořit Kafka povolena Služba Event Hubs](event-hubs-create-kafka-enabled.md) informace o tom, jak koncový bod Event Hubs Kafka. Ujistěte se, že zkopírujte připojovací řetězec služby Event Hubs pro pozdější použití.

## <a name="clone-the-example-project"></a>Klonování projektu z příkladu

Teď, když máte připojovací řetězec systému Kafka s podporou služby Event Hubs, naklonujte si úložiště služby Azure Event Hubs a přejděte `akka` podsložky:

```shell
git clone https://github.com/Azure/azure-event-hubs.git
cd azure-event-hubs/samples/kafka/akka
```

## <a name="akka-streams-producer"></a>Výrobce Akka datové proudy

Pomocí zadaného příklad producenta Akka datové proudy, odesílání zpráv do služby Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Zadejte koncový bod Event Hubs Kafka

#### <a name="producer-applicationconf"></a>Výrobce application.conf

Aktualizace `bootstrap.servers` a `sasl.jaas.config` hodnoty v `producer/src/main/resources/application.conf` ke směrování producent ke koncovému bodu Event Hubs Kafka s správné ověření.

```xml
akka.kafka.producer {
    #Akka Kafka producer properties can be defined here


    # Properties defined by org.apache.kafka.clients.producer.ProducerConfig
    # can be defined in this configuration section.
    kafka-clients {
        bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
        sasl.mechanism=PLAIN
        security.protocol=SASL_SSL
        sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-producer-from-the-command-line"></a>Spustit z příkazového řádku

Producent spustit z příkazového řádku, vygenerujte soubor JAR a potom spustit z Mavenu (nebo generovat soubor JAR pomocí nástroje Maven, spusťte v jazyce Java tak, že přidáte nezbytné JAR(s) Kafka do cesty pro třídy):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

Producent začne odesílání událostí do centra událostí povolené Kafka v tématu `test`a vytiskne události do stdout.

## <a name="akka-streams-consumer"></a>Příjemce Akka datové proudy

Použijeme příklad zadaná příjemce, přijímá zprávy z Kafka povolené event hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Zadejte koncový bod Event Hubs Kafka

#### <a name="consumer-applicationconf"></a>Application.conf příjemce

Aktualizace `bootstrap.servers` a `sasl.jaas.config` hodnoty v `consumer/src/main/resources/application.conf` ke směrování uživatelů do koncového bodu Event Hubs Kafka s správné ověření.

```xml
akka.kafka.consumer {
    #Akka Kafka consumer properties defined here
    wakeup-timeout=60s

    # Properties defined by org.apache.kafka.clients.consumer.ConsumerConfig
    # defined in this configuration section.
    kafka-clients {
       request.timeout.ms=60000
       group.id=akka-example-consumer

       bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
       sasl.mechanism=PLAIN
       security.protocol=SASL_SSL
       sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-consumer-from-the-command-line"></a>Spuštěním příjemce z příkazového řádku

Příjemce spustit z příkazového řádku, vygenerujte soubor JAR a potom spustit z Mavenu (nebo generovat soubor JAR pomocí nástroje Maven, spusťte v jazyce Java tak, že přidáte nezbytné JAR(s) Kafka do cesty pro třídy):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Pokud povolená Kafka eventhub událostí (například pokud vaše producent také běží), pak uživatel začne přijímat události z tématu `test`. 

Podívejte se [Akka datové proudy Kafka průvodce](https://doc.akka.io/docs/akka-stream-kafka/current/home.html) podrobné informace o datových proudů Akka.

## <a name="next-steps"></a>Další postup

* [Informace o službě Event Hubs](event-hubs-what-is-event-hubs.md)
* [Další informace o službě Event Hubs pro systém Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* Použití nástroje [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) ke [streamování událostí z místního systému Kafka do služby Event Hubs s podporou Kafka v cloudu.](event-hubs-kafka-mirror-maker-tutorial.md)
* Naučíte se Streamovat do Kafka povolené služby Event Hubs pomocí [nativních aplikací Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md) nebo [Apache Flink](event-hubs-kafka-flink-tutorial.md)
