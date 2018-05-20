---
title: Spravované aplikace Azure vytvořit definici funkcí uživatelského rozhraní | Microsoft Docs
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
ms.openlocfilehash: a01a59a7e8c9757cb41d328cd26a34fa219f9152
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="createuidefinition-functions"></a>Funkce CreateUiDefinition
Tato část obsahuje podpisy pro všechny podporované funkce CreateUiDefinition.

Pokud chcete používat funkci, uzavřete deklaraci do složených závorek. Příklad:

```json
"[function()]"
```

Řetězce a dalších funkcí, může být odkazován jako parametry pro funkci, ale řetězce musí být uzavřena do jednoduchých uvozovek. Příklad:

```json
"[fn1(fn2(), 'foobar')]"
```

Případně vlastnosti výstupu funkce můžete odkazovat pomocí operátoru tečka. Příklad:

```json
"[func().prop1]"
```

## <a name="referencing-functions"></a>Funkce odkazů
Tyto funkce slouží k odkazování výstupy z vlastnosti nebo kontextu CreateUiDefinition.

### <a name="basics"></a>Základy
Vrátí hodnoty výstup elementu, který je definovaný v kroku základy.

Následující příklad vrátí její výstup elementu s názvem `foo` v základní informace o kroku:

```json
"[basics('foo')]"
```

### <a name="steps"></a>kroky
Vrátí hodnoty výstup elementu, který je definován v zadané kroku. Výstupní hodnoty elementů v kroku základy, použijte `basics()` místo.

Následující příklad vrátí její výstup elementu s názvem `bar` v kroku s názvem `foo`:

```json
"[steps('foo').bar]"
```

### <a name="location"></a>location
Vrátí umístění vybrané v kroku základy nebo aktuální kontext.

V následujícím příkladu může vrátit `"westus"`:

```json
"[location()]"
```

## <a name="string-functions"></a>Řetězcové funkce
Tyto funkce slouží pouze s řetězce formátu JSON.

### <a name="concat"></a>concat
Zřetězí nejméně jeden řetězec.

Například pokud hodnota výstupu `element1` Pokud `"bar"`, pak tento příklad vrátí řetězec `"foobar!"`:

```json
"[concat('foo', steps('step1').element1), '!']"
```

### <a name="substring"></a>dílčí řetězec
Vrátí dílčí řetězec zadaného řetězce. Dílčí řetězec spustí v zadaném indexu a má na určenou délku.

Následující příklad vrací `"ftw"`:

```json
"[substring('azure-ftw!!!1one', 6, 3)]"
```

### <a name="replace"></a>Nahradit
Vrátí řetězec, ve kterém jsou všechny výskyty zadaného řetězce v aktuálním řetězec nahrazen jiným řetězcem.

Následující příklad vrací `"Everything is awesome!"`:

```json
"[replace('Everything is terrible!', 'terrible', 'awesome')]"
```

### <a name="guid"></a>Identifikátor GUID
Generuje řetězec globálně jedinečný (identifikátor GUID).

V následujícím příkladu může vrátit `"c7bc8bdc-7252-4a82-ba53-7c468679a511"`:

```json
"[guid()]"
```

### <a name="tolower"></a>toLower
Vrací řetězec převedený na malá písmena.

Následující příklad vrací `"foobar"`:

```json
"[toLower('FOOBAR')]"
```

### <a name="toupper"></a>toUpper
Vrací řetězec převedený na velká písmena.

Následující příklad vrací `"FOOBAR"`:

```json
"[toUpper('foobar')]"
```

## <a name="collection-functions"></a>Kolekce funkcí
Tyto funkce slouží ke kolekcím, jako je řetězce formátu JSON, pole a objekty.

### <a name="contains"></a>obsahuje
Vrátí `true` řetězec obsahuje určený dílčí řetězec, pole obsahuje zadanou hodnotu, nebo objekt obsahuje zadaný klíč.

#### <a name="example-1-string"></a>Příklad 1: řetězec
Následující příklad vrací `true`:

```json
"[contains('foobar', 'foo')]"
```

#### <a name="example-2-array"></a>Příklad 2: pole
Předpokládejme `element1` vrátí `[1, 2, 3]`. Následující příklad vrací `false`:

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

Následující příklad vrací `true`:

```json
"[contains(steps('foo').element1, 'key1')]"
```

### <a name="length"></a>Délka
Vrátí počet znaků v řetězci, počet hodnot v poli nebo počet klíčů v objektu.

#### <a name="example-1-string"></a>Příklad 1: řetězec
Následující příklad vrací `6`:

```json
"[length('foobar')]"
```

