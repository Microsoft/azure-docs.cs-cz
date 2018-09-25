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
ms.openlocfilehash: 39404ff74552b11e982cf5968c0eb131ea642e27
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46979439"
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-classic-cli"></a>Odstranění clusteru HDInsight pomocí prohlížeče, Powershellu nebo rozhraní příkazového řádku Azure Classic

Účtování clusteru HDInsight začne vytvořením clusteru a skončí jeho odstraněním. Účtuje se poměrnou částí po minutách, takže byste cluster měli odstranit vždy, když už se nepoužívá. V tomto dokumentu se dozvíte, jak odstranit cluster pomocí webu Azure portal, prostředí Azure PowerShell a rozhraní příkazového řádku Azure Classic.

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

## <a name="azure-classic-cli"></a>Klasické rozhraní příkazového řádku Azure

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

Na řádku pomocí následujícího postupu odstraňte cluster:

    azure hdinsight cluster delete CLUSTERNAME

Nahraďte **CLUSTERNAME** názvem clusteru HDInsight.
