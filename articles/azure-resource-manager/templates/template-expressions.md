---
title: Syntaxe a výrazy šablon
description: Popisuje deklarativní syntaxi JSON pro šablony Azure Resource Manager (šablony ARM).
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 44a386ed849771dfba717c8d1414e64422d0c7bd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97797039"
---
# <a name="syntax-and-expressions-in-arm-templates"></a>Syntaxe a výrazy v šablonách ARM

Základní syntaxe šablony Azure Resource Manager (šablona ARM) je JavaScript Object Notation (JSON). Můžete však použít výrazy k rozšiřování hodnot JSON dostupných v rámci šablony.  Výrazy začínají a končí závorkami `[` a `]` v uvedeném pořadí. Hodnota výrazu se vyhodnocuje po nasazení šablony. Výraz může vrátit řetězec, celé číslo, logickou hodnotu, pole nebo objekt.

Výraz šablony nemůže být delší než 24 576 znaků.

## <a name="use-functions"></a>Použití funkcí

Azure Resource Manager poskytuje [funkce](template-functions.md) , které můžete použít v šabloně. Následující příklad ukazuje výraz, který používá funkci ve výchozí hodnotě parametru:

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

Syntaxe v rámci výrazu `resourceGroup()` volá jednu z funkcí, které správce prostředků poskytují pro použití v rámci šablony. V tomto případě je to funkce [Resource](template-functions-resource.md#resourcegroup) . Stejně jako v jazyce JavaScript jsou volání funkcí formátována jako `functionName(arg1,arg2,arg3)` . Syntaxe `.location` načte jednu vlastnost z objektu vráceného touto funkcí.

Funkce šablon a jejich parametry rozlišují velká a malá písmena. Například Správce prostředků přeložit `variables('var1')` a `VARIABLES('VAR1')` jako stejný. Je-li vyhodnocena, pokud funkce výslovně nemění velká a malá písmena (například `toUpper` nebo `toLower` ), funkce zachová případ. Některé typy prostředků můžou mít požadavky na případy, které jsou oddělené od způsobu, jakým se funkce vyhodnocují.

Chcete-li předat řetězcovou hodnotu jako parametr funkci, použijte jednoduché uvozovky.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

Většina funkcí funguje stejně, ať už je nasazená do skupiny prostředků, předplatného, skupiny pro správu nebo tenanta. Následující funkce mají omezení na základě rozsahu:

* [](template-functions-resource.md#resourcegroup) skupina prostředků se dá použít jenom v nasazeních do skupiny prostředků.
* [ResourceID](template-functions-resource.md#resourceid) -lze použít v jakémkoli oboru, ale platné parametry se mění v závislosti na rozsahu.
* [předplatné](template-functions-resource.md#subscription) – dá se použít jenom v nasazeních do skupiny prostředků nebo předplatného.

## <a name="escape-characters"></a>Řídicí znaky

Chcete-li, aby byl řetězcový literál začínat levou hranatou závorkou `[` a končit pravou závorkou `]` , ale nebyl interpretován jako výraz, přidejte další hranatou závorku, která zahájí řetězec s `[[` . Například proměnná:

```json
"demoVar1": "[[test value]"
```

Přeloží na `[test value]` .

Nicméně pokud literální řetězec nekončí závorkou, nezařídí první vymezovač. Například proměnná:

```json
"demoVar2": "[test] value"
```

Přeloží na `[test] value` .

Chcete-li ve výrazu, jako je například přidání objektu JSON do šablony, řídicí dvojité uvozovky, použijte zpětné lomítko.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

Při předávání hodnot parametrů závisí použití řídicích znaků na místě, kde je zadána hodnota parametru. Pokud nastavíte výchozí hodnotu v šabloně, budete potřebovat další levou hranatou závorku.

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

Použijete-li výchozí hodnotu, šablona se vrátí `[test value]` .

Nicméně pokud předáte hodnotu parametru prostřednictvím příkazového řádku, znaky jsou interpretovány doslova. Nasazování předchozí šablony pomocí:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[[test value]"
```

Vrací objekt `[[test value]`. Místo toho použijte:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[test value]"
```

Stejné formátování platí při předávání hodnot ze souboru parametrů. Znaky jsou interpretovány doslova. Při použití s předchozí šablonou se vrátí následující soubor parametrů `[test value]` :

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

Pokud chcete nastavit vlastnost na hodnotu null, můžete použít `null` nebo `[json('null')]`. [Funkce JSON](template-functions-object.md#json) vrátí prázdný objekt, když zadáte `null` jako parametr. V obou případech šablony Správce prostředků považují za, jako by vlastnost nebyla přítomna.

```json
"stringValue": null,
"objectValue": "[json('null')]"
```

## <a name="next-steps"></a>Další kroky

* Úplný seznam funkcí šablon najdete v tématu [funkce šablon ARM](template-functions.md).
* Další informace o souborech šablon naleznete v tématu [pochopení struktury a syntaxe šablon ARM](template-syntax.md).
