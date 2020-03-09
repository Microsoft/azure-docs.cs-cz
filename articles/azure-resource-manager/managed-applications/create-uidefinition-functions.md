---
title: Vytvoření funkcí definice uživatelského rozhraní
description: Popisuje funkce, které se mají použít při vytváření definic uživatelského rozhraní pro Azure Managed Applications
author: tfitzmac
ms.topic: conceptual
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: 6e56c5e528a17d42a75da54158f00857a917645c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78380854"
---
# <a name="createuidefinition-functions"></a>Funkce CreateUiDefinition
Tato část obsahuje signatury všech podporovaných funkcí CreateUiDefinition.

Chcete-li použít funkci, uzavřete deklaraci do hranatých závorek. Příklad:

```json
"[function()]"
```

Na řetězce a další funkce lze odkazovat jako na parametry funkce, ale řetězce musí být obklopeny v jednoduchých uvozovkách. Příklad:

```json
"[fn1(fn2(), 'foobar')]"
```

V případě potřeby můžete odkazovat na vlastnosti výstupu funkce pomocí operátoru tečka. Příklad:

```json
"[func().prop1]"
```

## <a name="referencing-functions"></a>Odkazování na funkce
Tyto funkce lze použít k odkazování na výstupy z vlastností nebo kontextu CreateUiDefinition.

### <a name="basics"></a>základní informace
Vrátí výstupní hodnoty prvku, který je definován v kroku základy.

Následující příklad vrátí výstup elementu s názvem `foo` v kroku základy:

```json
"[basics('foo')]"
```

### <a name="steps"></a>kroky
Vrátí výstupní hodnoty prvku, který je definován v zadaném kroku. Chcete-li získat výstupní hodnoty prvků v kroku základy, použijte místo toho `basics()`.

Následující příklad vrátí výstup elementu s názvem `bar` v kroku s názvem `foo`:

```json
"[steps('foo').bar]"
```

### <a name="location"></a>location
Vrátí umístění vybrané v kroku základy nebo v aktuálním kontextu.

Následující příklad může vracet `"westus"`:

```json
"[location()]"
```

## <a name="string-functions"></a>Funkce řetězců
Tyto funkce lze použít pouze s řetězci JSON.

### <a name="concat"></a>spojuje
Zřetězí jeden nebo více řetězců.

Například pokud výstupní hodnota `element1` Pokud `"bar"`, pak tento příklad vrátí řetězec `"foobar!"`:

```json
"[concat('foo', steps('step1').element1, '!')]"
```

### <a name="substring"></a>substring
Vrátí podřetězec určeného řetězce. Podřetězec začíná zadaným indexem a má zadanou délku.

Následující příklad vrátí `"ftw"`:

```json
"[substring('azure-ftw!!!1one', 6, 3)]"
```

### <a name="replace"></a>replace
Vrátí řetězec, ve kterém jsou všechny výskyty zadaného řetězce v aktuálním řetězci nahrazeny jiným řetězcem.

Následující příklad vrátí `"Everything is awesome!"`:

```json
"[replace('Everything is terrible!', 'terrible', 'awesome')]"
```

### <a name="guid"></a>guid
Generuje globálně jedinečný řetězec (GUID).

Následující příklad může vracet `"c7bc8bdc-7252-4a82-ba53-7c468679a511"`:

```json
"[guid()]"
```

### <a name="tolower"></a>toLower
Vrátí řetězec převedený na malá písmena.

Následující příklad vrátí `"foobar"`:

```json
"[toLower('FOOBAR')]"
```

### <a name="toupper"></a>toUpper
Vrátí řetězec převedený na velká písmena.

Následující příklad vrátí `"FOOBAR"`:

```json
"[toUpper('foobar')]"
```

## <a name="collection-functions"></a>Funkce kolekce
Tyto funkce lze použít s kolekcemi, jako jsou řetězce JSON, pole a objekty.

### <a name="contains"></a>Obsahuje
Vrátí `true`, pokud řetězec obsahuje zadaný podřetězec, pole obsahuje zadanou hodnotu nebo objekt obsahuje zadaný klíč.

#### <a name="example-1-string"></a>Příklad 1: řetězec
Následující příklad vrátí `true`:

```json
"[contains('foobar', 'foo')]"
```

#### <a name="example-2-array"></a>Příklad 2: pole
Předpokládat `element1` vrátí `[1, 2, 3]`. Následující příklad vrátí `false`:

```json
"[contains(steps('foo').element1, 4)]"
```

#### <a name="example-3-object"></a>Příklad 3: objekt
Předpokládejme, `element1` vrátí:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Následující příklad vrátí `true`:

```json
"[contains(steps('foo').element1, 'key1')]"
```

### <a name="length"></a>length
Vrátí počet znaků v řetězci, počet hodnot v poli nebo počet klíčů v objektu.

