---
title: Transformace pivotu v toku dat mapování
description: Kontingenční data z řádků do sloupců pomocí Azure Data Factory mapování pivotu toku dat
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/17/2020
ms.openlocfilehash: e098182c000cbe05df533434a41c55b797ef876f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87086655"
---
# <a name="pivot-transformation-in-mapping-data-flow"></a>Transformace pivotu v toku dat mapování


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Použijte transformaci Pivot k vytvoření více sloupců z jedinečných hodnot řádků v jednom sloupci. Pivot je agregovaná transformace, kde můžete vybrat seskupit podle sloupců a generovat kontingenční sloupce pomocí [agregačních funkcí](data-flow-expression-functions.md#aggregate-functions).

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4C4YN]

## <a name="configuration"></a>Konfigurace

Transformace pivotu vyžaduje tři různé vstupy: seskupit podle sloupců, vytvořit kontingenční klíč a jak generovat sloupce s kontingenčními tabulkami.

### <a name="group-by"></a>Seskupit podle

![Možnosti pro seskupení](media/data-flow/pivot2.png "Možnosti pro seskupení")

Vyberte sloupce, přes které se mají agregovat sloupce tabulky. Výstupní data budou seskupovat všechny řádky se stejnou hodnotou Group by do jednoho řádku. Agregace provedená v kontingenčním sloupci probíhají přes každou skupinu.

Tato část je volitelná. Pokud nejsou vybrány žádné sloupce Group by, bude celý datový proud agregován a bude výstupem pouze jeden řádek.

### <a name="pivot-key"></a>Klávesa Pivot

![Klávesa Pivot](media/data-flow/pivot3.png "Klávesa Pivot")

Kontingenční klíč je sloupec, jehož hodnoty řádků se překopírují do nových sloupců. Ve výchozím nastavení vytvoří transformace Pivot pro každou hodnotu jedinečného řádku nový sloupec.

V části s popisem **hodnoty** můžete zadat konkrétní hodnoty řádků, které se mají Překlopit. Budou předávány pouze hodnoty řádků zadané v této části. Když se povolí **hodnota null** , vytvoří se pro hodnoty null ve sloupci sloupec s kontingenčním objektem.

### <a name="pivoted-columns"></a>Sloupce v kontingenčním sloupci

![Sloupce v kontingenčním sloupci](media/data-flow/pivot4.png "Sloupce v kontingenčním sloupci")

Pro každou jedinečnou hodnotu kontingenčního klíče, která se stala sloupcem, vygenerujte agregovanou hodnotu řádku pro každou skupinu. Můžete vytvořit více sloupců pro každý pivotový klíč. Každý sloupec kontingenčního pole musí obsahovat alespoň jednu [agregační funkci](data-flow-expression-functions.md#aggregate-functions).

**Vzor názvu sloupce:** Vyberte způsob formátování názvu sloupce u každého kontingenčního sloupce. Název sloupce výstupu pro výstup bude kombinace hodnoty Pivot Key, prefixu sloupce a volitelné předpony, postačuje, prostřední znaky. 

**Uspořádání sloupců:** Pokud vygenerujete více než jeden kontingenční sloupec pro každý kontingenční klíč, určete, jak chcete seřadit sloupce. 

**Předpona sloupce:** Pokud vygenerujete více než jeden kontingenční sloupec pro každý kontingenční klíč, zadejte předponu sloupce pro každý sloupec. Toto nastavení je volitelné, pokud máte pouze jeden sloupec s kontingenčním prvkem.

## <a name="help-graphic"></a>Obrázek v nápovědě

Následující obrázek Help znázorňuje, jak různé komponenty pivotu vzájemně komunikují.

![Grafika v nápovědě pro Pivot](media/data-flow/pivot5.png "Obrázek v nápovědě k pivotu")

## <a name="pivot-metadata"></a>Metadata pivotu

Nejsou-li v konfiguraci klíče Pivot zadány žádné hodnoty, budou v době běhu dynamicky generovány sloupce s kontingenčními tabulkami. Počet předaných sloupců se rovná počtu jedinečných hodnot klíče Pivot vynásobený počtem kontingenčních sloupců. Může se jednat o měnící se číslo, ale v uživatelském rozhraní se nezobrazí metadata sloupce na kartě **Kontrola** a nebude se provádět žádné šíření sloupců. Chcete-li tyto sloupce transformovat, použijte možnosti [vzoru sloupce](concepts-data-flow-column-pattern.md) pro mapování toku dat. 

Pokud jsou nastavené konkrétní hodnoty klíče pivotu, v metadatech se zobrazí sloupce s kontingenčními tabulkami. Názvy sloupců budou k dispozici v mapování kontroly a jímky.

### <a name="generate-metadata-from-drifted-columns"></a>Generovat metadata z vydaných sloupců

Kontingenční tabulka dynamicky generuje nové názvy sloupců založené na hodnotách řádků. Tyto nové sloupce můžete přidat do metadat, na která lze později odkazovat v toku dat. Uděláte to tak, že použijete [mapu](concepts-data-flow-schema-drift.md#map-drifted-columns-quick-action) s odrovnou rychlé akce v náhledu dat. 

![Kontingenční sloupce](media/data-flow/newpivot1.png "Mapování odhozených kontingenčních sloupců")

### <a name="sinking-pivoted-columns"></a>Jímka – kontingenční sloupce

I když jsou pivotované sloupce dynamické, můžou být pořád zapsané do cílového úložiště dat. Povolte možnost **Povolit posun schématu** v nastavení jímky. To vám umožní zapsat sloupce, které nejsou zahrnuté v metadatech. V metadatech sloupců se neobjeví nové dynamické názvy, ale možnost posunu schématu vám umožní tato data obložit.

### <a name="rejoin-original-fields"></a>Znovu připojit původní pole

Transformace pivotu bude promítnout pouze sloupce Group by a pivoted. Pokud chcete, aby výstupní data zahrnovala jiné vstupní sloupce, použijte vzor [vlastního spojení](data-flow-join.md#self-join) .

## <a name="data-flow-script"></a>Skript toku dat

### <a name="syntax"></a>Syntax

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

Obrazovky zobrazené v sekci konfigurace obsahují následující skript toku dat:

```
BasketballPlayerStats pivot(groupBy(Tm),
    pivotBy(Pos),
    {} = count(),
    columnNaming: '$V$N count',
    lateral: true) ~> PivotExample

```

## <a name="next-steps"></a>Další kroky

Zkuste [transformaci Unpivot](data-flow-unpivot.md) , aby se hodnoty sloupce přepnuly na hodnoty řádků. 
