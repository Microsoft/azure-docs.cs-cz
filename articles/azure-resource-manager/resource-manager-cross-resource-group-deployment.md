---
title: "Nasazení prostředků Azure do několika skupin předplatného a prostředků | Microsoft Docs"
description: "Ukazuje, jak mít více než jeden Azure předplatném nebo skupině prostředků během nasazení."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2018
ms.author: tomfitz
ms.openlocfilehash: 90cb87b3fe94b7b3b0eba1b261d29a1c8f4348d6
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Nasazení prostředků Azure k více než jedno předplatné nebo skupinu prostředků

Obvykle se nasazení všechny prostředky v šabloně na jednu [skupiny prostředků](resource-group-overview.md). Existují však scénáře, ve které chcete nasadit sadu prostředků společně ale umístěte je v různých skupinách prostředků nebo předplatných. Můžete například nasazení zálohování virtuálního počítače pro Azure Site Recovery na skupinu samostatné prostředků a umístění. Resource Manager umožňuje použití vnořených šablon cíl různých předplatných a skupin prostředků než předplatného a použití v šabloně nadřazené skupiny prostředků.

> [!NOTE]
> Můžete nasadit do pouze pět skupin prostředků v jednom nasazení. Toto omezení obvykle znamená, že můžete nasadit do jedné skupiny prostředků zadané nadřazené šablony a až čtyři skupiny prostředků v vnořené nebo propojené nasazení. Ale pokud šablona nadřazené obsahuje pouze vnořené nebo propojených šablon a nemá sám nasadit všechny prostředky, můžete zahrnout až pět skupin prostředků v vnořené nebo propojené nasazení.

## <a name="specify-a-subscription-and-resource-group"></a>Zadejte skupinu předplatného a prostředků

Pokud chcete zacílit na různé prostředků, použijte šablonu vnořené nebo propojené. `Microsoft.Resources/deployments` Typ prostředku poskytuje parametry pro `subscriptionId` a `resourceGroup`. Tyto vlastnosti umožňují zadat jiné předplatné a prostředek skupiny pro vnořené nasazení. Všechny skupiny prostředků musí existovat před spuštěním nasazení. Pokud nezadáte skupině ID nebo prostředků předplatného, předplatné a skupina prostředků z nadřazené šablony se používá.

Účet, který použijete k nasazení šablony musí mít oprávnění k nasazování ID předplatného. Pokud zadaný odběr existuje v jiné klienta Azure Active Directory, musíte [přidat uživatele typu Host z jiného adresáře](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).

Pokud chcete zadat jiné skupině prostředků a předplatné, použijte:

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

Pokud jsou vaše skupiny prostředků v rámci stejného předplatného, můžete odebrat **subscriptionId** hodnotu.

Následující příklad nasadí dva účty úložiště – jeden ve skupině prostředků, které se zadávají během nasazení, a v zadat jeden ve skupině prostředků `secondResourceGroup` parametr:

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

Pokud nastavíte `resourceGroup` název skupiny prostředků, který ještě neexistuje, nasazení se nezdaří.

Pokud chcete nasadit šablonu příklad, použijte prostředí Azure PowerShell 4.0.0 nebo novější nebo Azure CLI 2.0.0 nebo novější.

## <a name="use-the-resourcegroup-function"></a>Použijte funkci resourceGroup()

Pro různé nasazení skupiny prostředků, [resourceGroup() funkce](resource-group-template-functions-resource.md#resourcegroup) vyřeší různě v závislosti na tom, jak je zadat vnořené šablony. 

Pokud vložit jednu šablonu v rámci jiné šablony resourceGroup() v šabloně vnořené přeloží do nadřazené skupiny prostředků. Šablonu embedded používá následující formát:

```json
"apiVersion": "2017-05-10",
"name": "embeddedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "template": {
        ...
        resourceGroup() refers to parent resource group
    }
}
```

Pokud jste odkaz na šablonu samostatné, resourceGroup() v šabloně propojené přeloží na skupině vnořeného prostředku. Propojené šablona používá následující formát:

```json
"apiVersion": "2017-05-10",
"name": "linkedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "templateLink": {
        ...
        resourceGroup() in linked template refers to linked resource group
    }
}
```

## <a name="example-templates"></a>Příklad šablony

Následující šablony ukazují více nasazení skupiny prostředků. Skripty pro nasazení šablony se zobrazí pod tabulkou.

|Šablona  |Popis  |
|---------|---------|
|[Mezi předplatné šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crosssubscription.json) |Nasadí jeden účet úložiště do jedné skupiny prostředků a jeden účet úložiště do druhé skupiny prostředků. Zahrnout hodnotu pro ID předplatného druhý skupina prostředků je v jiném předplatném. |
|[Různé šablony vlastnosti skupiny prostředků](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) |Ukazuje, jak `resourceGroup()` funkce vyřeší. Všechny prostředky, není nasazen. |

### <a name="powershell"></a>PowerShell

Pro nasazení dva účty úložiště do dvou skupin prostředků v prostředí PowerShell **stejného předplatného**, použijte:

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

Pro prostředí PowerShell, chcete-li nasadit dva účty úložiště pro **obě předplatná**, použijte:

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

Pro prostředí PowerShell k testování jak **objektu skupiny prostředků** přeloží pro použití nadřazené šablony, šablony vložené a propojené šablony:

```azurepowershell-interactive
New-AzureRmResourceGroup -Name parentGroup -Location southcentralus
New-AzureRmResourceGroup -Name inlineGroup -Location southcentralus
New-AzureRmResourceGroup -Name linkedGroup -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

V předchozím příkladu obě **parentRG** a **inlineRG** odkazující na **parentGroup**. **linkedRG** přeloží na **linkedGroup**. Výstup z předchozího příkladu je:

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

Azure CLI, k nasazení dva účty úložiště pro dvě skupiny prostředků v **stejného předplatného**, použijte:

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

Pokud používáte Azure CLI, chcete-li nasadit dva účty úložiště pro **obě předplatná**, použijte:

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

Azure CLI pro testování jak **objektu skupiny prostředků** přeloží pro použití nadřazené šablony, šablony vložené a propojené šablony:

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

V předchozím příkladu obě **parentRG** a **inlineRG** odkazující na **parentGroup**. **linkedRG** přeloží na **linkedGroup**. Výstup z předchozího příkladu je:

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

* Chcete-li pochopit, jak definovat parametry v šabloně, přečtěte si téma [pochopit strukturu a syntaxe šablon Azure Resource Manager](resource-group-authoring-templates.md).
* Tipy k řešení běžných chyb při nasazení, naleznete v části [odstraňování běžných chyb nasazení Azure pomocí Azure Resource Manageru](resource-manager-common-deployment-errors.md).
* Informace o nasazení šablony, která vyžaduje tokenu SAS naleznete v tématu [privátní šablony nasazení s tokenem SAS](resource-manager-powershell-sas-token.md).
