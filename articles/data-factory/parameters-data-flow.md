---
title: Parametrizace mapování toků dat
description: Naučte se parametrizovat tok dat mapování z kanálů Data Factory.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/19/2021
ms.openlocfilehash: 22c4fc0680d8666d8c2dfafb8829436e27cf1ebd
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725705"
---
# <a name="parameterizing-mapping-data-flows"></a>Parametrizace mapování toků dat

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Mapování toků dat v Azure Data Factory a Azure synapse Analytics podporuje použití parametrů. Definujte parametry uvnitř definice toku dat a používejte je v rámci svých výrazů. Hodnoty parametrů jsou nastaveny prostřednictvím volajícího kanálu prostřednictvím aktivity spustit tok dat. Máte tři možnosti, jak nastavit hodnoty ve výrazech aktivity toku dat:

* Nastavení dynamické hodnoty pomocí jazyka Expression Flow řízení kanálu
* Nastavení dynamické hodnoty pomocí jazyka výrazu data Flow
* Pro nastavení hodnoty statického literálu použijte buď jazyk výrazů.

Využijte tuto možnost k tomu, aby vaše datové toky byly pro obecné účely, flexibilní a opakovaně použitelné. Pomocí těchto parametrů můžete parametrizovat nastavení toku dat a výrazů.

## <a name="create-parameters-in-a-mapping-data-flow"></a>Vytváření parametrů v toku dat mapování

Pokud chcete do toku dat přidat parametry, klikněte na prázdnou část plátna toku dat a zobrazte obecné vlastnosti. V podokně nastavení se zobrazí karta s názvem **parametr**. Vyberte **Nový** a vygenerujte nový parametr. Pro každý parametr musíte přiřadit název, vybrat typ a volitelně nastavit výchozí hodnotu.

![Vytvoření parametrů toku dat](media/data-flow/create-params.png "Vytvoření parametrů toku dat")

## <a name="use-parameters-in-a-mapping-data-flow"></a>Použití parametrů v toku dat mapování 

Na parametry se dá odkazovat v jakémkoli výrazu toku dat. Parametry začínají na $ a jsou neměnné. Seznam dostupných parametrů se nachází uvnitř Tvůrce výrazů na kartě **parametry** .

![Snímek obrazovky ukazuje dostupné parametry na kartě Parameters (parametry).](media/data-flow/parameter-expression.png "Výraz parametru toku dat")

Další parametry můžete rychle přidat tak, že vyberete **Nový parametr** a zadáte název a typ.

![Snímek obrazovky ukazuje parametry na kartě parametry s přidanými novými parametry.](media/data-flow/new-parameter-expression.png "Výraz parametru toku dat")

## <a name="assign-parameter-values-from-a-pipeline"></a>Přiřazení hodnot parametrů z kanálu

Jakmile vytvoříte tok dat s parametry, můžete ho spustit z kanálu s aktivitou spustit tok dat. Po přidání aktivity na plátno kanálu se zobrazí dostupné parametry toku dat na kartě **parametry** aktivity.

Při přiřazování hodnot parametrů můžete použít jazyk [výrazu kanálu](control-flow-expression-language-functions.md) nebo [Jazyk výrazu datového toku](data-flow-expression-functions.md) , který je založený na typech Spark. Každý tok dat mapování může obsahovat libovolnou kombinaci parametrů výrazu kanálu a toku dat.

![Snímek obrazovky zobrazuje kartu parametry s výrazem toku dat vybraným pro hodnotu myParam.](media/data-flow/parameter-assign.png "Nastavení parametru toku dat")

### <a name="pipeline-expression-parameters"></a>Parametry výrazu kanálu

Parametry výrazu kanálu umožňují odkazovat systémové proměnné, funkce, parametry kanálu a proměnné podobně jako jiné aktivity kanálu. Když kliknete na **kanál – výraz**, otevře se vedlejší navigace, která vám umožní zadat výraz pomocí Tvůrce výrazů.

![Snímek obrazovky se zobrazí v podokně Tvůrce výrazů.](media/data-flow/parameter-pipeline.png "Nastavení parametru toku dat")

V případě, že se na něj odkazuje, vyhodnocují se parametry kanálu a pak se jejich hodnota používá v jazyce výrazu toku dat. Typ výrazu kanálu nemusí odpovídat typu parametru toku dat. 

#### <a name="string-literals-vs-expressions"></a>Řetězcové literály vs – výrazy

