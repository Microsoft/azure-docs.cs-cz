---
title: Použití Apache Kafka nástroje MirrorMaker – Azure Event Hubs | Microsoft Docs
description: Tento článek poskytuje informace o tom, jak pomocí Kafka nástroje MirrorMaker zrcadlit cluster Kafka v rozbočovačích AzureEvent.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: conceptual
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 6d1596cf0a50ed5dcb896896282178b6fc12c1a1
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555107"
---
# <a name="use-kafka-mirrormaker-with-event-hubs-for-apache-kafka"></a>Použití Kafka nástroje MirrorMaker s Event Hubs pro Apache Kafka

V tomto kurzu se dozvíte, jak zrcadlit zprostředkovatele Kafka v centru událostí s povoleným Kafka pomocí Kafka nástroje MirrorMaker.

   ![Kafka nástroje MirrorMaker s Event Hubs](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> Tato ukázka je k dispozici na [GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker).


V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření oboru názvů služby Event Hubs
> * Naklonování ukázkového projektu
> * Nastavení clusteru Kafka
> * Konfigurace Kafka nástroje MirrorMaker
> * Spustit Kafka nástroje MirrorMaker

## <a name="introduction"></a>Představení
Důležitým aspektem moderních cloudových aplikací je schopnost aktualizovat, zdokonalovat a měnit infrastrukturu bez přerušení služby. V tomto kurzu se dozvíte, jak Kafka centrum událostí a Kafka nástroje MirrorMaker integruje stávající kanál Kafka do Azure tím, že "zrcadlí" vstupní datový proud Kafka ve službě Event Hubs. 

Koncový bod služby Azure Event Hubs Kafka vám umožňuje připojit se k Azure Event Hubs pomocí protokolu Kafka (tj. klienti Kafka). Díky minimálním změnám aplikace v Kafka se můžete připojit k Azure Event Hubs a využívat výhody ekosystému Azure. Kafka Enabled Event Hubs aktuálně podporuje Kafka verze 1,0 a novější.

## <a name="prerequisites"></a>Předpoklady

Abyste mohli absolvovat tento kurz, ujistěte se, že máte následující:

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

K odesílání do jakékoli služby Event Hubs a příjmu z ní se vyžaduje obor názvů služby Event Hubs. Pokyny k získání koncového bodu Event Hubs Kafka najdete v tématu [Vytvoření centra událostí s podporou Kafka](event-hubs-create.md). Nezapomeňte zkopírovat připojovací řetězec Event Hubs pro pozdější použití.

## <a name="clone-the-example-project"></a>Naklonování ukázkového projektu

Teď, když máte povolený Kafka připojovací řetězec Event Hubs, naklonujte Azure Event Hubs pro úložiště Kafka a přejděte do podsložky `mirror-maker`:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Nastavení clusteru Kafka

Pomocí [Průvodce rychlým startem pro Kafka](https://kafka.apache.org/quickstart) nastavte cluster s požadovaným nastavením (nebo použijte existující cluster Kafka).

## <a name="configure-kafka-mirrormaker"></a>Konfigurace Kafka nástroje MirrorMaker

Kafka nástroje MirrorMaker povoluje "zrcadlení" datového proudu. V případě zdrojových a cílových Kafka clusterů zajišťuje nástroje MirrorMaker všechny zprávy odesílané do zdrojového clusteru ve zdrojovém i cílovém clusteru. Tento příklad ukazuje, jak zrcadlit zdrojový cluster Kafka pomocí cílového centra událostí s povoleným Kafka. Tento scénář lze použít k odeslání dat z existujícího kanálu Kafka do Event Hubs, aniž by došlo k přerušení toku dat. 

Podrobnější informace o Kafka nástroje MirrorMaker najdete v tématu [Průvodce zrcadlením Kafka/nástroje MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

Pokud chcete nakonfigurovat Kafka nástroje MirrorMaker, přidělte mu jako svůj výrobce/zdroj Kafka cluster jako jeho spotřebitel nebo zdroj a centrum událostí s podporou Kafka.

#### <a name="consumer-configuration"></a>Konfigurace příjemce

Aktualizujte konfigurační soubor příjemce `source-kafka.config`, který dává pokyn pro nástroje mirrormakerí vlastností clusteru zdrojového Kafka.

##### <a name="source-kafkaconfig"></a>Source – Kafka. config

```
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Konfigurace výrobce

Teď aktualizujte konfigurační soubor producenta `mirror-eventhub.config`, který dává pokyn, aby službě Event Hubs služba odesílala duplicitní (neboli zrcadlená) data. Konkrétně změňte `bootstrap.servers` a `sasl.jaas.config` tak, aby odkazovaly na Event Hubs koncový bod Kafka. Služba Event Hubs vyžaduje zabezpečenou komunikaci (SASL), která se dosahuje nastavením poslední tři vlastnosti v následující konfiguraci: 

##### <a name="mirror-eventhubconfig"></a>zrcadlení – eventhub. config

```
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

## <a name="run-kafka-mirrormaker"></a>Spustit Kafka nástroje MirrorMaker

Spusťte skript Kafka nástroje MirrorMaker z kořenového adresáře Kafka pomocí nově aktualizovaných konfiguračních souborů. Nezapomeňte buď zkopírovat konfigurační soubory do kořenového adresáře Kafka, nebo aktualizovat jejich cesty v následujícím příkazu.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Pokud chcete ověřit, jestli se události blíží centra událostí s povoleným Kafka, přečtěte si statistiku příchozího přenosu dat ve [Azure Portal](https://azure.microsoft.com/features/azure-portal/)nebo spusťte příjemce v centru událostí.

Při spuštění nástroje MirrorMaker jsou všechny události odeslané do zdrojového clusteru Kafka přijímány clusterem Kafka i zrcadlenou službou centra událostí s povoleným Kafka. Pomocí nástroje MirrorMaker a koncového bodu služby Event Hubs Kafka můžete migrovat existující kanál Kafka do spravované služby Azure Event Hubs, aniž byste museli měnit stávající cluster nebo přerušovat průběžný tok dat.

## <a name="samples"></a>Ukázky
Podívejte se na následující ukázky na GitHubu:

- [Ukázkový kód pro tento kurz na GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)
- [Azure Event Hubs Kafka nástroje MirrorMaker běžící na instanci kontejneru Azure](https://github.com/djrosanova/EventHubsMirrorMaker)

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření oboru názvů služby Event Hubs
> * Naklonování ukázkového projektu
> * Nastavení clusteru Kafka
> * Konfigurace Kafka nástroje MirrorMaker
> * Spustit Kafka nástroje MirrorMaker

Další informace o službě Event Hubs a Event Hubs pro ekosystém Kafka najdete v následujícím tématu:  

- [Informace o službě Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs pro Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Vytvoření služby Event Hubs s podporou Kafka](event-hubs-create-kafka-enabled.md)
- [Streamování do služby Event Hubs z aplikací Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Připojení Apache Sparku k centru událostí s podporou Kafka](event-hubs-kafka-spark-tutorial.md)
- [Připojení Apache Flinku k centru událostí s podporou Kafka](event-hubs-kafka-flink-tutorial.md)
- [Integrace připojení Kafka s centrem událostí s podporou Kafka](event-hubs-kafka-connect-tutorial.md)
- [Připojení Akka Streams k centru událostí s podporou Kafka](event-hubs-kafka-akka-streams-tutorial.md)
- [Prozkoumejte ukázky na našem GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka)
