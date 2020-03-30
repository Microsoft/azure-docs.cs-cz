---
title: Odvozená transformace sloupce v toku dat mapování
description: Zjistěte, jak transformovat data ve velkém měřítku v Azure Data Factory pomocí mapování toku dat odvozené sloupce transformace.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/15/2019
ms.openlocfilehash: 66396de52b3709c1d9357f32a375a29a8dcdbd1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77048749"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Odvozená transformace sloupce v toku dat mapování

Odvozená transformace sloupce slouží ke generování nových sloupců v toku dat nebo k úpravě existujících polí.

## <a name="derived-column-settings"></a>Nastavení odvozených sloupců

Chcete-li přepsat existující sloupec, vyberte jej pomocí rozevíracího přehledu sloupců. V opačném případě použijte pole pro výběr sloupce jako textové pole a zadejte název nového sloupce. Chcete-li vytvořit výraz odvozeného sloupce, klikněte na pole Zadat výraz a otevřete [Tvůrce exprese toku dat](concepts-data-flow-expression-builder.md).

![Nastavení odvozených sloupců](media/data-flow/dc1.png "Nastavení odvozených sloupců")

Chcete-li přidat další odvozené sloupce, najeďte na existující odvozený sloupec a klepněte na ikonu plus. Zvolte **Přidat sloupec** nebo Přidat vzorek **sloupce**. Vzory sloupců se mohou hodit, pokud jsou názvy sloupců z vašich zdrojů variabilní. Další informace naleznete [v tématu Vzorky sloupců](concepts-data-flow-column-pattern.md).

![Nový výběr odvozeného sloupce](media/data-flow/columnpattern.png "Nový výběr odvozeného sloupce")

## <a name="build-schemas-in-output-schema-pane"></a>Vytváření schémat v podokně Schéma výstupu

Sloupce, které upravujete a přidáváte do schématu, jsou uvedeny v podokně Schéma výstupu. Můžete interaktivně vytvářet jednoduché a složité datové struktury zde. Chcete-li přidat další pole, vyberte **Přidat sloupec**. Chcete-li vytvořit hierarchie, vyberte **Přidat podsloupec**.

![Přidat podsloupec](media/data-flow/addsubcolumn.png "Přidat podsloupec")

Další informace o zpracování složitých typů v toku dat naleznete [v tématu Zpracování JSON v mapování toku dat](format-json.md#mapping-data-flow-properties).

![Přidání složitého sloupce](media/data-flow/complexcolumn.png "Přidání sloupců")

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

### <a name="example"></a>Příklad

Níže uvedený příklad je odvozený sloupec s `CleanData` `MoviesYear` názvem, který přebírá příchozí datový proud a vytvoří dva odvozené sloupce. První odvozený sloupec nahradí `Rating` sloupec hodnotou hodnocení jako celého typu. Druhý odvozený sloupec je vzorek, který odpovídá každému sloupci, jehož název začíná na "filmy". Pro každý odpovídající sloupec vytvoří `movie` sloupec, který se rovná hodnotě odpovídajícího sloupce s předponou "movie_". 

V ux datové továrny tato transformace vypadá jako následující obrázek:

![Odvozený příklad](media/data-flow/derive-script1.png "Odvozený příklad")

Skript toku dat pro tuto transformaci je ve fragmentu níže:

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

- Přečtěte si další informace o [výrazu mapování toku dat](data-flow-expression-functions.md).
