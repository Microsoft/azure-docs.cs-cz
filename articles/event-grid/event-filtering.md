---
title: Filtrování událostí pro Azure Event Grid
description: Popisuje, jak filtrovat události při vytváření předplatného Azure Event Grid.
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 94445341891149d5d02c7f33caef20bf45123e9b
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102197771"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Principy filtrování událostí pro předplatná Event Grid

Tento článek popisuje různé způsoby, jak filtrovat, které události se odešlou do vašeho koncového bodu. Při vytváření odběru událostí máte k dispozici tři možnosti pro filtrování:

* Typy událostí
* Předmět začíná na nebo končí
* Pokročilá pole a operátory

## <a name="event-type-filtering"></a>Filtrování typu události

Ve výchozím nastavení jsou všechny [typy událostí](event-schema.md) pro zdroj události odesílány do koncového bodu. Do koncového bodu se můžete rozhodnout odeslat jenom určité typy událostí. Můžete například dostávat oznámení o aktualizacích vašich prostředků, ale ne upozornění na jiné operace, jako je odstranění. V takovém případě filtr podle `Microsoft.Resources.ResourceWriteSuccess` typu události. Zadejte pole s typy událostí nebo určete, že `All` se mají načíst všechny typy událostí pro zdroj události.

Syntaxe JSON pro filtrování podle typu události je:

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>Filtrování subjektu

Pro jednoduché filtrování podle předmětu zadejte počáteční nebo koncovou hodnotu předmětu. Například můžete určit, že má předmět ukončen, `.txt` aby se zobrazily pouze události související s odesláním textového souboru do účtu úložiště. Nebo můžete filtrovat předmět `/blobServices/default/containers/testcontainer` na začátku a získat tak všechny události pro tento kontejner, ale ne jiné kontejnery v účtu úložiště.

Při publikování událostí pro vlastní témata vytvořte předměty pro události, které předplatitelům umožní snadno zjistit, jestli se o událost zajímá. Předplatitelé používají vlastnost Subject k filtrování a směrování událostí. Zvažte přidání cesty pro místo, kde došlo k události, takže předplatitelé mohou filtrovat segmenty této cesty. Cesta umožňuje předplatitelům zúžit nebo široce filtrovat události. Pokud zadáte cestu tři segmenty `/A/B/C` , například v předmětu, můžou předplatitelé filtrovat podle prvního segmentu `/A` a získat tak širokou škálu událostí. Tyto předplatitelé získají události s předměty, jako je `/A/B/C` nebo `/A/D/E` . Jiní předplatitelé mohou filtrovat podle `/A/B` a získat tak užší sadu událostí.

Syntaxe JSON pro filtrování podle předmětu je:

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>Rozšířené filtrování

Chcete-li filtrovat podle hodnot v datových polích a zadat operátor porovnání, použijte možnost pokročilého filtrování. V části Rozšířené filtrování zadáte:

* typ operátoru – typ porovnání.
* klíč – pole v datech události, které používáte pro filtrování. Může to být číslo, logická hodnota, řetězec nebo pole.
* hodnoty – hodnota nebo hodnoty, které se mají porovnat s klíčem.

## <a name="key"></a>Klíč
Klíč je pole v datech události, které používáte pro filtrování. Může to být jeden z následujících typů:

- Číslo
- Logická hodnota
- Řetězec
- Skupin. Chcete- `enableAdvancedFilteringOnArrays` li použít tuto funkci, je nutné nastavit vlastnost na hodnotu true. V současné době Azure Portal nepodporuje povolení této funkce. 

    ```json
    "filter":
    {
        "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
        "subjectEndsWith": ".jpg",
        "enableAdvancedFilteringOnArrays": true
    }
    ```

Pro události ve **schématu Event Grid** použijte následující hodnoty pro klíč: `ID` , `Topic` , `Subject` ,, `EventType` `DataVersion` nebo data události (například `data.key1` ).

Pro události v rámci **schématu cloudových událostí** použijte následující hodnoty pro klíč: `eventid` , `source` , `eventtype` , `eventtypeversion` nebo data události (například `data.key1` ).

