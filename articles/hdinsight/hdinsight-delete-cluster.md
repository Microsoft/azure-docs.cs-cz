---
title: Postup odstranění clusteru služby HDInsight – Azure
description: Informace o různých způsobech, že jste odstranili clusteru služby HDInsight.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: jasonh
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 737cc120877a9d0f06a1f6d209bcf9a233aa7d19
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43091305"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Odstranění clusteru HDInsight pomocí prohlížeče, Powershellu nebo rozhraní příkazového řádku Azure

Účtování clusteru HDInsight začne vytvořením clusteru a skončí jeho odstraněním. Účtuje se poměrnou částí po minutách, takže byste cluster měli odstranit vždy, když už se nepoužívá. V tomto dokumentu se dozvíte, jak odstranit cluster pomocí webu Azure portal, prostředí Azure PowerShell a rozhraní příkazového řádku Azure CLI 1.0.

> [!IMPORTANT]
> Odstranění clusteru služby HDInsight nedojde k odstranění účtů služby Azure Storage nebo Data Lake Store přidružené ke clusteru. Můžete využít data uložená v těchto služeb v budoucnu.

## <a name="azure-portal"></a>portál Azure

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a vyberte svůj cluster HDInsight. Pokud váš cluster HDInsight není připnutý na řídicí panel, můžete ho vyhledat podle názvu pomocí vyhledávacího pole.
   
    ![vyhledávání v portálu](./media/hdinsight-delete-cluster/navbar.png)

2. Nastavení clusteru, vyberte **odstranit** ikonu. Po zobrazení výzvy vyberte **Ano** cluster odstranit.
   
    ![Ikona odstranění](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

Z příkazového řádku Powershellu pomocí následujícího příkazu odstraňte cluster:

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

Nahraďte **CLUSTERNAME** názvem clusteru HDInsight.

## <a name="azure-cli-10"></a>Azure CLI 1.0

Na řádku pomocí následujícího postupu odstraňte cluster:

    azure hdinsight cluster delete CLUSTERNAME

Nahraďte **CLUSTERNAME** názvem clusteru HDInsight.

> [!NOTE]
> Azure CLI 2.0 nepodporuje odstranění clusterů HDInsight v tuto chvíli (23. října 2017).
