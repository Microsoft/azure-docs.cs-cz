---
title: 'Kurz: Vytvoření clusteru s povoleným proxy serverem Apache Kafka REST ve službě HDInsight pomocí rozhraní příkazového příkazového příkazu Azure'
description: Zjistěte, jak provádět operace Apache Kafka pomocí proxy Kafka REST na Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: tutorial
ms.date: 02/27/2020
ms.openlocfilehash: 6ba5e433839d1f27c9522749fd7a8831c7243aae
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78201882"
---
# <a name="tutorial-create-an-apache-kafka-rest-proxy-enabled-cluster-in-hdinsight-using-azure-cli"></a>Kurz: Vytvoření clusteru s povoleným proxy serverem Apache Kafka REST ve službě HDInsight pomocí rozhraní příkazového příkazového příkazu Azure

V tomto kurzu se dozvíte, jak vytvořit apache kafka [rest proxy povolil](./rest-proxy.md) cluster u Azure HDInsight pomocí rozhraní příkazového řádku Azure (CLI). Azure HDInsight je spravovaná opensourcová analytická služba určená pro podniky. Apache Kafka je open source distribuovaná streamovací platforma. Často se používá jako zprostředkovatel zpráv, protože nabízí funkce podobné frontě pro publikování a odběr zpráv. Kafka REST Proxy umožňuje interakci s clusterem Kafka prostřednictvím [rozhraní REST API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/) přes HTTP. Azure CLI je nové víceplatformové prostředí příkazového řádku Microsoftu pro správu prostředků Azure.

