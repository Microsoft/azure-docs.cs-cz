---
title: Uložená hledání v řešení pro správu | Dokumentace Microsoftu
description: Řešení pro správu obvykle zahrnují uložené výsledky hledání v Log Analytics k analýze data shromážděná tímto řešením. Tento článek popisuje, jak definovat uložené výsledky hledání v šabloně Resource Manageru, proto mohou být součástí řešení pro správu Log Analytics.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 64b79d613463ac2097a89a8e3ca3870b885a3332
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2019
ms.locfileid: "56985253"
---
# <a name="adding-log-analytics-saved-searches-to-management-solution-preview"></a>Přidání Log Analytics uložená hledání řešení pro správu (Preview)

> [!IMPORTANT]
> Předchozí verze tohoto článku zahrnuty podrobnosti o vytvoření výstrahy pomocí šablony Resource Manageru. Tyto informace se z celkového počtu data nyní [upozornění Log Analytics se rozšířily a Azure Monitor](../platform/alerts-extend.md). Podrobnosti o vytváření upozornění na protokol pomocí šablony Resource Manageru najdete v tématu [Správa upozornění protokolů pomocí šablony Azure Resource](../platform/alerts-log.md#managing-log-alerts-using-azure-resource-template).

> [!NOTE]
> Toto je předběžná dokumentace pro vytváření řešení pro správu, které jsou aktuálně ve verzi preview. Žádné schéma je popsáno níže se může změnit.

Tento článek popisuje, jak definovat uložené výsledky hledání v Log Analytics [šablony Resource Manageru](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md) tak mohou být zahrnuty v [řešení pro správu](solutions-creating.md).

> [!NOTE]
> Ukázky v tomto článku použijte parametry a proměnné, které jsou povinné nebo společné pro řešení pro správu a jsou popsány v [návrh a sestavení řešení pro správu v Azure](solutions-creating.md)

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že jste již obeznámeni s postupy [vytvořte řešení pro správu](solutions-creating.md) a strukturu [šablony Resource Manageru](../../azure-resource-manager/resource-group-authoring-templates.md) a soubor řešení.


## <a name="log-analytics-workspace"></a>Pracovní prostor Log Analytics
Všechny prostředky ve službě Log Analytics jsou obsaženy v [pracovní prostor](../../azure-monitor/platform/manage-access.md). Jak je popsáno v [pracovní prostor Log Analytics a účet Automation](solutions.md#log-analytics-workspace-and-automation-account), pracovní prostor není zahrnutý v řešení pro správu, ale musí existovat před instalací řešení. Pokud není k dispozici, řešení instalace se nezdaří.

Název pracovního prostoru je název každého prostředku Log Analytics. To se provádí v řešení se **pracovní prostor** parametr jako v následujícím příkladu elementu SavedSearch prostředku.

    "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearchId'))]"

## <a name="log-analytics-api-version"></a>Verze rozhraní API pro analýzu protokolů
Všechny prostředky Log Analytics, které jsou definované v šabloně Resource Manageru, mít vlastnost **apiVersion** , který určuje verzi rozhraní API prostředku by měl používat.

Následující tabulka uvádí verze rozhraní API pro prostředek, který používá v tomto příkladu.

| Typ prostředku | Verze API | Dotaz |
|:---|:---|:---|
| savedSearches | 2017-03-15-preview | Událost &#124; kde EventLevelName == "Chyba"  |


## <a name="saved-searches"></a>Uložená hledání
Zahrnout [uložená hledání](../../azure-monitor/log-query/log-query-overview.md) v řešení, které umožňují uživatelům provádět dotazy na data shromážděná z vašeho řešení. Uložená hledání se zobrazí v rámci **uložená hledání** na webu Azure Portal. Uložené výsledky hledání je také nutný pro každou výstrahu.

[Uložené výsledky hledání log Analytics](../../azure-monitor/log-query/log-query-overview.md) prostředky mají typ `Microsoft.OperationalInsights/workspaces/savedSearches` a mají následující strukturu. To zahrnuje společné proměnné a parametry, takže můžete zkopírovat a vložit tento fragment kódu do souboru řešení a změňte názvy parametrů.

    {
        "name": "[concat(parameters('workspaceName'), '/', variables('SavedSearch').Name)]",
        "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "dependsOn": [
        ],
        "tags": { },
        "properties": {
            "etag": "*",
            "query": "[variables('SavedSearch').Query]",
            "displayName": "[variables('SavedSearch').DisplayName]",
            "category": "[variables('SavedSearch').Category]"
        }
    }

Uložené výsledky hledání se jednotlivé vlastnosti je popsané v následující tabulce.

| Vlastnost | Popis |
|:--- |:--- |
| category | Kategorie pro uložené výsledky hledání.  Všechny uložené výsledky hledání ve stejném řešení často sdílí jednu kategorii, takže jsou seskupené dohromady v konzole. |
| DisplayName | Název má být zobrazen pro uložené výsledky hledání na portálu. |
| query | Spustit dotaz. |

> [!NOTE]
> Budete muset použít řídicí znaky v dotazu, pokud obsahuje znaky, které může být interpretován jako JSON. Například, pokud byl váš dotaz **AzureActivity | OperationName:"Microsoft.Compute/virtualMachines/write"**, by měl být zadaný v souboru řešení, které **AzureActivity | OperationName: /\"Microsoft.Compute/virtualMachines/write\"**.


## <a name="next-steps"></a>Další postup
* [Přidání zobrazení](solutions-resources-views.md) do řešení pro správu.
* [Přidání runbooků služeb automatizace a dalším prostředkům](solutions-resources-automation.md) do řešení pro správu.
