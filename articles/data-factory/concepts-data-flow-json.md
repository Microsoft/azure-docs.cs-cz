---
title: Azure Data Factory konceptů JSON pro mapování toku dat
description: Tok dat mapování Data Factory obsahuje integrované funkce pro zpracování dokumentů JSON s hierarchiemi.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: cde42dda47d54c03c50895bc625f99c9350b53e3
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210686"
---
# <a name="mapping-data-flow-json-handling"></a>Mapování zpracování JSON toku dat

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="creating-json-structures-in-expression-editor"></a>Vytváření struktur JSON v editoru výrazů
### <a name="derived-column-transformation"></a>Odvozená transformace sloupce
Přidání složitého sloupce do toku dat je snazší prostřednictvím odvozeného editoru výrazů pro sloupce. Po přidání nového sloupce a otevření editoru jsou k dispozici dvě možnosti: Ruční zadání struktury JSON nebo použití uživatelského rozhraní k interaktivnímu přidání podsloupců.

#### <a name="interactive-ui-json-design"></a>Interaktivní návrh JSON uživatelského rozhraní
V podokně výstup v rámci schématu lze nové podsloupce přidat pomocí `+` nabídky: ![Přidat Podsloupec](media/data-flow/addsubcolumn.png "Přidat Podsloupec")

Odtud lze stejným způsobem přidat nové sloupce a podsloupce. Pro každé nekomplikované pole lze přidat výraz do pravé části editoru výrazů.

![Složitý sloupec](media/data-flow/complexcolumn.png "Složitý sloupec")

#### <a name="manual-json-design"></a>Ruční návrh JSON
Chcete-li ručně přidat strukturu JSON, přidejte nový sloupec a v editoru zadejte výraz. Výraz následuje po tomto obecném formátu:
```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```
Pokud se tento výraz zadal pro sloupec s názvem "complexColumn", pak se do jímky zapíše jako následující JSON:
```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-dsl"></a>Ukázka ruční DSL
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

## <a name="source-format-options"></a>Možnosti formátu zdroje
### <a name="default"></a>Výchozí
```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

### <a name="single-document"></a>Jeden dokument
* Možnost 1
```
[
    {
        "json": "record 1"
    },
    {
        "json": "record 2"
    },
    {
        "json": "record 3"
    }
]
```

* Možnost dvě
```
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```

### <a name="unquoted-column-names"></a>Názvy sloupců bez uvozovek
```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

### <a name="has-comments"></a>Obsahuje komentáře
```
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

### <a name="single-quoted"></a>Jednoduché v uvozovkách
```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

### <a name="backslash-escaped"></a>Zpětné lomítko – řídicí
```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

# <a name="higher-order-functions"></a>Vyšší pořadí funkcí
## <a name="filter"></a>filter
Filtruje prvky z pole, které nesplňuje zadaný predikát. Filtr očekává odkaz na jeden prvek ve funkci predikátu jako #item.

### <a name="examples"></a>Příklady
```
filter([1, 2, 3, 4], #item > 2) => [3, 4]
filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') => ['a', 'b']
```

## <a name="map"></a>map
Mapuje každý prvek pole na nový prvek pomocí poskytnutého výrazu. Mapa očekává odkaz na jeden prvek ve funkci Expression jako #item.

### <a name="examples"></a>Příklady
```
map([1, 2, 3, 4], #item + 2) => [3, 4, 5, 6]
map(['a', 'b', 'c', 'd'], #item + '_processed') => ['a_processed', 'b_processed', 'c_processed', 'd_processed']
```

## <a name="reduce"></a>úrovně
Shromáždí prvky v poli. Omezení očekává odkaz na akumulátor a jeden element v prvním výrazu funkce jako #acc a #item a očekává výslednou hodnotu jako #result pro použití ve druhé funkci výrazu.

### <a name="examples"></a>Příklady
```
reduce([1, 2, 3, 4], 0, #acc + #item, #result) => 10
reduce(['1', '2', '3', '4'], '0', #acc + #item, #result) => '01234'
reduce([1, 2, 3, 4], 0, #acc + #item, #result + 15) => 25
```

## <a name="sort"></a>druhu
Seřadí pole pomocí zadané funkce predikátu. Řazení očekává odkaz na dva po sobě jdoucí prvky ve funkci Expression jako #item1 a #item2.

### <a name="examples"></a>Příklady
```
sort([4, 8, 2, 3], compare(#item1, #item2)) => [2, 3, 4, 8]
sort(['a3', 'b2', 'c1'],
        iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) => ['c1', 'b2', 'a3']
sort(['a3', 'b2', 'c1'],
        iif(#item1 >= #item2, 1, -1)) => ['a3', 'b2', 'c1']
```

## <a name="contains"></a>Obsahuje
Vrátí hodnotu true, pokud je libovolný prvek v zadaném poli vyhodnocen jako true v zadaném predikátu. Obsahuje očekává odkaz na jeden prvek ve funkci predikátu jako #item.

### <a name="examples"></a>Příklady
```
contains([1, 2, 3, 4], #item == 3) => true
contains([1, 2, 3, 4], #item > 5) => false
```

## <a name="next-steps"></a>Další postup

* [Použití transformace odvozeného sloupce k sestavení hierarchických struktur](data-flow-derived-column.md)
