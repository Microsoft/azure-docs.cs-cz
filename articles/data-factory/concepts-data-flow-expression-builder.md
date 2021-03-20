---
title: Tvůrce výrazů v mapování toku dat
description: Vytváření výrazů pomocí Tvůrce výrazů v mapování toků dat v Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/04/2021
ms.openlocfilehash: 753f201fbde5d9e7100b6e257f8dc79e4462d7b6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99584919"
---
# <a name="build-expressions-in-mapping-data-flow"></a>Výrazy sestavení v mapování toku dat

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

V rámci mapování toku dat jsou jako výrazy zadány mnoho vlastností transformace. Tyto výrazy se skládají z hodnot sloupců, parametrů, funkcí, operátorů a literálů, které se vyhodnotí na datový typ Spark v době běhu. Mapování toků dat má vyhrazené prostředí, které vám pomůže při sestavování těchto výrazů s názvem **Tvůrce výrazů**. Pro zvýraznění, kontrolu syntaxe a automatického dokončování se používá dokončování kódu  [IntelliSense](/visualstudio/ide/using-intellisense) , protože Tvůrce výrazů je navržený tak, aby se usnadnily vytváření toků dat. Tento článek vysvětluje, jak pomocí Tvůrce výrazů efektivně sestavovat obchodní logiku.

![Tvůrce výrazů](media/data-flow/expresion-builder.png "Tvůrce výrazů")

## <a name="open-expression-builder"></a>Otevřít Tvůrce výrazů

Existuje více vstupních bodů pro otevření Tvůrce výrazů. Všechny jsou závislé na konkrétním kontextu transformace toku dat. Nejběžnější případ použití je v transformacích, jako je [odvozený sloupec](data-flow-derived-column.md) a [agregace](data-flow-aggregate.md) , kde uživatelé vytvářejí nebo aktualizují sloupce pomocí jazyka výrazů toku dat. Tvůrce výrazů lze otevřít výběrem možnosti **otevřít Tvůrce výrazů** nad seznamem sloupců. Můžete také kliknout na kontext sloupce a otevřít Tvůrce výrazů přímo k tomuto výrazu.

![Otevřít Tvůrce výrazů je odvozen](media/data-flow/open-expression-builder-derive.png "Otevřít Tvůrce výrazů je odvozen")

V některých transformacích, jako je [Filtr](data-flow-filter.md), se kliknutím na textové pole s modrým výrazem otevře Tvůrce výrazů. 

![Modré pole výrazu](media/data-flow/expressionbox.png "Modré pole výrazu")

Když odkazujete na sloupce v rámci párové nebo skupinové podmínky, může výraz extrahovat hodnoty ze sloupců. Pokud chcete vytvořit výraz, vyberte **vypočítaný sloupec**.

![Možnost vypočítaného sloupce](media/data-flow/computedcolumn.png "Možnost vypočítaného sloupce")

V případech, kdy je výraz nebo hodnota literálu platnými vstupy, vyberte **Přidat dynamický obsah** k sestavení výrazu, který se vyhodnotí jako literální hodnota.

![Přidat možnost dynamického obsahu](media/data-flow/add-dynamic-content.png "Přidat možnost dynamického obsahu")

## <a name="expression-elements"></a>Elementy výrazu

V části mapování toků dat mohou být výrazy tvořeny hodnotami sloupců, parametry, funkcemi, místními proměnnými, operátory a literály. Tyto výrazy musí být vyhodnoceny jako datový typ Spark, jako je například řetězec, logická hodnota nebo celé číslo.

![Elementy výrazu](media/data-flow/expression-elements.png "Elementy výrazu")

### <a name="functions"></a>Functions

Mapování datových toků obsahuje integrované funkce a operátory, které lze použít ve výrazech. Seznam dostupných funkcí najdete v [referenčních informacích k mapování jazyka toku dat](data-flow-expression-functions.md).

