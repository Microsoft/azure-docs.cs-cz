---
title: Vytvoření specifikace šablony s propojenými šablonami
description: Naučte se vytvořit specifikaci šablony s propojenými šablonami.
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: b952baa465092fef19ad2feb11a43328a6177d1c
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387859"
---
# <a name="tutorial-create-a-template-spec-with-linked-templates-preview"></a>Kurz: Vytvoření specifikace šablony s propojenými šablonami (Preview)

Naučte se vytvořit [specifikaci šablony](template-specs.md) s [propojenou šablonou](linked-templates.md#linked-template). Pomocí specifikací šablon můžete sdílet šablony ARM s ostatními uživateli ve vaší organizaci. V tomto článku se dozvíte, jak vytvořit šablonu specifikace pro zabalení hlavní šablony a jejích propojených šablon pomocí nové `relativePath` vlastnosti [prostředku nasazení](/azure/templates/microsoft.resources/deployments).

## <a name="prerequisites"></a>Předpoklady

Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!NOTE]
> Specifikace šablony jsou momentálně ve verzi Preview. Pokud ho chcete použít, musíte se [zaregistrovat ve verzi Preview](https://aka.ms/templateSpecOnboarding).

## <a name="create-linked-templates"></a>Vytvoření propojených šablon

Vytvořte hlavní šablonu a propojenou šablonu.

Pokud chcete propojit šablonu, přidejte do hlavní šablony [prostředek nasazení](/azure/templates/microsoft.resources/deployments) . Do `templateLink` Vlastnosti zadejte relativní cestu k propojené šabloně v souladu s cestou nadřazené šablony.

Odkazovaná šablona se nazývá **linkedTemplate.jsv**a je uložena v podsložce s názvem **artefakty** v cestě, kde je uložena hlavní šablona.  Můžete použít jednu z následujících hodnot pro relativePath:

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

```azurepowershell
New-AzResourceGroup `
  -Name templateSpecRG `
  -Location westus2

New-AzTemplateSpec `
  -ResourceGroupName templateSpecRG `
  -Name webSpec `
  -Version "1.0.0.0" `
  -Location westus2 `
  -TemplateJsonFile "c:\Templates\linkedTS\azuredeploy.json"
```

Až budete hotovi, můžete zobrazit specifikaci šablony z Azure Portal nebo pomocí následující rutiny:

```azurepowershell-interactive
Get-AzTemplateSpec -ResourceGroupName templatespecRG -Name webSpec
```

## <a name="deploy-template-spec"></a>Nasadit specifikaci šablony

Nyní můžete nasadit specifikaci šablony. nasazení specifikace šablony je stejně jako nasazení šablony, kterou obsahuje, s výjimkou toho, že předáte ID prostředku specifikace šablony. Použijete stejné příkazy pro nasazení a v případě potřeby předáte hodnoty parametrů pro specifikaci šablony.

```azurepowershell
New-AzResourceGroup `
  -Name webRG `
  -Location westus2

$id = (Get-AzTemplateSpec -ResourceGroupName templateSpecRG -Name webSpec -Version "1.0.0.0").Version.Id

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName webRG
```

## <a name="next-steps"></a>Další kroky

Další informace o nasazení specifikace šablony jako propojené šablony najdete v tématu [kurz: nasazení specifikace šablony jako propojené šablony](template-specs-deploy-linked-template.md).
