---
title: Nasazení specifikace šablony jako propojené šablony
description: Přečtěte si, jak nasadit stávající specifikaci šablony v propojeném nasazení.
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 8d4ccd77c8b37a696fab7494a8d3f8052fc89b35
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889259"
---
# <a name="tutorial-deploy-a-template-spec-as-a-linked-template-preview"></a>Kurz: nasazení specifikace šablony jako propojené šablony (Náhled)

Přečtěte si, jak nasadit stávající [specifikaci šablony](template-specs.md) pomocí [propojeného nasazení](linked-templates.md#linked-template). Pomocí specifikací šablon můžete sdílet šablony ARM s ostatními uživateli ve vaší organizaci. Po vytvoření specifikace šablony můžete nasadit specifikaci šablony pomocí Azure PowerShell nebo rozhraní příkazového řádku Azure CLI. Můžete také nasadit specifikaci šablony jako součást řešení pomocí propojené šablony.

## <a name="prerequisites"></a>Předpoklady

Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Specifikace šablony jsou momentálně ve verzi Preview. Pokud ho chcete použít s Azure PowerShell, musíte nainstalovat [verzi 5.0.0 nebo novější](/powershell/azure/install-az-ps). Pokud ho chcete použít v Azure CLI, použijte [verzi 2.14.2 nebo novější](/cli/azure/install-azure-cli).

## <a name="create-a-template-spec"></a>Vytvoření specifikace šablony

Postup pro [rychlé zprovoznění: vytvoření a nasazení specifikace šablony](quickstart-create-template-specs.md) pro vytvoření specifikace šablony pro nasazení účtu úložiště. V další části budete potřebovat název skupiny prostředků specifikace šablony, název specifikace šablony a verze specifikace šablony.

## <a name="create-the-main-template"></a>Vytvoření hlavní šablony

K nasazení specifikace šablony v šabloně ARM přidejte do hlavní šablony [prostředek nasazení](/azure/templates/microsoft.resources/deployments) . Ve `templateLink` Vlastnosti zadejte ID prostředku specifikace šablony. Vytvořte šablonu s následujícím JSON s názvem **azuredeploy.jsv**. V tomto kurzu se předpokládá, že jste uložili do cesty **c:\Templates\deployTS\azuredeploy.js** , ale můžete použít libovolnou cestu.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "tsResourceGroup":{
      "type": "string",
      "metadata": {
        "Description": "Specifies the resource group name of the template spec."
      }
    },
    "tsName": {
      "type": "string",
      "metadata": {
        "Description": "Specifies the name of the template spec."
      }
    },
    "tsVersion": {
      "type": "string",
      "defaultValue": "1.0.0.0",
      "metadata": {
        "Description": "Specifies the version the template spec."
      }
    },
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "metadata": {
        "Description": "Specifies the storage account type required by the template spec."
      }
    }
  },
  "variables": {
    "appServicePlanName": "[concat('plan', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2016-09-01",
      "name": "[variables('appServicePlanName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "B1",
        "tier": "Basic",
        "size": "B1",
        "family": "B",
        "capacity": 1
      },
      "kind": "linux",
      "properties": {
        "perSiteScaling": false,
        "reserved": true,
        "targetWorkerCount": 0,
        "targetWorkerSizeId": 0
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-10-01",
      "name": "createStorage",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId(parameters('tsResourceGroup'), 'Microsoft.Resources/templateSpecs/versions', parameters('tsName'), parameters('tsVersion'))]"
        },
        "parameters": {
          "storageAccountType": {
            "value": "[parameters('storageAccountType')]"
          }
        }
      }
    }
  ],
  "outputs": {
    "templateSpecId": {
      "type": "string",
      "value": "[resourceId(parameters('tsResourceGroup'), 'Microsoft.Resources/templateSpecs/versions', parameters('tsName'), parameters('tsVersion'))]"
    }
  }
}
```

ID specifikace šablony se generuje pomocí [`resourceID()`](template-functions-resource.md#resourceid) funkce. Argument skupiny prostředků ve funkci resourceID () je volitelný, pokud je templateSpec ve stejné skupině prostředků aktuálního nasazení.  ID prostředku můžete také přímo předat jako parametr. ID získáte pomocí:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$id = (Get-AzTemplateSpec -ResourceGroupName $resourceGroupName -Name $templateSpecName -Version $templateSpecVersion).Versions.Id
```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/azure-cli)

```azurecli-interactive
id = $(az ts show --name $templateSpecName --resource-group $resourceGroupName --version $templateSpecVersion --query "id")
```

> [!NOTE]
> Došlo k známému problému s získáním ID specifikace šablony a jeho přiřazením k proměnné ve Windows PowerShellu.

---

Syntaxe pro předávání parametrů do specifikace šablony je:

```json
"parameters": {
  "storageAccountType": {
    "value": "[parameters('storageAccountType')]"
  }
}
```

> [!NOTE]
> ApiVersion `Microsoft.Resources/deployments` musí být 2020-06-01 nebo vyšší.

## <a name="deploy-the-template"></a>Nasazení šablony

Když nasadíte propojenou šablonu, nasadí jak webovou aplikaci, tak i účet úložiště. Nasazení je stejné jako nasazení jiných šablon ARM.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

New-AzResourceGroupDeployment `
  -ResourceGroupName webRG `
  -TemplateFile "c:\Templates\deployTS\azuredeploy.json" `
  -tsResourceGroup templateSpecRg `
  -tsName storageSpec `
  -tsVersion 1.0
```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/azure-cli)

```azurecli
az group create \
  --name webRG \
  --location westus2

az deployment group create \
  --resource-group webRG \
  --template-file "c:\Templates\deployTS\azuredeploy.json" \
  --parameters tsResourceGroup=templateSpecRG tsName=storageSpec tsVersion=1.0
```

---

## <a name="next-steps"></a>Další kroky

Další informace o vytvoření specifikace šablony, která obsahuje propojené šablony, najdete v tématu [Vytvoření šablony specifikace propojené šablony](template-specs-create-linked.md).
