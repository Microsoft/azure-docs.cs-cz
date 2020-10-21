---
title: Použití datových proudů Akka pro Apache Kafka – Azure Event Hubs | Microsoft Docs
description: Tento článek poskytuje informace o tom, jak připojit Akka datové proudy k centru událostí Azure.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 43f0c359af6f699ce3e7c19948d706c09adeba70
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92316324"
---
# <a name="using-akka-streams-with-event-hubs-for-apache-kafka"></a>Použití Akka Streams se službou Event Hubs pro Apache Kafka

V tomto kurzu se dozvíte, jak propojit Akka streamy prostřednictvím podpory Event Hubs Apache Kafka bez změny klientů protokolu nebo spuštění vlastních clusterů. 

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření oboru názvů služby Event Hubs
> * Naklonování ukázkového projektu
> * Spustit producenta streamování Akka 
> * Spustit příjemce streamování Akka

> [!NOTE]
> Tato ukázka je k dispozici na [GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java) .

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu se ujistěte, že máte následující požadavky:

* Přečtěte si článek [Event Hubs pro Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Java Development Kit (JDK) 1.8+](/azure/developer/java/fundamentals/java-jdk-long-term-support)
    * Na Ubuntu nainstalujte sadu JDK spuštěním příkazu `apt-get install default-jdk`.
    * Nezapomeňte nastavit proměnnou prostředí JAVA_HOME tak, aby odkazovala na složku, ve které je sada JDK nainstalovaná.
* [Stažení](https://maven.apache.org/download.cgi) a [instalace](https://maven.apache.org/install.html) binárního archivu Maven
    * Na Ubuntu můžete Maven nainstalovat spuštěním příkazu `apt-get install maven`.
* [Git](https://www.git-scm.com/downloads)
    * Na Ubuntu můžete Git nainstalovat spuštěním příkazu `sudo apt-get install git`.

## <a name="create-an-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs

K odeslání nebo přijetí z jakékoli služby Event Hubs se vyžaduje Event Hubs obor názvů. Podrobné informace najdete v tématu [vytvoření centra událostí](event-hubs-create.md) . Nezapomeňte zkopírovat připojovací řetězec Event Hubs pro pozdější použití.

## <a name="clone-the-example-project"></a>Naklonování ukázkového projektu

Teď, když máte Event Hubs připojovací řetězec, naklonujte Azure Event Hubs pro úložiště Kafka a přejděte do `akka` podsložky:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/akka/java
```

## <a name="run-akka-streams-producer"></a>Spustit producenta streamování Akka

Pomocí poskytnutého příkladu producenta Akka streamy odešlete zprávy službě Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Zadejte Event Hubs koncový bod Kafka.

#### <a name="producer-applicationconf"></a>Aplikace producenta. conf

Aktualizujte `bootstrap.servers` hodnoty a v nástroji a `sasl.jaas.config` `producer/src/main/resources/application.conf` nasměrujte producent na Event Hubs koncový bod Kafka se správným ověřením.

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

Producent zahájí odesílání událostí do centra událostí v tématu `test` a vytiskne události do STDOUT.

## <a name="run-akka-streams-consumer"></a>Spustit příjemce streamování Akka

Pomocí poskytnutého příkladu příjemce přijímají zprávy z centra událostí.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Zadejte Event Hubs koncový bod Kafka.

#### <a name="consumer-applicationconf"></a>Aplikace příjemce. conf

Aktualizujte `bootstrap.servers` hodnoty a v nástroji a `sasl.jaas.config` `consumer/src/main/resources/application.conf` nasměrujte uživatele na Event Hubs koncový bod Kafka se správným ověřením.

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

Pokud má centrum událostí události (například pokud je váš výrobce spuštěný), pak příjemce začne přijímat události z tématu `test` . 

Podrobnější informace o datových proudech Akka najdete v [příručce Akka Streams Kafka](https://doc.akka.io/docs/akka-stream-kafka/current/home.html) .

## <a name="next-steps"></a>Další kroky
Další informace o Event Hubs pro Kafka najdete v následujících článcích:  

- [Zrcadlení zprostředkovatele Kafka v centru událostí](event-hubs-kafka-mirror-maker-tutorial.md)
- [Připojení Apache Sparku k centru událostí](event-hubs-kafka-spark-tutorial.md)
- [Připojení Apache Flinku k centru událostí](event-hubs-kafka-flink-tutorial.md)
- [Integrace Kafka Connect do centra událostí](event-hubs-kafka-connect-tutorial.md)
- [Prozkoumejte ukázky na našem GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Apache Kafka příručka pro vývojáře pro Azure Event Hubs](apache-kafka-developer-guide.md)