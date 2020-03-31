---
title: 'Úvodní příručka: Clustery Apache Spark s rozhraním příkazového příkazu Azure – Azure HDInsight'
description: Tento rychlý start ukazuje, jak pomocí rozhraní příkazového příkazu Azure vytvořit cluster Apache Spark v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 02/03/2020
ms.openlocfilehash: e4679d5a04be7b8c0145fd93818e4187170b4194
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77049684"
---
# <a name="quickstart-create-apache-spark-cluster-in-azure-hdinsight-using-azure-cli"></a>Úvodní příručka: Vytvoření clusteru Apache Spark v Azure HDInsight pomocí rozhraní příkazového příkazu Azure

V tomto rychlém startu se dozvíte, jak vytvořit cluster Apache Spark v Azure HDInsight pomocí rozhraní příkazového řádku Azure (CLI). Azure HDInsight je spravovaná opensourcová analytická služba určená pro podniky. Architektura Apache Spark pro HDInsight umožňuje rychlou analýzu dat a clusterové výpočty pomocí zpracování v paměti. Azure CLI je nové víceplatformové prostředí příkazového řádku Microsoftu pro správu prostředků Azure.

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli), pokud nechcete používat Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-an-apache-spark-cluster"></a>Vytvoření clusteru Apache Spark

1. Přihlaste se ke svému předplatnému Azure. Pokud máte v plánu používat Azure Cloud Shell, vyberte **Vyzkoušet** v pravém horním rohu následujícího bloku kódu. Jinak zadejte následující příkaz:

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. Nastavte proměnné prostředí. Použití proměnných v tomto rychlém startu je založeno na Bash. Pro jiná prostředí budou zapotřebí drobné odchylky. Nahraďte název RESOURCEGROUPNAME, UMÍSTĚNÍ, NÁZEV_CLUSTERNAME, STORAGEACCOUNTNAME a HESLO Ve fragmentu kódu níže požadované hodnoty. Poté zadejte příkazy příkazu příkazu k příkazu pro nastavení proměnných prostředí.

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

4. Vytvořte si účet úložiště Azure zadáním následujícího příkazu:

    ```azurecli-interactive
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. Extrahujte primární klíč z účtu úložiště Azure a uložte ho do proměnné zadáním následujícího příkazu:

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

Po dokončení rychlého startu můžete cluster odstranit. S HDInsight, vaše data jsou uloženy ve službě Azure Storage, takže můžete bezpečně odstranit clusteru, když není v provozu. Účtuje se vám také cluster HDInsight, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster jsou mnohonásobně vyšší než poplatky za úložiště, má ekonomické smysl odstranit clustery, když nejsou používány.

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

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak vytvořit cluster Apache Spark v Azure HDInsight pomocí Rozhraní příkazového příkazového příkazu Azure.  Přejdete k dalšímu kurzu, kde se dozvíte, jak pomocí clusteru HDInsight spouštět interaktivní dotazy na ukázková data.

> [!div class="nextstepaction"]
> [Spouštění interaktivních dotazů na Apache Spark](./apache-spark-load-data-run-query.md)