Pro **vlastní vstupní schéma** použijte datová pole události (například `data.key1` ). Chcete-li získat přístup k polím v části data, použijte `.` zápis (tečka). Například pro `data.sitename` přístup k `data.appEventTypeDetail.action` `sitename` `action` následující ukázkové události nebo pro ni.

```json
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    },
```

## <a name="values"></a>Hodnoty
Hodnoty mohou být: Number, String, Boolean nebo Array.

## <a name="operators"></a>Operátory

K dispozici jsou operátory pro **čísla** :

## <a name="numberin"></a>NumberIn
Operátor NumberIn se vyhodnotí jako true, pokud je hodnotou **klíče** jedna ze zadaných hodnot **filtru** . V následujícím příkladu kontroluje, zda hodnota `counter` atributu v `data` oddílu je 5 nebo 1. 

```json
"advancedFilters": [{
    "operatorType": "NumberIn",
    "key": "data.counter",
    "values": [
        5,
        1
    ]
}]
```


Pokud je klíč pole, všechny hodnoty v poli jsou zkontrolovány na základě pole hodnot filtru. Zde je pseudo kód s klíčem: `[v1, v2, v3]` a filtrem: `[a, b, c]` . Všechny klíčové hodnoty s datovými typy, které se neshodují s datovým typem filtru, se ignorují.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            MATCH
```

## <a name="numbernotin"></a>NumberNotIn
NumberNotIn se vyhodnotí jako true,  Pokud hodnota klíče **není žádnou z** určených hodnot **filtru** . V následujícím příkladu kontroluje, zda hodnota `counter` atributu v `data` oddílu není 41 a 0. 

```json
"advancedFilters": [{
    "operatorType": "NumberNotIn",
    "key": "data.counter",
    "values": [
        41,
        0
    ]
}]
```

Pokud je klíč pole, všechny hodnoty v poli jsou zkontrolovány na základě pole hodnot filtru. Zde je pseudo kód s klíčem: `[v1, v2, v3]` a filtrem: `[a, b, c]` . Všechny klíčové hodnoty s datovými typy, které se neshodují s datovým typem filtru, se ignorují.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            FAIL_MATCH
```

## <a name="numberlessthan"></a>NumberLessThan
Operátor NumberLessThan se vyhodnotí jako true, pokud je hodnota **klíče** **menší, než** je zadaná hodnota **filtru** . V následujícím příkladu kontroluje, zda hodnota `counter` atributu v `data` oddílu je menší než 100. 

```json
"advancedFilters": [{
    "operatorType": "NumberLessThan",
    "key": "data.counter",
    "value": 100
}]
```

Pokud je klíč pole, všechny hodnoty v poli jsou zkontrolovány proti hodnotě filtru. Zde je pseudo kód s klíčem: `[v1, v2, v3]` . Všechny klíčové hodnoty s datovými typy, které se neshodují s datovým typem filtru, se ignorují.

```
FOR_EACH key IN (v1, v2, v3)
    IF key < filter
        MATCH
```

## <a name="numbergreaterthan"></a>NumberGreaterThan
Operátor NumberGreaterThan se vyhodnotí jako true, pokud je hodnota **klíče** **větší než** zadaná hodnota **filtru** . V následujícím příkladu kontroluje, zda hodnota `counter` atributu v `data` oddílu je větší než 20. 

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThan",
    "key": "data.counter",
    "value": 20
}]
```

Pokud je klíč pole, všechny hodnoty v poli jsou zkontrolovány proti hodnotě filtru. Zde je pseudo kód s klíčem: `[v1, v2, v3]` . Všechny klíčové hodnoty s datovými typy, které se neshodují s datovým typem filtru, se ignorují.

```
FOR_EACH key IN (v1, v2, v3)
    IF key > filter
        MATCH
