---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 309ef92b33d5bbdf8e8aed6b162ed9428a669c87
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67175477"
---
## <a name="verify-the-output"></a>Ověření výstupu
Kanál v kontejneru objektů blob adftutorial automaticky vytvoří výstupní složku. Potom do výstupní složky zkopíruje soubor emp.txt ze vstupní složky. 

1. Na webu Azure Portal na stránce kontejneru **adftutorial** klikněte na **Obnovit**. Zobrazí se výstupní složka. 
    
    ![Obnovení](media/data-factory-quickstart-verify-output-cleanup/output-refresh.png)
2. V seznamu složek klikněte na **output**. 
2. Potvrďte, že je do výstupní složky zkopírovaný soubor **emp.txt**. 

    ![Obnovení](media/data-factory-quickstart-verify-output-cleanup/output-file.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků
Prostředky, které jste vytvořili v rámci tohoto rychlého startu, můžete vyčistit dvěma způsoby. Můžete odstranit [skupinu prostředků Azure](../articles/azure-resource-manager/resource-group-overview.md), což zahrnuje odstranění všech prostředků v této skupině prostředků. Pokud chcete ostatní prostředky zachovat beze změny, odstraňte pouze datovou továrnu, kterou jste vytvořili v tomto kurzu.

Odstranění skupiny prostředků odstraní všechny prostředky, které v ní jsou, včetně datových továren. Spuštěním následujícího příkazu odstraníte celou skupinu prostředků: 
```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

Poznámka: Odstranění skupiny prostředků může chvíli trvat. Prosíme vás o trpělivost.

Pokud chcete odstranit jenom datovou továrnu, a ne celou skupinu prostředků, spusťte následující příkaz: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```