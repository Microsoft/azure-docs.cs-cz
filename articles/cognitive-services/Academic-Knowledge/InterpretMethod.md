---
title: Interpretace metoda – Academic Knowledge API
titlesuffix: Azure Cognitive Services
description: Pomocí této metody interpretaci vrátí formátovaný interpretace řetězce dotazu na základě dat Academic Graph a akademické gramatiky Microsoft Cognitive Services.
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: e16a772caa5fba632f8544094e2d8b57ed4ca765
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902565"
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
**Model**    | Textový řetězec | Ne  | Název modelu, který chcete dotazovat.  V současné době má výchozí hodnotu *nejnovější*.        
**Dokončení** | 0 nebo 1 | Ne<br>Výchozí: 0  | 1 znamená, že návrhy automatického dokončování jsou generovány na základě dat gramatiky a graf.         
**Počet**    | Číslo | Ne<br>Výchozí: 10 | Maximální počet interpretace vrátit.         
**Posun**   | Číslo | Ne<br>Výchozí: 0  | Index prvního interpretace vrátit. Například *počet = 2 & UN přenosu = 0* vrátí interpretace 0 a 1. *Počet = 2 & UN přenosu = 2* vrátí interpretace 2 a 3.       
**časový limit**  | Číslo | Ne<br>Výchozí: 1000 | Časový limit v milisekundách. Jsou vráceny pouze interpretace nalezen předtím, než vypršel časový limit.
<br>
  
## <a name="response-json"></a>Odpověď (JSON)
Název     | Popis
---------|---------
**Dotaz** |*Dotazu* parametr z požadavku.
**interpretace** |Pole 0 nebo více různých způsobů, jak odpovídající vstup uživatele v porovnání se gramatika.
**.logprob interpretace [x]**  |Pravděpodobnost relativní přirozený logaritmus výkladu. Vyšší hodnoty budou pravděpodobně.
**.parse interpretace [x]**  |Řetězec XML, který ukazuje, jak byl interpretován každá část dotazu.
**.rules interpretace [x]**  |Pole 1 nebo více pravidel definovaných v gramatice, které byly vyvolány během vyhodnocení. Pro rozhraní Academic Knowledge API bude vždy 1 pravidlo.
**interpretace [.name .rules [y] x]**  |Název pravidla.
**interpretace [.output .rules [y] x]**  |Výstup pravidla.
**interpretace [.output.type .rules [y] x]** |Datový typ výstupu pravidla.  Pro rozhraní Academic Knowledge API bude vždy "dotaz".
**interpretace [.output.value .rules [y] x]**  |Výstup pravidla. Pro rozhraní Academic Knowledge API Toto je řetězec výraz dotazu, který může být předán vyhodnotit a calchistogram metody.
**Přerušeno** | True, pokud vypršel časový limit žádosti.

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
 