---
title: Vytvoření specifikace šablony s propojenými šablonami
description: Naučte se vytvořit specifikaci šablony s propojenými šablonami.
ms.topic: conceptual
ms.date: 01/05/2021
ms.openlocfilehash: e5725ece165f5716480afbcb4ef9098274c09993
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97900633"
---
# <a name="tutorial-create-a-template-spec-with-linked-templates-preview"></a>Kurz: Vytvoření specifikace šablony s propojenými šablonami (Preview)

Naučte se vytvořit [specifikaci šablony](template-specs.md) s hlavní šablonou a [propojenou šablonou](linked-templates.md#linked-template). Pomocí specifikací šablon můžete sdílet šablony ARM s ostatními uživateli ve vaší organizaci. V tomto článku se dozvíte, jak vytvořit specifikaci šablony pro zabalení hlavní šablony a jejích propojených šablon pomocí `relativePath` vlastnosti [prostředku nasazení](/azure/templates/microsoft.resources/deployments).

## <a name="prerequisites"></a>Předpoklady

Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Specifikace šablony jsou momentálně ve verzi Preview. Pokud ho chcete použít s Azure PowerShell, musíte nainstalovat [verzi 5.0.0 nebo novější](/powershell/azure/install-az-ps). Pokud ho chcete použít v Azure CLI, použijte [verzi 2.14.2 nebo novější](/cli/azure/install-azure-cli).

## <a name="create-linked-templates"></a>Vytvoření propojených šablon

Vytvořte hlavní šablonu a propojenou šablonu.

Pokud chcete propojit šablonu, přidejte do hlavní šablony [prostředek nasazení](/azure/templates/microsoft.resources/deployments) . Do `templateLink` Vlastnosti zadejte relativní cestu k propojené šabloně v souladu s cestou nadřazené šablony.

Odkazovaná šablona se nazývá **linkedTemplate.jsv** a je uložena v podsložce s názvem **artefakty** v cestě, kde je uložena hlavní šablona.  Můžete použít jednu z následujících hodnot pro relativePath:

- `./artifacts/linkedTemplate.json`
- `/artifacts/linkedTemplate.json`
- `artifacts/linkedTemplate.json`

`relativePath`Vlastnost je vždy relativní vzhledem k souboru šablony, ve kterém `relativePath` je deklarována, takže pokud je k dispozici jiné linkedTemplate2.jsv, která je volána z linkedTemplate.jsna a linkedTemplate2.jsje uložena ve stejné podsložce artefakty, je RelativePath určený v linkedTemplate.js`linkedTemplate2.json` .

1. Vytvořte hlavní šablonu s následujícím JSON. Uložte hlavní šablonu jako **azuredeploy.js** do svého místního počítače. V tomto kurzu se předpokládá, že jste uložili do cesty **c:\Templates\linkedTS\azuredeploy.js** , ale můžete použít libovolnou cestu.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "location": {
          "type": "string",
          "defaultValue": "westus2",
          "metadata":{
            "description": "Specify the location for the resources."
          }
        },
        "storageAccountType": {
          "type": "string",
          "defaultValue": "Standard_LRS",
          "metadata":{
            "description": "Specify the storage account type."
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
          "apiVersion": "2020-06-01",
          "name": "createStorage",
          "properties": {
            "mode": "Incremental",
            "templateLink": {
              "relativePath": "artifacts/linkedTemplate.json"
            },
            "parameters": {
              "storageAccountType": {
                "value": "[parameters('storageAccountType')]"
              }
            }
          }
        }
      ]
    }
    ```

    > [!NOTE]
    > ApiVersion `Microsoft.Resources/deployments` musí být 2020-06-01 nebo vyšší.

1. Ve složce, ve které je uložena hlavní šablona, vytvořte adresář s názvem **artefakty** .
1. Vytvořte propojenou šablonu s následujícím kódem JSON:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountType": {
          "type": "string",
          "defaultValue": "Standard_LRS",
          "allowedValues": [
            "Standard_LRS",
            "Standard_GRS",
            "Standard_ZRS",
            "Premium_LRS"
          ],
          "metadata": {
            "description": "Storage Account type"
          }
        },
        "location": {
          "type": "string",
          "defaultValue": "[resourceGroup().location]",
          "metadata": {
            "description": "Location for all resources."
          }
        }
      },
      "variables": {
        "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
      },
      "resources": [
        {
          "type": "Microsoft.Storage/storageAccounts",
          "apiVersion": "2019-04-01",
          "name": "[variables('storageAccountName')]",
          "location": "[parameters('location')]",
          "sku": {
            "name": "[parameters('storageAccountType')]"
          },
          "kind": "StorageV2",
          "properties": {}
        }
      ],
      "outputs": {
        "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
        }
      }
    }
    ```

1. Uložte šablonu jako **linkedTemplate.js** ve složce **artefakty** .

## <a name="create-template-spec"></a>Vytvořit specifikaci šablony

Specifikace šablon se ukládají do skupin prostředků.  Vytvořte skupinu prostředků a pak vytvořte specifikaci šablony pomocí následujícího skriptu. Název specifikace šablony je **webspec**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name templateSpecRG `
  -Location westus2

New-AzTemplateSpec `
  -Name webSpec `
  -Version "1.0.0.0" `
  -ResourceGroupName templateSpecRG `
  -Location westus2 `
  -TemplateFile "c:\Templates\linkedTS\azuredeploy.json"
```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/azure-cli)

```azurecli
az group create \
  --name templateSpecRG \
  --location westus2

az ts create \
  --name webSpec \
  --version "1.0.0.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "<path-to-main-template>"
```

---

Až budete hotovi, můžete zobrazit specifikaci šablony z Azure Portal nebo pomocí následující rutiny:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Get-AzTemplateSpec -ResourceGroupName templatespecRG -Name webSpec
```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/azure-cli)

```azurecli
az ts show --name webSpec --resource-group templateSpecRG --version "1.0.0.0"
```

---

## <a name="deploy-template-spec"></a>Nasadit specifikaci šablony

Nyní můžete nasadit specifikaci šablony. Nasazení specifikace šablony je stejně jako nasazení šablony, kterou obsahuje, s tím rozdílem, že předáte ID prostředku specifikace šablony. Použijete stejné příkazy pro nasazení a v případě potřeby předáte hodnoty parametrů pro specifikaci šablony.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

$id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name webSpec -Version "1.0.0.0").Versions.Id

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName webRG
```

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/azure-cli)

```azurecli
az group create \
  --name webRG \
  --location westus2

id = $(az ts show --name webSpec --resource-group templateSpecRG --version "1.0.0.0" --query "id")

az deployment group create \
  --resource-group webRG \
  --template-spec $id
```

> [!NOTE]
> Došlo k známému problému s získáním ID specifikace šablony a jeho přiřazením k proměnné ve Windows PowerShellu.

---

## <a name="next-steps"></a>Další kroky

Další informace o nasazení specifikace šablony jako propojené šablony najdete v tématu [kurz: nasazení specifikace šablony jako propojené šablony](template-specs-deploy-linked-template.md).
