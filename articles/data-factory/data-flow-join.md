---
title: Spojit transformaci v toku dat mapování
description: Kombinování dat ze dvou zdrojů dat pomocí transformace spojení v toku dat mapování Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/02/2020
ms.openlocfilehash: 0c0e35f7f06afd0cafa4a1e353b7eda84ed226f2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413671"
---
# <a name="join-transformation-in-mapping-data-flow"></a>Spojit transformaci v toku dat mapování

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Transformace spojení slouží ke kombinování dat ze dvou zdrojů nebo datových proudů v toku dat mapování. Výstupní datový proud bude obsahovat všechny sloupce z obou zdrojů odpovídající na základě podmínky spojení. 

## <a name="join-types"></a>Typy spojení

Mapování datových toků aktuálně podporuje pět různých typů spojení.

### <a name="inner-join"></a>Vnitřní spojení

Vnitřní spojení pouze výstupy řádky, které mají odpovídající hodnoty v obou tabulkách.

### <a name="left-outer"></a>Levý vnější

Levé vnější spojení vrátí všechny řádky z levého datového proudu a odpovídající záznamy z pravého datového proudu. Pokud řádek z levého datového proudu nemá žádnou shodu, výstupní sloupce z pravého datového proudu jsou nastaveny na hodnotu NULL. Výstupem budou řádky vrácené vnitřním spojením plus neodpovídající řádky z levého datového proudu.

> [!NOTE]
> Modul Spark používaný datovými toky občas selže kvůli možným kartézním produktům v podmínkách spojení. Pokud k tomu dojde, můžete přepnout na vlastní křížové spojení a ručně zadat podmínku spojení. To může mít za následek pomalejší výkon v tocích dat jako spuštění motoru může být nutné vypočítat všechny řádky z obou stran relace a potom filtrovat řádky.

### <a name="right-outer"></a>Pravý vnější

Pravé vnější spojení vrátí všechny řádky z pravého datového proudu a odpovídající záznamy z levého datového proudu. Pokud řádek z pravého datového proudu nemá žádnou shodu, výstupní sloupce z levého datového proudu jsou nastaveny na hodnotu NULL. Výstupem budou řádky vrácené vnitřním spojením plus neodpovídající řádky z pravého datového proudu.

### <a name="full-outer"></a>Úplné vnější

Úplné vnější spojení výstupy všechny sloupce a řádky z obou stran s hodnotami NULL pro sloupce, které nejsou odpovídající.

### <a name="custom-cross-join"></a>Vlastní křížové spojení

Křížové spojení výstupy křížový součin dvou proudů na základě podmínky. Pokud používáte podmínku, která není rovnost, zadejte vlastní výraz jako podmínku křížového spojení. Výstupní datový proud bude všechny řádky, které splňují podmínku spojení.

Tento typ spojení můžete použít pro neequi spojení a ```OR``` podmínky.

Pokud chcete explicitně vytvořit úplný kartézský produkt, použijte odvozený sloupec transformace v každém ze dvou nezávislých datových proudů před spojení mj. Můžete například vytvořit nový sloupec v odvozeném sloupci v každém volaném ```SyntheticKey``` datovém proudu a nastavit jej na rovno ```1```. Pak ```a.SyntheticKey == b.SyntheticKey``` použijte jako vlastní spojení výraz.

> [!NOTE]
> Nezapomeňte do vlastního křížového spojení zahrnout alespoň jeden sloupec z každé strany levého a pravého vztahu. Provádění křížových spojení se statickými hodnotami namísto sloupců z každé strany má za následek úplné prohledávání celé datové sady, což způsobí, že tok dat bude fungovat špatně.

## <a name="configuration"></a>Konfigurace

