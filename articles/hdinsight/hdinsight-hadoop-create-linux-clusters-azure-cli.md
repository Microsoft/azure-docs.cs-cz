---
title: Vytváření clusterů Apache Hadoop pomocí Azure CLI – Azure HDInsight
description: Naučte se vytvářet clustery Azure HDInsight pomocí Azure CLI pro různé platformy.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli
ms.date: 02/03/2020
ms.openlocfilehash: 9028d85346611341afec0d0598f27a77e4f37fdf
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101715492"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Vytváření clusterů HDInsight pomocí rozhraní příkazového řádku Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Kroky v tomto dokumentu vás provedou vytvořením clusteru HDInsight 3,6 pomocí Azure CLI.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-a-cluster"></a>Vytvoření clusteru

1. Přihlaste se k předplatnému Azure. Pokud plánujete použít Azure Cloud Shell, vyberte **vyzkoušet** v pravém horním rohu bloku kódu. V opačném případě zadejte následující příkaz:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Nastavte proměnné prostředí. Použití proměnných v tomto článku je založené na bash. Pro ostatní prostředí se budou potřebovat mírné variace. Úplný seznam možných parametrů pro vytvoření clusteru najdete v tématu [AZ-HDInsight-Create](/cli/azure/hdinsight#az-hdinsight-create) .

    |Parametr | Popis |
    |---|---|
    |`--workernode-count`| Počet uzlů pracovního procesu v clusteru. V tomto článku se používá proměnná `clusterSizeInNodes` jako hodnota předaná `--workernode-count` . |
    |`--version`| Verze clusteru HDInsight. V tomto článku se používá proměnná `clusterVersion` jako hodnota předaná `--version` . Viz také: [podporované verze HDInsight](./hdinsight-component-versioning.md#supported-hdinsight-versions).|
    |`--type`| Zadejte cluster HDInsight, například: Hadoop, interactivehive, HBA, Kafka,, Spark, Rserver, mlservices.  V tomto článku se používá proměnná `clusterType` jako hodnota předaná `--type` . Viz také: [typy clusterů a konfigurace](./hdinsight-hadoop-provision-linux-clusters.md#cluster-type).|
    |`--component-version`|Verze různých komponent systému Hadoop ve formátu "Component = Version" ve verzích oddělených mezerami. V tomto článku se používá proměnná `componentVersion` jako hodnota předaná `--component-version` . Viz také: [komponenty Hadoop](./hdinsight-component-versioning.md).|

    Nahraďte `RESOURCEGROUPNAME` ,, `LOCATION` `CLUSTERNAME` , `STORAGEACCOUNTNAME` a `PASSWORD` požadovanými hodnotami. Změňte hodnoty pro ostatní proměnné podle potřeby. Pak zadejte příkazy rozhraní příkazového řádku.

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

3. [Vytvořte skupinu prostředků](/cli/azure/group#az-group-create) zadáním následujícího příkazu:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    Chcete-li zobrazit seznam platných umístění, použijte `az account list-locations` příkaz a pak použijte jedno z umístění z `name` hodnoty.

4. [Vytvořte účet Azure Storage](/cli/azure/storage/account#az-storage-account-create) zadáním následujícího příkazu:

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

5. [Extrahujte primární klíč z účtu Azure Storage](/cli/azure/storage/account/keys#az-storage-account-keys-list) a uložte ho do proměnné zadáním následujícího příkazu:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. [Vytvořte kontejner Azure Storage](/cli/azure/storage/container#az-storage-container-create) zadáním následujícího příkazu:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. [Vytvořte cluster HDInsight](/cli/azure/hdinsight#az-hdinsight-create) zadáním následujícího příkazu:

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
    > Clustery HDInsight přicházejí do různých typů, které odpovídají úlohám nebo technologiím, pro které je cluster laděn. Neexistuje žádná podporovaná metoda pro vytvoření clusteru, který kombinuje více typů, jako je například zaplavení a HBA v jednom clusteru.

    Dokončení procesu vytváření clusteru může trvat několik minut. Obvykle přibližně 15.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile budete s článkem hotovi, můžete cluster odstranit. Ve službě HDInsight jsou vaše data uložená v Azure Storage, takže můžete cluster bezpečně odstranit, pokud se nepoužívá. Účtují se vám také poplatky za cluster HDInsight, a to i v případě, že se už nepoužívá. Vzhledem k tomu, že se poplatky za cluster mnohokrát účtují rychleji než poplatky za úložiště, má ekonomický smysl odstraňovat clustery, když se nepoužívají.

Pro odebrání prostředků zadejte všechny nebo některé z následujících příkazů:

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

Teď, když jste úspěšně vytvořili cluster HDInsight pomocí rozhraní příkazového řádku Azure, se naučíte pracovat s clusterem pomocí následujícího postupu:

### <a name="apache-hadoop-clusters"></a>Clustery Apache Hadoop

* [Použití Apache Hive se službou HDInsight](hadoop/hdinsight-use-hive.md)
* [Použití MapReduce se službou HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Clustery Apache HBA

* [Začínáme s Apache HBA v HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Vývoj aplikací Java pro Apache HBA v HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Clustery Apache Storm

* [Vývoj topologií v jazyce Java pro Apache Storm v HDInsight](storm/apache-storm-develop-java-topology.md)
* [Použití součástí Pythonu v Apache Storm ve službě HDInsight](storm/apache-storm-develop-python-topology.md)
* [Nasazení a monitorování topologií pomocí Apache Storm v HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
