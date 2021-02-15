---
title: Proměnné v šablonách
description: Popisuje definování proměnných v šabloně Azure Resource Manager (šablona ARM) a souboru bicep.
ms.topic: conceptual
ms.date: 02/12/2021
ms.openlocfilehash: cafd42112e5d296cb73f88e292a66ca2203f3810
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100364456"
---
# <a name="variables-in-arm-templates"></a>Proměnné v šablonách ARM

Tento článek popisuje, jak definovat a používat proměnné v šabloně Azure Resource Manager (šablona ARM) nebo souboru bicep. K zjednodušení šablony můžete použít proměnné. Místo opakujících se složitých výrazů v rámci šablony definujete proměnnou, která obsahuje složitý výraz. Pak použijete tuto proměnnou podle potřeby v rámci šablony.

Správce prostředků vyřeší proměnné před spuštěním operací nasazení. Všude, kde je proměnná v šabloně použitá, ji Resource Manager nahradí vyřešenou hodnotou.

[!INCLUDE [Bicep preview](../../../includes/resource-manager-bicep-preview.md)]

## <a name="define-variable"></a>Definovat proměnnou

Při definování proměnné nezadáte [datový typ](template-syntax.md#data-types) pro proměnnou. Místo toho zadejte hodnotu nebo výraz šablony. Typ proměnné je odvozen z přeložené hodnoty. Následující příklad nastaví proměnnou na řetězec.

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "stringVar": "example value"
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var stringVar = 'example value'
```

---

Při vytváření proměnné můžete použít hodnotu z parametru nebo jiné proměnné.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "inputValue": {
    "defaultValue": "deployment parameter",
    "type": "string"
  }
},
"variables": {
  "stringVar": "myVariable",
  "concatToVar": "[concat(variables('stringVar'), '-addtovar') ]",
  "concatToParam": "[concat(parameters('inputValue'), '-addtoparam')]"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param inputValue string = 'deployment parameter'

var stringVar = 'myVariable'
var concatToVar =  '${stringVar}-addtovar'
var concatToParam = '${inputValue}-addtoparam'
```

---

K vytvoření hodnoty proměnné lze použít [funkce šablon](template-functions.md) .

V šablonách JSON nelze použít [referenční](template-functions-resource.md#reference) funkci ani žádnou z funkcí [seznamu](template-functions-resource.md#list) v deklaraci proměnné. Tyto funkce získávají běhový stav prostředku a nelze jej provést před nasazením, když jsou proměnné vyřešeny.

Funkce reference a list jsou platné při deklaraci proměnné v souboru bicep.

Následující příklad vytvoří hodnotu řetězce pro název účtu úložiště. Používá několik funkcí šablon k získání hodnoty parametru a zřetězuje je do jedinečného řetězce.

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var storageName = '${toLower(storageNamePrefix)}${uniqueString(resourceGroup().id)}'
```

---

## <a name="use-variable"></a>Použít proměnnou

Následující příklad ukazuje, jak použít proměnnou pro vlastnost prostředku.

# <a name="json"></a>[JSON](#tab/json)

V šabloně JSON odkazujete na hodnotu proměnné pomocí funkce [Variables](template-functions-deployment.md#variables) .

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageName')]",
    ...
  }
]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

V souboru bicep odkazujete na hodnotu proměnné, a to zadáním názvu proměnné.

```bicep
resource demoAccount 'Microsoft.Storage/storageAccounts@2019-06-01' = {
  name: storageName
```

---

## <a name="example-template"></a>Příklad šablony

Následující šablona neimplementuje žádné prostředky. Ukazuje několik způsobů, jak deklarovat proměnné různých typů.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variables.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

Bicep v současné době nepodporuje smyčky.

:::code language="bicep" source="~/resourcemanager-templates/azure-resource-manager/variables.bicep":::

---

## <a name="configuration-variables"></a>Konfigurační proměnné

Můžete definovat proměnné, které uchovávají související hodnoty pro konfiguraci prostředí. Proměnnou definujete jako objekt s hodnotami. Následující příklad ukazuje objekt, který obsahuje hodnoty pro dvě prostředí – **test** a **prod**. Před nasazením předejte jednu z těchto hodnot.

# <a name="json"></a>[JSON](#tab/json)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.json":::

# <a name="bicep"></a>[Bicep](#tab/bicep)

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/variablesconfigurations.bicep":::

---

## <a name="next-steps"></a>Další kroky

* Další informace o dostupných vlastnostech proměnných najdete v tématu [pochopení struktury a syntaxe šablon ARM](template-syntax.md).
* Doporučení k vytváření proměnných najdete v tématu [osvědčené postupy – proměnné](template-best-practices.md#variables).
* Příklad šablony, která přiřazuje pravidla zabezpečení skupině zabezpečení sítě, najdete v tématu [pravidla zabezpečení sítě](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) a [soubor parametrů](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json).
