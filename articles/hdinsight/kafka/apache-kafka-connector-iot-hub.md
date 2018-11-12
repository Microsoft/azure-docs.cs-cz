---
title: Použití Apache Kafka v HDInsight pomocí Azure IoT Hub
description: Další informace o použití Apache Kafka v HDInsight pomocí Azure IoT Hub. Projekt Kafka připojení Azure IoT Hub poskytuje konektor k zdroje a jímky Kafka. Konektor zdroje může číst data ze služby IoT Hub a konektoru jímky zapisuje do služby IoT Hub.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 8941a7332c19b1a9d5c04abb0e4b03ae83e98016
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51260478"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>Použití Apache Kafka v HDInsight pomocí Azure IoT Hub

Další informace o použití [Kafka připojení Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) konektor pro přesun dat mezi Apache Kafka v HDInsight a Azure IoT Hub. V tomto dokumentu se dozvíte, jak pro spuštění konektoru služby IoT Hub z hraniční uzel v clusteru.

Rozhraní API systému Kafka připojení umožňuje implementovat konektory, které průběžně načítat data do Kafka nebo zápis dat z Kafka do jiného systému. [Kafka připojení Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) je konektor, který získává data ze služby Azure IoT Hub do Kafka. To může také nabízet data z Kafka do služby IoT Hub. 

Při přijetí ze služby IoT Hub, můžete použít __zdroj__ konektoru. Při nabízení do služby IoT Hub, můžete použít __jímky__ konektoru. Konektor služby IoT Hub poskytuje zdroj a jímku konektory.

Následující diagram znázorňuje tok dat mezi službou Azure IoT Hub a Kafka na HDInsight při použití konektoru.

![Obrázek znázorňující dat odesílaných ze služby IoT Hub Kafka prostřednictvím konektoru](./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png)

