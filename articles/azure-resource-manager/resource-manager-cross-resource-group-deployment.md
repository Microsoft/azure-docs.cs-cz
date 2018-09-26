---
title: Nasazujte prostředky Azure na několika předplatného a skupiny prostředků. | Dokumentace Microsoftu
description: Ukazuje, jak cílit na více než jeden Azure předplatného a skupiny prostředků během nasazování.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/02/2018
ms.author: tomfitz
ms.openlocfilehash: fec075a744b5f47a4be7f1b960cceedfea7b9a2c
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47090788"
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Nasazujte prostředky Azure k více než jedno předplatné nebo skupinu prostředků

Obvykle nasadit všechny prostředky ve vaší šabloně do jediné [skupiny prostředků](resource-group-overview.md). Existují ale scénáře, ve které chcete nasadit sadu prostředků společně, ale je umístit do různých skupin prostředků nebo předplatných. Chcete například nasadit zálohování virtuálního počítače pro Azure Site Recovery do samostatné skupiny prostředků a umístění. Resource Manager vám umožňuje používat vnořené šablony pro cíl různých předplatných a skupin prostředků než dané předplatné a skupinu prostředků použitou pro nadřazené šablony.

> [!NOTE]
> Můžete nasadit do pouze pět skupin prostředků v jednom nasazení. Toto omezení obvykle znamená, že můžete nasadit do jedné skupiny prostředků pro nadřazené šablony a až čtyř skupin prostředků v nasazeních vnořenou nebo propojenou určena. Ale pokud nadřazené šablony obsahuje pouze vnořenou nebo propojenou šablony a nemá sama nasadit všechny prostředky, pak můžete vytvořit až pět skupin prostředků v nasazeních vnořenou nebo propojenou.

## <a name="specify-a-subscription-and-resource-group"></a>Zadejte předplatné a skupinu prostředků

Cílit na jiný prostředek, použijte vnořenou nebo propojenou šablonu. `Microsoft.Resources/deployments` Poskytuje parametry pro typ prostředku `subscriptionId` a `resourceGroup`. Tyto vlastnosti umožňují zadat jiné předplatné a skupinu prostředků pro vnořené nasazení. Všechny skupiny prostředků musí existovat před spuštěním nasazení. Pokud nezadáte předplatné ID nebo skupinu prostředků, předplatné a skupinu prostředků z nadřazené šablony se používá.

Účet, který použijete k nasazení šablony musí mít oprávnění k nasazení na ID předplatného. Pokud zadaný odběr existuje v jiném tenantovi Azure Active Directory, je nutné [přidat uživatele typu Host z jiného adresáře](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).

Pokud chcete nastavit jinou skupinu prostředků a předplatném, použijte:

```json
"resources": [
    {
        "apiVersion": "2017-05-10",
        "name": "nestedTemplate",
        "type": "Microsoft.Resources/deployments",
        "resourceGroup": "[parameters('secondResourceGroup')]",
        "subscriptionId": "[parameters('secondSubscriptionID')]",
        ...
    }
]
```

Pokud vaše skupiny prostředků jsou ve stejném předplatném, můžete odebrat **subscriptionId** hodnotu.

