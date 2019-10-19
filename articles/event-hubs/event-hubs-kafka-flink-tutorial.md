---
title: Použití Apache Flink pro Apache Kafka – Azure Event Hubs | Microsoft Docs
description: Tento článek poskytuje informace o tom, jak připojit Apache Flink k Apache Kafka povolenému centru událostí Azure.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 881546a97b01bef993cc24c6b868ec97ddf5ac36
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555715"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Použití Apache Flink se službou Azure Event Hubs pro Apache Kafka
V tomto kurzu se dozvíte, jak připojit Apache Flink k centrům událostí s povoleným Kafka bez změny klientů protokolu nebo spuštění vlastních clusterů. Azure Event Hubs podporuje [Apache Kafka verze 1,0.](https://kafka.apache.org/10/documentation.html)..

Jednou z klíčových výhod používání Apache Kafka je ekosystém rozhraní, ke kterému se může připojit. Kafka Enabled Event Hubs kombinuje flexibilitu Kafka s škálovatelností, konzistencí a podporou ekosystému Azure.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření oboru názvů služby Event Hubs
> * Naklonování ukázkového projektu
> * Spustit Flink výrobce 
> * Spustit Flink příjemce

> [!NOTE]
> Tato ukázka je k dispozici na [GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink).

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu se ujistěte, že máte následující požadavky:

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

K odeslání nebo přijetí z jakékoli služby Event Hubs se vyžaduje Event Hubs obor názvů. Informace o získání koncového bodu Event Hubs Kafka najdete v tématu věnovaném [Vytvoření Kafka s povoleným Event Hubs](event-hubs-create-kafka-enabled.md) . Nezapomeňte zkopírovat připojovací řetězec Event Hubs pro pozdější použití.

## <a name="clone-the-example-project"></a>Naklonování ukázkového projektu

Teď, když máte Kafka připojovací řetězec Event Hubs, naklonujte Azure Event Hubs pro úložiště Kafka a přejděte do podsložky `flink`:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/flink
```

## <a name="run-flink-producer"></a>Spustit Flink výrobce

Pomocí poskytnutého příkladu Flink producenta odešle zprávy službě Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Zadejte Event Hubs koncový bod Kafka.

#### <a name="producerconfig"></a>soubor. config pro výrobce

Aktualizujte hodnoty `bootstrap.servers` a `sasl.jaas.config` v `producer/src/main/resources/producer.config` a nasměrujte producenta na Event Hubs koncový bod Kafka se správným ověřením.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=FlinkExampleProducer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-producer-from-the-command-line"></a>Spustit producenta z příkazového řádku

Pokud chcete spustit producenta z příkazového řádku, vygenerujte JAR a pak ho spusťte z Maven (nebo vygenerujte JAR pomocí Maven a pak spusťte v jazyce Java přidáním nezbytných Kafka JAR do cesty k cestě):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

Producent nyní začne odesílat události do centra událostí s povoleným Kafka v tématu `test` a tiskne události do STDOUT.

## <a name="run-flink-consumer"></a>Spustit Flink příjemce

Pomocí poskytnutého příkladu příjemce přijímají zprávy z Event Hubs povolené Kafka.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Zadejte Event Hubs koncový bod Kafka.

#### <a name="consumerconfig"></a>příjemce. config

Aktualizujte hodnoty `bootstrap.servers` a `sasl.jaas.config` v `consumer/src/main/resources/consumer.config` a nasměrujte uživatele do Event Hubs koncového bodu Kafka se správným ověřováním.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-consumer-from-the-command-line"></a>Spustit příjemce z příkazového řádku

Pokud chcete spustit příjemce z příkazového řádku, vygenerujte JAR a pak spusťte z Maven (nebo vygenerujte JAR pomocí Maven a pak spusťte v jazyce Java přidáním potřebných Kafka JAR do cesty pro cestu):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Pokud má centrum událostí s povoleným Kafka události (například pokud je váš výrobce také spuštěný), pak uživatel začne přijímat události z tématu `test`.

Podrobnější informace o připojení Flink k Kafka najdete v [příručce k Flink konektoru Kafka](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html) .

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste zjistili, jak připojit Apache Flink k centrům událostí s povoleným Kafka bez změny klientů protokolu nebo spuštění vlastních clusterů. V rámci tohoto kurzu jste provedli následující kroky: 

> [!div class="checklist"]
> * Vytvoření oboru názvů služby Event Hubs
> * Naklonování ukázkového projektu
> * Spustit Flink výrobce 
> * Spustit Flink příjemce

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