Rozhraní Apache Kafka API je přístupné jenom pro prostředky ve stejné virtuální síti. Ke clusteru můžete přistupovat přímo pomocí SSH. Pokud chcete k platformě Apache Kafka připojit jiné služby, sítě nebo virtuální počítače, musíte nejprve vytvořit virtuální síť a pak v síti vytvořit prostředky. Další informace najdete [v tématu Připojení k Apache Kafka pomocí virtuální sítě](./apache-kafka-connect-vpn-gateway.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Předpoklady pro proxy Kafka REST
> * Vytvoření clusteru Apache Kafka pomocí azure CLI

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

* Aplikace registrovaná ve službě Azure AD. Klientské aplikace, které zapisujete pro interakci s proxy kafka REST bude používat id této aplikace a tajný klíč k ověření azure. Další informace naleznete [v tématu Registrace aplikace s platformou identit společnosti Microsoft](../../active-directory/develop/quickstart-register-app.md).

* Skupina zabezpečení Azure AD s registrovanou aplikací jako členem. Tato skupina zabezpečení bude použita k řízení, které aplikace mohou pracovat s proxy serverem REST. Další informace o vytváření skupin Azure AD najdete [v tématu Vytvoření základní skupiny a přidání členů pomocí služby Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

* Azure CLI. Ujistěte se, že máte alespoň verzi 2.0.79. Viz [Instalace příkazového příkazového příkazu k Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-an-apache-kafka-cluster"></a>Vytvoření clusteru Apache Kafka

1. Přihlaste se ke svému předplatnému Azure.

    ```azurecli
    az login
    
    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. Nastavte proměnné prostředí. Použití proměnných v tomto kurzu je založeno na Bash. Pro jiná prostředí budou zapotřebí drobné odchylky.

    |Proměnná | Popis |
    |---|---|
    |resourceGroupName|Nahraďte název resourcegroupname názvem nové skupiny prostředků.|
    |location|Nahraďte umístění oblastí, ve které bude vytvořen cluster. Seznam platných umístění použijte `az account list-locations` příkaz|
    |clusterName|Nahraďte název CLUSTERNAME globálně jedinečným názvem nového clusteru.|
    |storageAccount|Nahraďte STORAGEACCOUNTNAME názvem pro váš nový účet úložiště.|
    |httpPassword|Nahraďte heslo pro přihlášení clusteru, **správce**.|
    |sshPassword|Nahraďte heslo pro zabezpečené uživatelské jméno prostředí, **sshuser**.|
    |securityGroupName|Nahraďte název skupiny zabezpečení AAD klienta pro kafka rest proxy. Proměnná bude předána `--kafka-client-group-name` parametru pro `az-hdinsight-create`.|
    |securityGroupID|Nahraďte SECURITYGROUPID id skupiny zabezpečení klienta pro Kafka Rest Proxy. Proměnná bude předána `--kafka-client-group-id` parametru pro `az-hdinsight-create`.|
    |storageContainer|Kontejner úložiště clusteru bude používat, ponechat jako-je pro tento kurz. Tato proměnná bude nastavena s názvem clusteru.|
    |workernodeCount|Počet pracovních uzlů v clusteru, ponechat jako-je pro tento kurz. Pro zajištění vysoké dostupnosti vyžaduje Kafka minimálně 3 pracovní uzly|
    |typ clusteru|Typ clusteru HDInsight, ponechat jako-je pro tento kurz.|
    |clusterVersion|HDInsight verze clusteru, ponechat jako-je pro tento kurz. Kafka Rest Proxy vyžaduje minimální verzi clusteru 4.0.|
    |komponentaVersion|Kafka verze, nechte, jak-je pro tento výukový program. Kafka Rest Proxy vyžaduje minimální verzi komponenty 2.1.|

    Aktualizujte proměnné požadovanými hodnotami. Poté zadejte příkazy příkazu příkazu k příkazu pro nastavení proměnných prostředí.

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

1. [Vytvořte skupinu prostředků](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) zadáním následujícího příkazu:

    ```azurecli
     az group create \
        --location $location \
        --name $resourceGroupName
    ```

1. [Vytvořte si účet Azure Storage](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) zadáním následujícího příkazu:

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

1. [Extrahujte primární klíč](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list) z účtu Azure Storage a uložte ho do proměnné zadáním následujícího příkazu:

    ```azurecli
    export storageAccountKey=$(az storage account keys list \
        --account-name $storageAccount \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

1. [Vytvořte kontejner úložiště Azure](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) zadáním následujícího příkazu:

    ```azurecli
    az storage container create \
        --name $storageContainer \
        --account-key $storageAccountKey \
        --account-name $storageAccount
    ```

1. [Vytvořte cluster HDInsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create). Před zadáním příkazu si všimněte následujících parametrů:

    1. Požadované parametry pro kafcké klastry:

        |Parametr | Popis|
        |---|---|
        |--typ|Hodnota musí být **Kafka**.|
        |--workernode-data-disks-per-node --workernode-data-disks-per-node --workernode-data-disks-per-node --worker|Počet datových disků, které mají být používány na pracovní uzel. HDInsight Kafka je podporován pouze datovými disky. Tento kurz používá hodnotu **2**.|

    1. Požadované parametry pro proxy kafka REST:

        |Parametr | Popis|
        |---|---|
        |--kafka-management-velikost uzlu|Velikost uzlu. Tento kurz používá hodnotu **Standard_D4_v2**.|
        |--kafka-client-group-id|ID skupiny zabezpečení AAD klienta pro Kafka Rest Proxy. Hodnota je předána z proměnné **$securityGroupID**.|
        |--kafka-client-group-name --kafka-client-group-name --kafka-client-group-name --k|Název skupiny zabezpečení AAD klienta pro Kafka Rest Proxy. Hodnota je předána z proměnné **$securityGroupName**.|
        |--verze|Verze clusteru HDInsight musí být alespoň 4.0. Hodnota je předána z proměnné **$clusterVersion**.|
        |--komponenta-verze|Verze Kafka musí být alespoň 2.1. Hodnota je předána z proměnné **$componentVersion**.|
    
        Chcete-li vytvořit cluster bez proxy serveru `--kafka-management-node-size` `--kafka-client-group-id`REST, `--kafka-client-group-name` odstraňte , a z příkazu. `az hdinsight create`

    1. Pokud máte existující virtuální síť, přidejte parametry `--vnet-name` a `--subnet`, a jejich hodnoty.

    Chcete-li vytvořit cluster, zadejte následující příkaz:

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

    Dokončení procesu vytváření clusteru může trvat několik minut. Obvykle kolem 15.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile budete s článkem hotovi, můžete cluster odstranit. S HDInsight, vaše data jsou uloženy ve službě Azure Storage, takže můžete bezpečně odstranit clusteru, když není v provozu. Účtuje se vám také cluster HDInsight, i když se nepoužívá. Vzhledem k tomu, že poplatky za cluster jsou mnohonásobně vyšší než poplatky za úložiště, má ekonomické smysl odstranit clustery, když nejsou používány.

Chcete-li odebrat prostředky, zadejte všechny nebo některé z následujících příkazů:

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

Teď, když jste úspěšně vytvořili cluster s podporou proxy serveru Apache Kafka REST v Azure HDInsight pomocí rozhraní příkazového příkazu Azure, použijte kód Pythonu k interakci se serverem REST proxy:

> [!div class="nextstepaction"]
> [Vytvořit ukázkovou aplikaci](./rest-proxy.md#client-application-sample)