---
title: 'Rychlý Start: Apache Kafka pomocí Azure Resource Manager-HDInsight'
description: V tomto rychlém startu se dozvíte, jak vytvořit cluster Apache Kafka v Azure HDInsight pomocí šablony Azure Resource Manager. Dozvíte se také o tématech, předplatitelích a konzumentech platformy Kafka.
ms.service: hdinsight
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 03/13/2020
ms.openlocfilehash: 72a468057cef5c8616cb3f88063204509aaf7fe0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863322"
---
# <a name="quickstart-create-apache-kafka-cluster-in-azure-hdinsight-using-arm-template"></a>Rychlý Start: Vytvoření clusteru Apache Kafka ve službě Azure HDInsight pomocí šablony ARM

V tomto rychlém startu použijete šablonu Azure Resource Manager (šablona ARM) k vytvoření clusteru [Apache Kafka](./apache-kafka-introduction.md) ve službě Azure HDInsight. Kafka je open source distribuovaná streamovací platforma. Často se používá jako zprostředkovatel zpráv, protože nabízí funkce podobné frontě pro publikování a odběr zpráv.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Rozhraní API Kafka je přístupné jenom pro prostředky ve stejné virtuální síti. V tomto rychlém startu budete ke clusteru přistupovat přímo pomocí SSH. Pokud chcete k platformě Kafka připojit jiné služby, sítě nebo virtuální počítače, musíte nejprve vytvořit virtuální síť a pak v síti vytvořit prostředky. Další informace najdete v dokumentu [Připojení k platformě Apache Kafka pomocí virtuální sítě](apache-kafka-connect-vpn-gateway.md).

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[:::image type="icon" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Nasazení do Azure" border="false":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-kafka%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-kafka/).

:::code language="json" source="~/quickstart-templates/101-hdinsight-kafka/azuredeploy.json":::

V šabloně jsou definované dva prostředky Azure:

* [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts): vytvořte účet Azure Storage.
* [Microsoft. HDInsight/cluster](/azure/templates/microsoft.hdinsight/clusters): Vytvořte cluster HDInsight.

## <a name="deploy-the-template"></a>Nasazení šablony

1. Kliknutím na tlačítko **nasadit do Azure** níže se přihlaste k Azure a otevřete šablonu ARM.

   [:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Nasazení do Azure" border="false":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-kafka%2Fazuredeploy.json)

1. Zadejte nebo vyberte tyto hodnoty:

    |Vlastnost |Popis |
    |---|---|
    |Předplatné|V rozevíracím seznamu vyberte předplatné Azure, které se používá pro cluster.|
    |Skupina prostředků|V rozevíracím seznamu vyberte existující skupinu prostředků nebo vyberte **vytvořit novou**.|
    |Umístění|Hodnota bude automaticky vyplněna umístěním použitým pro skupinu prostředků.|
    |Název clusteru|Zadejte globálně jedinečný název. Pro tuto šablonu použijte jenom malá písmena a čísla.|
    |Uživatelské jméno přihlášení clusteru|Zadejte uživatelské jméno, výchozí nastavení je **admin**.|
    |Heslo přihlášení clusteru|Zadejte heslo. Heslo musí mít minimálně 10 znaků a musí obsahovat aspoň jedno číslo, jedno velké písmeno a jedno malé písmeno, jeden jiný než alfanumerický znak (kromě znaků). |
    |Uživatelské jméno SSH|Zadejte uživatelské jméno, výchozí hodnota je **sshuser**|
    |Heslo SSH|Zadejte heslo.|

    :::image type="content" source="./media/apache-kafka-quickstart-resource-manager-template/resource-manager-template-kafka.png" alt-text="Snímek obrazovky vlastností šablony" border="false":::

1. Přečtěte si podmínky **a ujednání**. Pak vyberte Souhlasím **s výše uvedenými podmínkami a ujednáními a** pak na **koupit**. Obdržíte oznámení, že vaše nasazení probíhá. Vytvoření clusteru trvá přibližně 20 minut.

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Po vytvoření clusteru obdržíte oznámení o **úspěšném nasazení** s odkazem **na prostředek přejít na prostředek** . Na stránce skupiny prostředků se zobrazí seznam nového clusteru HDInsight a výchozí úložiště přidružené ke clusteru. Každý cluster má účet [Azure Blob Storage](../hdinsight-hadoop-use-blob-storage.md) , [Azure Data Lake Storage Gen1](../hdinsight-hadoop-use-data-lake-storage-gen1.md)nebo  [`Azure Data Lake Storage Gen2`](../hdinsight-hadoop-use-data-lake-storage-gen2.md) závislost. Označuje se jako výchozí účet úložiště. Cluster HDInsight a jeho výchozí účet úložiště musí být společně umístěné ve stejné oblasti Azure. Odstraněním clusterů nedojde k odstranění účtu úložiště.

## <a name="get-the-apache-zookeeper-and-broker-host-information"></a>Získání informací o hostiteli Apache Zookeeper a Broker

Při práci s Kafka je nutné znát hostitele *Apache Zookeeper* a *Broker* . Tito hostitelé se používají s rozhraním Kafka API s mnohými z nástrojů, které se s tímto systémem dodávají.

V této části získáte informace o hostiteli z rozhraní REST API Ambari v clusteru.

1. Připojte se ke clusteru pomocí [příkazu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) . Níže uvedený příkaz upravte tak, že ho nahradíte názvem clusteru a pak zadáte tento příkaz:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Z připojení SSH pomocí následujícího příkazu nainstalujte `jq` nástroj. Tento nástroj slouží k parsování dokumentů JSON a je užitečný při načítání informací o hostiteli:

    ```bash
    sudo apt -y install jq
    ```

1. K nastavení proměnné prostředí na název clusteru použijte následující příkaz:

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

    Po zobrazení výzvy zadejte název clusteru Kafka.

1. K nastavení proměnné prostředí s informacemi o hostiteli Zookeeper použijte následující příkaz. Příkaz načte všechny hostitele Zookeeper a potom vrátí pouze první dvě položky. Je to proto, že chcete určitou redundanci pro případ, že jeden hostitel bude nedosažitelný.

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
        
        Informace o počtu domén selhání v oblasti najdete v dokumentu popisujícím [dostupnost Linuxových virtuálních počítačů](../../virtual-machines/availability.md).

        Kafka neví o doménách selhání Azure. Při vytváření replik oddílu pro témata se nemusí repliky distribuovat správně z hlediska vysoké dostupnosti.

        Pokud chcete zajistit vysokou dostupnost, použijte [Nástroj pro obnovení rovnováhy oddílu Apache Kafka](https://github.com/hdinsight/hdinsight-kafka-tools). Tento nástroj se musí spustit z připojení SSH k hlavnímu uzlu clusteru Kafka.

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

    Pokud používáte starší verzi Kafka, nahraďte `--bootstrap-server $KAFKABROKERS` `--zookeeper $KAFKAZKHOSTS` .

1. Konzumenta zastavíte stisknutím __Ctrl+C__.

Můžete také programově vytvořit producenty a spotřebitele. Příklad použití tohoto rozhraní API najdete v dokumentu [rozhraní API pro Apache Kafka výrobce a příjemce s](apache-kafka-producer-consumer-api.md) využitím služby HDInsight.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení rychlého startu možná budete chtít cluster odstranit. Ve službě HDInsight jsou vaše data uložená v Azure Storage, takže můžete cluster bezpečně odstranit, pokud se nepoužívá. Účtují se vám také poplatky za cluster HDInsight, a to i v případě, že se už nepoužívá. Vzhledem k tomu, že se poplatky za cluster mnohokrát účtují rychleji než poplatky za úložiště, má ekonomický smysl odstraňovat clustery, když se nepoužívají.

Z Azure Portal přejděte do svého clusteru a vyberte **Odstranit**.

:::image type="content" source="./media/apache-kafka-quickstart-resource-manager-template/azure-portal-delete-kafka.png" alt-text="Správce prostředků šablony HBA" border="false":::

Můžete také výběrem názvu skupiny prostředků otevřít stránku skupiny prostředků a pak vybrat **Odstranit skupinu prostředků**. Odstraněním skupiny prostředků odstraníte cluster HDInsight i výchozí účet úložiště.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit cluster Apache Kafka v HDInsight pomocí šablony ARM. V dalším článku se dozvíte, jak vytvořit aplikaci, která používá rozhraní API služby Apache Kafka Streams a jak ji spustit s Kafka ve službě HDInsight.

> [!div class="nextstepaction"]
> [Použití rozhraní API datových proudů Apache Kafka ve službě Azure HDInsight](./apache-kafka-streams-api.md)