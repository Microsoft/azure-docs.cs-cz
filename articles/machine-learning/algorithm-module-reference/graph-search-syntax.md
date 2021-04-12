---
title: Syntaxe dotazů prohledávání grafů
titleSuffix: Azure Machine Learning
description: Naučte se používat syntaxi vyhledávacích dotazů v Návrháři Azure Machine Learning k hledání uzlů v grafu kanálu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/24/2021
ms.openlocfilehash: 74cf0b897529e8bb198b6f82a57e187662a4a285
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259216"
---
# <a name="graph-search-query-syntax"></a>Syntaxe dotazů prohledávání grafů

V tomto článku se dozvíte o funkcích hledání grafů v Azure Machine Learning. 

Vyhledávání v grafu umožňuje rychle procházet uzel při ladění nebo vytváření kanálu. Můžete buď zadat klíčové slovo nebo dotaz na vstupní pole na panelu nástrojů nebo v části Karta Hledat na levém panelu a aktivovat vyhledávání. Všechny odpovídající výsledky budou zvýrazněny žlutě na plátně. Pokud vyberete výsledek na levém panelu, uzel na plátně se zvýrazní červeně.

![Snímek obrazovky s ukázkovým prostředím pro hledání grafů](media/search/graph-search-0322.png)

Hledání v grafu podporuje fulltextové vyhledávání klíčových slov v názvu uzlu a komentářích. Můžete také filtrovat podle vlastností uzlu, jako je runStatus, Duration, computeTarget. Hledání klíčových slov je založené na dotazu Lucene. Úplný vyhledávací dotaz vypadá takto:  

**[[dotaz na Lucene] | [dotaz filtru]]** 

Můžete použít buď dotaz na Lucene, nebo dotaz na filtr. Chcete-li použít obojí, použijte **|** oddělovač. Syntaxe dotazu filtru je více striktní než dotaz Lucene. Takže pokud lze vstup zákazníka analyzovat jak obojí, použije se dotaz filtru.

Například `data OR model | compute in {cpucluster}` je třeba hledat uzly, které obsahují název nebo komentář obsahující `data` nebo `model` a COMPUTE jsou cpucluster.
 

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
- publish
- tags

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