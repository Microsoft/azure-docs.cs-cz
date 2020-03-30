---
title: Názory na řešení pro správu | Dokumenty společnosti Microsoft
description: 'Řešení pro správu budou obvykle obsahovat jedno nebo více zobrazení pro vizualizaci dat.  Tento článek popisuje, jak exportovat zobrazení vytvořené návrhářem zobrazení a zahrnout jej do řešení pro správu. '
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2018
ms.openlocfilehash: a9a1c1718fb95a6ace3700af043134072d582473
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663041"
---
# <a name="views-in-management-solutions-preview"></a>Zobrazení v řešeních pro správu (Náhled)
> [!NOTE]
> Toto je předběžná dokumentace pro vytváření řešení pro správu, které jsou aktuálně ve verzi Preview. Jakékoli schéma popsané níže se může změnit.    


[Řešení pro správu](solutions.md) budou obvykle obsahovat jedno nebo více zobrazení pro vizualizaci dat.  Tento článek popisuje, jak exportovat zobrazení vytvořené [návrhářem zobrazení](../../azure-monitor/platform/view-designer.md) a zahrnout jej do řešení pro správu.  

> [!NOTE]
> Ukázky v tomto článku používají parametry a proměnné, které jsou povinné nebo společné pro řešení správy a popsané v [návrhu a sestavení řešení pro správu v Azure](solutions-creating.md)
>
>

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že jste již obeznámeni s tím, jak [vytvořit řešení pro správu](solutions-creating.md) a strukturu souboru řešení.

## <a name="overview"></a>Přehled
Chcete-li zahrnout zobrazení do řešení pro správu, vytvořte pro něj **prostředek** v [souboru řešení](solutions-creating.md).  JSON, který popisuje podrobné konfigurace zobrazení je obvykle složité i když a ne něco, co typický autor řešení by bylo možné vytvořit ručně.  Nejběžnější metodou je vytvoření zobrazení pomocí [Návrháře zobrazení](../../azure-monitor/platform/view-designer.md), exportujte jej a potom přidejte jeho podrobnou konfiguraci do řešení.

Základní kroky pro přidání zobrazení řešení jsou následující.  Každý krok je podrobněji popsán v následujících částech.

1. Exportujte zobrazení do souboru.
2. Vytvořte zdroj zobrazení v řešení.
3. Přidejte podrobnosti o zobrazení.

## <a name="export-the-view-to-a-file"></a>Export zobrazení do souboru
Podle pokynů v [Návrháři zobrazení Analýzy protokolů](../../azure-monitor/platform/view-designer.md) exportujte zobrazení do souboru.  Exportovaný soubor bude ve formátu JSON se [stejnými prvky jako soubor řešení](solutions-solution-file.md).  

Prvek **prostředků** souboru zobrazení bude mít prostředek s typem **Microsoft.OperationalInsights/workspaces,** který představuje pracovní prostor Log Analytics.  Tento prvek bude mít dílčí prvek s typem **zobrazení,** který představuje zobrazení a obsahuje jeho podrobnou konfiguraci.  Zkopírujete podrobnosti tohoto prvku a zkopírujete jej do vašeho řešení.

## <a name="create-the-view-resource-in-the-solution"></a>Vytvoření prostředku zobrazení v řešení
Přidejte následující prostředek zobrazení do prvku **prostředků** souboru řešení.  To používá proměnné, které jsou popsány níže, které je nutné také přidat.  Všimněte si, že **řídicí panel** a **OverviewTile** vlastnosti jsou zástupné symboly, které přepíšete odpovídající vlastnosti z exportovaného souboru zobrazení.

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

Přidejte následující proměnné do prvku proměnných souboru řešení a nahraďte hodnoty na hodnoty pro vaše řešení.

    "LogAnalyticsApiVersion": "<api-version>",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."

Všimněte si, že můžete zkopírovat celý prostředek zobrazení z exportovaného souboru zobrazení, ale budete muset provést následující změny, aby fungoval ve vašem řešení.  

* **Typ** zdroje zobrazení je třeba změnit ze **zobrazení** na **Microsoft.OperationalInsights/workspaces**.
* Vlastnost **názvu** prostředku zobrazení musí být změněna tak, aby zahrnovala název pracovního prostoru.
* Závislost na pracovním prostoru je třeba odebrat, protože prostředek pracovního prostoru není definován v řešení.
* Do zobrazení je třeba přidat vlastnost **DisplayName.**  **Id**, **Name**a **DisplayName** se musí shodovat.
* Názvy parametrů musí být změněny tak, aby odpovídaly požadované sadě parametrů.
* Proměnné by měly být definovány v roztoku a použity v příslušných vlastnostech.

### <a name="log-analytics-api-version"></a>Verze rozhraní API analýzy protokolů
Všechny prostředky Log Analytics definované v šabloně Správce prostředků mají **vlastnost apiVersion,** která definuje verzi rozhraní API, které by měl prostředek používat.  Tato verze se liší pro zobrazení s dotazy, které používají [starší verzi a upgradovaný dotazovací jazyk](../../azure-monitor/log-query/log-query-overview.md).  

 Následující tabulka určuje verze rozhraní API analýzy protokolů pro zobrazení ve starších a upgradovaných pracovních prostorech: 

| Verze pracovního prostoru | Verze rozhraní API | Dotaz |
|:---|:---|:---|
| v1 (starší verze)   | 2015-11-01-náhled | Starší formát.<br> Příklad: Type=Event EventLevelName = Chyba  |
| v2 (inovováno) | 2015-11-01-náhled | Starší formát.  Při instalaci byl převeden do inovovaného formátu.<br> Příklad: Type=Event EventLevelName = Chyba<br>Převedeno na: Událost &#124; kde EventLevelName == "Chyba"  |
| v2 (inovováno) | 2017-03-03-náhled | Formát upgradu. <br>Příklad: Událost &#124; kde EventLevelName == "Chyba"  |


## <a name="add-the-view-details"></a>Přidání podrobností o zobrazení
Prostředek zobrazení v exportovaném souboru zobrazení bude obsahovat dva prvky v elementu **vlastností** s názvem **Dashboard** a **OverviewTile,** které obsahují podrobnou konfiguraci zobrazení.  Zkopírujte tyto dva prvky a jejich obsah do elementu **vlastností** prostředku zobrazení v souboru řešení.

## <a name="example"></a>Příklad
Například následující ukázka ukazuje jednoduchý soubor řešení se zobrazením.  Elipsy (...) jsou zobrazeny pro **řídicí panel** a **PřehledTile** obsah z prostorových důvodů.

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
* Získejte podrobné informace o vytváření [řešení pro správu](solutions-creating.md).
* Zahrňte [runbooky automatizace do svého řešení pro správu](solutions-resources-automation.md).
