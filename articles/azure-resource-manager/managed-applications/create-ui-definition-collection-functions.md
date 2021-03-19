---
title: Vytvořit funkce kolekce definic uživatelského rozhraní
description: Popisuje funkce, které se použijí při práci s kolekcemi, jako jsou pole a objekty.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: tomfitz
ms.openlocfilehash: 2a075c5c99f457681cd49e75014487bf9cca263c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87096962"
---
# <a name="createuidefinition-collection-functions"></a>Funkce kolekce CreateUiDefinition

Tyto funkce lze použít s kolekcemi, jako jsou řetězce JSON, pole a objekty.

## <a name="contains"></a>obsahuje

Vrátí `true` , zda řetězec obsahuje zadaný podřetězec, pole obsahuje zadanou hodnotu nebo objekt obsahuje zadaný klíč.

### <a name="example-string-contains"></a>Příklad: řetězec obsahuje

Následující příklad vrátí `true` :

```json
"[contains('webapp', 'web')]"
```

### <a name="example-array-contains"></a>Příklad: pole obsahuje

Předpokládat `element1` , že vrátí `[1, 2, 3]` . Následující příklad vrátí `false` :

```json
"[contains(steps('demoStep').element1, 4)]"
```

### <a name="example-object-contains"></a>Příklad: objekt obsahuje

Předpokládat, že `element1` vrátí:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

Následující příklad vrátí `true` :

```json
"[contains(steps('demoStep').element1, 'key1')]"
```

## <a name="empty"></a>empty

Vrátí, `true` zda má řetězec, pole nebo objekt hodnotu null nebo je prázdný.

### <a name="example-string-empty"></a>Příklad: řetězec prázdný

Následující příklad vrátí `true` :

```json
"[empty('')]"
```

### <a name="example-array-empty"></a>Příklad: pole prázdné

Předpokládat `element1` , že vrátí `[1, 2, 3]` . Následující příklad vrátí `false` :

```json
"[empty(steps('demoStep').element1)]"
```

### <a name="example-object-empty"></a>Příklad: objekt je prázdný.

Předpokládat, že `element1` vrátí:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

Následující příklad vrátí `false` :

```json
"[empty(steps('demoStep').element1)]"
```

### <a name="example-null-and-undefined"></a>Příklad: null a Nedefinováno

Předpokládá `element1` se `null` nebo není definován. Následující příklad vrátí `true` :

```json
"[empty(steps('demoStep').element1)]"
```

## <a name="filter"></a>filter

Po použití logiky filtrování poskytnuté jako funkce lambda vrátí nové pole. První parametr je pole, které se má použít pro filtrování. Druhý parametr je funkce lambda, která určuje logiku filtrování.

Následující ukázka vrátí pole `[ { "name": "abc" } ]` .

```json
"[filter(parse('[{\"name\":\"abc\"},{\"name\":\"xyz\"}]'), (item) => contains(item.name, 'abc'))]"
```

## <a name="first"></a>první

Vrátí první znak zadaného řetězce; první hodnota určeného pole; nebo první klíč a hodnota zadaného objektu.

### <a name="example-string-first"></a>Příklad: řetězec jako první

Následující příklad vrátí `"c"` :

```json
"[first('contoso')]"
```

### <a name="example-array-first"></a>Příklad: pole First

Předpokládat `element1` , že vrátí `[1, 2, 3]` . Následující příklad vrátí `1` :

```json
"[first(steps('demoStep').element1)]"
```

#### <a name="example-object-first"></a>Příklad: Object First

Předpokládat, že `element1` vrátí:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

Následující příklad vrátí `{"key1": "Linux"}` :

```json
"[first(steps('demoStep').element1)]"
```

## <a name="last"></a>poslední

Vrátí poslední znak zadaného řetězce, poslední hodnotu zadaného pole nebo poslední klíč a hodnotu zadaného objektu.

### <a name="example-string-last"></a>Příklad: řetězec poslední

Následující příklad vrátí `"o"` :

