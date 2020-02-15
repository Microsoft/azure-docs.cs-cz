---
title: Syntaxe a výrazy šablon
description: Popisuje deklarativní syntaxi JSON pro šablony Azure Resource Manager.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 7bca3125f80225d2180734f483194a63e39d9cf5
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207396"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Syntaxe a výrazy v šablonách Azure Resource Manager

Základní syntaxí šablony je JSON. Můžete však použít výrazy k rozšiřování hodnot JSON dostupných v rámci šablony.  Výrazy začínají a končí hranatými závorkami: `[` a `]`, v uvedeném pořadí. Hodnota výrazu je vyhodnocena při nasazení šablony. Výraz může vracet řetězec, celé číslo, logickou hodnotu, pole nebo objekt.

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

V rámci výrazu syntaxe `resourceGroup()` volá jednu z funkcí, které Správce prostředků poskytuje pro použití v rámci šablony. V tomto případě je to funkce [Resource](template-functions-resource.md#resourcegroup) . Stejně jako v jazyce JavaScript jsou volání funkcí formátována jako `functionName(arg1,arg2,arg3)`. Syntaxe `.location` načte jednu vlastnost z objektu vráceného touto funkcí.

Funkce šablon a jejich parametry rozlišují velká a malá písmena. Například Správce prostředků vyřeší **proměnné (' var1 ')** a **proměnné (' var1 ')** jako stejné. Je-li tato funkce vyhodnocena, pokud funkce Express nemění velká a malá písmena (například toUpper nebo toLower), funkce zachovává případ. Některé typy prostředků můžou mít požadavky na případy, které jsou oddělené od způsobu, jakým se funkce vyhodnocují.

Chcete-li předat řetězcovou hodnotu jako parametr funkci, použijte jednoduché uvozovky.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

Většina funkcí funguje stejně, ať už je nasazená do skupiny prostředků, předplatného, skupiny pro správu nebo tenanta. Následující funkce mají omezení na základě rozsahu:

* [](template-functions-resource.md#resourcegroup) skupina prostředků se dá použít jenom v nasazeních do skupiny prostředků.
* [ResourceID](template-functions-resource.md#resourceid) -lze použít v jakémkoli oboru, ale platné parametry se mění v závislosti na rozsahu.
* [předplatné](template-functions-resource.md#subscription) – dá se použít jenom v nasazeních do skupiny prostředků nebo předplatného.

## <a name="escape-characters"></a>Řídicí znaky

Chcete-li, aby byl řetězcový literál začínat levou hranatou závorkou `[` a končit pravou hranatou závorkou `]`, ale nebyl interpretován jako výraz, přidejte další hranatou závorku, aby se řetězec začal `[[`. Například proměnná:

```json
"demoVar1": "[[test value]"
```

Přeloží na `[test value]`.

Nicméně pokud literální řetězec nekončí závorkou, nezařídí první vymezovač. Například proměnná:

```json
"demoVar2": "[test] value"
```

Přeloží na `[test] value`.

Chcete-li ve výrazu, jako je například přidání objektu JSON do šablony, řídicí dvojité uvozovky, použijte zpětné lomítko.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

## <a name="null-values"></a>Hodnoty null

Chcete-li nastavit vlastnost na hodnotu null, můžete použít **hodnotu null** nebo **[JSON (' null ')]** . [Funkce JSON](template-functions-array.md#json) vrátí prázdný objekt při zadání `null` jako parametru. V obou případech šablony Správce prostředků považují za, jako by vlastnost nebyla přítomna.

```json
"stringValue": null,
"objectValue": "[json('null')]"
```

## <a name="next-steps"></a>Další kroky

* Úplný seznam funkcí šablon naleznete v tématu [Azure Resource Manager Functions Template](template-functions.md).
* Další informace o souborech šablon naleznete v tématu [pochopení struktury a syntaxe šablon Azure Resource Manager](template-syntax.md).
