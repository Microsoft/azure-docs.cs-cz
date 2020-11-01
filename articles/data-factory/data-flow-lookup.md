---
title: Transformace vyhledávání v toku dat mapování
description: Referenční data z jiného zdroje pomocí transformace vyhledávání v toku dat mapování.
author: kromerm
ms.reviewer: daperlov
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/30/2020
ms.openlocfilehash: 7ed1d9db09357b0702188c01a802600ff6350aff
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2020
ms.locfileid: "93147262"
---
# <a name="lookup-transformation-in-mapping-data-flow"></a>Transformace vyhledávání v toku dat mapování

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Pomocí transformace vyhledávání můžete odkazovat na data z jiného zdroje v datovém proudu toku dat. Transformace vyhledávání připojí sloupce z odpovídajících dat ke zdrojovým datům.

Transformace vyhledávání se podobá levému vnějšímu spojení. Všechny řádky z primárního datového proudu budou existovat ve výstupním datovém proudu s dalšími sloupci z vyhledávacího datového proudu.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xsVT]

## <a name="configuration"></a>Konfigurace

![Snímek obrazovky zobrazuje kartu nastavení vyhledávání s popisky popsanými v následujícím textu.](media/data-flow/lookup1.png "Vyhledávání")

**Primární datový proud:** Příchozí datový proud dat. Tento datový proud je ekvivalentní levé straně spojení.

**Vyhledávací Stream:** Data připojená k primárnímu datovému proudu. Data, která se přidávají, určují podmínky vyhledávání. Tento datový proud je ekvivalentní pravé straně spojení.

**Porovnává více řádků:** Pokud je povoleno, bude řádek s více shodami v primárním datovém proudu vracet více řádků. V opačném případě bude vrácen pouze jeden řádek na základě podmínky ' shoda na '.

**Shoda na:** Není vybráno pouze, pokud není vybrána možnost rozlišovat více řádků. Vyberte, jestli se má shoda na žádném řádku, první shodě nebo poslední shodě. Každý řádek se doporučuje, protože se spustí nejrychlejší. Pokud vyberete první řádek nebo poslední řádek, budete muset zadat podmínky řazení.

**Podmínky vyhledávání:** Vyberte sloupce, na kterých se má shoda. Pokud je splněna podmínka rovnosti, řádky budou považovány za shodné. Najeďte myší a vyberte vypočítaný sloupec a extrahujte hodnotu pomocí [jazyka výrazu toku dat](data-flow-expression-functions.md).

Transformace vyhledávání podporuje pouze shody rovnosti. Pokud chcete výraz vyhledávání přizpůsobit tak, aby zahrnoval jiné operátory, jako je větší než, doporučuje se použít [v transformaci JOIN vzájemné spojení](data-flow-join.md#custom-cross-join). V případě vzájemného spojení se vyhnete případným chybám kartézském produktu při spuštění.

Výstupní data obsahují všechny sloupce z obou datových proudů. Chcete-li odstranit duplicitní nebo nechtěné sloupce, po transformaci vyhledávání přidejte [transformaci Select](data-flow-select.md) . Sloupce je také možné v transformaci jímky vyřadit nebo přejmenovat.

### <a name="non-equi-joins"></a>Spojení nepoužívající operátora

Chcete-li použít podmíněný operátor, jako je například není rovno (! =) nebo větší než (>) ve vašich podmínkách vyhledávání, změňte rozevírací seznam operátoru mezi dvěma sloupci. Spojení typu non-čárka vyžadují všesměrové vysílání aspoň jednoho ze dvou datových proudů pomocí **pevného** vysílání na kartě **optimalizace** .

![Vyhledávání bez operátorů](media/data-flow/non-equi-lookup.png "Vyhledávání bez operátorů")

## <a name="analyzing-matched-rows"></a>Analýza odpovídajících řádků

Po transformaci vyhledávání `isMatch()` lze funkci použít k zobrazení, zda vyhledávání odpovídá jednotlivým řádkům.

![Vzor vyhledávání](media/data-flow/lookup111.png "Vzor vyhledávání")

Příklad tohoto modelu používá transformaci podmíněného rozdělení k rozdělení na `isMatch()` funkci. V předchozím příkladu přecházejí řádky, které odpovídají hornímu streamu, a nevyhovujícím tokům řádků přes ```NoMatch``` datový proud.

## <a name="testing-lookup-conditions"></a>Testování podmínek vyhledávání

Při testování transformace vyhledávání s náhledem dat v režimu ladění použijte malou sadu známých dat. Při vzorkování řádků z velké datové sady nelze předpovědět, které řádky a klíče budou načteny pro testování. Výsledek je Nedeterministický, což znamená, že podmínky spojení nemusí vracet žádné shody.

## <a name="broadcast-optimization"></a>Optimalizace všesměrového vysílání

![Připojení všesměrového vysílání](media/data-flow/broadcast.png "Připojení všesměrového vysílání")

V okně spojení, vyhledávání a existence transformace, pokud se jeden nebo oba datové proudy vejdou do paměti pracovního uzlu, můžete optimalizovat výkon tím, že povolíte **všesměrové vysílání** . Ve výchozím nastavení se modul Spark automaticky rozhodne, zda se má vysílání jedna strana vysílat. Chcete-li ručně zvolit, která strana se má vysílat, vyberte možnost **pevná** .

Nedoporučuje se zakázat všesměrové vysílání přes možnost **off** , pokud vaše spojení neběží v případě chyb časového limitu.

## <a name="cached-lookup"></a>Vyhledávání v mezipaměti

Pokud provádíte více menších hledání na stejném zdroji, jímka v mezipaměti a vyhledávání jsou pravděpodobně vhodnější, než transformace vyhledávání. Běžné příklady, kdy může být jímka mezipaměti lepší, vyhledávají maximální hodnotu v úložišti dat a odpovídají kódy chyb v databázi chybových zpráv. Další informace najdete v informacích o [jímky mezipaměti](data-flow-sink.md#cache-sink) a [vyhledáváních uložených v mezipaměti](concepts-data-flow-expression-builder.md#cached-lookup).

## <a name="data-flow-script"></a>Skript toku dat

### <a name="syntax"></a>Syntax

```
<leftStream>, <rightStream>
    lookup(
        <lookupConditionExpression>,
        multiple: { true | false },
        pickup: { 'first' | 'last' | 'any' },  ## Only required if false is selected for multiple
        { desc | asc }( <sortColumn>, { true | false }), ## Only required if 'first' or 'last' is selected. true/false determines whether to put nulls first
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <lookupTransformationName>
```
### <a name="example"></a>Příklad

![Snímek obrazovky zobrazující kartu nastavení vyhledávání pro následující kód.](media/data-flow/lookup-dsl-example.png "Vyhledávání")

Skript toku dat pro výše uvedenou konfiguraci vyhledávání je ve fragmentu kódu níže.

```
SQLProducts, DimProd lookup(ProductID == ProductKey,
    multiple: false,
    pickup: 'first',
    asc(ProductKey, true),
    broadcast: 'auto')~> LookupKeys
```
## 
Další kroky

* Transformace [a](data-flow-join.md) [existence existují](data-flow-exists.md) ve více vstupech datových proudů.
* Použití [podmíněné transformace rozdělení](data-flow-conditional-split.md) s ```isMatch()``` k rozdělení řádků na shodné a neshodné hodnoty
