---
title: Použití Apache Kafka nástroje MirrorMaker – Azure Event Hubs | Microsoft Docs
description: Tento článek poskytuje informace o tom, jak pomocí Kafka nástroje MirrorMaker zrcadlit cluster Kafka v rozbočovačích AzureEvent.
ms.topic: how-to
ms.date: 01/04/2021
ms.openlocfilehash: 654e9e19dfde0d0c58d00e41cf8ab0ba8e1484d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97861004"
---
# <a name="use-apache-kafka-mirrormaker-with-event-hubs"></a>Použití Apache Kafka nástroje MirrorMaker s Event Hubs

V tomto kurzu se dozvíte, jak můžete Kafka zprostředkovatele zrcadlit do centra událostí Azure pomocí Kafka nástroje MirrorMaker. Pokud hostuje Apache Kafka v Kubernetes pomocí operátoru Strimzi CNCF, můžete si přečíst kurz v [tomto blogovém příspěvku](https://strimzi.io/blog/2020/06/09/mirror-maker-2-eventhub/) , kde se dozvíte, jak nastavit Kafka s Strimzi a nástrojem zrcadlení Maker 2. 

   ![Kafka nástroje MirrorMaker s Event Hubs](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> Tato ukázka je k dispozici na [GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker) .

> [!NOTE]
> Tento článek obsahuje odkazy na seznam *povolených* termínů, který už Microsoft nepoužívá. Po odebrání termínu ze softwaru ho odebereme z tohoto článku.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření oboru názvů služby Event Hubs
> * Naklonování ukázkového projektu
> * Nastavení clusteru Kafka
> * Konfigurace Kafka nástroje MirrorMaker
> * Spustit Kafka nástroje MirrorMaker

## <a name="introduction"></a>Úvod
V tomto kurzu se dozvíte, jak může centrum událostí a Kafka nástroje MirrorMaker integrovat stávající kanál Kafka do Azure tím, že "zrcadlí" vstupní datový proud Kafka ve službě Event Hubs, který umožňuje integraci Apache Kafka datových proudů pomocí několika [vzorů federace](event-hubs-federation-overview.md). 

Koncový bod služby Azure Event Hubs Kafka vám umožňuje připojit se k Azure Event Hubs pomocí protokolu Kafka (tj. klienti Kafka). Díky minimálním změnám aplikace v Kafka se můžete připojit k Azure Event Hubs a využívat výhody ekosystému Azure. Event Hubs aktuálně podporuje protokol Apache Kafka verze 1,0 a novější.

Apache Kafka 1 můžete v Apache Kafka na Event Hubs použít jednosměrně. Nástroje MirrorMaker 2 lze použít v obou směrech, ale konfigurace [ `MirrorCheckpointConnector` a `MirrorHeartbeatConnector` , které jsou v nástroje MirrorMaker 2](https://cwiki.apache.org/confluence/display/KAFKA/KIP-382%3A+MirrorMaker+2.0) , musí být nakonfigurovány tak, aby odkazovaly na zprostředkovatele Apache Kafka a nikoli na Event Hubs. V tomto kurzu se dozvíte, jak nakonfigurovat nástroje MirrorMaker 1.

## <a name="prerequisites"></a>Předpoklady

Abyste mohli absolvovat tento kurz, ujistěte se, že máte následující:

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

K odesílání do jakékoli služby Event Hubs a příjmu z ní se vyžaduje obor názvů služby Event Hubs. Pokyny k vytvoření oboru názvů a centra událostí najdete v tématu [vytvoření centra událostí](event-hubs-create.md) . Nezapomeňte zkopírovat připojovací řetězec Event Hubs pro pozdější použití.

## <a name="clone-the-example-project"></a>Naklonování ukázkového projektu

Teď, když máte Event Hubs připojovací řetězec, naklonujte Azure Event Hubs pro úložiště Kafka a přejděte do `mirror-maker` podsložky:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Nastavení clusteru Kafka

Pomocí [Průvodce rychlým startem pro Kafka](https://kafka.apache.org/quickstart) nastavte cluster s požadovaným nastavením (nebo použijte existující cluster Kafka).

## <a name="configure-kafka-mirrormaker"></a>Konfigurace Kafka nástroje MirrorMaker

Kafka nástroje MirrorMaker povoluje "zrcadlení" datového proudu. V případě zdrojových a cílových Kafka clusterů zajišťuje nástroje MirrorMaker všechny zprávy odesílané do zdrojového clusteru ve zdrojovém i cílovém clusteru. Tento příklad ukazuje, jak zrcadlit zdrojový cluster Kafka pomocí cílového centra událostí. Tento scénář lze použít k odeslání dat z existujícího kanálu Kafka do Event Hubs, aniž by došlo k přerušení toku dat. 

Podrobnější informace o Kafka nástroje MirrorMaker najdete v tématu [Průvodce zrcadlením Kafka/nástroje MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

Pokud chcete nakonfigurovat Kafka nástroje MirrorMaker, přidělte mu jako svůj výrobce nebo zdroj událostí cluster Kafka jako jeho příjemce a jako cíl.

#### <a name="consumer-configuration"></a>Konfigurace příjemce

Aktualizujte konfigurační soubor příjemce `source-kafka.config` , který oznamuje nástroje MirrorMaker vlastností zdrojového clusteru Kafka.

##### <a name="source-kafkaconfig"></a>source-kafka.config

```
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Konfigurace výrobce

Teď aktualizujte konfigurační soubor producenta `mirror-eventhub.config` , který dává službě Event Hubs pokyn, aby odesílala duplicitní data (neboli zrcadlená). Konkrétně změňte `bootstrap.servers` a `sasl.jaas.config` přejděte na Event Hubs koncový bod Kafka. Služba Event Hubs vyžaduje zabezpečenou komunikaci (SASL), která se dosahuje nastavením poslední tři vlastnosti v následující konfiguraci: 

##### <a name="mirror-eventhubconfig"></a>mirror-eventhub.config

```
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> Nahraďte `{YOUR.EVENTHUBS.CONNECTION.STRING}` připojovacím řetězcem pro váš Event Hubs obor názvů. Pokyny k získání připojovacího řetězce najdete v tématu [získání připojovacího řetězce Event Hubs](event-hubs-get-connection-string.md). Tady je příklad konfigurace: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`

## <a name="run-kafka-mirrormaker"></a>Spustit Kafka nástroje MirrorMaker

Spusťte skript Kafka nástroje MirrorMaker z kořenového adresáře Kafka pomocí nově aktualizovaných konfiguračních souborů. Nezapomeňte buď zkopírovat konfigurační soubory do kořenového adresáře Kafka, nebo aktualizovat jejich cesty v následujícím příkazu.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Pokud chcete ověřit, jestli se události blíží centra událostí, Projděte si statistiku příchozího přenosu dat ve [Azure Portal](https://azure.microsoft.com/features/azure-portal/)nebo spusťte příjemce v centru událostí.

Při spuštění nástroje MirrorMaker jsou všechny události odeslané do zdrojového clusteru Kafka přijímány clusterem Kafka i zrcadlením centra událostí. Pomocí nástroje MirrorMaker a koncového bodu služby Event Hubs Kafka můžete migrovat existující kanál Kafka do spravované služby Azure Event Hubs, aniž byste museli měnit stávající cluster nebo přerušovat průběžný tok dat.

## <a name="samples"></a>ukázky
Podívejte se na následující ukázky na GitHubu:

- [Ukázkový kód pro tento kurz na GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)
- [Azure Event Hubs Kafka nástroje MirrorMaker běžící na instanci kontejneru Azure](https://github.com/djrosanova/EventHubsMirrorMaker)

## <a name="next-steps"></a>Další kroky
Další informace o Event Hubs pro Kafka najdete v následujících článcích:  

- [Připojení Apache Sparku k centru událostí](event-hubs-kafka-spark-tutorial.md)
- [Připojení Apache Flinku k centru událostí](event-hubs-kafka-flink-tutorial.md)
- [Integrace Kafka Connect do centra událostí](event-hubs-kafka-connect-tutorial.md)
- [Prozkoumejte ukázky na našem GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Připojení Akka Streams k centru událostí](event-hubs-kafka-akka-streams-tutorial.md)
- [Apache Kafka příručka pro vývojáře pro Azure Event Hubs](apache-kafka-developer-guide.md)