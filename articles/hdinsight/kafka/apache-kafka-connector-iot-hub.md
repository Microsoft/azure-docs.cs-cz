---
title: Použití Apache Kafka na HDInsight u Azure IoT Hubu
description: Přečtěte si, jak používat Apache Kafka na HDInsightu pomocí Azure IoT Hubu. Projekt Kafka Connect Azure IoT Hub poskytuje zdroj ový a jímací konektor pro Kafka. Zdrojový konektor můžete číst data z ioT hub uhloubení a konektor jímky zapíše do ioT hubu.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/26/2019
ms.openlocfilehash: 48a72b5ba3819712b9e1d2536ae2dd3a06eaf3f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238821"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>Použití Apache Kafka na HDInsight u Azure IoT Hubu

Zjistěte, jak pomocí konektoru [Apache Kafka Connect Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) přesunout data mezi Apache Kafka na HDInsighta a Azure IoT Hubu. V tomto dokumentu se dozvíte, jak spustit konektor služby IoT Hub z hraničního uzlu v clusteru.

Kafka Connect API umožňuje implementovat konektory, které průběžně natahují data do Kafky, nebo přesouvat data z Kafky do jiného systému. [Apache Kafka Connect Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) je konektor, který stahuje data z Azure IoT Hub do Kafky. Může také nabízená data z Kafky do centra IoT Hub.

Při vytahování z centra IoT Hub, použijte __zdrojový__ konektor. Při odesílání do ioT hubu, použijte konektor __jímky.__ Konektor ioT hub poskytuje zdroj ové a jímací konektory.

Následující diagram znázorňuje tok dat mezi Azure IoT Hub a Kafka na HDInsight při použití konektoru.

![Obrázek znázorňující data točící z IoT Hubu do Kafky přes konektor](./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png)

