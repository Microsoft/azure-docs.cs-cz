---
title: Pomocí služby Azure Event Hubs Kafka MirrorMaker pro ekosystém Kafka | Microsoft Docs
description: Pomocí Kafka MirrorMaker zrcadlení cluster Kafka ve službě Event Hubs.
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: mirror-maker
ms.custom: mvc
ms.date: 05/07/2018
ms.author: bahariri
ms.openlocfilehash: 0693fc2fff5735fb2b3c0a9b8f1d3d256746f40d
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298317"
---
# <a name="using-kafka-mirrormaker-with-event-hubs-for-kafka-ecosystems"></a>Pomocí služby Event Hubs Kafka MirrorMaker pro Kafka ekosystémů

> [!NOTE]
> Tato ukázka je k dispozici na [GitHubu](https://github.com/Azure/azure-event-hubs).

Jeden hlavní úkolem pro moderní cloudové škálování aplikace je možnost aktualizace, vylepšení a změňte infrastruktury bez přerušení služby. Tento kurz ukazuje, jak Centrum událostí podporující Kafka a Kafka MirrorMaker existující kanál Kafka do integrovat Azure "zrcadlení" Kafka vstupního datového proudu ve službě Event Hubs. 

Koncový bod Azure Event Hubs Kafka umožňuje připojení k Azure Event Hubs pomocí protokolu Kafka (tj. Kafka klientů). Tím, že minimální změny k aplikaci Kafka, můžete se připojit k Azure Event Hubs a využívat výhod ekosystému Azure. Kafka povoleno Event Hubs aktuálně podporuje Kafka verze 1.0 nebo novější.

Tento příklad ukazuje, jak pro zrcadlení zprostředkovatele Kafka v rozbočovači Kafka povoleno událostí pomocí Kafka MirrorMaker.

   ![Kafka MirrorMaker službou Event Hubs](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu, zkontrolujte, zda že máte:

* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
    * Na Ubuntu nainstalujte sadu JDK spuštěním příkazu `apt-get install default-jdk`.
    * Nezapomeňte nastavit proměnnou prostředí JAVA_HOME tak, aby odkazovala na složku, ve které je sada JDK nainstalovaná.
* [Stáhněte si](http://maven.apache.org/download.cgi) a [nainstalovat](http://maven.apache.org/install.html) Maven binární archivu
    * Na Ubuntu můžete Maven nainstalovat spuštěním příkazu `apt-get install maven`.
* [Git](https://www.git-scm.com/downloads)
    * Na Ubuntu můžete Git nainstalovat spuštěním příkazu `sudo apt-get install git`.

## <a name="create-an-event-hubs-namespace"></a>Vytvoření oboru názvů Event Hubs

Obor názvů Event Hubs je potřeba odesílat a přijímat z jakékoli služby Event Hubs. V tématu [vytváření Kafka povolit Centru událostí](event-hubs-create.md) pro pokyny pro koncový bod Kafka centra událostí. Ujistěte se, že zkopírujte připojovací řetězec centra událostí pro pozdější použití.

## <a name="clone-the-example-project"></a>Clone – příklad projektu

Teď, když máte Kafka povoleno Event Hubs připojovací řetězec, klonovat úložiště v Azure Event Hubs a přejděte do `mirror-maker` podsložky:

```shell
git clone https://github.com/Azure/azure-event-hubs.git
cd azure-event-hubs/samples/kafka/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Nastavení clusteru Kafka

Použít [Průvodce rychlým zahájením Kafka](https://kafka.apache.org/quickstart) nastavení clusteru s požadovaným nastavením (nebo použijte existující cluster Kafka).

## <a name="kafka-mirrormaker"></a>Kafka MirrorMaker

Kafka MirrorMaker umožňuje "zrcadlení" datového proudu. Zadaný zdrojový a cílový clusterů Kafka, MirrorMaker zajistí, že jsou všechny zprávy odeslané na zdrojovém clusteru přijatých zdrojový i cílový clustery. Tento příklad ukazuje, jak pro zrcadlení zdroj Kafka clusteru s rozbočovačem cílové povoleno Kafka událostí. Tento scénář umožňuje odesílat data do centra událostí bez přerušení toku dat z existující Kafka kanál. 

Podrobné informace o Kafka MirrorMaker, najdete v článku [Kafka zrcadlení nebo MirrorMaker průvodce](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

### <a name="configuration"></a>Konfigurace

Ke konfiguraci Kafka MirrorMaker, pojmenujte ji cluster Kafka jako jeho příjemce zdroj a podporou Kafka událostí centra jako svůj producent cíl.

#### <a name="consumer-configuration"></a>Konfigurace příjemce

Aktualizovat konfigurační soubor příjemce `source-kafka.config`, která sděluje MirrorMaker vlastnosti zdroje Kafka clusteru.

##### <a name="source-kafkaconfig"></a>Zdroj kafka.config

```xml
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Proto, že konfigurace

Nyní aktualizovat konfigurační soubor producent `mirror-eventhub.config`, která sděluje MirrorMaker k odesílání dat duplicitní (nebo "zrcadlené") do služby Event Hubs. Konkrétně změnit `bootstrap.servers` a `sasl.jaas.config` tak, aby odkazovaly na váš koncový bod Kafka centra událostí. Služba Event Hubs vyžaduje zabezpečené komunikace (SASL), které se dosáhne nastavením poslední tři vlastnosti v následující konfiguraci: 

##### <a name="mirror-eventhubconfig"></a>zrcadlení eventhub.config

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-mirrormaker"></a>Spustit MirrorMaker

Spusťte skript Kafka MirrorMaker z kořenového adresáře Kafka pomocí nově aktualizovaných konfigurační soubory. Zajistěte, aby zkopírujte konfigurační soubory do kořenového adresáře Kafka, nebo aktualizovat jejich cesty v následujícím příkazu.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Pokud chcete ověřit, že události jsou dosažení centra událostí Kafka povoleno, najdete v části Statistika příjem příchozích dat v [portál Azure](https://azure.microsoft.com/features/azure-portal/), nebo příjemce spouštění centra událostí.

Při MirrorMaker spuštěna jsou všech událostí odeslaných do zdroji Kafka clusteru přijatých Kafka clusteru a zrcadlené Kafka povolena služba centra událostí. Pomocí MirrorMaker a koncový bod Kafka centra událostí můžete migrovat existující kanál Kafka spravované služby Azure Event Hubs bez Změna existujícího clusteru nebo přerušení všechny probíhající datový tok.

## <a name="next-steps"></a>Další postup

* [Informace o službě Event Hubs](event-hubs-what-is-event-hubs.md)
* [Přečtěte si informace o službě Event Hubs pro ekosystém Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* Další informace o [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) do datového proudu událostí z Kafka místním nasazením a Kafka povolená služba event hubs v cloudu.
* Zjistěte, jak k vysílání datového proudu do Kafka povoleno použití služby Event Hubs [nativních aplikací Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md), [Apache Flink](event-hubs-kafka-flink-tutorial.md), nebo [datové proudy Akka](event-hubs-kafka-akka-streams-tutorial.md).
