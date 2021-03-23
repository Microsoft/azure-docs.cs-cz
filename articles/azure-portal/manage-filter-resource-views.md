---
title: Zobrazení a filtrování informací o prostředcích Azure
description: Filtrujte informace a používejte různá zobrazení k lepšímu pochopení vašich prostředků Azure.
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: bb48d0b0a7bf6017fbf407a95c33ef17729e34e3
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771630"
---
# <a name="view-and-filter-azure-resource-information"></a>Zobrazení a filtrování informací o prostředcích Azure

Azure Portal vám umožní procházet podrobné informace o prostředcích v rámci předplatných Azure. V tomto článku se dozvíte, jak filtrovat informace a používat různá zobrazení k lepšímu pochopení prostředků.

Článek se zaměřuje na obrazovku **všechny prostředky** zobrazené na následujícím snímku obrazovky. Obrazovky pro jednotlivé typy prostředků, jako jsou například virtuální počítače, mají různé možnosti, jako je spuštění a zastavení virtuálního počítače.

:::image type="content" source="media/manage-filter-resource-views/all-resources.png" alt-text="Azure Portal zobrazení všech prostředků":::

## <a name="filter-resources"></a>Filtrovat prostředky

Začněte zkoumat **všechny prostředky** pomocí filtrů, abyste se mohli soustředit na podmnožinu vašich prostředků. Následující snímek obrazovky ukazuje filtrování u skupin prostředků a výběr dvou z šesti skupin prostředků v rámci předplatného.

:::image type="content" source="media/manage-filter-resource-views/filter-resource-group.png" alt-text="Zobrazení filtru na základě skupin prostředků":::

Filtry můžete kombinovat, včetně těch, které jsou založené na hledání textu, jak je znázorněno na následujícím snímku obrazovky. V takovém případě jsou výsledky vymezeny na prostředky, které obsahují "SimpleWinVM" v jedné ze dvou skupin prostředků, které jsou již vybrány.

:::image type="content" source="media/manage-filter-resource-views/filter-simplewinvm.png" alt-text="Filtrovat zobrazení na základě zadávání textu":::

Pokud chcete změnit, které sloupce jsou zahrnuté v zobrazení, vyberte **Spravovat zobrazení** a pak **Upravit sloupce**.

:::image type="content" source="media/manage-filter-resource-views/edit-columns.png" alt-text="Upravit sloupce zobrazené v zobrazení":::

## <a name="save-use-and-delete-views"></a>Uložení, použití a odstranění zobrazení

Můžete uložit zobrazení, která zahrnují filtry a sloupce, které jste vybrali. Uložení a použití zobrazení:

1. Vyberte **Spravovat zobrazení** a pak **Uložit zobrazení**.

1. Zadejte název zobrazení a pak vyberte **OK**. Uložené zobrazení se nyní zobrazí v nabídce **Spravovat zobrazení** .

    :::image type="content" source="media/manage-filter-resource-views/simple-view.png" alt-text="Uložené zobrazení":::

1. Chcete-li použít zobrazení, přepínejte mezi **výchozími** a jedním z vašich vlastních zobrazení, abyste viděli, jak to ovlivní seznam zobrazených prostředků.

Odstranění zobrazení:

1. Vyberte **Spravovat zobrazení** a pak **Procházet všechna zobrazení**.

1. V podokně **uložená zobrazení** vyberte zobrazení a pak vyberte ikonu **Odstranit** ikona ![ zobrazení ](media/manage-filter-resource-views/icon-delete.png) .

## <a name="export-information-from-a-view"></a>Export informací ze zobrazení

Informace o prostředku můžete exportovat ze zobrazení. Export informací ve formátu CSV:

1. Vyberte **exportovat do sdíleného svazku clusteru**.

    :::image type="content" source="media/manage-filter-resource-views/export-csv.png" alt-text="Snímek obrazovky exportu do formátu CSV":::

1. Uložte soubor místně a pak ho otevřete v Excelu nebo v jiné aplikaci, která podporuje formát CSV. 

Při pohybu na portálu uvidíte další oblasti, kde můžete exportovat informace, jako je například jednotlivé skupiny prostředků.

## <a name="summarize-resources-with-visuals"></a>Sumarizace prostředků pomocí vizuálů

Zobrazení, která jsme prohlédli zatím, byla _zobrazením seznamu_, ale existují také _Souhrnná zobrazení_ , která obsahují vizuály. Tato zobrazení můžete uložit a používat stejně jako u zobrazení seznamu. Filtry se chovají mezi dvěma typy zobrazení. Existují standardní zobrazení, jako je například zobrazení **umístění** , které je relevantní pro konkrétní služby, jako je například zobrazení **stavu** pro Azure Storage.

:::image type="content" source="media/manage-filter-resource-views/summary-map.png" alt-text="Souhrn prostředků v zobrazení mapy":::

Uložení a použití souhrnného zobrazení:

1. V nabídce zobrazení vyberte **souhrnné zobrazení**.

    :::image type="content" source="media/manage-filter-resource-views/menu-summary-view.png" alt-text="Nabídka zobrazení souhrnu":::

1. Souhrnné zobrazení umožňuje vytvořit souhrn podle různých atributů, včetně **umístění** a **typu**. Vyberte možnost **Shrnutí podle** možnosti a odpovídající vizuál. Následující snímek obrazovky ukazuje **Souhrn typu** s **pruhem pruhového grafu** .

    :::image type="content" source="media/manage-filter-resource-views/type-summary-bar-chart.png" alt-text="Shrnutí typu znázorňující pruhový graf":::

1. Vyberte **Spravovat zobrazení** a pak **Uložit** a uložte toto zobrazení jako v zobrazení seznamu.

1. V souhrnném zobrazení v části **Souhrn typu** vyberte pruh v grafu. Výběr pruhu poskytuje seznam filtrovaný na jeden typ prostředku.

    :::image type="content" source="media/manage-filter-resource-views/all-resources-filtered-type.png" alt-text="Všechny prostředky filtrované podle typu":::

## <a name="run-queries-in-azure-resource-graph"></a>Spuštění dotazů v Azure Resource graphu

Azure Resource Graph poskytuje efektivní a výkonný průzkum prostředků s možností provádět dotazy ve velkém rozsahu napříč sadou předplatných. Obrazovka **všechny prostředky** v Azure Portal obsahuje odkaz pro otevření dotazu grafu prostředku, který je vymezen na aktuálně filtrované zobrazení.

Spuštění dotazu na graf prostředku:

1. Vyberte **Otevřít dotaz**.

    :::image type="content" source="media/manage-filter-resource-views/open-query.png" alt-text="Otevřít dotaz na graf prostředků Azure":::

1. V **Průzkumníku Azure Resource graphu** vyberte **Spustit dotaz** a zobrazí se výsledky.

    :::image type="content" source="media/manage-filter-resource-views/run-query.png" alt-text="Spustit dotaz na Azure Resource Graph":::

    Další informace najdete v tématu [spuštění prvního dotazu na graf prostředku pomocí Průzkumníka Azure Resource graphu](../governance/resource-graph/first-query-portal.md).

## <a name="next-steps"></a>Další kroky

[Přehled webu Azure Portal](azure-portal-overview.md)

[Vytváření a sdílení řídicích panelů na webu Azure Portal](azure-portal-dashboards.md)
