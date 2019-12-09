---
title: Existuje transformace v toku dat mapování.
description: Vyhledat existující řádky pomocí transformace Exists v Azure Data Factory toku dat mapování
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: efcc45dcf3565b70305323701810c49c4a720394
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930410"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>Existuje transformace v toku dat mapování.

Transformace EXISTS je transformace při filtrování řádků, která kontroluje, jestli data existují v jiném zdroji nebo streamu. Výstupní datový proud obsahuje všechny řádky v levém proudu, které buď existují, nebo neexistují ve správném datovém proudu. Transformace EXISTS je podobná ```SQL WHERE EXISTS``` a ```SQL WHERE NOT EXISTS```.

## <a name="configuration"></a>Konfigurace

1. V rozevíracím seznamu **správný datový proud** vyberte datový proud, který chcete zkontrolovat.
1. Určete, jestli chcete, aby tato data existovala nebo neexistovala v nastavení s **existujícím typem** .
1. Vyberte, jestli chcete **vlastní výraz**.
1. Vyberte klíčové sloupce, které chcete porovnat, protože existují podmínky. Ve výchozím nastavení tok dat hledá rovnost mezi jedním sloupcem v každém datovém proudu. Pro porovnání přes vypočítanou hodnotu umístěte ukazatel myši na rozevírací seznam sloupec a vyberte **vypočítaný sloupec**.

![Existuje nastavení](media/data-flow/exists.png "Existuje 1")

### <a name="multiple-exists-conditions"></a>Existuje několik podmínek.

Pokud chcete porovnat více sloupců z každého datového proudu, přidejte podmínku New Exists kliknutím na ikonu se symbolem plus vedle existujícího řádku. Každá další podmínka je připojena příkazem "a". Porovnání dvou sloupců je stejné jako u následujícího výrazu:

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>Vlastní výraz

Chcete-li vytvořit výraz ve volném formátu, který obsahuje operátory jiné než a a Equals, vyberte pole **vlastní výraz** . Kliknutím na modrý rámeček zadejte vlastní výraz pomocí Tvůrce výrazů toku dat.

![Existuje vlastní nastavení](media/data-flow/exists1.png "existuje vlastní")

## <a name="data-flow-script"></a>Skript toku dat

### <a name="syntax"></a>Syntaxe

```
<leftStream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: { true | false },
        broadcast: {'none' | 'left' | 'right' | 'both'}
    ) ~> <existsTransformationName>
```

### <a name="example"></a>Příklad:

Níže uvedený příklad je transformace s názvem `checkForChanges`, která přebírá levý Stream `NameNorm2` a `TypeConversions`pravého streamu.  Podmínka EXISTS je výraz `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region`, který vrací hodnotu true, pokud se ve sloupcích `EMPID` a `Region` v každém datovém proudu shodují. Po kontrole existence `negate` je false. Na kartě optimalizace nepovolujeme žádné vysílání, takže `broadcast` má `'none'`Value.

V uživatelském prostředí Data Factory Tato transformace vypadá jako na následujícím obrázku:

![Existuje příklad](media/data-flow/exists-script.png "Existuje příklad")

Skript toku dat pro tuto transformaci je v následujícím fragmentu kódu:

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'none'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>Další kroky

Podobné transformace jsou [Lookup](data-flow-lookup.md) a [Join](data-flow-join.md).
