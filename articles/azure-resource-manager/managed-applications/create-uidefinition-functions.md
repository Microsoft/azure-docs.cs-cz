---
title: Vytvoření definičních funkcí ui
description: Popisuje funkce, které se mají použít při vytváření definic ui pro spravované aplikace Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: 6e56c5e528a17d42a75da54158f00857a917645c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248447"
---
# <a name="createuidefinition-functions"></a>Funkce CreateUiDefinition
Tato část obsahuje podpisy pro všechny podporované funkce CreateUiDefinition.

Chcete-li použít funkci, obklopte deklaraci hranatými závorkami. Například:

```json
"[function()]"
```

Řetězce a další funkce lze odkazovat jako parametry pro funkci, ale řetězce musí být obklopeny v jednoduchých uvozovkách. Například:

```json
"[fn1(fn2(), 'foobar')]"
```

Pokud je to možné, můžete odkazovat na vlastnosti výstupu funkce pomocí operátoru tečky. Například:

```json
"[func().prop1]"
```

## <a name="referencing-functions"></a>Odkazovací funkce
Tyto funkce lze použít k odkazování výstupy z vlastností nebo kontextu CreateUiDefinition.

### <a name="basics"></a>Základy
Vrátí výstupní hodnoty prvku, který je definován v kroku Základy.

Následující příklad vrátí výstup prvku `foo` pojmenovaného v kroku Základy:

```json
"[basics('foo')]"
```

### <a name="steps"></a>kroky
Vrátí výstupní hodnoty prvku, který je definován v zadaném kroku. Chcete-li získat výstupní hodnoty prvků v `basics()` kroku Základy, použijte místo toho.

Následující příklad vrátí výstup prvku `bar` pojmenovaného v `foo`kroku s názvem :

```json
"[steps('foo').bar]"
```

### <a name="location"></a>location
Vrátí umístění vybrané v kroku Základy nebo v aktuálním kontextu.

Následující příklad může `"westus"`vrátit :

```json
"[location()]"
```

## <a name="string-functions"></a>Funkce řetězců
Tyto funkce lze použít pouze s řetězci JSON.

### <a name="concat"></a>concat
Zřetězí jeden nebo více řetězců.

Například pokud výstupní hodnota `element1` `"bar"`if , pak tento `"foobar!"`příklad vrátí řetězec :

```json
"[concat('foo', steps('step1').element1, '!')]"
```

### <a name="substring"></a>Podřetězec
Vrátí podřetězec zadaného řetězce. Podřetězec začíná na zadaný index a má zadanou délku.

Následující příklad `"ftw"`vrátí :

```json
"[substring('azure-ftw!!!1one', 6, 3)]"
```

### <a name="replace"></a>Nahradit
Vrátí řetězec, ve kterém jsou všechny výskyty zadaného řetězce v aktuálním řetězci nahrazeny jiným řetězcem.

Následující příklad `"Everything is awesome!"`vrátí :

```json
"[replace('Everything is terrible!', 'terrible', 'awesome')]"
```

### <a name="guid"></a>Identifikátor guid
Generuje globálně jedinečný řetězec (GUID).

Následující příklad může `"c7bc8bdc-7252-4a82-ba53-7c468679a511"`vrátit :

```json
"[guid()]"
```

### <a name="tolower"></a>Tolower
Vrátí řetězec převedený na malá písmena.

Následující příklad `"foobar"`vrátí :

```json
"[toLower('FOOBAR')]"
```

### <a name="toupper"></a>Toupper
Vrátí řetězec převedený na velká písmena.

Následující příklad `"FOOBAR"`vrátí :

```json
"[toUpper('foobar')]"
```

## <a name="collection-functions"></a>Funkce sběru
Tyto funkce lze použít s kolekcemi, jako jsou řetězce JSON, pole a objekty.

### <a name="contains"></a>obsahuje
Vrátí, `true` pokud řetězec obsahuje zadaný podřetězec, pole obsahuje zadanou hodnotu nebo objekt obsahuje zadaný klíč.

#### <a name="example-1-string"></a>Příklad 1: řetězec
Následující příklad `true`vrátí :

```json
"[contains('foobar', 'foo')]"
```

#### <a name="example-2-array"></a>Příklad 2: pole
Předpokládejme, že `element1` vrátí `[1, 2, 3]`. Následující příklad `false`vrátí :

```json
"[contains(steps('foo').element1, 4)]"
```

#### <a name="example-3-object"></a>Příklad 3: objekt
Předpokládejme, že `element1` vrátí:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Následující příklad `true`vrátí :

```json
"[contains(steps('foo').element1, 'key1')]"
```

### <a name="length"></a>length
Vrátí počet znaků v řetězci, počet hodnot v poli nebo počet klíčů v objektu.

