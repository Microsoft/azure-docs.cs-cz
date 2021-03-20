---
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
author: linda33wj
ms.author: jingwang
ms.openlocfilehash: 34848b638ff0c7f7b9d1a2f3e5894339f8310ccc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96013346"
---
## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Kanál v kontejneru objektů blob adftutorial automaticky vytvoří výstupní složku. Potom do výstupní složky zkopíruje soubor emp.txt ze vstupní složky. 

1. V Azure Portal na stránce kontejneru **adftutorial** vyberte **aktualizovat** , aby se zobrazila výstupní složka. 
    
    ![Snímek obrazovky zobrazující stránku kontejneru, kde můžete stránku aktualizovat.](media/data-factory-quickstart-verify-output-cleanup/output-refresh.png)

2. V seznamu složek vyberte **výstup** . 

3. Potvrďte, že je do výstupní složky zkopírovaný soubor **emp.txt**. 

    ![Snímek obrazovky se zobrazí v obsahu výstupní složky.](media/data-factory-quickstart-verify-output-cleanup/output-file.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Prostředky, které jste vytvořili v rámci tohoto rychlého startu, můžete vyčistit dvěma způsoby. Můžete odstranit [skupinu prostředků Azure](../articles/azure-resource-manager/management/overview.md), což zahrnuje odstranění všech prostředků v této skupině prostředků. Pokud chcete ostatní prostředky zachovat beze změny, odstraňte pouze datovou továrnu, kterou jste vytvořili v tomto kurzu.

Odstranění skupiny prostředků odstraní všechny prostředky, které v ní jsou, včetně datových továren. Spuštěním následujícího příkazu odstraníte celou skupinu prostředků: 

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

> [!Note]
> Vyřazení skupiny prostředků může nějakou dobu trvat. Prosíme vás o trpělivost.

Pokud chcete odstranit jenom datovou továrnu, a ne celou skupinu prostředků, spusťte následující příkaz: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```