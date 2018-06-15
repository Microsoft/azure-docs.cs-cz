---
title: Použít Apache Kafka v HDInsight službou Azure IoT Hub | Microsoft Docs
description: Další informace o použití Apache Kafka v HDInsight službou Azure IoT Hub. Projekt Kafka připojení Azure IoT Hub poskytuje zdroj a jímka konektor pro Kafka. Konektor zdroje můžete číst data ze služby IoT Hub a konektor podřízený zapisuje do služby IoT Hub.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/15/2018
ms.author: larryfr
ms.openlocfilehash: 33fdb5b099efc40fec94a860b21cda75ced44fe9
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
ms.locfileid: "34267432"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>Použití Apache Kafka v HDInsight službou Azure IoT Hub

Další informace o použití [Kafka připojení Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) konektor pro přesun dat mezi Apache Kafka v HDInsight a Azure IoT Hub. V tomto dokumentu zjistěte, jak pro spuštění konektoru služby IoT Hub z hraniční uzel v clusteru.

Rozhraní API Kafka připojení umožňuje implementovat konektory, které se nepřetržitě získání dat do Kafka, nebo nabízet data z Kafka do jiného systému. [Kafka připojení Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) je konektor, který získává data ze služby Azure IoT Hub do Kafka. Je také může nabízet data z Kafka do služby IoT Hub. 

Při stahování ze služby IoT Hub, je použít __zdroj__ konektor. Při nabízení do služby IoT Hub, můžete použít __podřízený__ konektor. Konektor služby IoT Hub poskytuje zdroj a jímka konektory.

Následující diagram znázorňuje tok dat mezi Azure IoT Hub a Kafka v HDInsight, když pomocí konektoru.

![Obrázek znázorňující dat odesílaných ze služby IoT Hub Kafka, prostřednictvím konektoru](./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png)

