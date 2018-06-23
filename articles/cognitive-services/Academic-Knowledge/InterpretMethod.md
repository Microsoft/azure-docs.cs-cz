---
title: Interpretace metoda v rozhraní Academic Knowledge API | Microsoft Docs
description: Pomocí této metody Interpret vrátí formátovaný interpretace řetězců dotazů uživatele na základě dat Academic grafu a Academic gramatika v kognitivní služby společnosti Microsoft.
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: alch
ms.openlocfilehash: a46c792f14fabf6562666d1067ef880bd505741f
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342387"
---
# <a name="interpret-method"></a>Interpretace – metoda

**Interpretovat** REST API trvá koncový uživatel řetězec dotazu (tj, dotaz zadá uživatel aplikace) a vrátí formátovaný interpretace záměru uživatele na základě dat Academic grafu a Academic gramatika.

Zajistit interaktivní prostředí, můžete tuto metodu lze volat opakovaně po každý znak zadané uživatelem. V takovém případě byste měli nastavit **dokončení** parametr 1 pro zapnutí automatického dokončování návrhy. Pokud vaše aplikace nemusí automatické doplňování, byste měli nastavit **dokončení** parametru na hodnotu 0.

**Koncový bod REST:**

    https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?

## <a name="request-parameters"></a>Parametry žádosti

Název     | Hodnota | Povinné?  | Popis
---------|---------|---------|---------
**dotaz**    | Textový řetězec | Ano | Dotaz zadaný uživatelem.  Pokud dokončení nastavena na hodnotu 1, dotaz, bude vyhodnocen jako předponu pro generování návrhy automatické dokončování dotazů.        
**model**    | Textový řetězec | Ne  | Název modelu, který chcete dotaz.  V současné době má výchozí hodnotu *nejnovější*.        
**dokončení** | 0 nebo 1 | Ne<br>Výchozí: 0  | 1 znamená, že jsou automatické doplňování návrzích vydány na základě dat gramatika a graf.         
**počet**    | Číslo | Ne<br>Výchozí: 10 | Maximální počet interpretace vrátit.         
**Posun**   | Číslo | Ne<br>Výchozí: 0  | Index prvního interpretace vrátit. Například *počet = 2 & Posun = 0* vrátí interpretace 0 a 1. *Počet = 2 & Posun = 2* vrátí interpretace 2 a 3.       
**časový limit**  | Číslo | Ne<br>Výchozí: 1000 | Časový limit v milisekundách. Vrátí se pouze interpretace nalezen předtím, než je časový limit uplynul.
<br>
  
## <a name="response-json"></a>Odpověď (JSON)
Název     | Popis
---------|---------
**dotaz** |*Dotazu* parametr z požadavku.
**interpretace** |Pole odpovídající vstup uživatele s gramatiky 0 nebo více různých způsobů.
**.logprob interpretace [x]**  |Relativní přirozené protokolu pravděpodobnost interpretaci. Vyšší hodnoty budou s větší pravděpodobností.
**.parse interpretace [x]**  |Řetězec XML, který ukazuje, jak se interpretují jednotlivých součástí dotazu.
**.rules interpretace [x]**  |Pole 1 nebo více pravidla definovaná v gramatiku, které byly vyvolány během interpretace. Academic Knowledge API bude vždy 1 pravidlo.
**interpretace [.název .rules [y] x]**  |Název pravidla.
**interpretace [.output .rules [y] x]**  |Výstupní pravidlo.
**interpretace [.output.type .rules [y] x]** |Datový typ výstupu pravidla.  Academic Knowledge API bude vždy "dotaz".
**interpretace [.output.value .rules [y] x]**  |Výstupní pravidlo. Academic Knowledge API to je řetězec dotazu výraz, který se dá předat do hodnocení a calchistogram metody.
**byl zrušen** | Hodnota TRUE, pokud vypršel časový limit požadavku.

<br>
#### <a name="example"></a>Příklad:
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/interpret?query=papers by jaime&complete=1&count=2
 ```
<br>Níže uvedené odpovědi obsahuje horních dvou (z důvodu parametr *počet = 2*) pravděpodobně interpretace, která dokončí částečné uživatelský vstup *dokumenty Paper podle jaime*: *dokumenty Paper podle jaime teevan*  a *dokumenty Paper podle jaime zelená*.  Dokončování dotazů služby generované místo pouze s přesnou odpovídá od autora *jaime* vzhledem k tomu, že zadaný požadavek *dokončení = 1*. Všimněte si, že kanonické hodnoty *j l zelená* shodná prostřednictvím synonymum *jamie zelená*, jak je uvedeno v analýzy.


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
<br>Pro načtení entity výsledky pro interpretaci, použijte *output.value* z **interpretovat** rozhraní API a předejte, který do **vyhodnotit** rozhraní API prostřednictvím *expr*  parametr. V tomto příkladu dotaz pro první výkladu je: 
```
evaluate?expr=Composite(AA.AuN=='jaime teevan')
```
 