```json
"[last('contoso')]"
```

### <a name="example-array-last"></a>Příklad: pole Příjmení

Předpokládat `element1` , že vrátí `[1, 2, 3]` . Následující příklad vrátí `3` :

```json
"[last(steps('demoStep').element1)]"
```

### <a name="example-object-last"></a>Příklad: Object Last

Předpokládat, že `element1` vrátí:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

Následující příklad vrátí `{"key2": "Windows"}` :

```json
"[last(steps('demoStep').element1)]"
```

## <a name="length"></a>length

Vrátí počet znaků v řetězci, počet hodnot v poli nebo počet klíčů v objektu.

### <a name="example-string-length"></a>Příklad: délka řetězce

Následující příklad vrátí `7` :

```json
"[length('Contoso')]"
```

### <a name="example-array-length"></a>Příklad: délka pole

Předpokládat `element1` , že vrátí `[1, 2, 3]` . Následující příklad vrátí `3` :

```json
"[length(steps('demoStep').element1)]"
```

### <a name="example-object-length"></a>Příklad: délka objektu

Předpokládat, že `element1` vrátí:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

Následující příklad vrátí `2` :

```json
"[length(steps('demoStep').element1)]"
```

## <a name="map"></a>mapa

Po volání funkce lambda v poskytnutém poli vrátí nové pole. První parametr je pole, které se má použít pro funkci lambda. Druhým parametrem je funkce lambda.

Následující ukázka vrátí nové pole s každou hodnotou Double. Výsledek je `[2, 4, 6]` .

```json
"[map(parse('[1, 2, 3]'), (item) => mul(2, item))]"
```

Následující ukázka vrátí nové pole `["abc", "xyz"]` .

```json
"[map(parse('[{\"name\":\"abc\"},{\"name\":\"xyz\"}]'), (item) => item.name)]"
```

## <a name="skip"></a>Přeskočit

Obchází zadaný počet prvků v kolekci a vrátí zbývající prvky.

### <a name="example-string-skip"></a>Příklad: přeskočení řetězce

Následující příklad vrátí `"app"` :

```json
"[skip('webapp', 3)]"
```

### <a name="example-array-skip"></a>Příklad: přeskočení pole

Předpokládat `element1` , že vrátí `[1, 2, 3]` . Následující příklad vrátí `[3]` :

```json
"[skip(steps('demoStep').element1, 2)]"
```

### <a name="example-object-skip"></a>Příklad: přeskočení objektu

Předpokládat, že `element1` vrátí:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```
Následující příklad vrátí `{"key2": "Windows"}` :

```json
"[skip(steps('demoStep').element1, 1)]"
```

## <a name="split"></a>split

Vrátí pole řetězců obsahující podřetězce vstupu oddělené oddělovačem.

Následující ukázka vrátí pole `[ "555", "867", "5309" ]` .

```json
"[split('555-867-5309', '-')]"
```

## <a name="take"></a>take

Vrátí zadaný počet souvislých znaků od začátku řetězce, zadaného počtu souvislých hodnot od začátku pole nebo zadaného počtu souvislých klíčů a hodnot od začátku objektu.

### <a name="example-string-take"></a>Příklad: řetězec převezme

Následující příklad vrátí `"web"` :

```json
"[take('webapp', 3)]"
```

### <a name="example-array-take"></a>Příklad: pole převzít

Předpokládat `element1` , že vrátí `[1, 2, 3]` . Následující příklad vrátí `[1, 2]` :

```json
"[take(steps('demoStep').element1, 2)]"
```

### <a name="example-object-take"></a>Příklad: objekt převzít

Předpokládat, že `element1` vrátí:

```json
{
  "key1": "Linux",
  "key2": "Windows"
}
```

Následující příklad vrátí `{"key1": "Linux"}` :

```json
"[take(steps('demoStep').element1, 1)]"
```

## <a name="next-steps"></a>Další kroky

* Úvod do Azure Resource Manager najdete v tématu [Azure Resource Manager Overview](../management/overview.md).
