---
title: Syntaxe vyhledávání lambda v Academic Knowledge API | Microsoft Docs
description: Další informace o syntaxe vyhledávání Lambda, které můžete použít v Academic Knowledge API v kognitivní služby společnosti Microsoft.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: f486368e1d0258087091acb846a84b125712db40
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342397"
---
# <a name="lambda-search-syntax"></a>Syntaxe vyhledávání lambda

Každý *lambda* vyhledávací dotaz řetězec popisuje schéma grafu. Dotaz musí mít alespoň jeden počáteční uzel zadání, z které uzlu grafu začneme průchodu. Chcete-li zadat počáteční uzel, zavolejte *MAG. StartFrom()* metoda a předejte jí ID jeden nebo více uzly nebo dotaz objektu, který určuje omezení vyhledávání. *StartFrom()* metoda má tři přetížení. Všechny z nich trvat dva argumenty s druhou je volitelné. První argument může být dlouhé celé číslo, kolekci vyčíslitelná dlouhé celé číslo nebo řetězec představující JSON objekt se stejnou sémantiku jako v *json* vyhledávání:
```
StartFrom(long cellid, IEnumerable<string> select = null)
StartFrom(IEnumerable<long> cellid, IEnumerable<string> select = null)
StartFrom(string queryObject, IEnumerable<string> select = null)
```

Proces zápisu lambda vyhledávací dotaz se vás z jednoho uzlu do jiného. Pokud chcete zadat typ edge vás provede, použijte *FollowEdge()* a předat typy požadované okraj. *FollowEdge()* přebírá libovolný počet řetězcové argumenty:
```
FollowEdge(params string[] edgeTypes)
```
> [!NOTE]
> Pokud není záleží nám typy edge(s) provést, jednoduše vynechat *FollowEdge()* mezi dvěma uzly: dotaz provede všechny možné okraje mezi těmito dvěma uzly.

Lze zadat akce traversal mají být provedeny v uzlu prostřednictvím *VisitNode()*, to znamená, zda chcete zastavit tento uzel a jako výsledek vrátí aktuální cestě nebo pokračovat v průzkumu grafu.  Typ výčtu *akce* definuje dva typy akcí: *Action.Return* a *Action.Continue*. Můžete předat takové hodnotu výčtu přímo do *VisitNode()*, nebo jejich kombinací s bitové- a operátor 'a'. Když dvě akce se zkombinují, znamená to, že obě akce se provedou. Poznámka: Nepoužívejte bitový- nebo operátor ' |' na akce. To způsobí, že dotaz ukončit bez vrácení nic. Přeskočení *VisitNode()* mezi dvěma *FollowEdge()* volání způsobí, že dotaz bezpodmínečně prozkoumat grafu po přicházejících na uzlu.

```
VisitNode(Action action, IEnumerable<string> select = null)
```

Pro *VisitNode()*, můžete také předat ve výrazu lambda typu *výraz\<Func\<uzlů INode, akce\>\>*, což trvá *Uzlů INode* a vrátí traversal akce:

```
VisitNode(Expression<Func<INode, Action>> action, IEnumerable<string> select = null)
```

## <a name="inode"></a>*Uzlů INode* 

*Uzlů INode* poskytuje *jen pro čtení* rozhraní a několik integrovaných pomocných funkcí na uzlu přístup k datům. 

### <a name="basic-data-access-interfaces"></a>Základní data přístup k rozhraní

##### <a name="long-cellid"></a>dlouhé CellID

64bitová verze ID uzlu. 

##### <a name="t-getfieldtstring-fieldname"></a>T GetField\<T\>(název pole řetězce)

Získá hodnotu zadanou vlastnost. *T* je požadovaný typ, který by měla být interpretovat jako pole. Pokud požadovaný typ nelze implicitně převést z typu pole se pokusí automatické typ přetypování. Poznámka: Pokud je vlastnost více hodnot, *GetField\<řetězec\>*  způsobí, že v seznamu k serializaci do řetězce formátu Json ["hodnota1", "hodnota2",...]. Pokud vlastnost neexistuje, bude vyvolána výjimka a zrušení aktuální zkoumání grafu.

##### <a name="bool-containsfieldstring-fieldname"></a>BOOL ContainsField (název pole řetězce)

Určuje, zda pole s daným názvem existuje v aktuálním uzlu.

##### <a name="string-getstring-fieldname"></a>řetězec získat (název pole řetězce)

Funguje jako *GetField\<řetězec\>(název pole)*. Ale pokud pole není nalezeno nevyvolá výjimky, místo toho vrátí prázdný řetězec.

##### <a name="bool-hasstring-fieldname"></a>má BOOL (název pole řetězce)

Určuje, jestli se daná vlastnost existuje v aktuálním uzlu. Stejné jako *ContainsField(fieldName)*.

##### <a name="bool-hasstring-fieldname-string-value"></a>má BOOL (název řetězce, pole řetězcovou hodnotu)

Informuje, pokud má vlastnost předané hodnoty. 

##### <a name="int-countstring-fieldname"></a>počet int (název pole řetězce)

Získáte počet hodnot dané vlastnosti. Pokud vlastnost neexistuje, vrátí hodnotu 0.

### <a name="built-in-helper-functions"></a>Integrovaných pomocných funkcí

##### <a name="action-returnifbool-condition"></a>Akce return_if (bool podmínku)

Vrátí *Action.Return* Pokud je podmínka vyhodnocena *true*. Pokud je podmínka vyhodnocena *false* a tento výraz není spojena s další akce bitové- a operátorem, bude přerušena zkoumání grafu.

##### <a name="action-continueifbool-condition"></a>Akce continue_if (bool podmínku)

Vrátí *Action.Continue* Pokud je podmínka vyhodnocena *true*. Pokud je podmínka vyhodnocena *false* a tento výraz není spojena s další akce bitové- a operátorem, bude přerušena zkoumání grafu.

##### <a name="bool-dicedouble-p"></a>rozčlenění BOOL (dvojité p)

Generuje náhodné číslo, které je větší než nebo rovno 0,0 a menší než 1,0. Funkce vrátí hodnotu *true* pouze v případě, že počet je menší než nebo rovno *p*.

Ve srovnání s *json* hledání, *lambda* vyhledávání je více výrazovou: C# lambda – výrazy dá přímo použít k zadání vzorům dotazů. Tady jsou dva příklady.

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
