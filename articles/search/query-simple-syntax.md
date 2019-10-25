---
title: Jednoduchá syntaxe dotazu
titleSuffix: Azure Cognitive Search
description: Referenční informace pro jednoduchou syntaxi dotazů, která se používá pro dotazy fulltextového vyhledávání v Azure Kognitivní hledání.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: fb98be9975de38ec9f65e723e078a1db8755b4ed
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792558"
---
# <a name="simple-query-syntax-in-azure-cognitive-search"></a>Jednoduchá syntaxe dotazů v Azure Kognitivní hledání

Azure Kognitivní hledání implementuje dva jazyky dotazů založené na Lucene: [jednoduchý analyzátor dotazů](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/simple/SimpleQueryParser.html) a [analyzátor dotazů Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). V Azure Kognitivní hledání nezahrnuje jednoduchá syntaxe dotazů možnosti přibližné/sklony.  

> [!NOTE]  
>  Azure Kognitivní hledání poskytuje alternativní [syntaxi dotazů Lucene](query-lucene-syntax.md) pro složitější dotazy. Další informace o architektuře analýzy dotazů a výhodách jednotlivých syntaxí najdete [v tématu Jak funguje fulltextové vyhledávání v Azure kognitivní hledání](search-lucene-query-architecture.md).

## <a name="how-to-invoke-simple-parsing"></a>Postup vyvolání jednoduché analýzy

Výchozí hodnota je jednoduchá syntaxe. Volání je nezbytné pouze v případě, že obnovuje syntaxi z úplného na jednoduchou. K explicitnímu nastavení syntaxe použijte parametr `queryType` Search. Platné hodnoty zahrnují `simple|full`, s `simple` jako výchozí a `full` pro Lucene. 

## <a name="query-behavior-anomalies"></a>Anomálie chování dotazů

Libovolný text s jednou nebo více výrazy je považován za platný výchozí bod pro provedení dotazu. Azure Kognitivní hledání bude odpovídat dokumentům, které obsahují některé nebo všechny z těchto podmínek, včetně všech variant zjištěných během analýzy textu. 

Stejně jako u těchto zvuků je ve službě Azure Kognitivní hledání jeden aspekt provádění dotazů, které *by mohly* vést k neočekávaným výsledkům, a ne zmenšení výsledků hledání, protože se do vstupního řetězce přidávají další výrazy a operátory. Bez ohledu na to, zda toto rozšíření skutečně probíhá, závisí na zahrnutí operátoru NOT v kombinaci s parametrem `searchMode` parametr, který určuje, jak není interpretováno v souvislosti s chováním a nebo nebo. S ohledem na výchozí, `searchMode=Any`a operátor NOT se operace vypočítává jako akce nebo, což znamená, že `"New York" NOT Seattle` vrátí všechna města, která nejsou Seattle.  

