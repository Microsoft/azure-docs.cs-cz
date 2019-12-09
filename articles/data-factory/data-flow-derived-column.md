---
title: Odvozená transformace sloupce v toku mapování dat
description: Naučte se, jak transformovat data ve velkém měřítku v Azure Data Factory pomocí transformace sloupce s odvozeným datovým tokem.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/15/2019
ms.openlocfilehash: ce774868acc281f769e7f2991472e7d2a5da9739
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930397"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Odvozená transformace sloupce v toku mapování dat

Pomocí transformace odvozeného sloupce můžete vygenerovat nové sloupce v toku dat nebo upravit existující pole.

## <a name="derived-column-settings"></a>Nastavení odvozeného sloupce

Pokud chcete přepsat existující sloupec, vyberte ho přes rozevírací seznam sloupec. V opačném případě použijte pole výběru sloupce jako textové pole a zadejte název nového sloupce. Chcete-li vytvořit výraz odvozeného sloupce, klikněte na pole zadejte výraz pro otevření [Tvůrce výrazů toku dat](concepts-data-flow-expression-builder.md).

![Nastavení odvozeného sloupce](media/data-flow/dc1.png "Nastavení odvozeného sloupce")

Chcete-li přidat další odvozené sloupce, najeďte myší na existující odvozený sloupec a klikněte na ikonu se symbolem plus. Vyberte možnost **Přidat sloupec** nebo **Přidat vzor sloupce**. Vzorce sloupce mohou být užitečné, pokud jsou názvy sloupců z vašich zdrojů proměnné. Další informace najdete v tématu [vzory sloupců](concepts-data-flow-column-pattern.md).

![Nový odvozený výběr sloupce](media/data-flow/columnpattern.png "Nový odvozený výběr sloupce")

## <a name="data-flow-script"></a>Skript toku dat

### <a name="syntax"></a>Syntaxe

```
<incomingStream>
    derive(
           <columnName1> = <expression1>,
           <columnName2> = <expression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <deriveTransformationName>
```

### <a name="example"></a>Příklad:

Níže uvedený příklad je odvozený sloupec s názvem `CleanData`, který přebírá příchozí datový proud `MoviesYear` a vytvoří dva odvozené sloupce. První odvozený sloupec nahrazuje sloupec `Rating` hodnotou hodnocení jako typ Integer. Druhý odvozený sloupec je vzor, který se shoduje se všemi sloupci, jejichž název začíná řetězcem "filmy". Pro každý odpovídající sloupec vytvoří sloupec `movie`, který se rovná hodnotě odpovídajícího sloupce s předponou "movie_". 

V uživatelském prostředí Data Factory Tato transformace vypadá jako na následujícím obrázku:

![Odvodit příklad](media/data-flow/derive-script1.png "Odvodit příklad")

Skript toku dat pro tuto transformaci je v následujícím fragmentu kódu:

```
MoviesYear derive(
                Rating = toInteger(Rating),
                each(
                    match(startsWith(name,'movies')),
                    'movie' = 'movie_' + toString($$)
                )
            ) ~> CleanData
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [jazyce výrazu Mapping data Flow](data-flow-expression-functions.md).
