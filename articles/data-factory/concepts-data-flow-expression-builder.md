---
title: Azure Data Factory mapování datového toku Tvůrce
description: Toky Tvůrce výrazů pro Azure Data Factory mapovacích dat
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: a65ba66b91b89cca7a41b7276a0ee4790088d5a1
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56213294"
---
# <a name="azure-data-factory-mapping-data-flow-expression-builder"></a>Azure Data Factory mapování datového toku Tvůrce

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

Jazyk výrazů toku dat objekt pro vytváření dat Azure [úplnou referenční dokumentaci](http://aka.ms/dataflowexpressions), umožňuje funkce, které zahrnují syntaxi regulárního výrazu. Při použití funkce regulárního výrazu, Tvůrce výrazů pokusí interpretovat zpětné lomítko (\) jako řídicí znak sekvence. Při použití zpětná lomítka v regulárním výrazu, uzavřete celý regulární výraz v taktech ` ` nebo použijte dvojité zpětné lomítko.

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
