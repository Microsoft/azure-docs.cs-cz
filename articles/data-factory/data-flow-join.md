---
title: Transformace datového toku Azure Data Factory
description: Transformace datového toku Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: da6c3c90ebbeffcf468aad3809da097976d8ef0d
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387229"
---
# <a name="mapping-data-flow-join-transformation"></a>Mapování transformace toku dat



Ke kombinování dat ze dvou tabulek v toku dat použijte JOIN. Klikněte na transformaci, která bude levou relací a přidejte transformaci spojení ze sady nástrojů. V transformaci spojení vyberete jiný datový proud z datového toku, který má být pravý.

![Transformace spojení](media/data-flow/join.png "Spojit")

## <a name="join-types"></a>Typy spojení

Pro transformaci JOIN je vyžadován výběr typu spojení.

### <a name="inner-join"></a>Vnitřní spojení

Vnitřní spojení projde pouze řádky, které odpovídají podmínkám sloupce z obou tabulek.

### <a name="left-outer"></a>Levé vnější

Všechny řádky z levého proudu, který nesplňuje podmínky spojení, jsou předány a výstupní sloupce z druhé tabulky jsou kromě všech řádků vrácených vnitřním spojením nastaveny na hodnotu NULL.

### <a name="right-outer"></a>Pravé vnější

Všechny řádky ze pravého proudu, který nesplňuje podmínky spojení, jsou předány a výstupní sloupce, které odpovídají druhé tabulce, jsou nastaveny na hodnotu NULL a navíc na všechny řádky vrácené vnitřním spojením.

### <a name="full-outer"></a>Úplné vnější

Úplné vnější vytvoří všechny sloupce a řádky z obou stran s hodnotami NULL pro sloupce, které nejsou k dispozici v druhé tabulce.

### <a name="cross-join"></a>Vzájemné spojení

Určete různé produkty dvou datových proudů pomocí výrazu. Tuto možnost můžete použít k vytvoření vlastních podmínek spojení.

## <a name="specify-join-conditions"></a>Zadat podmínky připojení

Zbývající podmínka spojení je z datového proudu připojeného nalevo od vaší služby JOIN. Pravá podmínka spojení je druhý datový proud připojený k vašemu spojení v dolní části, který bude buď přímým konektorem na jiný datový proud, nebo odkaz na jiný datový proud.

Je nutné zadat alespoň 1 (1.. n) podmínky spojení. Můžou to být pole, na která se odkazuje přímo, vybraná z rozevírací nabídky nebo výrazy.

## <a name="join-performance-optimizations"></a>Sloučit optimalizace výkonu

Na rozdíl od spojení sloučení v nástrojích, jako je SSIS, se spojení v toku dat ADF nejedná o povinnou operaci sloučení slučovacího spojení. Proto se klíče JOIN nemusejí seřadit jako první. Operace join proběhne na základě optimální operace join ve Sparku: všesměrové vysílání/spojení na straně mapy:

![Optimalizace transformace JOIN](media/data-flow/joinoptimize.png "Spojit optimalizaci")

Pokud se vaše datová sada vejde do paměti pracovního uzlu, můžeme optimalizovat výkon připojení. Můžete také určit rozdělení dat v rámci operace JOIN a vytvořit tak sady dat, které mohou být pro jednotlivé pracovní procesy lépe přizpůsobeny paměti.

## <a name="self-join"></a>Připojovat se k sobě

Podmínky pro samoobslužné spojení v toku dat ADF můžete dosáhnout pomocí transformace SELECT pro vytvoření aliasu existujícího datového proudu. Nejdřív vytvořte z datového proudu "novou větev" a pak přidejte výběr pro vytvoření aliasu pro celý původní datový proud.

![Připojovat se k sobě](media/data-flow/selfjoin.png "Připojovat se k sobě")

Ve výše uvedeném diagramu je transformace SELECT v horní části. Všechny IT jsou aliasy původního datového proudu na "OrigSourceBatting". V zvýrazněné transformaci spojení níže vidíte, že používáme tento datový proud s aliasem jako připojení na pravé straně, což nám umožňuje odkázat na stejný klíč v levém & pravé straně vnitřního spojení.

## <a name="composite-and-custom-keys"></a>Složené a vlastní klíče

V transformaci spojení můžete vytvořit vlastní a složené klíče průběžně. Přidejte řádky pro další sloupce spojení se znaménkem plus (+) vedle každého řádku relace. Nebo vypočítá novou hodnotu klíče v Tvůrci výrazů pro hodnotu průběžného spojení.

## <a name="next-steps"></a>Další kroky

Po připojení dat můžete [vytvořit nové sloupce](data-flow-derived-column.md) a [ukládat data do cílového úložiště dat](data-flow-sink.md).
