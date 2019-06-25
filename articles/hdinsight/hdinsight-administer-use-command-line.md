---
title: Správa clusterů Azure HDInsight pomocí rozhraní příkazového řádku Azure
description: Zjistěte, jak pomocí Azure CLI ke správě clusterů Azure HDInsight. Typy clusterů patří Apache Hadoop, Spark, HBase, Storm, Kafka, Interactive Query a služby ML.
ms.reviewer: jasonh
author: tylerfox
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: tyfox
ms.openlocfilehash: 5ae97b17d06fa0a9934a58ac662ef12116cce4f6
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/14/2019
ms.locfileid: "67137404"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Správa clusterů Azure HDInsight pomocí rozhraní příkazového řádku Azure

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Další informace o použití [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) ke správě clusterů Azure HDInsight. Rozhraní příkazového řádku Azure (CLI) je nové víceplatformové prostředí příkazového řádku Microsoftu pro správu prostředků Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Azure CLI. Pokud jste nenainstalovali Azure CLI, přečtěte si téma [instalace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) kroky.

* Cluster Apache Hadoop v HDInsight. Zobrazit [Začínáme s HDInsight v Linuxu](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="connect-to-azure"></a>Připojení k Azure

Přihlaste se ke svému předplatnému Azure. Pokud plánujete použít Azure Cloud Shell, pak stačí vybrat **vyzkoušet** v pravém horním rohu bloku kódu. Jinak zadejte následující příkaz:

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>Výpis clusterů

Použití [seznam hdinsight az](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-list) pro seznam clusterů. Upravte příkazy pod nahrazením `RESOURCE_GROUP_NAME` s názvem vaší skupiny prostředků, zadejte příkaz:

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

## <a name="show-cluster"></a>Zobrazení clusteru

Použití [az hdinsight zobrazit](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-show) zobrazuje informace pro zadaný cluster. Upravte následující příkaz tak, že nahradíte `RESOURCE_GROUP_NAME`, a `CLUSTER_NAME` relevantní informace, zadejte příkaz:

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>Odstranění clusterů

Použití [az hdinsight odstranit](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-delete) zadaný cluster odstranit. Upravte následující příkaz tak, že nahradíte `RESOURCE_GROUP_NAME`, a `CLUSTER_NAME` relevantní informace, zadejte příkaz:

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

Můžete také odstranit cluster tak, že odstraníte skupinu prostředků, která obsahuje clusteru. Poznámka: Tato akce odstraní všechny prostředky ve skupině, včetně výchozího účtu úložiště.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>Škálování clusterů

Použití [změnit velikost az hdinsight](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-resize) ke změně velikosti zadaný cluster HDInsight zadané velikosti. Upravte následující příkaz tak, že nahradíte `RESOURCE_GROUP_NAME`, a `CLUSTER_NAME` příslušné informace. Nahraďte `TARGET_INSTANCE_COUNT` s požadovaný počet uzlů pracovního procesu pro váš cluster. Další informace o škálování clusterů najdete v tématu [clusterů HDInsight škálování](./hdinsight-scaling-best-practices.md). Zadejte příkaz:

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --target-instance-count TARGET_INSTANCE_COUNT
```

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak provádět různé úlohy správy clusteru HDInsight. Další informace naleznete v následujících článcích:

* [Spravovat clustery systému Apache Hadoop v HDInsight pomocí webu Azure portal](hdinsight-administer-use-portal-linux.md)
* [Správa HDInsight pomocí Azure Powershellu](hdinsight-administer-use-powershell.md)
* [Začínáme s Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Začínáme s Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest)