Při přiřazování parametru výrazu kanálu typu String budou přidány výchozí uvozovky a hodnota bude vyhodnocena jako literál. Chcete-li načíst hodnotu parametru jako výraz toku dat, zaškrtněte políčko výrazu vedle parametru.

![Snímek obrazovky ukazuje výraz podokna parametry toku dat vybraný pro parametr.](media/data-flow/string-parameter.png "Nastavení parametru toku dat")

Pokud parametr toku dat `stringParam` odkazuje na parametr kanálu s hodnotou `upper(column1)` . 

- Pokud je výraz zaškrtnutý, `$stringParam` vyhodnotí se hodnota Sloupec1 vše velkými písmeny.
- Pokud výraz není zaškrtnuto (výchozí chování),  `$stringParam` vyhodnotí se `'upper(column1)'`

#### <a name="passing-in-timestamps"></a>Předávání do časových razítek

V jazyce výrazu kanálu jsou systémové proměnné, jako například `pipeline().TriggerTime` , a funkce jako `utcNow()` návratová časová razítka ve formátu yyyy-MM-DD \' T \' HH: mm: ss. SSSSSSZ'. Chcete-li je převést na parametry toku dat typu timestamp, použijte interpolaci řetězce k zahrnutí požadovaného časového razítka do `toTimestamp()` funkce. Chcete-li například převést dobu triggeru kanálu na parametr toku dat, můžete použít `toTimestamp(left('@{pipeline().TriggerTime}', 23), 'yyyy-MM-dd\'T\'HH:mm:ss.SSS')` . 

![Snímek obrazovky se zobrazí na kartě parametry, kde můžete zadat dobu triggeru.](media/data-flow/parameter-timestamp.png "Nastavení parametru toku dat")

> [!NOTE]
> Toky dat můžou podporovat jenom až 3 milisekundové číslice. `left()`Funkce se používá pro oříznutí dalších číslic.

#### <a name="pipeline-parameter-example"></a>Příklad parametru kanálu

Řekněme, že máte parametr typu Integer, `intParam` který odkazuje na parametr kanálu typu String, `@pipeline.parameters.pipelineParam` . 

![Snímek obrazovky se zobrazí na kartě parametry s parametry s názvem stringParam a intParam.](media/data-flow/parameter-pipeline-2.png "Nastavení parametru toku dat")

`@pipeline.parameters.pipelineParam` je přiřazena hodnota za `abs(1)` běhu.

![Snímek obrazovky zobrazuje kartu parametry s hodnotou a b s (1) vybranou.](media/data-flow/parameter-pipeline-4.png "Nastavení parametru toku dat")

Pokud `$intParam` je odkazováno ve výrazu, jako je například odvozený sloupec, vyhodnotí se `abs(1)` vrátí zpět `1` . 

![Snímek obrazovky zobrazuje hodnotu sloupců.](media/data-flow/parameter-pipeline-3.png "Nastavení parametru toku dat")

### <a name="data-flow-expression-parameters"></a>Parametry výrazu toku dat

Vyberete-li **výraz tok dat** , otevře se Tvůrce výrazů toku dat. V rámci toku dat budete moci odkazovat na funkce, jiné parametry a jakýkoli definovaný sloupec schématu. Tento výraz bude vyhodnocen tak, jak je odkazováno.

> [!NOTE]
> Pokud předáte neplatný výraz nebo odkazujete na sloupec schématu, který v této transformaci neexistuje, bude parametr vyhodnocen jako null.


### <a name="passing-in-a-column-name-as-a-parameter"></a>Předání názvu sloupce jako parametru

Běžným vzorem je předat název sloupce jako hodnotu parametru. Pokud je sloupec definován ve schématu toku dat, můžete na něj odkazovat přímo jako řetězcový výraz. Pokud sloupec není definován ve schématu, použijte `byName()` funkci. Nezapomeňte přetypovat sloupec na příslušný typ pomocí funkce přetypování, jako je například `toString()` .

Například pokud jste chtěli namapovat sloupec řetězců na základě parametru `columnName` , můžete přidat transformaci odvozeného sloupce Equal `toString(byName($columnName))` .

![Předání názvu sloupce jako parametru](media/data-flow/parameterize-column-name.png "Předání názvu sloupce jako parametru")

## <a name="next-steps"></a>Další kroky
* [Spustit aktivitu toku dat](control-flow-execute-data-flow-activity.md)
* [Výrazy toku řízení](control-flow-expression-language-functions.md)
