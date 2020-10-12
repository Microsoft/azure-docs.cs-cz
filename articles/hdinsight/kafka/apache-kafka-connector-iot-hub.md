---
title: Použití Apache Kafka v HDInsight s Azure IoT Hub
description: Naučte se používat Apache Kafka v HDInsight s Azure IoT Hub. Projekt Kafka Connect Azure IoT Hub poskytuje konektor pro zdroj a jímku pro Kafka. Zdrojový konektor může číst data z IoT Hub a konektor jímky zapisuje do IoT Hub.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/26/2019
ms.openlocfilehash: ea7aa7758b5ccf7be02fa8d450ce710dcbef86a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86087380"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>Použití Apache Kafka v HDInsight s Azure IoT Hub

Naučte se používat [Apache Kafka připojit konektor Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) k přesunu dat mezi Apache Kafka v HDInsight a Azure IoT Hub. V tomto dokumentu se dozvíte, jak spustit konektor IoT Hub z hraničního uzlu v clusteru.

Rozhraní API Kafka Connect umožňuje implementovat konektory, které průběžně přidávají data do Kafka, nebo doručovat data z Kafka do jiného systému. [Apache Kafka připojit Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) je konektor, který získává data z Azure IoT Hub do Kafka. Může také doručovat data z Kafka do IoT Hub.

Při přijímání z IoT Hub použijete __zdrojový__ konektor. Při doručování do IoT Hub použijete konektor __jímky__ . Konektor IoT Hub poskytuje zdrojové i jímky konektoru.

Následující diagram znázorňuje tok dat mezi Azure IoT Hub a Kafka v HDInsight při použití konektoru.

![Obrázek znázorňující tok dat z IoT Hub do Kafka prostřednictvím konektoru](./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png)

