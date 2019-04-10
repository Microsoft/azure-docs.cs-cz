---
title: Azure Data Factory mapování datového toku Tvůrce
description: Toky Tvůrce výrazů pro Azure Data Factory mapovacích dat
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: df9cfb0c0e36f54c8b1fbee4def552c78e9d42c1
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2019
ms.locfileid: "59359775"
---
# <a name="mapping-data-flow-expression-builder"></a>Mapování Tvůrce výrazů toku dat

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

V Azure Data Factory mapování toku dat najdete pole výrazu, kde můžete zadat výrazy pro transformaci dat. Použít sloupce, pole, proměnné, parametry, funkce ze svého toku dat v těchto polích. Sestavit výraz, použijte Tvůrce, který se spustí po kliknutí do textového pole výraz uvnitř transformace. Také v některých případech uvidíte možnosti "Vypočítaného sloupce" při výběru sloupce pro transformaci. Po klepnutí na tlačítko, které, uvidíte také spustit Tvůrce výrazů.

![Tvůrce výrazů](media/data-flow/expression.png "Tvůrce výrazů")

Nástroj Tvůrce výchozí možnosti textového editoru. funkce automatického dokončování čte z objektový model celý tok dat objekt pro vytváření dat Azure pomocí syntaxe kontrolu a zvýraznění.

![Tvůrce výrazů automatické dokončování](media/data-flow/expb1.png "Tvůrce automatického dokončování")

## <a name="currently-working-on-field"></a>Právě pracuje na pole

![Tvůrce výrazů](media/data-flow/exp3.png "aktuálně pracují")

V levém horním rohu rozhraní Tvůrce výrazů, zobrazí se pole s názvem "Aktuálně práce na" s názvem pole, které jste právě pracujete. Výraz, který vytvoříte v uživatelském rozhraní se použijí jenom k, které aktuálně pracovní pole. Pokud chcete transformovat jiné pole, uložit aktuální práci a pomocí rozevíracího seznamu vyberte jiné pole a sestavení výrazu pro ostatní pole.

## <a name="data-preview-in-debug-mode"></a>Náhled dat v režimu ladění

![Tvůrce výrazů](media/data-flow/exp4b.png "výraz dat ve verzi Preview")

Při práci na vaší výrazy, můžete volitelně přepnout na režim ladění z povrchu návrhu toku dat objekt pro vytváření dat Azure povolení živého náhledu v průběhu výsledky dat z výrazu, který vytváříte. Ladění v za provozu v reálném čase je povolená pro vaše výrazy.

![Režim ladění](media/data-flow/debugbutton.png "ladění tlačítko")


![Tvůrce výrazů](media/data-flow/exp5.png "výraz dat ve verzi Preview")

## <a name="comments"></a>Komentáře

Přidání komentářů do vaší výrazů pomocí jeden řádek a víceřádkových komentářů syntaxe:

![Komentáře](media/data-flow/comments.png "komentáře")

## <a name="regular-expressions"></a>Regulární výrazy

Jazyk výrazů toku dat objekt pro vytváření dat Azure [úplnou referenční dokumentaci](https://aka.ms/dataflowexpressions), umožňuje funkce, které zahrnují syntaxi regulárního výrazu. Při použití funkce regulárního výrazu, Tvůrce výrazů pokusí interpretovat zpětné lomítko (\\) jako znak sekvence escape. Při použití zpětná lomítka v regulárním výrazu, uzavřete celý regulární výraz v taktech (\`) nebo použijte dvojité zpětné lomítko.

Příklad použití značky

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

nebo pomocí dvojité lomítka

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="addressing-array-indexes"></a>Základní adresování indexy pole

U výrazu funkcí, které vrací pole k adresování specifickými indexy uvnitř objektu pole vrácené použijte hranaté závorky []. Pole je založená na ty.

![Výraz pole Tvůrce](media/data-flow/expb2.png "výraz dat ve verzi Preview")

## <a name="handling-names-with-special-characters"></a>Zpracování názvů s speciální znaky

Až budete mít názvy sloupců, které obsahují speciální znaky ani mezery, uzavřete název pomocí složených závorek.
* ```{[dbo].this_is my complex name$$$}```

## <a name="next-steps"></a>Další postup

[Začít sestavovat výrazy transformace dat](data-flow-expression-functions.md)
