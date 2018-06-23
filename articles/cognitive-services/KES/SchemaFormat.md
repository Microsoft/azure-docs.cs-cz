---
title: Formát schématu v rozhraní API služby zkoumání znalostní báze | Microsoft Docs
description: Další informace o formátu schématu v znalostní báze zkoumání služby (KES) rozhraní API v kognitivní služby.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 3009392a5acb12a8f4df3d30a2cbe5e74f2172fc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342479"
---
# <a name="schema-format"></a>Formát schématu
Schéma je zadán v souboru JSON, který popisuje strukturu atribut objektů v datovém souboru použít k vytvoření indexu.  Schéma pro každý atribut určuje název, datový typ, volitelné operace a volitelné synonyma seznamu.  Objekt může mít 0 nebo více hodnot každý atribut.  Níže je zjednodušená příklad z domény academic publikace:

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

Názvy atributů jsou malá a velká písmena identifikátorů, které začínat písmenem a obsahovat jenom písmena (A-Z), čísla (0-9) a podtržítko (\_).  Atribut vyhrazené "logprob" slouží k určení relativních přirozené protokolu pravděpodobností mezi objekty.

## <a name="attribute-type"></a>Typ atributu
Níže je seznam podporovaných atributů datové typy:

| Typ | Popis | Operace | Příklad: |
|------|-------------|------------|---------|
| Řetězec | Řetězec (1-1024 znaků) | rovná se, starts_with | "hello, world" |
| Int32 | 32bitové celé číslo se znaménkem | rovná, starts_with, is_between | 2016 |
| Int64 | 64bitové celé číslo se znaménkem | rovná, starts_with, is_between | 9876543210 |
| Double | Dvojitá přesnost s plovoucí desetinnou čárkou | rovná, starts_with, is_between | 1.602e-19 |
| Datum | Datum (1400-01-01 do 9999-12-31) | rovná se, is_between | : 2016-03-14. |
| Guid | Globálně jedinečný identifikátor | rovná se | "602DD052-CC47-4B23-A16A-26B52D30C05B" |
| Objekt blob | Interně komprimovaná data neindexované | *None* | "Umožnit všech osob a každá organizace na světě víc toho Dosáhněte" |
| Složené | Složení více podřízených atributů| *–* | {"Název": "harry shum", "Přidružení": "microsoft"} |

Atributy řetězec se používá k reprezentování řetězcové hodnoty, které se mohou objevit jako součást dotazu uživatele.  Podporují spouštění na přesnou shodu *rovná* operace, a taky *starts_with* operace pro scénáře dokončení dotazu, jako je například odpovídající "micros" s "microsoft".  Velká a malá písmena a přibližné odpovídající se budou zpracovávat chyby pravopisu bude podporovaný v budoucí verzi.

Int32, Int64 nebo dvojitou atributy se používají k vyjádření číselné hodnoty.  *Is_between* operace umožňuje podporu nerovnosti (lt, le, gt, ge) v době běhu.  *Starts_with* operace podporuje scénáře dokončení dotazu, jako je například odpovídající pomocí "2016", "20" nebo "3". s "3.14".

Atributy data se používají k efektivní kódování hodnot data.  *Is_between* operace umožňuje podporu nerovnosti (lt, le, gt, ge) v době běhu.
  
Atributy identifikátor GUID slouží k efektivní představují hodnoty GUID s podporou výchozí *rovná* operaci.

Objekt BLOB atributy se používají ke efektivní kódování objekty BLOB potenciálně velkého množství dat pro vyhledávání runtime z odpovídající objektu bez podpory pro všechny operace indexování na základě obsahu hodnot objektů blob.

### <a name="composite-attributes"></a>Složené atributy
Složené atributy se používají k vyjádření seskupení hodnot atributu.  Název každé dílčí atributu začíná název složený atributu, za nímž následuje ".".  Jako objekt JSON obsahující vnořené atribut hodnoty, které jsou zadány hodnoty složené atributy.  Složené atributy mohou mít více hodnot objektu.  Složené atributy však nemusí mít dílčí atributy, které jsou sami složené atributy.

V předchozím příkladu academic publikace to umožňuje službě se dotázat na dokumenty Paper podle "harry shum" během jeho na "microsoft".  Bez složené atributy můžete službu pouze dotazů pro dokumenty Paper, kde jeden autorů je "harry shum" a jeden autorů je společnost "microsoft".  Další informace najdete v tématu [složené dotazy](SemanticInterpretation.md#composite-function).

## <a name="attribute-operations"></a>Atribut operací
Ve výchozím nastavení je každý atribut indexovaný pro podporu všechny operace, které jsou k dispozici pro datový typ atributu.  Pokud konkrétní operaci není potřeba, můžete sadu indexované operací explicitně určena ke snížení velikosti indexu.  V následujícím fragmentu kódu z výše uvedeného příkladu schématu je atribut Author.Id indexovaný pro podporu pouze *rovná* operaci, ale ne další *starts_with* a *is_between*  operací pro atributy Int32.
```json
{"name":"Author.Id", "type":"Int32", "operations":["equals"]}
```

Když je atribut uvnitř gramatiku, *starts_with* operaci je třeba zadat ve schématu v pořadí pro službu, kterou chcete vygenerovat dokončených z částečné dotazů.  

## <a name="attribute-synonyms"></a>Atribut synonyma
Je často žádoucí synonymum pro referenci konkrétní řetězcovou hodnotu atributu.  Například uživatelé mohou odkazovat na "Microsoft" jako "MSFT" nebo "MS".  V těchto případech definice atributu můžete zadat název souboru schématu, který je umístěný ve stejném adresáři jako soubor schématu.  Každý řádek v souboru synonymum představuje položku synonymum ve formátu JSON: `["<canonical>", "<synonym>"]`.  "AuthorName.syn" v příkladu schématu, je soubor JSON, který obsahuje hodnoty synonymum pro atribut Author.Name.

`{"name":"Author.Name", "type":"String", "synonyms":"AuthorName.syn"}`


Kanonické hodnoty může mít několik synonyma.  Běžné synonymum může sdílet více kanonických hodnot.  V takových případech bude služba vyřešit nejednoznačnosti prostřednictvím více interpretace.  Níže je příklad AuthorName.syn synonyma souboru výše uvedeného schématu odpovídající:
```json
["harry shum","heung-yeung shum"]
["harry shum","h shum"]
["henry shum","h shum"]
...
```
