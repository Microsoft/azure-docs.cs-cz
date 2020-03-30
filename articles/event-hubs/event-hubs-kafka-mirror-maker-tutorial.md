---
title: Použití Apache Kafka MirrorMaker – Azure Event Hubs | Dokumenty společnosti Microsoft
description: Tento článek obsahuje informace o tom, jak pomocí Kafka MirrorMaker zrcadlit clusterU Kafka v AzureEvent Hubs.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: conceptual
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 6dc902b6a26c175713381b4fce88934dca3f409e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283578"
---
# <a name="use-kafka-mirrormaker-with-event-hubs-for-apache-kafka"></a>Použití Kafka MirrorMaker s Event Hubs pro Apache Kafka

Tento kurz ukazuje, jak zrcadlit zprostředkovatele Kafka v centru událostí s povolenou Kafka pomocí Kafka MirrorMaker.

   ![Kafka MirrorMaker s rozbočovači událostí](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> Tato ukázka je k dispozici na [GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)


V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření oboru názvů služby Event Hubs
> * Naklonování ukázkového projektu
> * Nastavení clusteru Kafka
> * Konfigurace aplikace Kafka MirrorMaker
> * Spustit Kafka MirrorMaker

## <a name="introduction"></a>Úvod
Jedním z hlavních aspektů moderních cloudových aplikací je možnost aktualizovat, vylepšovat a měnit infrastrukturu bez přerušení služby. Tento kurz ukazuje, jak centrum událostí a Kafka MirrorMaker můžete integrovat existující kanál Kafka do Azure "zrcadlení" vstupní datový proud Kafka ve službě Event Hubs. 

Koncový bod Kafka centra událostí Azure umožňuje připojení k Azure Event Hubs pomocí protokolu Kafka (to znamená klientů Kafka). Provedením minimálních změn v aplikaci Kafka se můžete připojit k Azure Event Hubs a využívat výhod ekosystému Azure. Centra událostí s povolenou kafkou v současné době podporuje kafka verze 1.0 a novější.

## <a name="prerequisites"></a>Požadavky

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

Teď, když máte připojovací řetězec Event Hubs s povolenou Kafka, naklonujte `mirror-maker` centra událostí Azure pro úložiště Kafka a přejděte do podsložky:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Nastavení clusteru Kafka

Pomocí [úvodního průvodce Kafka](https://kafka.apache.org/quickstart) nastavte cluster s požadovaným nastavením (nebo použijte existující cluster Kafka).

## <a name="configure-kafka-mirrormaker"></a>Konfigurace aplikace Kafka MirrorMaker

Kafka MirrorMaker umožňuje "zrcadlení" datového proudu. Vzhledem k tomu, zdroj ové a cílové clustery Kafka, MirrorMaker zajišťuje všechny zprávy odeslané do zdrojového clusteru jsou přijímány zdrojové i cílové clustery. Tento příklad ukazuje, jak zrcadlit zdrojový cluster Kafka s centrem událostí určení. Tento scénář lze použít k odeslání dat z existujícího kanálu Kafka do centra událostí bez přerušení toku dat. 

Podrobnější informace o Kafka MirrorMaker, naleznete [v Kafka Mirroring /MirrorMaker průvodce](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

Chcete-li nakonfigurovat Kafka MirrorMaker, dejte mu cluster Kafka jako jeho příjemce/zdroj a centrum událostí jako jeho výrobce/cíl.

#### <a name="consumer-configuration"></a>Konfigurace spotřebitele

Aktualizujte konfigurační soubor `source-kafka.config`příjemce , který sdělí mirrormakeru vlastnosti zdrojového clusteru Kafka.

##### <a name="source-kafkaconfig"></a>zdroj-kafka.config

```
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Konfigurace výrobce

Nyní aktualizujte konfigurační soubor `mirror-eventhub.config`výrobce , který říká MirrorMaker ukládat duplicitní (nebo "zrcadlené") data do služby Event Hubs. Konkrétně změnit `bootstrap.servers` `sasl.jaas.config` a přejděte na koncový bod Event Hubs Kafka. Služba Event Hubs vyžaduje zabezpečenou komunikaci (SASL), které je dosaženo nastavením posledních tří vlastností v následující konfiguraci: 

##### <a name="mirror-eventhubconfig"></a>mirror-eventhub.config

```
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

## <a name="run-kafka-mirrormaker"></a>Spustit Kafka MirrorMaker

Spusťte skript Kafka MirrorMaker z kořenového adresáře Kafka pomocí nově aktualizovaných konfiguračních souborů. Nezapomeňte zkopírovat konfigurační soubory do kořenového adresáře Kafka nebo aktualizovat jejich cesty v následujícím příkazu.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Pokud chcete ověřit, že se události dostanou do centra událostí, přečtěte si statistiku příchozího přenosu dat na [webu Azure Portal](https://azure.microsoft.com/features/azure-portal/)nebo spusťte příjemce proti centru událostí.

Při spuštění mirrormakeru jsou všechny události odeslané do zdrojového clusteru Kafka přijímány clusterem Kafka i zrcadlenou službou event hub uzly kafka. Pomocí MirrorMaker a event hubs Kafka koncový bod, můžete migrovat existující kanál Kafka do spravované služby Azure Event Hubs bez změny existujícího clusteru nebo přerušení probíhající tok dat.

## <a name="samples"></a>ukázky
Podívejte se na následující ukázky na GitHubu:

- [Ukázkový kód pro tento kurz na GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker)
- [Azure Event Hubs Kafka MirrorMaker spuštěný na instanci kontejneru Azure](https://github.com/djrosanova/EventHubsMirrorMaker)

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření oboru názvů služby Event Hubs
> * Naklonování ukázkového projektu
> * Nastavení clusteru Kafka
> * Konfigurace aplikace Kafka MirrorMaker
> * Spustit Kafka MirrorMaker

Další informace o službě Event Hubs a Event Hubs pro ekosystém Kafka najdete v následujícím tématu:  

- [Informace o službě Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs pro Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Jak vytvořit centrum událostí](event-hubs-create.md)
- [Streamování do služby Event Hubs z aplikací Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Připojení Apache Sparku k centru událostí](event-hubs-kafka-spark-tutorial.md)
- [Připojení Apache Flinku k centru událostí](event-hubs-kafka-flink-tutorial.md)
- [Integrace aplikace Kafka Connect s centrem událostí](event-hubs-kafka-connect-tutorial.md)
- [Připojení Streamů Akka k centru událostí](event-hubs-kafka-akka-streams-tutorial.md)
- [Prozkoumejte ukázky na našem GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka)
