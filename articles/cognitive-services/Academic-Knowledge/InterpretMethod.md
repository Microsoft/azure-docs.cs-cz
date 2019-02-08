---
title: Interpretace metoda – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Pomocí této metody interpretaci vrátí formátovaný interpretace řetězce dotazu na základě dat Academic Graph a akademické gramatiky Microsoft Cognitive Services.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: 6db9a5b65fc2723af2eae006ad81716e23e52133
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55860520"
---
# <a name="interpret-method"></a>interpretace – metoda

**Interpretovat** rozhraní REST API přijímá koncový uživatel řetězec dotazu (například dotazu zadaného uživatelem, aplikace) a vrátí formátovaný interpretace záměru uživatele na základě dat Academic Graph a akademické gramatiky.

Pro zajištění interaktivní prostředí, můžete volat tuto metodu opakovaně po jednotlivé znaky zadané uživatelem. V takovém případě byste měli nastavit **kompletní** parametr 1 pro zapnutí návrhy automatického dokončování. Pokud aplikace nepotřebuje automatického dokončování, byste měli nastavit **kompletní** parametru na hodnotu 0.

**Koncový bod REST:**

    https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?

## <a name="request-parameters"></a>Parametry žádosti

Název     | Hodnota | Povinné?  | Popis
---------|---------|---------|---------
**Dotaz**    | Textový řetězec | Ano | Dotazu zadaného uživatelem.  Pokud dokončení je nastavená na 1, dotaz bude interpretovat jako předponu pro generování návrhy automatického dokončování dotazů.        
**model**    | Textový řetězec | Ne  | Název modelu, který chcete dotazovat.  V současné době má výchozí hodnotu *nejnovější*.        
**Dokončení** | 0 nebo 1 | Ne<br>Výchozí: 0  | 1 znamená, že návrhy automatického dokončování jsou generovány na základě dat gramatiky a graf.         
**count**    | Číslo | Ne<br>Výchozí: 10 | Maximální počet interpretace vrátit.         
**Posun**   | Číslo | Ne<br>Výchozí: 0  | Index prvního interpretace vrátit. Například *počet = 2 & UN přenosu = 0* vrátí interpretace 0 a 1. *Počet = 2 & UN přenosu = 2* vrátí interpretace 2 a 3.       
**timeout**  | Číslo | Ne<br>Výchozí: 1000 | Časový limit v milisekundách. Jsou vráceny pouze interpretace nalezen předtím, než vypršel časový limit.
<br>
  
## <a name="response-json"></a>Odpověď (JSON)
Název     | Popis
---------|---------
**Dotaz** |*Dotazu* parametr z požadavku.
**interpretace** |Pole 0 nebo více různých způsobů, jak odpovídající vstup uživatele v porovnání se gramatika.
**interpretations[x].logprob**  |Pravděpodobnost relativní přirozený logaritmus výkladu. Vyšší hodnoty budou pravděpodobně.
**interpretations[x].parse**  |Řetězec XML, který ukazuje, jak byl interpretován každá část dotazu.
**interpretations[x].rules**  |Pole 1 nebo více pravidel definovaných v gramatice, které byly vyvolány během vyhodnocení. Pro rozhraní Academic Knowledge API bude vždy 1 pravidlo.
**interpretace [.name .rules [y] x]**  |Název pravidla.
**interpretations[x].rules[y].output**  |Výstup pravidla.
**interpretations[x].rules[y].output.type** |Datový typ výstupu pravidla.  Pro rozhraní Academic Knowledge API bude vždy "dotaz".
**interpretations[x].rules[y].output.value**  |Výstup pravidla. Pro rozhraní Academic Knowledge API Toto je řetězec výraz dotazu, který může být předán vyhodnotit a calchistogram metody.
**aborted** | True, pokud vypršel časový limit žádosti.

<br>
#### <a name="example"></a>Příklad:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime&complete=1&count=2
 ```
<br>Odpověď níže obsahuje dva (z důvodu parametr *počet = 2*) pravděpodobně interpretace, které dokončení částečné uživatelský vstup *Paper podle jaime*: *Paper podle jaime teevan*  a *Paper zelenou barvou jaime*.  Služba vygeneruje dokončování dotazů místo vzhledem k tomu jenom přesné shody pro autora *jaime* vzhledem k tomu, že zadaná žádost *dokončení = 1*. Všimněte si, že Kanonická hodnota *j l zelené* odpovídající prostřednictvím synonymum *jamie zelená*, jak je uvedeno v analýzy.


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
<br>Chcete-li načíst výsledky entit pro výklad, použijte *output.value* z **interpretovat** rozhraní API a předat ho do **vyhodnotit** API prostřednictvím *výraz*  parametru. V tomto příkladu dotaz pro první výkladu je: 
```
evaluate?expr=Composite(AA.AuN=='jaime teevan')
```
 
