---
title: Azure Monitor sešitů a šablon Azure Resource Manager
description: Zjednodušení složitých sestav s předem sestavenými a vlastními parametrizovanými Azure Monitor sešity nasazených prostřednictvím šablon Azure Resource Manager
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/30/2020
ms.openlocfilehash: 77190b85da08d09cf05a02dcc5787f0c24229948
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100611692"
---
# <a name="programmatically-manage-workbooks"></a>Správa sešitů prostřednictvím kódu programu

Vlastníci prostředků mají možnost vytvářet a spravovat své sešity prostřednictvím šablon Správce prostředků.

To může být užitečné ve scénářích, jako jsou:
* Nasazení analytických sestav specifických pro organizace nebo domén spolu s nasazeními prostředků. Například můžete nasadit sešity výkonu a selhání specifické pro organizaci pro vaše nové aplikace nebo virtuální počítače.
* Nasazení standardních sestav nebo řídicích panelů pomocí sešitů pro stávající prostředky.

Sešit se vytvoří v požadované skupině sub/Resource-Group a s obsahem zadaným v šablonách Správce prostředků.

Existují dva typy prostředků sešitu, které lze spravovat prostřednictvím kódu programu:
* [Šablony sešitu](#azure-resource-manager-template-for-deploying-a-workbook-template)
* [Instance sešitu](#azure-resource-manager-template-for-deploying-a-workbook-instance)

## <a name="azure-resource-manager-template-for-deploying-a-workbook-template"></a>Šablona Azure Resource Manager pro nasazení šablony sešitu

1. Otevřete sešit, který chcete nasadit programově.
2. Kliknutím na položku panelu nástrojů pro _Úpravy_ přepněte sešit do režimu úprav.
3. Otevřete _Rozšířený editor_ pomocí _</>_ tlačítka na panelu nástrojů.
4. Ujistěte se, že jste na kartě _Šablona Galerie_ .

    ![Karta šablony galerie](./media/workbooks-automate/gallery-template.png)
1. Zkopírujte kód JSON v šabloně galerie do schránky.
2. Níže je příklad šablony Azure Resource Manager, která nasadí šablonu sešitu do Azure Monitor Galerie sešitů. Vložte JSON, který jste zkopírovali místo `<PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>` . Odkaz na Azure Resource Manager šablonu, která vytvoří šablonu sešitu, najdete [tady](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/ARM-template-for-creating-workbook-template).

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "resourceName": {
                "type": "string",
                "defaultValue": "my-workbook-template",
                "metadata": {
                    "description": "The unique name for this workbook template instance"
                }
            }
        },
        "resources": [
            {
                "name": "[parameters('resourceName')]",
                "type": "microsoft.insights/workbooktemplates",
                "location": "[resourceGroup().location]",
                "apiVersion": "2019-10-17-preview",
                "dependsOn": [],
                "properties": {
                    "galleries": [
                        {
                            "name": "A Workbook Template",
                            "category": "Deployed Templates",
                            "order": 100,
                            "type": "workbook",
                            "resourceType": "Azure Monitor"
                        }
                    ],
                    "templateData": <PASTE-COPIED-WORKBOOK_TEMPLATE_HERE>
                }
            }
        ]
    }
    ```
1. V `galleries` objektu vyplňte `name` `category` pomocí vašich hodnot klíče a. Další informace o [parametrech](#parameters) najdete v další části.
2. Tuto šablonu Azure Resource Manager nasaďte buď pomocí [Azure Portal](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), [rozhraní příkazového řádku](../../azure-resource-manager/templates/deploy-cli.md), [PowerShellu](../../azure-resource-manager/templates/deploy-powershell.md)atd.
3. Otevřete Azure Portal a přejděte do galerie sešitů zvolené v šabloně Azure Resource Manager. V ukázkové šabloně přejděte do galerie Azure Monitorového sešitu:
    1. Otevřete Azure Portal a přejděte do Azure Monitor
    2. Otevřít `Workbooks` z obsahu
    3. Najděte šablonu v galerii v kategorii `Deployed Templates` (bude jednou z fialových položek).

### <a name="parameters"></a>Parametry

|Parametry                |Vysvětlení                                                                                             |
|:-------------------------|:-------------------------------------------------------------------------------------------------------|
| `name`                   | Název prostředku šablony sešitu v Azure Resource Manager.                                  |
|`type`                    | Vždy Microsoft. Insights/workbooktemplates                                                            |
| `location`               | Umístění Azure, ve kterém se sešit vytvoří                                               |
| `apiVersion`             | 2019-10-17 Preview                                                                                     |
| `type`                   | Vždy Microsoft. Insights/workbooktemplates                                                            |
| `galleries`              | Sada galerií, ve kterých se má zobrazit tato šablona sešitu                                                |
| `gallery.name`           | Popisný název šablony sešitu v galerii.                                             |
| `gallery.category`       | Skupina v galerii, do které se umístí šablona                                                     |
| `gallery.order`          | Číslo, které určuje pořadí zobrazení šablony v rámci kategorie v galerii. Nižší pořadí znamená vyšší prioritu. |
| `gallery.resourceType`   | Typ prostředku odpovídající galerii. Obvykle se jedná o řetězec typu prostředku, který odpovídá prostředku (například Microsoft. operationalinsights/Workspaces). |
|`gallery.type`            | Označuje se jako typ sešitu tento jedinečný klíč, který rozlišuje galerii v rámci určitého typu prostředku. Application Insights například mají typy `workbook` a `tsg` odpovídají různým galeriím sešitů. |

### <a name="galleries"></a>Galerie

| Galerie                                        | Typ prostředku                                      | Typ sešitu |
| :--------------------------------------------- |:---------------------------------------------------|:--------------|
| Sešity v Azure Monitor                     | `Azure Monitor`                                    | `workbook`    |
| Přehledy virtuálních počítačů v Azure Monitor                   | `Azure Monitor`                                    | `vm-insights` |
| Sešity v pracovním prostoru Log Analytics           | `microsoft.operationalinsights/workspaces`         | `workbook`    |
| Sešity v Application Insights              | `microsoft.insights/component`                     | `workbook`    |
| Průvodci odstraňováním potíží v Application Insights | `microsoft.insights/component`                     | `tsg`         |
| Využití v Application Insights                  | `microsoft.insights/component`                     | `usage`       |
| Sešity ve službě Kubernetes                | `Microsoft.ContainerService/managedClusters`       | `workbook`    |
| Sešity ve skupinách prostředků                   | `microsoft.resources/subscriptions/resourcegroups` | `workbook`    |
| Sešity v Azure Active Directory            | `microsoft.aadiam/tenant`                          | `workbook`    |
| Přehledy virtuálních počítačů ve virtuálních počítačích                | `microsoft.compute/virtualmachines`                | `insights`    |
| Přehledy virtuálních počítačů v sadě Virtual Machine Scale Sets      | `microsoft.compute/virtualmachinescalesets`        | `insights`    |

## <a name="azure-resource-manager-template-for-deploying-a-workbook-instance"></a>Šablona Azure Resource Manager pro nasazení instance sešitu

1. Otevřete sešit, který chcete nasadit programově.
2. Kliknutím na položku panelu nástrojů pro _Úpravy_ přepněte sešit do režimu úprav.
3. Otevřete _Rozšířený editor_ pomocí _</>_ tlačítka na panelu nástrojů.
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
| `workbookType` | Galerie, pod kterou se sešit zobrazí Mezi podporované hodnoty patří sešit, `tsg` , Azure monitor atd. |
| `workbookSourceId` | ID instance prostředku, ke které bude sešit přidružen Nový sešit se zobrazí v souvislosti s touto instancí prostředků, například v tabulce obsahu prostředku v části _sešit_. Pokud chcete, aby se váš sešit zobrazoval v galerii sešitů v Azure Monitor, použijte místo ID prostředku _Azure monitor_ řetězce. |
| `workbookId` | Jedinečný identifikátor GUID pro tuto instanci sešitu K automatickému vytvoření nového identifikátoru GUID použijte _[newGuid ()]_ . |
| `kind` | Slouží k určení, jestli je vytvořený sešit sdílený nebo soukromý. Použijte hodnotu _sdílenou_ pro sdílené sešity a _uživatele_ pro soukromé objekty. |
| `location` | Umístění Azure, ve kterém se sešit vytvoří. Pokud chcete vytvořit ve stejném umístění jako skupinu prostředků, použijte _[resourceName (). Location]._ |
| `serializedData` | Obsahuje obsah nebo datovou část, která se má použít v sešitu. K získání hodnoty použijte šablonu Správce prostředků z uživatelského rozhraní sešity. |

### <a name="workbook-types"></a>Typy sešitů
Typy sešitu: Určete, v jakém typu Galerie sešitů se bude nová instance sešitu zobrazovat. Vaše možnosti jsou:

| Typ | Umístění galerie |
| :------------- |:-------------|
| `workbook` | Výchozí použití ve většině sestav, včetně galerie sešitů Application Insights, Azure Monitor atd.  |
| `tsg` | Galerie průvodců odstraňováním potíží v Application Insights |
| `usage` | _Další_ galerie v části _použití_ v Application Insights |

### <a name="limitations"></a>Omezení
Z technického důvodu Tento mechanismus nelze použít k vytvoření instancí sešitu v galerii _sešitů_ Application Insights. Pracujeme na řešení tohoto omezení. Mezitím doporučujeme, abyste použili galerii Průvodce odstraňováním potíží (workbookType: `tsg` ) k nasazení Application Insights souvisejících sešitů.

## <a name="next-steps"></a>Další kroky

Prozkoumejte, jak se používají sešity k napájení nových [Azure monitor pro prostředí úložiště](../insights/storage-insights-overview.md).
