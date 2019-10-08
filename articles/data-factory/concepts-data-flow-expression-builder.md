---
title: Tvůrce výrazů pro tok dat mapování Azure Data Factory
description: Tvůrce výrazů pro Azure Data Factory mapování datových toků
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: 9862866d5cddb227d9417ac15db6b8ea851507e6
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72030291"
---
# <a name="mapping-data-flow-expression-builder"></a>Tvůrce výrazů pro tok dat mapování



V Azure Data Factory mapování toku dat najdete pole výrazů, kde můžete zadat výrazy pro transformaci dat. V těchto polích můžete použít sloupce, pole, proměnné, parametry a funkce z datového toku. Chcete-li sestavit výraz, použijte Tvůrce výrazů, který je spuštěn, kliknutím do textového pole výraz uvnitř transformace. Při výběru sloupců pro transformaci se taky někdy zobrazí možnosti vypočítaného sloupce. Když na něj kliknete, uvidíte také, že se spustil Tvůrce výrazů.

(media/data-flow/xpb1.png "Tvůrce") výrazů ![Tvůrce výrazů]

Nástroj Tvůrce výrazů je standardně nastaven na možnost textový editor. funkce automatického dokončování načte z celého modelu objektu Azure Data Factory toku dat pomocí kontroly syntaxe a zvýrazňování.

Automatické dokončování(media/data-flow/expb1.png "Tvůrce výrazů") ![automatického dokončování Tvůrce výrazů]– automatické dokončování

## <a name="build-schemas-in-output-schema-pane"></a>Schémata sestavení v podokně výstupní schéma

![Přidat složité sloupce](media/data-flow/complexcolumn.png "Přidat sloupce")

V podokně výstupní schéma na levé straně uvidíte sloupce, které upravujete, a přidáte je do schématu. Tady můžete interaktivně vytvářet jednoduché a komplexní datové struktury. Přidejte další pole pomocí příkazu přidat sloupec a hierarchie sestavení pomocí příkazu přidat Podsloupec.

![Přidat]Podsloupec(media/data-flow/addsubcolumn.png "Přidat") Podsloupec

## <a name="data-preview-in-debug-mode"></a>Náhled dat v režimu ladění

(media/data-flow/exp4b.png "Náhled dat výrazů") ![Tvůrce výrazů]

Při práci na výrazech toku dat přepněte na režim ladění z Azure Data Factory plochy pro návrh toku dat a v rámci výrazu, který sestavíte, umožníte živý náhled výsledků vašich dat. Živé ladění v reálném čase je pro vaše výrazy povolené.

Ladicí ![režim]ladění –(media/data-flow/debugbutton.png "tlačítko")

Kliknutím na tlačítko Aktualizovat aktualizujete výsledky výrazu na živý ukázkový zdroj v reálném čase.

(media/data-flow/exp5.png "Náhled dat výrazů") ![Tvůrce výrazů]

## <a name="comments"></a>Komentáře

Přidejte komentáře ke svým výrazům pomocí jedné řádky a syntaxe víceřádkového komentáře:

(media/data-flow/comments.png "Komentáře") k ![komentářům]

## <a name="regular-expressions"></a>Regulární výrazy

Azure Data Factory jazyk pro datový tok toku dat, [Úplná Referenční dokumentace](https://aka.ms/dataflowexpressions), umožňuje funkce, které zahrnují syntaxi regulárního výrazu. Při použití funkcí regulárních výrazů se Tvůrce výrazů pokusí interpretovat zpětné lomítko (\\) jako řídicí znak sekvence. Při použití zpětných lomítek ve regulárním výrazu buď vložte celý regulární výraz do tiků (\`) nebo použijte dvojité zpětné lomítko.

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

(media/data-flow/expb2.png "Ukázková data výrazu") ![pole Tvůrce výrazů]– náhled

## <a name="handling-names-with-special-characters"></a>Zpracování názvů se speciálními znaky

Pokud máte názvy sloupců, které obsahují speciální znaky nebo mezery, uzavřete název do složených závorek.
* ```{[dbo].this_is my complex name$$$}```

## <a name="next-steps"></a>Další kroky

[Začít sestavovat výrazy transformace dat](data-flow-expression-functions.md)
