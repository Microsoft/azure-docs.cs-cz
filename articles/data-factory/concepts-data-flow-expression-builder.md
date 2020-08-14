---
title: Tvůrce výrazů v mapování toku dat
description: Vytváření výrazů pomocí Tvůrce výrazů v mapování toků dat v Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: f522812f762b55ec61794101e6cd1ec15fb171ca
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212112"
---
# <a name="build-expressions-in-mapping-data-flow"></a>Výrazy sestavení v mapování toku dat

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

V rámci mapování toku dat jsou jako výrazy zadány mnoho vlastností transformace. Tyto výrazy se skládají z hodnot sloupců, parametrů, funkcí, operátorů a literálů, které se vyhodnotí na datový typ Spark v době běhu.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tkur]

## <a name="open-expression-builder"></a>Otevřít Tvůrce výrazů

Rozhraní pro úpravu výrazů v uživatelském prostředí Azure Data Factory se říká tvůrci výrazů. Při zadávání logiky výrazů používá data Factory pro zvýraznění, kontrolu syntaxe a redokončování kód [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019) .

![Tvůrce výrazů](media/data-flow/xpb1.png "Tvůrce výrazů")

V transformacích, jako je odvozený sloupec a filtr, kde jsou výrazy povinné, otevřete Tvůrce výrazů výběrem modrého pole výrazu.

![Modré pole výrazu](media/data-flow/expressionbox.png "Tvůrce výrazů")

Když odkazujete na sloupce v rámci párové nebo skupinové podmínky, může výraz extrahovat hodnoty ze sloupců. Pokud chcete vytvořit výraz, vyberte **vypočítaný sloupec**.

![Možnost vypočítaného sloupce](media/data-flow/computedcolumn.png "Tvůrce výrazů")

V případech, kdy je výraz nebo hodnota literálu platnými vstupy, vyberte **Přidat dynamický obsah** k sestavení výrazu, který se vyhodnotí jako literální hodnota.

![Přidat možnost dynamického obsahu](media/data-flow/add-dynamic-content.png "Tvůrce výrazů")

## <a name="expression-language-reference"></a>Reference k jazyku výrazu

Mapování datových toků obsahuje integrované funkce a operátory, které lze použít ve výrazech. Seznam dostupných funkcí najdete v tématu [funkce výrazu v toku dat mapování](data-flow-expression-functions.md).

## <a name="column-names-with-special-characters"></a>Názvy sloupců se speciálními znaky

Pokud máte názvy sloupců, které obsahují speciální znaky nebo mezery, uzavřete název do složených závorek, aby na ně odkazovaly ve výrazu.

```{[dbo].this_is my complex name$$$}```

## <a name="preview-expression-results"></a>Náhled výsledků výrazu

Pokud je [režim ladění](concepts-data-flow-debug-mode.md) zapnutý, můžete použít cluster Live Spark k zobrazení průběžného náhledu, na který se výraz vyhodnocuje. Při sestavování logiky můžete výraz ladit v reálném čase. 

![Průběžná verze Preview](media/data-flow/exp4b.png "Náhled dat výrazu")

Vyberte **aktualizovat** a aktualizujte výsledky výrazu proti živé ukázce vašeho zdroje.

![Tlačítko Refresh](media/data-flow/exp5.png "Náhled dat výrazu")

## <a name="string-interpolation"></a>Interpolace řetězců

Použijte uvozovky k uzavření textu řetězcového literálu spolu s výrazy. Můžete zahrnout funkce výrazů, sloupce a parametry. Interpolace řetězců je užitečné, aby nedocházelo k rozsáhlému použití zřetězení řetězců, když jsou parametry zahrnuty v řetězcích dotazů. Chcete-li použít syntaxi výrazu, vložte ji do složených závorek,

Některé příklady interpolace řetězců:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="comment-expressions"></a>Výrazy komentářů

Přidejte komentáře ke svým výrazům pomocí jednoduché a víceřádkové syntaxe komentáře.

V následujících příkladech jsou platné komentáře:

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```

Pokud vložíte komentář na začátek výrazu, zobrazí se v textovém poli transformace, kde můžete zdokumentovat své výrazy transformace.

![Komentář v textovém poli transformace](media/data-flow/comments2.png "Komentáře")

## <a name="regular-expressions"></a>Regulární výrazy

Mnoho funkcí jazyka Expression používá syntaxi regulárního výrazu. Pokud používáte funkce regulárního výrazu, nástroj Tvůrce výrazů se pokusí interpretovat zpětné lomítko ( \\ ) jako řídicí znak sekvence. Použijete-li zpětná lomítka v regulárním výrazu, buď vložte celý regulární výraz do značek ( \` ) nebo použijte dvojité zpětné lomítko.

Příklad, který používá přetržení:

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

Příklad, který používá dvojitá lomítka:

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="address-array-indexes"></a>Indexy polí adres

Pomocí funkcí výrazů, které vracejí pole, použijte hranaté závorky ([]) k adresování konkrétních indexů uvnitř tohoto návratového objektu pole. Pole je založené na.

![Pole Tvůrce výrazů](media/data-flow/expb2.png "Náhled dat výrazu")

## <a name="keyboard-shortcuts"></a>Klávesové zkratky

* CTRL + K CTRL + C: komentovat celý řádek.
* CTRL + K CTRL + U: zrušit komentář.
* F1: Poskytněte příkazy Nápověda pro Editor.
* Klávesa Alt + šipka dolů: přesunout aktuální řádek dolů
* Alt + šipka nahoru klávesa: přesunout aktuální řádek nahoru
* CTRL + MEZERNÍK: zobrazit kontextovou podporu.

## <a name="convert-to-dates-or-timestamps"></a>Převést na data nebo časová razítka

Chcete-li do výstupu časového razítka zahrnout řetězcové literály, zabalte převod do ```toString()``` .

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Chcete-li převést milisekundy z epocha na datum nebo časové razítko, použijte `toTimestamp(<number of milliseconds>)` . Pokud se čas koná v sekundách, vynásobte 1 000.

```toTimestamp(1574127407*1000l)```

Koncový znak "l" na konci předchozího výrazu znamená převod na dlouhý typ jako vloženou syntaxi.

## <a name="find-time-from-epoch-or-unix-time"></a>Najít čas z času epocha nebo UNIX

toLong (currentTimestamp ()-toTimestamp (' 1970-01-01 00:00:00.000 ', ' RRRR-MM-DD HH: mm: ss. Zabezpečené úložiště)) * 1000

## <a name="next-steps"></a>Další kroky

[Začít sestavovat výrazy transformace dat](data-flow-expression-functions.md)
