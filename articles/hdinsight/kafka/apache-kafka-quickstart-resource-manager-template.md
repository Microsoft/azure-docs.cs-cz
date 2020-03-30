---
title: 'Úvodní příručka: Apache Kafka pomocí Azure Resource Manager - HDInsight'
description: V tomto rychlém startu se dozvíte, jak vytvořit cluster Apache Kafka na Azure HDInsight pomocí šablony Azure Resource Manager. Dozvíte se také o tématech, předplatitelích a konzumentech platformy Kafka.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: f5f92044a0274b809388eeb164be9f1587013e0b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80064619"
---
# <a name="quickstart-create-apache-kafka-cluster-in-azure-hdinsight-using-resource-manager-template"></a>Úvodní příručka: Vytvoření clusteru Apache Kafka v Azure HDInsight pomocí šablony Správce prostředků

V tomto rychlém startu použijete šablonu Azure Resource Manager k vytvoření clusteru [Apache Kafka](./apache-kafka-introduction.md) v Azure HDInsight. Kafka je open source distribuovaná streamovací platforma. Často se používá jako zprostředkovatel zpráv, protože nabízí funkce podobné frontě pro publikování a odběr zpráv.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Rozhraní API Kafka je přístupné jenom pro prostředky ve stejné virtuální síti. V tomto rychlém startu budete ke clusteru přistupovat přímo pomocí SSH. Pokud chcete k platformě Kafka připojit jiné služby, sítě nebo virtuální počítače, musíte nejprve vytvořit virtuální síť a pak v síti vytvořit prostředky. Další informace najdete v dokumentu [Připojení k platformě Apache Kafka pomocí virtuální sítě](apache-kafka-connect-vpn-gateway.md).

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="create-an-apache-kafka-cluster"></a>Vytvoření clusteru Apache Kafka

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure QuickStart](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-kafka).

:::code language="json" source="~/quickstart-templates/101-hdinsight-kafka/azuredeploy.json" range="1-150":::

V šabloně jsou definovány dva prostředky Azure:

