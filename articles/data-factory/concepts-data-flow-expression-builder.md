---
title: Tvůrce výrazů pro tok dat mapování Azure Data Factory
description: Tvůrce výrazů pro Azure Data Factory mapování datových toků
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/17/2019
ms.openlocfilehash: 3664a7c311e15ce3aa61fc71f98a46e3f2618143
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74184669"
---
# <a name="mapping-data-flow-expression-builder"></a>Tvůrce výrazů pro tok dat mapování



V Azure Data Factory mapování toku dat najdete pole výrazů, kde můžete zadat výrazy pro transformaci dat. V těchto polích můžete použít sloupce, pole, proměnné, parametry a funkce z datového toku. Chcete-li sestavit výraz, použijte Tvůrce výrazů, který je spuštěn, kliknutím do textového pole výraz uvnitř transformace. Při výběru sloupců pro transformaci se taky někdy zobrazí možnosti vypočítaného sloupce. Když na něj kliknete, uvidíte také, že se spustil Tvůrce výrazů.

![Tvůrce výrazů](media/data-flow/xpb1.png "Tvůrce výrazů")

Nástroj Tvůrce výrazů je standardně nastaven na možnost textový editor. funkce automatického dokončování načte z celého modelu objektu Azure Data Factory toku dat pomocí kontroly syntaxe a zvýrazňování.

![Automatické dokončování Tvůrce výrazů](media/data-flow/expb1.png "Automatické dokončování Tvůrce výrazů")

## <a name="build-schemas-in-output-schema-pane"></a>Schémata sestavení v podokně výstupní schéma

![Přidat složitý sloupec](media/data-flow/complexcolumn.png "Přidat sloupce")

V podokně výstupní schéma na levé straně uvidíte sloupce, které upravujete, a přidáte je do schématu. Tady můžete interaktivně vytvářet jednoduché a komplexní datové struktury. Přidejte další pole pomocí příkazu přidat sloupec a hierarchie sestavení pomocí příkazu přidat Podsloupec.

![Přidat Podsloupec](media/data-flow/addsubcolumn.png "Přidat Podsloupec")

## <a name="data-preview-in-debug-mode"></a>Náhled dat v režimu ladění

![Tvůrce výrazů](media/data-flow/exp4b.png "Náhled dat výrazu")

Při práci na výrazech toku dat přepněte na režim ladění z Azure Data Factory plochy pro návrh toku dat a v rámci výrazu, který sestavíte, umožníte živý náhled výsledků vašich dat. Živé ladění v reálném čase je pro vaše výrazy povolené.

![Režim ladění](media/data-flow/debugbutton.png "Tlačítko ladit")

Kliknutím na tlačítko Aktualizovat aktualizujete výsledky výrazu na živý ukázkový zdroj v reálném čase.

![Tvůrce výrazů](media/data-flow/exp5.png "Náhled dat výrazu")

## <a name="comments"></a>Komentáře

Přidejte komentáře ke svým výrazům pomocí jedné řádky a syntaxe víceřádkového komentáře:

![Komentáře](media/data-flow/comments.png "Komentáře")

## <a name="regular-expressions"></a>Regulární výrazy

Azure Data Factory jazyk pro datový tok toku dat, [Úplná Referenční dokumentace](https://aka.ms/dataflowexpressions), umožňuje funkce, které zahrnují syntaxi regulárního výrazu. Při použití funkcí regulárních výrazů se Tvůrce výrazů pokusí interpretovat zpětné lomítko (\\) jako sekvenci řídicích znaků. Při použití zpětných lomítek ve regulárním výrazu buď vložte celý regulární výraz do impulzů (\`) nebo použijte dvojité zpětné lomítko.

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

## <a name="handling-names-with-special-characters"></a>Zpracování názvů se speciálními znaky

Pokud máte názvy sloupců, které obsahují speciální znaky nebo mezery, uzavřete název do složených závorek.
* ```{[dbo].this_is my complex name$$$}```

## <a name="keyboard-shortcuts"></a>Klávesové zkratky

* ```Ctrl-K Ctrl-C```: komentáře k celému řádku
* ```Ctrl-K Ctrl-U```: odkomentovat
* ```F1```: Poskytněte příkazy pro Help Editor.
* ```Alt-Down Arrow```: přesunout aktuální řádek dolů
* ```Alt-Up Arrow```: přesunout aktuální řádek nahoru
* ```Cntrl-Space```: zobrazit kontextovou podporu

## <a name="manual-comments"></a>Ruční komentáře

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

Pokud komentář zadáte v horní části výrazu, zobrazí se v textovém poli transformace k dokumentaci vašich transformačních výrazů:

![Komentáře](media/data-flow/comments2.png "Komentáře")

## <a name="convert-to-dates-or-timestamps"></a>Převést na data nebo časová razítka

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Všimněte si, že pokud chcete do výstupu časového razítka zahrnout řetězcové literály, je potřeba zabalit převod do metody toString ().

## <a name="handling-column-names-with-special-characters"></a>Zpracování názvů sloupců se speciálními znaky

Pokud máte názvy sloupců, které obsahují speciální znaky nebo mezery, uzavřete název do složených závorek.

```{[dbo].this_is my complex name$$$}```

## <a name="next-steps"></a>Další kroky

[Začít sestavovat výrazy transformace dat](data-flow-expression-functions.md)
