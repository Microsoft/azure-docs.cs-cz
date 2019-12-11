---
title: Nasazení prostředků mezi předplatnými & skupiny prostředků
description: Ukazuje, jak během nasazení cílit více než jedno předplatné Azure a skupinu prostředků.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 0754895215384f76b1cb44224f3ba06c80181827
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978760"
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Nasazení prostředků Azure do více než jednoho předplatného nebo skupiny prostředků

Obvykle se všechny prostředky v šabloně nasazují do jedné [skupiny prostředků](resource-group-overview.md). Existují však situace, kdy chcete nasadit sadu prostředků dohromady, ale umístit je do různých skupin prostředků nebo předplatných. Například můžete chtít nasadit virtuální počítač pro zálohování pro Azure Site Recovery do samostatné skupiny prostředků a umístění. Správce prostředků umožňuje používat vnořené šablony pro cílení na více než jedno předplatné a skupinu prostředků.

> [!NOTE]
> V jednom nasazení můžete nasadit jenom pět skupin prostředků. Obvykle toto omezení znamená, že můžete nasadit do jedné skupiny prostředků zadané pro nadřazenou šablonu a až čtyř skupin prostředků ve vnořených nebo propojených nasazeních. Pokud ale vaše nadřazená šablona obsahuje jenom vnořené nebo propojené šablony a sám o sobě neimplementuje žádné prostředky, můžete ve vnořených nebo propojených nasazeních zahrnout až pět skupin prostředků.

## <a name="specify-subscription-and-resource-group"></a>Zadat předplatné a skupinu prostředků

Pokud chcete cílit na jinou skupinu prostředků nebo předplatné, použijte [vnořenou nebo propojenou šablonu](resource-group-linked-templates.md). Typ prostředku `Microsoft.Resources/deployments` poskytuje parametry pro `subscriptionId` a `resourceGroup`, které umožňují určit předplatné a skupinu prostředků pro vnořené nasazení. Pokud nezadáte ID předplatného nebo skupinu prostředků, použije se předplatné a skupina prostředků z nadřazené šablony. Před spuštěním nasazení musí existovat všechny skupiny prostředků.

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

Následující příklad nasadí dva účty úložiště. První účet úložiště se nasadí do skupiny prostředků zadané během nasazování. Druhý účet úložiště se nasadí do skupiny prostředků zadané v parametrech `secondResourceGroup` a `secondSubscriptionID`:

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
            "name": "[variables('firstStorageName')]",
            "apiVersion": "2017-06-01",
            "location": "[resourceGroup().location]",
            "sku":{
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        },
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
        }
    ]
}
```

Pokud jste nastavili `resourceGroup` na název skupiny prostředků, která neexistuje, nasazení se nezdařilo.

K otestování předchozí šablony a zobrazení výsledků použijte PowerShell nebo Azure CLI.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete nasadit dva účty úložiště do dvou skupin prostředků ve **stejném předplatném**, použijte:

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

K nasazení dvou účtů úložiště do **dvou předplatných**použijte:

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

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete nasadit dva účty úložiště do dvou skupin prostředků ve **stejném předplatném**, použijte:

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

K nasazení dvou účtů úložiště do **dvou předplatných**použijte:

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

---

## <a name="use-functions"></a>Použití funkcí

Funkce [Resource ()](resource-group-template-functions-resource.md#resourcegroup) a [Subscription ()](resource-group-template-functions-resource.md#subscription) se vyřeší odlišně podle toho, jak zadáte šablonu. Když propojíte externí šablonu, funkce se vždy vyhodnotí do oboru pro tuto šablonu. Při vnořování šablony v rámci nadřazené šablony použijte vlastnost `expressionEvaluationOptions` a určete, zda funkce překládá na skupinu prostředků a odběr nadřazené šablony nebo vnořené šablony. Nastavte vlastnost na `inner`, aby se přeložila na obor pro vnořenou šablonu. Nastavte vlastnost na `outer`, aby se přeložila na rozsah nadřazené šablony.

Následující tabulka ukazuje, jestli se funkce překládají na nadřazenou nebo integrovanou skupinu prostředků a předplatné.

| Typ šablony | Rozsah | Rozlišení |
| ------------- | ----- | ---------- |
| vnořené        | vnější (výchozí) | Nadřazená skupina prostředků |
| vnořené        | vnitřní | Dílčí skupina prostředků |
| Spojeného        | Nevztahuje se   | Dílčí skupina prostředků |

Následující [příklad šablony]((https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json)) ukazuje:

* vnořená šablona s výchozím (vnějším) rozsahem
* vnořená šablona s vnitřním rozsahem
* odkazovaná šablona

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "defaultScopeTemplate",
            "apiVersion": "2017-05-10",
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
            "name": "innerScopeTemplate",
            "apiVersion": "2017-05-10",
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
            "apiVersion": "2017-05-10",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
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

K otestování předchozí šablony a zobrazení výsledků použijte PowerShell nebo Azure CLI.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Výstup z předchozího příkladu:

```powershell
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

Výstup z předchozího příkladu:

```azurecli
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

* Chcete-li pochopit, jak definovat parametry v šabloně, přečtěte si téma [pochopení struktury a syntaxe šablon Azure Resource Manager](resource-group-authoring-templates.md).
* Tipy k řešení běžných chyb nasazení najdete v tématu [řešení běžných chyb při nasazení Azure pomocí Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Informace o nasazení šablony, která vyžaduje token SAS, najdete v tématu [nasazení privátní šablony s tokenem SAS](resource-manager-powershell-sas-token.md).
