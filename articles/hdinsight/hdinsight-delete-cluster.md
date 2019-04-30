---
title: Postup odstranění clusteru služby HDInsight – Azure
description: Informace o různých způsobech, že jste odstranili clusteru služby HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: eca7b4f8bd7e91bc8dcb9bcc49ed3b981010aaee
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097169"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Odstranění clusteru HDInsight pomocí prohlížeče, Powershellu nebo rozhraní příkazového řádku Azure

Účtování clusteru HDInsight začne vytvořením clusteru a skončí jeho odstraněním. Účtuje se poměrnou částí po minutách, takže byste cluster měli odstranit vždy, když už se nepoužívá. V tomto dokumentu se dozvíte, jak odstranit cluster pomocí [webu Azure portal](https://portal.azure.com), [modul Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/overview)a [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

> [!IMPORTANT]  
> Odstranění clusteru služby HDInsight nedojde k odstranění účtů služby Azure Storage nebo Data Lake Storage přidružené ke clusteru. Můžete využít data uložená v těchto služeb v budoucnu.

## <a name="azure-portal"></a>portál Azure

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. V nabídce vlevo přejděte na **všechny služby** > **Analytics** > **clustery HDInsight** a vyberte svůj cluster.

3. Výchozí zobrazení, vyberte **odstranit** ikonu. Po zobrazení výzvy k odstranění clusteru.
   
    ![Ikona odstranění](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell-az-module"></a>Modul Azure PowerShell Az

Nahraďte `CLUSTERNAME` s názvem vašeho clusteru HDInsight v následujícím kódu. Z příkazového řádku Powershellu zadejte pomocí následujícího příkazu odstraňte cluster:

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Azure CLI

Nahraďte `CLUSTERNAME` s názvem vašeho clusteru HDInsight a `RESOURCEGROUP` s názvem vaší skupiny prostředků v následujícím kódu.  Z příkazového řádku zadejte následující příkaz pro odstranění clusteru:

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
