---
title: Použití datových proudů Akka pro Apache Kafka – Azure Event Hubs | Microsoft Docs
description: Tento článek poskytuje informace o tom, jak propojit Akka datové proudy s povoleným Apache Kafkam centra událostí Azure.
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
ms.openlocfilehash: ba81ce88bcdf039d020dcd945e45a11cf603c114
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555749"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>Použití datových proudů Akka s Event Hubs pro Apache Kafka
V tomto kurzu se dozvíte, jak připojit Akka datové proudy k centrům událostí s povoleným Kafka bez změny klientů protokolu nebo spustit vlastní clustery. Azure Event Hubs pro Kafka podporuje [Apache Kafka verze 1,0.](https://kafka.apache.org/10/documentation.html)

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření oboru názvů služby Event Hubs
> * Naklonování ukázkového projektu
> * Spustit producenta streamování Akka 
> * Spustit příjemce streamování Akka

> [!NOTE]
> Tato ukázka je k dispozici na [GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java).

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu se ujistěte, že máte následující požadavky:

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

K odeslání nebo přijetí z jakékoli služby Event Hubs se vyžaduje Event Hubs obor názvů. Informace o získání koncového bodu Event Hubs Kafka najdete v tématu věnovaném [Vytvoření Kafka s povoleným Event Hubs](event-hubs-create-kafka-enabled.md) . Nezapomeňte zkopírovat připojovací řetězec Event Hubs pro pozdější použití.

## <a name="clone-the-example-project"></a>Naklonování ukázkového projektu

Teď, když máte Kafka připojovací řetězec Event Hubs, naklonujte Azure Event Hubs pro úložiště Kafka a přejděte do podsložky `akka`:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/akka/java
```

## <a name="run-akka-streams-producer"></a>Spustit producenta streamování Akka

Pomocí poskytnutého příkladu producenta Akka streamy odešlete zprávy službě Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Zadejte Event Hubs koncový bod Kafka.

#### <a name="producer-applicationconf"></a>Aplikace producenta. conf

Aktualizujte hodnoty `bootstrap.servers` a `sasl.jaas.config` v `producer/src/main/resources/application.conf` a nasměrujte producenta na Event Hubs koncový bod Kafka se správným ověřením.

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

### <a name="run-producer-from-the-command-line"></a>Spustit producenta z příkazového řádku

Pokud chcete spustit producenta z příkazového řádku, vygenerujte JAR a pak ho spusťte z Maven (nebo vygenerujte JAR pomocí Maven a pak spusťte v jazyce Java přidáním nezbytných Kafka JAR do cesty k cestě):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

Producent začíná odesílat události do centra událostí s povoleným Kafka v tématu `test` a vytiskne události do STDOUT.

## <a name="run-akka-streams-consumer"></a>Spustit příjemce streamování Akka

Pomocí poskytnutého příkladu příjemce přijímají zprávy z centra událostí s podporou Kafka.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Zadejte Event Hubs koncový bod Kafka.

#### <a name="consumer-applicationconf"></a>Aplikace příjemce. conf

Aktualizujte hodnoty `bootstrap.servers` a `sasl.jaas.config` v `consumer/src/main/resources/application.conf` a nasměrujte uživatele do Event Hubs koncového bodu Kafka se správným ověřováním.

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

### <a name="run-consumer-from-the-command-line"></a>Spustit příjemce z příkazového řádku

Pokud chcete spustit příjemce z příkazového řádku, vygenerujte JAR a pak spusťte z Maven (nebo vygenerujte JAR pomocí Maven a pak spusťte v jazyce Java přidáním potřebných Kafka JAR do cesty pro cestu):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Pokud má centrum událostí s povoleným Kafka události (například pokud je váš výrobce spuštěný), pak příjemce začne přijímat události z tématu `test`. 

Podrobnější informace o datových proudech Akka najdete v [příručce Akka Streams Kafka](https://doc.akka.io/docs/akka-stream-kafka/current/home.html) .

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zjistili, jak propojit proudy Akka s centry událostí s povoleným Kafka bez změny klientů protokolu nebo spustit vlastní clustery. Azure Event Hubs pro Kafka podporuje [Apache Kafka verze 1,0.](https://kafka.apache.org/10/documentation.html).. V rámci tohoto kurzu jste provedli následující akce: 

> [!div class="checklist"]
> * Vytvoření oboru názvů služby Event Hubs
> * Naklonování ukázkového projektu
> * Spustit producenta streamování Akka 
> * Spustit příjemce streamování Akka

Další informace o službě Event Hubs a Event Hubs pro ekosystém Kafka najdete v následujícím tématu:  

- [Informace o službě Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs pro Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Vytvoření služby Event Hubs s podporou Kafka](event-hubs-create-kafka-enabled.md)
- [Streamování do služby Event Hubs z aplikací Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Zrcadlení zprostředkovatele Kafka v centru událostí s podporou Kafka](event-hubs-kafka-mirror-maker-tutorial.md)
- [Připojení Apache Sparku k centru událostí s podporou Kafka](event-hubs-kafka-spark-tutorial.md)
- [Připojení Apache Flinku k centru událostí s podporou Kafka](event-hubs-kafka-flink-tutorial.md)
- [Integrace připojení Kafka s centrem událostí s podporou Kafka](event-hubs-kafka-connect-tutorial.md)
- [Prozkoumejte ukázky na našem GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka)
