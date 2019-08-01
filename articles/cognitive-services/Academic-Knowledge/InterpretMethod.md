---
title: Interpretovat metodu – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Pomocí metody interpretace můžete vracet formátované interprety řetězců uživatelských dotazů na základě dat akademického grafu a akademické gramatiky v Microsoft Cognitive Services.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: d960aff109e0eca70cb87463770620093e563f63
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706678"
---
# <a name="interpret-method"></a>Interpretovat metodu

**Interpretace** REST API přebírá řetězec dotazu koncového uživatele (tj. dotaz zadaný uživatelem vaší aplikace) a vrací naformátované interpretace záměru uživatele na základě dat akademického grafu a školní gramatiky.

Pro zajištění interaktivního prostředí můžete zavolat tuto metodu opakovaně za každý znak zadaný uživatelem. V takovém případě byste měli nastavit parametr **Complete** na hodnotu 1, aby se povolily automatické dokončování návrhů. Pokud vaše aplikace nepotřebuje automatické dokončování, měli byste nastavit **úplný** parametr na 0.

**Koncový bod REST:**

    https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?

## <a name="request-parameters"></a>Parametry žádosti

Name     | Value | Požadováno?  | Popis
---------|---------|---------|---------
**query**    | Textový řetězec | Ano | Dotaz zadal uživatel.  Pokud je kompletní nastavení nastaveno na hodnotu 1, dotaz bude interpretován jako předpona pro generování návrhů automatického dokončování dotazů.        
**model**    | Textový řetězec | Ne  | Název modelu, který chcete dotazovat.  V současné době je hodnota standardně *nejnovější*.        
**Plňte** | 0 nebo 1 | Ne<br>Výchozí hodnota: 0  | 1 znamená, že se návrhy automatického dokončování generují na základě gramatických dat a dat grafu.         
**count**    | Number | Ne<br>výchozí: 10 | Maximální počet interpretů, které se mají vrátit.         
**polohy**   | Number | Ne<br>Výchozí hodnota: 0  | Index první interpretace, která se má vrátit Například *Count = 2 & offset = 0* vrátí interprety 0 a 1. *Count = 2 & offset = 2* vrátí interprety 2 a 3.       
**prodlev**  | Number | Ne<br>Výchozí hodnota: 1000 | Časový limit v milisekundách Budou vráceny pouze interprety, které byly nalezeny před uplynutím časového limitu.

<br>
  
## <a name="response-json"></a>Odpověď (JSON)

Name     | Popis
---------|---------
**query** |Parametr *dotazu* z požadavku.
**interpretace** |Pole o 0 nebo více různých způsobech porovnání vstupu uživatele proti gramatice.
**interpretations[x].logprob**  |Relativní pravděpodobnost přirozeného protokolu. Větší hodnoty jsou pravděpodobnější.
**interpretations[x].parse**  |Řetězec XML, který ukazuje, jakým způsobem byly jednotlivé části dotazu interpretovány.
**výklady [x]. Rules**  |Pole s 1 nebo více pravidly definovaná v gramatice, které byly vyvolány během výkladu. U Academic Knowledge API bude vždy 1 pravidlo.
**interpretace [x]. rules [y]. Name**  |Název pravidla
**interpretations[x].rules[y].output**  |Výstup pravidla
**interpretations[x].rules[y].output.type** |Datový typ výstupu pravidla  U Academic Knowledge API to bude vždycky "dotaz".
**interpretations[x].rules[y].output.value**  |Výstup pravidla Pro Academic Knowledge API se jedná o řetězec výrazu dotazu, který lze předat metodám Evaluate a calchistogram.
**aborted** | Hodnota true, pokud vypršel časový limit žádosti

<br>

#### <a name="example"></a>Příklad:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime&complete=1&count=2
 ```
<br>Níže uvedená odpověď obsahuje horní dvě (vzhledem k *počtu parametrů = 2*) nejpravděpodobnější interpretace, která dokončí částečnou vstupní *dokumentaci uživatele o Espinosy*: *Paper podle Espinosy teevan* a *Paper podle Espinosy zeleně*.  Služba vygenerovala dokončování dotazů místo toho, aby zvažuje pouze přesné shody pro autora *Espinosy* , protože zadaná žádost je *dokončena = 1*. Všimněte si, že kanonická hodnota *j l zelený* se shodovala přes synonymum *Novák zelenou*, jak je uvedeno v analýze.


```JSON
{
  "query": "papers by jaime",
  "interpretations": [
    {
      "logprob": -12.728,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#AA.AuN\">jaime teevan</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(AA.AuN=='jaime teevan')"
          }
        }
      ]
    },
    {
      "logprob": -12.774,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#AA.AuN\" canonical=\"j l green\">jaime green</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(AA.AuN=='j l green')"
          }
        }
      ]
    }
  ]
}
```  
<br>Chcete-li načíst výsledky entit pro výklad, použijte příkaz *Output. Value* z rozhraní API pro **interpretaci** a předejte ho do rozhraní **Evaluate** API pomocí parametru *expr* . V tomto příkladu je dotaz pro první interpretaci: 
```
evaluate?expr=Composite(AA.AuN=='jaime teevan')
```
 