Další informace o rozhraní API pro připojení najdete v tématu [https://kafka.apache.org/documentation/#connect](https://kafka.apache.org/documentation/#connect) .

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Kafka v HDInsight. Další informace najdete v dokumentu [Rychlý start k systému Kafka ve službě HDInsight](apache-kafka-get-started.md).

* Hraniční uzel v clusteru Kafka. Další informace najdete v dokumentu [použití hraničních uzlů s](../hdinsight-apps-use-edge-node.md) dokumentem HDInsight.

* Klient SSH. Další informace najdete v tématu [připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* IoT Hub a zařízení Azure. V tomto článku zvažte použití [simulátoru připojení ke službě malin. PI online do Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-web-simulator-get-started).

* [Nástroj Scala Build](https://www.scala-sbt.org/).

## <a name="build-the-connector"></a>Sestavení konektoru

1. Stáhněte si zdroj konektoru z nástroje [https://github.com/Azure/toketi-kafka-connect-iothub/](https://github.com/Azure/toketi-kafka-connect-iothub/) do místního prostředí.

2. Z příkazového řádku přejděte do `toketi-kafka-connect-iothub-master` adresáře. Potom pomocí následujícího příkazu Sestavte a zabalite projekt:

    ```cmd
    sbt assembly
    ```

    Dokončení sestavení bude trvat několik minut. Příkaz vytvoří soubor s názvem `kafka-connect-iothub-assembly_2.11-0.7.0.jar` v `toketi-kafka-connect-iothub-master\target\scala-2.11` adresáři pro projekt.

## <a name="install-the-connector"></a>Instalace konektoru

1. Nahrajte soubor. jar do hraničního uzlu vašeho Kafka v clusteru HDInsight. Následující příkaz upravte tak, že nahradíte `CLUSTERNAME` skutečným názvem vašeho clusteru. Níže jsou uvedeny výchozí hodnoty uživatelského účtu SSH a název [hraničního uzlu](../hdinsight-apps-use-edge-node.md#access-an-edge-node) , podle potřeby upravit.

    ```cmd
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Po dokončení kopírování souborů se připojte k hraničnímu uzlu pomocí SSH:

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Chcete-li nainstalovat konektor do `libs` adresáře Kafka, použijte následující příkaz:

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

Nechejte připojení SSH aktivní pro zbývající kroky.

## <a name="configure-apache-kafka"></a>Konfigurace Apache Kafka

V rámci připojení SSH k hraničnímu uzlu pomocí následujících kroků nakonfigurujte Kafka, aby konektor běžel v samostatném režimu:

1. Nastavte proměnnou hesla. Heslo pro přihlášení ke clusteru nahraďte heslem a pak zadejte příkaz:

    ```bash
    export password='PASSWORD'
    ```

1. Nainstalujte nástroj [JQ](https://stedolan.github.io/jq/) . JQ usnadňuje zpracování dokumentů JSON vrácených dotazy Ambari. Zadejte následující příkaz:

    ```bash
    sudo apt -y install jq
    ```

1. Získejte adresu zprostředkovatelů Kafka. V clusteru může být spousta zprostředkovatelů, ale stačí, abyste odkazovali jenom na jeden nebo dva. Pokud chcete získat adresu dvou hostitelů zprostředkovatelů, použijte následující příkaz:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    Zkopírujte hodnoty pro pozdější použití. Vrácená hodnota je obdobná následujícímu textu:

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

1. Získejte adresu uzlů Apache Zookeeper. V clusteru je několik uzlů Zookeeper, ale stačí, abyste odkazovali na jeden nebo dva uzly. Pomocí následujícího příkazu uložte adresy v proměnné `KAFKAZKHOSTS` :

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

1. Při spuštění konektoru v samostatném režimu se `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` soubor používá ke komunikaci s Kafka zprostředkovateli. Pokud chcete `connect-standalone.properties` soubor upravit, použijte následující příkaz:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

1. Proveďte následující úpravy:

    |Aktuální hodnota |Nová hodnota | Komentář |
    |---|---|---|
    |`bootstrap.servers=localhost:9092`|Nahraďte `localhost:9092` hodnotu hostiteli zprostředkovatele z předchozího kroku.|Nakonfiguruje samostatnou konfiguraci pro hraniční uzel, aby bylo možné najít zprostředkovatele Kafka.|
    |`key.converter=org.apache.kafka.connect.json.JsonConverter`|`key.converter=org.apache.kafka.connect.storage.StringConverter`|Tato změna umožňuje testovat pomocí výrobce konzoly, který je součástí Kafka. Pro ostatní výrobce a zákazníky budete možná potřebovat jiné převaděče. Informace o použití jiných hodnot převaděče naleznete v tématu [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) .|
    |`value.converter=org.apache.kafka.connect.json.JsonConverter`|`value.converter=org.apache.kafka.connect.storage.StringConverter`|Stejné jako výše.|
    |Není k dispozici|`consumer.max.poll.records=10`|Přidat ke konci souboru Tato změna umožňuje zabránit vypršení časových limitů v konektoru jímky tím, že je omezí na 10 záznamů najednou. Další informace najdete na webu [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).|

1. Pokud chcete soubor uložit, použijte __CTRL + X__, __Y__a pak __Zadejte__.

1. Chcete-li vytvořit témata používaná konektorem, použijte následující příkazy:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    Chcete-li ověřit, zda `iotin` `iotout` existují témata a, použijte následující příkaz:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    `iotin`Téma slouží k přijímání zpráv z IoT Hub. `iotout`Téma slouží k posílání zpráv do IoT Hub.

## <a name="get-iot-hub-connection-information"></a>Získat informace o IoT Hub připojení

Chcete-li získat informace o službě IoT Hub používané konektorem, použijte následující postup:

1. Získejte název koncového bodu kompatibilního s centrem událostí a název koncového bodu kompatibilního s centrem událostí pro službu IoT Hub. Chcete-li získat tyto informace, použijte jednu z následujících metod:

   * __Z [Azure Portal](https://portal.azure.com/)__ použijte následující postup:

     1. Přejděte do IoT Hub a vyberte __koncové body__.
     2. Z __předdefinovaných koncových bodů__vyberte možnost __události__.
     3. Z __vlastností__Zkopírujte hodnotu následujících polí:

         * __Název kompatibilní s centrem událostí__
         * __Koncový bod kompatibilní s centrem událostí__
         * __Oddíly__

        > [!IMPORTANT]  
        > Hodnota koncového bodu z portálu může obsahovat navíc text, který v tomto příkladu není potřeba. Extrahujte text, který odpovídá tomuto vzoru `sb://<randomnamespace>.servicebus.windows.net/` .

   * __V [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__ použijte následující příkaz:

       ```azurecli
       az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
       ```

       Nahraďte `myhubname` názvem vašeho centra IoT. Odpověď je podobná následujícímu textu:

       ```json
       "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
       "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
       "Partitions": 2
       ```

2. Získejte __zásady sdíleného přístupu__ a __klíč__. V tomto příkladu použijte klíč __služby__ . Chcete-li získat tyto informace, použijte jednu z následujících metod:

    * __Z [Azure Portal](https://portal.azure.com/)__ použijte následující postup:

        1. Vyberte __zásady sdíleného přístupu__a pak vyberte __Služba__.
        2. Zkopírujte hodnotu __primárního klíče__ .
        3. Zkopírujte __připojovací řetězec--hodnota primárního klíče__ .

    * __V [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__ použijte následující příkaz:

        1. K získání hodnoty primárního klíče použijte následující příkaz:

            ```azurecli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            Nahraďte `myhubname` názvem vašeho centra IoT. Odpověď je primární klíč pro `service` zásady pro toto centrum.

        2. Připojovací řetězec pro `service` zásadu získáte pomocí následujícího příkazu:

            ```azurecli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            Nahraďte `myhubname` názvem vašeho centra IoT. Odpověď je připojovací řetězec pro `service` zásadu.

## <a name="configure-the-source-connection"></a>Konfigurace zdrojového připojení

Chcete-li nakonfigurovat zdroj pro práci s IoT Hub, proveďte následující akce z připojení SSH k hraničnímu uzlu:

1. Vytvořte kopii `connect-iot-source.properties` souboru v `/usr/hdp/current/kafka-broker/config/` adresáři. Chcete-li stáhnout soubor z projektu toketi-Kafka-Connect-iothub, použijte následující příkaz:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

1. Chcete-li upravit `connect-iot-source.properties` soubor a přidat informace o službě IoT Hub, použijte následující příkaz:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

1. V editoru vyhledejte a změňte následující položky:

    |Aktuální hodnota |Upravit|
    |---|---|
    |`Kafka.Topic=PLACEHOLDER`|Nahraďte `PLACEHOLDER` za `iotin` (Jak velká může být moje znalostní báze?). Zprávy přijaté ze služby IoT Hub jsou uvedeny v `iotin` tématu.|
    |`IotHub.EventHubCompatibleName=PLACEHOLDER`|Nahraďte `PLACEHOLDER` názvem kompatibilním s centrem událostí.|
    |`IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`|Nahraďte `PLACEHOLDER` koncovým bodem kompatibilním se službou Event hub.|
    |`IotHub.AccessKeyName=PLACEHOLDER`|Nahraďte `PLACEHOLDER` za `service` (Jak velká může být moje znalostní báze?).|
    |`IotHub.AccessKeyValue=PLACEHOLDER`|Nahraďte `PLACEHOLDER` primárním klíčem `service` zásad.|
    |`IotHub.Partitions=PLACEHOLDER`|Nahraďte `PLACEHOLDER` počtem oddílů z předchozích kroků.|
    |`IotHub.StartTime=PLACEHOLDER`|Nahraďte `PLACEHOLDER` datem UTC. Toto datum je v případě, že konektor spouští kontrolu zpráv. Formát data je `yyyy-mm-ddThh:mm:ssZ` .|
    |`BatchSize=100`|Nahraďte `100` za `5` (Jak velká může být moje znalostní báze?). Tato změna způsobí, že konektor čte zprávy do Kafka, jakmile bude v centru IoT Hub pět nových zpráv.|

    Příklad konfigurace najdete v tématu [Kafka Connect source Connector for Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

1. Chcete-li uložit změny, použijte __kombinaci kláves CTRL + X__, __Y__a potom __Zadejte__.

Další informace o konfiguraci zdroje konektoru najdete v tématu [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md) .

## <a name="configure-the-sink-connection"></a>Konfigurace připojení jímky

Chcete-li nakonfigurovat připojení jímky pro práci s IoT Hub, proveďte následující akce z připojení SSH k hraničnímu uzlu:

1. Vytvořte kopii `connect-iothub-sink.properties` souboru v `/usr/hdp/current/kafka-broker/config/` adresáři. Chcete-li stáhnout soubor z projektu toketi-Kafka-Connect-iothub, použijte následující příkaz:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

1. Chcete-li upravit `connect-iothub-sink.properties` soubor a přidat informace o službě IoT Hub, použijte následující příkaz:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

1. V editoru vyhledejte a změňte následující položky:

    |Aktuální hodnota |Upravit|
    |---|---|
    |`topics=PLACEHOLDER`|Nahraďte `PLACEHOLDER` za `iotout` (Jak velká může být moje znalostní báze?). Zprávy zapsané do `iotout` tématu se předávají do centra IoT.|
    |`IotHub.ConnectionString=PLACEHOLDER`|Nahraďte `PLACEHOLDER` připojovacím řetězcem pro `service` zásadu.|

    Příklad konfigurace najdete v tématu [konektor jímky Kafka Connect pro Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

1. Chcete-li uložit změny, použijte __kombinaci kláves CTRL + X__, __Y__a potom __Zadejte__.

Další informace o konfiguraci jímky konektoru najdete v tématu [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) .

## <a name="start-the-source-connector"></a>Spustit zdrojový konektor

1. Pokud chcete spustit zdrojový konektor, použijte následující příkaz z připojení SSH k hraničnímu uzlu:

    ```bash
    /usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    Po spuštění konektoru odešlete zprávy do služby IoT Hub ze svých zařízení. Protože konektor čte zprávy ze služby IoT Hub a ukládá je v tématu Kafka, protokoluje informace do konzoly:

    ```output
    [2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.connect.IotHubSourceTask:39)
    [2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (org.apache.kafka.connect.runtime.WorkerSourceTask:356)
    ```

    > [!NOTE]  
    > Při spuštění konektoru se může zobrazit několik upozornění. Tato upozornění nezpůsobují problémy s přijímáním zpráv ze služby IoT Hub.

1. Zastavte konektor za několik minut dvakrát pomocí **kombinace kláves CTRL + C** . Zastavení konektoru bude trvat několik minut.

## <a name="start-the-sink-connector"></a>Spustit konektor jímky

Z připojení SSH k hraničnímu uzlu pomocí následujícího příkazu spusťte konektor jímky v samostatném režimu:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

Při spuštění konektoru se zobrazí informace podobné následujícímu textu:

```output
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connect.runtime.WorkerSinkTask:262)
```

> [!NOTE]  
> Při spuštění konektoru si můžete všimnout několika upozornění. Ta můžete bezpečně ignorovat.

## <a name="send-messages"></a>Odesílání zpráv

K odesílání zpráv prostřednictvím konektoru použijte následující postup:

1. Otevřete *druhou* relaci SSH ke clusteru Kafka:

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Získá adresu zprostředkovatelů Kafka pro novou relaci SSH. Heslo pro přihlášení ke clusteru nahraďte heslem a pak zadejte příkaz:

    ```bash
    export password='PASSWORD'

    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

1. Chcete-li odeslat zprávy do `iotout` tématu, použijte následující příkaz:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    Tento příkaz vás nevrátí do normálního bash výzvy. Místo toho odesílá vstup z klávesnice do `iotout` tématu.

1. Pokud chcete do zařízení poslat zprávu, vložte dokument JSON do relace SSH pro `kafka-console-producer` .

    > [!IMPORTANT]  
    > Je nutné nastavit hodnotu `"deviceId"` položky na ID vašeho zařízení. V následujícím příkladu se zařízení jmenuje `myDeviceId` :

    ```json
    {"messageId":"msg1","message":"Turn On","deviceId":"myDeviceId"}
    ```

    Schéma pro tento dokument JSON je podrobněji popsáno na adrese [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) .

    Pokud používáte simulované zařízení malinu pro malin a je spuštěno, zaznamená se tato zpráva do protokolu zařízení:

    ```output
    Receive message: Turn On
    ```

    Znovu odešle dokument JSON, ale změní hodnotu `"message"` položky. Zařízení zaznamená novou hodnotu.

Další informace o použití konektoru jímky najdete v tématu [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) .

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak pomocí rozhraní API Apache Kafka Connect spustit konektor IoT Kafka ve službě HDInsight. Pomocí následujících odkazů můžete zjistit další způsoby práce s Kafka:

* [Použití Apache Spark s Apache Kafka v HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Použití Apache Storm s Apache Kafka v HDInsight](../hdinsight-apache-storm-with-kafka.md)