Obvykle je pravděpodobnější, že toto chování vidíte ve vzorcích interakce s uživateli pro aplikace, které procházejí obsah, kde uživatelé mají větší pravděpodobně zahrnutí operátoru do dotazu, a to na rozdíl od webů elektronického obchodování, které mají více integrovaných navigačních struktur. Další informace naleznete v tématu [operátor NOT](#not-operator). 

## <a name="boolean-operators-and-or-not"></a>Logické operátory (a, nebo, NOT) 

Operátory můžete vložit do řetězce dotazu a vytvořit tak bohatou sadu kritérií, proti které jsou nalezeny vyhovující dokumenty. 

### <a name="and-operator-"></a>AND – operátor `+`

Operátor AND je znaménko plus. `wifi+luxury` například vyhledá dokumenty obsahující `wifi` a `luxury`.

### <a name="or-operator-"></a>Operátor OR `|`

Operátor OR je znak svislého pruhu nebo svislé čáry. `wifi | luxury` například vyhledá dokumenty, které obsahují buď `wifi` nebo `luxury`, nebo obojí.

<a name="not-operator"></a>

### <a name="not-operator--"></a>Operátor NOT `-`

Operátor NOT je symbol mínus. `wifi –luxury` například vyhledá dokumenty, které mají `wifi` podmínky a/nebo nemají `luxury` (a/nebo jsou ovládány `searchMode`).

> [!NOTE]  
>  Možnost `searchMode` určuje, zda je výraz s operátorem NOT ANDed nebo ORed s ostatními podmínkami v dotazu v případě absence operátoru `+` nebo `|`. Odvolání tohoto `searchMode` lze nastavit buď na `any` (výchozí), nebo na `all`. Použijete-li `any`, dojde k zvýšení navracení dotazů zahrnutím více výsledků a ve výchozím nastavení `-` bude interpretováno jako "nebo ne". Například `wifi -luxury` bude odpovídat dokumentům, které buď obsahují `wifi` nebo které neobsahují termín `luxury`. Použijete-li `all`, bude zvýšena přesnost dotazů včetně méně výsledků a ve výchozím nastavení bude interpretována jako "a nikoli". `wifi -luxury` například bude odpovídat dokumentům, které obsahují termín `wifi` a nesmí obsahovat pojem "luxus". To je pravděpodobně intuitivní chování operátoru `-`. Proto byste měli zvážit použití `searchMode=all` místo `searchMode=any`, pokud chcete optimalizovat hledání přesnosti místo odvolání *a* uživatelé často používají `-` operátora v hledání.

## <a name="suffix-operator"></a>Operátor přípony

Operátor přípona je hvězdička `*`. `lux*` například vyhledá dokumenty s termínem, který začíná na `lux`a bude ignorován případ.  

## <a name="phrase-search-operator"></a>Operátor hledání fráze

Operátor fráze uzavře frázi v uvozovkách `" "`. Například když `Roach Motel` (bez uvozovek) vyhledá dokumenty obsahující `Roach` nebo `Motel` kdekoli v libovolném pořadí, `"Roach Motel"` (s uvozovkami) budou odpovídat pouze dokumentům, které obsahují tuto celou frázi společně a v tomto pořadí (analýza textu je stále platí).

## <a name="precedence-operator"></a>Priorita – operátor

Operátor priority uzavře řetězec do závorek `( )`. `motel+(wifi | luxury)` například vyhledá dokumenty obsahující termín Motel a buď `wifi` nebo `luxury` (nebo obojí).  

## <a name="escaping-search-operators"></a>Operátory hledání uvozovacích znaků  

 Aby bylo možné použít výše uvedené symboly jako skutečný text hledaného textu, měly by být uvozeny pomocí zpětného lomítka. Například `luxury\+hotel` bude mít za následek `luxury+hotel`období. Aby bylo možné zjednodušit věci z více typických případů, existují dvě výjimky z tohoto pravidla, kde není nutné uvozovací znaky:  

- Operátor NOT `-` musí být uvozena řídicím znakem pouze v případě, že se jedná o první znak za prázdným znakem, nikoli pokud je uprostřed podmínky. `wi-fi` je například jedna podmínka; zatímco identifikátory GUID (například `3352CDD0-EF30-4A2E-A512-3B30AF40F3FD`) se považují za jeden token.
- Operátor přípona `*` musí být uvozena pouze v případě, že se jedná o poslední znak před prázdným znakem, nikoli v případě, že je uprostřed podmínky. Například `wi*fi` se považuje za jeden token.

> [!NOTE]  
>  I když uvozovací znaky zachovává tokeny, analýza textu je může rozdělit v závislosti na režimu analýzy. Podrobnosti najdete v tématu [Podpora &#40;jazyka&#41; Azure kognitivní hledání REST API](index-add-language-analyzers.md) .  

## <a name="see-also"></a>Další informace najdete v tématech  

+ [Hledání dokumentů &#40;Azure kognitivní hledání REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) 
+ [Syntaxe dotazů Lucene](query-lucene-syntax.md)
+ [Syntaxe výrazů OData](query-odata-filter-orderby-syntax.md) 
