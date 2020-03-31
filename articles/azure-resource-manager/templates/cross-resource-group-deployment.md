---
title: Nasazení prostředků & skupiny prostředků & prostředků
description: Ukazuje, jak cílit na více než jedno předplatné Azure a skupiny prostředků během nasazení.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 70868f5a3598c26ffff81f0ad3536a6c5c0a7e53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460343"
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Nasazení prostředků Azure do více než jednoho předplatného nebo skupiny prostředků

Obvykle nasadíte všechny prostředky v šabloně do jedné [skupiny prostředků](../management/overview.md). Existují však scénáře, kde chcete nasadit sadu prostředků společně, ale umístit je do různých skupin prostředků nebo předplatných. Můžete například nasadit záložní virtuální počítač pro Azure Site Recovery do samostatné skupiny prostředků a umístění. Správce prostředků umožňuje použít vnořené šablony k cílení na více než jedno předplatné a skupinu prostředků.

> [!NOTE]
> V jednom nasazení můžete nasadit pouze pět skupin prostředků. Toto omezení obvykle znamená, že můžete nasadit do jedné skupiny prostředků určené pro nadřazenou šablonu a až čtyři skupiny prostředků v vnořených nebo propojených nasazeních. Pokud však nadřazená šablona obsahuje pouze vnořené nebo propojené šablony a sama nenasazuje žádné prostředky, můžete do vnořených nebo propojených nasazení zahrnout až pět skupin prostředků.

## <a name="specify-subscription-and-resource-group"></a>Určení předplatného a skupiny prostředků

Chcete-li cílit na jinou skupinu prostředků nebo předplatné, použijte [vnořenou nebo propojenou šablonu](linked-templates.md). Typ `Microsoft.Resources/deployments` prostředku poskytuje parametry pro `subscriptionId` a `resourceGroup`, které umožňují zadat předplatné a skupinu prostředků pro vnořené nasazení. Pokud nezadáte ID předplatného nebo skupinu prostředků, použije se odběr a skupina prostředků z nadřazené šablony. Před spuštěním nasazení musí existovat všechny skupiny prostředků.

Účet, který používáte k nasazení šablony, musí mít oprávnění k nasazení na zadané ID předplatného. Pokud zadané předplatné existuje v jiném tenantovi služby Azure Active Directory, je nutné [přidat uživatele typu Host z jiného adresáře](../../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).

Chcete-li určit jinou skupinu prostředků a předplatné, použijte:

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

Pokud vaše skupiny prostředků jsou ve stejném předplatném, můžete odebrat hodnotu **subscriptionId.**

