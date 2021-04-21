---
title: 'Kurz: Vytvoření clusteru s povoleným Apache Kafka proxy REST v HDInsight pomocí Azure CLI'
description: Naučte se provádět operace Apache Kafka pomocí proxy serveru REST Kafka ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: tutorial
ms.date: 02/27/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: cfb9ff9e6b107c9da84b164a055453994fc29229
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786634"
---
# <a name="tutorial-create-an-apache-kafka-rest-proxy-enabled-cluster-in-hdinsight-using-azure-cli"></a>Kurz: Vytvoření clusteru s povoleným Apache Kafka proxy REST v HDInsight pomocí Azure CLI

V tomto kurzu se naučíte, jak vytvořit cluster Apache Kafka s [podporou proxy REST](./rest-proxy.md) v Azure HDInsight pomocí rozhraní příkazového řádku (CLI) Azure. Azure HDInsight je spravovaná opensourcová analytická služba určená pro podniky. Apache Kafka je open source distribuovaná streamovací platforma. Často se používá jako zprostředkovatel zpráv, protože nabízí funkce podobné frontě pro publikování a odběr zpráv. Kafka REST proxy umožňuje komunikovat s clusterem Kafka prostřednictvím [REST API](/rest/api/hdinsight-kafka-rest-proxy/) přes protokol HTTP. Azure CLI je nové víceplatformové prostředí příkazového řádku Microsoftu pro správu prostředků Azure.