* [Microsoft.Storage/storageAccounts:](https://docs.microsoft.com/azure/templates/microsoft.storage/storageaccounts)vytvořte účet úložiště Azure.
* [Microsoft.HDInsight/cluster:](https://docs.microsoft.com/azure/templates/microsoft.hdinsight/clusters)vytvořte cluster HDInsight.

### <a name="deploy-the-template"></a>Nasazení šablony

1. Kliknutím na tlačítko **Nasadit do Azure** se přihlaste do Azure a otevřete šablonu Správce prostředků.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-kafka-java-get-started%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/apache-kafka-quickstart-resource-manager-template/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

1. Zadejte nebo vyberte tyto hodnoty:

    |Vlastnost |Popis |
    |---|---|
    |Předplatné|V rozevíracím seznamu vyberte předplatné Azure, které se používá pro cluster.|
    |Skupina prostředků|V rozevíracím seznamu vyberte existující skupinu prostředků nebo vyberte **Vytvořit nový**.|
    |Umístění|Hodnota se automaticky naplní umístěním použitým pro skupinu prostředků.|
    |Název clusteru|Zadejte globálně jedinečný název. Pro tuto šablonu používejte pouze malá písmena a čísla.|
    |Uživatelské jméno přihlášení clusteru|Zadejte uživatelské jméno, výchozí je **admin**.|
    |Heslo přihlášení clusteru|Zadejte heslo. Heslo musí mít délku alespoň 10 znaků a musí obsahovat alespoň jednu číslici, jedno velké a jedno malé písmeno, jeden nealfanumerický znak (kromě znaků " " ). |
    |Uživatelské jméno SSH|Zadejte uživatelské jméno, výchozí je **sshuser**|
    |Ssh heslo|Zadejte heslo.|

    ![Snímek obrazovky vlastností šablony](./media/apache-kafka-quickstart-resource-manager-template/resource-manager-template-kafka.png)

1. Přečtěte si **podmínky**. Pak vyberte **Souhlasím s podmínkami uvedenými výše**, pak **nákup**. Obdržíte oznámení, že probíhá nasazení. Vytvoření clusteru trvá přibližně 20 minut.

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Po vytvoření clusteru obdržíte oznámení **o úspěšném nasazení** s odkazem **přejít na prostředek.** Na stránce skupiny prostředků bude uveden nový cluster HDInsight a výchozí úložiště přidružené k clusteru. Každý cluster má účet [Azure Storage](../hdinsight-hadoop-use-blob-storage.md) nebo závislost na účtu Azure Data [Lake Storage.](../hdinsight-hadoop-use-data-lake-store.md) Označuje se jako výchozí účet úložiště. Cluster HDInsight a jeho výchozí účet úložiště musí být společně umístěny ve stejné oblasti Azure. Odstraněním clusterů účet úložiště neodstraníte.

## <a name="get-the-apache-zookeeper-and-broker-host-information"></a>Získejte informace o hostiteli Apache Zookeeper a Broker

Při práci s Kafkou musíte znát hostitele *Apache Zookeeper* a *Broker.* Tito hostitelé se používají s rozhraním Kafka API s mnohými z nástrojů, které se s tímto systémem dodávají.

V této části získáte informace o hostiteli z rozhraní REST API Ambari v clusteru.

1. Pomocí [příkazu ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) se připojte ke clusteru. Upravte níže uvedený příkaz nahrazením názvu clusteru názvem clusteru a zadejte příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Z připojení SSH použijte následující příkaz `jq` k instalaci nástroje. Tento nástroj slouží k parsování dokumentů JSON a je užitečný při načítání informací o hostiteli:

    ```bash
    sudo apt -y install jq
    ```

1. K nastavení proměnné prostředí na název clusteru použijte následující příkaz:

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

    Po zobrazení výzvy zadejte název clusteru Kafka.

1. Chcete-li nastavit proměnnou prostředí s informacemi o hostiteli Zookeeper, použijte následující příkaz. Příkaz načte všechny hostitele Zookeeper a pak vrátí pouze první dvě položky. Je to proto, že chcete určitou redundanci pro případ, že jeden hostitel bude nedosažitelný.

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Po zobrazení výzvy zadejte heslo účtu pro přihlášení ke clusteru (ne účtu SSH).

1. Pokud chcete ověřit správné nastavení proměnné prostředí, použijte následující příkaz:

    ```bash
     echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    ```

    Tento příkaz by měl vrátit informace podobné následujícímu textu:

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

1. K nastavení proměnné prostředí s použitím informací o hostiteli zprostředkovatele Kafka použijte následující příkaz:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

    Po zobrazení výzvy zadejte heslo účtu pro přihlášení ke clusteru (ne účtu SSH).

1. Pokud chcete ověřit správné nastavení proměnné prostředí, použijte následující příkaz:

    ```bash
    echo '$KAFKABROKERS='$KAFKABROKERS
    ```

    Tento příkaz by měl vrátit informace podobné následujícímu textu:

    `wn1-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092,wn0-kafka.eahjefxxp1netdbyklgqj5y1ud.cx.internal.cloudapp.net:9092`

## <a name="manage-apache-kafka-topics"></a>Správa témat Apache Kafka

Kafka ukládá datové proudy do *témat*. Témata můžete spravovat pomocí nástroje `kafka-topics.sh`.

* **K vytvoření tématu** použijte tento příkaz v připojení SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    Tento příkaz se připojí k hostiteli Zookeeper s použitím informací uložených v proměnné `$KAFKAZKHOSTS`. Pak vytvoří téma Kafka s názvem **test**.

    * Data uložená v tomto tématu jsou rozdělená mezi osm oddílů.

    * Každý oddíl se replikuje mezi tři pracovní uzly v clusteru.

        Pokud jste vytvořili cluster v oblasti Azure, která poskytuje tři domény selhání, použijte faktor replikace 3. Jinak použijte faktor replikace 4.
        
        V oblastech se třemi doménami selhání faktor replikace 3 umožní rozložení replik mezi domény selhání. V oblastech se dvěma doménami selhání faktor replikace 4 rozloží repliky rovnoměrně mezi domény selhání.
        
        Informace o počtu domén selhání v oblasti najdete v dokumentu popisujícím [dostupnost Linuxových virtuálních počítačů](../../virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set).

        Kafka neví o doménách selhání Azure. Při vytváření replik oddílu pro témata se nemusí repliky distribuovat správně z hlediska vysoké dostupnosti.

        Chcete-li zajistit vysokou dostupnost, použijte [nástroj pro vyvážení oddílů Apache Kafka](https://github.com/hdinsight/hdinsight-kafka-tools). Tento nástroj se musí spustit z připojení SSH k hlavnímu uzlu clusteru Kafka.

        K zajištění nejvyšší dostupnosti dat Kafka byste měli obnovit rovnováhu replik oddílů pro vaše téma v těchto situacích:

        * Při vytvoření nového tématu nebo oddílu

        * Při vertikálním navýšení kapacity clusteru

* **K zobrazení seznamu témat** použijte tento příkaz:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    Tento příkaz vypíše seznam témat dostupných v clusteru Kafka.

* **K odstranění tématu** použijte tento příkaz:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic topicname --zookeeper $KAFKAZKHOSTS
    ```

    Tento příkaz odstraní téma s názvem `topicname`.

    > [!WARNING]  
    > Pokud odstraníte dříve vytvořené téma `test`, pak ho musíte vytvořit znovu. Používá se v dalších krocích tohoto dokumentu.

Další informace o příkazech, které jsou k dispozici v nástroji `kafka-topics.sh`, získáte pomocí tohoto příkazu:

```bash
/usr/hdp/current/kafka-broker/bin/kafka-topics.sh
```

## <a name="produce-and-consume-records"></a>Produkce a konzumace záznamů

Kafka ukládá *záznamy* v tématech. Záznamy jsou vytvářeny *producenty* a spotřebovávány *konzumenty*. Producenti a konzumenti komunikují se službou *zprostředkovatele Kafka*. Každý pracovní uzel v clusteru HDInsight je hostitelem zprostředkovatele Kafka.

Pokud chcete uložit záznamy do dříve vytvořeného tématu test a pak je načíst pomocí konzumenta, použijte následující postup:

1. Pokud chcete zapsat záznamy do tématu, použijte nástroj `kafka-console-producer.sh` z připojení SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test
    ```

    Po tomto příkazu přejdete na prázdný řádek.

1. Na prázdný řádek zadejte textovou zprávu a stiskněte Enter. Tímto způsobem zadejte několik zpráv a pak se pomocí klávesové zkratky **Ctrl + C** vraťte na normální příkazový řádek. Každý řádek se odešle do tématu Kafka jako samostatný záznam.

1. Pokud chcete číst záznamy z tématu, použijte nástroj `kafka-console-consumer.sh` z připojení SSH:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```

    Tento příkaz načte záznamy z tématu a zobrazí je. Parametr `--from-beginning` způsobí, že konzument začne načítat od začátku datového proudu a zpracuje tak všechny záznamy.

    Pokud používáte starší verzi Kafky, nahraďte ji `--bootstrap-server $KAFKABROKERS` `--zookeeper $KAFKAZKHOSTS`.

1. Konzumenta zastavíte stisknutím __Ctrl+C__.

Můžete také programově vytvořit producenty a spotřebitele. Příklad použití tohoto rozhraní API najdete v dokumentu [Apache Kafka Producer and Consumer API s HDInsight.](apache-kafka-producer-consumer-api.md)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení rychlého startu můžete cluster odstranit. S HDInsight, vaše data jsou uloženy ve službě Azure Storage, takže můžete bezpečně odstranit clusteru, když není v provozu. Účtuje se vám také cluster HDInsight, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster jsou mnohonásobně vyšší než poplatky za úložiště, má ekonomické smysl odstranit clustery, když nejsou používány.

Na webu Azure navigujte do svého clusteru a vyberte **Odstranit**.

![Šablona Správce prostředků HBase](./media/apache-kafka-quickstart-resource-manager-template/azure-portal-delete-kafka.png)

Můžete také výběrem názvu skupiny prostředků otevřít stránku skupiny prostředků a pak vybrat **Odstranit skupinu prostředků**. Odstraněním skupiny prostředků odstraníte cluster HDInsight i výchozí účet úložiště.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak vytvořit cluster Apache Kafka v HDInsight pomocí šablony Správce prostředků. V dalším článku se dozvíte, jak vytvořit aplikaci, která používá Apache Kafka Streams API a spustit jej s Kafka na HDInsight.

> [!div class="nextstepaction"]
> [Použití rozhraní API streamů Apache Kafka v Azure HDInsight](./apache-kafka-streams-api.md)
