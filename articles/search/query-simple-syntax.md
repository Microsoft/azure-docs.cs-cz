---
title: Jednoduchá syntaxe dotazů – Azure Search
description: Odkaz na jednoduchou syntaxi dotazů, která se používá pro dotazy fulltextového vyhledávání v Azure Search.
services: search
ms.service: search
ms.topic: conceptual
ms.date: 08/08/2019
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
ms.openlocfilehash: 41a9c87731dcb6a2cb31e9120a0170b892c58b6f
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68884095"
---
# <a name="simple-query-syntax-in-azure-search"></a>Jednoduchá syntaxe dotazů v Azure Search
Azure Search implementuje dva jazyky dotazů založené na Lucene: [Jednoduchý analyzátor dotazů](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) a [analyzátor dotazů Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html) V Azure Search syntaxe jednoduchého dotazu vylučuje možnosti přibližné/sklony.  

> [!NOTE]  
>  Azure Search poskytuje alternativní [syntaxi dotazů Lucene](query-lucene-syntax.md) pro složitější dotazy. Další informace o architektuře analýzy dotazů a výhodách jednotlivých syntaxí najdete [v tématu Jak funguje fulltextové vyhledávání v Azure Search](search-lucene-query-architecture.md).

## <a name="how-to-invoke-simple-parsing"></a>Postup vyvolání jednoduché analýzy

Výchozí hodnota je jednoduchá syntaxe. Volání je nezbytné pouze v případě, že obnovuje syntaxi z úplného na jednoduchou. K explicitnímu nastavení syntaxe použijte `queryType` parametr Search. Platné hodnoty zahrnují `simple|full`, s `simple` jako výchozí a `full` pro Lucene. 

## <a name="query-behavior-anomalies"></a>Anomálie chování dotazů

Libovolný text s jednou nebo více výrazy je považován za platný výchozí bod pro provedení dotazu. Azure Search budou odpovídat dokumentům obsahujícím některé nebo všechny z těchto podmínek, včetně všech variant nalezených během analýzy textu. 

Stejně jako u těchto zvuků je jeden aspekt provádění dotazů v Azure Search, který *může* způsobit neočekávané výsledky, větší místo zmenšení výsledků hledání, protože se do vstupního řetězce přidávají další výrazy a operátory. Bez ohledu na to, zda toto rozšíření skutečně probíhá, závisí na zahrnutí operátoru not `searchMode` v kombinaci s nastavením parametru, které určuje, jak není interpretováno v souvislosti s chováním a nebo nebo. V případě, že `searchMode=Any`jsou zadány výchozí hodnoty, a operátor NOT, operace je vypočítána jako akce `"New York" NOT Seattle` nebo, která vrací všechna města, která nejsou v Seattlu.  