#### <a name="example-1-string"></a>Příklad 1: řetězec
Následující příklad vrátí `6`:

```json
"[length('foobar')]"
```

#### <a name="example-2-array"></a>Příklad 2: pole
Předpokládat `element1` vrátí `[1, 2, 3]`. Následující příklad vrátí `3`:

```json
"[length(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Příklad 3: objekt
Předpokládejme, `element1` vrátí:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Následující příklad vrátí `2`:

```json
"[length(steps('foo').element1)]"
```

### <a name="empty"></a>Prázdná
Vrátí `true`, pokud má řetězec, pole nebo objekt hodnotu null nebo je prázdný.

#### <a name="example-1-string"></a>Příklad 1: řetězec
Následující příklad vrátí `true`:

```json
"[empty('')]"
```

#### <a name="example-2-array"></a>Příklad 2: pole
Předpokládat `element1` vrátí `[1, 2, 3]`. Následující příklad vrátí `false`:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Příklad 3: objekt
Předpokládejme, `element1` vrátí:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Následující příklad vrátí `false`:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-4-null-and-undefined"></a>Příklad 4: null a Nedefinováno
Přepokládejme, `element1` je `null` nebo undefined. Následující příklad vrátí `true`:

```json
"[empty(steps('foo').element1)]"
```

### <a name="first"></a>první
Vrátí první znak zadaného řetězce; první hodnota určeného pole; nebo první klíč a hodnota zadaného objektu.

#### <a name="example-1-string"></a>Příklad 1: řetězec
Následující příklad vrátí `"f"`:

```json
"[first('foobar')]"
```

#### <a name="example-2-array"></a>Příklad 2: pole
Předpokládat `element1` vrátí `[1, 2, 3]`. Následující příklad vrátí `1`:

```json
"[first(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Příklad 3: objekt
Předpokládejme, `element1` vrátí:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
Následující příklad vrátí `{"key1": "foobar"}`:

```json
"[first(steps('foo').element1)]"
```

### <a name="last"></a>poslední
Vrátí poslední znak zadaného řetězce, poslední hodnotu zadaného pole nebo poslední klíč a hodnotu zadaného objektu.

#### <a name="example-1-string"></a>Příklad 1: řetězec
Následující příklad vrátí `"r"`:

```json
"[last('foobar')]"
```

#### <a name="example-2-array"></a>Příklad 2: pole
Předpokládat `element1` vrátí `[1, 2, 3]`. Následující příklad vrátí `2`:

```json
"[last(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Příklad 3: objekt
Předpokládejme, `element1` vrátí:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Následující příklad vrátí `{"key2": "raboof"}`:

```json
"[last(steps('foo').element1)]"
```

### <a name="take"></a>nezbytná
Vrátí zadaný počet souvislých znaků od začátku řetězce, zadaného počtu souvislých hodnot od začátku pole nebo zadaného počtu souvislých klíčů a hodnot od začátku objektu.

#### <a name="example-1-string"></a>Příklad 1: řetězec
Následující příklad vrátí `"foo"`:

```json
"[take('foobar', 3)]"
```

#### <a name="example-2-array"></a>Příklad 2: pole
Předpokládat `element1` vrátí `[1, 2, 3]`. Následující příklad vrátí `[1, 2]`:

```json
"[take(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Příklad 3: objekt
Předpokládejme, `element1` vrátí:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```

Následující příklad vrátí `{"key1": "foobar"}`:

```json
"[take(steps('foo').element1, 1)]"
```

### <a name="skip"></a>přímo
Obchází zadaný počet prvků v kolekci a vrátí zbývající prvky.

#### <a name="example-1-string"></a>Příklad 1: řetězec
Následující příklad vrátí `"bar"`:

```json
"[skip('foobar', 3)]"
```

#### <a name="example-2-array"></a>Příklad 2: pole
Předpokládat `element1` vrátí `[1, 2, 3]`. Následující příklad vrátí `[3]`:

```json
"[skip(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Příklad 3: objekt
Předpokládejme, `element1` vrátí:

```json
{
  "key1": "foobar",
  "key2": "raboof"
}
```
Následující příklad vrátí `{"key2": "raboof"}`:

```json
"[skip(steps('foo').element1, 1)]"
```

## <a name="logical-functions"></a>Logické funkce
Tyto funkce lze použít v podmíněných podmínkách. Některé funkce nemusí podporovat všechny datové typy JSON.

### <a name="equals"></a>rovná se
Vrátí `true`, pokud mají oba parametry stejný typ a hodnotu. Tato funkce podporuje všechny datové typy JSON.

Následující příklad vrátí `true`:

```json
"[equals(0, 0)]"
```

Následující příklad vrátí `true`:

