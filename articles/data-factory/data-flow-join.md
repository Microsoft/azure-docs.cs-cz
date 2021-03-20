---
title: Transformace spojení v toku dat mapování
description: Kombinování dat ze dvou zdrojů dat pomocí transformace spojení v Azure Data Factory toku dat mapování
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/15/2020
ms.openlocfilehash: ac84ce17f53145ffd85ffa31b6633d8b4b184962
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93042653"
---
# <a name="join-transformation-in-mapping-data-flow"></a>Transformace spojení v toku dat mapování

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Použijte transformaci JOIN ke kombinování dat ze dvou zdrojů nebo datových proudů v toku dat mapování. Výstupní datový proud bude obsahovat všechny sloupce z obou zdrojů odpovídajících v závislosti na podmínce spojení. 

## <a name="join-types"></a>Typy spojení

Mapování toků dat aktuálně podporuje pět různých typů spojení.

### <a name="inner-join"></a>Vnitřní spojení

Vnitřní spojení pouze obsahuje řádky, které mají v obou tabulkách odpovídající hodnoty.

### <a name="left-outer"></a>Levé vnější

Levé vnější spojení vrátí všechny řádky z levého proudu a odpovídající záznamy z pravého datového proudu. Pokud řádek z levého datového proudu neodpovídá, výstupní sloupce z pravého datového proudu jsou nastaveny na hodnotu NULL. Výstupem budou řádky vrácené vnitřním spojením a nespárované řádky z levého streamu.

> [!NOTE]
> Modul Spark používaný datovými toky občas selže z důvodu možného kartézském produktů v podmínkách připojení. Pokud k tomu dojde, můžete přepnout na vlastní vzájemné spojení a zadat podmínku JOIN ručně. Výsledkem může být pomalejší výkon datových toků, protože spouštěcí modul může potřebovat vypočítat všechny řádky z obou stran relace a potom filtrovat řádky.

### <a name="right-outer"></a>Pravé vnější

Pravé vnější spojení vrátí všechny řádky z pravého proudu a odpovídajících záznamů z levého datového proudu. Pokud řádek ze pravého datového proudu neodpovídá, výstupní sloupce z levého datového proudu jsou nastaveny na hodnotu NULL. Výstupem budou řádky vrácené vnitřním spojením a nespárované řádky z pravého datového proudu.

### <a name="full-outer"></a>Úplné vnější

Úplné vnější spojení vyprodukuje všechny sloupce a řádky z obou stran s hodnotami NULL pro sloupce, které se neshodují.

### <a name="custom-cross-join"></a>Vlastní vzájemné spojení

V případě vzájemného spojení se v závislosti na podmínce vytvoří výstup z obou datových proudů mezi různými produkty. Pokud používáte podmínku, která není rovnost, zadejte jako podmínku vzájemného spojení vlastní výraz. Výstupní datový proud bude obsahovat všechny řádky, které splňují podmínku spojení.

Tento typ spojení můžete použít pro spojení a podmínky, které nejsou typu ekvivalentní ```OR``` .

Pokud chcete explicitně vytvořit úplný kartézském produkt, použijte transformaci odvozeného sloupce v každém ze dvou nezávislých datových proudů předtím, než se připojíte k vytvoření syntetického klíče, který se má shodovat. V každém datovém proudu můžete například vytvořit nový sloupec v odvozeném sloupci ```SyntheticKey``` a nastavit jej jako je roven ```1``` . Pak použijte ```a.SyntheticKey == b.SyntheticKey``` jako vlastní výraz JOIN.

> [!NOTE]
> Nezapomeňte do vlastního vzájemného spojení zahrnout alespoň jeden sloupec z každé strany levého a pravého vztahu. Výsledkem provádění vzájemného spojení se statickými hodnotami místo sloupců z každé strany je úplné prověřování celé datové sady, což způsobí, že tok dat nebude dostatečně fungovat.

## <a name="configuration"></a>Konfigurace

1. Vyberte datový proud, se kterým se připojujete, v rozevíracím seznamu **správný datový proud** .
1. Vyberte **typ spojení**
1. Vyberte, na které klíčové sloupce se má shoda při spojování podmínky připojit. Ve výchozím nastavení tok dat hledá rovnost mezi jedním sloupcem v každém datovém proudu. Pro porovnání přes vypočítanou hodnotu umístěte ukazatel myši na rozevírací seznam sloupec a vyberte **vypočítaný sloupec**.

![Transformace spojení](media/data-flow/join.png "Připojení")

### <a name="non-equi-joins"></a>Spojení nepoužívající operátora

Chcete-li použít podmíněný operátor, jako je například není rovno (! =) nebo větší než (>) v podmínkách připojení, změňte rozevírací seznam operátoru mezi dvěma sloupci. Spojení typu non-čárka vyžadují všesměrové vysílání aspoň jednoho ze dvou datových proudů pomocí **pevného** vysílání na kartě **optimalizace** .

