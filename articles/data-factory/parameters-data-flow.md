---
title: Parametrizace mapování toků dat
description: Zjistěte, jak parametrizovat tok dat mapování z kanálů datové továrny
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 82660cdb4ab6523bae7608fe3b071f20cb3603f8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419166"
---
# <a name="parameterizing-mapping-data-flows"></a>Parametrizace mapování toků dat

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Mapování toků dat v Azure Data Factory podporuje použití parametrů. Můžete definovat parametry uvnitř definice toku dat, které pak můžete použít v celém výrazu. Hodnoty parametrů lze nastavit volajícím kanálem prostřednictvím aktivity Spustit tok dat. Máte tři možnosti pro nastavení hodnot ve výrazech aktivity toku dat:

* Nastavení dynamické hodnoty pomocí jazyka toku řízení kanálu
* Nastavení dynamické hodnoty pomocí jazyka toku dat
* Nastavení statické hodnoty literálu pomocí obou výrazů

Pomocí této funkce můžete usnadnit, vytvořit univerzální, flexibilní a opakovaně použitelné toky dat. Pomocí těchto parametrů můžete parametrizovat nastavení toku dat a výrazy.

## <a name="create-parameters-in-a-mapping-data-flow"></a>Vytvoření parametrů v toku dat mapování

Chcete-li do toku dat přidat parametry, klikněte na prázdnou část plátna toku dat, abyste viděli obecné vlastnosti. V podokně nastavení se zobrazí karta s názvem **Parametr**. Chcete-li vygenerovat nový parametr, vyberte **možnost Nový.** Pro každý parametr musíte přiřadit název, vybrat typ a volitelně nastavit výchozí hodnotu.

![Vytvoření parametrů toku dat](media/data-flow/create-params.png "Vytvoření parametrů toku dat")

## <a name="use-parameters-in-a-mapping-data-flow"></a>Použití parametrů v toku dat mapování 

Na parametry lze odkazovat v libovolném výrazu toku dat. Parametry začínají $ a jsou neměnné. Seznam dostupných parametrů najdete uvnitř Tvůrce výrazů na kartě **Parametry.**

![Výraz parametru toku dat](media/data-flow/parameter-expression.png "Výraz parametru toku dat")

Další parametry můžete rychle přidat výběrem **parametru Nový** a zadáním názvu a typu.

![Výraz parametru toku dat](media/data-flow/new-parameter-expression.png "Výraz parametru toku dat")

### <a name="passing-in-a-column-name-as-a-parameter"></a>Předání názvu sloupce jako parametru

Běžným vzorem je předat název sloupce jako hodnotu parametru. Chcete-li odkazovat na sloupec `byName()` přidružený k parametru, použijte funkci. Nezapomeňte přetypovat sloupec na jeho vhodný typ `toString()`s funkcí přetypování, například .

Pokud jste například chtěli namapovat sloupec `columnName`řetězce na základě parametru , `toString(byName($columnName))`můžete přidat odvozenou transformaci sloupce rovnou .

![Předání názvu sloupce jako parametru](media/data-flow/parameterize-column-name.png "Předání názvu sloupce jako paramete")

## <a name="assign-parameter-values-from-a-pipeline"></a>Přiřazení hodnot parametrů z kanálu

Po vytvoření toku dat s parametry jej můžete spustit z kanálu s aktivitou toku dat. Po přidání aktivity na plátno kanálu se zobrazí dostupné parametry toku dat na kartě **Parametry aktivity.**

![Nastavení parametru toku dat](media/data-flow/parameter-assign.png "Nastavení parametru toku dat")

Pokud je datový typ parametru řetězec, můžete po klepnutí na textové pole nastavit hodnoty parametrů, můžete zadat kanál nebo výraz toku dat. Pokud zvolíte výraz kanálu, zobrazí se vám panel výrazů kanálu. Nezapomeňte zahrnout funkce potrubí do syntaxe `'@{<expression>}'`interpolace řetězce pomocí , například:

```'@{pipeline().RunId}'```

Pokud váš parametr není typu řetězce, bude vždy uveden s Tvůrce výrazů toku dat. Zde můžete zadat libovolný výraz nebo literál hodnoty, které chcete, aby odpovídaly datový typ parametru. Níže jsou uvedeny příklady výrazu toku dat a literálový řetězec z tvůrce výrazů:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Každý tok dat mapování může mít libovolnou kombinaci parametrů kanálu a toku dat. 

![Ukázka parametrů toku dat](media/data-flow/parameter-example.png "Ukázka parametrů toku dat")



## <a name="next-steps"></a>Další kroky
* [Spustit aktivitu toku dat](control-flow-execute-data-flow-activity.md)
* [Řízení výrazů toku](control-flow-expression-language-functions.md)