```json
"[equals('foo', 'foo')]"
```

Následující příklad vrátí `false`:

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

### <a name="less"></a>tolik
Vrátí `true`, pokud je první parametr striktně menší než druhý parametr. Tato funkce podporuje pouze parametry typu Number a String.

Následující příklad vrátí `true`:

```json
"[less(1, 2)]"
```

Následující příklad vrátí `false`:

```json
"[less('9', '10')]"
```

### <a name="lessorequals"></a>lessOrEquals
Vrátí `true`, pokud je první parametr menší nebo roven druhému parametru. Tato funkce podporuje pouze parametry typu Number a String.

Následující příklad vrátí `true`:

```json
"[lessOrEquals(2, 2)]"
```

### <a name="greater"></a>zvýšen
Vrátí `true`, pokud je první parametr striktně větší než druhý parametr. Tato funkce podporuje pouze parametry typu Number a String.

Následující příklad vrátí `false`:

```json
"[greater(1, 2)]"
```

Následující příklad vrátí `true`:

```json
"[greater('9', '10')]"
```

### <a name="greaterorequals"></a>greaterOrEquals
Vrátí `true`, pokud je první parametr větší než nebo roven druhému parametru. Tato funkce podporuje pouze parametry typu Number a String.

Následující příklad vrátí `true`:

```json
"[greaterOrEquals(2, 2)]"
```

### <a name="and"></a>a
Vrátí `true`, pokud jsou všechny parametry vyhodnoceny jako `true`. Tato funkce podporuje dva nebo více parametrů pouze typu Boolean.

Následující příklad vrátí `true`:

```json
"[and(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

Následující příklad vrátí `false`:

```json
"[and(equals(0, 0), greater(1, 2))]"
```

### <a name="or"></a>nebo
Vrátí `true`, pokud je alespoň jeden z parametrů vyhodnocen jako `true`. Tato funkce podporuje dva nebo více parametrů pouze typu Boolean.

Následující příklad vrátí `true`:

```json
"[or(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

Následující příklad vrátí `true`:

```json
"[or(equals(0, 0), greater(1, 2))]"
```

### <a name="not"></a>mění
Vrátí `true`, pokud je parametr vyhodnocen jako `false`. Tato funkce podporuje pouze parametry typu Boolean.

Následující příklad vrátí `true`:

```json
"[not(false)]"
```

Následující příklad vrátí `false`:

```json
"[not(equals(0, 0))]"
```

### <a name="coalesce"></a>COALESCE
Vrací hodnotu prvního parametru, který není null. Tato funkce podporuje všechny datové typy JSON.

Předpokládat `element1` a `element2` nejsou definovány. Následující příklad vrátí `"foobar"`:

```json
"[coalesce(steps('foo').element1, steps('foo').element2, 'foobar')]"
```

## <a name="conversion-functions"></a>Převodní funkce
Tyto funkce lze použít pro převod hodnot mezi datovými typy JSON a kódování.

### <a name="int"></a>int
Převede parametr na celé číslo. Tato funkce podporuje parametry typu Number a String.

Následující příklad vrátí `1`:

```json
"[int('1')]"
```

Následující příklad vrátí `2`:

```json
"[int(2.9)]"
```

### <a name="float"></a>float
Převede parametr na plovoucí desetinnou čárku. Tato funkce podporuje parametry typu Number a String.

Následující příklad vrátí `1.0`:

```json
"[float('1.0')]"
```

Následující příklad vrátí `2.9`:

```json
"[float(2.9)]"
```

### <a name="string"></a>řetězec
Převede parametr na řetězec. Tato funkce podporuje parametry všech datových typů JSON.

Následující příklad vrátí `"1"`:

```json
"[string(1)]"
```

Následující příklad vrátí `"2.9"`:

```json
"[string(2.9)]"
```

Následující příklad vrátí `"[1,2,3]"`:

```json
"[string([1,2,3])]"
```

Následující příklad vrátí `"{"foo":"bar"}"`:

```json
"[string({\"foo\":\"bar\"})]"
```

### <a name="bool"></a>logick
Převede parametr na logickou hodnotu. Tato funkce podporuje parametry typu Number, String a Boolean. Podobně jako logické hodnoty v JavaScriptu, jakákoli hodnota kromě `0` nebo `'false'` vrátí `true`.

Následující příklad vrátí `true`:

```json
"[bool(1)]"
```

Následující příklad vrátí `false`:

```json
"[bool(0)]"
```

Následující příklad vrátí `true`:

```json
"[bool(true)]"
```

Následující příklad vrátí `true`:

```json
"[bool('true')]"
```

### <a name="parse"></a>Analýza
Převede parametr na nativní typ. Jinými slovy je tato funkce inverzní `string()`. Tato funkce podporuje pouze parametry typu String.