Tento příklad nasadí dva účty úložiště – jeden ve skupině prostředků, které se zadávají během nasazení, a jednu skupinu prostředků podle `secondResourceGroup` parametr:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        },
        "secondResourceGroup": {
            "type": "string"
        },
        "secondSubscriptionID": {
            "type": "string",
            "defaultValue": ""
        },
        "secondStorageLocation": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "firstStorageName": "[concat(parameters('storagePrefix'), uniqueString(resourceGroup().id))]",
        "secondStorageName": "[concat(parameters('storagePrefix'), uniqueString(parameters('secondSubscriptionID'), parameters('secondResourceGroup')))]"
    },
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "[parameters('secondResourceGroup')]",
            "subscriptionId": "[parameters('secondSubscriptionID')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "name": "[variables('secondStorageName')]",
                            "apiVersion": "2017-06-01",
                            "location": "[parameters('secondStorageLocation')]",
                            "sku":{
                                "name": "Standard_LRS"
                            },
                            "kind": "Storage",
                            "properties": {
                            }
                        }
                    ]
                },
                "parameters": {}
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('firstStorageName')]",
            "apiVersion": "2017-06-01",
            "location": "[resourceGroup().location]",
            "sku":{
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        }
    ]
}
```

Pokud nastavíte `resourceGroup` na název skupiny prostředků, která neexistuje, nasazení se nezdaří.

## <a name="use-the-resourcegroup-and-subscription-functions"></a>Funkce resourceGroup() a subscription()

Pro různé nasazeními skupin prostředků [resourceGroup()](resource-group-template-functions-resource.md#resourcegroup) a [subscription()](resource-group-template-functions-resource.md#subscription) přeložit odlišně závislosti na tom, jak zadat vnořené šablony funkce. 

Je-li vložit jednu šablonu v rámci jiné šablony funkce v vnořené šablony přeložit na nadřazenou skupinu prostředků a předplatném. Vložené šablony používá následující formát:

```json
"apiVersion": "2017-05-10",
"name": "embeddedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "template": {
        ...
        resourceGroup() and subscription() refer to parent resource group/subscription
    }
}
```

Pokud jste do samostatné šablony, funkce v propojené šablony řešení ke skupině vnořeného prostředku a předplatné. Propojené šablony používá následující formát:

```json
"apiVersion": "2017-05-10",
"name": "linkedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "templateLink": {
        ...
        resourceGroup() and subscription() in linked template refer to linked resource group/subscription
    }
}
```

## <a name="example-templates"></a>Příklad šablony

Následující šablony ukazují více nasazeními skupin prostředků. Skripty pro nasazení šablony se zobrazí pod tabulkou.

|Šablona  |Popis  |
|---------|---------|
|[Různé šablony předplatného](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crosssubscription.json) |Jeden účet úložiště do jedné skupiny prostředků a jeden účet úložiště nasadí do druhé skupiny prostředků. Zahrnout hodnotu pro ID předplatného, když druhý skupina prostředků je v jiném předplatném. |
|[Různé vlastnosti šablony skupiny prostředků](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) |Ukazuje, jak `resourceGroup()` funkce odstraňuje. Všechny prostředky není nasazena. |

### <a name="powershell"></a>PowerShell

Pro nasazení dva účty úložiště do dvou skupin prostředků v prostředí PowerShell **předplatným**, použijte:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzureRmResourceGroup -Name $firstRG -Location southcentralus
New-AzureRmResourceGroup -Name $secondRG -Location eastus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

Pro prostředí PowerShell pro nasazení dva účty úložiště, které **dva odběry**, použijte:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzureRmSubscription -Subscription $secondSub
New-AzureRmResourceGroup -Name $secondRG -Location eastus

Select-AzureRmSubscription -Subscription $firstSub
New-AzureRmResourceGroup -Name $firstRG -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

Pro prostředí PowerShell, k otestování jak **objekt skupiny prostředků** řeší nadřazené šablony, šablony vložené a propojené šablony, použijte:

```azurepowershell-interactive
New-AzureRmResourceGroup -Name parentGroup -Location southcentralus
New-AzureRmResourceGroup -Name inlineGroup -Location southcentralus
New-AzureRmResourceGroup -Name linkedGroup -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

V předchozím příkladu obě **parentRG** a **inlineRG** přeložit na **parentGroup**. **linkedRG** přeloží na **linkedGroup**. Výstup z předchozího příkladu je:

```powershell
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         Object                     {
                                               "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
                                               "name": "parentGroup",
                                               "location": "southcentralus",
                                               "properties": {
                                                 "provisioningState": "Succeeded"
                                               }
                                             }
 inlineRG         Object                     {
                                               "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
                                               "name": "parentGroup",
                                               "location": "southcentralus",
                                               "properties": {
                                                 "provisioningState": "Succeeded"
                                               }
                                             }
 linkedRG         Object                     {
                                               "id": "/subscriptions/<subscription-id>/resourceGroups/linkedGroup",
                                               "name": "linkedGroup",
                                               "location": "southcentralus",
                                               "properties": {
                                                 "provisioningState": "Succeeded"
                                               }
                                             }
```

### <a name="azure-cli"></a>Azure CLI

Pro Azure CLI k nasazení do dvou skupin prostředků v dva účty úložiště **předplatným**, použijte:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

Pro Azure CLI k nasazení dva účty úložiště, které **dva odběry**, použijte:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

Azure CLI, k otestování jak **objekt skupiny prostředků** řeší nadřazené šablony, šablony vložené a propojené šablony, použijte:

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

V předchozím příkladu obě **parentRG** a **inlineRG** přeložit na **parentGroup**. **linkedRG** přeloží na **linkedGroup**. Výstup z předchozího příkladu je:

```azurecli
...
"outputs": {
  "inlineRG": {
    "type": "Object",
    "value": {
      "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
      "location": "southcentralus",
      "name": "parentGroup",
      "properties": {
        "provisioningState": "Succeeded"
      }
    }
  },
  "linkedRG": {
    "type": "Object",
    "value": {
      "id": "/subscriptions/<subscription-id>/resourceGroups/linkedGroup",
      "location": "southcentralus",
      "name": "linkedGroup",
      "properties": {
        "provisioningState": "Succeeded"
      }
    }
  },
  "parentRG": {
    "type": "Object",
    "value": {
      "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
      "location": "southcentralus",
      "name": "parentGroup",
      "properties": {
        "provisioningState": "Succeeded"
      }
    }
  }
},
...
```

## <a name="next-steps"></a>Další postup

* Chcete-li pochopit, jak definovat parametry v šabloně, přečtěte si téma [Princip struktury a syntaxe šablon Azure Resource Manageru](resource-group-authoring-templates.md).
* Tipy pro řešení běžných chyb při nasazení, najdete v části [řešit běžné chyby nasazení v Azure pomocí Azure Resource Manageru](resource-manager-common-deployment-errors.md).
* Informace o nasazení šablony, která se vyžaduje SAS token najdete v tématu [nasazení privátní šablony s tokenem SAS](resource-manager-powershell-sas-token.md).
