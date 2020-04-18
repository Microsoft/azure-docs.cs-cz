---
title: Existuje transformace v toku dat mapování
description: Kontrola existujících řádků pomocí transformace exists v toku dat mapování Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: a303c8fa1e23460fb906232eedb6bfb1930b4bc9
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606466"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>Existuje transformace v toku dat mapování

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Existuje transformace je řádek filtrování transformace, která kontroluje, zda vaše data existuje v jiném zdroji nebo datového proudu. Výstupní datový proud obsahuje všechny řádky v levém datovém proudu, které existují nebo neexistují v pravém datovém proudu. Existující transformace je podobná ```SQL WHERE EXISTS``` ```SQL WHERE NOT EXISTS```a .

## <a name="configuration"></a>Konfigurace

1. V rozevíracím souboru Pravý datový **proud** zvolte, který datový proud kontrolujete.
1. Určete, zda hledáte data, která mají existovat nebo neexistují, v nastavení **Exist type.**
1. Vyberte, zda chcete **vlastní výraz**.
1. Zvolte klíčové sloupce, které chcete porovnat, protože existují podmínky. Ve výchozím nastavení tok dat hledá rovnost mezi jedním sloupcem v každém datovém proudu. Chcete-li porovnat pomocí vypočítané hodnoty, najeďte na rozbalovací seznam sloupců a vyberte **Vypočítaný sloupec**.

![Existuje nastavení](media/data-flow/exists.png "existuje 1")

### <a name="multiple-exists-conditions"></a>Více existuje podmínky

Chcete-li porovnat více sloupců z každého datového proudu, přidejte novou podmínku kliknutím na ikonu plus vedle existujícího řádku. Každá další podmínka je spojena příkazem "a". Porovnání dvou sloupců je stejné jako následující výraz:

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>Vlastní výraz

Chcete-li vytvořit výraz volného tvaru, který obsahuje jiné operátory než "a" a "rovná se", vyberte pole **Vlastní výraz.** Zadejte vlastní výraz prostřednictvím tvůrce výrazů toku dat kliknutím na modré pole.

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

### <a name="example"></a>Příklad

Níže uvedený příklad je existuje `checkForChanges` transformace s `NameNorm2` názvem, `TypeConversions`který trvá levý proud a pravý proud .  Podmínka exists je `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region` výraz, který vrátí `EMPID` `Region` hodnotu true, pokud se shodují sloupce v každém datovém proudu. Jak jsme se snaží o `negate` existenci, je falešný. Nejsme povolení žádné vysílání v optimalizaci `broadcast` kartu, takže má hodnotu `'none'`.

V ux datové továrny tato transformace vypadá jako následující obrázek:

![Existuje příklad](media/data-flow/exists-script.png "Existuje příklad")

Skript toku dat pro tuto transformaci je ve fragmentu níže:

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'none'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>Další kroky

Podobné transformace jsou [Vyhledávání](data-flow-lookup.md) a [Připojit .](data-flow-join.md)
