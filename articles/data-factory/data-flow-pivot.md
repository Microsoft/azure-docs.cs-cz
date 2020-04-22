---
title: Transformace kontingenčního stavu v toku dat mapování
description: Kontingenční data z řádků do sloupců pomocí transformace pivotu toku toku dat mapování datové toku Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: a58444f81f60b48f9c2c76f13257a6a2431158a8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686432"
---
# <a name="pivot-transformation-in-mapping-data-flow"></a>Transformace kontingenčního stavu v toku dat mapování


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Pomocí transformace kontingenčního čepu můžete vytvořit více sloupců z jedinečných hodnot řádků jednoho sloupce. Pivot je transformace agregace, ve které vybíráte seskupit podle sloupců a generujete sloupce kontingenčních sloupců pomocí [agregačních funkcí](data-flow-expression-functions.md#aggregate-functions).

## <a name="configuration"></a>Konfigurace

Transformace pivotu vyžaduje tři různé vstupy: seskupit podle sloupců, kontingenční klávesu a způsob generování otočných sloupců.

### <a name="group-by"></a>Seskupit podle

![Možnosti pro seskupení](media/data-flow/pivot2.png "[Seskupit podle možností")

Vyberte sloupce, přes které chcete agregovat otočené sloupce. Výstupní data seskupí všechny řádky se stejnou skupinou podle hodnot do jednoho řádku. Agregace provedená v otočném sloupci bude probíhat v každé skupině.

Tato část je nepovinná. Pokud není vybrána žádná skupina podle sloupců, bude agregován celý datový proud a bude výstupem pouze jeden řádek.

### <a name="pivot-key"></a>Kontingenční klávesa

![Kontingenční klávesa](media/data-flow/pivot3.png "Kontingenční klávesa")

Kontingenční klávesa je sloupec, jehož hodnoty řádků se převedou do nových sloupců. Ve výchozím nastavení transformace kontingenčního čepu vytvoří nový sloupec pro každou jedinečnou hodnotu řádku.

V oddílu s názvem **Hodnota**můžete zadat konkrétní hodnoty řádků, které mají být otočné. Budou otočte pouze hodnoty řádků zadané v této části. Povolením **hodnoty Null** se vytvoří otočný sloupec pro hodnoty null ve sloupci.

### <a name="pivoted-columns"></a>Otočné sloupce

![Otočné sloupce](media/data-flow/pivot4.png "Otočné sloupce")

Pro každou jedinečnou hodnotu kontingenčního klíče, která se stane sloupcem, vygenerujte agregovanou hodnotu řádku pro každou skupinu. Na kontingenční klávesu můžete vytvořit více sloupců. Každý kontingenční sloupec musí obsahovat alespoň [jednu agregační funkci](data-flow-expression-functions.md#aggregate-functions).

**Vzor názvu sloupce:** Vyberte způsob formátování názvu sloupce každého kontingenčního sloupce. Výstupní název sloupce bude kombinací hodnoty pivotklíče, předpony sloupce a volitelné předpony, stačí, střední znaky. 

**Uspořádání kolony:** Pokud generujete více než jeden kontingenční sloupec na kontingenční klávesu, zvolte, jak chcete sloupce seřazené. 

**Předpona sloupce:** Pokud generujete více než jeden kontingenční sloupec na kontingenční klávesu, zadejte předponu sloupce pro každý sloupec. Toto nastavení je volitelné, pokud máte pouze jeden otočný sloupec.

## <a name="help-graphic"></a>Obrázek nápovědy

Níže uvedená nápověda ukazuje, jak různé kontingenční komponenty vzájemně spolupracují

![Grafika nápovědy k převisu](media/data-flow/pivot5.png "Grafika nápovědy k přetisku")

## <a name="pivot-metadata"></a>Kontingenční metadata

Pokud v konfiguraci kontingenčního klíče nejsou zadány žádné hodnoty, budou otočné sloupce dynamicky generovány za běhu. Počet otočných sloupců se bude rovnat počtu jedinečných hodnot kontingenčního klíče vynásobenému počtem kontingenčních sloupců. Vzhledem k tomu, že se může jednalo o měnící se číslo, uživatelské číslo nezobrazí metadata sloupce na kartě **Zkontrolovat** a nebude žádné šíření sloupců. Chcete-li tyto sloupce transformovat, použijte možnosti mapování toku dat [ve vzorku.](concepts-data-flow-column-pattern.md) 

Pokud jsou nastaveny určité hodnoty kontingenčního klíče, zobrazí se v souboru metadat.e Názvy sloupců budou k dispozici v mapování Zkontrolovat a Jímce.

### <a name="generate-metadata-from-drifted-columns"></a>Generovat metadata z posunutých sloupců

Pivot generuje nové názvy sloupců dynamicky na základě hodnot řádků. Tyto nové sloupce můžete přidat do metadat, na která lze odkazovat později v toku dat. Chcete-li to provést, použijte [mapu driftované](concepts-data-flow-schema-drift.md#map-drifted-columns-quick-action) rychlou akci v náhledu dat. 

![Kontingenční sloupce](media/data-flow/newpivot1.png "Posunuté sloupce kontingenčního pole mapy")

### <a name="sinking-pivoted-columns"></a>Potopení otočných sloupců

Přestože jsou otočné sloupce dynamické, lze je stále zapsat do cílového úložiště dat. Povolit **povolit posun schématu** v nastavení jímky. To vám umožní psát sloupce, které nejsou zahrnuty v metadatech. metadat sloupců, ale možnost posunu schématu vám umožní data přistát.

### <a name="rejoin-original-fields"></a>Opětovné připojení původních polí

Transformace pivotu promítne pouze skupinu podle a otočných sloupců. Pokud chcete, aby výstupní data obsahovala další vstupní sloupce, použijte vzor [vlastního spojení.](data-flow-join.md#self-join)

## <a name="data-flow-script"></a>Skript toku dat

### <a name="syntax"></a>Syntaxe

```
<incomingStreamName>
    pivot(groupBy(Tm),
        pivotBy(<pivotKeyColumn, [<specifiedColumnName1>,...,<specifiedColumnNameN>]),
        <pivotColumnPrefix> = <pivotedColumnValue>,
        columnNaming: '< prefix >< $N | $V ><middle >< $N | $V >< suffix >',
        lateral: { 'true' | 'false'}
    ) ~> <pivotTransformationName
```
### <a name="example"></a>Příklad

Obrazovky zobrazené v konfigurační části mají následující skript toku dat:

```
BasketballPlayerStats pivot(groupBy(Tm),
    pivotBy(Pos),
    {} = count(),
    columnNaming: '$V$N count',
    lateral: true) ~> PivotExample

```

## <a name="next-steps"></a>Další kroky

Zkuste [přeměnu unpivot](data-flow-unpivot.md) uzasete hodnoty sloupců na hodnoty řádků. 
