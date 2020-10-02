---
title: Zobrazení a filtrování informací o prostředcích Azure
description: Filtrujte informace a používejte různá zobrazení k lepšímu pochopení vašich prostředků Azure.
author: mgblythe
ms.service: azure-portal
ms.topic: quickstart
ms.author: mblythe
ms.date: 09/11/2020
ms.openlocfilehash: 3b9783e7f452b38292c784d44ddb60672e150961
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91650390"
---
# <a name="view-and-filter-azure-resource-information"></a>Zobrazení a filtrování informací o prostředcích Azure

Azure Portal vám umožní procházet podrobné informace o prostředcích v rámci předplatných Azure. V tomto článku se dozvíte, jak filtrovat informace a používat různá zobrazení k lepšímu pochopení prostředků.

Článek se zaměřuje na obrazovku **všechny prostředky** zobrazené na následujícím snímku obrazovky. Obrazovky pro jednotlivé typy prostředků, jako jsou například virtuální počítače, mají různé možnosti, jako je spuštění a zastavení virtuálního počítače.

:::image type="content" source="media/manage-filter-resource-views/all-resources.png" alt-text="Azure Portal zobrazení všech prostředků":::

## <a name="filter-resources"></a>Filtrovat prostředky

Začněte zkoumat **všechny prostředky** pomocí filtrů, abyste se mohli soustředit na podmnožinu vašich prostředků. Následující snímek obrazovky ukazuje filtrování u skupin prostředků a výběr dvou z šesti skupin prostředků v rámci předplatného.

:::image type="content" source="media/manage-filter-resource-views/filter-resource-group.png" alt-text="Azure Portal zobrazení všech prostředků" v jedné ze dvou skupin prostředků, které jsou již vybrány.

:::image type="content" source="media/manage-filter-resource-views/filter-simplewinvm.png" alt-text="Azure Portal zobrazení všech prostředků":::

Pokud chcete změnit, které sloupce jsou zahrnuté v zobrazení, vyberte **Spravovat zobrazení** a pak **Upravit sloupce**.

:::image type="content" source="media/manage-filter-resource-views/edit-columns.png" alt-text="Azure Portal zobrazení všech prostředků":::

## <a name="save-use-and-delete-views"></a>Uložení, použití a odstranění zobrazení

Můžete uložit zobrazení, která zahrnují filtry a sloupce, které jste vybrali. Uložení a použití zobrazení:

1. Vyberte **Spravovat zobrazení** a pak **Uložit zobrazení**.

1. Zadejte název zobrazení a pak vyberte **OK**. Uložené zobrazení se nyní zobrazí v nabídce **Spravovat zobrazení** .

    :::image type="content" source="media/manage-filter-resource-views/simple-view.png" alt-text="Azure Portal zobrazení všech prostředků":::

1. Chcete-li použít zobrazení, přepínejte mezi **výchozími** a jedním z vašich vlastních zobrazení, abyste viděli, jak to ovlivní seznam zobrazených prostředků.

Odstranění zobrazení:

1. Vyberte **Spravovat zobrazení** a pak **Procházet všechna zobrazení**.

1. V podokně **uložená zobrazení pro všechny prostředky** vyberte zobrazení a pak vyberte ikonu **Odstranit** ikona ![ Odstranit zobrazení ](media/manage-filter-resource-views/icon-delete.png) .

## <a name="summarize-resources-with-visuals"></a>Sumarizace prostředků pomocí vizuálů

Zobrazení, která jsme prohlédli zatím, byla _zobrazením seznamu_, ale existují také _Souhrnná zobrazení_ , která obsahují vizuály. Tato zobrazení můžete uložit a používat stejně jako seznam zobrazení. Filtry se chovají mezi dvěma typy zobrazení. Existují standardní zobrazení, jako je například zobrazení **umístění** , které je relevantní pro konkrétní služby, jako je například zobrazení **stavu** virtuálních počítačů.

:::image type="content" source="media/manage-filter-resource-views/summary-map.png" alt-text="Azure Portal zobrazení všech prostředků":::

Uložení a použití souhrnného zobrazení:

1. V nabídce zobrazení vyberte **souhrnné zobrazení**.

    :::image type="content" source="media/manage-filter-resource-views/menu-summary-view.png" alt-text="Azure Portal zobrazení všech prostředků":::

1. Souhrnné zobrazení umožňuje vytvořit souhrn podle různých atributů, včetně **umístění** a **typu**. Vyberte možnost **Shrnutí podle** možnosti a odpovídající vizuál. Následující snímek obrazovky ukazuje **Souhrn typu** s **pruhem pruhového grafu** .

    :::image type="content" source="media/manage-filter-resource-views/type-summary-bar-chart.png" alt-text="Azure Portal zobrazení všech prostředků":::

1. Vyberte **Spravovat zobrazení** a pak **Uložit** a uložte toto zobrazení jako v zobrazení seznamu.

1. V souhrnném zobrazení v části **Souhrn typu**vyberte pruh v grafu. Výběr pruhu poskytuje seznam filtrovaný na jeden typ prostředku.

    :::image type="content" source="media/manage-filter-resource-views/all-resources-filtered-type.png" alt-text="Azure Portal zobrazení všech prostředků":::

## <a name="run-queries-in-azure-resource-graph"></a>Spuštění dotazů v Azure Resource graphu

Azure Resource Graph poskytuje efektivní a výkonný průzkum prostředků s možností provádět dotazy ve velkém rozsahu napříč sadou předplatných. Obrazovka **všechny prostředky** v Azure Portal obsahuje odkaz pro otevření dotazu grafu prostředku, který je vymezen na aktuálně filtrované zobrazení.

Spuštění dotazu na graf prostředku:

1. Vyberte **Otevřít dotaz**.

    :::image type="content" source="media/manage-filter-resource-views/open-query.png" alt-text="Azure Portal zobrazení všech prostředků":::

1. V **Průzkumníku Azure Resource graphu**vyberte **Spustit dotaz** a zobrazí se výsledky.

    :::image type="content" source="media/manage-filter-resource-views/run-query.png" alt-text="Azure Portal zobrazení všech prostředků":::

    Další informace najdete v tématu [spuštění prvního dotazu na graf prostředku pomocí Průzkumníka Azure Resource graphu](../governance/resource-graph/first-query-portal.md).

## <a name="next-steps"></a>Další kroky

[Přehled webu Azure Portal](azure-portal-overview.md)

[Vytváření a sdílení řídicích panelů na webu Azure Portal](azure-portal-dashboards.md)
