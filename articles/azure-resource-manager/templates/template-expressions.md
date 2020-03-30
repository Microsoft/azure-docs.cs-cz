---
title: Syntaxe šablony a výrazy
description: Popisuje deklarativní syntaxi JSON pro šablony Azure Resource Manageru.
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 172838fa24709eb60fbcb6a68277f44bbd42f01e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460105"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Syntaxe a výrazy v šablonách Azure Resource Manageru

Základní syntaxí šablony je JSON. Výrazy však můžete použít k rozšíření hodnot JSON dostupných v šabloně.  Výrazy začínají a končí závorkami `[` a `]` v uvedeném pořadí. Hodnota výrazu se vyhodnocuje po nasazení šablony. Výraz může vrátit řetězec, celé číslo, logickou hodnotu, pole nebo objekt.

Výraz šablony nesmí překročit 24 576 znaků.

## <a name="use-functions"></a>Použití funkcí

Azure Resource Manager poskytuje [funkce,](template-functions.md) které můžete použít v šabloně. Následující příklad ukazuje výraz, který používá funkci ve výchozí hodnotě parametru:

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

V rámci výrazu `resourceGroup()` volá syntaxe jednu z funkcí, které Správce prostředků poskytuje pro použití v rámci šablony. V tomto případě je [resourceGroup](template-functions-resource.md#resourcegroup) funkce. Stejně jako v JavaScriptu jsou `functionName(arg1,arg2,arg3)`volání funkcí formátována jako . Syntaxe `.location` načte jednu vlastnost z objektu vrácené ho funkce.

Funkce šablony a jejich parametry nerozlišují malá a velká písmena. Správce prostředků například řeší **proměnné ('var1')** a **VARIABLES('VAR1')** jako stejné. Při vyhodnocení, pokud funkce výslovně upravuje případ (například toUpper neboLower), funkce zachová případ. Některé typy prostředků mohou mít požadavky na případ, které jsou oddělené od způsobu vyhodnocení funkcí.

Chcete-li funkci předat hodnotu řetězce jako parametr, použijte jednoduché uvozovky.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

Většina funkcí funguje stejně, ať už je nasazena do skupiny prostředků, předplatného, skupiny pro správu nebo klienta. Následující funkce mají omezení založená na oboru:

* [resourceGroup](template-functions-resource.md#resourcegroup) - lze použít pouze v nasazení ch a) do skupiny prostředků.
* [resourceId](template-functions-resource.md#resourceid) - lze použít v libovolném oboru, ale platné parametry se mění v závislosti na oboru.
* [předplatné](template-functions-resource.md#subscription) – lze použít pouze v nasazení do skupiny prostředků nebo předplatného.

## <a name="escape-characters"></a>Řídicí znaky

Chcete-li mít literál řetězec `[` začínat levou `]`závorkou a končit pravou závorkou , ale `[[`neinterpretovat jej jako výraz, přidejte další závorku pro spuštění řetězce pomocí aplikace . Například proměnná:

```json
"demoVar1": "[[test value]"
```

Překládá `[test value]`na .

Pokud však literálový řetězec nekončí závorkou, neunikne první závorce. Například proměnná:

```json
"demoVar2": "[test] value"
```

Překládá `[test] value`na .

Chcete-li uniknout dvojité uvozovky ve výrazu, jako je například přidání objektu JSON v šabloně, použijte zpětné lomítko.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

Při předávání hodnot parametrů závisí použití řídicích znaků na tom, kde je zadána hodnota parametru. Pokud v šabloně nastavíte výchozí hodnotu, budete potřebovat další levou závorku.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1":{
            "type": "string",
            "defaultValue": "[[test value]"
        }
    },
    "resources": [],
    "outputs": {
        "exampleOutput": {
            "type": "string",
            "value": "[parameters('demoParam1')]"
        }
    }
}
```

Pokud použijete výchozí hodnotu, `[test value]`vrátí šablona .

Pokud však předáte hodnotu parametru prostřednictvím příkazového řádku, znaky jsou interpretovány doslova. Nasazení předchozí šablony pomocí:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[[test value]"
```

Vrací objekt `[[test value]`. Místo toho použijte:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[test value]"
```

Stejné formátování platí při předávání hodnot ze souboru parametrů. Znaky jsou interpretovány doslovně. Při použití s předchozí šablonou vrátí `[test value]`následující soubor parametrů :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1": {
            "value": "[test value]"
        }
   }
}
```

## <a name="null-values"></a>Hodnoty Null

Chcete-li nastavit vlastnost na hodnotu null, můžete použít **null** nebo **[json('null')]**. [Funkce json](template-functions-array.md#json) vrátí prázdný objekt, `null` pokud zadáte jako parametr. V obou případech šablony Správce prostředků zacházet jako v případě, že vlastnost není k dispozici.

```json
"stringValue": null,
"objectValue": "[json('null')]"
```

## <a name="next-steps"></a>Další kroky

* Úplný seznam funkcí šablony najdete v tématu [Funkce šablony Správce prostředků Azure](template-functions.md).
* Další informace o souborech šablon [najdete v tématu Principy struktury a syntaxe šablon Azure Resource Manageru](template-syntax.md).