```

## <a name="numberlessthanorequals"></a>NumberLessThanOrEquals
Operátor NumberLessThanOrEquals se vyhodnotí jako true, pokud je hodnota **klíče** **menší nebo rovna** zadané hodnotě **filtru** . V následujícím příkladu kontroluje, zda `counter` je hodnota atributu v `data` oddílu menší nebo rovna hodnotě 100. 

```json
"advancedFilters": [{
    "operatorType": "NumberLessThanOrEquals",
    "key": "data.counter",
    "value": 100
}]
```

Pokud je klíč pole, všechny hodnoty v poli jsou zkontrolovány proti hodnotě filtru. Zde je pseudo kód s klíčem: `[v1, v2, v3]` . Všechny klíčové hodnoty s datovými typy, které se neshodují s datovým typem filtru, se ignorují.

```
FOR_EACH key IN (v1, v2, v3)
    IF key <= filter
        MATCH
```

## <a name="numbergreaterthanorequals"></a>NumberGreaterThanOrEquals
Operátor NumberGreaterThanOrEquals se vyhodnotí jako true, pokud je hodnota **klíče** **větší nebo rovna** zadané hodnotě **filtru** . V následujícím příkladu kontroluje, zda hodnota `counter` atributu v `data` oddílu je větší než nebo rovna 30. 

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThanOrEquals",
    "key": "data.counter",
    "value": 30
}]
```

Pokud je klíč pole, všechny hodnoty v poli jsou zkontrolovány proti hodnotě filtru. Zde je pseudo kód s klíčem: `[v1, v2, v3]` . Všechny klíčové hodnoty s datovými typy, které se neshodují s datovým typem filtru, se ignorují.

```
FOR_EACH key IN (v1, v2, v3)
    IF key >= filter
        MATCH
```

## <a name="numberinrange"></a>NumberInRange
Operátor NumberInRange se vyhodnotí jako true, pokud je hodnota **klíče** v jednom ze zadaných **rozsahů filtru**. V následujícím příkladu kontroluje, zda hodnota `key1` atributu v `data` oddílu je v jednom ze dvou rozsahů: 3,14159-999,95, 3000-4000. 

```json
{
    "operatorType": "NumberInRange",
    "key": "data.key1",
    "values": [[3.14159, 999.95], [3000, 4000]]
}
```

`values`Vlastnost je pole rozsahů. V předchozím příkladu je to pole dvou rozsahů. Tady je příklad pole s jedním rozsahem, který se má kontrolovat. 

**Pole s jedním rozsahem:** 
```json
{
    "operatorType": "NumberInRange",
    "key": "data.key1",
    "values": [[3000, 4000]]
}
```

Pokud je klíč pole, všechny hodnoty v poli jsou zkontrolovány na základě pole hodnot filtru. Zde je pseudo kód s klíčem: `[v1, v2, v3]` a filtr: pole rozsahů. V tomto pseudo kódu `a` a `b` jsou nízké a vysoké hodnoty každého rozsahu v poli. Všechny klíčové hodnoty s datovými typy, které se neshodují s datovým typem filtru, se ignorují.

```
FOR_EACH (a,b) IN filter.Values
    FOR_EACH key IN (v1, v2, v3)
       IF key >= a AND key <= b
           MATCH
```


## <a name="numbernotinrange"></a>NumberNotInRange
Operátor NumberNotInRange se vyhodnotí jako true,  Pokud hodnota klíče **není v žádném** ze zadaných **rozsahů filtru**. V následujícím příkladu kontroluje, zda hodnota `key1` atributu v `data` oddílu je v jednom ze dvou rozsahů: 3,14159-999,95, 3000-4000. Pokud je, operátor vrátí hodnotu false. 

```json
{
    "operatorType": "NumberNotInRange",
    "key": "data.key1",
    "values": [[3.14159, 999.95], [3000, 4000]]
}
```
`values`Vlastnost je pole rozsahů. V předchozím příkladu je to pole dvou rozsahů. Tady je příklad pole s jedním rozsahem, který se má kontrolovat.

**Pole s jedním rozsahem:** 
```json
{
    "operatorType": "NumberNotInRange",
    "key": "data.key1",
    "values": [[3000, 4000]]
}
```

Pokud je klíč pole, všechny hodnoty v poli jsou zkontrolovány na základě pole hodnot filtru. Zde je pseudo kód s klíčem: `[v1, v2, v3]` a filtr: pole rozsahů. V tomto pseudo kódu `a` a `b` jsou nízké a vysoké hodnoty každého rozsahu v poli. Všechny klíčové hodnoty s datovými typy, které se neshodují s datovým typem filtru, se ignorují.