#### <a name="example-1-string"></a>Příklad 1: řetězec
Následující příklad `6`vrátí :

```json
"[length('foobar')]"
```

#### <a name="example-2-array"></a>Příklad 2: pole
Předpokládejme, že `element1` vrátí `[1, 2, 3]`. Následující příklad `3`vrátí :

```json
"[length(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Příklad 3: objekt
Předpokládejme, že `element1` vrátí:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Následující příklad `2`vrátí :

```json
"[length(steps('foo').element1)]"
```

### <a name="empty"></a>empty
Vrátí, `true` pokud je řetězec, pole nebo objekt null nebo prázdný.

#### <a name="example-1-string"></a>Příklad 1: řetězec
Následující příklad `true`vrátí :

```json
"[empty('')]"
```

#### <a name="example-2-array"></a>Příklad 2: pole
Předpokládejme, že `element1` vrátí `[1, 2, 3]`. Následující příklad `false`vrátí :

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Příklad 3: objekt
Předpokládejme, že `element1` vrátí:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Následující příklad `false`vrátí :

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-4-null-and-undefined"></a>Příklad 4: null a undefined
Předpokládejme, že `element1` je `null` nebo není definován. Následující příklad `true`vrátí :

```json
"[empty(steps('foo').element1)]"
```

### <a name="first"></a>První
Vrátí první znak zadaného řetězce. první hodnota zadaného pole; nebo první klíč a hodnotu zadaného objektu.

#### <a name="example-1-string"></a>Příklad 1: řetězec
Následující příklad `"f"`vrátí :

```json
"[first('foobar')]"
```

#### <a name="example-2-array"></a>Příklad 2: pole
Předpokládejme, že `element1` vrátí `[1, 2, 3]`. Následující příklad `1`vrátí :

```json
"[first(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Příklad 3: objekt
Předpokládejme, že `element1` vrátí:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
Následující příklad `{"key1": "foobar"}`vrátí :

```json
"[first(steps('foo').element1)]"
```

### <a name="last"></a>poslední
Vrátí poslední znak zadaného řetězce, poslední hodnotu zadaného pole nebo poslední klíč a hodnotu zadaného objektu.

#### <a name="example-1-string"></a>Příklad 1: řetězec
Následující příklad `"r"`vrátí :

```json
"[last('foobar')]"
```

#### <a name="example-2-array"></a>Příklad 2: pole
Předpokládejme, že `element1` vrátí `[1, 2, 3]`. Následující příklad `2`vrátí :

```json
"[last(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Příklad 3: objekt
Předpokládejme, že `element1` vrátí:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Následující příklad `{"key2": "raboof"}`vrátí :

```json
"[last(steps('foo').element1)]"
```

### <a name="take"></a>vzít
Vrátí zadaný počet souvislých znaků od začátku řetězce, zadaný počet sousedících hodnot od začátku pole nebo zadaný počet sousedících klíčů a hodnot od začátku objektu.

#### <a name="example-1-string"></a>Příklad 1: řetězec
Následující příklad `"foo"`vrátí :

```json
"[take('foobar', 3)]"
```

#### <a name="example-2-array"></a>Příklad 2: pole
Předpokládejme, že `element1` vrátí `[1, 2, 3]`. Následující příklad `[1, 2]`vrátí :

```json
"[take(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Příklad 3: objekt
Předpokládejme, že `element1` vrátí:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Následující příklad `{"key1": "foobar"}`vrátí :

```json
"[take(steps('foo').element1, 1)]"
```

### <a name="skip"></a>Přeskočit
Obchází zadaný počet prvků v kolekci a potom vrátí zbývající prvky.

#### <a name="example-1-string"></a>Příklad 1: řetězec
Následující příklad `"bar"`vrátí :

```json
"[skip('foobar', 3)]"
```

#### <a name="example-2-array"></a>Příklad 2: pole
Předpokládejme, že `element1` vrátí `[1, 2, 3]`. Následující příklad `[3]`vrátí :

