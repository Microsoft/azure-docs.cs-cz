---
title: Zobrazení v řešení pro správu | Dokumentace Microsoftu
description: 'Řešení pro správu obvykle zahrnují jeden nebo více zobrazení k vizualizaci dat.  Tento článek popisuje, jak exportovat zobrazení vytvořené návrhářem zobrazení a zahrnout do řešení pro správu. '
services: monitoring
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 570b278c-2d47-4e5a-9828-7f01f31ddf8c
ms.service: monitoring
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/16/2018
ms.author: bwren
ms.openlocfilehash: 6e0bab08e52233d10cff3ec936d024dcbb86d2a6
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53188006"
---
# <a name="views-in-management-solutions-preview"></a>Zobrazení v řešení pro správu (Preview)
> [!NOTE]
> Toto je předběžná dokumentace pro vytváření řešení pro správu, které jsou aktuálně ve verzi preview. Žádné schéma je popsáno níže se může změnit.    


[Řešení pro správu](solutions.md) bude obvykle obsahovat jedno nebo více zobrazení, která bude vizualizovat data.  Tento článek popisuje, jak exportovat zobrazení vytvořené [Návrhář zobrazení](../../azure-monitor/platform/view-designer.md) a zahrnout do řešení pro správu.  

> [!NOTE]
> Ukázky v tomto článku použijte parametry a proměnné, které jsou povinné nebo společné pro řešení pro správu a jsou popsány v [návrh a sestavení řešení pro správu v Azure](solutions-creating.md)
>
>

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že jste již obeznámeni s postupy [vytvořte řešení pro správu](solutions-creating.md) a struktura souboru řešení.

## <a name="overview"></a>Přehled
V rámci řešení pro správu zahrnují zobrazení, vytvoření **prostředků** pro něj v [soubor řešení](solutions-creating.md).  Ve formátu JSON, který popisuje podrobnou konfiguraci zobrazení je obvykle složitý ale a nemít něco, že autor typické řešení budou moct vytvořit ručně.  Nejběžnější metodou je vytvořte pomocí zobrazení [Návrhář zobrazení](../../azure-monitor/platform/view-designer.md), exportujte ho a pak přidejte jeho podrobnou konfiguraci řešení.

Základní postup pro přidání do řešení zobrazení jsou následující.  Každý krok je podrobně popsán dále v následujících částech.

1. Zobrazení exportujte do souboru.
2. Vytvořte zobrazení prostředků v řešení.
3. Přidáním podrobností o zobrazení.

## <a name="export-the-view-to-a-file"></a>Zobrazení exportovat do souboru
Postupujte podle pokynů na adrese [Návrhář zobrazení Log Analytics](../../azure-monitor/platform/view-designer.md) zobrazení export do souboru.  Exportovaný soubor bude ve formátu JSON se stejným [prvky jako soubor řešení](solutions-solution-file.md).  

**Prostředky** element zobrazit soubor bude mít prostředek typu **Microsoft.OperationalInsights/workspaces** , která představuje pracovní prostor Log Analytics.  Tento element bude mít dílčí element typu **zobrazení** , který představuje zobrazení a obsahuje jeho podrobnou konfiguraci.  Bude kopírovat podrobnosti tohoto elementu a zkopírujte je do vašeho řešení.

## <a name="create-the-view-resource-in-the-solution"></a>Vytvořit zobrazení prostředků v řešení
Přidejte následující zobrazení prostředku **prostředky** prvek souboru řešení.  Tady se používá proměnné, které jsou popsané níže, musíte taky přidat.  Všimněte si, že **řídicí panel** a **OverviewTile** vlastnosti jsou zástupné symboly, které se přepíšou s odpovídající vlastností ze souboru exportovaného zobrazení.

    {
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
        "type": "Microsoft.OperationalInsights/workspaces/views",
        "location": "[parameters('workspaceregionId')]",
        "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
        "dependson": [
            ],
        "properties": {
            "Id": "[variables('ViewName')]",
            "Name": "[variables('ViewName')]",
            "DisplayName": "[variables('ViewName')]",
            "Description": "",
            "Author": "[variables('ViewAuthor')]",
            "Source": "Local",
            "Dashboard": ,
            "OverviewTile":
        }
    }

Přidejte následující proměnné na proměnné prvek souboru řešení a nahraďte hodnoty tak, aby pro vaše řešení.

    "LogAnalyticsApiVersion": "<api-version>",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."

Všimněte si, že celé zobrazení prostředků lze zkopírovat ze souboru exportovaného zobrazení, ale je třeba provést následující změny, aby to fungovalo ve vašem řešení.  

