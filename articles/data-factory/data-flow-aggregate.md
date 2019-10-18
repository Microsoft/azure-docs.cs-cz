---
title: Agregovaná transformace v datovém toku mapování Azure Data Factory | Microsoft Docs
description: Naučte se, jak agregovat data ve velkém měřítku v Azure Data Factory s mapováním agregované transformace toku dat.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: add548a184440c408b8b74e131f2249b4f616ddc
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72514825"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Agregovaná transformace v toku mapování dat 

Agregovaná transformace definuje agregace sloupců v datových proudech. Pomocí Tvůrce výrazů můžete definovat různé typy agregací, jako například SUM, MIN, MAX a COUNT seskupené podle existujících nebo počítaných sloupců.

## <a name="group-by"></a>Seskupit podle

Vyberte existující sloupec nebo vytvořte nový počítaný sloupec, který chcete použít jako klauzuli Group by pro agregaci. Pokud chcete použít existující sloupec, vyberte ho z rozevíracího seznamu. Pokud chcete vytvořit nový počítaný sloupec, najeďte myší na klauzuli a klikněte na **vypočítaný sloupec**. Tím se otevře [Tvůrce výrazů toku dat](concepts-data-flow-expression-builder.md). Po vytvoření vypočítaného sloupce zadejte název výstupního sloupce pod pole **název jako** . Pokud chcete přidat další klauzuli Group by, najeďte myší nad existující klauzuli a klikněte na ikonu se symbolem plus.

![Agregovaná transformační skupina podle nastavení](media/data-flow/agg.png "Agregovaná transformační skupina podle nastavení")

Klauzule GROUP by je volitelná v agregační transformaci.

## <a name="aggregate-column"></a>Agregovaný sloupec 

Pro sestavení agregačních výrazů použijte kartu **agregace** . Existující sloupec můžete buď přepsat agregací, nebo vytvořit nové pole s novým názvem. Agregační výraz je zadán v pravém poli vedle pole selektor názvu sloupce. Chcete-li upravit výraz, klikněte na textové pole a otevřete Tvůrce výrazů. Chcete-li přidat další agregace, najeďte myší na existující výraz a kliknutím na ikonu Plus vytvořte nový agregační sloupec nebo [vzorec sloupce](concepts-data-flow-column-pattern.md).

Každý agregační výraz musí obsahovat alespoň jednu agregační funkci.

![Agregovaná nastavení agregace transformace](media/data-flow/agg2.png "Agregovaná nastavení agregace transformace")


> [!NOTE]
> V režimu ladění nemůže Tvůrce výrazů vydávat náhledy dat s agregačními funkcemi. Chcete-li zobrazit náhledy dat pro agregované transformace, ukončete Tvůrce výrazů a zobrazte data prostřednictvím karty Náhled dat.

## <a name="reconnect-rows-and-columns"></a>Znovu připojit řádky a sloupce

Agregované transformace jsou podobné dotazům na agregační výběr SQL. Sloupce, které nejsou zahrnuté v klauzuli Group by ani agregační funkce, nepřecházejí do výstupu agregační transformace. Pokud chcete zahrnout další sloupce do agregovaného výstupu, proveďte jednu z následujících metod:

* Použijte agregační funkci, například `last()` nebo `first()` k zahrnutí tohoto dalšího sloupce.
* Znovu připojte sloupce k výstupnímu datovému proudu pomocí [vzoru pro vlastní spojení](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/).

## <a name="data-flow-script"></a>Skript toku dat

### <a name="syntax"></a>Syntaxe

```
<incomingStream>
    aggregate(
           groupBy(
                <groupByColumnName> = <groupByExpression1>,
                <groupByExpression2>
               ),
           <aggregateColumn1> = <aggregateExpression1>,
           <aggregateColumn2> = <aggregateExpression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <aggregateTransformationName>
```

### <a name="example"></a>Příklad:

Následující příklad přijímá příchozí datový proud `MoviesYear` a seskupuje řádky podle sloupce `year`. Transformace vytvoří agregovaný sloupec `avgrating`, který se vyhodnotí jako průměr sloupce `Rating`. Tato agregovaná transformace se nazývá `AvgComedyRatingsByYear`.

V uživatelském prostředí Data Factory Tato transformace vypadá jako na následujícím obrázku:

![Seskupit podle příkladu](media/data-flow/agg-script1.png "Seskupit podle příkladu")

![Agregovaný příklad](media/data-flow/agg-script2.png "Agregovaný příklad")

Skript toku dat pro tuto transformaci je v následujícím fragmentu kódu.

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>Další kroky

* Definování agregace na základě okna pomocí [transformace okna](data-flow-window.md)