Rozhraní Apache Kafka API je přístupné jenom pro prostředky ve stejné virtuální síti. Ke clusteru můžete přistupovat přímo pomocí SSH. Pokud chcete k platformě Apache Kafka připojit jiné služby, sítě nebo virtuální počítače, musíte nejprve vytvořit virtuální síť a pak v síti vytvořit prostředky. Další informace najdete v tématu [připojení k Apache Kafka pomocí virtuální sítě](./apache-kafka-connect-vpn-gateway.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Předpoklady pro proxy Kafka REST
> * Vytvoření clusteru Apache Kafka pomocí Azure CLI

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

* Aplikace zaregistrovaná ve službě Azure AD. Klientské aplikace, které napíšete pro interakci s proxy Kafka REST, budou k ověření v Azure používat ID a tajný kód této aplikace. Další informace najdete v tématu [Registrace aplikace s platformou Microsoft Identity](../../active-directory/develop/quickstart-register-app.md).

* Skupina zabezpečení Azure AD s vaší registrovanou aplikací jako členem. Tato skupina zabezpečení se použije k určení, které aplikace můžou pracovat s proxy REST. Další informace o vytváření skupin Azure AD najdete v tématu [Vytvoření základní skupiny a přidání členů pomocí Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

* Azure CLI Ujistěte se, že máte minimálně verzi 2.0.79. Viz [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-an-apache-kafka-cluster"></a>Vytvoření clusteru Apache Kafka

1. Přihlaste se ke svému předplatnému Azure.

    ```azurecli
    az login
    
    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Nastavte proměnné prostředí. Použití proměnných v tomto kurzu vychází z bash. Pro ostatní prostředí se budou potřebovat mírné variace.

    |Proměnná | Popis |
    |---|---|
    |resourceGroupName|Nahraďte RESOURCEGROUPNAME názvem nové skupiny prostředků.|
    |location|MÍSTO pro umístění nahraďte oblastí, ve které se cluster vytvoří. Seznam platných umístění získáte pomocí `az account list-locations` příkazu.|
    |clusterName|Parametr název_clusteru nahraďte globálně jedinečným názvem pro nový cluster.|
    |storageAccount|Nahraďte STORAGEACCOUNTNAME názvem nového účtu úložiště.|
    |httpPassword|HESLO pro přihlášení ke clusteru a **správce** nahraďte heslem.|
    |sshPassword|Heslo nahraďte heslem pro uživatelské jméno zabezpečeného prostředí **sshuser**.|
    |securityGroupName|Nahraďte SECURITYGROUPNAME názvem skupiny zabezpečení AAD klienta pro proxy Kafka REST. Proměnná bude předána `--kafka-client-group-name` parametru pro `az-hdinsight-create` .|
    |securityGroupID|Nahraďte SECURITYGROUPID ID skupiny zabezpečení AAD klienta pro proxy REST Kafka. Proměnná bude předána `--kafka-client-group-id` parametru pro `az-hdinsight-create` .|
    |storageContainer|Kontejner úložiště, který bude cluster používat, ponechte ho pro tento kurz. Tato proměnná se nastaví s názvem clusteru.|
    |workernodeCount|Počet uzlů pracovního procesu v clusteru, ponechte to pro účely tohoto kurzu. Kafka vyžaduje minimálně 3 pracovní uzly, aby se zajistila vysoká dostupnost.|
    |clusterType|Typ clusteru HDInsight, ponechte to pro účely tohoto kurzu.|
    |clusterVersion|Verze clusteru HDInsight, ponechte to pro tento kurz. Kafka REST proxy vyžaduje minimální verzi clusteru 4,0.|
    |componentVersion|Kafka verze, ponechte to pro tento kurz. Kafka REST proxy vyžaduje minimální verzi součásti 2,1.|

    Aktualizujte proměnné s požadovanými hodnotami. Pak zadejte příkazy rozhraní příkazového řádku pro nastavení proměnných prostředí.

    ```azurecli
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export storageAccount=STORAGEACCOUNTNAME
    export httpPassword='PASSWORD'
    export sshPassword='PASSWORD'
    export securityGroupName=SECURITYGROUPNAME
    export securityGroupID=SECURITYGROUPID

    export storageContainer=$(echo $clusterName | tr "[:upper:]" "[:lower:]")
    export workernodeCount=3
    export clusterType=kafka
    export clusterVersion=4.0
    export componentVersion=kafka=2.1
    ```

1. [Vytvořte skupinu prostředků](/cli/azure/group#az_group_create) zadáním následujícího příkazu:

    ```azurecli
     az group create \
        --location $location \
        --name $resourceGroupName
    ```

1. [Vytvořte účet Azure Storage](/cli/azure/storage/account#az_storage_account_create) zadáním následujícího příkazu:

    ```azurecli
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $storageAccount \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

1. [Extrahujte primární klíč](/cli/azure/storage/account/keys#az_storage_account_keys_list) z účtu Azure Storage a uložte ho do proměnné zadáním následujícího příkazu:

    ```azurecli
    export storageAccountKey=$(az storage account keys list \
        --account-name $storageAccount \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

1. [Vytvořte kontejner Azure Storage](/cli/azure/storage/container#az_storage_container_create) zadáním následujícího příkazu:

    ```azurecli
    az storage container create \
        --name $storageContainer \
        --account-key $storageAccountKey \
        --account-name $storageAccount
    ```

1. [Vytvořte cluster HDInsight](/cli/azure/hdinsight#az_hdinsight_create). Před zadáním příkazu si všimněte následujících parametrů:

    1. Požadované parametry pro clustery Kafka:

        |Parametr | Popis|
        |---|---|
        |--typ|Hodnota musí být **Kafka**.|
        |--workernode-data-disky – na uzel|Počet datových disků, které se mají použít na jeden pracovní uzel. HDInsight Kafka se podporuje jenom s datovými disky. V tomto kurzu se používá hodnota **2**.|

    1. Požadované parametry pro proxy Kafka REST:

        |Parametr | Popis|
        |---|---|
        |--Kafka-Management-Node-Size|Velikost uzlu. V tomto kurzu se používá hodnota **Standard_D4_v2**.|
        |--Kafka-Client-Group-ID|ID skupiny zabezpečení AAD klienta pro proxy Kafka REST Hodnota je předána z proměnné **$securityGroupID**.|
        |--Kafka-Client-Group-Name|Název skupiny zabezpečení AAD klienta pro proxy Kafka REST Hodnota je předána z proměnné **$securityGroupName**.|
        |--verze|Verze clusteru HDInsight musí být minimálně 4,0. Hodnota je předána z proměnné **$clusterVersion**.|
        |--součást-verze|Verze Kafka musí být minimálně 2,1. Hodnota je předána z proměnné **$componentVersion**.|
    
        Pokud chcete cluster vytvořit bez proxy serveru REST, vylučte `--kafka-management-node-size` , `--kafka-client-group-id` a `--kafka-client-group-name` z `az hdinsight create` příkazu.

    1. Pokud máte existující virtuální síť, přidejte parametry `--vnet-name` a a `--subnet` jejich hodnoty.

    Zadejte následující příkaz pro vytvoření clusteru:

    ```azurecli
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpPassword \
        --http-user admin \
        --location $location \
        --ssh-password $sshPassword \
        --ssh-user sshuser \
        --storage-account $storageAccount \
        --storage-account-key $storageAccountKey \
        --storage-container $storageContainer \
        --version $clusterVersion \
        --workernode-count $workernodeCount \
        --workernode-data-disks-per-node 2 \
        --kafka-management-node-size "Standard_D4_v2" \
        --kafka-client-group-id $securityGroupID \
        --kafka-client-group-name "$securityGroupName"
    ```

    Dokončení procesu vytváření clusteru může trvat několik minut. Obvykle přibližně 15.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile budete s článkem hotovi, můžete cluster odstranit. Ve službě HDInsight jsou vaše data uložená v Azure Storage, takže můžete cluster bezpečně odstranit, pokud se nepoužívá. Účtují se vám také poplatky za cluster HDInsight, a to i v případě, že se už nepoužívá. Vzhledem k tomu, že se poplatky za cluster mnohokrát účtují rychleji než poplatky za úložiště, má ekonomický smysl odstraňovat clustery, když se nepoužívají.

Pro odebrání prostředků zadejte všechny nebo některé z následujících příkazů:

```azurecli
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $storageAccount  \
    --name $storageContainer

# Remove storage account
az storage account delete \
    --name $storageAccount  \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="next-steps"></a>Další kroky

Teď, když jste úspěšně vytvořili cluster Apache Kafka s podporou proxy REST v Azure HDInsight pomocí rozhraní příkazového řádku Azure, použijte kód Pythonu pro interakci s proxy REST:

> [!div class="nextstepaction"]
> [Vytvořit ukázkovou aplikaci](./rest-proxy.md#client-application-sample)