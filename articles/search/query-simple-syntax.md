---
title: Jednoduchá syntaxe dotazů – Azure Search
description: Referenční informace pro jednoduchá syntaxe dotazů používá pro dotazech fulltextového vyhledávání ve službě Azure Search.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 01/31/2019
author: brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 12589d9fa4623dbb6ebfd12d2e007eb9303ecdbf
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56008325"
---
# <a name="simple-query-syntax-in-azure-search"></a>Jednoduchá syntaxe dotazů ve službě Azure Search
Služba Azure Search implementuje dva jazyků dotazů Lucene: [Jednoduchý analyzátor dotazů](https://lucene.apache.org/core/4_7_0/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) a [analyzátor dotazů Lucene](https://lucene.apache.org/core/4_10_2/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Jednoduchá syntaxe dotazů ve službě Azure Search, nezahrnuje možnosti přibližné/odpadový.  

> [!NOTE]  
>  Služba Azure Search poskytuje alternativu [syntaxe dotazů Lucene](query-lucene-syntax.md) pro složitější dotazy. Další informace o analýze architektury a výhody každého syntaxi dotazu najdete v tématu [jak funguje fulltextové vyhledávání ve službě Azure Search](https://docs.microsoft.com/azure/search/search-lucene-query-architecture).

## <a name="how-to-invoke-simple-parsing"></a>Jak volat jednoduchou analýza kódu

Jednoduchá syntaxe je výchozí nastavení. Volání je potřeba, pouze pokud se resetování syntaxe z úplného na jednoduché. Pokud chcete explicitně nastavit syntaxe, použijte `queryType` parametr hledání. Platné hodnoty jsou `simple|full`, s `simple` jako výchozí, a `full` pro Lucene. 

## <a name="query-behavior-anomalies"></a>Anomálie chování dotazu

Jakýkoli text s jeden nebo více výrazů se považuje za platný výchozí bod pro provedení dotazu. Služba Azure Search se odpovídaly i dokumenty obsahující některé nebo všechny podmínky, včetně všech změn během analýzy textu nalezen. 

Jednoznačné zvuky, je jeden aspekt jejich provádění dotazů ve službě Azure Search, která *může* produktu neočekávané výsledky, zvýšení spíše než snížení hledání výsledky jako další podmínky a operátory jsou přidány do vstupu řetězec. Určuje, zda skutečně dochází toto rozšíření závisí na zahrnutí operátoru NOT v kombinaci s `searchMode` je interpretován parametr nastavení, která určuje, jak není z hlediska a nebo nebo chování. Zadaný výchozí `searchMode=Any`, a operátor NOT, operace, které je vypočítán jako akci nebo tak, aby `"New York" NOT Seattle` vrátí všechny města, ve kterých nejsou Seattle.  

Obvykle můžete si s větší pravděpodobností zobrazíte těchto projevů v vzory interakcí uživatelů pro aplikace, které možnost vyhledávat obsah, ve kterém budou pravděpodobně obsahovat operátor v dotazu, třeba weby elektronického obchodování, které mají další vestavěné navigační struktury na rozdíl od uživatelů. Další informace najdete v tématu [operátor NOT](#not-operator). 

## <a name="operators-in-simple-search"></a>Operátory v jednoduché hledání

Operátory můžete vložit do řetězce dotazu k sestavení riche sadu kritérií, u kterých se nacházejí odpovídajících dokumentů. 

## <a name="and-operator-"></a>AND – operátor `+`

Operátor AND se znaménkem plus. Například `wifi+luxury` Vyhledá dokumenty, které obsahují i `wifi` a `luxury`.

## <a name="or-operator-"></a>NEBO – operátor `|`

Operátor OR je svislá čára neboli znakem přesměrování. Například `wifi | luxury` Vyhledá dokumenty, které obsahují buď `wifi` nebo `luxury` nebo obojí.

<a name="not-operator"></a>

## <a name="not-operator--"></a>NOT – operátor `-`

Operátor NOT se záporným znaménkem. Například `wifi –luxury` Vyhledá dokumenty, které mají `wifi` termín a/nebo nemají `luxury` (a/nebo se řídí `searchMode`).

> [!NOTE]  
>  `searchMode` Možnost řídí, zda výraz s operátorem NOT spojeny nebo sloučeny pomocí operátoru OR s podmínkami v dotazu chybí `+` nebo `|` operátor. Vzpomeňte si, že `searchMode` může být nastaven na hodnotu `any` (výchozí) nebo `all`. Pokud používáte `any`, včetně více výsledků a ve výchozím nastavení odvolání dotazů zvýší `-` bude vyhodnocen jako "Nebo NOT". Například `wifi -luxury` bude odpovídaly i dokumenty, že buď obsahují pojem `wifi` nebo ty, které neobsahují termín `luxury`. Pokud používáte `all`, zvýšit přesnost dotazů včetně méně výsledků a ve výchozím nastavení – bude interpretovat jako "A ne". Například `wifi -luxury` bude odpovídat dokumenty, které obsahují pojem `wifi` a neobsahují termín "luxusní". To je pravděpodobně mnohem intuitivnější chování `-` operátor. Proto byste měli zvážit použití `searchMode=all` místo `searchMode=any` Pokud chcete optimalizovat vyhledá přesnost Vzpomínáte, *a* vaši uživatelé často používají `-` operátor v hledání.

## <a name="suffix-operator-"></a>Přípona – operátor `*`

Sufixový operátor je hvězdička. Například `lux*` Vyhledá dokumenty, které mají termín, který začíná `lux`, ignorování případu.  

## <a name="phrase-search-operator--"></a>Operátor vyhledávání frází `" "`

Operátor frázi obklopuje frázi v uvozovkách. Například zatímco `Roach Motel` (bez uvozovek) by vyhledejte dokumenty, které obsahují `Roach` a/nebo `Motel` kdekoli v libovolném pořadí `"Roach Motel"` (pomocí nabídky) bude porovnávat pouze dokumenty, které obsahují tento celá fráze dohromady a v pořadí (Analýza textu je relevantní).

## <a name="precedence-operator--"></a>Přednost operátoru `( )`

Přednost operátoru obklopuje řetězce v závorkách. Například `motel+(wifi | luxury)` Vyhledá dokumenty, které obsahují termín motel a buď `wifi` nebo `luxury` (nebo obojí). |  

## <a name="escaping-search-operators"></a>Uvozovací znaky operátorů vyhledávání  

 Chcete-li používat vyšší symboly v rámci skutečné hledaný text, by měl být uvozena jsou zpětným lomítkem. Například `luxury\+hotel` termín způsobí `luxury+hotel`. Aby věci jednoduché pro více typické případy, existují dvě výjimky z tohoto pravidla ve kterém není potřeba uvozovací znaky:  

- Operátor NOT `-` pouze musí být uvozen řídicími znaky, pokud je první znak po prázdné znaky, není-li je uprostřed termín. Například `wi-fi` je jeden termín, že identifikátory GUID (například `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`) jsou považovány za jeden token.
- Sufixový operátor `*` musí být uvozena pouze v případě, že je to poslední znak před prázdné znaky, není-li je uprostřed termín. Například `wi*fi` je považován za jeden token.

> [!NOTE]  
>  I když uvození udržuje tokeny společně, analýzu textu může rozdělit je, v závislosti na režimu analýzy. Zobrazit [jazykovou podporu &#40;rozhraní REST API služby Azure Search&#41; ](index-add-language-analyzers.md) podrobnosti.  

## <a name="see-also"></a>Další informace najdete v tématech  

+ [Hledání dokumentů &#40;rozhraní REST API služby Azure Search&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Syntaxe dotazů Lucene](query-lucene-syntax.md)
+ [Syntaxe výrazů OData](query-odata-filter-orderby-syntax.md) 
