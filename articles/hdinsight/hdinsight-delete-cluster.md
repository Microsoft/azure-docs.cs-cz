---
title: Jak odstranit cluster HDInsight – Azure
description: Informace o různých způsobech odstranění clusteru Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 2912777c001a36ec913e2cfd618091ada5aa107a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74807132"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Odstranění clusteru HDInsight pomocí prohlížeče, PowerShellu nebo rozhraní příkazového příkazu Azure

Účtování clusteru HDInsight začne vytvořením clusteru a skončí jeho odstraněním. Fakturace je poměrná za minutu, takže byste měli cluster odstranit vždy, když se už nepoužívá. V tomto dokumentu se dozvíte, jak odstranit cluster pomocí [portálu Azure](https://portal.azure.com), [modulu Azure PowerShell Az](https://docs.microsoft.com/powershell/azure/overview)a [rozhraní příkazového příkazu k Onomu Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

> [!IMPORTANT]  
> Odstraněním clusteru HDInsight neodstraníte účty Azure Storage nebo Storage data lake storage přidružené ke clusteru. V budoucnu můžete znovu použít data uložená v těchto službách.

## <a name="azure-portal"></a>portál Azure

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

2. V levé nabídce přejděte do clusterů **Služby** > **Analytics** > **HDInsight** a vyberte svůj cluster.

3. Ve výchozím zobrazení vyberte ikonu **Odstranit.** Podle pokynů smažte cluster.

    ![Tlačítko hdinsight odstranit cluster](./media/hdinsight-delete-cluster/hdinsight-delete-cluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

V `CLUSTERNAME` níže uvedeném kódu nahraďte název clusteru HDInsight. Z výzvy prostředí PowerShell zadejte následující příkaz pro odstranění clusteru:

```powershell
Remove-AzHDInsightCluster -ClusterName CLUSTERNAME
```

## <a name="azure-cli"></a>Azure CLI

Nahraďte `CLUSTERNAME` název clusteru HDInsight `RESOURCEGROUP` a názvem skupiny prostředků v níže uvedeném kódu.  Z příkazového řádku zadejte následující příkaz, abyste cluster odstranili:

```azurecli
az hdinsight delete --name CLUSTERNAME --resource-group RESOURCEGROUP
```
