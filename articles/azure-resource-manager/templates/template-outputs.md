---
title: Výstupy v šablonách
description: Popisuje, jak definovat výstupní hodnoty v šabloně Azure Resource Manager (šablona ARM).
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: f8f13b6caf063cea79dc71775fb936f406a3ee6c
ms.sourcegitcommit: f6f928180504444470af713c32e7df667c17ac20
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2021
ms.locfileid: "97964010"
---
# <a name="outputs-in-arm-templates"></a>Výstupy v šablonách ARM

Tento článek popisuje, jak definovat výstupní hodnoty v šabloně Azure Resource Manager (šablona ARM). Použijete, `outputs` když potřebujete vrátit hodnoty z nasazených prostředků.

Formát každé výstupní hodnoty se musí shodovat s jedním z [datových typů](template-syntax.md#data-types).

## <a name="define-output-values"></a>Definovat výstupní hodnoty

Následující příklad ukazuje, jak vrátit ID prostředku pro veřejnou IP adresu:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

## <a name="conditional-output"></a>Podmíněný výstup

V `outputs` části můžete podmíněně vracet hodnotu. Obvykle se používá `condition` v případě, `outputs` kdy jste prostředek [nasadili podmíněně](conditional-resource-deployment.md) . Následující příklad ukazuje, jak podmíněně vracet ID prostředku pro veřejnou IP adresu na základě toho, zda byla nasazena nová:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Jednoduchý příklad podmíněného výstupu naleznete v tématu [podmíněná výstupní šablona](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

## <a name="dynamic-number-of-outputs"></a>Dynamický počet výstupů

V některých scénářích neznáte počet instancí hodnoty, které potřebujete vrátit při vytváření šablony. Můžete vrátit proměnný počet hodnot pomocí `copy` elementu.

```json
"outputs": {
  "storageEndpoints": {
    "type": "array",
    "copy": {
      "count": "[parameters('storageCount')]",
      "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
    }
  }
}
```

Další informace najdete v tématu [výstupní iterace v šablonách ARM](copy-outputs.md).

## <a name="linked-templates"></a>Propojené šablony

K načtení výstupní hodnoty z propojené šablony použijte [odkazovou](template-functions-resource.md#reference) funkci v nadřazené šabloně. Syntaxe v nadřazené šabloně je:

```json
"[reference('<deploymentName>').outputs.<propertyName>.value]"
```

Při získávání výstupní vlastnosti z propojené šablony název vlastnosti nemůže obsahovat pomlčku.

Následující příklad ukazuje, jak nastavit IP adresu v nástroji pro vyrovnávání zatížení načtením hodnoty z propojené šablony.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

Funkci nelze použít `reference` v části výstupy [vnořené šablony](linked-templates.md#nested-template). Chcete-li vrátit hodnoty nasazeného prostředku ve vnořené šabloně, převeďte vnořenou šablonu na propojenou šablonu.

## <a name="get-output-values"></a>Získat výstupní hodnoty

Po úspěšném nasazení se výstupní hodnoty automaticky vrátí do výsledků nasazení.

Chcete-li získat výstupní hodnoty z historie nasazení, můžete použít skript.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="example-templates"></a>Příklady šablon

Následující příklady ukazují scénáře použití výstupů.

|Template (Šablona)  |Popis  |
|---------|---------|
|[Kopírovat proměnné](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Vytvoří komplexní proměnné a vypíše tyto hodnoty. Neimplementuje žádné prostředky. |
|[Veřejná IP adresa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Vytvoří veřejnou IP adresu a vypíše ID prostředku. |
|[Load Balancer](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Odkazuje na předchozí šablonu. Při vytváření nástroje pro vyrovnávání zatížení používá ID prostředku ve výstupu. |

## <a name="next-steps"></a>Další kroky

* Další informace o dostupných vlastnostech výstupů najdete v tématu [pochopení struktury a syntaxe šablon ARM](template-syntax.md).
