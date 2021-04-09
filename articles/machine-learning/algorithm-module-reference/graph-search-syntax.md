---
title: Syntaxe dotazů prohledávání grafů
titleSuffix: Azure Machine Learning
description: Naučte se používat syntaxi vyhledávacích dotazů v Návrháři Azure Machine Learning k hledání uzlů v v grafu kanálu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 8/24/2020
ms.openlocfilehash: 762581ea5b3183d62913e9ea6935bf7e4c4ae67f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "93420763"
---
# <a name="graph-search-query-syntax"></a>Syntaxe dotazů prohledávání grafů

V tomto článku se dozvíte o syntaxi dotazu vyhledávacího grafu v Azure Machine Learning. Funkce hledání v grafu umožňuje vyhledat uzel podle jeho názvu a vlastností. 

 ![Animovaný snímek obrazovky znázorňující ukázkové možnosti vyhledávání grafů](media/search/graph-search.gif)

Hledání v grafu podporuje fulltextové vyhledávání klíčových slov v názvu uzlu a komentářích. Můžete také filtrovat vlastnost uzlu, jako je runStatus, Duration, computeTarget. Hledání klíčových slov je založené na dotazu Lucene. Úplný vyhledávací dotaz vypadá takto:  

**[dotaz na Lucene | [dotaz filtru]** 

Můžete použít buď dotaz na Lucene, nebo dotaz na filtr. Chcete-li použít obojí, použijte **|** oddělovač. Syntaxe dotazu filtru je více striktní než dotaz Lucene. Takže pokud lze vstup zákazníka analyzovat jak obojí, použije se dotaz filtru.

 

## <a name="lucene-query"></a>Dotaz Lucene

Při vyhledávání v grafu se jako syntaxe fulltextového vyhledávání v uzlu název a komentář používá jednoduchý dotaz Lucene. Podporovány jsou následující operátory Lucene:

 
- A/NEBO
- Shoda se zástupnými znaky **?** a **\*** operátory.

### <a name="examples"></a>Příklady

- Jednoduché hledání: `JSON Data`

- A/NEBO: `JSON AND Validation`

- Více nebo: `(JSON AND Validation) OR (TSV AND Training)`

 
- Shoda se zástupnými znaky: 
    - `machi?e learning`
    - `mach*ing`
 
>[!NOTE]
> Nelze spustit dotaz Lucene se znakem "*".

##  <a name="filter-query"></a>Filtrovat dotaz

 
Filtrovat dotazy pomocí následujícího vzoru:
 
`**[key1] [operator1] [value1]; [key2] [operator1] [value2];**`

 
Následující vlastnosti uzlu můžete použít jako klíče:

- runStatus
- compute
- doba trvání
- opakovan

A použijte následující operátory:

- Větší nebo rovno: `>=`
- Menší nebo rovno: `<=`
- Zvýšen `>`
- Tolik `<`
- Výši `==`
- Uzavírací `=`
- NotEqual: `!=`
- Pro `in`

 
 

### <a name="example"></a>Příklad

- Doba trvání > 100;
- stav v {Failed, NotStarted}
- COMPUTE v {GPU-cluster}; runStatus v {Completed}

## <a name="technical-notes"></a>Technické poznámky

- Vztah mezi více filtry je "a"
- Pokud `>=,  >,  <, or  <=` je zvolena, hodnoty budou automaticky převedeny na typ čísla. V opačném případě jsou typy řetězců použity pro porovnání.
- Pro všechny hodnoty řetězcového typu je v porovnání nerozlišuje velká a malá písmena.
- Operátor "in" očekává kolekci jako hodnotu, syntax shromažďování je `{name1, name2, name3}`
- Místo pro klíčová slova se bude ignorovat.