1. V rozevíracím souboru Pravý **datový proud** zvolte, ke kterému datovému proudu se připojujete.
1. Vyberte **typ spojení**
1. Zvolte klíčové sloupce, které se mají shodovat s podmínkou spojení. Ve výchozím nastavení tok dat hledá rovnost mezi jedním sloupcem v každém datovém proudu. Chcete-li porovnat pomocí vypočítané hodnoty, najeďte na rozbalovací seznam sloupců a vyberte **Vypočítaný sloupec**.

![Připojit transformace](media/data-flow/join.png "Spojit")

## <a name="optimizing-join-performance"></a>Optimalizace výkonu spojení

Na rozdíl od sloučení spojit v nástrojích, jako je SSIS, transformace spojení není povinné operace sloučení spojení. Klíče spojení nevyžadují řazení. Operace spojení probíhá na základě optimální operace spojení v Spark, buď vysílání nebo připojení na straně mapy.

![Optimalizace transformace spojení](media/data-flow/joinoptimize.png "Připojit se k optimalizaci")

Pokud jeden nebo oba datové proudy zapadají do paměti pracovního uzlu, dále optimalizovat výkon povolením **vysílání** na kartě optimalizace. Můžete také znovu rozdělit data o operaci spojení tak, aby lépe zapadá do paměti na pracovníka.

## <a name="self-join"></a>Vlastní připojení

Chcete-li datový proud připojit sami se sebou, alias existující datový proud s vybranou transformací. Vytvořte novou větev kliknutím na ikonu plus vedle transformace a výběrem **nové větve**. Přidejte vybranou transformaci, aby alias původní datový proud. Přidejte transformaci spojení a zvolte původní datový proud jako **levý datový proud** a vybranou transformaci jako pravý datový **proud**.

![Vlastní spojení](media/data-flow/selfjoin.png "Vlastní spojení")

## <a name="testing-join-conditions"></a>Podmínky spojení testování

Při testování transformace spojení s náhledem dat v režimu ladění použijte malou sadu známých dat. Při vzorkování řádků z velké datové sady nelze předpovědět, které řádky a klíče budou přečteny pro testování. Výsledkem je nedeterministický, což znamená, že podmínky spojení nemusí vrátit žádné shody.

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

Níže uvedený příklad je `JoinMatchedData` transformace spojení `TripData` s názvem, která přebírá levý proud a pravý datový proud `TripFare`.  Podmínka spojení je `hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}` výraz, který `hack_license`vrací `medallion` `vendor_id`hodnotu `pickup_datetime` true, pokud se v každé shodě datového proudu shodují sloupce , a sloupce. Je `joinType` `'inner'`. Povolujeme vysílání pouze v `broadcast` levém `'left'`proudu, takže má hodnotu .

V ux datové továrny tato transformace vypadá jako následující obrázek:

![Příklad připojení](media/data-flow/join-script1.png "Příklad připojení")

Skript toku dat pro tuto transformaci je ve fragmentu níže:

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

### <a name="custom-cross-join-example"></a>Příklad vlastního křížového spojení

Níže uvedený příklad je `JoiningColumns` transformace spojení `LeftStream` s názvem, která přebírá levý proud a pravý datový proud `RightStream`. Tato transformace trvá ve dvou proudů a `leftstreamcolumn` spojuje všechny `rightstreamcolumn`řádky, kde sloupec je větší než sloupec . Je `joinType` `cross`. Vysílání není povolena `broadcast` `'none'`má hodnotu .

V ux datové továrny tato transformace vypadá jako následující obrázek:

![Příklad připojení](media/data-flow/join-script2.png "Příklad připojení")

Skript toku dat pro tuto transformaci je ve fragmentu níže:

```
LeftStream, RightStream
    join(
        leftstreamcolumn > rightstreamcolumn,
        joinType:'cross',
        broadcast: 'none'
    )~> JoiningColumns
```

## <a name="next-steps"></a>Další kroky

Po připojení dat vytvořte [odvozený sloupec](data-flow-derived-column.md) a [uložte](data-flow-sink.md) data do cílového úložiště dat.