Obvykle je pravděpodobnější, že toto chování vidíte ve vzorcích interakce s uživateli pro aplikace, které procházejí obsah, kde uživatelé mají větší pravděpodobně zahrnutí operátoru do dotazu, a to na rozdíl od webů elektronického obchodování, které mají více integrovaných navigačních struktur. Další informace naleznete v tématu [operátor NOT](#not-operator). 

## <a name="boolean-operators-and-or-not"></a>Logické operátory (a, nebo, NOT) 

Operátory můžete vložit do řetězce dotazu a vytvořit tak bohatou sadu kritérií, proti které jsou nalezeny vyhovující dokumenty. 

### <a name="and-operator-"></a>AND – operátor`+`

Operátor AND je znaménko plus. Vyhledá například `wifi+luxury` dokumenty obsahující obojí `wifi` a `luxury`.

### <a name="or-operator-"></a>OR – operátor`|`

Operátor OR je znak svislého pruhu nebo svislé čáry. Vyhledá například `wifi | luxury` dokumenty obsahující buď `wifi` nebo `luxury` nebo obojí.

<a name="not-operator"></a>

### <a name="not-operator--"></a>NOT – operátor`-`

Operátor NOT je symbol mínus. Vyhledá například `wifi –luxury` dokumenty, které `wifi` mají podmínky a `luxury` /nebo nemají (a/nebo jsou ovládány `searchMode`).

> [!NOTE]  
>  Možnost určuje, zda je výraz s operátorem NOT ANDed nebo ORed s ostatními podmínkami v dotazu při absenci `+` operátoru OR `|`. `searchMode` Odvolání může být nastaveno na hodnotu `any` (výchozí) nebo `all`. `searchMode` Pokud použijete `any`, zvýší se tím odvolání dotazů, včetně dalších výsledků, a ve výchozím nastavení `-` bude interpretována jako "nebo ne". Například `wifi -luxury` se bude shodovat s dokumenty, které buď obsahují termín `wifi` , nebo ty, které neobsahují `luxury`termín. Použijete `all`-li, bude zvýšena přesnost dotazů, včetně méně výsledků, a ve výchozím nastavení bude interpretována jako "a nikoli". Například `wifi -luxury` bude odpovídat dokumentům, které obsahují termín `wifi` a nesmí obsahovat pojem "luxus". To je pravděpodobně intuitivní chování `-` operátoru. Proto byste měli `searchMode=all` zvážit použití `searchMode=any` místo toho, pokud chcete optimalizovat hledání přesnosti místo odvolání `-` *a* vaši uživatelé často používají operátor v hledání.

## <a name="suffix-operator"></a>Operátor přípony

Operátor přípona je hvězdička `*`. Vyhledá například `lux*` dokumenty, které mají termín, který `lux`začíná a ignoruje velká a malá písmena.  

## <a name="phrase-search-operator"></a>Operátor hledání fráze

Operátor fráze uzavře frázi v `" "`uvozovkách. Například když `Roach Motel` (bez uvozovek) bude hledat dokumenty obsahující `Roach` a/nebo `Motel` kdekoli v libovolném pořadí, (s uvozovkami), `"Roach Motel"` budou odpovídat pouze dokumentům, které obsahují tuto celou frázi společně a v této. pořadí (analýza textu se pořád používá).

## <a name="precedence-operator"></a>Priorita – operátor

Operátor priority uzavře řetězec do závorek `( )`. Vyhledá například `motel+(wifi | luxury)` dokumenty obsahující termín Motel a buď `wifi` nebo `luxury` (nebo obojí).  

## <a name="escaping-search-operators"></a>Operátory hledání uvozovacích znaků  

 Aby bylo možné použít výše uvedené symboly jako skutečný text hledaného textu, měly by být uvozeny pomocí zpětného lomítka. Výsledkem `luxury\+hotel` bude například výraz `luxury+hotel`. Aby bylo možné zjednodušit věci z více typických případů, existují dvě výjimky z tohoto pravidla, kde není nutné uvozovací znaky:  

- Pokud se jedná `-` o první znak za prázdným znakem, je třeba, aby operátor NOT byl řídicí, a ne, pokud je uprostřed podmínky. Například `wi-fi` je jeden výraz, zatímco identifikátory GUID ( `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`například) se považují za jeden token.
- Operátor `*` přípony musí být uvozen pouze v případě, že se jedná o poslední znak před prázdným znakem, nikoli v případě, že je uprostřed podmínky. Například `wi*fi` je považován za jeden token.

> [!NOTE]  
>  I když uvozovací znaky zachovává tokeny, analýza textu je může rozdělit v závislosti na režimu analýzy. Podrobnosti najdete v tématu [Podpora &#40;jazyků&#41; Azure Search REST API služby](index-add-language-analyzers.md) .  

## <a name="see-also"></a>Viz také:  

+ [Hledat dokumenty &#40;Azure Search REST API služby&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Syntaxe dotazů Lucene](query-lucene-syntax.md)
+ [Syntaxe výrazů OData](query-odata-filter-orderby-syntax.md) 
