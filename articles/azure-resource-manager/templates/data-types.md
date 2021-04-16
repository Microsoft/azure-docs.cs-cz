---
title: Datové typy v šablonách
description: Popisuje datové typy, které jsou k dispozici v šablonách Azure Resource Manager.
ms.topic: conceptual
ms.author: tomfitz
author: tfitzmac
ms.date: 03/04/2021
ms.openlocfilehash: 4d6c8306b3dbdfe895055dc008d81cc0d85d8d6c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538058"
---
# <a name="data-types-in-arm-templates"></a>Datové typy v šablonách ARM

Tento článek popisuje typy dat podporované v šablonách Azure Resource Manager (šablony ARM). Zahrnuje datové typy JSON a bicep.

## <a name="supported-types"></a>Podporované typy

V rámci šablony ARM můžete použít tyto typy dat:

* array
* bool
* int
* object
* secureObject – označeno modifikátorem v bicep
* secureString – označeno modifikátorem v bicep
* řetězec

## <a name="arrays"></a>Pole

Pole začínají levou hranatou závorkou ( `[` ) a končí pravou hranatou závorkou ( `]` ).

Ve formátu JSON může být pole deklarováno v jednom řádku nebo několika řádcích. Každý prvek je oddělen čárkou.

V bicep musí být pole deklarované na více řádcích. Mezi hodnotami nepoužívejte čárky.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleArray": {
    "type": "array",
    "defaultValue": [
      1,
      2,
      3
    ]
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleArray array = [
  1
  2
  3
]
```

---

Prvky pole mohou být stejného typu nebo různé typy.

# <a name="json"></a>[JSON](#tab/json)

```json
"variables": {
  "mixedArray": [
    "[resourceGroup().name]",
    1,
    true,
    "example string"
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
var mixedArray = [
  resourceGroup().name
  1
  true
  'example string'
]
```

---

## <a name="booleans"></a>Logické hodnoty

Při zadávání logických hodnot použijte `true` nebo `false` . Nezadávejte hodnotu pomocí uvozovek.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleBool": {
    "type": "bool",
    "defaultValue": true
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleBool bool = true
```

---

## <a name="integers"></a>Celá čísla

Při zadávání celočíselných hodnot nepoužívejte uvozovky.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleInt": {
    "type": "int",
    "defaultValue": 1
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleInt int = 1
```

---

Pro celá čísla předaná jako vložené parametry může být rozsah hodnot omezen sadou SDK nebo nástrojem příkazového řádku, který používáte pro nasazení. Například při použití prostředí PowerShell k nasazení šablony mohou být typy celého čísla v rozsahu od-2147483648 do 2147483647. Chcete-li se tomuto omezení vyhnout, zadejte v [souboru parametrů](parameter-files.md)velké celočíselné hodnoty. Typy prostředků použijí vlastní omezení pro celočíselné vlastnosti.

## <a name="objects"></a>Objekty

Objekty začínají levou závorkou ( `{` ) a končí pravou závorkou ( `}` ). Každá vlastnost v objektu se skládá z klíče a hodnoty. Klíč a hodnota jsou oddělené dvojtečkou ( `:` ).

# <a name="json"></a>[JSON](#tab/json)

Ve formátu JSON je klíč uzavřený v dvojitých uvozovkách. Každá vlastnost je oddělená čárkou.

```json
"parameters": {
  "exampleObject": {
    "type": "object",
    "defaultValue": {
      "name": "test name",
      "id": "123-abc",
      "isCurrent": true,
      "tier": 1
    }
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

V bicep klíč není uzavřen do uvozovek. Mezi vlastnostmi nepoužívejte čárky.

```bicep
param exampleObject object = {
  name: 'test name'
  id: '123-abc'
  isCurrent: true
  tier: 1
}
```

Přistupující objekty vlastnosti se používají pro přístup k vlastnostem objektu. Jsou vytvořené pomocí `.` operátoru. Například:

```bicep
var x = {
  y: {
    z: 'Hello`
    a: true
  }
  q: 42
}
```

S ohledem na předchozí deklaraci se výraz x. y. z vyhodnocuje na literální řetězec Hello. Podobně výraz x. q se vyhodnotí na celočíselný literál 42.

Přistupující objekty vlastnosti lze použít s libovolným objektem. To zahrnuje parametry a proměnné typů objektů a literálů objektů. Použití přístupového objektu vlastnosti u výrazu neobjektového typu je chyba.

---

## <a name="strings"></a>Řetězce

Ve formátu JSON jsou řetězce označeny dvojitými uvozovkami. V bicep jsou řetězce označeny pomocí jednoduchých uvozovek.

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "exampleString": {
    "type": "string",
    "defaultValue": "test value"
  }
},
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param exampleString string = 'test value'
```
---

## <a name="secure-strings-and-objects"></a>Zabezpečené řetězce a objekty

Zabezpečený řetězec používá stejný formát jako řetězec a zabezpečený objekt používá stejný formát jako objekt. Když nastavíte parametr na zabezpečený řetězec nebo zabezpečený objekt, hodnota parametru se neuloží do historie nasazení a nezaprotokoluje se. Nicméně pokud nastavíte tuto zabezpečenou hodnotu na vlastnost, která neočekává zabezpečenou hodnotu, hodnota není chráněná. Například pokud nastavíte zabezpečený řetězec na značku, tato hodnota je uložena jako prostý text. Používejte zabezpečené řetězce pro hesla a tajné kódy.

Pomocí bicep přidáte `@secure()` modifikátor do řetězce nebo objektu.

Následující příklad ukazuje dva zabezpečené parametry:

# <a name="json"></a>[JSON](#tab/json)

```json
"parameters": {
  "password": {
    "type": "secureString"
  },
  "configValues": {
    "type": "secureObject"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@secure()
param password string

@secure()
param configValues object
```

---

## <a name="next-steps"></a>Další kroky

Další informace o syntaxi šablon najdete v tématu [pochopení struktury a syntaxe šablon ARM](template-syntax.md).
