---
title: Formát schémat – Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: Další informace o formátu schématu v znalostní báze zkoumání služby (KES) rozhraní API.
services: cognitive-services
author: bojunehsu
manager: cgronlun
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 07f5536641b55aadf9d8b2623bf4797b8dcd7bd5
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129246"
---
# <a name="schema-format"></a>Formát schémat

Schéma je zadáno v souboru JSON, která popisuje atribut strukturu objektů v datovém souboru služby použít k vytvoření indexu.  Pro každý atribut schématu Určuje název, datový typ, volitelné operace a volitelné synonyma seznamu.  Objekt může mít 0 nebo více hodnot každého atributu.  Níže je zjednodušený příklad z akademického publikace domény:

``` json
{
  "attributes":[
    {"name":"Title", "type":"String"},
    {"name":"Year", "type":"Int32"},
    {"name":"Author", "type":"Composite"},
    {"name":"Author.Id", "type":"Int64", "operations":["equals"]},
    {"name":"Author.Name", "type":"String"},
    {"name":"Author.Affiliation", "type":"String"},
    {"name":"Keyword", "type":"String", "synonyms":"Keyword.syn"}
  ]
}
```

Názvy atributů jsou malá a velká písmena identifikátorů, které začínají písmenem a obsahovat jenom písmena (A – Z), číslice (0 – 9) a podtržítko (\_).  Atribut vyhrazené "logprob" slouží k určení pravděpodobnosti relativní přirozený protokolu mezi objekty.

## <a name="attribute-type"></a>Typ atributu

Tady je seznam podporovaných atributů datové typy:

| Typ | Popis | Operace | Příklad: |
|------|-------------|------------|---------|
| Řetězec | Řetězec (1-1024 znaků). | je rovno, starts_with | "hello world" |
| Datový typ Int32 | 32bitové celé číslo se znaménkem | je rovno, starts_with, is_between | 2016 |
| Int64 | 64bitové celé číslo se znaménkem | je rovno, starts_with, is_between | 9876543210 |
| Double | Dvojité přesnosti s plovoucí desetinnou čárkou | je rovno, starts_with, is_between | 1.602e-19 |
| Datum | Datum (1400-01-01 do 9999-12-31) | je rovno, is_between | "2016-03-14. |
| Guid | Globálně jedinečný identifikátor | rovná se | "602DD052-CC47-4B23-A16A-26B52D30C05B" |
| Objekt blob | Interně komprimovaných dat neindexovanou | *None* | "Umožněte každé osobě a každá organizace na celém světě, aby dokázali víc." |
| Složené | Složení více dílčích atributů| *–* | {"Name": "harry shum", "Umístění": "microsoft"} |

Atributy řetězec se používá k reprezentování řetězcové hodnoty, které se mohou objevit jako součást příkazu jazyka uživatele.  Podporují spouštění přesnou shodu *rovná* operace, stejně jako *starts_with* operace pro dotaz dokončení scénáře, jako je například porovnávání "micros" s "microsoft".  Velká a malá písmena a přibližné vzorů pro zpracování pravopisné chyby bude podporovat v budoucí verzi.

Datový typ Int32 nebo Int64/Double atributy se používají k vyjádření číselné hodnoty.  *Is_between* operace umožňuje podporu nerovnosti (lt, le, gt, ge) v době běhu.  *Starts_with* operace podporuje scénáře dokončení dotazu, jako je například porovnávání "20" s "2016" nebo "3". s "3.14".

Datum atributy se používají k efektivní kódování hodnoty data.  *Is_between* operace umožňuje podporu nerovnosti (lt, le, gt, ge) v době běhu.
  
Identifikátor GUID atributy se používají k efektivní představují hodnoty GUID s podporou výchozí *rovná* operace.

Atributy objektu BLOB se používají k efektivní kódování potenciálně velkých objemů dat objektů blob pro vyhledávání v modulu runtime z odpovídajícího objektu bez podpory pro všechny operace indexování na základě obsahu objektu blob hodnoty.

### <a name="composite-attributes"></a>Složený atributy

Složený atributy se používají k vyjádření seskupení hodnot atributu.  Název atributu každý dílčí začíná název složený atributu, za nímž následuje ".".  Jako objekt JSON obsahující vnořené hodnoty, které jsou zadány hodnoty pro složené atributy.  Složený atributů může mít více hodnot objektu.  Složené atributy však nemusí mít dílčí atributy, které představují samy o sobě složené atributy.

V předchozím příkladu academic publikace to umožňuje službě se dotázat na Paper podle "harry shum" během jeho společnost "microsoft".  Bez složených atributy můžete službu pouze dotazů pro papíry, kde jedním z autorů je "harry shum" a jedním z autorů je společnost "microsoft".  Další informace najdete v tématu [složené dotazy](SemanticInterpretation.md#composite-function).

## <a name="attribute-operations"></a>Operace atributů

Ve výchozím nastavení každý atribut je indexováno pro podporu všech operací, které jsou k dispozici pro datový typ atributu.  Pokud konkrétní operace se nevyžaduje, můžete sadu indexované operací explicitně zadán ke zmenšení velikosti indexu.  V následujícím fragmentu kódu z výše uvedeného příkladu schématu atribut Author.Id indexování pro podporu pouze *rovná* operace, ale ne další *starts_with* a *is_between*  operace pro atributy Int32.
```json
{"name":"Author.Id", "type":"Int32", "operations":["equals"]}
```

Když je atribut odkazovat uvnitř gramatiku, *starts_with* operace musí být zadaná ve schématu v pořadí pro službu vygenerovat dokončování částečné dotazy.  

## <a name="attribute-synonyms"></a>Atribut synonyma

Je často žádoucí odkazovat na konkrétní řetězcovou hodnotu atributu synonymum.  Například uživatelé mohou odkazovat na "Microsoft" jako "MSFT" nebo "MS".  V těchto případech definice atributu můžete zadat název souboru schématu je umístěn ve stejném adresáři jako soubor schématu.  Každý řádek v souboru synonymum představuje synonymum položku v následujícím formátu JSON: `["<canonical>", "<synonym>"]`.  V příkladu schématu "AuthorName.syn" představuje soubor JSON, který obsahuje hodnoty synonymum pro atribut Author.Name.

`{"name":"Author.Name", "type":"String", "synonyms":"AuthorName.syn"}`


Kanonická hodnota může mít více synonym.  Více kanonických hodnot může sdílet běžných synonym.  V takových případech bude služba se vyřešit nejednoznačnost prostřednictvím více interpretací.  Níže je příklad souboru synonyma AuthorName.syn výše uvedeného schématu odpovídající:
```json
["harry shum","heung-yeung shum"]
["harry shum","h shum"]
["henry shum","h shum"]
...
```
