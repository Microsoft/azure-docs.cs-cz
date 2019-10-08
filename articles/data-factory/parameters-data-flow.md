---
title: Azure Data Factory parametry toku dat mapování
description: Naučte se parametrizovat tok dat mapování z kanálů Data Factory.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 733d3f9c4079193107f22178bdbde3a3ecf0e7ca
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72028226"
---
# <a name="mapping-data-flow-parameters"></a>Mapování parametrů toku dat



Mapování toků dat v Azure Data Factory podporuje použití parametrů. Parametry můžete definovat uvnitř definice toku dat, kterou pak můžete použít v celém výrazu. Hodnoty parametrů lze nastavit pomocí kanálu volání prostřednictvím aktivity spustit tok dat. Máte tři možnosti, jak nastavit hodnoty ve výrazech aktivity toku dat:

* Nastavení dynamické hodnoty pomocí jazyka Expression Flow řízení kanálu
* Nastavení dynamické hodnoty pomocí jazyka výrazu data Flow
* Pro nastavení hodnoty statického literálu použijte buď jazyk výrazů.

Využijte tuto možnost k tomu, aby vaše datové toky byly pro obecné účely, flexibilní a opakovaně použitelné. Pomocí těchto parametrů můžete parametrizovat nastavení toku dat a výrazů.

> [!NOTE]
> Chcete-li používat výrazy toku řízení kanálu, musí být parametr toku dat typu String.

## <a name="create-parameters-in-mapping-data-flow"></a>Vytváření parametrů v toku dat mapování

Pokud chcete do toku dat přidat parametry, klikněte na prázdnou část plátna toku dat a zobrazte obecné vlastnosti. V podokně nastavení se zobrazí karta s názvem Parameters. Kliknutím na tlačítko Nový vygenerujete nový parametr. Pro každý parametr musíte přiřadit název, vybrat typ a volitelně nastavit výchozí hodnotu.

![Vytvoření parametrů toku dat](media/data-flow/create-params.png "vytvoření parametrů toku dat")

Parametry lze využít v jakémkoli výrazu toku dat. Parametry začínají na $ a jsou neměnné. Seznam dostupných parametrů se nachází uvnitř Tvůrce výrazů na kartě Parameters (parametry).

(media/data-flow/parameter-expression.png "Výraz parametru toku dat") ![výrazu datového toku dat]

## <a name="use-parameters-in-your-data-flow"></a>Použití parametrů v toku dat

* Hodnoty parametrů můžete použít ve výrazech transformace. Seznam parametrů najdete na kartě Parametry v Tvůrci výrazů. ![Použití parametrů toku dat](media/data-flow/params9.png "použití parametrů toku dat")

* Parametry slouží také ke konfiguraci dynamických hodnot pro nastavení transformace zdroje a jímky. Když kliknete do části konfigurovatelné pole, zobrazí se odkaz Přidat dynamický contect. Kliknutím přejdete na Tvůrce výrazů, kde můžete použít parametry k použití dynamických hodnot. (media/data-flow/params6.png "Dynamický obsah toku dat") ![dynamického obsahu toku dat]

## <a name="set-mapping-data-flow-parameters-from-pipeline"></a>Nastavení parametrů toku dat mapování z kanálu

Jakmile vytvoříte tok dat s parametry, můžete ho spustit z kanálu s aktivitou spustit tok dat. Po přidání aktivity na plátno kanálu se zobrazí dostupné parametry toku dat na kartě Parametry aktivity.

![Nastavení parametru toku dat](media/data-flow/parameter-assign.png "pro parametr toku dat")

Pokud je datovým typem parametru řetězec, po kliknutí na textové pole pro nastavení hodnot parametrů se můžete rozhodnout, že zadáte buď kanál, nebo výraz toku dat. Pokud zvolíte možnost výraz kanálu, zobrazí se panel výrazu kanálu. Nezapomeňte zahrnout funkce kanálu do syntaxe řetězcové interpolace pomocí `'@{<expression>}'`, například:

```'@{pipeline().RunId}'```

Pokud váš parametr není typu řetězec, bude vždy zobrazen Tvůrce výrazů toku dat. Zde můžete zadat libovolný výraz nebo hodnoty literálu, které chcete, aby odpovídaly datovému typu parametru. Níže jsou uvedeny příklady výrazu toku dat a literálového řetězce v Tvůrci výrazů:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Každý tok dat mapování může obsahovat libovolnou kombinaci parametrů výrazu kanálu a toku dat. 

Ukázka parametrů ![toku dat]ukázka(media/data-flow/parameter-example.png "datových toků")



## <a name="next-steps"></a>Další kroky
* [Spustit aktivitu toku dat](control-flow-execute-data-flow-activity.md)
* [Výrazy toku řízení](control-flow-expression-language-functions.md)
