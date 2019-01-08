---
title: Vytvoření funkce definic uživatelského rozhraní Azure spravované aplikace | Dokumentace Microsoftu
description: Popisuje funkce pro použití při vytváření definice uživatelského rozhraní pro spravované aplikace Azure
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2017
ms.author: tomfitz
ms.openlocfilehash: 80fd593eecf189d516a8c9d7ef2a94ec9f23fc39
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2019
ms.locfileid: "54063790"
---
# <a name="createuidefinition-functions"></a>Funkcí CreateUiDefinition
Tato část obsahuje podpisy pro všechny podporované funkce CreateUiDefinition.

Pokud chcete používat funkce, před a za deklaraci do složených závorek. Příklad:

```json
"[function()]"
```

Řetězce a další funkce může být odkazováno jako parametry pro funkci, ale řetězce musí být uzavřen v jednoduchých uvozovkách. Příklad:

```json
"[fn1(fn2(), 'foobar')]"
```

Případně můžete odkazovat na vlastnosti výstupu funkce pomocí operátoru tečka. Příklad:

```json
"[func().prop1]"
```

## <a name="referencing-functions"></a>Funkce odkazů
Tyto funkce slouží k odkazování výstupy z vlastnosti nebo kontextu CreateUiDefinition.

### <a name="basics"></a>Základy
Vrátí výstupní hodnoty elementu, který je definovaný v kroku základy.

Následující příklad vrátí její výstup element s názvem `foo` v základní informace o kroku:

```json
"[basics('foo')]"
```

### <a name="steps"></a>kroky
Vrátí výstupní hodnoty elementu, který je definován v zadané kroku. Výstupní hodnoty prvků v základní informace o kroku získáte pomocí `basics()` místo.

Následující příklad vrátí její výstup element s názvem `bar` v kroku s názvem `foo`:

```json
"[steps('foo').bar]"
```

### <a name="location"></a>location
Vrátí umístění vybrané v kroku základy nebo aktuálního kontextu.

Můžou se zobrazovat v následujícím příkladu `"westus"`:

```json
"[location()]"
```

## <a name="string-functions"></a>Funkce řetězců
Tyto funkce jde použít jenom s řetězci JSON.

### <a name="concat"></a>concat
Zřetězí nejmíň jeden řetězec.

Například pokud výstupní hodnota z `element1` Pokud `"bar"`, pak v tomto příkladu vrátí řetězec `"foobar!"`:

```json
"[concat('foo', steps('step1').element1, '!')]"
```

### <a name="substring"></a>dílčí řetězec
Vrátí podřetězec zadaného řetězce. Podřetězec začíná v zadaném indexu a má určené délky.

Následující příklad vrátí `"ftw"`:

```json
"[substring('azure-ftw!!!1one', 6, 3)]"
```

### <a name="replace"></a>nahradit
Vrátí řetězec, ve kterém se nahradí všechny výskyty zadaný řetězec do aktuálního řetězce jiným řetězcem.

Následující příklad vrátí `"Everything is awesome!"`:

```json
"[replace('Everything is terrible!', 'terrible', 'awesome')]"
```

### <a name="guid"></a>identifikátor GUID
Vygeneruje globálně jedinečný řetězec (GUID).

Můžou se zobrazovat v následujícím příkladu `"c7bc8bdc-7252-4a82-ba53-7c468679a511"`:

```json
"[guid()]"
```

### <a name="tolower"></a>toLower
Vrací řetězec převedený na malá písmena.

Následující příklad vrátí `"foobar"`:

```json
"[toLower('FOOBAR')]"
```

### <a name="toupper"></a>toUpper
Vrací řetězec převedený na velká písmena.

Následující příklad vrátí `"FOOBAR"`:

```json
"[toUpper('foobar')]"
```

## <a name="collection-functions"></a>Kolekce funkcí
Tyto funkce jde použít s kolekcí, jako jsou řetězce JSON, polí a objekty.

### <a name="contains"></a>obsahuje
Vrátí `true` řetězec obsahuje zadaný podřetězec, pole obsahuje zadanou hodnotu, nebo obsahuje objekt se zadaným klíčem.

