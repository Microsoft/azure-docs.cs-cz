---
title: Nasazení prostředků do skupin prostředků
description: Popisuje nasazení prostředků v šabloně Azure Resource Manager. Ukazuje, jak se zaměřit na více než jednu skupinu prostředků.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: 9eb70e5ce69a2c7bc7ac9b8c9a7a558d09ecbef0
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98186222"
---
# <a name="resource-group-deployments-with-arm-templates"></a>Nasazení skupiny prostředků s využitím šablon ARM

Tento článek popisuje, jak nastavit obor nasazení do skupiny prostředků. Pro nasazení použijete šablonu Azure Resource Manager (šablonu ARM). Tento článek také ukazuje, jak rozšířit rozsah nad rámec skupiny prostředků v rámci operace nasazení.

## <a name="supported-resources"></a>Podporované prostředky

Většinu prostředků je možné nasadit do skupiny prostředků. Seznam dostupných prostředků naleznete v tématu Reference k [šabloně ARM](/azure/templates).

## <a name="schema"></a>Schéma

Pro šablony použijte následující schéma:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    ...
}
```

Pro soubory parametrů použijte:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>Příkazy nasazení

K nasazení do skupiny prostředků použijte příkazy pro nasazení skupiny prostředků.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

V případě Azure CLI použijte příkaz [AZ Deployment Group Create](/cli/azure/deployment/group#az_deployment_group_create). Následující příklad nasadí šablonu pro vytvoření skupiny prostředků:

```azurecli-interactive
az deployment group create \
  --name demoRGDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pro příkaz nasazení prostředí PowerShell použijte rutinu [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Následující příklad nasadí šablonu pro vytvoření skupiny prostředků:

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Name demoRGDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -storageAccountType Standard_GRS `
```

---

Podrobnější informace o příkazech nasazení a možnostech nasazení šablon ARM najdete v těchto tématech:

* [Nasazení prostředků pomocí šablon ARM a Azure Portal](deploy-portal.md)
* [Nasazení prostředků pomocí šablon ARM a Azure CLI](deploy-cli.md)
* [Nasazení prostředků pomocí šablon ARM a Azure PowerShell](deploy-powershell.md)
* [Nasazení prostředků pomocí šablon ARM a Azure Resource Manager REST API](deploy-rest.md)
* [Použití tlačítka nasazení k nasazení šablon z úložiště GitHub](deploy-to-azure-button.md)
* [Nasazení šablon ARM z Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-scopes"></a>Obory nasazení

Při nasazení do skupiny prostředků můžete prostředky nasadit do:

* Cílová skupina prostředků z operace
* Další skupiny prostředků ve stejném předplatném nebo jiných předplatných
* jakékoli předplatné v tenantovi
* tenant pro skupinu prostředků

[Prostředek rozšíření](scope-extension-resources.md) může být vymezen na cíl, který se liší od cíle nasazení.

Uživatel, který šablonu nasazuje, musí mít přístup k zadanému oboru.

V této části se dozvíte, jak zadat různé obory. Tyto různé obory můžete kombinovat v jediné šabloně.

### <a name="scope-to-target-resource-group"></a>Rozsah pro cílovou skupinu prostředků

Chcete-li nasadit prostředky do cílového prostředku, přidejte tyto prostředky do části Resources (prostředky) v šabloně.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-rg.json" highlight="5":::

Příklad šablony najdete v tématu [nasazení do cílové skupiny prostředků](#deploy-to-target-resource-group).

### <a name="scope-to-resource-group-in-same-subscription"></a>Rozsah do skupiny prostředků ve stejném předplatném

Pokud chcete nasadit prostředky do jiné skupiny prostředků ve stejném předplatném, přidejte vnořené nasazení a zahrňte `resourceGroup` vlastnost. Pokud nezadáte ID předplatného nebo skupinu prostředků, použije se předplatné a skupina prostředků z nadřazené šablony. Před spuštěním nasazení musí existovat všechny skupiny prostředků.

V následujícím příkladu je vnořené nasazení cíleno na skupinu prostředků s názvem `demoResourceGroup` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/same-sub-to-resource-group.json" highlight="9,13":::

Příklad šablony najdete v tématu [nasazení do více skupin prostředků](#deploy-to-multiple-resource-groups).

### <a name="scope-to-resource-group-in-different-subscription"></a>Rozsah do skupiny prostředků v jiném předplatném

Pokud chcete nasadit prostředky do skupiny prostředků v jiném předplatném, přidejte vnořené nasazení a přidejte `subscriptionId` vlastnosti a `resourceGroup` . V následujícím příkladu je vnořené nasazení cíleno na skupinu prostředků s názvem `demoResourceGroup` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/different-sub-to-resource-group.json" highlight="9,10,14":::

Příklad šablony najdete v tématu [nasazení do více skupin prostředků](#deploy-to-multiple-resource-groups).

### <a name="scope-to-subscription"></a>Rozsah pro předplatné

Chcete-li nasadit prostředky do předplatného, přidejte vnořené nasazení a zahrňte `subscriptionId` vlastnost. Předplatné může být předplatné pro cílovou skupinu prostředků nebo jakékoli jiné předplatné v tenantovi. Také nastavte `location` vlastnost pro vnořené nasazení.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/resource-group-to-subscription.json" highlight="9,10,14":::

Příklad šablony najdete v tématu [Vytvoření skupiny prostředků](#create-resource-group).

### <a name="scope-to-tenant"></a>Rozsah do tenanta

Prostředky můžete vytvořit v tenantovi nastavením nastavení `scope` na `/` . Uživatel, který šablonu nasazuje, musí mít [požadovaný přístup k nasazení v tenantovi](deploy-to-tenant.md#required-access).

Můžete použít vnořené nasazení se sadou `scope` a `location` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/resource-group-to-tenant.json" highlight="9,10,14":::

Nebo můžete nastavit obor na `/` pro některé typy prostředků, jako jsou skupiny pro správu.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/resource-group-create-mg.json" highlight="12,15":::

Další informace najdete v tématu [skupina pro správu](deploy-to-management-group.md#management-group).

## <a name="deploy-to-target-resource-group"></a>Nasadit do cílové skupiny prostředků

K nasazení prostředků v cílové skupině prostředků definujte tyto prostředky v části **Resources (prostředky** ) v šabloně. Následující šablona vytvoří účet úložiště ve skupině prostředků, která je určená v operaci nasazení.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json":::

## <a name="deploy-to-multiple-resource-groups"></a>Nasazení do více skupin prostředků

V jedné šabloně ARM můžete nasadit do více než jedné skupiny prostředků. Chcete-li cílit na skupinu prostředků, která je jiná než ta pro nadřazenou šablonu, použijte [vnořenou nebo propojenou šablonu](linked-templates.md). V části typ prostředku nasazení zadejte hodnoty pro ID předplatného a skupinu prostředků, do které chcete vnořenou šablonu nasadit. Skupiny prostředků můžou existovat v různých předplatných.

> [!NOTE]
> V jednom nasazení můžete nasadit do **skupin prostředků 800** . Obvykle toto omezení znamená, že můžete nasadit do jedné skupiny prostředků zadané pro nadřazenou šablonu a až 799 skupiny prostředků ve vnořených nebo propojených nasazeních. Pokud ale vaše nadřazená šablona obsahuje jenom vnořené nebo propojené šablony a sám o sobě neimplementuje žádné prostředky, můžete do vnořených nebo propojených nasazení zahrnout až 800 skupin prostředků.

Následující příklad nasadí dva účty úložiště. První účet úložiště se nasadí do skupiny prostředků zadané v operaci nasazení. Druhý účet úložiště se nasadí do skupiny prostředků zadané v `secondResourceGroup` `secondSubscriptionID` parametrech a:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json":::

Pokud nastavíte `resourceGroup` název skupiny prostředků, která neexistuje, nasazení se nezdařilo.

K otestování předchozí šablony a zobrazení výsledků použijte PowerShell nebo Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

K nasazení dvou účtů úložiště do **dvou předplatných** použijte:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Set-AzContext -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Set-AzContext -Subscription $firstSub
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

Pokud chcete nasadit dva účty úložiště do dvou skupin prostředků ve **stejném předplatném**, použijte:

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

K nasazení dvou účtů úložiště do **dvou předplatných** použijte:

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

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Z nasazení skupiny prostředků můžete přejít na úroveň předplatného a vytvořit skupinu prostředků. Následující šablona nasadí účet úložiště do cílové skupiny prostředků a vytvoří v zadaném předplatném novou skupinu prostředků.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        },
        "newResourceGroupName": {
            "type": "string"
        },
        "nestedSubscriptionID": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "storageName": "[concat(parameters('storagePrefix'), uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[variables('storageName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "demoSubDeployment",
            "location": "westus",
            "subscriptionId": "[parameters('nestedSubscriptionID')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Resources/resourceGroups",
                            "apiVersion": "2020-06-01",
                            "name": "[parameters('newResourceGroupName')]",
                            "location": "[parameters('location')]",
                            "properties": {}
                        }
                    ],
                    "outputs": {}
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Další kroky

* Příklad nasazení nastavení pracovního prostoru pro Azure Security Center najdete v tématu [deployASCwithWorkspaceSettings.jsna](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