```json
"[skip(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Příklad 3: objekt
Předpokládejme, že `element1` vrátí:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
Následující příklad `{"key2": "raboof"}`vrátí :

```json
"[skip(steps('foo').element1, 1)]"
```

## <a name="logical-functions"></a>Logické funkce
Tyto funkce lze použít v podmíněných podmínek. Některé funkce nemusí podporovat všechny datové typy JSON.

### <a name="equals"></a>equals
Vrátí, `true` pokud oba parametry mají stejný typ a hodnotu. Tato funkce podporuje všechny datové typy JSON.

Následující příklad `true`vrátí :

```json
"[equals(0, 0)]"
```

Následující příklad `true`vrátí :

```json
"[equals('foo', 'foo')]"
```

Následující příklad `false`vrátí :

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

### <a name="less"></a>less
Vrátí, `true` pokud je první parametr přísně menší než druhý parametr. Tato funkce podporuje parametry pouze typu číslo a řetězec.

Následující příklad `true`vrátí :

```json
"[less(1, 2)]"
```

Následující příklad `false`vrátí :

```json
"[less('9', '10')]"
```

### <a name="lessorequals"></a>lessOrEquals
Vrátí, `true` pokud je první parametr menší nebo roven druhému parametru. Tato funkce podporuje parametry pouze typu číslo a řetězec.

Následující příklad `true`vrátí :

```json
"[lessOrEquals(2, 2)]"
```

### <a name="greater"></a>greater
Vrátí, `true` pokud je první parametr přísně větší než druhý parametr. Tato funkce podporuje parametry pouze typu číslo a řetězec.

Následující příklad `false`vrátí :

```json
"[greater(1, 2)]"
```

Následující příklad `true`vrátí :

```json
"[greater('9', '10')]"
```

### <a name="greaterorequals"></a>greaterOrEquals
Vrátí, `true` pokud je první parametr větší nebo roven druhému parametru. Tato funkce podporuje parametry pouze typu číslo a řetězec.

Následující příklad `true`vrátí :

```json
"[greaterOrEquals(2, 2)]"
```

### <a name="and"></a>a
Vrátí, `true` pokud jsou všechny `true`parametry vyhodnoceny do . Tato funkce podporuje dva nebo více parametrů pouze typu Logická hodnota.

Následující příklad `true`vrátí :

```json
"[and(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

Následující příklad `false`vrátí :

```json
"[and(equals(0, 0), greater(1, 2))]"
```

### <a name="or"></a>– nebo –
Vrátí `true` hodnotu, pokud je alespoň `true`jeden z parametrů vyhodnocen . Tato funkce podporuje dva nebo více parametrů pouze typu Logická hodnota.

Následující příklad `true`vrátí :

