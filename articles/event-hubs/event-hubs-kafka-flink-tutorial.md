---
title: Použití Apache Flink pro Apache Kafka – Azure Event Hubs | Microsoft Docs
description: Tento článek poskytuje informace o tom, jak připojit Apache Flink k centru událostí Azure.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 1d382270248e95b1b973f57f67ebf81160f03a16
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92369501"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Použití Apache Flink se službou Azure Event Hubs pro Apache Kafka
V tomto kurzu se dozvíte, jak připojit Apache Flink k centru událostí beze změny klientů protokolu nebo spuštění vlastních clusterů. Další informace o podpoře Event Hubs "pro Apache Kafka příjemce protokolu najdete v tématu [Event Hubs pro Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md).


V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření oboru názvů služby Event Hubs
> * Naklonování ukázkového projektu
> * Spustit Flink výrobce 
> * Spustit Flink příjemce

> [!NOTE]
> Tato ukázka je k dispozici na [GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink) .

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu se ujistěte, že máte následující požadavky:

* Přečtěte si článek [Event Hubs pro Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Java Development Kit (JDK) 1.7 +](/azure/developer/java/fundamentals/java-jdk-long-term-support)
    * Na Ubuntu nainstalujte sadu JDK spuštěním příkazu `apt-get install default-jdk`.
    * Nezapomeňte nastavit proměnnou prostředí JAVA_HOME tak, aby odkazovala na složku, ve které je sada JDK nainstalovaná.
* [Stažení](https://maven.apache.org/download.cgi) a [instalace](https://maven.apache.org/install.html) binárního archivu Maven
    * Na Ubuntu můžete Maven nainstalovat spuštěním příkazu `apt-get install maven`.
* [Git](https://www.git-scm.com/downloads)
    * Na Ubuntu můžete Git nainstalovat spuštěním příkazu `sudo apt-get install git`.

## <a name="create-an-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs

K odeslání nebo přijetí z jakékoli služby Event Hubs se vyžaduje Event Hubs obor názvů. Pokyny k vytvoření oboru názvů a centra událostí najdete v tématu [vytvoření centra událostí](event-hubs-create.md) . Nezapomeňte zkopírovat připojovací řetězec Event Hubs pro pozdější použití.

## <a name="clone-the-example-project"></a>Naklonování ukázkového projektu

Teď, když máte Event Hubs připojovací řetězec, naklonujte úložiště Azure Event Hubs for Kafka a přejděte do `flink` podsložky:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/flink
```

## <a name="run-flink-producer"></a>Spustit Flink výrobce

Pomocí poskytnutého příkladu Flink producenta odešle zprávy službě Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Zadejte Event Hubs koncový bod Kafka.

#### <a name="producerconfig"></a>producer.config

Aktualizujte `bootstrap.servers` hodnoty a v nástroji a `sasl.jaas.config` `producer/src/main/resources/producer.config` nasměrujte producent na Event Hubs koncový bod Kafka se správným ověřením.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=FlinkExampleProducer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> Nahraďte `{YOUR.EVENTHUBS.CONNECTION.STRING}` připojovacím řetězcem pro váš Event Hubs obor názvů. Pokyny k získání připojovacího řetězce najdete v tématu [získání připojovacího řetězce Event Hubs](event-hubs-get-connection-string.md). Tady je příklad konfigurace: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`

### <a name="run-producer-from-the-command-line"></a>Spustit producenta z příkazového řádku

Pokud chcete spustit producenta z příkazového řádku, vygenerujte JAR a pak ho spusťte z Maven (nebo vygenerujte JAR pomocí Maven a pak spusťte v jazyce Java přidáním nezbytných Kafka JAR do cesty k cestě):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

Producent nyní začne odesílat události do centra událostí v tématu `test` a tisknout události do STDOUT.

## <a name="run-flink-consumer"></a>Spustit Flink příjemce

Pomocí poskytnutého příkladu příjemce přijímají zprávy z centra událostí. 

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Zadejte Event Hubs koncový bod Kafka.

#### <a name="consumerconfig"></a>consumer.config

Aktualizujte `bootstrap.servers` hodnoty a v nástroji a `sasl.jaas.config` `consumer/src/main/resources/consumer.config` nasměrujte uživatele na Event Hubs koncový bod Kafka se správným ověřením.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> Nahraďte `{YOUR.EVENTHUBS.CONNECTION.STRING}` připojovacím řetězcem pro váš Event Hubs obor názvů. Pokyny k získání připojovacího řetězce najdete v tématu [získání připojovacího řetězce Event Hubs](event-hubs-get-connection-string.md). Tady je příklad konfigurace: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`


### <a name="run-consumer-from-the-command-line"></a>Spustit příjemce z příkazového řádku

Pokud chcete spustit příjemce z příkazového řádku, vygenerujte JAR a pak spusťte z Maven (nebo vygenerujte JAR pomocí Maven a pak spusťte v jazyce Java přidáním potřebných Kafka JAR do cesty pro cestu):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Pokud má centrum událostí události (například pokud je váš výrobce také spuštěný), pak uživatel začne přijímat události z tématu `test` .

Podrobnější informace o připojení Flink k Kafka najdete v [příručce k Flink konektoru Kafka](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html) .

## <a name="next-steps"></a>Další kroky
Další informace o Event Hubs pro Kafka najdete v následujících článcích:  

- [Zrcadlení zprostředkovatele Kafka v centru událostí](event-hubs-kafka-mirror-maker-tutorial.md)
- [Připojení Apache Sparku k centru událostí](event-hubs-kafka-spark-tutorial.md)
- [Integrace Kafka Connect do centra událostí](event-hubs-kafka-connect-tutorial.md)
- [Prozkoumejte ukázky na našem GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Připojení Akka Streams k centru událostí](event-hubs-kafka-akka-streams-tutorial.md)
- [Apache Kafka příručka pro vývojáře pro Azure Event Hubs](apache-kafka-developer-guide.md)