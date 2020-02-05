---
title: 'Rychlý Start: Apache Spark clusterů pomocí Azure CLI – Azure HDInsight'
description: V tomto rychlém startu se dozvíte, jak pomocí Azure CLI vytvořit cluster Apache Spark ve službě Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 02/03/2020
ms.openlocfilehash: ac937519ca3fa9381fe4970f2e4336fe4a0a9227
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989123"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-cli"></a>Rychlý Start: Vytvoření clusteru Apache Spark ve službě Azure HDInsight pomocí rozhraní příkazového řádku Azure

V tomto rychlém startu se dozvíte, jak vytvořit cluster Apache Spark v Azure HDInsight pomocí rozhraní příkazového řádku Azure CLI. Apache Spark umožňuje rychlou analýzu dat a clusterové výpočty s využitím zpracování v paměti. [Rozhraní příkazového řádku Azure (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) je prostředí příkazového řádku pro různé platformy od Microsoftu pro správu prostředků Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Azure CLI. Pokud jste nenainstalovali Azure CLI, přečtěte si téma [instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) pro kroky.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-an-apache-spark-cluster"></a>Vytvoření clusteru Apache Spark

1. Přihlaste se ke svému předplatnému Azure. Pokud plánujete použít Azure Cloud Shell, vyberte **vyzkoušet** v pravém horním rohu bloku kódu. V opačném případě zadejte následující příkaz:

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

V tomto rychlém startu jste zjistili, jak vytvořit cluster Apache Spark v Azure HDInsight pomocí rozhraní příkazového řádku Azure CLI.  V dalším kurzu se dozvíte, jak pomocí clusteru HDInsight Spark spouštět interaktivní dotazy na ukázková data.

> [!div class="nextstepaction"]
> [Spouštění interaktivních dotazů na Apache Spark](./apache-spark-load-data-run-query.md)
