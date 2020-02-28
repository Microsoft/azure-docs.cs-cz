---
title: Zobrazení v řešení pro správu | Microsoft Docs
description: 'Řešení pro správu obvykle zahrnují jedno nebo více zobrazení k vizualizaci dat.  Tento článek popisuje, jak exportovat zobrazení vytvořené návrhářem zobrazení a zahrnout ho do řešení pro správu. '
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2018
ms.openlocfilehash: a9a1c1718fb95a6ace3700af043134072d582473
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77663041"
---
# <a name="views-in-management-solutions-preview"></a>Zobrazení v řešeních pro správu (Preview)
> [!NOTE]
> Toto je předběžná dokumentace k vytváření řešení pro správu, která jsou momentálně ve verzi Preview. Jakékoli schéma popsané níže se může změnit.    


[Řešení pro správu](solutions.md) obvykle zahrnují jedno nebo více zobrazení k vizualizaci dat.  Tento článek popisuje, jak exportovat zobrazení vytvořené [návrhářem zobrazení](../../azure-monitor/platform/view-designer.md) a zahrnout ho do řešení pro správu.  

> [!NOTE]
> V ukázkách v tomto článku se používají parametry a proměnné, které jsou buď vyžadované, nebo běžné pro řešení pro správu, popsaná v článku [Návrh a sestavení řešení pro správu v Azure](solutions-creating.md) .
>
>

## <a name="prerequisites"></a>Požadavky
V tomto článku se předpokládá, že už jste obeznámeni s tím, jak [vytvořit řešení pro správu](solutions-creating.md) a strukturu souboru řešení.

## <a name="overview"></a>Přehled
Chcete-li zahrnout zobrazení do řešení pro správu, vytvořte pro něj **prostředek** v [souboru řešení](solutions-creating.md).  KÓD JSON, který popisuje podrobnou konfiguraci zobrazení, je obvykle složitý, ale není to něco, co by bylo možné vytvořit pomocí typického autora řešení ručně.  Nejběžnější metodou je vytvořit zobrazení pomocí [návrháře zobrazení](../../azure-monitor/platform/view-designer.md), exportovat ho a pak přidat do řešení jeho podrobnou konfiguraci.

Postup pro přidání zobrazení do řešení je základní:  Jednotlivé kroky jsou podrobněji popsané v následujících částech.

1. Exportujte zobrazení do souboru.
2. Vytvořte prostředek zobrazení v řešení.
3. Přidejte podrobnosti zobrazení.

## <a name="export-the-view-to-a-file"></a>Exportovat zobrazení do souboru
Pokud chcete exportovat zobrazení do souboru, postupujte podle pokynů v tématu [Log Analytics Návrhář zobrazení](../../azure-monitor/platform/view-designer.md) .  Exportovaný soubor bude ve formátu JSON se stejnými [prvky jako soubor řešení](solutions-solution-file.md).  

Element **Resources** souboru zobrazení bude mít prostředek s typem **Microsoft. OperationalInsights/Workspaces** , který představuje pracovní prostor Log Analytics.  Tento prvek bude mít dílčí prvek s typem **zobrazení** , které představuje zobrazení a obsahuje jeho podrobnou konfiguraci.  Budete Kopírovat podrobnosti tohoto prvku a pak ho zkopírovat do svého řešení.

## <a name="create-the-view-resource-in-the-solution"></a>Vytvoření prostředku zobrazení v řešení
Přidejte následující prostředek zobrazení do elementu **Resources** souboru řešení.  Používá proměnné, které jsou popsány níže, je také nutné přidat.  Všimněte si, že vlastnosti **řídicího panelu** a **OverviewTile** jsou zástupné symboly, které budete přepsat odpovídajícími vlastnostmi z exportovaného souboru zobrazení.

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

Přidejte následující proměnné do prvku proměnné souboru řešení a nahraďte hodnoty hodnotami pro vaše řešení.

    "LogAnalyticsApiVersion": "<api-version>",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."

Všimněte si, že můžete zkopírovat celý prostředek zobrazení z exportovaného souboru zobrazení, ale pokud chcete, aby fungoval ve vašem řešení, je třeba provést následující změny.  

* **Typ** pro prostředek zobrazení musí být změněn z **zobrazení** na **Microsoft. OperationalInsights/Workspaces**.
* Vlastnost **Name** prostředku zobrazení musí být změněna tak, aby zahrnovala název pracovního prostoru.
* Závislost v pracovním prostoru je nutné odebrat, protože prostředek pracovního prostoru není v řešení definován.
* Vlastnost **DisplayName** je třeba přidat do zobrazení.  **ID**, **název**a **DisplayName** musí odpovídat všem.
* Názvy parametrů se musí změnit tak, aby odpovídaly požadované sadě parametrů.
* Proměnné by měly být definovány v řešení a použity v příslušných vlastnostech.

### <a name="log-analytics-api-version"></a>Verze rozhraní API pro Log Analytics
Všechny prostředky Log Analytics definované v šabloně Správce prostředků mají vlastnost **apiVersion** , která definuje verzi rozhraní API, kterou by měl prostředek použít.  Tato verze se liší v zobrazeních s dotazy, které používají [starší verze a upgradovaný dotazovací jazyk](../../azure-monitor/log-query/log-query-overview.md).  

 Následující tabulka uvádí Log Analytics verze rozhraní API pro zobrazení ve starších a upgradovaných pracovních prostorech: 

| Verze v pracovním prostoru | Verze rozhraní API | Dotaz |
|:---|:---|:---|
| V1 (starší verze)   | 2015-11-01-preview | Starší verze formátu.<br> Příklad: Type = Event EventLevelName = Error  |
| v2 (upgradovaný) | 2015-11-01-preview | Starší verze formátu.  Převedeno na upgradovaný formát při instalaci.<br> Příklad: Type = Event EventLevelName = Error<br>Převedeno na: &#124; Event, kde EventLevelName = = "Error"  |
| v2 (upgradovaný) | 2017-03-03 – Preview | Formát upgradu. <br>Příklad: událost &#124; , kde EventLevelName = = "Error"  |


## <a name="add-the-view-details"></a>Přidat podrobnosti zobrazení
Prostředek zobrazení v souboru s vyexportovaným zobrazením bude obsahovat dva prvky v elementu **Properties** s názvem **Dashboard** a **OverviewTile** obsahující podrobnou konfiguraci zobrazení.  Zkopírujte tyto dva prvky a jejich obsah do elementu **Properties (vlastnosti** ) prostředku zobrazení v souboru řešení.

## <a name="example"></a>Příklad
Například následující příklad ukazuje jednoduchý soubor řešení se zobrazením.  Zobrazí se tři tečky (...) pro **řídicí panel** a obsah **OverviewTile** z důvodu místa.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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




## <a name="next-steps"></a>Další kroky
* Přečtěte si podrobné informace o vytváření [řešení pro správu](solutions-creating.md).
* Zahrňte [do řešení pro správu Runbooky Automation](solutions-resources-automation.md).