#### <a name="example-1-string"></a>Příklad 1: řetězec
Následující příklad vrátí `true`:

```json
"[contains('foobar', 'foo')]"
```

#### <a name="example-2-array"></a>Příklad 2: pole
Předpokládejme `element1` vrátí `[1, 2, 3]`. Následující příklad vrátí `false`:

```json
"[contains(steps('foo').element1, 4)]"
```

#### <a name="example-3-object"></a>Příklad 3: objekt
Předpokládejme `element1` vrátí:

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

### <a name="length"></a>Délka
Vrátí počet znaků v řetězci, počet hodnot v poli nebo počet klíčů v objektu.

#### <a name="example-1-string"></a>Příklad 1: řetězec
Následující příklad vrátí `6`:

```json
"[length('foobar')]"
```

#### <a name="example-2-array"></a>Příklad 2: pole
Předpokládejme `element1` vrátí `[1, 2, 3]`. Následující příklad vrátí `3`:

```json
"[length(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Příklad 3: objekt
Předpokládejme `element1` vrátí:

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
Vrátí `true` Pokud řetězec, pole nebo objekt má hodnotu null nebo prázdný.

#### <a name="example-1-string"></a>Příklad 1: řetězec
Následující příklad vrátí `true`:

```json
"[empty('')]"
```

#### <a name="example-2-array"></a>Příklad 2: pole
Předpokládejme `element1` vrátí `[1, 2, 3]`. Následující příklad vrátí `false`:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Příklad 3: objekt
Předpokládejme `element1` vrátí:

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

#### <a name="example-4-null-and-undefined"></a>Příklad 4: null a Nedefinovaná
Předpokládejme `element1` je `null` nebo není definovaný. Následující příklad vrátí `true`:

```json
"[empty(steps('foo').element1)]"
```

### <a name="first"></a>první
Vrátí první znak zadaného řetězce; první hodnotu zadaného pole. nebo první klíč a hodnotu zadaného objektu.

#### <a name="example-1-string"></a>Příklad 1: řetězec
Následující příklad vrátí `"f"`:

```json
"[first('foobar')]"
```

#### <a name="example-2-array"></a>Příklad 2: pole
Předpokládejme `element1` vrátí `[1, 2, 3]`. Následující příklad vrátí `1`:

```json
"[first(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Příklad 3: objekt
Předpokládejme `element1` vrátí:

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
Vrátí poslední znak zadaného řetězce, poslední hodnotu zadaného pole, nebo poslední klíč a hodnotu zadaného objektu.

#### <a name="example-1-string"></a>Příklad 1: řetězec
Následující příklad vrátí `"r"`:

```json
"[last('foobar')]"
```

#### <a name="example-2-array"></a>Příklad 2: pole
Předpokládejme `element1` vrátí `[1, 2, 3]`. Následující příklad vrátí `2`:

```json
"[last(steps('foo').element1)]"
```

#### <a name="example-3-object"></a>Příklad 3: objekt
Předpokládejme `element1` vrátí:

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

### <a name="take"></a>Take
Vrátí zadaný počet souvislých znaků od začátku řetězce, zadaný počet souvislých hodnot od samého začátku pole nebo zadaný počet souvislých klíče a hodnoty od začátku objektu.

#### <a name="example-1-string"></a>Příklad 1: řetězec
Následující příklad vrátí `"foo"`:

```json
"[take('foobar', 3)]"
```

#### <a name="example-2-array"></a>Příklad 2: pole
Předpokládejme `element1` vrátí `[1, 2, 3]`. Následující příklad vrátí `[1, 2]`:

