---
title: Azure Monitor sešitů a šablon Azure Resource Manager
description: Zjednodušení složitých sestav s předem sestavenými a vlastními parametrizovanými Azure Monitor sešity nasazených prostřednictvím šablon Azure Resource Manager
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: e3d29beab7b1d82f73c706897af9650b68be11f5
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872906"
---
# <a name="programmatically-manage-workbooks"></a>Správa sešitů prostřednictvím kódu programu

Vlastníci prostředků mají možnost vytvářet a spravovat své sešity prostřednictvím šablon Správce prostředků. 

To může být užitečné ve scénářích, jako jsou:
* Nasazení analytických sestav specifických pro organizace nebo domén spolu s nasazeními prostředků. Například můžete nasadit sešity výkonu a selhání specifické pro organizaci pro vaše nové aplikace nebo virtuální počítače.
* Nasazení standardních sestav nebo řídicích panelů pomocí sešitů pro stávající prostředky.

Sešit se vytvoří v požadované skupině sub/Resource-Group a s obsahem zadaným v šablonách Správce prostředků.

## <a name="azure-resource-manager-template-for-deploying-workbooks"></a>Šablona Azure Resource Manager pro nasazení sešitů
1. Otevřete sešit, který chcete nasadit programově.
2. Kliknutím na položku panelu nástrojů pro _Úpravy_ přepněte sešit do režimu úprav.
3. Otevřete _Rozšířený editor_ pomocí tlačítka _</>_ na panelu nástrojů.
4. V editoru přepněte _typ šablony_ na _Správce prostředků šablona_.
5. Šablona Správce prostředků pro vytváření se zobrazí v editoru. Zkopírujte obsah a použijte je nebo ho slučte s větší šablonou, která také nasadí cílový prostředek.

    ![Obrázek znázorňující, jak získat šablonu Správce prostředků v uživatelském rozhraní sešitu](./media/workbooks-automate/programmatic-template.png)

## <a name="sample-azure-resource-manager-template"></a>Ukázka šablony Azure Resource Manager
V této šabloně se dozvíte, jak nasadit jednoduchý sešit, který zobrazuje Hello World!
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
| `workbookDisplayName` | Popisný název sešitu, který se používá v galerii nebo v seznamu uložených souborů. Musí být jedinečný v rozsahu skupiny prostředků a zdroje. |
| `workbookType` | Galerie, pod kterou se sešit zobrazí Mezi podporované hodnoty patří sešit, `tsg`, Azure Monitor atd. |
| `workbookSourceId` | ID instance prostředku, ke které bude sešit přidružen Nový sešit se zobrazí v souvislosti s touto instancí prostředků, například v tabulce obsahu prostředku v části _sešit_. Pokud chcete, aby se váš sešit zobrazoval v galerii sešitů v Azure Monitor, použijte místo ID prostředku _Azure monitor_ řetězce. |
| `workbookId` | Jedinečný identifikátor GUID pro tuto instanci sešitu K automatickému vytvoření nového identifikátoru GUID použijte _[newGuid ()]_ . |
| `kind` | Slouží k určení, jestli je vytvořený sešit sdílený nebo soukromý. Použijte hodnotu _sdílenou_ pro sdílené sešity a _uživatele_ pro soukromé objekty. |
| `location` | Umístění Azure, ve kterém se sešit vytvoří. Pokud chcete vytvořit ve stejném umístění jako skupinu prostředků, použijte _[resourceName (). Location]._ |
| `serializedData` | Obsahuje obsah nebo datovou část, která se má použít v sešitu. K získání hodnoty použijte šablonu Správce prostředků z uživatelského rozhraní sešity. |

### <a name="workbook-types"></a>Typy sešitů
Typy sešitu: Určete, v jakém typu Galerie sešitů se bude nová instance sešitu zobrazovat. Mezi možnosti patří:

| Typ | Umístění galerie |
| :------------- |:-------------|
| `workbook` | Výchozí použití ve většině sestav, včetně galerie sešitů Application Insights, Azure Monitor atd.  |
| `tsg` | Galerie průvodců odstraňováním potíží v Application Insights |
| `usage` | _Další_ galerie v části _použití_ v Application Insights |

### <a name="limitations"></a>Omezení
Z technického důvodu Tento mechanismus nelze použít k vytvoření instancí sešitu v galerii _sešitů_ Application Insights. Pracujeme na řešení tohoto omezení. Mezitím doporučujeme, abyste k nasazení Application Insights souvisejících sešitů použili galerii Průvodce odstraňováním potíží (workbookType: `tsg`).

## <a name="next-steps"></a>Další kroky

Prozkoumejte, jak se používají sešity k napájení nových [Azure monitor pro prostředí úložiště](../insights/storage-insights-overview.md).