```
FOR_EACH (a,b) IN filter.Values
    FOR_EACH key IN (v1, v2, v3)
        IF key >= a AND key <= b
            FAIL_MATCH
```


Dostupný operátor pro **logické hodnoty** je: 

## <a name="boolequals"></a>BoolEquals
Operátor BoolEquals se vyhodnotí jako true, pokud je hodnota **klíče** zadaným **filtrem** logických hodnot. V následujícím příkladu kontroluje, zda `isEnabled` je hodnota atributu v `data` oddílu `true` . 

```json
"advancedFilters": [{
    "operatorType": "BoolEquals",
    "key": "data.isEnabled",
    "value": true
}]
```

Pokud je klíč pole, všechny hodnoty v poli jsou zkontrolovány na základě logické hodnoty Filter. Zde je pseudo kód s klíčem: `[v1, v2, v3]` . Všechny klíčové hodnoty s datovými typy, které se neshodují s datovým typem filtru, se ignorují.

```
FOR_EACH key IN (v1, v2, v3)
    IF filter == key
        MATCH
```

Dostupné operátory pro **řetězce** jsou:

## <a name="stringcontains"></a>StringContains
**StringContains** se vyhodnotí jako true, pokud hodnota **klíče** **obsahuje** některou z určených hodnot **filtru** (jako podřetězce). V následujícím příkladu kontroluje, zda hodnota `key1` atributu v `data` oddílu obsahuje jeden ze zadaných podřetězců: `microsoft` nebo `azure` . Například `azure data factory` obsahuje `azure` . 

```json
"advancedFilters": [{
    "operatorType": "StringContains",
    "key": "data.key1",
    "values": [
        "microsoft", 
        "azure"
    ]
}]
```

Pokud je klíč pole, všechny hodnoty v poli jsou zkontrolovány na základě pole hodnot filtru. Zde je pseudo kód s klíčem: `[v1, v2, v3]` a filtrem: `[a,b,c]` . Všechny klíčové hodnoty s datovými typy, které se neshodují s datovým typem filtru, se ignorují.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key CONTAINS filter
            MATCH
```

## <a name="stringnotcontains"></a>StringNotContains
Operátor **StringNotContains** se vyhodnotí jako true,  Pokud klíč **neobsahuje zadané** hodnoty **filtru** jako podřetězce. Pokud klíč obsahuje jednu z určených hodnot jako podřetězec, operátor vyhodnotí jako NEPRAVDA. V následujícím příkladu vrátí operátor hodnotu true pouze v případě, že hodnota `key1` atributu v `data` oddílu nemá `contoso` a `fabrikam` jako podřetězce. 

```json
"advancedFilters": [{
    "operatorType": "StringNotContains",
    "key": "data.key1",
    "values": [
        "contoso", 
        "fabrikam"
    ]
}]
```

Pokud je klíč pole, všechny hodnoty v poli jsou zkontrolovány na základě pole hodnot filtru. Zde je pseudo kód s klíčem: `[v1, v2, v3]` a filtrem: `[a,b,c]` . Všechny klíčové hodnoty s datovými typy, které se neshodují s datovým typem filtru, se ignorují.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key CONTAINS filter
            FAIL_MATCH
```

## <a name="stringbeginswith"></a>StringBeginsWith
Operátor **StringBeginsWith** se vyhodnotí jako true, pokud hodnota **klíče** **začíná** kteroukoli z určených hodnot **filtru** . V následujícím příkladu kontroluje, zda hodnota `key1` atributu v `data` oddílu začíná `event` nebo `grid` . Například začíná na `event hubs` `event` .  

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "message"
    ]
}]
```

Pokud je klíč pole, všechny hodnoty v poli jsou zkontrolovány na základě pole hodnot filtru. Zde je pseudo kód s klíčem: `[v1, v2, v3]` a filtrem: `[a,b,c]` . Všechny klíčové hodnoty s datovými typy, které se neshodují s datovým typem filtru, se ignorují.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key BEGINS_WITH filter
            MATCH
```