Další informace o rozhraní API pro připojení najdete v tématu [ https://kafka.apache.org/documentation/#connect ](https://kafka.apache.org/documentation/#connect).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

* Kafka v clusteru HDInsight. Další informace najdete v dokumentu [Rychlý start k systému Kafka ve službě HDInsight](apache-kafka-get-started.md).

* Hraniční uzel v clusteru Kafka. Další informace najdete v tématu [použití hraničních uzlů s HDInsight](../hdinsight-apps-use-edge-node.md) dokumentu.

* Azure IoT Hub. Pro účely tohoto kurzu, můžu jenom doporučit [online simulátor Raspberry Pi připojit ke službě Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-web-simulator-get-started) dokumentu.

* Klient SSH. V krocích v tomto dokumentu se pro připojení ke clusteru používá SSH. Další informace najdete v dokumentu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="install-the-connector"></a>Instalace konektoru

1. Stáhněte si nejnovější verzi Kafka připojení pro službu Azure IoT Hub z [ https://github.com/Azure/toketi-kafka-connect-iothub/releases/ ](https://github.com/Azure/toketi-kafka-connect-iothub/releases).

2. Pokud chcete nahrát soubor .jar k hraničnímu uzlu vašeho systému Kafka v clusteru HDInsight, použijte následující příkaz:

    > [!NOTE]
    > Nahraďte `sshuser` pomocí uživatelského účtu SSH pro váš cluster HDInsight. Nahraďte `new-edgenode` s názvem hraniční uzel. Nahraďte `clustername` s názvem clusteru. Další informace na koncovém bodu SSH pro hraniční uzel, najdete v článku [použití hraničních uzlů s HDInsight](../hdinsight-apps-use-edge-node.md#access-an-edge-node) dokumentu.

    ```bash
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.clustername-ssh.azurehdinsight.net:
    ```

3. Po dokončení kopírování souborů, připojte k hraničnímu uzlu pomocí SSH:

    ```bash
    ssh sshuser@new-edgenode.clustername-ssh.azurehdinsight.net
    ```

    Další informace najdete v dokumentu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

4. Instalace konektoru do Kafka `libs` adresáře, použijte následující příkaz:

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

> [!TIP]
> Pokud narazíte na problémy se zbytkem pracovního postupu v tomto dokumentu při použití souboru .jar předem připravené, zkuste sestavit balíček ze zdroje.
>
> Pokud chcete vytvořit konektor, musí mít Scala vývojové prostředí s [nástroj sestavení Scala](http://www.scala-sbt.org/).
>
> 1. Stáhnout zdroj pro konektor z [ https://github.com/Azure/toketi-kafka-connect-iothub/ ](https://github.com/Azure/toketi-kafka-connect-iothub/) do svého vývojového prostředí.
>
> 2. V příkazovém řádku v adresáři projektu použijte následující příkaz Pokud chcete sestavit a zabalit projekt:
>
>    ```bash
>    sbt assembly
>    ```
>
>    Tento příkaz vytvoří soubor s názvem `kafka-connect-iothub-assembly_2.11-0.6.jar` v `target/scala-2.11` adresář pro projekt.

## <a name="configure-kafka"></a>Konfigurace systému Kafka

Z připojení SSH k hraničnímu uzlu použijte následující postup ke konfiguraci Kafka pro spuštění konektoru v samostatném režimu:

1. Uložte název clusteru pro proměnné. Použití proměnné usnadňuje kopírování a vkládání v této části příkazů:

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

2. Nainstalujte `jq` nástroj. Tento nástroj usnadňuje zpracování dokumentů JSON, které jsou vráceny z dotazů Ambari:

    ```bash
    sudo apt -y install jq
    ```

3. Získejte adresu Kafka zprostředkovatelů. Ve vašem clusteru může být mnoho zprostředkovatelů, ale potřebujete odkazovat na jeden nebo dva. Pokud chcete získat adresu dva hostitele zprostředkovatele, použijte následující příkaz:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    Po zobrazení výzvy zadejte heslo pro účet přihlášení clusteru (admin). Vrácená hodnota je obdobná následujícímu textu:

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

4. Získáte adresu uzly Zookeeper. Existuje několik uzly Zookeeper jsou v clusteru, ale potřebujete odkazovat na jeden nebo dva. Pokud chcete získat adresu dva uzly Zookeeper, použijte následující příkaz:

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

5. Při spuštění konektoru v samostatném režimu, `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` soubor se používá ke komunikaci s zprostředkovatelům systému Kafka. Chcete-li upravit `connect-standalone.properties` souboru, použijte následující příkaz:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

    * Ke konfiguraci v samostatné konfiguraci pro hraniční uzel najít zprostředkovatelů Kafka, vyhledejte řádek, který začíná `bootstrap.servers=`. Nahraďte `localhost:9092` hodnotu s hostiteli zprostředkovatele z předchozího kroku.

    * Změnit `key.converter=` a `value.converter=` řádky následující hodnoty:

        ```text
        key.converter=org.apache.kafka.connect.storage.StringConverter
        value.converter=org.apache.kafka.connect.storage.StringConverter
        ```

        > [!IMPORTANT]
        > Tato změna umožňuje testovat pomocí konzoly producent je součástí systému Kafka. Možná bude nutné různých převaděče pro ostatní producenty a spotřebiteli. Informace o použití jiných převaděče hodnot najdete v tématu [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

    * Přidáte řádek na konci souboru, který obsahuje následující text:

        ```text
        consumer.max.poll.records=10
        ```

        > [!TIP]
        > Tato změna je zabránit vypršení časového limitu pro v konektoru jímky omezení na 10 záznamů najednou. Další informace najdete na adrese [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

6. Chcete-li uložit soubor, použijte __Ctrl + X__, __Y__a potom __Enter__.

7. K vytvoření témata používá konektorem, použijte následující příkazy:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    Pro ověření, že `iotin` a `iotout` témata neexistuje, použijte následující příkaz:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    `iotin` Tématu se používá pro příjem zpráv ze služby IoT Hub. `iotout` Tématu se používá k odeslání zprávy do služby IoT Hub.

## <a name="get-iot-hub-connection-information"></a>Získání informací o připojení služby IoT Hub

Pokud chcete načíst informace o IoT hub používá konektorem, použijte následující kroky:

1. Získejte koncový bod kompatibilní s centrem událostí a název koncový bod kompatibilní s centrem událostí služby IoT hub. Pokud chcete získat tyto informace, použijte jednu z následujících metod:

    * __Z [webu Azure portal](https://portal.azure.com/)__, použijte následující postup:

        1. Přejděte do služby IoT Hub a vyberte __koncové body__.
        2. Z __integrovaných koncových bodech__vyberte __události__.
        3. Z __vlastnosti__, zkopírujte hodnotu z těchto polí:

            * __Název kompatibilní s centrem událostí__
            * __Koncový bod kompatibilní s centrem událostí__
            * __Oddíly__

        > [!IMPORTANT]
        > Hodnota koncový bod z portálu může obsahovat další text, který není nutné v tomto příkladu. Extrahovat text, který odpovídá tomuto vzoru `sb://<randomnamespace>.servicebus.windows.net/`.

    * __Z [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__, použijte následující příkaz:

        ```azure-cli
        az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
        ```

        Nahraďte `myhubname` s názvem služby IoT hub. Odpověď se podobá následujícímu textu:

        ```text
        "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
        "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
        "Partitions": 2
        ```

2. Získejte __sdílené zásady přístupu__ a __klíč__. V tomto příkladu použijte __služby__ klíč. Pokud chcete získat tyto informace, použijte jednu z následujících metod:

    * __Z [webu Azure portal](https://portal.azure.com/)__, použijte následující postup:

        1. Vyberte __zásady sdíleného přístupu__a pak vyberte __služby__.
        2. Kopírovat __primární klíč__ hodnotu.
        3. Kopírovat __připojovací řetězec – primární klíč__ hodnotu.

    * __Z [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__, použijte následující příkaz:

        1. Pokud chcete získat hodnotu primárního klíče, použijte následující příkaz:

            ```azure-cli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            Nahraďte `myhubname` s názvem služby IoT hub. Odpověď je primárním klíčem na `service` zásady pro toto centrum.

        2. Chcete-li získat připojovací řetězec pro `service` zásady, použijte následující příkaz:

            ```azure-cli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            Nahraďte `myhubname` s názvem služby IoT hub. Odpověď je připojovací řetězec pro `service` zásad.

## <a name="configure-the-source-connection"></a>Nakonfigurujte připojení ke zdroji

Pokud chcete nakonfigurovat zdroj pro práci s centrem IoT, udělejte toto z připojení SSH k hraničnímu uzlu:

1. Vytvoření kopie `connect-iot-source.properties` soubor `/usr/hdp/current/kafka-broker/config/` adresáře. Stáhněte soubor z projektu toketi-kafka-connect-iothub, použijte následující příkaz:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

2. Chcete-li upravit `connect-iot-source.properties` soubor a přidejte informace o IoT hub, použijte následující příkaz:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    V editoru najít a změnit následující položky:

    * `Kafka.Topic=PLACEHOLDER`: Nahradit `PLACEHOLDER` s `iotin`. V jsou umístěny zprávy přijaté ze služby IoT hub `iotin` tématu.
    * `IotHub.EventHubCompatibleName=PLACEHOLDER`: Nahradit `PLACEHOLDER` s názvem kompatibilní s centrem událostí.
    * `IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`: Nahradit `PLACEHOLDER` koncový bod kompatibilní s centrem událostí.
    * `IotHub.Partitions=PLACEHOLDER`: Nahradit `PLACEHOLDER` s počtem oddílů z předchozího postupu.
    * `IotHub.AccessKeyName=PLACEHOLDER`: Nahradit `PLACEHOLDER` s `service`.
    * `IotHub.AccessKeyValue=PLACEHOLDER`: Nahradit `PLACEHOLDER` s primárním klíčem `service` zásad.
    * `IotHub.StartType=PLACEHOLDER`: Nahradit `PLACEHOLDER` s datum UTC. Toto datum je při spuštění konektoru probíhá kontrola zpráv. Formát data je `yyyy-mm-ddThh:mm:ssZ`.
    * `BatchSize=100`: Nahradit `100` s `5`. Tato změna způsobí, že konektor umožní číst zprávy do Kafka, jakmile existuje pět nových zpráv ve službě IoT hub.

    Příklad konfigurace najdete v tématu [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

3. Chcete-li změny uložit, použijte __Ctrl + X__, __Y__a potom __Enter__.

Další informace o konfiguraci zdrojového konektoru najdete v části [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

## <a name="configure-the-sink-connection"></a>Konfigurace připojení jímky

Konfigurace připojení jímky pro práci s centrem IoT, udělejte toto z připojení SSH k hraničnímu uzlu:

1. Vytvoření kopie `connect-iothub-sink.properties` soubor `/usr/hdp/current/kafka-broker/config/` adresáře. Stáhněte soubor z projektu toketi-kafka-connect-iothub, použijte následující příkaz:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

2. Chcete-li upravit `connect-iothub-sink.properties` soubor a přidejte informace o IoT hub, použijte následující příkaz:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

    V editoru najít a změnit následující položky:

    * `topics=PLACEHOLDER`: Nahradit `PLACEHOLDER` s `iotout`. Zpráv zapsaných do `iotout` téma se předávají do služby IoT hub.
    * `IotHub.ConnectionString=PLACEHOLDER`: Nahradit `PLACEHOLDER` připojovacím řetězcem pro `service` zásad.

    Příklad konfigurace najdete v tématu [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

3. Chcete-li změny uložit, použijte __Ctrl + X__, __Y__a potom __Enter__.

Další informace o konfiguraci konektoru jímky, najdete v části [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

## <a name="start-the-source-connector"></a>Spuštění konektoru zdroje

Spuštění konektoru zdroj, použijte následující příkaz z připojení SSH k hraničnímu uzlu:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
```

Po spuštění konektoru, odesílání zpráv do služby IoT hub z vašich zařízení. Jak konektor přečte zprávy ze služby IoT hub a ukládá je do tématu Kafka, protokoluje informace do konzoly:

```text
[2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.co
nnect.IotHubSourceTask:39)
[2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (
org.apache.kafka.connect.runtime.WorkerSourceTask:356)
```

> [!NOTE]
> Spuštění konektoru, může se zobrazit několik upozornění. Tato upozornění nezpůsobí problémy s příjmem zpráv ze služby IoT hub.

Chcete-li zastavit konektoru, použijte __Ctrl + C__.

## <a name="start-the-sink-connector"></a>Spuštění konektoru jímky

Z připojení SSH k hraničnímu uzlu pomocí následujícího příkazu spusťte konektor jímky v samostatném režimu:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

Při spuštění konektoru, zobrazí se informace podobné následujícímu textu:

```text
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connec
t.runtime.WorkerSinkTask:262)
```

> [!NOTE]
> Můžete si všimnout několika upozornění spuštění konektoru. Ta můžete bezpečně ignorovat.

K odesílání zpráv prostřednictvím konektoru použijte následující kroky:

1. Otevřete další relaci SSH ke clusteru Kafka:

    ```bash
    ssh sshuser@new-edgenode.clustername-ssh.azurehdinsight.net
    ```
2. K odesílání zpráv `iotout` tématu, použijte následující příkaz:

    > [!WARNING]
    > Protože se jedná nové připojení SSH `$KAFKABROKERS` proměnné neobsahuje žádné informace. Ji nastavit, použijte jednu z následujících metod:
    >
    > * Postupujte podle prvních tří kroků v [konfiguraci Kafka](#configure-kafka) oddílu.
    > * Použití `echo $KAFKABROKERS` z předchozí připojení SSH k získání hodnoty a potom nahraďte `$KAFKABROKERS` v následujícím příkazu se skutečnými hodnotami.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    Tento příkaz nevrací můžete na normální příkazový řádek Bash. Místo toho odesílá vstup z klávesnice `iotout` tématu.

3. Odeslat zprávu do vašeho zařízení, vložte do relace SSH pro dokument JSON `kafka-console-producer`.

    > [!IMPORTANT]
    > Musíte nastavit hodnotu `"deviceId"` položku s ID zařízení. V následujícím příkladu je název zařízení `fakepi`:

    ```text
    {"messageId":"msg1","message":"Turn On","deviceId":"fakepi"}
    ```

    Schéma pro tento dokument JSON je popsána podrobněji na [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

    Pokud používáte simulované zařízení Raspberry Pi a běží, je v zařízení zaznamenána následující zpráva:

    ```text
    Receive message: Turn On
    ```

    Znovu poslat dokument JSON, ale změňte hodnotu `"message"` položka. Novou hodnotu zaprotokoluje zařízení.

Další informace o použití konektoru jímky, najdete v části [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

## <a name="next-steps"></a>Další postup

V tomto dokumentu jste zjistili, jak používat rozhraní API systému Kafka připojení ke spuštění konektoru IoT Kafka v HDInsight. Zjistit další způsoby, jak pracovat s využitím Kafka pomocí následujících odkazů:

* [Použití Apache Sparku se systémem Kafka ve službě HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Použití Apache Stormu se systémem Kafka ve službě HDInsight](../hdinsight-apache-storm-with-kafka.md)
