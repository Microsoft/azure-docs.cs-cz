---
title: Správa clusterů Azure HDInsight pomocí rozhraní příkazového řádku Azure
description: Naučte se používat rozhraní příkazového řádku Azure ke správě clusterů Azure HDInsight. Mezi typy clusterů patří Apache Hadoop, Spark, HBA, Kafka, interaktivní dotazy a služby ML.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-azurecli
ms.date: 02/26/2020
ms.openlocfilehash: d47c741bdf0d056a75a86cdb8aa14333320bb52a
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92742136"
---
# <a name="manage-azure-hdinsight-clusters-using-azure-cli"></a>Správa clusterů Azure HDInsight pomocí rozhraní příkazového řádku Azure

[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Naučte se používat [Azure CLI](/cli/azure/) ke správě clusterů Azure HDInsight. Rozhraní příkazového řádku Azure (CLI) je nové víceplatformové prostředí příkazového řádku Microsoftu pro správu prostředků Azure.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

## <a name="prerequisites"></a>Předpoklady

* Azure CLI Pokud jste nenainstalovali Azure CLI, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli) pro kroky.

* Cluster Apache Hadoop v HDInsight. Viz Začínáme [se službou HDInsight v systému Linux](hadoop/apache-hadoop-linux-tutorial-get-started.md).

## <a name="connect-to-azure"></a>Připojení k Azure

Přihlaste se ke svému předplatnému Azure. Pokud plánujete použít Azure Cloud Shell, vyberte **vyzkoušet** v pravém horním rohu bloku kódu. V opačném případě zadejte následující příkaz:

```azurecli-interactive
az login

# If you have multiple subscriptions, set the one to use
# az account set --subscription "SUBSCRIPTIONID"
```

## <a name="list-clusters"></a>Výpis clusterů

K vypsání clusterů použijte [AZ HDInsight list](/cli/azure/hdinsight#az-hdinsight-list) . Níže uvedené příkazy upravte nahrazením `RESOURCE_GROUP_NAME` názvem vaší skupiny prostředků a zadáním příkazů:

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

K zobrazení informací o zadaném clusteru použijte [AZ HDInsight show](/cli/azure/hdinsight#az-hdinsight-show) . Níže uvedený příkaz upravte nahrazením `RESOURCE_GROUP_NAME` a `CLUSTER_NAME` příslušnými informacemi a zadáním příkazu:

```azurecli-interactive
az hdinsight show --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

## <a name="delete-clusters"></a>Odstranění clusterů

Pomocí [AZ HDInsight Delete](/cli/azure/hdinsight#az-hdinsight-delete) odstraňte zadaný cluster. Níže uvedený příkaz upravte nahrazením `RESOURCE_GROUP_NAME` a `CLUSTER_NAME` příslušnými informacemi a zadáním příkazu:

```azurecli-interactive
az hdinsight delete --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME
```

Cluster můžete odstranit také odstraněním skupiny prostředků, která obsahuje cluster. Všimněte si, že se odstraní všechny prostředky ve skupině včetně výchozího účtu úložiště.

```azurecli-interactive
az group delete --name RESOURCE_GROUP_NAME
```

## <a name="scale-clusters"></a>Škálování clusterů

Pomocí [AZ HDInsight Resize](/cli/azure/hdinsight#az-hdinsight-resize) změňte velikost zadaného clusteru HDInsight na určenou velikost. Úpravou příkazu níže nahraďte `RESOURCE_GROUP_NAME` a `CLUSTER_NAME` příslušné informace. Nahraďte `WORKERNODE_COUNT` požadovaným počtem uzlů pracovního procesu pro váš cluster. Další informace o škálování clusterů najdete v tématu [škálování clusterů HDInsight](./hdinsight-scaling-best-practices.md). Zadejte příkaz:

```azurecli-interactive
az hdinsight resize --resource-group RESOURCE_GROUP_NAME --name CLUSTER_NAME --workernode-count WORKERNODE_COUNT
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili, jak provádět různé úlohy správy clusteru HDInsight. Další informace najdete v těchto článcích:

* [Správa clusterů Apache Hadoop ve službě HDInsight pomocí Azure Portal](hdinsight-administer-use-portal-linux.md)
* [Správa HDInsight pomocí Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Začínáme se službou Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Začínáme s Azure CLI](/cli/azure/get-started-with-azure-cli)