Následující příklad vrátí `1`:

```json
"[parse('1')]"
```

Následující příklad vrátí `true`:

```json
"[parse('true')]"
```

Následující příklad vrátí `[1,2,3]`:

```json
"[parse('[1,2,3]')]"
```

Následující příklad vrátí `{"foo":"bar"}`:

```json
"[parse('{\"foo\":\"bar\"}')]"
```

### <a name="encodebase64"></a>encodeBase64
Zakóduje parametr do řetězce kódovaného pomocí základního 64. Tato funkce podporuje pouze parametry typu String.

Následující příklad vrátí `"Zm9vYmFy"`:

```json
"[encodeBase64('foobar')]"
```

### <a name="decodebase64"></a>decodeBase64
Dekóduje parametr ze kódovaného řetězce Base-64. Tato funkce podporuje pouze parametry typu String.

Následující příklad vrátí `"foobar"`:

```json
"[decodeBase64('Zm9vYmFy')]"
```

### <a name="encodeuricomponent"></a>encodeUriComponent
Zakóduje parametr do řetězce kódovaného adresou URL. Tato funkce podporuje pouze parametry typu String.

Následující příklad vrátí `"https%3A%2F%2Fportal.azure.com%2F"`:

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

### <a name="decodeuricomponent"></a>decodeUriComponent
Dekóduje parametr z řetězce kódovaného adresou URL. Tato funkce podporuje pouze parametry typu String.

Následující příklad vrátí `"https://portal.azure.com/"`:

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="math-functions"></a>Matematické funkce
### <a name="add"></a>add
Přidá dvě čísla a vrátí výsledek.

Následující příklad vrátí `3`:

```json
"[add(1, 2)]"
```

### <a name="sub"></a>sub
Odečte druhé číslo od prvního čísla a vrátí výsledek.

Následující příklad vrátí `1`:

```json
"[sub(3, 2)]"
```

### <a name="mul"></a>mul
Vynásobí dvě čísla a vrátí výsledek.

Následující příklad vrátí `6`:

```json
"[mul(2, 3)]"
```

### <a name="div"></a>div
Vydělí první číslo druhým číslem a vrátí výsledek. Výsledkem je vždy celé číslo.

Následující příklad vrátí `2`:

```json
"[div(6, 3)]"
```

### <a name="mod"></a>střední
Vydělí první číslo druhým číslem a vrátí zbytek.

Následující příklad vrátí `0`:

```json
"[mod(6, 3)]"
```

Následující příklad vrátí `2`:

```json
"[mod(6, 4)]"
```

### <a name="min"></a>min
Vrátí malé ze dvou čísel.

Následující příklad vrátí `1`:

```json
"[min(1, 2)]"
```

### <a name="max"></a>max
Vrátí větší z těchto dvou čísel.

Následující příklad vrátí `2`:

```json
"[max(1, 2)]"
```

### <a name="range"></a>oblasti
Vygeneruje posloupnost integrálních čísel v zadaném rozsahu.

Následující příklad vrátí `[1,2,3]`:

```json
"[range(1, 3)]"
```

### <a name="rand"></a>funkcí
Vrátí náhodné integrální číslo v zadaném rozsahu. Tato funkce negeneruje kryptograficky zabezpečená náhodná čísla.

Následující příklad může vracet `42`:

```json
"[rand(-100, 100)]"
```

### <a name="floor"></a>Floor
Vrací největší celé číslo menší nebo rovné zadanému číslu.

Následující příklad vrátí `3`:

```json
"[floor(3.14)]"
```

### <a name="ceil"></a>ceil –
Vrátí největší celé číslo větší než nebo rovno zadanému číslu.

Následující příklad vrátí `4`:

```json
"[ceil(3.14)]"
```

## <a name="date-functions"></a>Funkce data
### <a name="utcnow"></a>utcNow
Vrátí řetězec ve formátu ISO 8601 aktuálního data a času v místním počítači.

Následující příklad může vracet `"1990-12-31T23:59:59.000Z"`:

```json
"[utcNow()]"
```

### <a name="addseconds"></a>addSeconds
Přidá do zadaného časového razítka celočíselný počet sekund.

Následující příklad vrátí `"1991-01-01T00:00:00.000Z"`:

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

### <a name="addminutes"></a>addMinutes
Přidá do zadaného časového razítka celočíselný počet minut.

Následující příklad vrátí `"1991-01-01T00:00:59.000Z"`:

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

### <a name="addhours"></a>addHours
Přidá celočíselný počet hodin do zadaného časového razítka.

Následující příklad vrátí `"1991-01-01T00:59:59.000Z"`:

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="next-steps"></a>Další kroky
* Úvod do Azure Resource Manager najdete v tématu [Azure Resource Manager Overview](../management/overview.md).

