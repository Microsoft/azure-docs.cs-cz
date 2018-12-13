---
title: Pomocí nástroje MirrorMaker Apache Kafka – Azure Event Hubs | Dokumentace Microsoftu
description: Tento článek obsahuje informace o tom, jak pomocí nástroje MirrorMaker Kafka pro cluster Kafka ve službě AzureEvent Hubs zrcadlení.
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: mirror-maker
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: 83f51d2980e5cb5bc2e2a415bb3ee96778667ed7
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53095031"
---
# <a name="use-kafka-mirrormaker-with-event-hubs-for-apache-kafka"></a>Pomocí nástroje MirrorMaker Kafka s Event Hubs pro Apache Kafka

Tento kurz ukazuje postupy při zrcadlení zprostředkovatele Kafka v rozbočovači povolená událost Kafka pomocí nástroje MirrorMaker Kafka.

   ![Nástroje MirrorMaker Kafka s Event Hubs](./media/event-hubs-kafka-mirror-maker-tutorial/evnent-hubs-mirror-maker1.png)

> [!NOTE]
> Tato ukázka je k dispozici na [GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker).


V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření oboru názvů služby Event Hubs
> * Naklonování ukázkového projektu
> * Nastavení clusteru Kafka
> * Konfigurace nástroje MirrorMaker Kafka
> * Spuštění nástroje MirrorMaker Kafka

## <a name="introduction"></a>Úvod
Jedním z hlavních faktorů pro moderní cloudové aplikace se možnost aktualizace, vylepšení a změna infrastruktury bez přerušení služby. Tento kurz ukazuje, jak povolené Kafka eventhub a nástroje MirrorMaker Kafka můžete integrovat existující kanál Kafka do Azure pomocí "zrcadlení" vstupního datového proudu Kafka ve službě Event Hubs. 

Koncový bod Azure Event Hubs Kafka umožňuje připojení k Azure Event Hubs pomocí protokolu Kafka (to znamená, že klienti Kafka). Tím, že minimální změny do aplikace Kafka, můžete připojit k Azure Event Hubs a mohli využívat výhody ekosystému Azure. Kafka povolena Služba Event Hubs aktuálně podporuje Kafka verze 1.0 nebo novější.

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento kurz, ujistěte se, že máte následující:

* Přečtěte si článek [Event Hubs pro Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Java Development Kit (JDK) 1.7+](https://aka.ms/azure-jdks)
    * Na Ubuntu nainstalujte sadu JDK spuštěním příkazu `apt-get install default-jdk`.
    * Nezapomeňte nastavit proměnnou prostředí JAVA_HOME tak, aby odkazovala na složku, ve které je sada JDK nainstalovaná.
* [Stáhněte si](http://maven.apache.org/download.cgi) a [nainstalovat](http://maven.apache.org/install.html) binární archiv Maven
    * Na Ubuntu můžete Maven nainstalovat spuštěním příkazu `apt-get install maven`.
* [Git](https://www.git-scm.com/downloads)
    * Na Ubuntu můžete Git nainstalovat spuštěním příkazu `sudo apt-get install git`.

## <a name="create-an-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs

K odesílání do jakékoli služby Event Hubs a příjmu z ní se vyžaduje obor názvů služby Event Hubs. Pokyny k získání koncového bodu Event Hubs Kafka najdete v tématu [Vytvoření centra událostí s podporou Kafka](event-hubs-create.md). Ujistěte se, že zkopírujte připojovací řetězec služby Event Hubs pro pozdější použití.

## <a name="clone-the-example-project"></a>Naklonování ukázkového projektu

Teď, když máte Kafka povolené připojovací řetězec služby Event Hubs, Azure Event Hubs, Kafka úložiště klonování a přejděte do `mirror-maker` podsložky:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/mirror-maker
```

## <a name="set-up-a-kafka-cluster"></a>Nastavení clusteru Kafka

Použít [příručky rychlý start Kafka](https://kafka.apache.org/quickstart) nastavení clusteru s požadovaným nastavením (nebo použijte existujícího clusteru Kafka).

## <a name="configure-kafka-mirrormaker"></a>Konfigurace nástroje MirrorMaker Kafka

Nástroje MirrorMaker Kafka umožňuje "zrcadlení" datového proudu. Zadaný zdrojový a cílový clustery Kafka, nástroje MirrorMaker zajišťuje všechny zprávy odeslané na zdrojovém clusteru jsou přijímány zdrojový i cílový clustery. Tento příklad ukazuje, jak pro zdrojový cluster Kafka s centrem událostí podporující Kafka cíl zrcadlení. Tento scénář lze použít k odesílání dat do služby Event Hubs bez přerušení tok dat z existujícího kanálu Kafka. 

Podrobnější informace o Kafka nástroje MirrorMaker najdete v článku [Kafka zrcadlení nebo nástroje MirrorMaker průvodce](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330).

Ke konfiguraci nástroje MirrorMaker Kafka, pojmenujte ji clusteru Kafka jako příjemce/zdroj a Centrum povolené Kafka událostí jako svůj producenta/cíl.

#### <a name="consumer-configuration"></a>Konfigurace uživatelů

Aktualizovat konfigurační soubor příjemce `source-kafka.config`, který dává pokyn nástroje MirrorMaker vlastnosti zdroje clusteru Kafka.

##### <a name="source-kafkaconfig"></a>Zdroj kafka.config

```xml
bootstrap.servers={SOURCE.KAFKA.IP.ADDRESS1}:{SOURCE.KAFKA.PORT1},{SOURCE.KAFKA.IP.ADDRESS2}:{SOURCE.KAFKA.PORT2},etc
group.id=example-mirrormaker-group
exclude.internal.topics=true
client.id=mirror_maker_consumer
```

#### <a name="producer-configuration"></a>Konfigurace výrobce

Nyní aktualizovat konfigurační soubor producent `mirror-eventhub.config`, který dává pokyn nástroje MirrorMaker k odesílání dat duplicitní (nebo "zrcadlených") do služby Event Hubs. Konkrétně změnit `bootstrap.servers` a `sasl.jaas.config` tak, aby odkazoval na koncový bod služby Event Hubs Kafka. Služba Event Hubs vyžaduje zabezpečené komunikace (SASL), který je dosaženo pomocí nastavení poslední tři vlastnosti v následující konfiguraci: 

##### <a name="mirror-eventhubconfig"></a>eventhub.config zrcadlový svazek

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=mirror_maker_producer

#Required for Event Hubs
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

## <a name="run-kafka-mirrormaker"></a>Spuštění nástroje MirrorMaker Kafka

Spusťte skript nástroje MirrorMaker Kafka z kořenového adresáře Kafka pomocí nově aktualizovaná konfigurační soubory. Ujistěte se, že buď zkopírujte konfigurační soubory do kořenového adresáře Kafka ani aktualizovat svoje cesty v následujícím příkazu.

```shell
bin/kafka-mirror-maker.sh --consumer.config source-kafka.config --num.streams 1 --producer.config mirror-eventhub.config --whitelist=".*"
```

Pokud chcete ověřit, že události jsou tam dostupné pro Centrum událostí Kafka s podporou, zobrazit statistiku příchozího přenosu dat v [webu Azure portal](https://azure.microsoft.com/features/azure-portal/), nebo spusťte konzumenta proti centra událostí.

Při spuštění nástroje MirrorMaker žádné události odeslané do clusteru Kafka zdroje jsou přijímány clusteru Kafka a zrcadlených Kafka povolena služby Azure event hub. Pomocí nástroje MirrorMaker a koncový bod Event Hubs Kafka můžete migrovat existující kanál Kafka do spravované služby Azure Event Hubs bez změny stávajícího clusteru nebo by bylo třeba přerušit všechny probíhající datový tok.

## <a name="next-steps"></a>Další postup

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření oboru názvů služby Event Hubs
> * Naklonování ukázkového projektu
> * Nastavení clusteru Kafka
> * Konfigurace nástroje MirrorMaker Kafka
> * Spuštění nástroje MirrorMaker Kafka

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
