---
title: Integrace s Apache Kafka připojení Azure Event Hubs | Dokumentace Microsoftu
description: Tento článek obsahuje informace o tom, jak používat Apache Spark pro Azure Event Hubs pro systém Kafka.
services: event-hubs
documentationcenter: .net
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: tutorial
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: bahariri
ms.openlocfilehash: f405fe1c93ad17476ac47bc23951c65a5ea5db73
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53091359"
---
# <a name="integrate-apache-kafka-connect-support-on-azure-event-hubs-preview"></a>Podpora integrace připojení Apache Kafka ve službě Azure Event Hubs (Preview)
Se zvyšujícími se obchodními požadavky na příjem dat se zvyšuje i potřeba příjmu dat z nejrůznějších externích zdrojů a jímek. [Připojení Apache Kafka](https://kafka.apache.org/documentation/#connect) poskytuje takovou architekturu pro připojení a import nebo export dat do nebo z jakéhokoli externího systému, jako je MySQL, HDFS a systém souborů, prostřednictvím clusteru Kafka. Tento kurz vás provede používáním architektury připojení Kafka se službou Event Hubs s podporou Kafka.

Tento kurz vás provede integrací připojení Kafka s centrem událostí Azure s podporou Kafka a nasazením základních konektorů FileStreamSource a FileStreamSink. Tato funkce je aktuálně ve verzi Preview. Tyto konektory nejsou určené pro produkční použití. Předvádějí však kompletní scénář připojení Kafka, kde služba Azure Event Hubs funguje jako zprostředkovatel Kafka.

> [!NOTE]
Tato ukázka je k dispozici na [GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect).

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Vytvoření oboru názvů služby Event Hubs
> * Naklonování ukázkového projektu
> * Konfigurace připojení Kafka pro službu Event Hubs
> * Spuštění připojení Kafka
> * Vytvoření konektorů

## <a name="prerequisites"></a>Požadavky
Abyste mohli dokončit tento návod, ujistěte se, že máte následující:

- Předplatné Azure. Pokud žádné nemáte, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/).
- [Git](https://www.git-scm.com/downloads)
- Linux/MacOS
- Vydaná verze Kafka (verze 1.1.1, Scala verze 2.11), která je k dispozici na webu [kafka.apache.org](https://kafka.apache.org/downloads#1.1.1)
- Přečtěte si úvodní článek [Event Hubs pro Apache Kafka](https://docs.microsoft.com/azure/event-hubs/event-hubs-for-kafka-ecosystem-overview).

## <a name="create-an-event-hubs-namespace"></a>Vytvoření oboru názvů služby Event Hubs
K odesílání do jakékoli služby Event Hubs a příjmu z ní se vyžaduje obor názvů služby Event Hubs. Pokyny k získání koncového bodu Event Hubs Kafka najdete v tématu [Vytvoření centra událostí s podporou Kafka](event-hubs-create.md). Získejte plně kvalifikovaný název domény a připojovací řetězec služby Event Hubs pro pozdější použití. Pokyny najdete v tématu [Získání připojovacího řetězce služby Event Hubs](event-hubs-get-connection-string.md). 

## <a name="clone-the-example-project"></a>Naklonování ukázkového projektu
Naklonujte úložiště Azure Event Hubs a přejděte do podsložky tutorials/connect: 

```
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/connect
```

## <a name="configure-kafka-connect-for-event-hubs"></a>Konfigurace připojení Kafka pro službu Event Hubs
Přesměrování propustnosti připojení Kafka z Kafka do služby Event Hubs vyžaduje minimální konfiguraci.  Následující ukázka souboru `connect-distributed.properties` znázorňuje, jak pro připojení nakonfigurovat ověřování a komunikaci s koncovým bodem Kafka ve službě Event Hubs:

```properties
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093 # e.g. namespace.servicebus.windows.net:9093
group.id=connect-cluster-group

# connect internal topic names, auto-created if not exists
config.storage.topic=connect-cluster-configs
offset.storage.topic=connect-cluster-offsets
status.storage.topic=connect-cluster-status

# internal topic replication factors - auto 3x replication in Azure Storage
config.storage.replication.factor=1
offset.storage.replication.factor=1
status.storage.replication.factor=1

rest.advertised.host.name=connect
offset.flush.interval.ms=10000

key.converter=org.apache.kafka.connect.json.JsonConverter
value.converter=org.apache.kafka.connect.json.JsonConverter
internal.key.converter=org.apache.kafka.connect.json.JsonConverter
internal.value.converter=org.apache.kafka.connect.json.JsonConverter

internal.key.converter.schemas.enable=false
internal.value.converter.schemas.enable=false

# required EH Kafka security settings
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

producer.security.protocol=SASL_SSL
producer.sasl.mechanism=PLAIN
producer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

consumer.security.protocol=SASL_SSL
consumer.sasl.mechanism=PLAIN
consumer.sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";

plugin.path={KAFKA.DIRECTORY}/libs # path to the libs directory within the Kafka release
```

## <a name="run-kafka-connect"></a>Spuštění připojení Kafka

V tomto kroku se místně spustí pracovní proces připojení Kafka v distribuovaném režimu a k zachování stavu clusteru s použije služba Event Hubs.

1. Místně uložte výše uvedený soubor `connect-distributed.properties`.  Nezapomeňte nahradit všechny hodnoty v závorkách.
2. Na svém počítači přejděte do umístění verze Kafka.
4. Spusťte `./bin/connect-distributed.sh /PATH/TO/connect-distributed.properties`.  Jakmile se zobrazí `'INFO Finished starting connectors and tasks'`, rozhraní REST API pracovního procesu připojení je připravené k interakci. 

> [!NOTE]
> Event Hubs podporuje automatické vytváření témat klienty Kafka. Rychlou kontrolou oboru názvů na webu Azure Portal zjistíte, že se interní témata pracovního procesu připojení vytvořila automaticky.

### <a name="create-connectors"></a>Vytvoření konektorů
Tato část vás provede aktivací konektorů FileStreamSource a FileStreamSink. 

1. Vytvořte adresář pro vstupní a výstupní datové soubory.
    ```bash
    mkdir ~/connect-quickstart
    ```

2. Vytvořte dva soubory: jeden soubor s počátečními hodnotami, ze kterého bude číst konektor FileStreamSource, a druhý soubor, do kterého bude zapisovat konektor FileStreamSink.
    ```bash
    seq 1000 > ~/connect-quickstart/input.txt
    touch ~/connect-quickstart/output.txt
    ```

3. Vytvořte konektor FileStreamSource.  Nezapomeňte nahradit složené závorky cestou k vašemu domovskému adresáři.
    ```bash
    curl -s -X POST -H "Content-Type: application/json" --data '{"name": "file-source","config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSourceConnector","tasks.max":"1","topic":"connect-quickstart","file": "{YOUR/HOME/PATH}/connect-quickstart/input.txt"}}' http://localhost:8083/connectors
    ```
    Po spuštění výše uvedeného příkazu by se ve vaší instanci služby Event Hubs mělo zobrazit centrum událostí `connect-quickstart`.
4. Zkontrolujte stav konektoru zdroje.
    ```bash
    curl -s http://localhost:8083/connectors/file-source/status
    ```
    Volitelně můžete pomocí [Service Bus Exploreru](https://github.com/paolosalvatori/ServiceBusExplorer/releases) ověřit, že události dorazily do tématu `connect-quickstart`.

5. Vytvořte konektor FileStreamSink.  Nezapomeňte opět nahradit složené závorky cestou k vašemu domovskému adresáři.
    ```bash
    curl -X POST -H "Content-Type: application/json" --data '{"name": "file-sink", "config": {"connector.class":"org.apache.kafka.connect.file.FileStreamSinkConnector", "tasks.max":"1", "topics":"connect-quickstart", "file": "{YOUR/HOME/PATH}/connect-quickstart/output.txt"}}' http://localhost:8083/connectors
    ```
 
6. Zkontrolujte stav konektoru jímky.
    ```bash
    curl -s http://localhost:8083/connectors/file-sink/status
    ```

7. Ověřte, že se data replikovala mezi soubory a že jsou v obou souborech identická.
    ```bash
    # read the file
    cat ~/connect-quickstart/output.txt
    # diff the input and output files
    diff ~/connect-quickstart/input.txt ~/connect-quickstart/output.txt
    ```

### <a name="cleanup"></a>Vyčištění
Připojení Kafka vytváří témata centra událostí, do kterých se ukládají konfigurace, posuny a stav a která se zachovají i po zrušení clusteru připojení. Pokud toto zachování nevyžadujete, doporučujeme tato témata odstranit. Možná budete chtít odstranit také centrum událostí `connect-quickstart` vytvořené v rámci tohoto návodu.

## <a name="next-steps"></a>Další postup

Další informace o službě Event Hubs a Event Hubs pro ekosystém Kafka najdete v následujícím tématu:  

- [Informace o službě Event Hubs](event-hubs-what-is-event-hubs.md)
- [Event Hubs pro Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)
- [Vytvoření služby Event Hubs s podporou Kafka](event-hubs-create-kafka-enabled.md)
- [Streamování do služby Event Hubs z aplikací Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Zrcadlení zprostředkovatele Kafka v centru událostí s podporou Kafka](event-hubs-kafka-mirror-maker-tutorial.md)
- [Připojení Apache Sparku k centru událostí s podporou Kafka](event-hubs-kafka-spark-tutorial.md)
- [Připojení Apache Flinku k centru událostí s podporou Kafka](event-hubs-kafka-flink-tutorial.md)
- [Připojení Akka Streams k centru událostí s podporou Kafka](event-hubs-kafka-akka-streams-tutorial.md)
- [Prozkoumejte ukázky na našem GitHubu](https://github.com/Azure/azure-event-hubs-for-kafka)