```json
"[or(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

Následující příklad `true`vrátí :

```json
"[or(equals(0, 0), greater(1, 2))]"
```

### <a name="not"></a>not
Vrátí, `true` pokud je `false`parametr vyhodnocen na . Tato funkce podporuje parametry pouze typu Logická hodnota.

Následující příklad `true`vrátí :

```json
"[not(false)]"
```

Následující příklad `false`vrátí :

```json
"[not(equals(0, 0))]"
```

### <a name="coalesce"></a>Coalesce
Vrátí hodnotu prvního parametru jiného parametru, který není null. Tato funkce podporuje všechny datové typy JSON.

`element1` Předpokládejme `element2` a jsou nedefinovány. Následující příklad `"foobar"`vrátí :

```json
"[coalesce(steps('foo').element1, steps('foo').element2, 'foobar')]"
```

## <a name="conversion-functions"></a>Funkce převodu
Tyto funkce lze použít k převodu hodnot mezi datovými typy JSON a kódováními.

### <a name="int"></a>int
Převede parametr na celé číslo. Tato funkce podporuje parametry typu číslo a řetězec.

Následující příklad `1`vrátí :

```json
"[int('1')]"
```

Následující příklad `2`vrátí :

```json
"[int(2.9)]"
```

### <a name="float"></a>float
Převede parametr na plovoucí bod. Tato funkce podporuje parametry typu číslo a řetězec.

Následující příklad `1.0`vrátí :

```json
"[float('1.0')]"
```

Následující příklad `2.9`vrátí :

```json
"[float(2.9)]"
```

### <a name="string"></a>řetězec
Převede parametr na řetězec. Tato funkce podporuje parametry všech datových typů JSON.

Následující příklad `"1"`vrátí :

```json
"[string(1)]"
```

Následující příklad `"2.9"`vrátí :

```json
"[string(2.9)]"
```

Následující příklad `"[1,2,3]"`vrátí :

```json
"[string([1,2,3])]"
```

Následující příklad `"{"foo":"bar"}"`vrátí :

```json
"[string({\"foo\":\"bar\"})]"
```

### <a name="bool"></a>bool
Převede parametr na logickou hodnotu. Tato funkce podporuje parametry typu číslo, řetězec a logická hodnota. Podobně jako logické hodnoty v jazyce `0` `'false'` JavaScript, libovolná hodnota s výjimkou nebo vrátí `true`.

Následující příklad `true`vrátí :

```json
"[bool(1)]"
```

Následující příklad `false`vrátí :

```json
"[bool(0)]"
```

Následující příklad `true`vrátí :

```json
"[bool(true)]"
```

Následující příklad `true`vrátí :

```json
"[bool('true')]"
```

### <a name="parse"></a>parse
Převede parametr na nativní typ. Jinými slovy, tato funkce je `string()`inverzní aplikace . Tato funkce podporuje parametry pouze typu řetězce.

Následující příklad `1`vrátí :

```json
"[parse('1')]"
```

Následující příklad `true`vrátí :

```json
"[parse('true')]"
```

Následující příklad `[1,2,3]`vrátí :

```json
"[parse('[1,2,3]')]"
```

Následující příklad `{"foo":"bar"}`vrátí :

```json
"[parse('{\"foo\":\"bar\"}')]"
```

### <a name="encodebase64"></a>encodeBase64
Zakóduje parametr na kódovaný řetězec base-64. Tato funkce podporuje parametry pouze typu řetězce.

Následující příklad `"Zm9vYmFy"`vrátí :

```json
"[encodeBase64('foobar')]"
```

### <a name="decodebase64"></a>decodeBase64
Dekóduje parametr z kódovaného řetězce base-64. Tato funkce podporuje parametry pouze typu řetězce.

Následující příklad `"foobar"`vrátí :

```json
"[decodeBase64('Zm9vYmFy')]"
```

### <a name="encodeuricomponent"></a>kódUriComponent
Zakóduje parametr na řetězec kódovaný adresou URL. Tato funkce podporuje parametry pouze typu řetězce.

Následující příklad `"https%3A%2F%2Fportal.azure.com%2F"`vrátí :

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

### <a name="decodeuricomponent"></a>decodeUriComponent
Dekóduje parametr z řetězce kódovaného adresou URL. Tato funkce podporuje parametry pouze typu řetězce.

Následující příklad `"https://portal.azure.com/"`vrátí :

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="math-functions"></a>Matematické funkce
### <a name="add"></a>add
Přidá dvě čísla a vrátí výsledek.

Následující příklad `3`vrátí :

```json
"[add(1, 2)]"
```

### <a name="sub"></a>Dílčí
Odečte druhé číslo od prvního čísla a vrátí výsledek.

Následující příklad `1`vrátí :

```json
"[sub(3, 2)]"
```

### <a name="mul"></a>mul
Vynásobí dvě čísla a vrátí výsledek.

Následující příklad `6`vrátí :

```json
"[mul(2, 3)]"
```

### <a name="div"></a>div
Vydělí první číslo druhým číslem a vrátí výsledek. Výsledkem je vždy celé číslo.

Následující příklad `2`vrátí :

```json
"[div(6, 3)]"
```

### <a name="mod"></a>Mod
Vydělí první číslo druhým číslem a vrátí zbytek.

Následující příklad `0`vrátí :

```json
"[mod(6, 3)]"
```

Následující příklad `2`vrátí :

```json
"[mod(6, 4)]"
```

### <a name="min"></a>min
Vrátí malé z těchto dvou čísel.

Následující příklad `1`vrátí :

```json
"[min(1, 2)]"
```

### <a name="max"></a>max
Vrátí větší ze dvou čísel.

Následující příklad `2`vrátí :

```json
"[max(1, 2)]"
```

### <a name="range"></a>range
Generuje posloupnost integrálních čísel v zadaném rozsahu.

Následující příklad `[1,2,3]`vrátí :

```json
"[range(1, 3)]"
```

### <a name="rand"></a>rand
Vrátí náhodné integrální číslo v zadaném rozsahu. Tato funkce negeneruje kryptograficky zabezpečená náhodná čísla.

Následující příklad může `42`vrátit :

```json
"[rand(-100, 100)]"
```

### <a name="floor"></a>Podlaze
Vrátí největší celé číslo menší nebo rovno zadanému číslu.

Následující příklad `3`vrátí :

```json
"[floor(3.14)]"
```

### <a name="ceil"></a>Ceil
Vrátí největší celé číslo větší nebo rovno zadanému číslu.

Následující příklad `4`vrátí :

```json
"[ceil(3.14)]"
```

## <a name="date-functions"></a>Datové funkce
### <a name="utcnow"></a>utcNow
Vrátí řetězec ve formátu ISO 8601 aktuálního data a času v místním počítači.

Následující příklad může `"1990-12-31T23:59:59.000Z"`vrátit :

```json
"[utcNow()]"
```

### <a name="addseconds"></a>addSeconds
Přidá k zadanému časovému razítku integrální počet sekund.

Následující příklad `"1991-01-01T00:00:00.000Z"`vrátí :

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

### <a name="addminutes"></a>Addminutes
Přidá k zadanému časovému razítku integrální počet minut.

Následující příklad `"1991-01-01T00:00:59.000Z"`vrátí :

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

### <a name="addhours"></a>addHours
Přidá k zadanému časovému razítku integrální počet hodin.

Následující příklad `"1991-01-01T00:59:59.000Z"`vrátí :

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="next-steps"></a>Další kroky
* Úvod do Správce prostředků Azure najdete v [tématu přehled Správce prostředků Azure](../management/overview.md).