* **Typ** zobrazení potřeba změnit z prostředků **zobrazení** k **Microsoft.OperationalInsights/workspaces**.
* **Název** vlastnost pro zobrazení prostředků je potřeba změnit tak, aby obsahovaly název pracovního prostoru.
* Závislost na pracovní prostor je potřeba odebrat, protože prostředek pracovní prostor není definovaný v řešení.
* **DisplayName** vlastnost musí být přidán do zobrazení.  **Id**, **název**, a **DisplayName** musí všechny odpovídaly.
* Názvy parametrů musí změnit tak, aby odpovídaly vyžaduje sadu parametrů.
* Proměnné by měl definovaný v řešení a použít v příslušné vlastnosti.

### <a name="log-analytics-api-version"></a>Verze rozhraní API pro analýzu protokolů
Všechny prostředky Log Analytics, které jsou definované v šabloně Resource Manageru, mít vlastnost **apiVersion** , který určuje verzi rozhraní API prostředku by měl používat.  Tato verze se liší pro zobrazení s dotazy, které používají [starší verze a upgradované dotazovací jazyk](../../azure-monitor/log-query/log-query-overview.md).  

 Následující tabulka obsahuje verze rozhraní API služby Log Analytics pro zobrazení v pracovních prostorech starších verzí a upgradovat: 

| Verze v pracovním prostoru | Verze API | Dotaz |
|:---|:---|:---|
| V1 (starší verze)   | 2015-11-01-preview | Starší verze formátu.<br> Příklad: Typ = EventLevelName události = chyba  |
| v2 (upgradovat) | 2015-11-01-preview | Starší verze formátu.  Převést na formát upgradovaný při instalaci.<br> Příklad: Typ = EventLevelName události = chyba<br>Převést na: Událost &#124; kde EventLevelName == "Chyba"  |
| v2 (upgradovat) | 2017-03-03-preview | Upgrade formátu. <br>Příklad: Událost &#124; kde EventLevelName == "Chyba"  |


## <a name="add-the-view-details"></a>Přidáním podrobností o zobrazení
Zobrazit prostředek v souboru exportované zobrazení bude obsahovat dva prvky v **vlastnosti** element s názvem **řídicí panel** a **OverviewTile** obsahující na podrobné Konfigurace zobrazení.  Zkopírujte tyto dva prvky a jejich obsah do **vlastnosti** element zobrazení prostředku v souboru řešení.

## <a name="example"></a>Příklad:
Například následující příklad ukazuje soubor jednoduchého řešení pomocí zobrazení.  Symbol tří teček (...) jsou platné pro **řídicí panel** a **OverviewTile** obsah z důvodu místa.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string"
            },
            "accountName": {
                "type": "string"
            },
            "workspaceRegionId": {
                "type": "string"
            },
            "regionId": {
                "type": "string"
            },
            "pricingTier": {
                "type": "string"
            }
        },
        "variables": {
            "SolutionVersion": "1.1",
            "SolutionPublisher": "Contoso",
            "SolutionName": "Contoso Solution",
            "LogAnalyticsApiVersion": "2015-11-01-preview",
            "ViewAuthor":  "user@contoso.com",
            "ViewDescription":  "This is a sample view.",
            "ViewName":  "Contoso View"
        },
        "resources": [
            {
                "name": "[concat(variables('SolutionName'), '(' ,parameters('workspacename'), ')')]",
                "location": "[parameters('workspaceRegionId')]",
                "tags": { },
                "type": "Microsoft.OperationsManagement/solutions",
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspacename'), '/views/', variables('ViewName'))]"
                ],
                "properties": {
                    "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspacename'))]",
                    "referencedResources": [
                    ],
                    "containedResources": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
                    ]
                },
                "plan": {
                    "name": "[concat(variables('SolutionName'), '(' ,parameters('workspaceName'), ')')]",
                    "Version": "[variables('SolutionVersion')]",
                    "product": "ContosoSolution",
                    "publisher": "[variables('SolutionPublisher')]",
                    "promotionCode": ""
                }
            },
            {
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
                "type": "Microsoft.OperationalInsights/workspaces/views",
                "location": "[parameters('workspaceregionId')]",
                "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
                "dependson": [
                ],
                "properties": {
                    "Id": "[variables('ViewName')]",
                    "Name": "[variables('ViewName')]",
                    "DisplayName": "[variables('ViewName')]",
                    "Description": "[variables('ViewDescription')]",
                    "Author": "[variables('ViewAuthor')]",
                    "Source": "Local",
                    "Dashboard": ...,
                    "OverviewTile": ...
                }
            }
          ]
    }




## <a name="next-steps"></a>Další postup
* Přečtěte si podrobné informace o vytváření [řešení pro správu](solutions-creating.md).
* Zahrnout [runbooků služeb automatizace ve vašem řešení správy](solutions-resources-automation.md).