Následující příklad nasazuje dva účty úložiště. První účet úložiště se nasadí do skupiny prostředků zadané během nasazení. Druhý účet úložiště se nasadí do `secondResourceGroup` skupiny prostředků zadané v parametrech a: `secondSubscriptionID`

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
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2017-06-01",
      "name": "[variables('firstStorageName')]",
      "location": "[resourceGroup().location]",
      "sku":{
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "nestedTemplate",
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
            "apiVersion": "2017-06-01",
            "name": "[variables('secondStorageName')]",
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
    }
  ]
}
```

Pokud nastavíte `resourceGroup` název skupiny prostředků, která neexistuje, nasazení se nezdaří.

Chcete-li otestovat předchozí šablonu a zobrazit výsledky, použijte PowerShell nebo Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Chcete-li nasadit dva účty úložiště do dvou skupin prostředků **ve stejném předplatném**, použijte:

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

Chcete-li nasadit dva účty úložiště do **dvou předplatných**, použijte:

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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li nasadit dva účty úložiště do dvou skupin prostředků **ve stejném předplatném**, použijte:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

Chcete-li nasadit dva účty úložiště do **dvou předplatných**, použijte:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

---

## <a name="use-functions"></a>Použití funkcí

Funkce [resourceGroup()](template-functions-resource.md#resourcegroup) a [subscription()](template-functions-resource.md#subscription) řeší odlišně podle toho, jak zadáte šablonu. Když propojíte externí šablonu, funkce se vždy přeloží do oboru pro tuto šablonu. Když vnoříte šablonu do `expressionEvaluationOptions` nadřazené šablony, použijte vlastnost k určení, zda se funkce přeloží na skupinu prostředků a odběr pro nadřazenou šablonu nebo vnořenou šablonu. Nastavte vlastnost `inner` přeložit do oboru pro vnořené šablony. Nastavte vlastnost `outer` přeložit do oboru nadřazené šablony.

Následující tabulka ukazuje, zda se funkce překládají na nadřazenou nebo vloženou skupinu prostředků a předplatné.

| Typ šablony | Rozsah | Řešení |
| ------------- | ----- | ---------- |
| Vnořené        | vnější (výchozí) | Nadřazená skupina prostředků |
| Vnořené        | Vnitřní | Podřízená skupina prostředků |
| Propojené        | Není dostupné.   | Podřízená skupina prostředků |

Následující [ukázková šablona](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) ukazuje:

* vnořená šablona s výchozím (vnějším) rozsahem
* vnořená šablona s vnitřním rozsahem
* propojená šablona

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "defaultScopeTemplate",
      "resourceGroup": "inlineGroup",
      "properties": {
      "mode": "Incremental",
      "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
          ],
          "outputs": {
          "resourceGroupOutput": {
            "type": "string",
            "value": "[resourceGroup().name]"
          }
          }
      },
      "parameters": {}
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "innerScopeTemplate",
      "resourceGroup": "inlineGroup",
      "properties": {
      "expressionEvaluationOptions": {
          "scope": "inner"
      },
      "mode": "Incremental",
      "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
          ],
          "outputs": {
          "resourceGroupOutput": {
            "type": "string",
            "value": "[resourceGroup().name]"
          }
          }
      },
      "parameters": {}
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "resourceGroup": "linkedGroup",
      "properties": {
      "mode": "Incremental",
      "templateLink": {
          "contentVersion": "1.0.0.0",
          "uri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/resourceGroupName.json"
      },
      "parameters": {}
      }
    }
  ],
  "outputs": {
    "parentRG": {
      "type": "string",
      "value": "[concat('Parent resource group is ', resourceGroup().name)]"
    },
    "defaultScopeRG": {
      "type": "string",
      "value": "[concat('Default scope resource group is ', reference('defaultScopeTemplate').outputs.resourceGroupOutput.value)]"
    },
    "innerScopeRG": {
      "type": "string",
      "value": "[concat('Inner scope resource group is ', reference('innerScopeTemplate').outputs.resourceGroupOutput.value)]"
    },
    "linkedRG": {
      "type": "string",
      "value": "[concat('Linked resource group is ', reference('linkedTemplate').outputs.resourceGroupOutput.value)]"
    }
  }
}
```

Chcete-li otestovat předchozí šablonu a zobrazit výsledky, použijte PowerShell nebo Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Výstup z předchozího příkladu je:

```output
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Výstup z předchozího příkladu je:

```output
"outputs": {
  "defaultScopeRG": {
    "type": "String",
    "value": "Default scope resource group is parentGroup"
  },
  "innerScopeRG": {
    "type": "String",
    "value": "Inner scope resource group is inlineGroup"
  },
  "linkedRG": {
    "type": "String",
    "value": "Linked resource group is linkedGroup"
  },
  "parentRG": {
    "type": "String",
    "value": "Parent resource group is parentGroup"
  }
},
```

---

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak definovat parametry v šabloně, [najdete v tématu Principy struktury a syntaxe šablon Azure Resource Manageru](template-syntax.md).
* Tipy k řešení běžných chyb nasazení najdete [v tématu Řešení běžných chyb nasazení Azure ve Správci prostředků Azure](common-deployment-errors.md).
* Informace o nasazení šablony, která vyžaduje token SAS, naleznete v [tématu Nasazení privátní šablony s tokenem SAS](secure-template-with-sas-token.md).
