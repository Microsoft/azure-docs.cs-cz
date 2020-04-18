---
title: Agregovaná transformace v toku dat mapování
description: Zjistěte, jak agregovat data ve velkém měřítku v Azure Data Factory s agregační transformací toku dat mapování.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: 871f2b49e2dce9d762ef8a54923da04b0f24e4be
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606538"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Agregovaná transformace v toku dat mapování

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Agregační transformace definuje agregace sloupců v datových proudech. Pomocí Tvůrce výrazů můžete definovat různé typy agregací, například SUMA, MIN, MAX a POČET seskupené podle existujících nebo vypočítaných sloupců.

## <a name="group-by"></a>Seskupit podle

Vyberte existující sloupec nebo vytvořte nový vypočítaný sloupec, který chcete použít jako klauzuli podle skupiny pro vaši agregaci. Chcete-li použít existující sloupec, vyberte jej z rozevíracího přehledu. Pokud chcete vytvořit nový vypočítaný sloupec, najeďte na klauzuli a klikněte na **Sloupec vypočítaný**. Tím se otevře [tvůrce výrazů toku dat](concepts-data-flow-expression-builder.md). Po vytvoření počítaného sloupce zadejte název výstupního sloupce pod pole **MAs jako.** Pokud chcete přidat další klauzuli skupiny podle, najeďte na existující klauzuli a klikněte na ikonu plus.

![Agregovat transformační skupinu podle nastavení](media/data-flow/agg.png "Agregovat transformační skupinu podle nastavení")

Klauzule group by je volitelná v agregační transformaci.

## <a name="aggregate-column"></a>Agregační sloupec 

Přejděte na kartu **Agregace** a vytvořte výrazy agregace. Můžete buď přepsat existující sloupec agregací, nebo vytvořit nové pole s novým názvem. Výraz agregace se zadá do pravého pole vedle voliče názvu sloupce. Chcete-li výraz upravit, kliknutím na textové pole otevřete tvůrce výrazů. Chcete-li přidat další agregace, najeďte na existující výraz a klepnutím na ikonu plus vytvořte nový sloupec nebo [vzorek sloupce agregace](concepts-data-flow-column-pattern.md).

Každý výraz agregace musí obsahovat alespoň jednu agregační funkci.

![Agregovaná agregační nastavení transformace](media/data-flow/agg2.png "Agregovaná agregační nastavení transformace")


> [!NOTE]
> V režimu ladění tvůrce výrazů nemůže vytvářet náhledy dat s agregačními funkcemi. Chcete-li zobrazit náhledy dat pro agregované transformace, zavřete tvůrce výrazů a zobrazte data na kartě Náhled dat.

## <a name="reconnect-rows-and-columns"></a>Opětovné připojení řádků a sloupců

Agregační transformace jsou podobné dotazům sql agregace výběru. Sloupce, které nejsou zahrnuty ve vaší skupině podle klauzule nebo agregační funkce nebude tok až do výstupu agregační transformace. Chcete-li do agregovaného výstupu zahrnout další sloupce, proveďte jednu z následujících metod:

* Použijte agregační `last()` funkci, například nebo `first()` zahrnout tento další sloupec.
* Znovu se připojte ke sloupcům výstupního datového proudu pomocí [vzoru vlastního spojení](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/).

## <a name="removing-duplicate-rows"></a>Odebrání duplicitních řádků

Běžné použití agregační transformace je odebrání nebo identifikace duplicitních položek ve zdrojových datech. Tento proces se nazývá odstranění duplicit. Na základě sady skupinpo klíčů, použijte heuristickou podle vašeho výběru k určení, který duplicitní řádek zachovat. Běžné heuristiky `last()` `max()`jsou `first()` `min()`, , , a . Pomocí [vzorů sloupců](concepts-data-flow-column-pattern.md) použijte pravidlo pro každý sloupec s výjimkou skupinpo sloupců.

![Odstranění duplicit](media/data-flow/agg-dedupe.png "Odstranění duplicit")

Ve výše uvedeném `ProductID` `Name` příkladu sloupce a jsou používány pro seskupení. Pokud dva řádky mají stejné hodnoty pro tyto dva sloupce, jsou považovány za duplicitní. V této agregační transformaci budou zachovány hodnoty prvního řádku odpovídající a všechny ostatní budou vynechány. Pomocí syntaxe vzorku sloupce jsou všechny `ProductID` `Name` sloupce, jejichž názvy nejsou a jsou mapovány na jejich existující název sloupce a vzhledem k hodnotě prvních odpovídajících řádků. Výstupní schéma je stejné jako vstupní schéma.

Pro scénáře ověření `count()` dat funkce lze spočítat, kolik duplicity existují.

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

### <a name="example"></a>Příklad

Níže uvedený příklad přebírá `MoviesYear` příchozí datový proud `year`a seskupuje řádky podle sloupců . Transformace vytvoří agregační sloupec, `avgrating` který je `Rating`vyhodnocen jako průměr sloupce . Tato agregační `AvgComedyRatingsByYear`transformace je pojmenována .

V ux datové továrny tato transformace vypadá jako následující obrázek:

![Seskupit podle příkladu](media/data-flow/agg-script1.png "Seskupit podle příkladu")

![Agregační příklad](media/data-flow/agg-script2.png "Agregační příklad")

Skript toku dat pro tuto transformaci je ve fragmentu níže.

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

![Agregovat skript toku dat](media/data-flow/aggdfs1.png "Agregovat skript toku dat")

```MoviesYear```: Odvozený sloupec definující ```AvgComedyRatingByYear```rok a sloupce nadpisu : Agregovaná transformace pro průměrné hodnocení komedií seskupených podle roku ```avgrating```: Název vytvářeného nového sloupce pro uložení agregované hodnoty

```
MoviesYear aggregate(groupBy(year),
    avgrating = avg(toInteger(Rating))) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>Další kroky

* Definovat agregaci na základě okna pomocí [window transformace](data-flow-window.md)
