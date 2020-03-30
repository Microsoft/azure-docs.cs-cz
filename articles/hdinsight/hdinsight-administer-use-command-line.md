---
title: Správa clusterů Azure HDInsight pomocí rozhraní příkazového příkazu Azure
description: Přečtěte si, jak pomocí rozhraní příkazového příkazu Azure spravovat clustery Azure HDInsight. Typy clusterů zahrnují Apache Hadoop, Spark, HBase, Storm, Kafka, Interactive Query a ML Services.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/26/2020
ms.openlocfilehash: 2c6495454e5ba2449d4b3c74a096681f74610813
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272770"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Správa clusterů Azure HDInsight pomocí rozhraní příkazového příkazu Azure

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Přečtěte si, jak používat [rozhraní příkazového příkazového od vlády Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) ke správě clusterů Azure HDInsight. Rozhraní příkazového řádku Azure (CLI) je nové víceplatformové prostředí příkazového řádku Microsoftu pro správu prostředků Azure.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

* Azure CLI. Pokud jste nenainstalovali azure cli, najdete [v tématu instalace azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli) pro kroky.

* Cluster Apache Hadoop na HDInsight. Viz [Začínáme s HDInsight na Linuxu](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="connect-to-azure"></a>Připojení k Azure

Přihlaste se ke svému předplatnému Azure. Pokud máte v plánu používat Azure Cloud Shell, pak vyberte **Try it** v pravém horním rohu bloku kódu. Jinak zadejte následující příkaz:

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>Seznam clusterů

Pomocí [seznamu az hdinsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-list) uveďte clustery. Upravte níže uvedené příkazy nahrazením `RESOURCE_GROUP_NAME` názvem skupiny prostředků a zadejte příkazy:

```azurecli-interactive
# List all clusters in the current subscription
az hdinsight list

# List only cluster name and its resource group
az hdinsight list --query "[].{Cluster:name, ResourceGroup:resourceGroup}" --output table

# List all cluster for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME

# List all cluster names for your resource group
az hdinsight list --resource-group RESOURCE_GROUP_NAME --query "[].{clusterName:name}" --output table
```

## <a name="show-cluster"></a>Zobrazit cluster

Pomocí [az hdinsight show](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-show) zobrazte informace pro zadaný cluster. Upravte níže uvedený `RESOURCE_GROUP_NAME`příkaz `CLUSTER_NAME` nahrazením a příslušnými informacemi a zadejte příkaz:

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>Odstranění clusterů

Pomocí odstranění zadaného clusteru použijte [az hdinsight delete.](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-delete) Upravte níže uvedený `RESOURCE_GROUP_NAME`příkaz `CLUSTER_NAME` nahrazením a příslušnými informacemi a zadejte příkaz:

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

Cluster můžete také odstranit odstraněním skupiny prostředků, která obsahuje cluster. Tím odstraníte všechny prostředky ve skupině včetně výchozího účtu úložiště.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>Škálovat clustery

Změna [velikosti az hdinsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) uděláte velikost zadaného clusteru HDInsight na zadanou velikost. Upravte níže uvedený `RESOURCE_GROUP_NAME`příkaz `CLUSTER_NAME` nahrazením a příslušnými informacemi. Nahraďte `WORKERNODE_COUNT` požadovaný počet pracovních uzlů pro váš cluster. Další informace o škálování clusterů naleznete v [tématu Škálování clusterů HDInsight](./hdinsight-scaling-best-practices.md). Zadejte příkaz:

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --workernode-count WORKERNODE_COUNT
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili, jak provádět různé úlohy správy clusteru HDInsight. Další informace naleznete v následujících článcích:

* [Správa clusterů Apache Hadoop v HDInsightu pomocí portálu Azure](hdinsight-administer-use-portal-linux.md)
* [Správa HDInsightu pomocí Azure PowerShellu](hdinsight-administer-use-powershell.md)
* [Začínáme se službou Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Začínáme s Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)
