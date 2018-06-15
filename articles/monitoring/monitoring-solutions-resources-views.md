---
title: Zobrazení v řešení pro správu | Microsoft Docs
description: 'Řešení pro správu bude obvykle obsahovat jedno nebo více zobrazení, která bude vizualizovat data.  Tento článek popisuje, jak exportovat zobrazení vytvořené pomocí návrháře zobrazení a její zahrnutí do řešení pro správu. '
services: monitoring
documentationcenter: ''
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 570b278c-2d47-4e5a-9828-7f01f31ddf8c
ms.service: monitoring
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/16/2018
ms.author: bwren
ms.openlocfilehash: b4f54358f4bc1db973d6fe7163411e3a313c3cf4
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
ms.locfileid: "33887864"
---
# <a name="views-in-management-solutions-preview"></a>Zobrazení v řešení pro správu (Preview)
> [!NOTE]
> Toto je předběžná dokumentace pro vytváření řešení pro správu, které jsou aktuálně ve verzi preview. Žádné schéma popsané níže se mohou změnit.    


[Řešení pro správu](monitoring-solutions.md) bude obvykle obsahovat jedno nebo více zobrazení, která bude vizualizovat data.  Tento článek popisuje, jak exportovat zobrazení vytvořené [Návrhář zobrazení](../log-analytics/log-analytics-view-designer.md) a její zahrnutí do řešení pro správu.  

> [!NOTE]
> Ukázky v tomto článku použít parametry a proměnné, které jsou nutné nebo společné pro řešení pro správu a jsou popsány v [návrhu a sestavení řešení pro správu v Azure](monitoring-solutions-creating.md)
>
>

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že jste již obeznámeni s postupy [vytvoření řešení správy](monitoring-solutions-creating.md) a struktura soubor řešení.

## <a name="overview"></a>Přehled
Chcete-li zahrnout zobrazení v rámci řešení pro správu, musíte vytvořit **prostředků** pro něj v [soubor řešení](monitoring-solutions-creating.md).  Formátu JSON, který popisuje konfiguraci podrobné zobrazení je obvykle komplexní ale a není něco Autor typické řešení bude moci vytvořit ručně.  Nejběžnější metodou je vytvoření zobrazení pomocí [Návrhář zobrazení](../log-analytics/log-analytics-view-designer.md), ho exportovat a poté přidejte jeho podrobnou konfiguraci k řešení.

Základní postup se zobrazení přidat do řešení se následujícím způsobem.  Každý krok je podrobně popsán dále v následujících částech.

1. Zobrazení exportujte do souboru.
2. Vytvořte prostředek zobrazení v řešení.
3. Přidáte zobrazení podrobností.

## <a name="export-the-view-to-a-file"></a>Zobrazení exportovat do souboru
Postupujte podle pokynů v [Návrhář zobrazení Log Analytics](../log-analytics/log-analytics-view-designer.md) zobrazení exportovat do souboru.  Exportovaný soubor bude ve formátu JSON se stejným [elementy jako soubor řešení](monitoring-solutions-solution-file.md).  

**Prostředky** prvek zobrazení souboru bude mít prostředek s typem **Microsoft.OperationalInsights/workspaces** představující pracovní prostor analýzy protokolů.  Tento prvek bude mít dílčí prvek s typem **zobrazení** , představuje zobrazení a obsahuje podrobné konfiguraci.  Bude kopírovat podrobnosti tohoto elementu a zkopírujte jej do řešení.

## <a name="create-the-view-resource-in-the-solution"></a>Vytvořte prostředek zobrazení v řešení
Přidejte následující zobrazení prostředek, který **prostředky** element souboru řešení.  Tato služba využívá proměnné, které jsou popsané níže, musíte taky přidat.  Všimněte si, že **řídicí panel** a **OverviewTile** vlastnosti jsou zástupné symboly, které bude přepsat odpovídající vlastnosti ze zobrazení exportovaného souboru.

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

Přidejte následující proměnné na prvek proměnné soubor řešení a nahraďte hodnoty, které se pro vaše řešení.

    "LogAnalyticsApiVersion": "<api-version>",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."

Všimněte si, že může kopírovat prostředek celého zobrazení z exportovaného zobrazení souboru, ale budete muset provést následující změny pro ho na spolupráci ve vašem řešení.  

* **Typ** pro zobrazení prostředků je potřeba změnit z **zobrazení** k **Microsoft.OperationalInsights/workspaces**.
* **Název** vlastnost pro prostředek zobrazení musí změnit tak, aby patří název pracovního prostoru.
* Závislost na pracovním prostoru musí odebrat, protože prostředek prostoru není definován v řešení.
* **DisplayName** vlastnost musí být přidán do zobrazení.  **Id**, **název**, a **DisplayName** musí všechny shodovat.
* Názvy parametrů musí změnit tak, aby odpovídaly vyžaduje sadu parametrů.
* Proměnné by měl definovaný v řešení a použít v příslušné vlastnosti.

### <a name="log-analytics-api-version"></a>Verze rozhraní API analýzy protokolů
Všechny prostředky analýzy protokolů, které jsou definované v šabloně Resource Manager mít vlastnost **apiVersion** verzi rozhraní API by měl použít na prostředek, který definuje.  Tato verze je pro zobrazení s dotazy, které používají jiné [starší verze a upgradovaný dotazovací jazyk](../log-analytics/log-analytics-log-search-upgrade.md).  

 Následující tabulka obsahuje verze rozhraní API analýzy protokolů pro zobrazení ve starší verzi a upgradovali pracovních prostorů: 

| Verze pracovního prostoru | Verze API | Dotaz |
|:---|:---|:---|
| V1 (starší)   | 2015-11-01-preview | Starší verze formátu.<br> Příklad: Zadejte = událostí EventLevelName = chyby  |
| v2 (upgradu) | 2015-11-01-preview | Starší verze formátu.  Převedeny na formát upgradované na instalaci.<br> Příklad: Zadejte = událostí EventLevelName = chyby<br>Převést na: událost &#124; kde EventLevelName == "Error.  |
| v2 (upgradu) | 2017-03-03-preview | Upgrade formátu. <br>Příklad: Událost &#124; kde EventLevelName == "Error.  |


## <a name="add-the-view-details"></a>Přidání zobrazení podrobností
Zobrazení prostředků v souboru exportovaný zobrazení bude obsahovat dva elementy v **vlastnosti** element s názvem **řídicí panel** a **OverviewTile** obsahující podrobnou konfiguraci zobrazení.  Zkopírujte tyto dva prvky a jejich obsah do **vlastnosti** element zobrazení prostředku v souboru řešení.

## <a name="example"></a>Příklad:
Například následující příklad ukazuje soubor simple řešení se zobrazením.  Výpustky (...) se zobrazují pro **řídicí panel** a **OverviewTile** obsah z důvodů místa.

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
* Další podrobné informace o vytváření [řešení pro správu](monitoring-solutions-creating.md).
* Zahrnout [runbooků služeb automatizace ve vašem řešení správy](monitoring-solutions-resources-automation.md).
