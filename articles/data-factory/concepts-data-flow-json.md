---
title: Použití formátu JSON při mapování toku dat v Azure Data Factory
description: Tok dat mapování Azure Data Factory obsahuje integrované funkce pro zpracování dokumentů JSON s hierarchiemi.
author: kromerm
ms.author: makromer
ms.review: djpmsft
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 605564ed541c23a9060879706fb25f91e97a8eac
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326525"
---
# <a name="mapping-data-flow-json-handling"></a>Mapování zpracování JSON toku dat

## <a name="creating-json-structures-in-derived-column"></a>Vytváření struktur JSON v odvozeném sloupci

Do toku dat můžete přidat složitý sloupec prostřednictvím Tvůrce výrazu odvozeného sloupce. V transformaci odvozeného sloupce přidejte nový sloupec a otevřete Tvůrce výrazů kliknutím na modrý rámeček. Chcete-li nastavit sloupec jako složitý, můžete ručně zadat strukturu JSON nebo použít uživatelské prostředí k interaktivnímu přidání podsloupců.

### <a name="using-the-expression-builder-ux"></a>Použití uživatelského rozhraní Tvůrce výrazů

V podokně výstupní schéma umístěte ukazatel myši na sloupec a klikněte na ikonu se symbolem plus. Vyberte možnost **Přidat dílčí sloupec** a nastavte si sloupec jako složitý typ.

![Přidat]Podsloupec(media/data-flow/addsubcolumn.png "Přidat") Podsloupec

Můžete přidat další sloupce a podsloupce stejným způsobem. Pro každé nekomplikované pole lze přidat výraz do pravé části editoru výrazů.

Složitý sloupec(media/data-flow/complexcolumn.png "se složitým") ![sloupcem]

### <a name="entering-the-json-structure-manually"></a>Ruční zadání struktury JSON

Chcete-li ručně přidat strukturu JSON, přidejte nový sloupec a v editoru zadejte výraz. Výraz následuje po tomto obecném formátu:

```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```

Pokud byl tento výraz zadán pro sloupec s názvem "complexColumn", pak bude zapsán do jímky jako následující JSON:

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

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>Ukázka ručního skriptu pro kompletní hierarchickou definici
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

Použití datové sady JSON jako zdroje v toku dat umožňuje nastavit pět dalších nastavení. Tato nastavení se dají najít v rámci souhlasu **Nastavení JSON** na kartě **zdrojové možnosti** .  

![](media/data-flow/json-settings.png "Nastavení") JSON JSON

### <a name="default"></a>Výchozí

Ve výchozím nastavení jsou data JSON čtena v následujícím formátu.

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

### <a name="single-document"></a>Jeden dokument

Je-li vybrán **jeden dokument** , mapování toků dat načtou z každého souboru jeden dokument JSON. 

``` json
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

Pokud je vybraná možnost **názvy sloupců bez uvozovek** , mapování toků dat přečte sloupce JSON, které nejsou obklopené uvozovkami. 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

### <a name="has-comments"></a>Obsahuje komentáře

Vyberte **má komentáře** , pokud data JSON obsahují komentáře v C++ jazyce C nebo Style.

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

### <a name="single-quoted"></a>Jednoduché v uvozovkách

Pokud pole a hodnoty JSON místo dvojitých uvozovek používají jednoduché uvozovky, vyberte **jedno uvozovky** .

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

### <a name="backslash-escaped"></a>Zpětné lomítko – řídicí

Pokud se k řídicím znakům v datech JSON mají použít zpětná lomítka, vyberte **jedno uvozovky** .

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="higher-order-functions"></a>Funkce vyššího řádu

Funkce vyššího řádu je funkce, která přijímá jednu nebo více funkcí jako argument. Níže najdete seznam funkcí vyšších pořadí podporovaných při mapování toků dat, které umožňují operace s poli.

### <a name="filter"></a>Filtrovací
Filtruje prvky z pole, které nesplňuje zadaný predikát. Filtr očekává odkaz na jeden prvek ve funkci predikátu jako #item.

#### <a name="examples"></a>Příklady
```
filter([1, 2, 3, 4], #item > 2) => [3, 4]
filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') => ['a', 'b']
```

### <a name="map"></a>map
Mapuje každý prvek pole na nový prvek pomocí poskytnutého výrazu. Mapa očekává odkaz na jeden prvek ve funkci Expression jako #item.

#### <a name="examples"></a>Příklady
```
map([1, 2, 3, 4], #item + 2) => [3, 4, 5, 6]
map(['a', 'b', 'c', 'd'], #item + '_processed') => ['a_processed', 'b_processed', 'c_processed', 'd_processed']
```

### <a name="reduce"></a>úrovně
Shromáždí prvky v poli. Omezení očekává odkaz na akumulátor a jeden element v prvním výrazu funkce jako #acc a #item a očekává výslednou hodnotu jako #result pro použití ve druhé funkci výrazu.

#### <a name="examples"></a>Příklady
```
reduce([1, 2, 3, 4], 0, #acc + #item, #result) => 10
reduce(['1', '2', '3', '4'], '0', #acc + #item, #result) => '01234'
reduce([1, 2, 3, 4], 0, #acc + #item, #result + 15) => 25
```

### <a name="sort"></a>Druhu
Seřadí pole pomocí zadané funkce predikátu. Řazení očekává odkaz na dva po sobě jdoucí prvky ve funkci Expression jako #item1 a #item2.

#### <a name="examples"></a>Příklady
```
sort([4, 8, 2, 3], compare(#item1, #item2)) => [2, 3, 4, 8]
sort(['a3', 'b2', 'c1'],
        iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) => ['c1', 'b2', 'a3']
sort(['a3', 'b2', 'c1'],
        iif(#item1 >= #item2, 1, -1)) => ['a3', 'b2', 'c1']
```

### <a name="contains"></a>Zobrazí
Vrátí hodnotu true, pokud je libovolný prvek v zadaném poli vyhodnocen jako true v zadaném predikátu. Obsahuje očekává odkaz na jeden prvek ve funkci predikátu jako #item.

#### <a name="examples"></a>Příklady
```
contains([1, 2, 3, 4], #item == 3) => true
contains([1, 2, 3, 4], #item > 5) => false
```

## <a name="next-steps"></a>Další kroky

* [Použití transformace odvozeného sloupce k sestavení hierarchických struktur](data-flow-derived-column.md)
