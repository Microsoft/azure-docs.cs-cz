---
title: Odvozená transformace sloupce v toku mapování dat
description: Naučte se, jak transformovat data ve velkém měřítku v Azure Data Factory pomocí transformace sloupce s odvozeným datovým tokem.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/14/2020
ms.openlocfilehash: 2e90a8779322cf8967ca9a194c6cc760f7c8b8f5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90531968"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Odvozená transformace sloupce v toku mapování dat

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Pomocí transformace odvozeného sloupce můžete vygenerovat nové sloupce v toku dat nebo upravit existující pole.

## <a name="create-and-update-columns"></a>Vytvoření a aktualizace sloupců

Při vytváření odvozeného sloupce můžete buď vygenerovat nový sloupec, nebo aktualizovat existující sloupec. Do textového pole **sloupce** zadejte ve sloupci, který vytváříte. Pokud chcete přepsat existující sloupec ve schématu, můžete použít rozevírací seznam sloupec. Chcete-li vytvořit výraz odvozeného sloupce, klikněte na textové pole **výrazu ENTER** . Můžete buď začít psát svůj výraz, nebo otevřít Tvůrce výrazů a vytvořit logiku.

![Nastavení odvozeného sloupce](media/data-flow/create-derive-column.png "Nastavení odvozeného sloupce")

Chcete-li přidat další odvozené sloupce, klikněte na tlačítko **Přidat** nad seznamem sloupců nebo ikonu se symbolem plus vedle existujícího odvozeného sloupce. Vyberte možnost **Přidat sloupec** nebo **Přidat vzor sloupce**.

![Nový odvozený výběr sloupce](media/data-flow/add-derived-column.png "Nový odvozený výběr sloupce")

### <a name="column-patterns"></a>Vzory sloupců

V případech, kdy vaše schéma není explicitně definováno nebo pokud chcete aktualizovat sadu sloupců hromadně, budete chtít vytvořit sloupec vzorců. Vzory sloupců umožňují odpovídat sloupcům pomocí pravidel založených na metadatech sloupců a vytvořit odvozené sloupce pro každý odpovídající sloupec. Další informace najdete v tématu [sestavování vzorů sloupců](concepts-data-flow-column-pattern.md#column-patterns-in-derived-column-and-aggregate) v odvozené transformaci sloupce.

![Vzory sloupců](media/data-flow/column-pattern-derive.png "Vzory sloupců")

## <a name="building-schemas-using-the-expression-builder"></a>Sestavování schémat pomocí Tvůrce výrazů

Pokud používáte [Tvůrce výrazů](concepts-data-flow-expression-builder.md)pro tok dat mapování, můžete vytvořit, upravit a spravovat odvozené sloupce v části **odvozené sloupce** . Zobrazí se všechny sloupce, které jsou v transformaci vytvořeny nebo změněny. Interaktivním výběrem sloupce nebo vzoru, který upravujete, kliknutím na název sloupce. Pokud chcete přidat další sloupec, vyberte **vytvořit novou** a zvolte, jestli chcete přidat jeden sloupec nebo vzor.

![Vytvořit nový sloupec](media/data-flow/derive-add-column.png "Vytvořit nový sloupec")

Při práci se složitými sloupci můžete vytvořit podsloupce. Provedete to tak, že kliknete na ikonu plus vedle libovolného sloupce a vyberete **Přidat sloupec**. Další informace o zpracování složitých typů v toku dat naleznete v tématu [zpracování JSON při mapování toku dat](format-json.md#mapping-data-flow-properties).

![Přidat Podsloupec](media/data-flow/derive-add-subcolumn.png "Přidat Podsloupec")

Další informace o zpracování složitých typů v toku dat naleznete v tématu [zpracování JSON při mapování toku dat](format-json.md#mapping-data-flow-properties).

![Přidat složitý sloupec](media/data-flow/derive-complex-column.png "Přidání sloupců")

### <a name="locals"></a>Místní hodnoty

Pokud sdílíte logiku napříč více sloupci nebo chcete rozdělit logiku, můžete v odvozené transformaci sloupce vytvořit místní. Místní je sada logiky, která nenačte podřízenou část následující transformaci. Národní prostředí lze v Tvůrci výrazů vytvořit tak, že kliknete na **prvky výrazu** a vyberete **lokální**hodnoty. Vytvořte nový výběrem možnosti **vytvořit nový**.

![Vytvořit místní](media/data-flow/create-local.png "Vytvořit místní")

Místní proměnné mohou odkazovat na libovolný element výrazu odvozený sloupec, včetně funkcí, vstupních schémat, parametrů a dalších lokálních hodnot. Při odkazování na jiné místní hodnoty se pořadí řídí tím, že se na něj odkazuje jako na odkaz "výše".

![Vytvořit místní 2](media/data-flow/create-local-2.png "Vytvořit místní 2")

Chcete-li odkazovat na místní v odvozeném sloupci, buď klikněte na místní v zobrazení **prvků výrazu** nebo na něj odkázat dvojtečku před jeho názvem. Například místní s názvem local1 by odkazovalo na `:local1` . Pokud chcete upravit místní definici, najeďte na ni ukazatel myši v zobrazení prvků výrazu a klikněte na ikonu tužky.

![Používání místních hodnot](media/data-flow/using-locals.png "Používání místních hodnot")

## <a name="data-flow-script"></a>Skript toku dat

### <a name="syntax"></a>Syntax

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

Níže uvedený příklad je odvozený sloupec s názvem `CleanData` , který přijímá příchozí datový proud `MoviesYear` a vytvoří dva odvozené sloupce. První odvozený sloupec nahrazuje sloupec `Rating` hodnotou hodnocení jako typ Integer. Druhý odvozený sloupec je vzor, který se shoduje se všemi sloupci, jejichž název začíná řetězcem "filmy". Pro každý odpovídající sloupec vytvoří sloupec `movie` , který je roven hodnotě odpovídajícího sloupce s předponou ' movie_ '. 

V uživatelském prostředí Data Factory Tato transformace vypadá jako na následujícím obrázku:

![Odvodit příklad](media/data-flow/derive-script.png "Odvodit příklad")

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
