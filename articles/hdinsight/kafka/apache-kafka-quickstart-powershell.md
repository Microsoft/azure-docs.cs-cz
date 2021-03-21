---
title: 'Rychlý Start: vytvoření Apache Kafka s využitím Azure PowerShell-HDInsight'
description: V tomto rychlém startu se dozvíte, jak vytvořit cluster Apache Kafka v Azure HDInsight pomocí Azure PowerShellu. Dozvíte se také o tématech, předplatitelích a konzumentech platformy Kafka.
ms.service: hdinsight
ms.custom: mvc
ms.topic: quickstart
ms.date: 06/12/2019
ms.openlocfilehash: f993ffa8d0d141d04ad399c5d1d4f0fc28cc82ac
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102505133"
---
# <a name="quickstart-create-apache-kafka-cluster-in-azure-hdinsight-using-powershell"></a>Rychlý Start: Vytvoření clusteru Apache Kafka ve službě Azure HDInsight pomocí prostředí PowerShell

[Apache Kafka](https://kafka.apache.org/) je open source platforma pro distribuované streamování. Často se používá jako zprostředkovatel zpráv, protože nabízí funkce podobné frontě pro publikování a odběr zpráv. 

V tomto rychlém startu se dozvíte, jak vytvořit cluster [Apache Kafka](https://kafka.apache.org) pomocí Azure PowerShellu. Dozvíte se také, jak používat obsažené nástroje k odesílání a příjmu zpráv pomocí platformy Kafka.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

Rozhraní API Kafka je přístupné jenom pro prostředky ve stejné virtuální síti. V tomto rychlém startu budete ke clusteru přistupovat přímo pomocí SSH. Pokud chcete k platformě Kafka připojit jiné služby, sítě nebo virtuální počítače, musíte nejprve vytvořit virtuální síť a pak v síti vytvořit prostředky. Další informace najdete v dokumentu [Připojení k platformě Apache Kafka pomocí virtuální sítě](apache-kafka-connect-vpn-gateway.md).

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

* Prostředí PowerShell [AZ Module](/powershell/azure/) installed.

* Klient SSH. Další informace najdete v tématu [Připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k předplatnému Azure pomocí `Connect-AzAccount` rutiny a postupujte podle pokynů na obrazovce.

```azurepowershell-interactive
# Login to your Azure subscription
$sub = Get-AzSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Connect-AzAccount
}

# If you have multiple subscriptions, set the one to use
# Select-AzSubscription -SubscriptionId "<SUBSCRIPTIONID>"
```

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Tento příklad vás vyzve k zadání názvu a umístění a pak vytvoří novou skupinu prostředků:

```azurepowershell-interactive
$resourceGroup = Read-Host -Prompt "Enter the resource group name"
$location = Read-Host -Prompt "Enter the Azure region to use"

New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>Vytvoření účtu úložiště

I když Kafka v HDInsight ukládá data Kafka pomocí Spravovaných disků Azure, cluster používá i Azure Storage k ukládání informací, jako jsou protokoly. Pomocí [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) vytvořte nový účet úložiště.

> [!IMPORTANT]  
> Druh účtu úložiště `BlobStorage` se dá použít jenom jako sekundární úložiště pro clustery HDInsight.

```azurepowershell-interactive
$storageName = Read-Host -Prompt "Enter the storage account name"

New-AzStorageAccount `
    -ResourceGroupName $resourceGroup `
    -Name $storageName `
    -Location $location `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableHttpsTrafficOnly 1
```

HDInsight ukládá data v účtu úložiště v kontejneru objektů blob. Pomocí [New-AzStorageContainer](/powershell/module/Az.Storage/New-AzStorageContainer) vytvořte nový kontejner.

```azurepowershell-interactive
$containerName = Read-Host -Prompt "Enter the container name"

$storageKey = (Get-AzStorageAccountKey `
                -ResourceGroupName $resourceGroup `
                -Name $storageName)[0].Value
$storageContext = New-AzStorageContext `
                    -StorageAccountName $storageName `
                    -StorageAccountKey $storageKey

New-AzStorageContainer -Name $containerName -Context $storageContext
```

## <a name="create-an-apache-kafka-cluster"></a>Vytvoření clusteru Apache Kafka

Vytvořte Apache Kafka v clusteru HDInsight pomocí [New-AzHDInsightCluster](/powershell/module/az.HDInsight/New-azHDInsightCluster).

```azurepowershell-interactive
# Create a Kafka 1.1 cluster
$clusterName = Read-Host -Prompt "Enter the name of the Kafka cluster"
$httpCredential = Get-Credential -Message "Enter the cluster login credentials" -UserName "admin"
$sshCredentials = Get-Credential -Message "Enter the SSH user credentials" -UserName "sshuser"

$numberOfWorkerNodes = "4"
$clusterVersion = "3.6"
$clusterType="Kafka"
$disksPerNode=2

$kafkaConfig = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
$kafkaConfig.Add("kafka", "1.1")

New-AzHDInsightCluster `
        -ResourceGroupName $resourceGroup `
        -ClusterName $clusterName `
        -Location $location `
        -ClusterSizeInNodes $numberOfWorkerNodes `
        -ClusterType $clusterType `
        -OSType "Linux" `
        -Version $clusterVersion `
        -ComponentVersion $kafkaConfig `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$storageName.blob.core.windows.net" `
        -DefaultStorageAccountKey $storageKey `
        -DefaultStorageContainer $clusterName `
        -SshCredential $sshCredentials `
        -DisksPerWorkerNode $disksPerNode
```

Vytvoření clusteru HDInsight může trvat až 20 minut.

Parametr `-DisksPerWorkerNode` konfiguruje škálovatelnost platformy Kafka v HDInsight. Kafka ve službě HDInsight používá k ukládání dat místní disky virtuálních počítačů v clusteru. Platforma Kafka je náročná na vstupně-výstupní operace, proto se k zajištění vysoké propustnosti a vyšší kapacity úložiště na každý uzel využívají [Spravované disky Azure](../../virtual-machines/managed-disks-overview.md).

Typ spravovaného disku může být buď __Standardní__ (HDD), nebo __Prémiový__ (SSD). Typ disku závisí na velikosti virtuálního počítače používaného pracovními uzly (zprostředkovateli Kafka). U virtuálních počítačů řady DS a GS se automaticky používají disky Premium. Všechny ostatní typy virtuálních počítačů používají standardní disky. Typ virtuálního počítače můžete nastavit pomocí parametru `-WorkerNodeSize`. Další informace o parametrech najdete v dokumentaci k [New-AzHDInsightCluster](/powershell/module/az.HDInsight/New-azHDInsightCluster) .

Pokud hodláte použít víc než 32 pracovních uzlů (při vytvoření clusteru nebo škálováním clusteru po vytvoření), musíte pomocí parametru `-HeadNodeSize` určit velikost virtuálního počítače s aspoň 8 jádry a 14 GB RAM. Další informace o velikostech uzlů a souvisejících nákladech najdete v [cenách pro HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="connect-to-the-cluster"></a>Připojení ke clusteru

1. Pro připojení k primárnímu hlavnímu uzlu clusteru Kafka použijte následující příkaz. Nahraďte `sshuser` uživatelským jménem SSH. Nahraďte `mykafka` názvem clusteru Kafka.

    ```bash
    ssh sshuser@mykafka-ssh.azurehdinsight.net
    ```

2. Když se ke clusteru poprvé připojíte, ve vašem klientovi SSH se může zobrazit upozornění na nemožnost potvrzení pravosti hostitele. Po zobrazení výzvy zadejte __yes__ (ano) a pak stisknutím klávesy __Enter__ přidejte hostitele na seznam důvěryhodných serverů vašeho klienta SSH.

3. Po zobrazení výzvy zadejte heslo uživatele SSH.

Po připojení se zobrazí informace podobné tomuto textu:

```output
Authorized uses only. All activity may be monitored and reported.
Welcome to Ubuntu 16.04.4 LTS (GNU/Linux 4.13.0-1011-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    https://www.ubuntu.com/business/services/cloud

83 packages can be updated.
37 updates are security updates.



Welcome to Kafka on HDInsight.

Last login: Thu Mar 29 13:25:27 2018 from 108.252.109.241
```

## <a name="get-the-apache-zookeeper-and-broker-host-information"></a><a id="getkafkainfo"></a>Získání informací o hostiteli Apache Zookeeper a Broker

Při práci s Kafka je nutné znát hostitele *Apache Zookeeper* a *Broker* . Tito hostitelé se používají s rozhraním Kafka API s mnohými z nástrojů, které se s tímto systémem dodávají.

V této části získáte informace o hostiteli z REST API Apache Ambari v clusteru.

1. Připojte se ke clusteru přes SSH a následujícím příkazem nainstalujte nástroj `jq`. Tento nástroj slouží k parsování dokumentů JSON a je užitečný při načítání informací o hostiteli:
   
    ```bash
    sudo apt -y install jq
    ```

2. K nastavení proměnné prostředí na název clusteru použijte následující příkaz:

    ```bash
    read -p "Enter the Kafka on HDInsight cluster name: " CLUSTERNAME
    ```

    Po zobrazení výzvy zadejte název clusteru Kafka.

3. K nastavení proměnné prostředí s informacemi o hostiteli Zookeeper použijte následující příkaz. Příkaz načte všechny hostitele Zookeeper a potom vrátí pouze první dvě položky. Je to proto, že chcete určitou redundanci pro případ, že jeden hostitel bude nedosažitelný.

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

    Po zobrazení výzvy zadejte heslo účtu pro přihlášení ke clusteru (ne účtu SSH).

4. Pokud chcete ověřit správné nastavení proměnné prostředí, použijte následující příkaz:

    ```bash
     echo '$KAFKAZKHOSTS='$KAFKAZKHOSTS
    ```

    Tento příkaz by měl vrátit informace podobné následujícímu textu:

    `zk0-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181,zk2-kafka.eahjefxxp1netdbyklgqj5y1ud.ex.internal.cloudapp.net:2181`

5. K nastavení proměnné prostředí s použitím informací o hostiteli zprostředkovatele Kafka použijte následující příkaz:

    ```bash
    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

    Po zobrazení výzvy zadejte heslo účtu pro přihlášení ke clusteru (ne účtu SSH).

6. Pokud chcete ověřit správné nastavení proměnné prostředí, použijte následující příkaz:

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

        Kafka nemá o doménách selhání Azure žádné informace. Při vytváření replik oddílu pro témata se nemusí repliky distribuovat správně z hlediska vysoké dostupnosti.

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

2. Na prázdný řádek zadejte textovou zprávu a stiskněte Enter. Tímto způsobem zadejte několik zpráv a pak se pomocí klávesové zkratky **Ctrl + C** vraťte na normální příkazový řádek. Každý řádek se odešle do tématu Kafka jako samostatný záznam.

3. Pokud chcete číst záznamy z tématu, použijte nástroj `kafka-console-consumer.sh` z připojení SSH:
   
    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic test --from-beginning
    ```
   
    Tento příkaz načte záznamy z tématu a zobrazí je. Parametr `--from-beginning` způsobí, že konzument začne načítat od začátku datového proudu a zpracuje tak všechny záznamy.

    Pokud používáte starší verzi Kafka, nahraďte `--bootstrap-server $KAFKABROKERS` za `--zookeeper $KAFKAZKHOSTS`.

4. Konzumenta zastavíte stisknutím __Ctrl+C__.

Můžete také programově vytvořit producenty a spotřebitele. Příklad použití tohoto rozhraní API najdete v dokumentu [rozhraní API pro Apache Kafka výrobce a příjemce s](apache-kafka-producer-consumer-api.md) využitím služby HDInsight.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, můžete k odebrání skupiny prostředků, HDInsight a všech souvisejících prostředků použít příkaz [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) .

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroup
```

> [!WARNING]  
> Účtování clusteru HDInsight začne vytvořením clusteru a skončí jeho odstraněním. Účtuje se poměrnou částí po minutách, takže byste cluster měli odstranit vždy, když už se nepoužívá.
> 
> Odstraněním clusteru Kafka ve službě HDInsight odstraníte také všechna data uložená v systému Kafka.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Použití Apache Spark s Apache Kafka](../hdinsight-apache-kafka-spark-structured-streaming.md)