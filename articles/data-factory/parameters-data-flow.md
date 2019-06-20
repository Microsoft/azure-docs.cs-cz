---
title: Mapování parametrů toku dat pro vytváření dat Azure
description: Zjistěte, jak parametrizovat mapování toku dat z kanálů data factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: ef97f17bf159511ce94f90cd00623e05489acb92
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274747"
---
# <a name="mapping-data-flow-parameters"></a>Mapování parametrů toku dat

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Mapování toků dat ve službě Azure Data Factory podporují použití parametrů. Parametry můžete definovat uvnitř definice toku vaše data, které pak můžete použít v celé vaší výrazy. Volání kanál pomocí aktivity spustit tok dat lze nastavit hodnoty parametrů. Existují tři možnosti pro nastavení hodnot v toku dat aktivity výrazů:

* Použijte jazyk výrazů kanálu řízení toku pro nastavení dynamické hodnoty
* Použijte jazyk výrazů toku dat pro nastavení dynamické hodnoty
* Nastavení statické literálovou hodnotou pomocí jazyka pro výraz

Tuto funkci používejte, aby vaše toky dat pro obecné účely, flexibilní a opakovaně použitelné. Nastavení toku dat a výrazy s použitím těchto parametrů můžete parametrizovat.

> [!NOTE]
> Použití kanálu řízení toku výrazů, parametr toku dat musí být typu String.

## <a name="create-parameters-in-mapping-data-flow"></a>Parametry vytvoření v mapování toku dat

Chcete-li přidat parametry do vašeho toku dat, klikněte na prázdné místo plátna tok dat můžete zobrazit obecné vlastnosti. V podokně nastavení se zobrazí na kartě označované jako "Parametry". Kliknutím na tlačítko 'New' generovat nový parametr. Pro každý parametr musíte přiřadit název, vyberte typ a volitelně nastavit výchozí hodnotu.

![Vytvoření toku dat parametry](media/data-flow/create-params.png "parametry vytvoření toku dat")

Parametry lze využít v jakékoli výraz datového toku. Parametry začínají znakem $ a jsou neměnné. Najdete seznam dostupných parametrů v rámci Tvůrce na kartě "Parametrů".

![Výraz parametru toku dat](media/data-flow/parameter-expression.png "výraz parametru toku dat")

## <a name="set-mapping-data-flow-parameters-from-pipeline"></a>Nastavit parametry mapování toku dat z kanálu

Po vytvoření toku dat s parametry, můžete je spustit z kanálu s aktivitou spuštění toku dat. Po přidání aktivity na plátno kanálu zobrazí s dostupnými daty parametry toku na kartě "Parametrů" aktivity.

![Nastavení parametrů se předávají Data](media/data-flow/parameter-assign.png "nastavení parametru toku dat")

Pokud datový typ parametru je řetězec, po kliknutí na textové pole nastavit hodnoty parametrů, můžete zadat kanálu nebo datového toku výrazu. Pokud se rozhodnete výraz kanál, zobrazí se panel výrazu kanálu. Nezapomeňte zahrnout funkce kanálu uvnitř pomocí syntaxe interpolace řetězce: @{<expression>} ", například:

```'@{pipeline().RunId}'```

Pokud parametr není typu String, vždy zobrazí Tvůrce výrazů datový tok. Tady můžete zadat libovolný výraz nebo hodnoty literálu, který chcete odpovídající datový typ parametru. Níže jsou příklady výraz datového toku a řetězcový literál z Tvůrce výrazů:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Každý datový tok mapování může mít libovolnou kombinaci kanál a datový výraz parametry toku. 

![Ukázková data pro parametry toku](media/data-flow/parameter-example.png "vzorek parametry toku dat")



## <a name="next-steps"></a>Další postup
* [Spuštění aktivity toku dat](control-flow-execute-data-flow-activity.md)
* [Řízení toku výrazů](control-flow-expression-language-functions.md)
