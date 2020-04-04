---
title: Použití Apache Flink pro Apache Kafka – Azure Event Hubs | Dokumenty společnosti Microsoft
description: Tento článek obsahuje informace o tom, jak připojit Apache Flink k centru událostí Azure
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: how-to
ms.date: 04/02/2020
ms.author: shvija
ms.openlocfilehash: 2e5a2924cdc00c1cc057d71c40645085df4bae6a
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632811"
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

Obor názvů Event Hubs je vyžadován k odesílání nebo přijímání z libovolné služby Event Hubs. Pokyny k vytvoření oboru názvů a centra událostí najdete v [tématu Vytvoření centra událostí.](event-hubs-create.md) Nezapomeňte zkopírovat připojovací řetězec Event Hubs pro pozdější použití.

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

Producent nyní začne odesílat události do `test` centra událostí v tématu a tisk událostí na stdout.

## <a name="run-flink-consumer"></a>Spustit příjemce Flink

Pomocí uvedeného spotřebitele příklad přijímat zprávy z centra událostí. 

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
Další informace o centru událostí pro Kafku najdete v následujících článcích:  

- [Zrcadlení zprostředkovatele Kafka v centru událostí](event-hubs-kafka-mirror-maker-tutorial.md)
- [Připojení Apache Sparku k centru událostí](event-hubs-kafka-spark-tutorial.md)
- [Integrace aplikace Kafka Connect s centrem událostí](event-hubs-kafka-connect-tutorial.md)
- [Prozkoumejte ukázky na našem GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Připojení Streamů Akka k centru událostí](event-hubs-kafka-akka-streams-tutorial.md)
- [Průvodce vývojáři Apache Kafka pro Azure Event Hubs](apache-kafka-developer-guide.md)