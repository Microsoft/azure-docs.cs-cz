---
title: Vytváření clusterů Apache Hadoop pomocí rozhraní příkazového příkazu Azure – Azure HDInsight
description: Zjistěte, jak vytvořit clustery Azure HDInsight pomocí rozhraní příkazového příkazu Azure pro různé platformy.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/03/2020
ms.openlocfilehash: b9d935e72c67b78484337e39e0897d4962340636
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77199037"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Vytváření clusterů HDInsight pomocí rozhraní příkazového příkazu Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Kroky v tomto dokumentu návod k vytvoření clusteru HDInsight 3.6 pomocí rozhraní příkazového příkazového příkazu Konazure.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Azure CLI. Pokud jste nenainstalovali azure cli, najdete [v tématu instalace azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli) pro kroky.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-cluster"></a>Vytvoření clusteru

1. Přihlaste se k předplatnému Azure. Pokud máte v plánu používat Azure Cloud Shell, pak vyberte **Try it** v pravém horním rohu bloku kódu. Jinak zadejte následující příkaz:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Nastavte proměnné prostředí. Použití proměnných v tomto článku je založena na Bash. Pro jiná prostředí budou zapotřebí drobné odchylky. Úplný seznam možných parametrů pro vytvoření clusteru najdete v tématu [az-hdinsight-create.](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create)

    |Parametr | Popis |
    |---|---|
    |`--workernode-count`| Počet pracovních uzlů v clusteru. Tento článek používá `clusterSizeInNodes` proměnnou jako `--workernode-count`hodnotu předanou . |
    |`--version`| Verze clusteru HDInsight. Tento článek používá `clusterVersion` proměnnou jako `--version`hodnotu předanou . Viz také: [Podporované verze HDInsight](./hdinsight-component-versioning.md#supported-hdinsight-versions).|
    |`--type`| Typ clusteru HDInsight, jako jsou: hadoop, interactivehive, hbase, kafka, storm, spark, rserver, mlservices.  Tento článek používá `clusterType` proměnnou jako `--type`hodnotu předanou . Viz také: [Typy clusterů a konfigurace](./hdinsight-hadoop-provision-linux-clusters.md#cluster-type).|
    |`--component-version`|Verze různých komponent Hadoop, v prostorově oddělených verzích ve formátu 'component=version'. Tento článek používá `componentVersion` proměnnou jako `--component-version`hodnotu předanou . Viz také: [Hadoop komponenty](./hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions).|

    `RESOURCEGROUPNAME`Nahraďte `CLUSTERNAME` `STORAGEACCOUNTNAME`, `LOCATION` `PASSWORD` , , , a s požadovanými hodnotami. Podle potřeby změňte hodnoty pro ostatní proměnné. Pak zadejte příkazy příkazu příkazu k příkazu.

    ```azurecli-interactive
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
    export httpCredential='PASSWORD'
    export sshCredentials='PASSWORD'

    export AZURE_STORAGE_CONTAINER=$clusterName
    export clusterSizeInNodes=1
    export clusterVersion=3.6
    export clusterType=hadoop
    export componentVersion=Hadoop=2.7
    ```

3. [Vytvořte skupinu prostředků](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) zadáním následujícího příkazu:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    Pro seznam platných umístění `az account list-locations` použijte příkaz a potom použijte jedno `name` z umístění z hodnoty.

4. [Vytvořte si účet Azure Storage](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) zadáním následujícího příkazu:

    ```azurecli-interactive
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. [Extrahujte primární klíč z účtu Azure Storage](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list) a uložte ho do proměnné zadáním následujícího příkazu:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. [Vytvořte kontejner úložiště Azure](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) zadáním následujícího příkazu:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. [Vytvořte cluster HDInsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) zadáním následujícího příkazu:

    ```azurecli-interactive
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpCredential \
        --http-user admin \
        --location $location \
        --workernode-count $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

    > [!IMPORTANT]  
    > Clustery HDInsight se domýšlet v různých typech, které odpovídají zatížení nebo technologii, pro kterou je cluster naladěn. Neexistuje žádná podporovaná metoda pro vytvoření clusteru, který kombinuje více typů, jako je storm a HBase v jednom clusteru.

    Dokončení procesu vytváření clusteru může trvat několik minut. Obvykle kolem 15.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile budete s článkem hotovi, můžete cluster odstranit. S HDInsight, vaše data jsou uloženy ve službě Azure Storage, takže můžete bezpečně odstranit clusteru, když není v provozu. Účtuje se vám také cluster HDInsight, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster jsou mnohonásobně vyšší než poplatky za úložiště, má ekonomické smysl odstranit clustery, když nejsou používány.

Chcete-li odebrat prostředky, zadejte všechny nebo některé z následujících příkazů:

```azurecli-interactive
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $AZURE_STORAGE_ACCOUNT \
    --name $AZURE_STORAGE_CONTAINER

# Remove storage account
az storage account delete \
    --name $AZURE_STORAGE_ACCOUNT \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="troubleshoot"></a>Řešení potíží

Pokud narazíte na problémy s vytvářením clusterů HDInsight, podívejte se na [požadavky na řízení přístupu](./hdinsight-hadoop-customize-cluster-linux.md#access-control).

## <a name="next-steps"></a>Další kroky

Teď, když jste úspěšně vytvořili cluster HDInsight pomocí rozhraní příkazového příkazu k řešení Azure, zjistěte, jak s clusterem pracovat, následující:

### <a name="apache-hadoop-clusters"></a>Clustery Apache Hadoop

* [Použití Apache Hive s HDInsight](hadoop/hdinsight-use-hive.md)
* [Použití mapreduce s HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Clustery Apache HBase

* [Začínáme s Apache HBase na HDInsightu](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Vývoj java aplikací pro Apache HBase na HDInsightu](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Clustery Apache Storm

* [Vývoj java topologie pro Apache Storm na HDInsight](storm/apache-storm-develop-java-topology.md)
* [Použití komponent Pythonu v Apache Storm na HDInsight](storm/apache-storm-develop-python-topology.md)
* [Nasazení a sledování topologie pomocí Apache Storm na HDInsightu](storm/apache-storm-deploy-monitor-topology-linux.md)
