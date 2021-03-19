---
title: Agregovaná transformace v toku mapování dat
description: Naučte se, jak agregovat data ve velkém měřítku v Azure Data Factory s mapováním agregované transformace toku dat.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/14/2020
ms.openlocfilehash: 71f5488b1f689e8892155b013730bcbb3c8e0e35
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90531915"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Agregovaná transformace v toku mapování dat

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Agregovaná transformace definuje agregace sloupců v datových proudech. Pomocí Tvůrce výrazů můžete definovat různé typy agregací, jako například SUM, MIN, MAX a COUNT seskupené podle existujících nebo počítaných sloupců.

## <a name="group-by"></a>Seskupit podle

Vyberte existující sloupec nebo vytvořte nový počítaný sloupec, který chcete použít jako klauzuli Group by pro agregaci. Pokud chcete použít existující sloupec, vyberte ho z rozevíracího seznamu. Pokud chcete vytvořit nový počítaný sloupec, najeďte myší na klauzuli a klikněte na **vypočítaný sloupec**. Tím se otevře [Tvůrce výrazů toku dat](concepts-data-flow-expression-builder.md). Po vytvoření vypočítaného sloupce zadejte název výstupního sloupce pod pole **název jako** . Pokud chcete přidat další klauzuli Group by, najeďte myší nad existující klauzuli a klikněte na ikonu se symbolem plus.

![Agregovaná transformační skupina podle nastavení](media/data-flow/agg.png "Agregovaná transformační skupina podle nastavení")

Klauzule GROUP by je volitelná v agregační transformaci.

## <a name="aggregate-columns"></a>Agregované sloupce

Pro sestavení agregačních výrazů použijte kartu **agregace** . Existující sloupec můžete buď přepsat agregací, nebo vytvořit nové pole s novým názvem. Agregační výraz je zadán v pravém poli vedle pole selektor názvu sloupce. Chcete-li upravit výraz, klikněte na textové pole a otevřete Tvůrce výrazů. Chcete-li přidat více agregačních sloupců, klikněte na tlačítko **Přidat** nad seznamem sloupců nebo ikonu se symbolem plus vedle existujícího agregačního sloupce. Vyberte možnost **Přidat sloupec** nebo **Přidat vzor sloupce**. Každý agregační výraz musí obsahovat alespoň jednu agregační funkci.

![Nastavení agregace](media/data-flow/aggregate-columns.png "Nastavení agregace")

> [!NOTE]
> V režimu ladění nemůže Tvůrce výrazů vydávat náhledy dat s agregačními funkcemi. Chcete-li zobrazit náhledy dat pro agregované transformace, ukončete Tvůrce výrazů a zobrazte data prostřednictvím karty Náhled dat.

### <a name="column-patterns"></a>Vzory sloupců

Pomocí [vzorů sloupců](concepts-data-flow-column-pattern.md) použijte stejnou agregaci pro sadu sloupců. To je užitečné, pokud chcete zachovat mnoho sloupců ze vstupního schématu, protože jsou ve výchozím nastavení vyřazeny. Použijte heuristickou hodnotu, například `first()` k uchování vstupních sloupců prostřednictvím agregace.

## <a name="reconnect-rows-and-columns"></a>Znovu připojit řádky a sloupce

Agregované transformace jsou podobné dotazům na agregační výběr SQL. Sloupce, které nejsou zahrnuté v klauzuli Group by ani agregační funkce, nepřecházejí do výstupu agregační transformace. Pokud chcete zahrnout další sloupce do agregovaného výstupu, proveďte jednu z následujících metod:

* Použijte agregační funkci, jako je například `last()` nebo `first()` , k zahrnutí tohoto dalšího sloupce.
* Znovu připojte sloupce k výstupnímu datovému proudu pomocí [vzoru pro vlastní spojení](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/).

## <a name="removing-duplicate-rows"></a>Odebírání duplicitních řádků

Běžné použití agregační transformace odebírá nebo identifikuje duplicitní položky ve zdrojových datech. Tento proces se označuje jako odstranění duplicit. V závislosti na sadě skupin podle klíčů použijte heuristickou volbu k určení, který duplicitní řádek chcete zachovat. Běžné heuristiky jsou `first()` , `last()` , `max()` a `min()` . Pomocí [vzorů sloupců](concepts-data-flow-column-pattern.md) můžete pravidlo použít pro všechny sloupce s výjimkou sloupců Group by.

![Odstranění duplicit](media/data-flow/agg-dedupe.png "Odstranění duplicit")

Ve výše uvedeném příkladu jsou sloupce `ProductID` a používány `Name` pro seskupení. Pokud mají dva řádky stejné hodnoty pro tyto dva sloupce, považují se za duplicitní. V této agregační transformaci budou zachovány hodnoty prvního řádku, které se shodují, a všechny ostatní budou zahozeny. Pomocí syntaxe vzoru sloupce všechny sloupce, jejichž názvy nejsou `ProductID` a `Name` jsou namapovány na jejich existující název sloupce a na základě hodnoty prvního spárovaného řádku. Výstupní schéma je stejné jako vstupní schéma.

Pro scénáře ověřování dat je `count()` možné pomocí funkce spočítat, kolik duplicit existuje.

## <a name="data-flow-script"></a>Skript toku dat

### <a name="syntax"></a>Syntax

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

Následující příklad přijímá příchozí datový proud `MoviesYear` a seskupuje řádky podle sloupce `year` . Transformace vytvoří agregovaný sloupec `avgrating` , který se vyhodnotí jako průměr sloupce `Rating` . Tato agregovaná transformace je pojmenována `AvgComedyRatingsByYear` .

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

![Skript agregovaného toku dat](media/data-flow/aggdfs1.png "Skript agregovaného toku dat")

```MoviesYear```: Odvozený sloupec definující rok a názvy sloupců ```AvgComedyRatingByYear``` : agregovaná transformace pro průměrné hodnocení kohodnoty v seskupení podle roku ```avgrating``` : název nového sloupce, který se vytváří, aby obsahoval agregovanou hodnotu.

```
MoviesYear aggregate(groupBy(year),
    avgrating = avg(toInteger(Rating))) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>Další kroky

* Definování agregace na základě okna pomocí [transformace okna](data-flow-window.md)