#### <a name="example-2-array"></a>Příklad 2: pole
Předpokládejme `element1` vrátí `[1, 2, 3]`. Následující příklad vrací `3`:

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

Následující příklad vrací `2`:

```json
"[length(steps('foo').element1)]"
```

### <a name="empty"></a>prázdný
Vrátí `true` Pokud řetězec, pole nebo objekt je null nebo prázdná.

#### <a name="example-1-string"></a>Příklad 1: řetězec
Následující příklad vrací `true`:

```json
"[empty('')]"
```

#### <a name="example-2-array"></a>Příklad 2: pole
Předpokládejme `element1` vrátí `[1, 2, 3]`. Následující příklad vrací `false`:

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

Následující příklad vrací `false`:

```json
"[empty(steps('foo').element1)]"
```

#### <a name="example-4-null-and-undefined"></a>Příklad 4: null a Nedefinovaná
Předpokládejme `element1` je `null` nebo nedefinované. Následující příklad vrací `true`:

```json
"[empty(steps('foo').element1)]"
```

### <a name="first"></a>první
Vrátí první znak zadaný řetězec; první hodnotu zadaného pole; nebo první klíč a hodnotu zadaného objektu.

#### <a name="example-1-string"></a>Příklad 1: řetězec
Následující příklad vrací `"f"`:

```json
"[first('foobar')]"
```

#### <a name="example-2-array"></a>Příklad 2: pole
Předpokládejme `element1` vrátí `[1, 2, 3]`. Následující příklad vrací `1`:

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
Následující příklad vrací `{"key1": "foobar"}`:

```json
"[first(steps('foo').element1)]"
```

### <a name="last"></a>poslední
Vrátí poslední znak zadaný řetězec, poslední hodnotu zadaného pole nebo poslední klíč a hodnotu zadaného objektu.

#### <a name="example-1-string"></a>Příklad 1: řetězec
Následující příklad vrací `"r"`:

```json
"[last('foobar')]"
```

#### <a name="example-2-array"></a>Příklad 2: pole
Předpokládejme `element1` vrátí `[1, 2, 3]`. Následující příklad vrací `2`:

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

Následující příklad vrací `{"key2": "raboof"}`:

```json
"[last(steps('foo').element1)]"
```

### <a name="take"></a>proveďte
Vrátí zadaný počet po sobě jdoucích znaků od začátku řetězce, zadaný počet souvislý hodnoty od začátku pole nebo zadaný počet sousedních klíčů a hodnoty ze začátku objektu.

#### <a name="example-1-string"></a>Příklad 1: řetězec
Následující příklad vrací `"foo"`:

```json
"[take('foobar', 3)]"
```

#### <a name="example-2-array"></a>Příklad 2: pole
Předpokládejme `element1` vrátí `[1, 2, 3]`. Následující příklad vrací `[1, 2]`:

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

Následující příklad vrací `{"key1": "foobar"}`:

```json
"[take(steps('foo').element1, 1)]"
```

### <a name="skip"></a>Přeskočit
Obchází zadaný počet elementů v kolekci a vrátí zbývající elementy.

#### <a name="example-1-string"></a>Příklad 1: řetězec
Následující příklad vrací `"bar"`:

```json
"[skip('foobar', 3)]"
```

#### <a name="example-2-array"></a>Příklad 2: pole
Předpokládejme `element1` vrátí `[1, 2, 3]`. Následující příklad vrací `[3]`:

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
Následující příklad vrací `{"key2": "raboof"}`:

```json
"[skip(steps('foo').element1, 1)]"
```

## <a name="logical-functions"></a>Logické funkce
Tyto funkce lze používat ve podmíněné příkazy. Některé funkce nemusí podporovat všechny typy dat JSON.

### <a name="equals"></a>rovná se
Vrátí `true` Pokud oba parametry mají stejný typ a hodnotu. Tato funkce podporuje všechny typy dat JSON.

Následující příklad vrací `true`:

```json
"[equals(0, 0)]"
```

Následující příklad vrací `true`:

```json
"[equals('foo', 'foo')]"
```

Následující příklad vrací `false`:

```json
"[equals('abc', ['a', 'b', 'c'])]"
```

### <a name="less"></a>menší
Vrátí `true` Pokud první parametr je striktně menší než druhý parametr. Tato funkce podporuje parametrů jenom číslo typ a řetězec.

Následující příklad vrací `true`:

```json
"[less(1, 2)]"
```

Následující příklad vrací `false`:

```json
"[less('9', '10')]"
```

### <a name="lessorequals"></a>lessOrEquals
Vrátí `true` Pokud první parametr je menší než nebo rovna druhý parametr. Tato funkce podporuje parametrů jenom číslo typ a řetězec.

Následující příklad vrací `true`:

```json
"[lessOrEquals(2, 2)]"
```

### <a name="greater"></a>větší
Vrátí `true` Pokud první parametr je výhradně větší než druhý parametr. Tato funkce podporuje parametrů jenom číslo typ a řetězec.

Následující příklad vrací `false`:

```json
"[greater(1, 2)]"
```

Následující příklad vrací `true`:

```json
"[greater('9', '10')]"
```

### <a name="greaterorequals"></a>greaterOrEquals
Vrátí `true` Pokud první parametr je větší než nebo rovna hodnotě druhý parametr. Tato funkce podporuje parametrů jenom číslo typ a řetězec.

Následující příklad vrací `true`:

```json
"[greaterOrEquals(2, 2)]"
```

### <a name="and"></a>a
Vrátí `true` Pokud jsou všechny parametry vyhodnoceny `true`. Tato funkce podporuje dva nebo více parametrů pouze typu logická hodnota.

Následující příklad vrací `true`:

```json
"[and(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

Následující příklad vrací `false`:

```json
"[and(equals(0, 0), greater(1, 2))]"
```

### <a name="or"></a>nebo
Vrátí `true` Pokud je alespoň jeden z parametrů výsledkem `true`. Tato funkce podporuje dva nebo více parametrů pouze typu logická hodnota.

Následující příklad vrací `true`:

```json
"[or(equals(0, 0), equals('foo', 'foo'), less(1, 2))]"
```

Následující příklad vrací `true`:

```json
"[or(equals(0, 0), greater(1, 2))]"
```

### <a name="not"></a>ne
Vrátí `true` Pokud je parametr výsledkem `false`. Tato funkce podporuje jenom parametry typu logická hodnota.

Následující příklad vrací `true`:

```json
"[not(false)]"
```

Následující příklad vrací `false`:

```json
"[not(equals(0, 0))]"
```

### <a name="coalesce"></a>sloučení
Vrátí hodnotu prvního parametru nesmí být nulová. Tato funkce podporuje všechny typy dat JSON.

Předpokládejme `element1` a `element2` nejsou definovány. Následující příklad vrací `"foobar"`:

```json
"[coalesce(steps('foo').element1, steps('foo').element2, 'foobar')]"
```

## <a name="conversion-functions"></a>Převodní funkce
Tyto funkce lze převést hodnoty mezi typy dat JSON a kódování.

### <a name="int"></a>celá čísla
Parametr převede na celé číslo. Tato funkce podporuje parametry počet typ a řetězec.

Následující příklad vrací `1`:

```json
"[int('1')]"
```

Následující příklad vrací `2`:

```json
"[int(2.9)]"
```

### <a name="float"></a>float
Převede parametr s plovoucí desetinnou čárkou. Tato funkce podporuje parametry počet typ a řetězec.

Následující příklad vrací `1.0`:

```json
"[float('1.0')]"
```

Následující příklad vrací `2.9`:

```json
"[float(2.9)]"
```

### <a name="string"></a>řetězec
Převede parametr na řetězec. Tato funkce podporuje parametry všech typů dat JSON.

Následující příklad vrací `"1"`:

```json
"[string(1)]"
```

Následující příklad vrací `"2.9"`:

```json
"[string(2.9)]"
```

Následující příklad vrací `"[1,2,3]"`:

```json
"[string([1,2,3])]"
```

Následující příklad vrací `"{"foo":"bar"}"`:

```json
"[string({\"foo\":\"bar\"})]"
```

### <a name="bool"></a>BOOL
Parametr převede na booleovskou hodnotu. Tato funkce podporuje parametry typ čísla, řetězce a logickou hodnotu. Podobně jako u logických výrazů v jazyce JavaScript, některá hodnota s výjimkou `0` nebo `'false'` vrátí `true`.

Následující příklad vrací `true`:

```json
"[bool(1)]"
```

Následující příklad vrací `false`:

```json
"[bool(0)]"
```

Následující příklad vrací `true`:

```json
"[bool(true)]"
```

Následující příklad vrací `true`:

```json
"[bool('true')]"
```

### <a name="parse"></a>analyzovat
Převede parametr nativního typu. Jinými slovy, tato funkce je opakem `string()`. Tato funkce podporuje jenom parametry typu řetězec.

Následující příklad vrací `1`:

```json
"[parse('1')]"
```

Následující příklad vrací `true`:

```json
"[parse('true')]"
```

Následující příklad vrací `[1,2,3]`:

```json
"[parse('[1,2,3]')]"
```

Následující příklad vrací `{"foo":"bar"}`:

```json
"[parse('{\"foo\":\"bar\"}')]"
```

### <a name="encodebase64"></a>encodeBase64
Kóduje parametr na řetězec s kódováním base-64. Tato funkce podporuje jenom parametry typu řetězec.

Následující příklad vrací `"Zm9vYmFy"`:

```json
"[encodeBase64('foobar')]"
```

### <a name="decodebase64"></a>decodeBase64
Dekóduje parametru z řetězec s kódováním base-64. Tato funkce podporuje jenom parametry typu řetězec.

Následující příklad vrací `"foobar"`:

```json
"[decodeBase64('Zm9vYmFy')]"
```

### <a name="encodeuricomponent"></a>encodeuricomponent –
Kóduje parametr na řetězec kódovaná adresou URL. Tato funkce podporuje jenom parametry typu řetězec.

Následující příklad vrací `"https%3A%2F%2Fportal.azure.com%2F"`:

```json
"[encodeUriComponent('https://portal.azure.com/')]"
```

### <a name="decodeuricomponent"></a>decodeuricomponent –
Dekóduje parametr v řetězci kódovaná adresou URL. Tato funkce podporuje jenom parametry typu řetězec.

Následující příklad vrací `"https://portal.azure.com/"`:

```json
"[decodeUriComponent('https%3A%2F%2Fportal.azure.com%2F')]"
```

## <a name="math-functions"></a>Matematické funkce
### <a name="add"></a>přidat
Sečte dvě čísla a vrátí výsledek.

Následující příklad vrací `3`:

```json
"[add(1, 2)]"
```

### <a name="sub"></a>Sub –
Odečítá od druhé číslo z první číslo a vrátí výsledek.

Následující příklad vrací `1`:

```json
"[sub(3, 2)]"
```

### <a name="mul"></a>mul
Vynásobí dvou čísel a vrátí výsledek.

Následující příklad vrací `6`:

```json
"[mul(2, 3)]"
```

### <a name="div"></a>div
Rozdělí prvního čísla druhé číslo a vrátí výsledek. Výsledkem je vždy celé číslo.

Následující příklad vrací `2`:

```json
"[div(6, 3)]"
```

### <a name="mod"></a>MOD
Provede podíl prvního čísla druhé číslo a vrátí zbytek.

Následující příklad vrací `0`:

```json
"[mod(6, 3)]"
```

Následující příklad vrací `2`:

```json
"[mod(6, 4)]"
```

### <a name="min"></a>min
Vrátí malým dvou čísel.

Následující příklad vrací `1`:

```json
"[min(1, 2)]"
```

### <a name="max"></a>max
Vrátí větší dvou čísel.

Následující příklad vrací `2`:

```json
"[max(1, 2)]"
```

### <a name="range"></a>rozsah
Generuje pořadí integrální čísel v daném rozsahu.

Následující příklad vrací `[1,2,3]`:

```json
"[range(1, 3)]"
```

### <a name="rand"></a>rand –
Vrátí náhodné číslo integrální v zadaném rozsahu. Tato funkce negeneruje kryptograficky zabezpečené náhodných čísel.

V následujícím příkladu může vrátit `42`:

```json
"[rand(-100, 100)]"
```

### <a name="floor"></a>Floor
Vrátí největší celé číslo menší než nebo rovna zadané číslo.

Následující příklad vrací `3`:

```json
"[floor(3.14)]"
```

### <a name="ceil"></a>ceil
Vrátí největší celé číslo větší než nebo rovna hodnotě zadané číslo.

Následující příklad vrací `4`:

```json
"[ceil(3.14)]"
```

## <a name="date-functions"></a>Datové funkce
### <a name="utcnow"></a>utcNow
Vrací řetězec ve formátu ISO 8601 aktuální datum a čas v místním počítači.

V následujícím příkladu může vrátit `"1990-12-31T23:59:59.000Z"`:

```json
"[utcNow()]"
```

### <a name="addseconds"></a>Přidat_sekundy
Přidá celočíselný počet sekund pro zadané časové razítko.

Následující příklad vrací `"1991-01-01T00:00:00.000Z"`:

```json
"[addSeconds('1990-12-31T23:59:60Z', 1)]"
```

### <a name="addminutes"></a>addMinutes
Přidá celočíselný počet minut pro zadané časové razítko.

Následující příklad vrací `"1991-01-01T00:00:59.000Z"`:

```json
"[addMinutes('1990-12-31T23:59:59Z', 1)]"
```

### <a name="addhours"></a>addHours
Přidá celočíselný počet hodin na zadané časové razítko.

Následující příklad vrací `"1991-01-01T00:59:59.000Z"`:

```json
"[addHours('1990-12-31T23:59:59Z', 1)]"
```

## <a name="next-steps"></a>Další postup
* Úvod do Azure Resource Manageru, najdete v části [přehled Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md).