#### <a name="address-array-indexes"></a>Indexy polí adres

Při práci se sloupci nebo funkcemi, které vracejí typy polí, použijte hranaté závorky ([]) pro přístup k určitému prvku. Pokud index neexistuje, výraz se vyhodnotí jako NULL.

![Pole Tvůrce výrazů](media/data-flow/expression-array.png "Náhled dat výrazu")

> [!IMPORTANT]
> V části mapování toků dat jsou pole s jedním, což znamená, že první element je odkazován pomocí indexu One. Například myArray [1] bude přistupovat k prvnímu prvku pole s názvem "myArray".

### <a name="input-schema"></a>Vstupní schéma

Pokud váš tok dat používá definované schéma v některém ze svých zdrojů, můžete odkazovat na sloupec podle názvu v mnoha výrazech. Pokud využíváte posun schématu, můžete odkazovat na sloupce explicitně pomocí `byName()` `byNames()` funkcí nebo nebo pomocí vzorů sloupců.

#### <a name="column-names-with-special-characters"></a>Názvy sloupců se speciálními znaky

Pokud máte názvy sloupců, které obsahují speciální znaky nebo mezery, uzavřete název do složených závorek, aby na ně odkazovaly ve výrazu.

```{[dbo].this_is my complex name$$$}```

### <a name="parameters"></a>Parametry

Parametry jsou hodnoty, které se předávají do toku dat za běhu z kanálu. Chcete-li odkazovat na parametr, buď klikněte na parametr v zobrazení **prvků výrazu** nebo na něj odkázat znak dolaru před jeho názvem. Například parametr s názvem parametr1 by odkazoval na `$parameter1` . Další informace najdete v tématu [datové toky mapování Parametrizace](parameters-data-flow.md).

### <a name="cached-lookup"></a>Vyhledávání v mezipaměti

