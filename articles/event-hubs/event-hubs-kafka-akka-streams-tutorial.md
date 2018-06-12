---
title: Datové proudy Akka pomocí služby Azure Event Hubs pro ekosystém Kafka | Microsoft Docs
description: Datové proudy Akka propojíte Kafka povoleno centra událostí
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
ms.openlocfilehash: 9db27340a2210ea0be0564b15241952477e592ba
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35303368"
---
# <a name="using-akka-streams-with-event-hubs-for-kafka-ecosystem"></a>Datové proudy Akka pomocí služby Event Hubs pro ekosystém Kafka

Jedním z klíčových výhod používání Apache Kafka je ekosystému rozhraní, které se může připojit k. Povolit Kafka Event Hubs kombinuje flexibilitu Kafka se škálovatelnost, konzistence a podpora ekosystému Azure.

V tomto kurzu se dozvíte, jak připojit Akka datových proudů do centra událostí Kafka povoleno bez změna vaši klienti protokolu nebo vlastní clustery spuštěná. Azure Event Hubs pro Kafka ekosystém podporuje [Apache Kafka verze 1.0.](https://kafka.apache.org/10/documentation.html)

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu, ujistěte se, že máte následující požadavky:

* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Java Development Kit (JDK) 1.8 +](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
    * Na Ubuntu nainstalujte sadu JDK spuštěním příkazu `apt-get install default-jdk`.
    * Nezapomeňte nastavit proměnnou prostředí JAVA_HOME tak, aby odkazovala na složku, ve které je sada JDK nainstalovaná.
* [Stáhněte si](http://maven.apache.org/download.cgi) a [nainstalovat](http://maven.apache.org/install.html) Maven binární archivu
    * Na Ubuntu můžete Maven nainstalovat spuštěním příkazu `apt-get install maven`.
* [Git](https://www.git-scm.com/downloads)
    * Na Ubuntu můžete Git nainstalovat spuštěním příkazu `sudo apt-get install git`.

## <a name="create-an-event-hubs-namespace"></a>Vytvoření oboru názvů Event Hubs

Obor názvů Event Hubs je potřeba odesílat nebo přijímat z jakékoli služby Event Hubs. V tématu [vytvořit Kafka povoleno Event Hubs](event-hubs-create-kafka-enabled.md) informace o získání koncový bod Kafka centra událostí. Ujistěte se, že zkopírujte připojovací řetězec centra událostí pro pozdější použití.

## <a name="clone-the-example-project"></a>Clone – příklad projektu

Teď, když máte povolené Kafka Event Hubs připojovací řetězec, klonovat úložiště v Azure Event Hubs a přejděte do `akka` podsložky:

```shell
git clone https://github.com/Azure/azure-event-hubs.git
cd azure-event-hubs/samples/kafka/akka
```

## <a name="akka-streams-producer"></a>Producent Akka datové proudy

Pomocí zadané příklad producent Akka datové proudy, odesílání zpráv do služby Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Zadejte koncový bod Kafka centra událostí

#### <a name="producer-applicationconf"></a>Producent application.conf

Aktualizace `bootstrap.servers` a `sasl.jaas.config` hodnoty v `producer/src/main/resources/application.conf` směrovat producent ke koncovému bodu Kafka centra událostí s správné ověření.

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

Autor spustit z příkazového řádku, generovat JAR a pak spustit z Maven (nebo generovat JAR pomocí nástroje Maven, spusťte v jazyce Java do cesty pro třídy přidávat i potřeby Kafka JAR(s)):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

Autor začne odesílání událostí do centra událostí Kafka povolené v tématu `test`a zobrazí události, které stdout.

## <a name="akka-streams-consumer"></a>Příjemce Akka datové proudy

Pomocí zadané příjemce příklad, přijímat zprávy z povoleno Kafka event hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Zadejte koncový bod Kafka centra událostí

#### <a name="consumer-applicationconf"></a>Application.conf příjemce

Aktualizace `bootstrap.servers` a `sasl.jaas.config` hodnoty v `consumer/src/main/resources/application.conf` pro přímý příjemce ke koncovému bodu Kafka centra událostí s správné ověření.

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

### <a name="run-consumer-from-the-command-line"></a>Spusťte příjemce z příkazového řádku

K příjemce spustit z příkazového řádku, generovat JAR a pak spustit z Maven (nebo generovat JAR pomocí nástroje Maven, spusťte v jazyce Java do cesty pro třídy přidávat i potřeby Kafka JAR(s)):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Pokud centra událostí povoleno Kafka události (například pokud vaše producent běží), potom příjemce zahájí přijímání událostí z tématu `test`. 

Podívejte se [Akka datové proudy Kafka průvodce](https://doc.akka.io/docs/akka-stream-kafka/current/home.html) podrobné informace o Akka datových proudů.

## <a name="next-steps"></a>Další postup

* [Informace o službě Event Hubs](event-hubs-what-is-event-hubs.md)
* [Informace o službě Event Hubs pro ekosystém Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* Použití [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) k [datového proudu událostí z Kafka místní Kafka povoleno Event Hubs v cloudu.](event-hubs-kafka-mirror-maker-tutorial.md)
* Zjistěte, jak k vysílání datového proudu do Kafka povoleno použití služby Event Hubs [nativních aplikací Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md) nebo [Apache Flink](event-hubs-kafka-flink-tutorial.md)