```json
"[take(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Příklad 3: objekt
Předpokládejme `element1` vrátí:

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

### <a name="skip"></a>přeskočit
Vynechá zadaný počet prvků v kolekci a vrátí zbývající prvky.

#### <a name="example-1-string"></a>Příklad 1: řetězec
Následující příklad vrátí `"bar"`:

```json
"[skip('foobar', 3)]"
```

#### <a name="example-2-array"></a>Příklad 2: pole
Předpokládejme `element1` vrátí `[1, 2, 3]`. Následující příklad vrátí `[3]`:

```json
"[skip(steps('foo').element1, 2)]"
```

#### <a name="example-3-object"></a>Příklad 3: objekt
Předpokládejme `element1` vrátí:

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
Tyto funkce lze použít v podmínkách. Některé funkce nemusí podporovat všechny typy dat JSON.

### <a name="equals"></a>rovná se
Vrátí `true` Pokud oba parametry mají stejný typ a hodnotu. Tato funkce podporuje všechny typy dat JSON.

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

### <a name="less"></a>méně
Vrátí `true` Pokud první parametr je striktně menší než druhý parametr. Tato funkce podporuje parametry pouze typ čísla a řetězce.

Následující příklad vrátí `true`:

```json
"[less(1, 2)]"
```

Následující příklad vrátí `false`:

```json
"[less('9', '10')]"
```

### <a name="lessorequals"></a>lessOrEquals
Vrátí `true` Pokud první parametr je menší než druhý parametr. Tato funkce podporuje parametry pouze typ čísla a řetězce.

Následující příklad vrátí `true`:

```json
"[lessOrEquals(2, 2)]"
```

### <a name="greater"></a>větší
Vrátí `true` Pokud první parametr je výhradně větší než druhý parametr. Tato funkce podporuje parametry pouze typ čísla a řetězce.

Následující příklad vrátí `false`:

```json
"[greater(1, 2)]"
```

Následující příklad vrátí `true`:

```json
"[greater('9', '10')]"
```

### <a name="greaterorequals"></a>greaterOrEquals
Vrátí `true` Pokud první parametr je větší než nebo rovna hodnotě druhý parametr. Tato funkce podporuje parametry pouze typ čísla a řetězce.

Následující příklad vrátí `true`:

```json
"[greaterOrEquals(2, 2)]"
```

### <a name="and"></a>a
Vrátí `true` Pokud mají všechny parametry `true`. Tato funkce podporuje dva nebo více parametrů pouze typu Boolean.

Následující příklad vrátí `true`:

```json
"[and(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

Následující příklad vrátí `false`:

```json
"[and(equals(0, 0), greater(1, 2))]"
```

### <a name="or"></a>nebo
Vrátí `true` Pokud se alespoň jeden z parametrů vyhodnotí jako `true`. Tato funkce podporuje dva nebo více parametrů pouze typu Boolean.

Následující příklad vrátí `true`:

