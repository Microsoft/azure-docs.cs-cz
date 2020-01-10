---
title: Toky dat mapování Parametrizace
description: Naučte se parametrizovat tok dat mapování z kanálů Data Factory.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: c589cfeab7a812e09ce7f7620e93b72bd362859a
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2020
ms.locfileid: "75746125"
---
# <a name="parameterizing-mapping-data-flows"></a>Toky dat mapování Parametrizace

Mapování toků dat v Azure Data Factory podporuje použití parametrů. Parametry můžete definovat uvnitř definice toku dat, kterou pak můžete použít v celém výrazu. Hodnoty parametrů lze nastavit pomocí kanálu volání prostřednictvím aktivity spustit tok dat. Máte tři možnosti, jak nastavit hodnoty ve výrazech aktivity toku dat:

* Nastavení dynamické hodnoty pomocí jazyka Expression Flow řízení kanálu
* Nastavení dynamické hodnoty pomocí jazyka výrazu data Flow
* Pro nastavení hodnoty statického literálu použijte buď jazyk výrazů.

Využijte tuto možnost k tomu, aby vaše datové toky byly pro obecné účely, flexibilní a opakovaně použitelné. Pomocí těchto parametrů můžete parametrizovat nastavení toku dat a výrazů.

> [!NOTE]
> Chcete-li používat výrazy toku řízení kanálu, musí být parametr toku dat typu String.

## <a name="create-parameters-in-a-mapping-data-flow"></a>Vytváření parametrů v toku dat mapování

Pokud chcete do toku dat přidat parametry, klikněte na prázdnou část plátna toku dat a zobrazte obecné vlastnosti. V podokně nastavení se zobrazí karta s názvem **parametr**. Vyberte **Nový** a vygenerujte nový parametr. Pro každý parametr musíte přiřadit název, vybrat typ a volitelně nastavit výchozí hodnotu.

![Vytvoření parametrů toku dat](media/data-flow/create-params.png "Vytvoření parametrů toku dat")

## <a name="use-parameters-in-a-mapping-data-flow"></a>Použití parametrů v toku dat mapování 

Na parametry se dá odkazovat v jakémkoli výrazu toku dat. Parametry začínají na $ a jsou neměnné. Seznam dostupných parametrů se nachází uvnitř Tvůrce výrazů na kartě **parametry** .

![Výraz parametru toku dat](media/data-flow/parameter-expression.png "Výraz parametru toku dat")

Další parametry můžete rychle přidat tak, že vyberete **Nový parametr** a zadáte název a typ.

![Výraz parametru toku dat](media/data-flow/new-parameter-expression.png "Výraz parametru toku dat")

### <a name="passing-in-a-column-name-as-a-parameter"></a>Předání názvu sloupce jako parametru

Běžným vzorem je předat název sloupce jako hodnotu parametru. Chcete-li odkazovat na sloupec přidružený k parametru, použijte funkci `byName()`. Nezapomeňte přetypovat sloupec na příslušný typ pomocí funkce přetypování, jako je například `toString()`.

Například pokud jste chtěli namapovat sloupec typu String na základě parametru `columnName`, můžete přidat transformaci odvozeného sloupce, která se rovná `toString(byName($columnName))`.

![Předání názvu sloupce jako parametru](media/data-flow/parameterize-column-name.png "Předání názvu sloupce jako paramete")

## <a name="assign-parameter-values-from-a-pipeline"></a>Přiřazení hodnot parametrů z kanálu

Jakmile vytvoříte tok dat s parametry, můžete ho spustit z kanálu s aktivitou spustit tok dat. Po přidání aktivity na plátno kanálu se zobrazí dostupné parametry toku dat na kartě **parametry** aktivity.

![Nastavení parametru toku dat](media/data-flow/parameter-assign.png "Nastavení parametru toku dat")

Pokud je datovým typem parametru řetězec, po kliknutí na textové pole pro nastavení hodnot parametrů se můžete rozhodnout, že zadáte buď kanál, nebo výraz toku dat. Pokud zvolíte možnost výraz kanálu, zobrazí se panel výrazu kanálu. Nezapomeňte zahrnout funkce kanálu do syntaxe řetězcové interpolace pomocí `'@{<expression>}'`, například:

```'@{pipeline().RunId}'```

Pokud váš parametr není typu řetězec, bude vždy zobrazen Tvůrce výrazů toku dat. Zde můžete zadat libovolný výraz nebo hodnoty literálu, které chcete, aby odpovídaly datovému typu parametru. Níže jsou uvedeny příklady výrazu toku dat a literálového řetězce v Tvůrci výrazů:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Každý tok dat mapování může obsahovat libovolnou kombinaci parametrů výrazu kanálu a toku dat. 

![Ukázka ukazatelů toku dat](media/data-flow/parameter-example.png "Ukázka ukazatelů toku dat")



## <a name="next-steps"></a>Další kroky
* [Spustit aktivitu toku dat](control-flow-execute-data-flow-activity.md)
* [Výrazy toku řízení](control-flow-expression-language-functions.md)
