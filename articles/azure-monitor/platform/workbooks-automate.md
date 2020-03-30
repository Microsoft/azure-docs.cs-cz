---
title: Azure Monitor Sešity a šablony Azure Resource Manager
description: Zjednodušení složitých sestav pomocí předem vytvořených a vlastních parametrizovaných sešitů Azure Monitor nasazených prostřednictvím šablon Azure Resource Manageru
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 2c2d70d1c945e700a3fa42609f8aa0e1607ba77c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658400"
---
# <a name="programmatically-manage-workbooks"></a>Programová správa sešitů

Vlastníci prostředků mají možnost vytvářet a spravovat své sešity programově prostřednictvím šablon Správce prostředků. 

To může být užitečné ve scénářích, jako jsou:
* Nasazení analytických sestav specifických pro danou na ormu nebo domény spolu s nasazeními prostředků. Můžete například nasadit sešity výkonu a selhání specifické pro konkrétní pro vaše nové aplikace nebo virtuální počítače.
* Nasazení standardních sestav nebo řídicích panelů pomocí sešitů pro existující prostředky.

Sešit bude vytvořen v požadované podskupině/skupině prostředků a s obsahem určeným v šablonách Správce prostředků.

## <a name="azure-resource-manager-template-for-deploying-workbooks"></a>Šablona Azure Resource Manager pro nasazení sešitů
1. Otevřete sešit, který chcete nasadit programově.
2. Přepnutí sešitu do režimu úprav kliknutím na položku panelu nástrojů _úpravy_
3. Otevřete _rozšířený editor_ _</>_ pomocí tlačítka na panelu nástrojů.
4. V editoru přepněte _typ šablony_ na _šablonu Správce prostředků_.
5. Šablona Správce prostředků pro vytváření se zobrazí v editoru. Zkopírujte obsah a použijte tak, jak je, nebo jej sloučit s větší šablonou, která také nasazuje cílový prostředek.

    ![Obrázek znázorňující, jak získat šablonu Správce prostředků z uživatelského uživatelského nastavení sešitu](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>Ukázka šablony Azure Resource Manager
Tato šablona ukazuje, jak nasadit jednoduchý sešit, který zobrazuje "Hello World!".
```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workbookDisplayName":  {             
            "type":"string",
            "defaultValue": "My Workbook",
            "metadata": {
                "description": "The friendly name for the workbook that is used in the Gallery or Saved List. Needs to be unique in the scope of the resource group and source" 
            }
        },
        "workbookType":  {             
            "type":"string",
            "defaultValue": "tsg",
            "metadata": {
                "description": "The gallery that the workbook will been shown under. Supported values include workbook, `tsg`, Azure Monitor, etc." 
            }
        },
        "workbookSourceId":  {             
            "type":"string",
            "defaultValue": "<insert-your-resource-id-here>",
            "metadata": {
                "description": "The id of resource instance to which the workbook will be associated" 
            }
        },
        "workbookId": {
            "type":"string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "The unique guid for this workbook instance" 
            }
        }
    },    
    "resources": [
        {
            "name": "[parameters('workbookId')]",
            "type": "Microsoft.Insights/workbooks",
            "location": "[resourceGroup().location]",
            "kind": "shared",
            "apiVersion": "2018-06-17-preview",
            "dependsOn": [],
            "properties": {
                "displayName": "[parameters('workbookDisplayName')]",
                "serializedData": "{\"version\":\"Notebook/1.0\",\"items\":[{\"type\":1,\"content\":\"{\\\"json\\\":\\\"Hello World!\\\"}\",\"conditionalVisibility\":null}],\"isLocked\":false}",
                "version": "1.0",
                "sourceId": "[parameters('workbookSourceId')]",
                "category": "[parameters('workbookType')]"
            }
        }
    ],
    "outputs": {
        "workbookId": {
            "type": "string",
            "value": "[resourceId( 'Microsoft.Insights/workbooks', parameters('workbookId'))]"
        }
    }
}
```

### <a name="template-parameters"></a>Parametry šablony

| Parametr | Vysvětlení |
| :------------- |:-------------|
| `workbookDisplayName` | Popisný název sešitu, který se používá v galerii nebo v uloženém seznamu. Musí být jedinečný v rozsahu skupiny prostředků a zdroje. |
| `workbookType` | Galerie, pod kterou bude sešit zobrazen. Mezi podporované hodnoty `tsg`patří sešit , Azure Monitor atd. |
| `workbookSourceId` | ID instance prostředku, ke kterému bude sešit přidružen. Nový sešit se zobrazí v souvislosti s touto instancí zdrojů – například v tabulce obsahu zdroje v části _Sešit_. Pokud chcete, aby se váš sešit zobrazoval v galerii sešitů ve službě Azure Monitor, použijte místo ID prostředku řetězec _Azure Monitor._ |
| `workbookId` | Jedinečný identifikátor GUID pro tuto instanci sešitu. Pomocí _[newGuid()]_ automaticky vytvořte nový identifikátor GUID. |
| `kind` | Slouží k určení, zda je vytvořený sešit sdílený nebo soukromý. Použijte _sdílenou_ hodnotu pro sdílené sešity a _uživatele_ pro soukromé. |
| `location` | Umístění Azure, kde se vytvoří sešit. K vytvoření ve stejném umístění jako skupina prostředků použijte _[resourceGroup().location]_ |
| `serializedData` | Obsahuje obsah nebo datovou část, která má být použita v sešitu. Použití šablony Správce prostředků z uživatelského uživatelského nastavení sešitů k získání hodnoty |

### <a name="workbook-types"></a>Typy sešitů
Typy sešitů určují, který typ galerie sešitu se zobrazí v části. Mezi možnosti patří:

| Typ | Umístění galerie |
| :------------- |:-------------|
| `workbook` | Výchozí nastavení se používá ve většině sestav, včetně galerie sešitů application insights, Azure Monitor atd.  |
| `tsg` | Galerie Průvodci odstraňováním potíží v přehledech aplikací |
| `usage` | Galerie _Další_ v části _Využití_ v přehledech aplikací |

### <a name="limitations"></a>Omezení
Z technického důvodu tento mechanismus nelze použít k vytvoření instance sešitu v galerii _sešitů_ Application Insights. Pracujeme na řešení tohoto omezení. Mezitím doporučujeme použít galerii průvodce odstraňováním potíží (sešittype: `tsg`) k nasazení sešitů souvisejících s aplikačními přehledy.

## <a name="next-steps"></a>Další kroky

Zjistěte, jak se sešity používají k napájení nového [prostředí Azure Monitor for Storage](../insights/storage-insights-overview.md).