Další informace o rozhraní API připojení najdete v tématu [ https://kafka.apache.org/documentation/#connect ](https://kafka.apache.org/documentation/#connect).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

* Kafka na clusteru HDInsight. Další informace najdete v dokumentu [Rychlý start k systému Kafka ve službě HDInsight](apache-kafka-get-started.md).

* Hraniční uzel v clusteru Kafka. Další informace najdete v tématu [používají uzly okraj v prostředí HDInsight](../hdinsight-apps-use-edge-node.md) dokumentu.

* Azure IoT Hub. V tomto kurzu I doporučujeme [online simulátoru pí malin připojit ke službě Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-web-simulator-get-started) dokumentu.

* Klient SSH. V krocích v tomto dokumentu se pro připojení ke clusteru používá SSH. Další informace najdete v dokumentu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="install-the-connector"></a>Instalace konektoru

1. Stažení nejnovější verze Kafka připojení pro Azure IoT Hub z [ https://github.com/Azure/toketi-kafka-connect-iothub/releases/ ](https://github.com/Azure/toketi-kafka-connect-iothub/releases).

2. Nahrát soubor .jar k uzlu edge vaší Kafka na clusteru HDInsight, použijte následující příkaz:

    > [!NOTE]
    > Nahraďte `sshuser` pomocí uživatelského účtu SSH pro váš cluster HDInsight. Nahraďte `new-edgenode` s názvem uzlu edge. Nahraďte `clustername` se název clusteru. Další informace o koncový bod SSH pro hraničního uzlu, najdete v článku [používá uzly okraj v prostředí HDInsight](../hdinsight-apps-use-edge-node.md#access-an-edge-node) dokumentu.

    ```bash
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.clustername-ssh.azurehdinsight.net:
    ```

3. Po dokončení kopírování souborů se připojte k uzlu edge pomocí protokolu SSH:

    ```bash
    ssh sshuser@new-edgenode.clustername-ssh.azurehdinsight.net
    ```

    Další informace najdete v dokumentu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

4. Instalace konektoru do Kafka `libs` adresáře, použijte následující příkaz:

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

> [!TIP]
> Pokud narazíte na problémy s dalšími kroky v tomto dokumentu při používání předdefinovaných .jar souboru, zkuste jej sestavit balíček ze zdroje.
>
> Pokud chcete vytvořit konektor, musí mít prostředí pro vývoj Scala s [Scala sestavení nástroj](http://www.scala-sbt.org/).
>
> 1. Stáhněte si zdroj pro konektor z [ https://github.com/Azure/toketi-kafka-connect-iothub/ ](https://github.com/Azure/toketi-kafka-connect-iothub/) na svoje vývojové prostředí.
>
> 2. Z příkazového řádku v adresáři projektu použijte následující příkaz pro sestavení a balíček projektu:
>
>    ```bash
>    sbt assembly
>    ```
>
>    Tento příkaz vytvoří soubor s názvem `kafka-connect-iothub-assembly_2.11-0.6.jar` v `target/scala-2.11` adresář pro projekt.

## <a name="configure-kafka"></a>Konfigurace Kafka

Z připojení SSH k uzlu edge pomocí následujícího postupu můžete nakonfigurovat Kafka pro spuštění konektoru v samostatném režimu:

1. Název clusteru uložte do proměnné. Použití proměnné usnadňuje zkopírujte a vložte další příkazy v této části:

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

2. Nainstalujte `jq` nástroj. Tento nástroj usnadňuje zpracování dokumentů JSON, které jsou vráceny z dotazů Ambari:

    ```bash
    sudo apt -y install jq
    ```

3. Získáte adresu Kafka zprostředkovatelé. V clusteru může být mnoho zprostředkovatelé, ale potřebujete odkazovat na jeden nebo dva kusy. Pokud chcete získat adresu dva hostitele zprostředkovatele, použijte následující příkaz:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    Po zobrazení výzvy zadejte heslo pro účet přihlášení clusteru (admin). Vrácená hodnota je obdobná následujícímu textu:

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

4. Získáte adresu uzly Zookeeper. Existuje několik Zookeeper uzlů v clusteru, ale potřebujete odkazovat na jeden nebo dva kusy. K získání adresy dva uzly Zookeeper, použijte následující příkaz:

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

5. Při spuštění konektor v samostatném režimu, `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` souboru se používá ke komunikaci s Kafka zprostředkovatelé. Chcete-li upravit `connect-standalone.properties` souboru, použijte následující příkaz:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

    * Pokud chcete nakonfigurovat samostatnou konfiguraci hraničního uzlu najít makléřům Kafka, najít řádek, který začíná `bootstrap.servers=`. Nahraďte `localhost:9092` hodnotu s hostiteli zprostředkovatele z předchozího kroku.

    * Změna `key.converter=` a `value.converter=` řádky na následující hodnoty:

        ```text
        key.converter=org.apache.kafka.connect.storage.StringConverter
        value.converter=org.apache.kafka.connect.storage.StringConverter
        ```

        > [!IMPORTANT]
        > Tato změna umožňuje testování pomocí konzoly Autor součástí Kafka. Různé převaděče může být nutné pro další producenti a spotřebitelé. Informace o použití jiných převaděče hodnot najdete v tématu [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

    * Přidejte řádek na konci souboru, který obsahuje následující text:

        ```text
        consumer.max.poll.records=10
        ```

        > [!TIP]
        > Tuto změnu je zabránit vypršení časových limitů v konektoru podřízený omezení na 10 záznamů najednou. Další informace najdete na adrese [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

6. Chcete-li uložit soubor, použijte __kombinaci kláves Ctrl + X__, __Y__a potom __Enter__.

7. K vytvoření témata používá konektorem, použijte následující příkazy:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    Ověřit, jestli `iotin` a `iotout` témata neexistuje, použijte následující příkaz:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    `iotin` Téma se používá k přijetí zprávy ze služby IoT Hub. `iotout` Téma se používá k odeslání zprávy do služby IoT Hub.

## <a name="get-iot-hub-connection-information"></a>Získat informace o připojení služby IoT Hub

Pro načtení IoT hub informace používané konektorem, použijte následující kroky:

1. Získání koncového bodu kompatibilního s centrem událostí a název koncového bodu kompatibilního s centrem událostí služby IoT hub. Tyto informace získat, použijte jednu z následujících metod:

    * __Z [portál Azure](https://portal.azure.com/)__, použijte následující postup:

        1. Přejděte do služby IoT Hub a vyberte __koncové body__.
        2. Z __předdefinované koncové body__, vyberte __události__.
        3. Z __vlastnosti__, zkopírujte hodnotu následující pole:

            * __Název kompatibilní s centrem událostí__
            * __Koncový bod kompatibilní s centrem událostí__
            * __Oddíly__

        > [!IMPORTANT]
        > Hodnotu koncového bodu z portálu může obsahovat další text, který není nutný v tomto příkladu. Rozbalte text, který odpovídá tento vzor `sb://<randomnamespace>.servicebus.windows.net/`.

    * __Z [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__, použijte následující příkaz:

        ```azure-cli
        az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
        ```

        Nahraďte `myhubname` s názvem služby IoT hub. Odpověď je podobná následující text:

        ```text
        "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
        "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
        "Partitions": 2
        ```

2. Získat __sdílené zásady přístupu__ a __klíč__. V tomto příkladu použijte __služby__ klíč. Tyto informace získat, použijte jednu z následujících metod:

    * __Z [portál Azure](https://portal.azure.com/)__, použijte následující postup:

        1. Vyberte __zásady sdíleného přístupu__a potom vyberte __služby__.
        2. Kopírování __primární klíč__ hodnotu.
        3. Kopírování __připojovací řetězec – primární klíč__ hodnotu.

    * __Z [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__, použijte následující příkaz:

        1. Chcete-li získat hodnotu primárního klíče, použijte následující příkaz:

            ```azure-cli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            Nahraďte `myhubname` s názvem služby IoT hub. Odpověď je primárním klíčem na `service` zásady pro toto centrum.

        2. Chcete-li získat připojovací řetězec pro `service` zásady, použijte následující příkaz:

            ```azure-cli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            Nahraďte `myhubname` s názvem služby IoT hub. Odpovědí je připojovací řetězec pro `service` zásad.

## <a name="configure-the-source-connection"></a>Konfigurace připojení ke zdroji

Pokud chcete nakonfigurovat zdroj pro práci se službou IoT Hub, proveďte následující akce z připojení SSH k uzlu edge:

1. Vytvořit kopii `connect-iot-source.properties` v soubor `/usr/hdp/current/kafka-broker/config/` adresáře. Stažení souboru z projektu toketi-kafka-connect-iothub, použijte následující příkaz:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

2. Chcete-li upravit `connect-iot-source.properties` souboru a přidejte informace o IoT hub, použijte následující příkaz:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    V editoru najít a změnit následující položky:

    * `Kafka.Topic=PLACEHOLDER`: Nahraďte `PLACEHOLDER` s `iotin`. Zprávy přijaté ze služby IoT hub jsou umístěny v `iotin` tématu.
    * `IotHub.EventHubCompatibleName=PLACEHOLDER`: Nahraďte `PLACEHOLDER` s názvem kompatibilní s centrem událostí.
    * `IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`: Nahraďte `PLACEHOLDER` koncový bod kompatibilní s centrem událostí.
    * `IotHub.Partitions=PLACEHOLDER`: Nahraďte `PLACEHOLDER` s počet oddílů z předchozích kroků.
    * `IotHub.AccessKeyName=PLACEHOLDER`: Nahraďte `PLACEHOLDER` s `service`.
    * `IotHub.AccessKeyValue=PLACEHOLDER`: Nahraďte `PLACEHOLDER` s primární klíč `service` zásad.
    * `IotHub.StartType=PLACEHOLDER`: Nahraďte `PLACEHOLDER` s datum UTC. Toto datum je při spuštění konektoru probíhá kontrola zpráv. Formát data je `yyyy-mm-ddThh:mm:ssZ`.
    * `BatchSize=100`: Nahraďte `100` s `5`. Tato změna způsobí, že konektor umožní číst zprávy do Kafka, jakmile ve IoT hub jsou pět nových zpráv.

    Příklad konfigurace najdete v tématu [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

3. Chcete-li uložit změny, použijte __kombinaci kláves Ctrl + X__, __Y__a potom __Enter__.

Další informace o konfiguraci konektoru zdroje, najdete v části [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

## <a name="configure-the-sink-connection"></a>Nakonfigurujte připojení jímka

Konfigurace připojení jímku pro práci se službou IoT Hub, proveďte následující akce z připojení SSH k uzlu edge:

1. Vytvořit kopii `connect-iothub-sink.properties` v soubor `/usr/hdp/current/kafka-broker/config/` adresáře. Stažení souboru z projektu toketi-kafka-connect-iothub, použijte následující příkaz:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

2. Chcete-li upravit `connect-iothub-sink.properties` souboru a přidejte informace o IoT hub, použijte následující příkaz:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

    V editoru najít a změnit následující položky:

    * `topics=PLACEHOLDER`: Nahraďte `PLACEHOLDER` s `iotout`. Zpráv zapsaných do `iotout` téma se předávají do centra IoT.
    * `IotHub.ConnectionString=PLACEHOLDER`: Nahraďte `PLACEHOLDER` připojovacím řetězcem pro `service` zásad.

    Příklad konfigurace najdete v tématu [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

3. Chcete-li uložit změny, použijte __kombinaci kláves Ctrl + X__, __Y__a potom __Enter__.

Další informace o konfiguraci konektoru jímky, najdete v části [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

## <a name="start-the-source-connector"></a>Spustit konektor zdroje

Pokud chcete spustit konektor zdroje, použijte následující příkaz z připojení SSH k uzlu edge:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
```

Jakmile se spustí tento konektor, odesílání zpráv do služby IoT hub z vašeho zařízení. Konektor čte zprávy typu ze služby IoT hub a ukládá je v tomto tématu Kafka, protokoluje informace do konzoly:

```text
[2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.co
nnect.IotHubSourceTask:39)
[2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (
org.apache.kafka.connect.runtime.WorkerSourceTask:356)
```

> [!NOTE]
> Jak konektor spustí, může se zobrazit několik upozornění. Tato upozornění nezpůsobí problémy s přijímání zpráv ze služby IoT hub.

Chcete-li zastavit konektor, použijte __kombinaci kláves Ctrl + C__.

## <a name="start-the-sink-connector"></a>Spustit konektor jímka

Připojení SSH k uzlu edge můžete spustit konektor jímka v samostatném režimu následující příkaz:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

Konektor běží, zobrazí se informace podobná následující text:

```text
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connec
t.runtime.WorkerSinkTask:262)
```

> [!NOTE]
> Můžete si povšimnout několik upozornění konektor při spuštění. Ta můžete bezpečně ignorovat.

Pokud chcete odesílat zprávy prostřednictvím konektoru, použijte následující kroky:

1. Otevřete jiné relace SSH do clusteru Kafka:

    ```bash
    ssh sshuser@new-edgenode.clustername-ssh.azurehdinsight.net
    ```
2. K odeslání zprávy `iotout` tématu, použijte následující příkaz:

    > [!WARNING]
    > Protože se jedná o nové připojení SSH, `$KAFKABROKERS` proměnná neobsahuje žádné informace. Nastavit, použijte jednu z následujících metod:
    >
    > * Postupujte podle prvních tří kroků v [konfigurace Kafka](#configure-kafka) části.
    > * Použití `echo $KAFKABROKERS` z předchozího připojení SSH získat hodnoty a potom můžete nahradit `$KAFKABROKERS` v následujícím příkazu se skutečnými hodnotami.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    Tento příkaz nevrací jste do příkazového řádku normální Bash. Místo toho odešle vstup z klávesnice `iotout` tématu.

3. Chcete-li odeslat zprávu na vaše zařízení, vložte do relace SSH pro dokument JSON `kafka-console-producer`.

    > [!IMPORTANT]
    > Musíte nastavit hodnotu `"deviceId"` položku s ID vašeho zařízení. V následujícím příkladu je název zařízení `fakepi`:

    ```text
    {"messageId":"msg1","message":"Turn On","deviceId":"fakepi"}
    ```

    Schéma pro tento dokument JSON je podrobně popsaná v další v [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

    Pokud používáte simulované zařízení malin platformy a je spuštěná, je zařízení zaznamenána následující zpráva:

    ```text
    Receive message: Turn On
    ```

    Opětovné odeslání dokumentu JSON, ale změňte hodnotu `"message"` položku. Nová hodnota se zaznamená zařízení.

Další informace o použití konektoru jímka, najdete v části [ https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md ](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

## <a name="next-steps"></a>Další postup

V tomto dokumentu jste zjistili, jak používat rozhraní API Kafka připojit k spuštění konektoru Kafka IoT v HDInsight. Chcete-li zjistit další způsoby, jak pracovat s Kafka pomocí následujících odkazů:

* [Použití Apache Sparku se systémem Kafka ve službě HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Použití Apache Stormu se systémem Kafka ve službě HDInsight](../hdinsight-apache-storm-with-kafka.md)