Vyhledávání v mezipaměti umožňuje provádět vložené vyhledávání výstupu jímky uložené v mezipaměti. Existují dvě funkce, které lze použít pro každou jímku `lookup()` a `outputs()` . Syntaxe pro odkazování na tyto funkce je `cacheSinkName#functionName()` . Další informace najdete v tématu [jímky mezipaměti](data-flow-sink.md#cache-sink).

`lookup()` převezme odpovídající sloupce v aktuální transformaci jako parametry a vrátí složitý sloupec stejný jako řádek, který odpovídá klíčovým sloupcům v jímky mezipaměti. Vrácený složený sloupec obsahuje Podsloupec pro každý sloupec mapovaný v jímky mezipaměti. Například pokud byla jímka mezipaměti kódu chyby `errorCodeCache` , která obsahovala klíčový sloupec odpovídající kódu a sloupci s názvem `Message` . Volání `errorCodeCache#lookup(errorCode).Message` by vrátilo zprávu odpovídající předanému kódu. 

`outputs()` nepřijímá žádné parametry a vrací celou vyrovnávací paměť jako pole komplexních sloupců. Tuto možnost nelze volat, pokud jsou v jímky zadány klíčové sloupce a měly by být použity pouze v případě, že je v jímky mezipaměti malý počet řádků. Běžným případem použití je připojení maximální hodnoty k přírůstkovým klíčům. Pokud jeden agregovaný řádek v mezipaměti `CacheMaxKey` obsahuje sloupec `MaxKey` , můžete na první hodnotu odkazovat voláním `CacheMaxKey#outputs()[1].MaxKey` .

![Vyhledávání v mezipaměti](media/data-flow/cached-lookup-example.png "Vyhledávání v mezipaměti")

### <a name="locals"></a>Místní hodnoty

Pokud sdílíte logiku napříč více sloupci nebo chcete svou logiku rozdělit, můžete v odvozené column\. vytvořit místní. Chcete-li odkazovat na místní, buď klikněte na místní v zobrazení **prvků výrazu** nebo na něj odkázat dvojtečku před jeho názvem. Například místní s názvem local1 by odkazovalo na `:local1` . Další informace o národních prostředích najdete v [dokumentaci k Odvozenému sloupci](data-flow-derived-column.md#locals).

## <a name="preview-expression-results"></a>Náhled výsledků výrazu

Pokud je [režim ladění](concepts-data-flow-debug-mode.md) zapnutý, můžete interaktivně použít cluster ladění k zobrazení náhledu, na který výraz vyhodnocuje. Vyberte **aktualizovat** vedle data Preview a aktualizujte výsledky náhledu dat. Můžete zobrazit výstup každého řádku, který je daný vstupními sloupci.

![Průběžná verze Preview](media/data-flow/preview-expression.png "Náhled dat výrazu")

## <a name="string-interpolation"></a>Interpolace řetězců

Při vytváření dlouhých řetězců, které používají prvky výrazu, použijte interpolaci řetězce k snadnému sestavení komplexní logiky řetězce. Interpolace řetězců zabraňuje rozsáhlému použití zřetězení řetězců, pokud jsou parametry zahrnuty v řetězcích dotazů. Použijte dvojité uvozovky k uzavření textu řetězcového literálu spolu s výrazy. Můžete zahrnout funkce výrazů, sloupce a parametry. Chcete-li použít syntaxi výrazu, vložte ji do složených závorek,

Některé příklady interpolace řetězců:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

* ```"{:playerName} is a {:playerRating} player"```

> [!NOTE]
> Při použití syntaxe interpolování řetězce ve zdrojových dotazech SQL musí být řetězec dotazu na jednom řádku bez operátoru/n.

## <a name="commenting-expressions"></a>Výrazy s komentářem

Přidejte komentáře ke svým výrazům pomocí jednoduché a víceřádkové syntaxe komentáře.

V následujících příkladech jsou platné komentáře:

* ```/* This is my comment */```

* ```/* This is a```
* ```multi-line comment */```

Pokud vložíte komentář na začátek výrazu, zobrazí se v textovém poli transformace, kde můžete zdokumentovat své výrazy transformace.

![Komentář v textovém poli transformace](media/data-flow/comment-expression.png "Komentáře")

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

## <a name="keyboard-shortcuts"></a>Klávesové zkratky

Níže najdete seznam klávesových zkratek, které jsou k dispozici v Tvůrci výrazů. Většina klávesových zkratek IntelliSense je k dispozici při vytváření výrazů.

* CTRL + K CTRL + C: komentovat celý řádek.
* CTRL + K CTRL + U: zrušit komentář.
* F1: Poskytněte příkazy Nápověda pro Editor.
* Klávesa Alt + šipka dolů: přesunout aktuální řádek dolů
* Alt + šipka nahoru klávesa: přesunout aktuální řádek nahoru
* CTRL + MEZERNÍK: zobrazit kontextovou podporu.

## <a name="commonly-used-expressions"></a>Běžně používané výrazy

### <a name="convert-to-dates-or-timestamps"></a>Převést na data nebo časová razítka

Chcete-li do výstupu časového razítka zahrnout řetězcové literály, zabalte převod do ```toString()``` .

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Chcete-li převést milisekundy z epocha na datum nebo časové razítko, použijte `toTimestamp(<number of milliseconds>)` . Pokud se čas koná v sekundách, vynásobte 1 000.

```toTimestamp(1574127407*1000l)```

Koncový znak "l" na konci předchozího výrazu znamená převod na dlouhý typ jako vloženou syntaxi.

### <a name="find-time-from-epoch-or-unix-time"></a>Najít čas z času epocha nebo UNIX

toLong (currentTimestamp ()-toTimestamp (' 1970-01-01 00:00:00.000 ', ' RRRR-MM-DD HH: mm: ss. Zabezpečené úložiště)) * 1000

## <a name="next-steps"></a>Další kroky

[Začít sestavovat výrazy transformace dat](data-flow-expression-functions.md)