```json
"[or(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

Následující příklad vrátí `true`:

```json
"[or(equals(0, 0), greater(1, 2))]"
```

### <a name="not"></a>ne
Vrátí `true` Pokud se vyhodnotí jako parametr `false`. Tato funkce podporuje jenom parametry typu logická hodnota.

Následující příklad vrátí `true`:

```json
"[not(false)]"
```

Následující příklad vrátí `false`:

```json
"[not(equals(0, 0))]"
```

### <a name="coalesce"></a>sloučení
Vrací hodnotu prvního parametru jinou hodnotu než null. Tato funkce podporuje všechny typy dat JSON.

Předpokládejme `element1` a `element2` nejsou definovány. Následující příklad vrátí `"foobar"`:

```json
"[coalesce(steps('foo').element1, steps('foo').element2, 'foobar')]"
```

## <a name="conversion-functions"></a>Převodní funkce
Tyto funkce lze použít k převodu hodnoty mezi typy dat JSON a kódování.

### <a name="int"></a>int
Převede parametr na celé číslo. Tato funkce podporuje parametry typu čísla a řetězce.

Následující příklad vrátí `1`:

```json
"[int('1')]"
```

Následující příklad vrátí `2`:

```json
"[int(2.9)]"
```

### <a name="float"></a>float
Převede parametr na plovoucí desetinné čárky. Tato funkce podporuje parametry typu čísla a řetězce.

Následující příklad vrátí `1.0`:

```json
"[float('1.0')]"
```

Následující příklad vrátí `2.9`:

```json
"[float(2.9)]"
```

### <a name="string"></a>řetězec
Převede parametr na řetězec. Tato funkce podporuje parametry všech typů dat JSON.

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

### <a name="bool"></a>BOOL
Převede parametr na logickou hodnotu. Tato funkce podporuje parametry typu number, string a Boolean. Podobně jako u logických hodnot v jazyce JavaScript, hodnot s výjimkou `0` nebo `'false'` vrátí `true`.

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

### <a name="parse"></a>Analýzy
Převede parametr na nativní typ. Jinými slovy, tato funkce je opakem `string()`. Tato funkce podporuje jenom parametry typu string.

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
Zakóduje parametr na řetězec kódovaný base-64. Tato funkce podporuje jenom parametry typu string.

Následující příklad vrátí `"Zm9vYmFy"`:

```json
"[encodeBase64('foobar')]"
```

### <a name="decodebase64"></a>decodeBase64
Dekóduje parametr z base-64 kódovaný řetězec. Tato funkce podporuje jenom parametry typu string.

Následující příklad vrátí `"foobar"`:

```json
"[decodeBase64('Zm9vYmFy')]"
```

### <a name="encodeuricomponent"></a>encodeuricomponent –
Zakóduje parametr na řetězec kódování URL. Tato funkce podporuje jenom parametry typu string.

Následující příklad vrátí `"https%3A%2F%2Fportal.azure.com%2F"`:

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

### <a name="decodeuricomponent"></a>decodeuricomponent –
Dekóduje parametr z řetězce kódování URL. Tato funkce podporuje jenom parametry typu string.

Následující příklad vrátí `"https://portal.azure.com/"`:

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="math-functions"></a>Matematické funkce
### <a name="add"></a>add
Sečte dvě čísla a vrátí výsledek.

Následující příklad vrátí `3`:

```json
"[add(1, 2)]"
```

### <a name="sub"></a>Sub
Odečte druhé číslo z první čísla a vrátí výsledek.

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
Vydělí číslo druhé číslo první a vrátí výsledek. Výsledkem je vždycky celé číslo.

Následující příklad vrátí `2`:

```json
"[div(6, 3)]"
```

### <a name="mod"></a>MOD
Vydělí číslo druhé číslo první a vrátí zbytek.

Následující příklad vrátí `0`:

```json
"[mod(6, 3)]"
```

Následující příklad vrátí `2`:

```json
"[mod(6, 4)]"
```

### <a name="min"></a>min
Vrátí malé daných dvou čísel.

Následující příklad vrátí `1`:

```json
"[min(1, 2)]"
```

### <a name="max"></a>max
Vrátí větší z daných dvou čísel.

Následující příklad vrátí `2`:

```json
"[max(1, 2)]"
```

### <a name="range"></a>rozsah
Generuje posloupnost integrálních čísel v zadaném rozsahu.

Následující příklad vrátí `[1,2,3]`:

```json
"[range(1, 3)]"
```

### <a name="rand"></a>rand
Vrátí náhodné celé číslo v zadaném rozsahu. Tato funkce negeneruje kryptograficky zabezpečená náhodná čísla.

Můžou se zobrazovat v následujícím příkladu `42`:

```json
"[rand(-100, 100)]"
```

### <a name="floor"></a>Dolní mez
Vrátí největší celé číslo menší nebo rovna zadané číslo.

Následující příklad vrátí `3`:

```json
"[floor(3.14)]"
```

### <a name="ceil"></a>Ceil –
Vrátí největší celé číslo větší než nebo rovna hodnotě zadané číslo.

Následující příklad vrátí `4`:

```json
"[ceil(3.14)]"
```

## <a name="date-functions"></a>Datové funkce
### <a name="utcnow"></a>utcNow
Vrací řetězec ve formátu ISO 8601 aktuální datum a čas v místním počítači.

Můžou se zobrazovat v následujícím příkladu `"1990-12-31T23:59:59.000Z"`:

```json
"[utcNow()]"
```

### <a name="addseconds"></a>Přidat_sekundy
Přidá celočíselný počet sekund do zadaného časového razítka.

Následující příklad vrátí `"1991-01-01T00:00:00.000Z"`:

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

### <a name="addminutes"></a>addMinutes
Přidá celočíselný počet minut do zadaného časového razítka.

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

## <a name="next-steps"></a>Další postup
* Úvod do Azure Resource Manageru, najdete v článku [přehled Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md).