![Spojení typu non-čárka](media/data-flow/non-equi-join.png "Spojení typu non-čárka")

## <a name="optimizing-join-performance"></a>Optimalizace výkonu připojení

Na rozdíl od sloučení slučovacích nástrojů v nástrojích, jako je SSIS, transformace spojení není povinná operace sloučení spojení. Klávesy JOIN nevyžadují řazení. Operace join probíhá na základě optimální operace join ve Sparku, ať už při vysílání, nebo při připojení na straně mapy.

![Optimalizace transformace JOIN](media/data-flow/joinoptimize.png "Spojit optimalizaci")

V okně spojení, vyhledávání a existence transformace, pokud se jeden nebo oba datové proudy vejdou do paměti pracovního uzlu, můžete optimalizovat výkon tím, že povolíte **všesměrové vysílání**. Ve výchozím nastavení se modul Spark automaticky rozhodne, zda se má vysílání jedna strana vysílat. Chcete-li ručně zvolit, která strana se má vysílat, vyberte možnost **pevná**.

Nedoporučuje se zakázat všesměrové vysílání přes možnost **off** , pokud vaše spojení neběží v případě chyb časového limitu.

## <a name="self-join"></a>Self-Join

Pokud chcete datový proud sám propojit se sebou samým, vytvořte alias pro existující datový proud s transformací SELECT. Kliknutím na ikonu plus vedle transformace a výběrem možnosti **Nová větev** vytvořte novou větev. Přidejte transformaci SELECT, která bude aliasovat původní datový proud. Přidejte transformaci JOIN a jako **levý datový** proud zvolte původní datový proud a jako **správný datový proud** vyberte transformaci.

![Připojovat se k sobě](media/data-flow/selfjoin.png "Připojovat se k sobě")

## <a name="testing-join-conditions"></a>Testování podmínek připojení

Při testování transformací spojení s náhledem dat v režimu ladění použijte malou sadu známých dat. Při vzorkování řádků z velké datové sady nelze předpovědět, které řádky a klíče budou načteny pro testování. Výsledek je Nedeterministický, což znamená, že podmínky spojení nemusí vracet žádné shody.

## <a name="data-flow-script"></a>Skript toku dat

### <a name="syntax"></a>Syntax

```
<leftStream>, <rightStream>
    join(
        <conditionalExpression>,
        joinType: { 'inner'> | 'outer' | 'left_outer' | 'right_outer' | 'cross' }
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <joinTransformationName>
```

### <a name="inner-join-example"></a>Příklad vnitřního spojení

Níže uvedený příklad je transformační transformace s názvem `JoinMatchedData` , která přebírá levý Stream `TripData` a správný Stream `TripFare` .  Podmínka spojení je výraz `hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}` , který vrací hodnotu true, pokud `hack_license` se `medallion` `vendor_id` `pickup_datetime` v každém datovém proudu shodují sloupce,, a. `joinType`Je `'inner'` . Povolujeme všesměrové vysílání pouze v levém datovém proudu, takže `broadcast` má hodnotu `'left'` .

V uživatelském prostředí Data Factory Tato transformace vypadá jako na následujícím obrázku:

![Snímek obrazovky zobrazuje transformaci s vybranou kartou nastavení spojení a typ spojení vnitřní.](media/data-flow/join-script1.png "Příklad spojení")

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

### <a name="custom-cross-join-example"></a>Příklad vlastního vzájemného propojení

Níže uvedený příklad je transformační transformace s názvem `JoiningColumns` , která přebírá levý Stream `LeftStream` a správný Stream `RightStream` . Tato transformace přebírá dva proudy a spojuje všechny řádky, kde sloupec `leftstreamcolumn` je větší než sloupec `rightstreamcolumn` . `joinType`Je `cross` . U všesměrového vysílání není povolená `broadcast` hodnota `'none'` .

V uživatelském prostředí Data Factory Tato transformace vypadá jako na následujícím obrázku:

![Snímek obrazovky ukazuje transformaci s vybranou kartou nastavení spojení a typem spojení Custom (příčný).](media/data-flow/join-script2.png "Příklad spojení")

Skript toku dat pro tuto transformaci je v následujícím fragmentu kódu:

```
LeftStream, RightStream
    join(
        leftstreamcolumn > rightstreamcolumn,
        joinType:'cross',
        broadcast: 'none'
    )~> JoiningColumns
```

## <a name="next-steps"></a>Další kroky

Po připojení dat vytvořte [odvozený sloupec](data-flow-derived-column.md) a [zajímka](data-flow-sink.md) dat do cílového úložiště dat.
