---
title: Tvůrce výrazů v mapování toku dat
description: Vytváření výrazů pomocí Tvůrce výrazů při mapování toků dat v Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/9/2019
ms.openlocfilehash: 01aa2574ac6edd1ce5e1b209eac3e43bbed82fce
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969337"
---
# <a name="building-expressions-in-mapping-data-flow"></a>Vytváření výrazů v mapování toku dat

V rámci mapování toku dat jsou jako výrazy zadány mnoho vlastností transformace. Tyto výrazy se skládají z hodnot sloupců, parametrů, funkcí, operátorů a literálů, které se vyhodnotí na datový typ Spark v době běhu.

## <a name="opening-the-expression-builder"></a>Otevření Tvůrce výrazů

Rozhraní pro úpravu výrazů v uživatelském prostředí objektu pro vytváření dat je známé jako **Tvůrce výrazů**. Když zadáte do logiky výrazů, Data Factory pomocí [technologie IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019) dokončí zvýrazňování, kontrolu syntaxe a automatického dokončování.

![Tvůrce výrazů](media/data-flow/xpb1.png "Tvůrce výrazů")

V transformacích, jako je odvozený sloupec a filtr, kde jsou výrazy povinné, otevřete Tvůrce výrazů kliknutím na modrý rámeček výrazu.

![Tvůrce výrazů](media/data-flow/expressionbox.png "Tvůrce výrazů")

Při odkazování na sloupce v rámci párové nebo skupinové podmínky může výraz extrahovat hodnoty ze sloupců. Pokud chcete vytvořit výraz, vyberte možnost vypočítaný sloupec.

![Tvůrce výrazů](media/data-flow/computedcolumn.png "Tvůrce výrazů")

V případech, kdy je výraz nebo hodnota literálu platnými vstupy, možnost Přidat dynamický obsah vám umožní vytvořit výraz, který se vyhodnotí na literál.

![Tvůrce výrazů](media/data-flow/add-dynamic-content.png "Tvůrce výrazů")

## <a name="expression-language-reference"></a>Reference k jazyku výrazu

Mapování toků dat obsahuje integrované funkce a operátory, které lze použít ve výrazech. Seznam dostupných funkcí najdete na stránce s odkazem na [Jazyk výrazu mapování toku dat](data-flow-expression-functions.md) .

## <a name="column-names-with-special-characters"></a>Názvy sloupců se speciálními znaky

Pokud máte názvy sloupců, které obsahují speciální znaky nebo mezery, uzavřete název do složených závorek, aby na ně odkazovaly ve výrazu.

```{[dbo].this_is my complex name$$$}```

## <a name="previewing-expression-results"></a>Zobrazení náhledu výsledků výrazu

Pokud je [režim ladění](concepts-data-flow-debug-mode.md) zapnutý, můžete pomocí živého clusteru Spark zobrazit průběžnou verzi Preview, na kterou se výraz vyhodnocuje. Při vytváření logiky můžete výraz ladit v reálném čase. 

![Tvůrce výrazů](media/data-flow/exp4b.png "Náhled dat výrazu")

Kliknutím na tlačítko Aktualizovat aktualizujte výsledky výrazu na živý vzorek vašeho zdroje.

![Tvůrce výrazů](media/data-flow/exp5.png "Náhled dat výrazu")

## <a name="string-interpolation"></a>Interpolace řetězců

Pomocí dvojitých uvozovek uzavřete textový řetězec literálu spolu s výrazy. Můžete zahrnout funkce výrazů, sloupce a parametry. Interpolace řetězců je užitečné, aby nedocházelo k rozsáhlému použití zřetězení řetězců při zahrnutí parametrů do řetězců dotazů. Chcete-li použít syntaxi výrazu, vložte ji do složených závorek,

Některé příklady interpolace řetězců:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="commenting-expressions"></a>Výrazy s komentářem

Přidejte komentáře ke svým výrazům pomocí jedné řádky a syntaxe víceřádkového komentáře:

![Komentáře](media/data-flow/comments.png "Komentáře")

Níže jsou uvedeny příklady platných komentářů:

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

Pokud komentář zadáte v horní části výrazu, zobrazí se v textovém poli transformace k dokumentaci vašich transformačních výrazů:

![Komentáře](media/data-flow/comments2.png "Komentáře")

## <a name="regular-expressions"></a>Regulární výrazy

Mnoho funkcí jazyka Expression používá syntaxi regulárního výrazu. Při použití funkcí regulárních výrazů se Tvůrce výrazů pokusí interpretovat zpětné lomítko (\\) jako sekvenci řídicích znaků. Při použití zpětných lomítek ve regulárním výrazu buď vložte celý regulární výraz do impulzů (\`) nebo použijte dvojité zpětné lomítko.

Příklad použití značek

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

nebo používá dvojité lomítko

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="addressing-array-indexes"></a>Indexy polí pro adresování

Pomocí funkcí výrazu, které vracejí pole, použijte hranaté závorky [] k adresování konkrétních indexů uvnitř tohoto objektu návratového pole. Pole je založené na.

![Pole Tvůrce výrazů](media/data-flow/expb2.png "Náhled dat výrazu")

## <a name="keyboard-shortcuts"></a>Klávesové zkratky

* ```Ctrl-K Ctrl-C```: komentáře k celému řádku
* ```Ctrl-K Ctrl-U```: odkomentovat
* ```F1```: Poskytněte příkazy pro Help Editor.
* ```Alt-Down Arrow```: přesunout aktuální řádek dolů
* ```Alt-Up Arrow```: přesunout aktuální řádek nahoru
* ```Cntrl-Space```: zobrazit kontextovou podporu

## <a name="convert-to-dates-or-timestamps"></a>Převést na data nebo časová razítka

Chcete-li do výstupu časového razítka zahrnout řetězcové literály, je třeba zabalit převod do ```toString()```.

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Chcete-li převést milisekundy z epocha na datum nebo časové razítko, použijte `toTimestamp(<number of milliseconds>)`. Pokud se čas koná v sekundách, vynásobte 1000.

```toTimestamp(1574127407*1000l)```

Koncový znak "l" na konci výše uvedeného výrazu znamená převod na dlouhý typ jako vloženou syntaxi.

## <a name="next-steps"></a>Další kroky

[Začít sestavovat výrazy transformace dat](data-flow-expression-functions.md)
