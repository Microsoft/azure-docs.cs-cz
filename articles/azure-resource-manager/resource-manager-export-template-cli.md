---
title: Export šablony Resource Manageru pomocí rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Export šablony ze skupiny prostředků pomocí Azure Resource Manageru a Azure CLI.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/23/2018
ms.author: tomfitz
ms.openlocfilehash: d4a1a687700badc550d37bf74f6a7e1680388897
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39440311"
---
# <a name="export-azure-resource-manager-templates-with-azure-cli"></a>Export šablony Azure Resource Manageru pomocí rozhraní příkazového řádku Azure

Resource Manager vám umožňuje exportovat šablonu Resource Manageru z existujících prostředků ve vašem předplatném. Z vygenerované šablony pak zjistíte syntaxi šablony a podle potřeby pak můžete automatizovat opakované nasazení svého řešení.

Je důležité si uvědomit, že se dvěma různými způsoby, jak vyexportovat šablonu:

* Můžete exportovat **skutečná Šablona použitá pro nasazení**. Exportovaná šablona zahrnuje všechny parametry a proměnné přesně tak, jak jsou uvedeny v původní šabloně. Tento přístup je užitečný, když budete chtít načíst šablonu.
* Můžete exportovat **vygenerovanou šablonu, která představuje aktuální stav skupiny prostředků**. Exportovaná šablona není založena na žádné šabloně, kterou jste použili k nasazení. Místo toho vytvoří šablony, která je "snímek" nebo "zálohování" skupiny prostředků. Exportovaná šablona má řadu pevně definovaných hodnot a pravděpodobně méně parametrů, než byste obvykle definovali. Tuto možnost použijte, chcete-li znovu nasadit prostředky do stejné skupiny prostředků. Tuto šablonu použít pro jiné skupiny prostředků, budete muset podstatně změnit.

Tento článek ukazuje oba přístupy.

## <a name="deploy-a-solution"></a>Nasazení řešení

Pro ilustraci oba přístupy k exportu šablony, Začněme tím, že nasazení řešení do vašeho předplatného. Pokud již máte skupinu prostředků v rámci vašeho předplatného, který chcete exportovat, není potřeba nasazovat toto řešení. Zbývající část tohoto článku však odkazuje na šablonu pro toto řešení. Ukázkový skript nasadí účet úložiště.

```azurecli
az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name NewStorage \
    --resource-group ExampleGroup \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
```  

## <a name="save-template-from-deployment-history"></a>Uložení šablony z historie nasazení

Načítání šablony z historie nasazení pomocí [export nasazení skupiny az](/cli/azure/group/deployment#az-group-deployment-export) příkazu. V následujícím příkladu se uloží šablony, které dřív nasadit:

```azurecli
az group deployment export --name NewStorage --resource-group ExampleGroup
```

Vrátí šablony. Zkopírujte JSON a uložte jako soubor. Všimněte si, že se jedná o přesnou šablonu, kterou jste použili k nasazení. Parametry a proměnné odpovídat šablony z Githubu. Tuto šablonu můžete znovu nasadit.


## <a name="export-resource-group-as-template"></a>Export skupiny prostředků jako šablonu

Místo získání šablony z historie nasazení, můžete načíst šablonu, která představuje aktuální stav skupiny prostředků s použitím [export skupiny az](/cli/azure/group#az-group-export) příkazu. Pokud jste provedli mnoho změn do vaší skupiny prostředků a žádná existující šablona představuje všechny změny se pomocí tohoto příkazu. Je určena jako snímek skupiny prostředků, který můžete použít k opětovnému nasazení do stejné skupiny prostředků. Použití vyexportované šablony pro ostatní řešení, je třeba ho upravit výrazně.

```azurecli
az group export --name ExampleGroup
```

Vrátí šablony. Zkopírujte JSON a uložte jako soubor. Všimněte si, že je jiný než šablona v Githubu. Šablona má různé parametry a žádné proměnné. SKU úložiště a umístění jsou pevně zakódovaná na hodnoty. Následující příklad ukazuje vyexportované šablony, ale vaše šablona má název parametru mírně liší:

```json
{
  "parameters": {
    "storageAccounts_mcyzaljiv7qncstandardsa_name": {
      "type": "String",
      "defaultValue": null
    }
  },
  "variables": {},
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "resources": [
    {
      "tags": {},
      "dependsOn": [],
      "apiVersion": "2016-01-01",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "Storage",
      "type": "Microsoft.Storage/storageAccounts",
      "location": "centralus",
      "name": "[parameters('storageAccounts_mcyzaljiv7qncstandardsa_name')]",
      "properties": {}
    }
  ],
  "contentVersion": "1.0.0.0"
}
```

Můžete znovu nasadit tuto šablonu, ale vyžaduje opakovaně uhodnout jedinečný název pro účet úložiště. Název parametru se mírně liší.

```azurecli
az group deployment create --name NewStorage --resource-group ExampleGroup \
  --template-file examplegroup.json \
  --parameters "{\"storageAccounts_mcyzaljiv7qncstandardsa_name\":{\"value\":\"tfstore0501\"}}"
```

## <a name="customize-exported-template"></a>Přizpůsobení exportované šablony

Můžete upravit této šablony můžete snadněji používá a flexibilnější. Povolit pro více míst, změňte hodnotu vlastnosti umístění a použít stejné umístění jako skupina prostředků:

```json
"location": "[resourceGroup().location]",
```

Abyste nemuseli uhodnutelné uniques název účtu úložiště, odeberte parametr pro název účtu úložiště. Přidání parametru pro příponu názvu úložiště a SKU úložiště:

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

Přidejte proměnnou, která vytvoří název účtu úložiště se uniqueString funkce:

```json
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
```

Nastavte název účtu úložiště do proměnné:

```json
"name": "[variables('storageAccountName')]",
```

Nastavte skladovou Položku k parametru:

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

Znovu nasaďte upravenou šablonu.

## <a name="next-steps"></a>Další postup
* Informace o použití portálu k exportu šablony najdete v tématu [Export šablony Azure Resource Manageru z existujících prostředků](resource-manager-export-template.md).
* Chcete-li definovat parametry v šabloně, přečtěte si téma [vytváření šablon](resource-group-authoring-templates.md#parameters).
* Tipy pro řešení běžných chyb při nasazení, najdete v části [řešit běžné chyby nasazení v Azure pomocí Azure Resource Manageru](resource-manager-common-deployment-errors.md).