---
title: Transformace spojení v Azure Data Factory toku dat mapování
description: Kombinování dat ze dvou zdrojů dat pomocí transformace spojení v Azure Data Factory toku dat mapování
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 1e9315195ceae435447739055105a66ee81e2a6a
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122920"
---
# <a name="join-transformation-in-mapping-data-flow"></a>Transformace spojení v toku dat mapování

Použijte transformaci JOIN ke kombinování dat ze dvou zdrojů nebo datových proudů v toku dat mapování. Výstupní datový proud bude obsahovat všechny sloupce z obou zdrojů odpovídajících v závislosti na podmínce spojení. 

## <a name="join-types"></a>Typy spojení

Mapování toků dat aktuálně podporuje pět různých typů spojení.

### <a name="inner-join"></a>Vnitřní spojení

Vnitřní spojení pouze zapisuje pouze řádky, které mají odpovídající hodnoty v obou tabulkách.

### <a name="left-outer"></a>Levé vnější

Levé vnější spojení vrátí všechny řádky z levého proudu a odpovídající záznamy z pravého datového proudu. Pokud řádek z levého datového proudu neodpovídá, výstupní sloupce z pravého datového proudu jsou nastaveny na hodnotu NULL. Výstupem budou řádky vrácené vnitřním spojením a nespárované řádky z levého streamu.

### <a name="right-outer"></a>Pravé vnější

Pravé vnější spojení vrátí všechny řádky z pravého proudu a odpovídajících záznamů z levého datového proudu. Pokud řádek ze pravého datového proudu neodpovídá, výstupní sloupce z levého datového proudu jsou nastaveny na hodnotu NULL. Výstupem budou řádky vrácené vnitřním spojením a nespárované řádky z pravého datového proudu.

### <a name="full-outer"></a>Úplné vnější

Úplné vnější spojení vyprodukuje všechny sloupce a řádky z obou stran s hodnotami NULL pro sloupce, které se neshodují.

### <a name="cross-join"></a>Vzájemné spojení

V případě vzájemného spojení se v závislosti na podmínce vytvoří výstup z obou datových proudů mezi různými produkty. Pokud používáte podmínku, která není rovnost, zadejte jako podmínku vzájemného spojení vlastní výraz. Výstupní datový proud bude obsahovat všechny řádky, které splňují podmínku spojení. Pokud chcete vytvořit kartézském produkt, který vypíše všechny kombinace řádků, zadejte jako podmínku JOIN `true()`.

## <a name="configuration"></a>Konfigurace

1. Vyberte datový proud, se kterým se připojujete, v rozevíracím seznamu **správný datový proud** .
1. Vyberte **typ spojení**
1. Vyberte, na které klíčové sloupce se má shoda při spojování podmínky připojit. Ve výchozím nastavení tok dat hledá rovnost mezi jedním sloupcem v každém datovém proudu. Pro porovnání přes vypočítanou hodnotu umístěte ukazatel myši na rozevírací seznam sloupec a vyberte **vypočítaný sloupec**.

![Transformace spojení](media/data-flow/join.png "Spojit")

## <a name="optimizing-join-performance"></a>Optimalizace výkonu připojení

Na rozdíl od sloučení slučovacích nástrojů v nástrojích, jako je SSIS, transformace spojení není povinná operace sloučení spojení. Klávesy JOIN nevyžadují řazení. Operace join probíhá na základě optimální operace join ve Sparku, ať už při vysílání, nebo při připojení na straně mapy.

![Optimalizace transformace JOIN](media/data-flow/joinoptimize.png "Spojit optimalizaci")

Pokud se jeden nebo oba datové proudy vejdou do paměti pracovního uzlu, Optimalizujte svůj výkon tím, že na kartě optimalizace povolíte **všesměrové vysílání** . Můžete také přerozdělit data na operaci JOIN tak, aby vyhovovala lepší paměti na pracovní proces.

## <a name="self-join"></a>Připojovat se k sobě

Pokud chcete datový proud sám propojit se sebou samým, vytvořte alias pro existující datový proud s transformací SELECT. Kliknutím na ikonu plus vedle transformace a výběrem možnosti **Nová větev**vytvořte novou větev. Přidejte transformaci SELECT, která bude aliasovat původní datový proud. Přidejte transformaci JOIN a jako **levý datový** proud zvolte původní datový proud a jako **správný datový proud**vyberte transformaci.

![Připojovat se k sobě](media/data-flow/selfjoin.png "Připojovat se k sobě")

## <a name="testing-join-conditions"></a>Testování podmínek připojení

Při testování transformací spojení s náhledem dat v režimu ladění použijte malou sadu známých dat. Při vzorkování řádků z velké datové sady nelze předpovědět, které řádky a klíče budou načteny pro testování. Výsledek je Nedeterministický, což znamená, že podmínky spojení nemusí vracet žádné shody.

## <a name="data-flow-script"></a>Skript toku dat

### <a name="syntax"></a>Syntaxe

```
<leftStream>, <rightStream>
    join(
        <conditionalExpression>,
        joinType: { 'inner'> | 'outer' | 'left_outer' | 'right_outer' | 'cross' }
        broadcast: { 'none' | 'left' | 'right' | 'both' }
    ) ~> <joinTransformationName>
```

### <a name="inner-join-example"></a>Příklad vnitřního spojení

Níže uvedený příklad je transformační transformace s názvem `JoinMatchedData`, která přebírá levý Stream `TripData` a `TripFare`pravého streamu.  Podmínka spojení je výraz `hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}`, který vrací hodnotu true, pokud se sloupce `hack_license`, `medallion`, `vendor_id`a `pickup_datetime` v každém datovém proudu shodují. `joinType` je `'inner'`. Povolujeme vysílání pouze v levém datovém proudu, takže `broadcast` má `'left'`hodnoty.

V uživatelském prostředí Data Factory Tato transformace vypadá jako na následujícím obrázku:

![Příklad spojení](media/data-flow/join-script1.png "Příklad spojení")

Skript toku dat pro tuto transformaci je v následujícím fragmentu kódu:

```
TripData, TripFare
    join(
        hack_license == { hack_license}
        && TripData@medallion == TripFare@medallion
        && vendor_id == { vendor_id}
        && pickup_datetime == { pickup_datetime},
        joinType:'inner',
        broadcast: 'left'
    )~> JoinMatchedData
```

### <a name="cross-join-example"></a>Příklad vzájemného spojení

Níže uvedený příklad je transformační transformace s názvem `CartesianProduct`, která přebírá levý Stream `TripData` a `TripFare`pravého streamu. Tato transformace přebírá dva proudy a vrací kartézském produkt jejich řádků. Podmínka spojení je `true()`, protože výstupem je plný kartézském produkt. `joinType` je `cross`. Povolujeme vysílání pouze v levém datovém proudu, takže `broadcast` má `'left'`hodnoty.

V uživatelském prostředí Data Factory Tato transformace vypadá jako na následujícím obrázku:

![Příklad spojení](media/data-flow/join-script2.png "Příklad spojení")

Skript toku dat pro tuto transformaci je v následujícím fragmentu kódu:

```
TripData, TripFare
    join(
        true(),
        joinType:'cross',
        broadcast: 'left'
    )~> CartesianProduct
```

## <a name="next-steps"></a>Další kroky

Po připojení dat vytvořte [odvozený sloupec](data-flow-derived-column.md) a [zajímka](data-flow-sink.md) dat do cílového úložiště dat.
