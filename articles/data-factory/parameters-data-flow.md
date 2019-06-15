---
title: Azure Data Factory mapování parametry toku dat
description: Zjistěte, jak parametrizovat mapování toku dat z kanálů data factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/10/2019
ms.openlocfilehash: af5f421cc3802f3a7ad44bb294f5066c32569f8b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082882"
---
# <a name="mapping-data-flow-parameters"></a>Mapování parametrů toku dat

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Mapování dat toky ve službě data factory podporují použití parametrů. Parametry můžete definovat uvnitř definice toku vaše data, které pak můžete použít v celé vaší výrazy. Volání kanál pomocí aktivity spustit tok dat můžete nastavit parametry. Máte tři možnosti, jak použít k nastavení hodnoty v toku dat aktivity výrazů:

* Použijte jazyk výrazů kanálu řízení toku pro nastavení dynamické hodnoty
* Použijte jazyk výrazů toku dat pro nastavení dynamické hodnoty
* Nastavení statické literálovou hodnotou pomocí jazyka pro výraz

Tuto funkci používejte, aby vaše toky dat pro obecné účely, flexibilní a opakovaně použitelné. Nastavení toku dat a výrazy s použitím těchto parametrů můžete parametrizovat.

> [!NOTE]
> Použití kanálu řízení toku výrazů, parametr toku dat musí být typu String.

* Přidáte aktivitu spuštění toku dat na plátno kanálu.
* Pokud váš tok dat obsahuje parametry, zobrazí se seznam dostupných parametrů ve parametry tab.* *, klikněte na textové pole vedle každého parametru zadat hodnotu parametru.
* Můžete vytvořit výraz parametru prostřednictvím kanálu řízení toku výraz jazyka nebo datového toku výrazů.

![Tok dat parametry 3](media/data-flow/params3.png "3 parametry toku dat")

## <a name="create-parameters-in-data-flow"></a>Parametry vytvoření v toku dat

![Tok dat parametry 1](media/data-flow/params1.png "parametry 1 toku dat")

Chcete-li přidat parametry do vašeho toku dat, klikněte na prázdné místo plátna tok dat můžete zobrazit obecné vlastnosti. V podokně nastavení se zobrazí na kartě označované jako parametry. Klikněte na tlačítko Generovat nové parametry, které lze nastavit z kanálu předávání hodnot do vašeho toku dat. Zadejte název parametru a vyberte datový typ pro každý parametr.

Ve výrazech toku dat můžete využít parametry pomocí hodnoty nastavené z kanálu. Parametry začínají znakem $ a jsou neměnné. Zjistíte také seznam dostupných parametry uvnitř Tvůrce na kartě Parametry. Tyto hodnoty můžete použít ve výrazech, i když nemusí přiřazení nové hodnoty pro parametry.

![Tok dat parametry 2](media/data-flow/params2.png "parametry 2 toku dat")

## <a name="set-data-flow-parameters-from-pipeline"></a>Nastavit parametry toku dat z kanálu

Po vytvoření toku dat s parametry, teď můžete spustit tento tok dat z kanálu s aktivitou spuštění toku dat. Po přidání aktivity na plátno kanálu návrhu zobrazí s dostupnými daty parametry toku v nastavení karty parametry aktivity.

![Jazyk výrazů parametrů se předávají data](media/data-flow/params4.png "jazyk výrazů parametry toku dat")

Po kliknutí na textové pole pro vyplnění hodnot parametrů, zobrazí se Data Flow Tvůrce. Tady můžete zadat libovolný výraz nebo hodnoty literálu, který chcete odpovídající datový typ parametru. Níže jsou příklady výraz datového toku a řetězcový literál z Tvůrce výrazů:

* ```toInteger(Role)```
* ```'this is my static literal string'```

Pokud datový typ parametru je řetězec, je možné zadat kanálu nebo datového toku výrazu. Pokud se rozhodnete výraz kanálu, místo toho zobrazí se panel výrazu kanálu. Nezapomeňte zahrnout funkce kanálu uvnitř pomocí syntaxe interpolace řetězce: @{<expression>} ", například:

```'@{pipeline().RunId}'```

![Ukázková data pro parametry toku](media/data-flow/params5.png "vzorek parametry toku dat")

## <a name="next-steps"></a>Další postup

* [Spuštění aktivity toku dat](control-flow-execute-data-flow-activity.md)
* [Řízení toku výrazů](control-flow-expression-language-functions.md)
