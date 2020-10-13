---
title: Jak odstranit cluster HDInsight – Azure
description: Informace o různých způsobech, jak můžete odstranit cluster Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 3903500e87dd162779d1732a4cfe405ba34e702c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87074749"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Odstranění clusteru HDInsight pomocí prohlížeče, PowerShellu nebo rozhraní příkazového řádku Azure

Účtování clusteru HDInsight začne vytvořením clusteru a skončí jeho odstraněním. Fakturuje se poměrnou částí po minutách, takže byste cluster měli vždycky odstranit, i když už se nepoužívá. V tomto dokumentu se dozvíte, jak odstranit cluster pomocí [Azure Portal](https://portal.azure.com), [Azure PowerShell AZ Module](https://docs.microsoft.com/powershell/azure/)a [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

> [!IMPORTANT]  
> Odstraněním clusteru HDInsight nedojde k odstranění Azure Storagech účtů ani Data Lake Storage přidružených ke clusteru. V budoucnu můžete znovu použít data uložená v těchto službách.

## <a name="azure-portal"></a>portál Azure

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

2. V nabídce vlevo přejděte na **všechny služby**  >  **Analytics**  >  **clustery HDInsight** a vyberte svůj cluster.

3. Ve výchozím zobrazení vyberte ikonu **Odstranit** . Pomocí výzvy odstraňte cluster.

    ![Tlačítko pro odstranění clusteru HDInsight](./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png)

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
