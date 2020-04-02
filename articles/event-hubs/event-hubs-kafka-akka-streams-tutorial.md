---
title: Použití Akka Streams pro Apache Kafka - Azure Event Hubs| Dokumenty společnosti Microsoft
description: Tento článek obsahuje informace o tom, jak připojit Akka Streams do centra událostí Azure.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: c53fcc7b4b99e77237ba7ea9219aef4182f2af61
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521821"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>Použití Akka Streams se službou Event Hubs pro Apache Kafka
Tento kurz ukazuje, jak připojit Akka Streams k centru událostí bez změny klientů protokolu nebo spuštění vlastních clusterů. Azure Event Hubs pro Kafka podporuje [Apache Kafka verze 1.0.](https://kafka.apache.org/10/documentation.html)

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření oboru názvů služby Event Hubs
> * Naklonování ukázkového projektu
> * Spustit Akka Streams výrobce 
> * Spustit akka streamy spotřebitele

> [!NOTE]
> Tato ukázka je k dispozici na [GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java)

## <a name="prerequisites"></a>Požadavky

Chcete-li dokončit tento kurz, ujistěte se, že máte následující předpoklady:

* Přečtěte si článek [Event Hubs pro Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Java Development Kit (JDK) 1.8+](https://aka.ms/azure-jdks)
    * Na Ubuntu nainstalujte sadu JDK spuštěním příkazu `apt-get install default-jdk`.
    * Nezapomeňte nastavit proměnnou prostředí JAVA_HOME tak, aby odkazovala na složku, ve které je sada JDK nainstalovaná.
* [Stažení](https://maven.apache.org/download.cgi) a [instalace](https://maven.apache.org/install.html) binárního archivu Maven
    * Na Ubuntu můžete Maven nainstalovat spuštěním příkazu `apt-get install maven`.
* [Git](https://www.git-scm.com/downloads)
    * Na Ubuntu můžete Git nainstalovat spuštěním příkazu `sudo apt-get install git`.

## <a name="create-an-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs

Obor názvů Event Hubs je vyžadován k odesílání nebo přijímání z libovolné služby Event Hubs. Podrobné informace najdete [v tématu Vytvoření centra událostí.](event-hubs-create.md) Nezapomeňte zkopírovat připojovací řetězec Event Hubs pro pozdější použití.

## <a name="clone-the-example-project"></a>Naklonování ukázkového projektu

Teď, když máte připojovací řetězec Event Hubs, klonujte Azure Event Hubs `akka` pro úložiště Kafka a přejděte do podsložky:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/akka/java
```

## <a name="run-akka-streams-producer"></a>Spustit Akka Streams výrobce

Pomocí uvedeného příkladu výrobce Akka Streams odesílejte zprávy službě Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Poskytnutí koncového bodu Kafka centra událostí

#### <a name="producer-applicationconf"></a>Výrobce application.conf

Aktualizujte `bootstrap.servers` `sasl.jaas.config` hodnoty `producer/src/main/resources/application.conf` a v aplikaci a nasměrujte producenta na koncový bod Event Hubs Kafka se správným ověřováním.

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

### <a name="run-producer-from-the-command-line"></a>Spustit výrobce z příkazového řádku

Chcete-li spustit výrobce z příkazového řádku, vygenerujte JAR a pak spustit z evnitř Maven (nebo generovat JAR pomocí Maven, pak spustit v Jazyce Java přidáním potřebné Kafka JAR (y) do třídy):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

Producent začne odesílat události do centra `test`událostí v tématu a vytiskne události stdout.

## <a name="run-akka-streams-consumer"></a>Spustit akka streamy spotřebitele

Pomocí uvedeného spotřebitele příklad přijímat zprávy z centra událostí.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Poskytnutí koncového bodu Kafka centra událostí

#### <a name="consumer-applicationconf"></a>Spotřebitelská aplikace.conf

Aktualizujte `bootstrap.servers` `sasl.jaas.config` hodnoty `consumer/src/main/resources/application.conf` a v aplikaci a nasměrujte spotřebitele na koncový bod Event Hubs Kafka se správným ověřováním.

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

### <a name="run-consumer-from-the-command-line"></a>Spuštění příjemce z příkazového řádku

Chcete-li spustit příjemce z příkazového řádku, vygenerujte JAR a pak spustit z evnitř Maven (nebo generovat JAR pomocí Maven, pak spustit v jazyce Java přidáním potřebné Kafka JAR (y) do třídy):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Pokud centrum událostí obsahuje události (například pokud je spuštěn také váš výrobce), `test`pak spotřebitel začne přijímat události z tématu . 

Podrobnější informace o Akka Streams vám najdete v [průvodci Akka Streams Kafka.](https://doc.akka.io/docs/akka-stream-kafka/current/home.html)

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili, jak připojit Akka Streams k centru událostí bez změny klientů protokolu nebo spuštění vlastních clusterů. Azure Event Hubs pro Kafka podporuje [Apache Kafka verze 1.0.](https://kafka.apache.org/10/documentation.html). V rámci tohoto kurzu jste provedli následující akce: 

> [!div class="checklist"]
> * Vytvoření oboru názvů služby Event Hubs
> * Naklonování ukázkového projektu
> * Spustit Akka Streams výrobce 
> * Spustit akka streamy spotřebitele

Další informace o službě Event Hubs a Event Hubs pro ekosystém Kafka najdete v následujícím tématu:  

- [Informace o službě Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs pro Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Jak vytvořit centrum událostí](event-hubs-create.md)
- [Streamování do služby Event Hubs z aplikací Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Zrcadlení zprostředkovatele Kafka v centru událostí](event-hubs-kafka-mirror-maker-tutorial.md)
- [Připojení Apache Sparku k centru událostí](event-hubs-kafka-spark-tutorial.md)
- [Připojení Apache Flinku k centru událostí](event-hubs-kafka-flink-tutorial.md)
- [Integrace aplikace Kafka Connect s centrem událostí](event-hubs-kafka-connect-tutorial.md)
- [Prozkoumejte ukázky na našem GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka)
