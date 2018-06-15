---
title: Export šablony Resource Manageru pomocí prostředí Azure PowerShell | Microsoft Docs
description: Export šablony ze skupiny prostředků pomocí Azure Resource Manageru a prostředí Azure PowerShell.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/23/2018
ms.author: tomfitz
ms.openlocfilehash: c69bab9d2956568473dd6def86ecbd9bbb6577cf
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359216"
---
# <a name="export-azure-resource-manager-templates-with-powershell"></a>Export šablony Azure Resource Manager pomocí prostředí PowerShell

Resource Manager vám umožňuje exportovat šablonu Resource Manageru z existujících prostředků ve vašem předplatném. Z vygenerované šablony pak zjistíte syntaxi šablony a podle potřeby pak můžete automatizovat opakované nasazení svého řešení.

Je důležité si uvědomit, že se dvěma různými způsoby, jak vyexportovat šablonu:

* Můžete exportovat **skutečné šablony použité pro nasazení**. Exportovaná šablona zahrnuje všechny parametry a proměnné přesně tak, jak jsou uvedeny v původní šabloně. Tento přístup je užitečné, když potřebujete načíst šablonu.
* Můžete exportovat **vygenerovanou šablonu, která představuje aktuální stav skupiny prostředků**. Exportovaná šablona není založena na žádné šabloně, kterou jste použili k nasazení. Místo toho vytvoří šablonu, která je "snímek" nebo "zálohování" skupiny prostředků. Exportovaná šablona má řadu pevně definovaných hodnot a pravděpodobně méně parametrů, než byste obvykle definovali. Tuto možnost použijte k opětovnému nasazení prostředků do stejné skupiny prostředků. Chcete-li tuto šablonu použít pro jiné skupině prostředků, může mít to výrazně změnit.

Tento článek ukazuje obou přístupů.

## <a name="deploy-a-solution"></a>Nasazení řešení

Pro ilustraci obou přístupů pro export šablony, Začněme tím, že nasazení řešení do vašeho předplatného. Pokud už máte skupinu prostředků v rámci vašeho předplatného, který chcete exportovat, nemáte nasazení tohoto řešení. Zbývající část tohoto článku, ale odkazuje na šablonu pro toto řešení. Ukázkový skript nasadí účet úložiště.

```powershell
New-AzureRmResourceGroup -Name ExampleGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup `
  -DeploymentName NewStorage
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```  

## <a name="save-template-from-deployment-history"></a>Uložit šablonu z historie nasazení

Šablonu z historie nasazení můžete načíst pomocí [uložit AzureRmResourceGroupDeploymentTemplate](/powershell/module/azurerm.resources/save-azurermresourcegroupdeploymenttemplate) příkaz. Následující příklad uloží šablony, která dříve nasazení:

```powershell
Save-AzureRmResourceGroupDeploymentTemplate -ResourceGroupName ExampleGroup -DeploymentName NewStorage
```

Vrátí umístění šablony.

```powershell
Path
----
C:\Users\exampleuser\NewStorage.json
```

Otevřete soubor a Všimněte si, že je přesný šablonu, kterou jste použili pro nasazení. Parametry a proměnné odpovídat šablony z Githubu. Tuto šablonu můžete znovu nasadit.

## <a name="export-resource-group-as-template"></a>Export skupiny prostředků jako šablony.

Místo načítání šablonu z historie nasazení, můžete načíst šablonu, která představuje aktuální stav skupiny prostředků pomocí [Export-AzureRmResourceGroup](/powershell/module/azurerm.resources/export-azurermresourcegroup) příkaz. Tento příkaz používají, když jste provedli mnoho změn vaší skupiny prostředků a žádné existující šablona představuje všechny změny. Je určený jako snímek skupiny prostředků, který můžete použít k opětovnému nasazení do stejné skupiny prostředků. Pokud chcete použít pro jiná řešení vyexportované šablony, je třeba ji upravit výrazně.

```powershell
Export-AzureRmResourceGroup -ResourceGroupName ExampleGroup
```

Vrátí umístění šablony.

```powershell
Path
----
C:\Users\exampleuser\ExampleGroup.json
```

Otevřete soubor a Všimněte si, že je jiný než má šablona v Githubu. Obsahuje různé parametry a žádné proměnné. Úložiště SKU a umístění jsou pevně zakódovaná na hodnoty. Následující příklad ukazuje vyexportované šablony, ale vaše šablona má název parametru mírně odlišný:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccounts_nf3mvst4nqb36standardsa_name": {
      "defaultValue": null,
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "Storage",
      "name": "[parameters('storageAccounts_nf3mvst4nqb36standardsa_name')]",
      "apiVersion": "2016-01-01",
      "location": "southcentralus",
      "tags": {},
      "properties": {},
      "dependsOn": []
    }
  ]
}
```

Můžete znovu nasadit této šablony, ale vyžaduje to uhodnutí jedinečný název pro účet úložiště. Název parametru se mírně liší.

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup `
  -TemplateFile C:\Users\exampleuser\ExampleGroup.json `
  -storageAccounts_nf3mvst4nqb36standardsa_name tfnewstorage0501
```

## <a name="customize-exported-template"></a>Přizpůsobení exportované šablony

Tato šablona, aby bylo snadné použití a flexibilnější, můžete upravit. Povolit pro více umístění, změňte hodnotu vlastnosti umístění použít stejné umístění jako pro skupinu prostředků:

```json
"location": "[resourceGroup().location]",
```

Chcete-li předejít tak snadno uhodnout uniques název pro účet úložiště, odeberte parametr pro název účtu úložiště. Přidání parametru pro přípony názvu úložiště a úložiště SKU:

```json
"parameters": {
    "storageSuffix": {
      "type": "string",
      "defaultValue": "standardsa"
    },
    "storageAccountType": {
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS"
      ],
      "type": "string",
      "metadata": {
        "description": "Storage Account type"
      }
    }
},
```

Přidání proměnné, která vytvoří název účtu úložiště pomocí funkce uniqueString:

```json
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
```

Název účtu úložiště nastavte proměnnou:

```json
"name": "[variables('storageAccountName')]",
```

Verze SKU nastaven na parametr:

```json
"sku": {
    "name": "[parameters('storageAccountType')]",
    "tier": "Standard"
},
```

Vaše šablona teď vypadá nějak takto:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageSuffix": {
      "type": "string",
      "defaultValue": "standardsa"
    },
    "storageAccountType": {
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS"
      ],
      "type": "string",
      "metadata": {
        "description": "Storage Account type"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), parameters('storageSuffix'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "sku": {
        "name": "[parameters('storageAccountType')]",
        "tier": "Standard"
      },
      "kind": "Storage",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {},
      "dependsOn": []
    }
  ]
}
```

Znovu nasaďte změněné šablony.

## <a name="next-steps"></a>Další postup
* Informace o používání portálu Export šablony najdete v tématu [Export šablony Azure Resource Manageru ze stávajících prostředků](resource-manager-export-template.md).
* Chcete-li definovat parametry v šabloně, přečtěte si téma [vytváření šablon](resource-group-authoring-templates.md#parameters).
* Tipy k řešení běžných chyb při nasazení, naleznete v části [odstraňování běžných chyb nasazení Azure pomocí Azure Resource Manageru](resource-manager-common-deployment-errors.md).
