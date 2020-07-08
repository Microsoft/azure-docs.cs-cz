---
title: Jak odstranit cluster HDInsight – Azure
description: Informace o různých způsobech, jak můžete odstranit cluster Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 2912777c001a36ec913e2cfd618091ada5aa107a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "74807132"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Odstranění clusteru HDInsight pomocí prohlížeče, PowerShellu nebo rozhraní příkazového řádku Azure

Účtování clusteru HDInsight začne vytvořením clusteru a skončí jeho odstraněním. Fakturuje se poměrnou částí po minutách, takže byste cluster měli vždycky odstranit, i když už se nepoužívá. V tomto dokumentu se dozvíte, jak odstranit cluster pomocí [Azure Portal](https://portal.azure.com), [Azure PowerShell AZ Module](https://docs.microsoft.com/powershell/azure/overview)a [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

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
