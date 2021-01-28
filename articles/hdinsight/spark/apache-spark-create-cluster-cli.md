---
title: 'Rychlý Start: Apache Spark clusterů pomocí Azure CLI – Azure HDInsight'
description: V tomto rychlém startu se dozvíte, jak pomocí Azure CLI vytvořit cluster Apache Spark ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: quickstart
ms.date: 02/03/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: db7e6b8fb6e70d6a64e84da29bbb0a299ed8da2a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98940547"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-cli"></a>Rychlý Start: Vytvoření clusteru Apache Spark ve službě Azure HDInsight pomocí rozhraní příkazového řádku Azure

V tomto rychlém startu se dozvíte, jak vytvořit cluster Apache Spark v Azure HDInsight pomocí rozhraní příkazového řádku (CLI) Azure. Azure HDInsight je spravovaná opensourcová analytická služba určená pro podniky. Rozhraní Apache Spark Framework for HDInsight umožňuje rychlé analýzy dat a výpočetní výkon clusteru pomocí zpracování v paměti. Azure CLI je nové víceplatformové prostředí příkazového řádku Microsoftu pro správu prostředků Azure.

Pokud používáte více clusterů společně, budete chtít vytvořit virtuální síť a pokud používáte cluster Spark, budete také chtít použít konektor pro skladiště z podregistru. Další informace najdete v tématu [plánování virtuální sítě pro Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md) a [integrace Apache Spark a Apache Hive pomocí konektoru skladu s podregistru](../interactive-query/apache-hive-warehouse-connector.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-an-apache-spark-cluster"></a>Vytvoření clusteru Apache Spark

1. Přihlaste se ke svému předplatnému Azure. Pokud plánujete použít Azure Cloud Shell, vyberte **vyzkoušet** v pravém horním rohu následujícího bloku kódu. V opačném případě zadejte následující příkaz:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Nastavte proměnné prostředí. Použití proměnných v tomto rychlém startu je založené na bash. Pro ostatní prostředí se budou potřebovat mírné variace. V následujícím fragmentu kódu nahraďte RESOURCEGROUPNAME, LOCATION, název_clusteru, STORAGEACCOUNTNAME a PASSWORD požadovanými hodnotami. Pak zadejte příkazy rozhraní příkazového řádku pro nastavení proměnných prostředí.

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
    export clusterType=spark
    export componentVersion=Spark=2.3
    ```

3. Vytvořte skupinu prostředků zadáním následujícího příkazu:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

4. Vytvořte účet úložiště Azure zadáním následujícího příkazu:

    ```azurecli-interactive
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. Extrahujte primární klíč z účtu služby Azure Storage a uložte ho do proměnné zadáním následujícího příkazu:

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. Vytvořte kontejner úložiště Azure zadáním následujícího příkazu:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. Vytvořte cluster Apache Spark zadáním následujícího příkazu:

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

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení rychlého startu možná budete chtít cluster odstranit. Ve službě HDInsight jsou vaše data uložená v Azure Storage, takže můžete cluster bezpečně odstranit, pokud se nepoužívá. Účtují se vám také poplatky za cluster HDInsight, a to i v případě, že se už nepoužívá. Vzhledem k tomu, že se poplatky za cluster mnohokrát účtují rychleji než poplatky za úložiště, má ekonomický smysl odstraňovat clustery, když se nepoužívají.

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

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit cluster Apache Spark v Azure HDInsight pomocí rozhraní příkazového řádku Azure CLI.  Přejděte k dalšímu kurzu, kde se dozvíte, jak používat cluster HDInsight ke spouštění interaktivních dotazů na ukázkových datech.

> [!div class="nextstepaction"]
> [Spouštění interaktivních dotazů na Apache Spark](./apache-spark-load-data-run-query.md)
