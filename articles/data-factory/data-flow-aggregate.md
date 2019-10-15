---
title: Agregovaná transformace v toku dat mapování – Azure Data Factory | Microsoft Docs
description: Naučte se, jak agregovat data ve velkém měřítku v Azure Data Factory s mapováním agregované transformace toku dat.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 1dcc28313d1d8e59024fbc70738567cb59585d20
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326462"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Agregovaná transformace v toku mapování dat 



Agregovaná transformace je místo, kde definujete agregace sloupců v datových proudech. Pomocí Tvůrce výrazů můžete definovat různé typy agregací, jako například SUM, MIN, MAX a COUNT, které lze seskupit podle existujících nebo počítaných sloupců.

## <a name="group-by"></a>Seskupit podle
Vyberte existující sloupec nebo vytvořte nový počítaný sloupec, který chcete použít jako klauzuli Group by pro agregaci. Chcete-li použít existující sloupec, vyberte požadovaný sloupec z rozevíracího seznamu. Pokud chcete vytvořit nový počítaný sloupec, najeďte myší na klauzuli a klikněte na vypočítaný sloupec. Tím se otevře [Tvůrce výrazů toku dat](concepts-data-flow-expression-builder.md). Po vytvoření vypočítaného sloupce zadejte název výstupního sloupce pod pole název jako. Pokud chcete přidat další klauzuli Group by, najeďte myší nad existující klauzuli a klikněte na +.

![Agregovaná transformační skupina podle nastavení]–(media/data-flow/agg.png "agregovaná transformační skupina podle nastavení")

> [!NOTE]
> Klauzule GROUP by je volitelná v agregační transformaci.

## <a name="aggregate-column"></a>Agregovaný sloupec 
Pro sestavení agregačních výrazů vyberte kartu agregace. Můžete buď zvolit existující sloupec a přepsat hodnotu agregací, nebo vytvořit nové pole s novým názvem. Agregační výraz je zadán v pravém poli vedle pole selektor názvu sloupce. Chcete-li upravit výraz, klikněte na textové pole a otevřete Tvůrce výrazů. Pokud chcete přidat další agregaci, najeďte myší na stávající výraz a kliknutím na + vytvořte nový agregační sloupec nebo [vzorec sloupce](concepts-data-flow-column-pattern.md).

Agregovaná(media/data-flow/agg2.png "Nastavení") agregované agregované transformace pro ![transformaci]

> [!NOTE]
> Každý agregační výraz musí obsahovat alespoň jednu agregační funkci.

> [!NOTE]
> V režimu ladění nemůže Tvůrce výrazů vydávat náhledy dat s agregačními funkcemi. Chcete-li zobrazit náhledy dat pro agregované transformace, ukončete Tvůrce výrazů a zobrazte data prostřednictvím karty Náhled dat.

## <a name="reconnect-rows-and-columns"></a>Znovu připojit řádky a sloupce
Agregované transformace jsou úzce rovnocenné dotazům na agregační výběr SQL. Sloupce, které nejsou zahrnuty v klauzuli Group by ani agregační funkce, nebudou předávány do výstupu agregační transformace. Pokud existují další sloupce, které chcete zahrnout do výstupu agregovaných řádků, musíte:

* Použití agregační funkce k zahrnutí tohoto dalšího sloupce, například Last () nebo First ()
* Znovu se připojte ke sloupcům před agregací pomocí [vzoru vlastního spojení](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/).

## <a name="data-flow-script"></a>Skript toku dat

Agregovaný(media/data-flow/aggdfs1.png "skript toku dat") pro ![skript toku dat]

* ```MoviesYear```: odvozený sloupec definující rok a sloupce názvu
* ```AvgComedyRatingByYear```: agregovaná transformace pro průměrné hodnocení kohodnot v seskupení podle roku
* ```avgrating```: název nového sloupce, který se vytváří, aby obsahoval agregovanou hodnotu

```
MoviesYear aggregate(groupBy(year),
    avgrating = avg(toInteger(Rating))) ~> AvgComedyRatingByYear
```
  
## <a name="next-steps"></a>Další kroky

* Definování agregace na základě okna pomocí [transformace okna](data-flow-window.md)
