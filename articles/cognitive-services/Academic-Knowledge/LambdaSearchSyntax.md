---
title: Syntaxe prohledávání lambda - rozhraní Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Další informace o syntaxe prohledávání Lambda, který můžete použít v rozhraní Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 284f1d90f043e2634e143508e2ab0e98cd309f46
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902684"
---
# <a name="lambda-search-syntax"></a>Syntaxe prohledávání lambda

Každý *lambda* vyhledávací dotaz řetězec popisuje vzor grafu. Dotaz musí mít aspoň jednu počáteční uzel zadání, ze které uzlu grafu začneme průchodu. Chcete-li zadat počáteční uzel, zavolejte *MAG. StartFrom()* metoda a předejte jí ID nejmíň jeden uzel (uzly) nebo dotazu objektu, který určuje omezení hledání. *StartFrom()* metoda má tři přetížení. Provést všechny z nich dva argumenty s druhou je volitelné. První argument může být dlouhé celé číslo, vyčíslitelné kolekce dlouhé celé číslo nebo řetězec JSON představující objekt se stejnou sémantiku jako v *json* hledání:
```
StartFrom(long cellid, IEnumerable<string> select = null)
StartFrom(IEnumerable<long> cellid, IEnumerable<string> select = null)
StartFrom(string queryObject, IEnumerable<string> select = null)
```

Proces vytváření vyhledávacího dotazu lambda je procesem z jednoho uzlu do jiného. Chcete-li určit typ hraničními zařízeními a provede, použijte *FollowEdge()* a předejte mu typy požadované edge. *FollowEdge()* přijímá libovolný počet řetězcových argumentů:
```
FollowEdge(params string[] edgeTypes)
```
> [!NOTE]
> Záleží nám na nejsou typy edge(s) dodržovat, jednoduše vynechejte *FollowEdge()* mezi dvěma uzly: dotaz provede všechny možné okraje mezi těmito dvěma uzly.

Lze zadat možné provádět na uzlu prostřednictvím akce procházení *VisitNode()*, to znamená, zda chcete zastavit na tomto uzlu a jako výsledek vrátit aktuální cestu nebo pokračovat v průzkumu grafu.  Typ výčtu *akce* definuje dva typy akcí: *Action.Return* a *Action.Continue*. Můžete předat tyto hodnoty výčtu přímo do *VisitNode()*, nebo můžete zkombinovat s bitovým – a operátor 'a'. Když dvě akce se zkombinují, znamená to, provedou se obě akce. Poznámka: Nepoužívejte bitový- nebo operátor ' |' na akce. To způsobí, že dotaz ukončit bez vrácení cokoli. Přeskakuje se *VisitNode()* mezi dvěma *FollowEdge()* volání způsobí, že dotaz tak, aby bezpodmínečně prozkoumat graf po přicházejících u uzlu.

```
VisitNode(Action action, IEnumerable<string> select = null)
```

Pro *VisitNode()*, jsme také můžete předat ve výrazu lambda typu *výraz\<Func\<uzlů, akce\>\>*, který by *Uzlů* a vrátí procházení akce:

```
VisitNode(Expression<Func<INode, Action>> action, IEnumerable<string> select = null)
```

## <a name="inode"></a>*Uzlů* 

*Uzlů* poskytuje *jen pro čtení* rozhraní a několik integrovaných pomocných funkcí na uzlu přístupu k datům. 

### <a name="basic-data-access-interfaces"></a>Rozhraní pro přístup k základní data

##### <a name="long-cellid"></a>dlouhé CellID

64-bit ID uzlu. 

##### <a name="t-getfieldtstring-fieldname"></a>T GetField\<T\>(řetězec fieldName)

Získá hodnotu zadané vlastnosti. *T* je požadovaný typ, který by měla být interpretován jako pole. Automatické přetypování se provedou, pokud požadovaný typ nejde implicitně převést z typu pole. Poznámka: Pokud je vlastnost s více hodnotami, *GetField\<řetězec\>*  způsobí, že seznam se musí serializovat do řetězce formátu Json ["hodnota1", "hodnota2",...]. Pokud vlastnost neexistuje, bude vyvolat výjimku a zrušení aktuální průzkum grafu.

##### <a name="bool-containsfieldstring-fieldname"></a>BOOL ContainsField (název pole řetězce)

Určuje, jestli pole s daným názvem existuje v aktuálním uzlu.

##### <a name="string-getstring-fieldname"></a>získat řetězec (řetězec fieldName)

Funguje jako *GetField\<řetězec\>(fieldName)*. Ale nevyvolá výjimky, pokud pole není nalezeno, místo toho vrátí prázdný řetězec.

##### <a name="bool-hasstring-fieldname"></a>má BOOL (název pole řetězce)

Sdělí, jestli se daná vlastnost existuje v aktuálním uzlu. Stejné jako *ContainsField(fieldName)*.

##### <a name="bool-hasstring-fieldname-string-value"></a>má BOOL (fieldName řetězec, řetězec)

Informuje, pokud má vlastnost zadanou hodnotu. 

##### <a name="int-countstring-fieldname"></a>počet int (název pole řetězce)

Získáte počet hodnot dané vlastnosti. Pokud vlastnost buď neexistuje, vrátí hodnotu 0.

### <a name="built-in-helper-functions"></a>Integrované pomocné funkce

##### <a name="action-returnifbool-condition"></a>Akce return_if (podmínka bool)

Vrátí *Action.Return* Pokud podmínka není *true*. Pokud je podmínka *false* a tento výraz není připojený k jiné akce pomocí logické bitové – a operátorem, průzkum grafu se přeruší.

##### <a name="action-continueifbool-condition"></a>Akce continue_if (podmínka bool)

Vrátí *Action.Continue* Pokud podmínka není *true*. Pokud je podmínka *false* a tento výraz není připojený k jiné akce pomocí logické bitové – a operátorem, průzkum grafu se přeruší.

##### <a name="bool-dicedouble-p"></a>rozčlenění BOOL (double p)

Generuje náhodné číslo, které je větší než nebo rovna hodnotě 0,0 a menší než 1,0. Tato funkce vrací *true* pouze v případě, že číslo je menší než nebo rovna *p*.

Ve srovnání s *json* vyhledávání, *lambda* je více výrazovými možnostmi vyhledávání: lambda výrazy jazyka C# můžete přímo použít k určení vzory dotazů. Tady jsou dva příklady.

```
MAG.StartFrom(@"{
    type  : ""ConferenceSeries"",
    match : {
        FullName : ""graph""
    }
}", new List<string>{ "FullName", "ShortName" })
.FollowEdge("ConferenceInstanceIDs")
.VisitNode(v => v.return_if(v.GetField<DateTime>("StartDate").ToString().Contains("2014")),
        new List<string>{ "FullName", "StartDate" })
```

```
MAG.StartFrom(@"{
    type  : ""Affiliation"",
    match : {
        Name : ""microsoft""
    }
}").FollowEdge("PaperIDs")
.VisitNode(v => v.return_if(v.get("NormalizedTitle").Contains("graph") || v.GetField<int>("CitationCount") > 100),
        new List<string>{ "OriginalTitle", "CitationCount" })
```
