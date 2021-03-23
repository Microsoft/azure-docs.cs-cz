---
title: Jak odstranit cluster HDInsight – Azure
description: Informace o různých způsobech, jak můžete odstranit cluster Azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive, devx-track-azurecli
ms.date: 11/29/2019
ms.openlocfilehash: 2160cfcd1f12f5effe7bb182eb665f85fec863af
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863390"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Odstranění clusteru HDInsight pomocí prohlížeče, PowerShellu nebo rozhraní příkazového řádku Azure

Účtování clusteru HDInsight začne vytvořením clusteru a skončí jeho odstraněním. Fakturuje se poměrnou částí po minutách, takže byste cluster měli vždycky odstranit, i když už se nepoužívá. V tomto dokumentu se dozvíte, jak odstranit cluster pomocí [Azure Portal](https://portal.azure.com), [Azure PowerShell AZ Module](/powershell/azure/)a [Azure CLI](/cli/azure/).

> [!IMPORTANT]  
> Odstraněním clusteru HDInsight nedojde k odstranění Azure Storagech účtů ani Data Lake Storage přidružených ke clusteru. V budoucnu můžete znovu použít data uložená v těchto službách.

## <a name="azure-portal"></a>portál Azure

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

2. V nabídce vlevo přejděte na **všechny služby**  >  **Analytics**  >  **clustery HDInsight** a vyberte svůj cluster.

3. Ve výchozím zobrazení vyberte ikonu **Odstranit** . Pomocí výzvy odstraňte cluster.

    :::image type="content" source="./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png" alt-text="Tlačítko pro odstranění clusteru HDInsight":::

## <a name="azure-powershell"></a>Azure PowerShell

Nahraďte `CLUSTERNAME` názvem vašeho clusteru HDInsight v následujícím kódu. Z příkazového řádku PowerShellu zadejte následující příkaz pro odstranění clusteru:

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Azure CLI

Nahraďte `CLUSTERNAME` názvem vašeho clusteru HDInsight a `RESOURCEGROUP` názvem vaší skupiny prostředků v následujícím kódu.  Na příkazovém řádku zadejte následující příkaz a odstraňte tak cluster:

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