## <a name="stringnotbeginswith"></a>StringNotBeginsWith
Operátor **StringNotBeginsWith** se vyhodnotí jako true,  Pokud hodnota klíče **nezačíná** žádnou z určených hodnot **filtru** . V následujícím příkladu kontroluje, zda hodnota `key1` atributu v `data` oddílu nezačíná na `event` nebo `message` .

```json
"advancedFilters": [{
    "operatorType": "StringNotBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "message"
    ]
}]
```

Pokud je klíč pole, všechny hodnoty v poli jsou zkontrolovány na základě pole hodnot filtru. Zde je pseudo kód s klíčem: `[v1, v2, v3]` a filtrem: `[a,b,c]` . Všechny klíčové hodnoty s datovými typy, které se neshodují s datovým typem filtru, se ignorují.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key BEGINS_WITH filter
            FAIL_MATCH
```

## <a name="stringendswith"></a>StringEndsWith
Operátor **StringEndsWith** se vyhodnotí jako true, pokud hodnota **klíče** **končí** na jednu ze zadaných hodnot **filtru** . V následujícím příkladu kontroluje, zda hodnota `key1` atributu v `data` oddílu končí `jpg` nebo `jpeg` nebo `png` . Například končí na `eventgrid.png` `png` .


```json
"advancedFilters": [{
    "operatorType": "StringEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

Pokud je klíč pole, všechny hodnoty v poli jsou zkontrolovány na základě pole hodnot filtru. Zde je pseudo kód s klíčem: `[v1, v2, v3]` a filtrem: `[a,b,c]` . Všechny klíčové hodnoty s datovými typy, které se neshodují s datovým typem filtru, se ignorují.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key ENDS_WITH filter
            MATCH
```

## <a name="stringnotendswith"></a>StringNotEndsWith
Operátor **StringNotEndsWith** se vyhodnotí jako true,  Pokud hodnota klíče **nekončí** žádnou z určených hodnot **filtru** . V následujícím příkladu kontroluje, zda hodnota `key1` atributu v `data` oddílu nekončí `jpg` nebo `jpeg` `png` . 


```json
"advancedFilters": [{
    "operatorType": "StringNotEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

Pokud je klíč pole, všechny hodnoty v poli jsou zkontrolovány na základě pole hodnot filtru. Zde je pseudo kód s klíčem: `[v1, v2, v3]` a filtrem: `[a,b,c]` . Všechny klíčové hodnoty s datovými typy, které se neshodují s datovým typem filtru, se ignorují.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key ENDS_WITH filter
            FAIL_MATCH
```

## <a name="stringin"></a>StringIn
Operátor **StringIn** zkontroluje, zda hodnota **klíče** **přesně odpovídá** jedné z určených hodnot **filtru** . V následujícím příkladu kontroluje, zda hodnota `key1` atributu v `data` oddílu je `exact` nebo `string` `matches` . 

```json
"advancedFilters": [{
    "operatorType": "StringIn",
    "key": "data.key1",
    "values": [
        "contoso", 
        "fabrikam", 
        "factory"
    ]
}]
```

Pokud je klíč pole, všechny hodnoty v poli jsou zkontrolovány na základě pole hodnot filtru. Zde je pseudo kód s klíčem: `[v1, v2, v3]` a filtrem: `[a,b,c]` . Všechny klíčové hodnoty s datovými typy, které se neshodují s datovým typem filtru, se ignorují.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            MATCH
```

## <a name="stringnotin"></a>StringNotIn
Operátor **StringNotIn** zkontroluje, zda hodnota **klíče** neodpovídá **žádné z** určených hodnot **filtru** . V následujícím příkladu kontroluje, zda hodnota `key1` atributu v `data` oddílu není `aws` a `bridge` . 

```json
"advancedFilters": [{
    "operatorType": "StringNotIn",
    "key": "data.key1",
    "values": [
        "aws", 
        "bridge"
    ]
}]
```

Pokud je klíč pole, všechny hodnoty v poli jsou zkontrolovány na základě pole hodnot filtru. Zde je pseudo kód s klíčem: `[v1, v2, v3]` a filtrem: `[a,b,c]` . Všechny klíčové hodnoty s datovými typy, které se neshodují s datovým typem filtru, se ignorují.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            FAIL_MATCH
```


Všechna porovnávání řetězců nerozlišují velká a malá písmena.

> [!NOTE]
> Pokud JSON události neobsahuje klíč rozšířeného filtru, filtr se evaulated jako **neodpovídající** pro následující operátory: NumberGreaterThan, NumberGreaterThanOrEquals, NumberLessThan, NumberLessThanOrEquals, NumberIn, BoolEquals, StringContains, StringNotContains, StringBeginsWith, StringNotBeginsWith, StringEndsWith, StringNotEndsWith, StringIn.
> 
>Filtr je evaulated tak, jak **odpovídá** následujícím operátorům: NumberNotIn, StringNotIn.


## <a name="isnullorundefined"></a>IsNullOrUndefined
Operátor IsNullOrUndefined se vyhodnotí jako true, pokud je hodnota klíče NULL nebo undefined. 

```json
{
    "operatorType": "IsNullOrUndefined",
    "key": "data.key1"
}
```

V následujícím příkladu chybí klíč1, takže by byl operátor vyhodnocen jako true. 

```json
{ 
    "data": 
    { 
        "key2": 5 
    } 
}
```

V následujícím příkladu je klíč1 nastaven na hodnotu null, takže se operátor vyhodnotí jako true.

```json
{
    "data": 
    { 
        "key1": null
    }
}
```

Pokud klíč1 má v těchto příkladech jinou hodnotu, operátor by byl vyhodnocen jako NEPRAVDA. 

## <a name="isnotnull"></a>IsNotNull
Operátor IsNotNull se vyhodnotí jako true, pokud hodnota klíče není NULL nebo není definována. 

```json
{
    "operatorType": "IsNotNull",
    "key": "data.key1"
}
```

## <a name="or-and-and"></a>NEBO a a
Pokud zadáte jeden filtr s více hodnotami, provede se operace **nebo** , takže hodnota pole klíč musí být jedna z těchto hodnot. Tady je příklad:

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/",
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

Pokud zadáte více různých filtrů, operace **a** se provede, takže je nutné splnit všechny podmínky filtru. Tady je příklad: 

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/"
        ]
    },
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

## <a name="cloudevents"></a>CloudEvents 
Pro události ve **schématu CloudEvents** použijte následující hodnoty pro klíč: `eventid` , `source` , `eventtype` , `eventtypeversion` nebo data události (například `data.key1` ). 

Můžete také použít [atributy kontextu rozšíření v CloudEvents 1,0](https://github.com/cloudevents/spec/blob/v1.0.1/spec.md#extension-context-attributes). V následujícím příkladu `comexampleextension1` a `comexampleothervalue` jsou atributy kontextu rozšíření. 

```json
{
    "specversion" : "1.0",
    "type" : "com.example.someevent",
    "source" : "/mycontext",
    "id" : "C234-1234-1234",
    "time" : "2018-04-05T17:31:00Z",
    "subject": null,
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
        "appinfoA" : "abc",
        "appinfoB" : 123,
        "appinfoC" : true
    }
}
```

Tady je příklad použití atributu kontextu rozšíření ve filtru.

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "comexampleothervalue",
    "values": [
        "5", 
        "1"
    ]
}]
```


## <a name="limitations"></a>Omezení

Rozšířené filtrování má následující omezení:

* 5 rozšířených filtrů a 25 hodnot filtru napříč všemi filtry na odběr Event gridu
* 512 znaků na hodnotu řetězce
* Pět hodnot pro operátor **in** a **Not in**
* Klávesy se znakem **`.` (tečka)** . Například: `http://schemas.microsoft.com/claims/authnclassreference` nebo `john.doe@contoso.com` . V současné době není v klíčích podporován řídicí znaky. 

Stejný klíč lze použít ve více než jednom filtru.





## <a name="next-steps"></a>Další kroky

* Další informace o filtrování událostí pomocí PowerShellu a rozhraní příkazového řádku Azure najdete v tématu [filtrování událostí pro Event Grid](how-to-filter-events.md).
* Pokud chcete rychle začít používat Event Grid, přečtěte si téma [Vytvoření a směrování vlastních událostí pomocí Azure Event Grid](custom-event-quickstart.md).
