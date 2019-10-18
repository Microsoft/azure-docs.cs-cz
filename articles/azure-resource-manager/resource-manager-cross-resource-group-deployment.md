---
title: Nasazení prostředků Azure mezi předplatnými & skupiny prostředků
description: Ukazuje, jak během nasazení cílit více než jedno předplatné Azure a skupinu prostředků.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/02/2018
ms.author: tomfitz
ms.openlocfilehash: c90096043f54eb8db5834fbe83ed1d6ae710d371
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72528329"
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Nasazení prostředků Azure do více než jednoho předplatného nebo skupiny prostředků

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Obvykle se všechny prostředky v šabloně nasazují do jedné [skupiny prostředků](resource-group-overview.md). Existují však situace, kdy chcete nasadit sadu prostředků dohromady, ale umístit je do různých skupin prostředků nebo předplatných. Například můžete chtít nasadit virtuální počítač pro zálohování pro Azure Site Recovery do samostatné skupiny prostředků a umístění. Správce prostředků umožňuje používat vnořené šablony pro cílení různých předplatných a skupin prostředků, než je předplatné a skupina prostředků používané pro nadřazenou šablonu.

> [!NOTE]
> V jednom nasazení můžete nasadit jenom pět skupin prostředků. Obvykle toto omezení znamená, že můžete nasadit do jedné skupiny prostředků zadané pro nadřazenou šablonu a až čtyř skupin prostředků ve vnořených nebo propojených nasazeních. Pokud ale vaše nadřazená šablona obsahuje jenom vnořené nebo propojené šablony a sám o sobě neimplementuje žádné prostředky, můžete ve vnořených nebo propojených nasazeních zahrnout až pět skupin prostředků.

## <a name="specify-a-subscription-and-resource-group"></a>Zadejte předplatné a skupinu prostředků.

Chcete-li cílit na jiný prostředek, použijte vnořenou nebo propojenou šablonu. @No__t_0 typ prostředku poskytuje parametry pro `subscriptionId` a `resourceGroup`. Tyto vlastnosti umožňují zadat jiné předplatné a skupinu prostředků pro vnořené nasazení. Před spuštěním nasazení musí existovat všechny skupiny prostředků. Pokud nezadáte buď ID předplatného, nebo skupinu prostředků, použije se předplatné a skupina prostředků z nadřazené šablony.

Účet, který použijete k nasazení šablony, musí mít oprávnění k nasazení na zadané ID předplatného. Pokud zadané předplatné existuje v jiném tenantovi Azure Active Directory, musíte [Přidat uživatele typu host z jiného adresáře](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).

Pokud chcete zadat jinou skupinu prostředků a předplatné, použijte:

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

Pokud jsou vaše skupiny prostředků ve stejném předplatném, můžete hodnotu **SubscriptionId** odebrat.

Následující příklad nasadí dva účty úložiště – jeden ve skupině prostředků zadané během nasazování a jeden ve skupině prostředků zadané v parametru `secondResourceGroup`:

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

Pokud jste nastavili `resourceGroup` na název skupiny prostředků, která neexistuje, nasazení se nezdařilo.

## <a name="use-the-resourcegroup-and-subscription-functions"></a>Použití funkcí Resource () a Subscription ()

Pro nasazení mezi skupinami prostředků se funkce [Resource ()](resource-group-template-functions-resource.md#resourcegroup) a [Subscription ()](resource-group-template-functions-resource.md#subscription) vyřeší odlišně podle toho, jak zadáte vnořenou šablonu. 

Pokud vložíte jednu šablonu do jiné šablony, funkce ve vnořené šabloně se přeloží na nadřazenou skupinu prostředků a předplatné. Vložená šablona používá následující formát:

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

Pokud odkazujete na samostatnou šablonu, funkce v propojené šabloně se vyřeší do vnořené skupiny prostředků a předplatného. Odkazovaná šablona používá následující formát:

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

## <a name="example-templates"></a>Příklady šablon

Následující šablony ukazují nasazení více skupin prostředků. Skripty pro nasazení šablon se zobrazí za tabulkou.

|Šablona  |Popis  |
|---------|---------|
|[Šablona pro různé odběry](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crosssubscription.json) |Nasadí jeden účet úložiště do jedné skupiny prostředků a jednoho účtu úložiště do druhé skupiny prostředků. Pokud je druhá skupina prostředků v jiném předplatném, uveďte hodnotu ID předplatného. |
|[Šablona vlastností více skupin prostředků](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) |Ukazuje, jak funkce `resourceGroup()` překládá. Neimplementuje žádné prostředky. |

### <a name="powershell"></a>PowerShell

Pokud chcete v prostředí PowerShell nasadit dva účty úložiště do dvou skupin prostředků ve **stejném předplatném**, použijte:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzResourceGroup -Name $firstRG -Location southcentralus
New-AzResourceGroup -Name $secondRG -Location eastus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

Pro PowerShell můžete pro nasazení dvou účtů úložiště do **dvou předplatných**použít:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzSubscription -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Select-AzSubscription -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

Pro prostředí PowerShell pro otestování toho, jak se **objekt skupiny prostředků** vyřeší pro nadřazenou šablonu, vloženou šablonu a propojenou šablonu, použijte:

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

V předchozím příkladu se obě **parentRG** a **inlineRG** překládají na **nadřazenou**. **linkedRG** se překládá na **odkazovanou**. Výstup z předchozího příkladu:

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

V případě Azure CLI nasazování dvou účtů úložiště do dvou skupin prostředků ve **stejném předplatném**použijte:

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

V případě Azure CLI nasazování dvou účtů úložiště do **dvou předplatných**použijte:

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

Pro rozhraní příkazového řádku Azure CLI můžete testovat, jak se **objekt skupiny prostředků** vyřeší pro nadřazenou šablonu, vloženou šablonu a propojenou šablonu, použijte:

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

V předchozím příkladu se obě **parentRG** a **inlineRG** překládají na **nadřazenou**. **linkedRG** se překládá na **odkazovanou**. Výstup z předchozího příkladu:

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

## <a name="next-steps"></a>Další kroky

* Chcete-li pochopit, jak definovat parametry v šabloně, přečtěte si téma [pochopení struktury a syntaxe šablon Azure Resource Manager](resource-group-authoring-templates.md).
* Tipy k řešení běžných chyb nasazení najdete v tématu [řešení běžných chyb při nasazení Azure pomocí Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Informace o nasazení šablony, která vyžaduje token SAS, najdete v tématu [nasazení privátní šablony s tokenem SAS](resource-manager-powershell-sas-token.md).
