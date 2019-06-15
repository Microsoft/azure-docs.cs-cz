---
title: Vytvořte clustery systému Apache Hadoop pomocí rozhraní příkazového řádku Azure – Azure HDInsight
description: Zjistěte, jak vytvářet clustery HDInsight pomocí Azure CLI pro různé platformy.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: hrasheed
ms.openlocfilehash: 0a278cd98b0dd6c6d8f0fe9bfee81e5bafd4f543
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65597670"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Vytvoření clusterů HDInsight pomocí rozhraní příkazového řádku Azure

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Kroky v tomto názorném postupu dokumentu, vytvoření clusteru HDInsight 3.6 pomocí Azure CLI.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Azure CLI. Pokud jste nenainstalovali Azure CLI, přečtěte si téma [instalace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) kroky.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-cluster"></a>Vytvoření clusteru

1. Přihlaste se ke svému předplatnému Azure. Pokud plánujete použít Azure Cloud Shell, pak stačí vybrat **vyzkoušet** v pravém horním rohu bloku kódu. Jinak zadejte následující příkaz:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Nastavení proměnných prostředí. Použití proměnných v tomto článku je založená na prostředí Bash. Malé odchylky bude potřebovat další prostředí. Zobrazit [az hdinsight vytvořit](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) úplný seznam možných parametrů pro vytvoření clusteru.

    |Parametr | Popis |
    |---|---|
    |`--size`| Počet pracovních uzlů v clusteru. Tento článek používá proměnnou `clusterSizeInNodes` jako hodnotu předanou `--size`. |
    |`--version`| Verze clusteru HDInsight. Tento článek používá proměnnou `clusterVersion` jako hodnotu předanou `--version`. Viz také: [Podporované verze HDInsight](./hdinsight-component-versioning.md#supported-hdinsight-versions).|
    |`--type`| Jako typ clusteru HDInsight: hadoop, interactivehive, hbase, kafka, storm, spark, r server, mlservices.  Tento článek používá proměnnou `clusterType` jako hodnotu předanou `--type`. Viz také: [Typy a konfiguraci clusteru](./hdinsight-hadoop-provision-linux-clusters.md#cluster-types).|
    |`--component-version`|Verze různých komponent systému Hadoop v oddělených mezerami verze v "součást verze =" formátu. Tento článek používá proměnnou `componentVersion` jako hodnotu předanou `--component-version`. Viz také: [Součásti platformy Hadoop](./hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions).|

    Nahraďte `RESOURCEGROUPNAME`, `LOCATION`, `CLUSTERNAME`, `STORAGEACCOUNTNAME`, a `PASSWORD` s požadované hodnoty. Změňte hodnoty pro proměnné podle potřeby. Zadejte příkazů rozhraní příkazového řádku.

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

3. [Vytvořte skupinu prostředků](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) tak, že zadáte následující příkaz:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    Seznam platných umístění, použijte `az account list-locations` příkaz a pak použijte jednu z umístění, ze `name` hodnotu.

4. [Vytvoření účtu služby Azure storage](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) tak, že zadáte následující příkaz:

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

5. [Extrahovat primární klíč z účtu úložiště Azure](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list) a uložte ji do proměnné tak, že zadáte následující příkaz:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. [Vytvoření kontejneru služby Azure storage](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) tak, že zadáte následující příkaz:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. [Vytvoření clusteru HDInsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) tak, že zadáte následující příkaz:

    ```azurecli-interactive
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpCredential \
        --http-user admin \
        --location $location \
        --size $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-default-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

    > [!IMPORTANT]  
    > HDInsight clustery se dělí na různé typy, které odpovídají úlohy nebo technologie, která clusteru je vyladěný pro. Neexistuje žádná podporovaná metoda pro vytvoření clusteru, který kombinuje více typů, jako je Storm a HBase na jednom clusteru.

    Může trvat několik minut na dokončení procesu vytváření clusteru. Obvykle přibližně 15.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile budete s článkem hotovi, můžete cluster odstranit. Pomocí HDInsight jsou vaše data uložena v Azure Storage, takže můžete clusteru bezpečně odstranit, pokud není používán. Za cluster služby HDInsight se účtují poplatky, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster představují několikanásobek poplatků za úložiště, dává ekonomický smysl odstraňovat clustery, které nejsou používány.

Zadejte všechny nebo některé z následujících příkazů odebrat prostředky:

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

## <a name="next-steps"></a>Další postup

Teď, když úspěšně vytvoříte clusteru služby HDInsight pomocí Azure CLI, použijte následující postup, jak pracovat s vaším clusterem:

### <a name="apache-hadoop-clusters"></a>Apache Hadoop clusterů

* [Použití Apache Hivu se službou HDInsight](hadoop/hdinsight-use-hive.md)
* [Použití Apache Pig s HDInsight](hadoop/hdinsight-use-pig.md)
* [Použití MapReduce se službou HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Clustery Apache HBase

* [Začínáme s Apache HBase v HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Vývoj aplikace Java pro Apache HBase v HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Clustery Apache Storm

* [Vývoj topologie Java pro Apache Storm v HDInsight](storm/apache-storm-develop-java-topology.md)
* [Použití komponent v Pythonu v Apache Storm v HDInsight](storm/apache-storm-develop-python-topology.md)
* [Nasazení a monitorování topologií pomocí Apache Storm v HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)
