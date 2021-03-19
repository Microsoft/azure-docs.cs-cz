---
title: Existuje transformace v toku dat mapování.
description: Vyhledat existující řádky pomocí transformace Exists v Azure Data Factory toku dat mapování
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/07/2020
ms.openlocfilehash: 805b51bf4e6d8feab9539f660dfc72ca78b82d5c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "82982628"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>Existuje transformace v toku dat mapování.

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Transformace EXISTS je transformace při filtrování řádků, která kontroluje, jestli data existují v jiném zdroji nebo streamu. Výstupní datový proud obsahuje všechny řádky v levém proudu, které buď existují, nebo neexistují ve správném datovém proudu. Transformace EXISTS je podobná ```SQL WHERE EXISTS``` a ```SQL WHERE NOT EXISTS``` .

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vZKz]

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

## <a name="broadcast-optimization"></a>Optimalizace všesměrového vysílání

![Připojení všesměrového vysílání](media/data-flow/broadcast.png "Připojení všesměrového vysílání")

V okně spojení, vyhledávání a existence transformace, pokud se jeden nebo oba datové proudy vejdou do paměti pracovního uzlu, můžete optimalizovat výkon tím, že povolíte **všesměrové vysílání**. Ve výchozím nastavení se modul Spark automaticky rozhodne, zda se má vysílání jedna strana vysílat. Chcete-li ručně zvolit, která strana se má vysílat, vyberte možnost **pevná**.

Nedoporučuje se zakázat všesměrové vysílání přes možnost **off** , pokud vaše spojení neběží v případě chyb časového limitu.

## <a name="data-flow-script"></a>Skript toku dat

### <a name="syntax"></a>Syntax

```
<leftStream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: { true | false },
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <existsTransformationName>
```

### <a name="example"></a>Příklad

Níže uvedený příklad je transformace s názvem `checkForChanges` , která přebírá levý Stream `NameNorm2` a pravý Stream `TypeConversions` .  Podmínka EXISTS je výraz `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region` , který vrací hodnotu true, pokud se `EMPID` `Region` shodují sloupce a v každém datovém proudu. Při kontrole existence `negate` je NEPRAVDA. Na kartě optimalizace nepovolujeme žádné vysílání, takže `broadcast` má hodnotu `'none'` .

V uživatelském prostředí Data Factory Tato transformace vypadá jako na následujícím obrázku:

![Existuje příklad](media/data-flow/exists-script.png "Existuje příklad")

Skript toku dat pro tuto transformaci je v následujícím fragmentu kódu:

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'auto'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>Další kroky

Podobné transformace jsou [Lookup](data-flow-lookup.md) a [Join](data-flow-join.md).
