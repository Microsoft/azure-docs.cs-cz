---
title: Transformace vyhledávání v toku dat mapování
description: Referenční data z jiného zdroje pomocí vyhledávací transformace v toku dat mapování.
author: kromerm
ms.reviewer: daperlov
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/23/2020
ms.openlocfilehash: 08ad231b462e21fefebacd4e280bfb2b13bc0eef
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413647"
---
# <a name="lookup-transformation-in-mapping-data-flow"></a>Transformace vyhledávání v toku dat mapování

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Transformace vyhledávání slouží k odkazování na data z jiného zdroje v datovém toku. Vyhledávací transformace připojí sloupce z odpovídajících dat ke zdrojovým datům.

Transformace vyhledávání je podobná levému vnějšímu spojení. Všechny řádky z primárního datového proudu budou existovat ve výstupním datovém proudu s dalšími sloupci z vyhledávacího proudu. 

## <a name="configuration"></a>Konfigurace

![Transformace vyhledávání](media/data-flow/lookup1.png "Vyhledávání")

**Primární datový proud:** Příchozí datový proud. Tento datový proud je ekvivalentní levé straně spojení.

**Vyhledávací proud:** Data, která je připojena k primárnímu datovému proudu. Která data jsou přidána, je určena podmínkami vyhledávání. Tento datový proud je ekvivalentní pravé straně spojení.

**Porovná více řádků:** Pokud je povoleno, řádek s více shodami v primárním datovém proudu vrátí více řádků. V opačném případě bude vrácen pouze jeden řádek na základě podmínky "Shoda na".

**Zápas na:** Viditelné pouze v případě, že je povoleno "Porovnat více řádků". Zvolte, zda se má shodovat na libovolném řádku, první shodě nebo poslední shodě. Každý řádek se doporučuje, protože provádí nejrychlejší. Pokud je vybrán první řádek nebo poslední řádek, budete muset zadat podmínky řazení.

**Podmínky vyhledávání:** Zvolte sloupce, ve kterých se mají shodovat. Pokud je splněna podmínka rovnosti, budou řádky považovány za shodu. Najeďte přes a vyberte "Vypočítaný sloupec", chcete-li extrahovat hodnotu pomocí [jazyka toku dat](data-flow-expression-functions.md).

Transformace vyhledávání podporuje pouze rovnost i rovnosti shody. Chcete-li přizpůsobit vyhledávací výraz tak, aby zahrnoval další operátory, například větší než, doporučujeme použít [křížové spojení v transformaci spojení](data-flow-join.md#custom-cross-join). Křížové spojení zabrání případným kartézovým chybám při provádění.

Všechny sloupce z obou datových proudů jsou zahrnuty ve výstupních datech. Chcete-li přetáhnout duplicitní nebo nežádoucí sloupce, přidejte [vybranou transformaci](data-flow-select.md) po transformaci vyhledávání. Sloupce lze také vynechat nebo přejmenovat v transformaci jímky.

## <a name="analyzing-matched-rows"></a>Analýza odpovídajících řádků

Po transformaci vyhledávání lze `isMatch()` funkci použít k zobrazení, zda se vyhledávání shoduje s jednotlivými řádky.

![Vyhledávací vzorek](media/data-flow/lookup111.png "Vyhledávací vzorek")

Příkladem tohoto vzoru je použití podmíněné rozdělení `isMatch()` transformace rozdělit na funkci. Ve výše uvedeném příkladu odpovídající řádky projít horní datový ```NoMatch``` proud a neodpovídající řádky toku prostřednictvím datového proudu.

## <a name="testing-lookup-conditions"></a>Testovací vyhledávací podmínky

Při testování transformace vyhledávání s náhledem dat v režimu ladění použijte malou sadu známých dat. Při vzorkování řádků z velké datové sady nelze předpovědět, které řádky a klíče budou přečteny pro testování. Výsledkem je nedeterministický, což znamená, že podmínky spojení nemusí vrátit žádné shody.

## <a name="broadcast-optimization"></a>Optimalizace vysílání

V Azure Data Factory mapování toků dat spustit v prostředích škálované svižné. Pokud se vaše datová sada vejde do paměti pracovního uzlu, může být výkon vyhledávání optimalizován povolením vysílání.

![Spojení vysílání](media/data-flow/broadcast.png "Spojení vysílání")

Povolení vysílání posune celou datovou sadu do paměti. U menších datových sad obsahujících pouze několik tisíc řádků může vysílání výrazně zlepšit výkon vyhledávání. U velkých datových sad může tato možnost vést k výjimce nedostatku paměti.

## <a name="data-flow-script"></a>Skript toku dat

### <a name="syntax"></a>Syntaxe

```
<leftStream>, <rightStream>
    lookup(
        <lookupConditionExpression>,
        multiple: { true | false },
        pickup: { 'first' | 'last' | 'any' },  ## Only required if false is selected for multiple
        { desc | asc }( <sortColumn>, { true | false }), ## Only required if 'first' or 'last' is selected. true/false determines whether to put nulls first
        broadcast: { 'none' | 'left' | 'right' | 'both' }
    ) ~> <lookupTransformationName>
```
### <a name="example"></a>Příklad

![Transformace vyhledávání](media/data-flow/lookup-dsl-example.png "Vyhledávání")

Skript toku dat pro výše uvedenou konfiguraci vyhledávání je ve fragmentu kódu níže.

```
SQLProducts, DimProd lookup(ProductID == ProductKey,
    multiple: false,
    pickup: 'first',
    asc(ProductKey, true),
    broadcast: 'none')~> LookupKeys
```
## 
Další kroky

* [Spojení](data-flow-join.md) a [existuje](data-flow-exists.md) transformace, které mají více vstupů datového proudu
* Použití [podmíněné](data-flow-conditional-split.md) rozdělení ```isMatch()``` transformace s rozdělit řádky na odpovídající a neodpovídající hodnoty
