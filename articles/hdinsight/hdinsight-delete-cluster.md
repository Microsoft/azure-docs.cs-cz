---
title: Postup odstranění clusteru služby HDInsight – Azure
description: Informace o různých způsobech, že jste odstranili clusteru služby HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: f5c2f6104a765c0e598e41234891c492686019dd
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53718975"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-classic-cli"></a>Odstranění clusteru HDInsight pomocí prohlížeče, Powershellu nebo rozhraní příkazového řádku Azure Classic

Účtování clusteru HDInsight začne vytvořením clusteru a skončí jeho odstraněním. Účtuje se poměrnou částí po minutách, takže byste cluster měli odstranit vždy, když už se nepoužívá. V tomto dokumentu se dozvíte, jak odstranit cluster pomocí [webu Azure portal](https://portal.azure.com), [prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/)a rozhraní příkazového řádku Azure Classic.

> [!IMPORTANT]  
> Odstranění clusteru služby HDInsight nedojde k odstranění účtů služby Azure Storage nebo Data Lake Storage přidružené ke clusteru. Můžete využít data uložená v těchto služeb v budoucnu.

## <a name="azure-portal"></a>portál Azure

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a vyberte svůj cluster HDInsight. Pokud váš cluster HDInsight není připnutý na řídicí panel, můžete ho vyhledat podle názvu pomocí vyhledávacího pole.
   
    ![vyhledávání v portálu](./media/hdinsight-delete-cluster/navbar.png)

2. Nastavení clusteru, vyberte **odstranit** ikonu. Po zobrazení výzvy vyberte **Ano** cluster odstranit.
   
    ![Ikona odstranění](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

Z příkazového řádku Powershellu pomocí následujícího příkazu odstraňte cluster:

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

Nahraďte **CLUSTERNAME** názvem clusteru HDInsight.

## <a name="azure-classic-cli"></a>Klasické rozhraní příkazového řádku Azure

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

Na řádku pomocí následujícího postupu odstraňte cluster:

    azure hdinsight cluster delete CLUSTERNAME

Nahraďte **CLUSTERNAME** názvem clusteru HDInsight.
