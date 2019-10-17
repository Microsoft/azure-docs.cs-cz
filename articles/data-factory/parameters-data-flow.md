---
title: Azure Data Factory parametry toku dat mapování
description: Naučte se parametrizovat tok dat mapování z kanálů Data Factory.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: 0a1051d67bf45e96f82833ef8190008204cdc90b
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387541"
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

![Vytvoření parametrů toku dat](media/data-flow/create-params.png "Vytvoření parametrů toku dat")

Parametry lze využít v jakémkoli výrazu toku dat. Parametry začínají na $ a jsou neměnné. Seznam dostupných parametrů se nachází uvnitř Tvůrce výrazů na kartě Parameters (parametry).

![Výraz parametru toku dat](media/data-flow/parameter-expression.png "Výraz parametru toku dat")

## <a name="use-parameters-in-your-data-flow"></a>Použití parametrů v toku dat

* Hodnoty parametrů můžete použít ve výrazech transformace. Seznam parametrů najdete na kartě Parametry v Tvůrci výrazů. ![Použití parametrů toku dat](media/data-flow/params9.png "Use vstupními parametry toku dat)

* Parametry slouží také ke konfiguraci dynamických hodnot pro nastavení transformace zdroje a jímky. Když kliknete do části konfigurovatelné pole, zobrazí se odkaz Přidat dynamický contect. Kliknutím přejdete na Tvůrce výrazů, kde můžete použít parametry k použití dynamických hodnot. ![Dynamický obsah toku dat](media/data-flow/params6.png "Ddynamický obsah toku ATA ")

## <a name="set-mapping-data-flow-parameters-from-pipeline"></a>Nastavení parametrů toku dat mapování z kanálu

Jakmile vytvoříte tok dat s parametry, můžete ho spustit z kanálu s aktivitou spustit tok dat. Po přidání aktivity na plátno kanálu se zobrazí dostupné parametry toku dat na kartě Parametry aktivity.

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
