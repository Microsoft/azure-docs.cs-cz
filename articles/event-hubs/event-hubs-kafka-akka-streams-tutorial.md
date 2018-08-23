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
ms.openlocfilehash: 674c3b5cbd3b4253833a785b32e2bc4b0f1928da
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "42057207"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>Datové proudy Akka pomocí služby Event Hubs pro Apache Kafka
V tomto kurzu se dozvíte, jak připojit Akka datové proudy Kafka povolené event hubs, aniž by změna vašim klientům protokolu nebo spuštění vlastní clustery. Podporuje Azure Event Hubs pro Kafka [Apache Kafka verze 1.0.](https://kafka.apache.org/10/documentation.html)

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření oboru názvů služby Event Hubs
> * Klonování projektu z příkladu
> * Spustit Flink výrobce 
> * Spusťte Flink příjemce

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu, nezapomeňte, že jsou splněné následující požadavky:

* Přečtěte si [Event Hubs pro Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) článku. 
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

## <a name="run-akka-streams-producer"></a>Spustit Akka datové proudy

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

## <a name="run-akka-streams-consumer"></a>Spusťte datové proudy Akka příjemce

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
V tomto kurzu jste zjistili, jak připojit Akka datové proudy Kafka povolené event hubs, aniž by změna vašim klientům protokolu nebo spuštění vlastní clustery. Azure Event Hubs pro Kafka podporuje [Apache Kafka verze 1.0.](https://kafka.apache.org/10/documentation.html). Jako součást v tomto kurzu jste provedli následující akce: 

> [!div class="checklist"]
> * Vytvoření oboru názvů služby Event Hubs
> * Klonování projektu z příkladu
> * Spustit Flink výrobce 
> * Spusťte Flink příjemce

Další informace o Event Hubs a centra událostí pro systém Kafka, naleznete v následujícím tématu:  

* [Informace o službě Event Hubs](event-hubs-what-is-event-hubs.md)
* [Další informace o službě Event Hubs pro systém Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* Použití nástroje [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) ke [streamování událostí z místního systému Kafka do služby Event Hubs s podporou Kafka v cloudu.](event-hubs-kafka-mirror-maker-tutorial.md)
* Naučíte se Streamovat do Kafka povolené služby Event Hubs pomocí [nativních aplikací Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md) nebo [Apache Flink](event-hubs-kafka-flink-tutorial.md)
