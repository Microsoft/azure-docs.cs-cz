---
title: Výstupy v šabloně Azure Resource Manager
description: Popisuje, jak definovat výstupní hodnoty v šabloně Azure Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: tomfitz
ms.openlocfilehash: 0ff1e3cb71bd1bf5ee947eb5204839d48103628b
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827932"
---
# <a name="outputs-in-azure-resource-manager-template"></a>Výstupy v šabloně Azure Resource Manager

Tento článek popisuje, jak definovat výstupní hodnoty v šabloně Azure Resource Manager. Výstupy použijete, když potřebujete vrátit hodnoty z nasazených prostředků.

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

V části výstupy můžete podmíněně vracet hodnotu. V případě [podmíněného nasazení](conditional-resource-deployment.md) prostředku se obvykle používá podmínka ve výstupech. Následující příklad ukazuje, jak podmíněně vracet ID prostředku pro veřejnou IP adresu na základě toho, zda byla nasazena nová:

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

## <a name="linked-templates"></a>Propojené šablony

K načtení výstupní hodnoty z propojené šablony použijte [odkazovou](resource-group-template-functions-resource.md#reference) funkci v nadřazené šabloně. Syntaxe v nadřazené šabloně je:

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

Nemůžete použít funkci `reference` v části výstupy [vnořené šablony](resource-group-linked-templates.md#nested-template). Chcete-li vrátit hodnoty nasazeného prostředku ve vnořené šabloně, převeďte vnořenou šablonu na propojenou šablonu.

## <a name="get-output-values"></a>Získat výstupní hodnoty

Po úspěšném nasazení se výstupní hodnoty automaticky vrátí do výsledků nasazení.

Chcete-li získat výstupní hodnoty z historie nasazení, můžete použít skript.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
(Get-AzResourceGroupDeployment `
  -ResourceGroupName <resource-group-name> `
  -Name <deployment-name>).Outputs.resourceID.value
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group deployment show \
  -g <resource-group-name> \
  -n <deployment-name> \
  --query properties.outputs.resourceID.value
```

---

## <a name="example-templates"></a>Příklady šablon

Následující příklady ukazují scénáře použití výstupů.

|Šablona  |Popis  |
|---------|---------|
|[Kopírovat proměnné](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Vytvoří komplexní proměnné a vypíše tyto hodnoty. Neimplementuje žádné prostředky. |
|[Veřejná IP adresa](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Vytvoří veřejnou IP adresu a vypíše ID prostředku. |
|[Load Balancer](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Odkazuje na předchozí šablonu. Při vytváření nástroje pro vyrovnávání zatížení používá ID prostředku ve výstupu. |

## <a name="next-steps"></a>Další kroky

* Další informace o dostupných vlastnostech výstupů naleznete v tématu [pochopení struktury a syntaxe šablon Azure Resource Manager](resource-group-authoring-templates.md).
* Doporučení k vytváření výstupů najdete v tématu [osvědčené postupy – výstupy](template-best-practices.md#outputs).