Další informace o rozhraní CONNECT [https://kafka.apache.org/documentation/#connect](https://kafka.apache.org/documentation/#connect)API naleznete v tématu .

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Kafka na HDInsight. Další informace najdete v dokumentu [Rychlý start k systému Kafka ve službě HDInsight](apache-kafka-get-started.md).

* Hraniční uzel v clusteru Kafka. Další informace najdete v tématu [Použití hraničních uzlů s dokumentem HDInsight.](../hdinsight-apps-use-edge-node.md)

* Klient SSH. Další informace naleznete [v tématu Připojení k HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Azure IoT Hub a zařízení. V tomto článku zvažte použití [online simulátoru Connect Raspberry Pi do služby Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-web-simulator-get-started).

* [Scala build nástroj](https://www.scala-sbt.org/).

## <a name="build-the-connector"></a>Sestavení konektoru

1. Stáhněte zdroj pro [https://github.com/Azure/toketi-kafka-connect-iothub/](https://github.com/Azure/toketi-kafka-connect-iothub/) konektor z místního prostředí.

2. Z příkazového řádku přejděte do `toketi-kafka-connect-iothub-master` adresáře. Potom k sestavení a sbalení projektu použijte následující příkaz:

    ```cmd
    sbt assembly
    ```

    Sestavení bude trvat několik minut. Příkaz vytvoří soubor `kafka-connect-iothub-assembly_2.11-0.7.0.jar` pojmenovaný `toketi-kafka-connect-iothub-master\target\scala-2.11` v adresáři projektu.

## <a name="install-the-connector"></a>Instalace konektoru

1. Nahrajte soubor .jar do hraničního uzlu clusteru Kafka v clusteru HDInsight. Upravte níže uvedený `CLUSTERNAME` příkaz nahrazením skutečným názvem clusteru. Výchozí hodnoty pro uživatelský účet SSH a název [hraničního uzlu](../hdinsight-apps-use-edge-node.md#access-an-edge-node) se používají níže, upravit podle potřeby.

    ```cmd
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Po dokončení kopírování souboru se připojte k hraničnímu uzlu pomocí SSH:

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Chcete-li konektor nainstalovat do `libs` adresáře Kafka, použijte následující příkaz:

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

Udržujte připojení SSH aktivní pro zbývající kroky.

## <a name="configure-apache-kafka"></a>Konfigurace Apache Kafka

Z připojení SSH k hraničnímu uzlu nakonfigurujte kafku následující kroky tak, aby spouštěla konektor v samostatném režimu:

1. Nastavte proměnnou hesla. Nahraďte heslo přihlašovacím heslem clusteru a zadejte příkaz:

    ```bash
    export password='PASSWORD'
    ```

1. Nainstalujte nástroj [jq.](https://stedolan.github.io/jq/) jq usnadňuje zpracování dokumentů JSON vrácených z dotazů Ambari. Zadejte následující příkaz:

    ```bash
    sudo apt -y install jq
    ```

1. Získejte adresu makléřů Kafka. Ve vašem clusteru může být mnoho zprostředkovatelů, ale stačí odkazovat na jeden nebo dva. Chcete-li získat adresu dvou hostitelů brokerů, použijte následující příkaz:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    Zkopírujte hodnoty pro pozdější použití. Vrácená hodnota je obdobná následujícímu textu:

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

1. Získejte adresu uzlů Apache Zookeeper. V clusteru je několik uzlů Zookeeper, ale stačí odkazovat na jeden nebo dva. Pro uložení adres v proměnné `KAFKAZKHOSTS`použijte následující příkaz :

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

1. Při spuštění konektoru v samostatném `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` režimu se soubor používá ke komunikaci s zprostředkovateli Kafka. Chcete-li `connect-standalone.properties` soubor upravit, použijte následující příkaz:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

1. Proveďte následující úpravy:

    |Aktuální hodnota |Nová hodnota | Poznámka |
    |---|---|---|
    |`bootstrap.servers=localhost:9092`|Nahraďte hodnotu `localhost:9092` hostiteli brokerů z předchozího kroku|Konfiguruje samostatnou konfiguraci hraničního uzlu, aby našel zprostředkovatele Kafka.|
    |`key.converter=org.apache.kafka.connect.json.JsonConverter`|`key.converter=org.apache.kafka.connect.storage.StringConverter`|Tato změna umožňuje testovat pomocí výrobce konzoly, který je součástí kafky. Možná budete potřebovat různé konvertory pro jiné výrobce a spotřebitele. Informace o použití jiných hodnot [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)převaděče naleznete v tématu .|
    |`value.converter=org.apache.kafka.connect.json.JsonConverter`|`value.converter=org.apache.kafka.connect.storage.StringConverter`|Stejně jako výše.|
    |Není dostupné.|`consumer.max.poll.records=10`|Přidat na konec souboru. Tato změna je zabránit časové limity v spojnici jímky omezením na 10 záznamů najednou. Další informace naleznete [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)v tématu .|

1. Chcete-li soubor uložit, použijte __kombinaci kláves Ctrl + X__, __Y__a potom __klávesu Enter__.

1. Chcete-li vytvořit témata používaná spojnicí, použijte následující příkazy:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    Chcete-li `iotin` ověřit, zda existují témata a, `iotout` použijte následující příkaz:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    Toto `iotin` téma se používá k přijímání zpráv z ioT hubu. Toto `iotout` téma se používá k odesílání zpráv do ioT hubu.

## <a name="get-iot-hub-connection-information"></a>Získání informací o připojení k centru IoT Hub

Chcete-li načíst informace o centru IoT používané konektorem, použijte následující kroky:

1. Získejte koncový bod kompatibilní s centrem událostí a název koncového bodu kompatibilního s centrem událostí pro vaše centrum IoT. Chcete-li získat tyto informace, použijte jednu z následujících metod:

   * __Na [webu Azure Portal](https://portal.azure.com/)__ použijte následující kroky:

     1. Přejděte do centra IoT Hub a vyberte __Koncové body__.
     2. V __předdefinované koncové body__vyberte __události__.
     3. Z __pole Vlastnosti__zkopírujte hodnotu následujících polí:

         * __Název kompatibilní s centrem událostí__
         * __Koncový bod kompatibilní s centrem událostí__
         * __Oddíly__

        > [!IMPORTANT]  
        > Hodnota koncového bodu z portálu může obsahovat další text, který není potřeba v tomto příkladu. Extrahovat text, `sb://<randomnamespace>.servicebus.windows.net/`který odpovídá tomuto vzoru .

   * __Z [příkazového příkazu k azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__ použijte následující příkaz:

       ```azurecli
       az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
       ```

       Nahraďte `myhubname` název vašeho centra IoT hub. Odpověď je podobná následujícímu textu:

       ```json
       "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
       "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
       "Partitions": 2
       ```

2. Získejte __zásady a__ __klíč__pro sdílený přístup . V tomto příkladu použijte klíč __služby.__ Chcete-li získat tyto informace, použijte jednu z následujících metod:

    * __Na [webu Azure Portal](https://portal.azure.com/)__ použijte následující kroky:

        1. Vyberte __zásady sdíleného přístupu__a vyberte __službu__.
        2. Zkopírujte hodnotu __primárního klíče.__
        3. Zkopírujte řetězec připojení – hodnotu __primárního klíče.__

    * __Z [příkazového příkazu k azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)__ použijte následující příkaz:

        1. Chcete-li získat hodnotu primárního klíče, použijte následující příkaz:

            ```azurecli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            Nahraďte `myhubname` název vašeho centra IoT hub. Odpověď je primární klíč `service` k zásadě pro toto centrum.

        2. Chcete-li získat připojovací řetězec pro zásadu, `service` použijte následující příkaz:

            ```azurecli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            Nahraďte `myhubname` název vašeho centra IoT hub. Odpověď je připojovací `service` řetězec pro zásadu.

## <a name="configure-the-source-connection"></a>Konfigurace zdrojového připojení

Chcete-li nakonfigurovat zdroj pro práci s vaším službou IoT Hub, proveďte následující akce z připojení SSH k hraničnímu uzlu:

1. Vytvořte kopii `connect-iot-source.properties` souboru `/usr/hdp/current/kafka-broker/config/` v adresáři. Chcete-li stáhnout soubor z projektu toketi-kafka-connect-iothub, použijte následující příkaz:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

1. Chcete-li `connect-iot-source.properties` soubor upravit a přidat informace o centru IoT, použijte následující příkaz:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

1. V editoru najděte a změňte následující položky:

    |Aktuální hodnota |Upravit|
    |---|---|
    |`Kafka.Topic=PLACEHOLDER`|Nahraďte `PLACEHOLDER` za `iotin` (Jak velká může být moje znalostní báze?). Zprávy přijaté z centra IoT `iotin` hub jsou umístěny v tématu.|
    |`IotHub.EventHubCompatibleName=PLACEHOLDER`|Nahraďte `PLACEHOLDER` název kompatibilní s centrem událostí.|
    |`IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`|Nahraďte `PLACEHOLDER` koncovýbod kompatibilní s centrem událostí.|
    |`IotHub.AccessKeyName=PLACEHOLDER`|Nahraďte `PLACEHOLDER` za `service` (Jak velká může být moje znalostní báze?).|
    |`IotHub.AccessKeyValue=PLACEHOLDER`|Nahraďte `PLACEHOLDER` primárním `service` klíčem zásady.|
    |`IotHub.Partitions=PLACEHOLDER`|Nahraďte `PLACEHOLDER` se počtem oddílů z předchozích kroků.|
    |`IotHub.StartTime=PLACEHOLDER`|Nahraďte `PLACEHOLDER` datem UTC. Toto datum je, když konektor začne kontrolovat zprávy. Formát data `yyyy-mm-ddThh:mm:ssZ`je .|
    |`BatchSize=100`|Nahraďte `100` za `5` (Jak velká může být moje znalostní báze?). Tato změna způsobí, že konektor číst zprávy do Kafka, jakmile existuje pět nových zpráv v centru IoT.|

    Příklad konfigurace najdete v tématu [Kafka Connect Source Connector pro Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

1. Chcete-li uložit změny, použijte __kombinaci kláves Ctrl + X__, __Y__a potom __klávesu Enter__.

Další informace o konfiguraci zdroje [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md)konektoru naleznete v tématu .

## <a name="configure-the-sink-connection"></a>Konfigurace připojení jímky

Chcete-li nakonfigurovat připojení jímky pro práci s centrem IoT Hub, proveďte následující akce z připojení SSH k hraničnímu uzlu:

1. Vytvořte kopii `connect-iothub-sink.properties` souboru `/usr/hdp/current/kafka-broker/config/` v adresáři. Chcete-li stáhnout soubor z projektu toketi-kafka-connect-iothub, použijte následující příkaz:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

1. Chcete-li `connect-iothub-sink.properties` soubor upravit a přidat informace o centru IoT, použijte následující příkaz:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

1. V editoru najděte a změňte následující položky:

    |Aktuální hodnota |Upravit|
    |---|---|
    |`topics=PLACEHOLDER`|Nahraďte `PLACEHOLDER` za `iotout` (Jak velká může být moje znalostní báze?). Zprávy zapsané do tématu jsou `iotout` předávány do centra IoT hub.|
    |`IotHub.ConnectionString=PLACEHOLDER`|Nahraďte `PLACEHOLDER` připojovacím řetězcem pro zásadu. `service`|

    Příklad konfigurace najdete v [tématu Kafka Connect Sink Connector pro Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

1. Chcete-li uložit změny, použijte __kombinaci kláves Ctrl + X__, __Y__a potom __klávesu Enter__.

Další informace o konfiguraci jímky [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)konektoru naleznete v tématu .

## <a name="start-the-source-connector"></a>Spuštění zdrojového konektoru

1. Chcete-li spustit zdrojovou spojnici, použijte následující příkaz z připojení SSH k hraničnímu uzlu:

    ```bash
    /usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    Po spuštění konektoru odesílejte zprávy do centra IoT hub z vašich zařízení. Jako konektor čte zprávy z centra IoT a ukládá je v tématu Kafka, zaznamenává informace do konzoly:

    ```output
    [2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.connect.IotHubSourceTask:39)
    [2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (org.apache.kafka.connect.runtime.WorkerSourceTask:356)
    ```

    > [!NOTE]  
    > Při spuštění konektoru se může zobrazit několik upozornění. Tato upozornění nezpůsobují problémy s přijímáním zpráv z centra IoT Hub.

1. Po několika minutách zastavte konektor pomocí **ctrl + c** dvakrát. Zastavení konektoru bude trvat několik minut.

## <a name="start-the-sink-connector"></a>Spuštění konektoru jímky

Z připojení SSH k hraničnímu uzlu spusťte konektor jímky v samostatném režimu pomocí následujícího příkazu:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

Při spuštění spojnice se zobrazí informace podobné následujícímu textu:

```output
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connect.runtime.WorkerSinkTask:262)
```

> [!NOTE]  
> Při spuštění konektoru můžete zaznamenat několik upozornění. Ta můžete bezpečně ignorovat.

## <a name="send-messages"></a>Odesílání zpráv

Chcete-li odesílat zprávy prostřednictvím konektoru, postupujte takto:

1. Otevřete *druhou* relaci SSH do clusteru Kafka:

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Získejte adresu makléřů Kafka pro nové ssh session. Nahraďte heslo přihlašovacím heslem clusteru a zadejte příkaz:

    ```bash
    export password='PASSWORD'

    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

1. Chcete-li odesílat zprávy do tématu, `iotout` použijte následující příkaz:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    Tento příkaz vás nevrátí k normálnímu bashovému řádku. Místo toho odešle vstup `iotout` z klávesnice do tématu.

1. Chcete-li odeslat zprávu do zařízení, vložte dokument JSON do relace SSH pro `kafka-console-producer`.

    > [!IMPORTANT]  
    > Je nutné nastavit hodnotu položky `"deviceId"` na ID vašeho zařízení. V následujícím příkladu je `myDeviceId`zařízení pojmenováno :

    ```json
    {"messageId":"msg1","message":"Turn On","deviceId":"myDeviceId"}
    ```

    Schéma tohoto dokumentu JSON je podrobněji popsáno [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)na .

    Pokud používáte simulované zařízení Raspberry Pi a je spuštěno, zařízení zaznamená následující zprávu:

    ```output
    Receive message: Turn On
    ```

    Znovu odešlete dokument JSON, ale `"message"` změňte hodnotu položky. Nová hodnota je zaznamenána zařízením.

Další informace o použití konektoru [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md)jímky naleznete v tématu .

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste se naučili používat rozhraní Apache Kafka Connect API ke spuštění konektoru IoT Kafka na HDInsight. Pomocí následujících odkazů můžete zjistit další způsoby práce s Kafkou:

* [Používejte Apache Spark s Apache Kafka na HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Použijte Apache Storm s Apache Kafka na HDInsight](../hdinsight-apache-storm-with-kafka.md)
