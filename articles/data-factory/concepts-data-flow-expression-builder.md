---
title: Tvůrce výrazů v toku dat mapování
description: Vytváření výrazů pomocí Tvůrce výrazů v mapování datových toků v Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/9/2019
ms.openlocfilehash: 1dd782092ce91f7b71a3a2a6f2ed1646ee39a7e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444538"
---
# <a name="build-expressions-in-mapping-data-flow"></a>Vytváření výrazů v toku dat mapování

V mapování toku dat je mnoho vlastností transformace zadáno jako výrazy. Tyto výrazy se skládají z hodnot sloupců, parametrů, funkcí, operátorů a literál, které jsou vyhodnocovány na datový typ Spark za běhu.

## <a name="open-expression-builder"></a>Otevřít Tvůrce výrazů

Rozhraní pro úpravy výrazů v uživatelském prostředí Azure Data Factory se označuje jako Tvůrce výrazů. Při zadávání logiky výrazů používá Data Factory dokončování kódu [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019) pro zvýraznění, kontrolu syntaxe a automatické dokončování.

![Tvůrce výrazů](media/data-flow/xpb1.png "Tvůrce výrazů")

V transformacích, jako je odvozený sloupec a filtr, kde jsou výrazy povinné, otevřete Tvůrce výrazů výběrem modrého pole výrazu.

![Modré pole výrazu](media/data-flow/expressionbox.png "Tvůrce výrazů")

Když odkazujete na sloupce v odpovídající nebo skupinové podmínce, výraz může extrahovat hodnoty ze sloupců. Chcete-li vytvořit výraz, vyberte **Vypočítaný sloupec**.

![Možnost vypočítaného sloupce](media/data-flow/computedcolumn.png "Tvůrce výrazů")

V případech, kdy výraz nebo hodnota literálu jsou platné vstupy, vyberte **Přidat dynamický obsah** k vytvoření výrazu, který vyhodnocuje na hodnotu literálu.

![Přidat možnost dynamického obsahu](media/data-flow/add-dynamic-content.png "Tvůrce výrazů")

## <a name="expression-language-reference"></a>Odkaz na jazyk výrazu

Mapování datových toků má vestavěné funkce a operátory, které lze použít ve výrazech. Seznam dostupných funkcí naleznete [v tématu Expression functions in the mapping data flow](data-flow-expression-functions.md).

## <a name="column-names-with-special-characters"></a>Názvy sloupců se speciálními znaky

Pokud máte názvy sloupců, které obsahují speciální znaky nebo mezery, obklopte název složenými závorkami, abyste na ně odkazovali ve výrazu.

```{[dbo].this_is my complex name$$$}```

## <a name="preview-expression-results"></a>Náhled výsledků výrazu

Pokud je zapnutý [režim ladění,](concepts-data-flow-debug-mode.md) můžete pomocí živého clusteru Spark zobrazit probíhající náhled toho, co váš výraz vyhodnocuje. Při vytváření logiky můžete ladit výraz v reálném čase. 

![Probíhající náhled](media/data-flow/exp4b.png "Náhled dat výrazu")

Vyberte **Aktualizovat,** chcete-li aktualizovat výsledky výrazu na živý vzorek zdroje.

![Tlačítko Aktualizovat](media/data-flow/exp5.png "Náhled dat výrazu")

## <a name="string-interpolation"></a>Interpolace řetězců

Pomocí uvozovek uzavřete text literálu spolu s výrazy. Můžete zahrnout funkce výrazu, sloupce a parametry. Interpolace řetězců je užitečná, aby se zabránilo rozsáhlému použití zřetězení řetězců, když jsou parametry zahrnuty v řetězcích dotazu. Chcete-li použít syntaxi výrazu, uzavřete ji do složených závorek,

Některé příklady interpolace řetězce:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="comment-expressions"></a>Výrazy komentářů

Přidejte komentáře k výrazům pomocí jednořádkové a víceřádkové syntaxe komentářů.

![Jednořádková a víceřádková syntaxe komentářů](media/data-flow/comments.png "Komentáře")

Následující příklady jsou platné komentáře:

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

Pokud vložíte komentář do horní části výrazu, zobrazí se v textovém poli transformace pro dokumentování transformačních výrazů.

![Komentář v textovém poli transformace](media/data-flow/comments2.png "Komentáře")

## <a name="regular-expressions"></a>Regulární výrazy

Mnoho funkcí jazyka výrazu používá syntaxi regulárních výrazů. Při použití funkcí regulárního výrazu se\\Tvůrce výrazů pokusí interpretovat zpětné lomítko ( ) jako posloupnost znaků escape. Při použití zpětná lomítka v regulárním výrazu,\`buď uzavřete celý regulární výraz v backticks ( ) nebo použijte dvojité zpětné lomítko.

Příklad, který používá zpětné zaškrtávací chod:

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

Příklad, který používá dvojitá lomítka:

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="address-array-indexes"></a>Indexy pole adres

S funkcemi výrazu, které vracejí pole, použijte závorky ([]) k adresování určitých indexů uvnitř, které vracejí objekty pole. Pole je založeno na jedničkách.

![Pole Tvůrce výrazů](media/data-flow/expb2.png "Náhled dat výrazu")

## <a name="keyboard-shortcuts"></a>Klávesové zkratky

* Ctrl+K Ctrl+C: Komentář celý řádek.
* Ctrl+K Ctrl+U: Odkomentujte.
* F1: Poskytněte příkazy nápovědy editoru.
* Alt + šipka dolů: Přesunout aktuální řádek dolů.
* Alt + šipka nahoru: Přesunout aktuální řádek nahoru.
* Ctrl+Mezerník: Zobrazit kontextovou nápovědu.

## <a name="convert-to-dates-or-timestamps"></a>Převod na kalendářní data nebo časová razítka

Chcete-li do výstupu časového razítka zahrnout řetězcové literály, zalomte převod do aplikace ```toString()```.

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Chcete-li převést milisekundy z epochy na datum nebo časové razítko, použijte `toTimestamp(<number of milliseconds>)`. Pokud čas přichází v sekundách, vynásobte 1000.

```toTimestamp(1574127407*1000l)```

Koncové "l" na konci předchozího výrazu znamená převod na dlouhý typ jako vložkovou syntaxi.

## <a name="next-steps"></a>Další kroky

[Zahájení vytváření výrazů transformace dat](data-flow-expression-functions.md)
