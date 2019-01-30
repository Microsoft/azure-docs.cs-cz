---
title: Interpretace metoda – Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: Zjistěte, jak použít metodu interpretaci v znalostní báze zkoumání služby (KES) rozhraní API.
services: cognitive-services
author: bojunehsu
manager: cgronlun
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 4f950734081be6cf76770b1c6cb2feca7efdae70
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55221399"
---
# <a name="interpret-method"></a>interpretace – metoda

*Interpretovat* metoda přebírá řetězec dotazu v přirozeném jazyce a vrátí formátovaný interpretace záměru uživatele na základě dat gramatiky a index.  Poskytnout prostředí pro interaktivní hledání, může tuto metodu volat jako jednotlivé znaky je zadané uživatelem s *kompletní* parametr nastaven na hodnotu 1, aby návrhy automatického dokončování.

## <a name="request"></a>Žádost

`http://<host>/interpret?query=<query>[&<options>]`

Name|Value| Popis
----|----|----
query    | Textový řetězec | Dotazu zadaného uživatelem.  Pokud dokončení je nastavená na 1, dotaz bude interpretovat jako předponu pro generování návrhy automatického dokončování dotazů.        
Dokončení | 0 (výchozí) nebo 1 | 1 znamená, že návrhy automatického dokončování jsou generovány na základě dat gramatiky a index.         
count    | Číslo (výchozí = 10) | Maximální počet interpretace vrátit.         
Posun   | Číslo (výchozí = 0) | Index prvního interpretace vrátit.  Například *počet = 2 & UN přenosu = 0* vrátí interpretace 0 a 1. *Počet = 2 & UN přenosu = 2* vrátí interpretace 2 a 3.       
timeout  | Číslo (výchozí = 1000) | Časový limit v milisekundách. Jsou vráceny pouze interpretace nalezen předtím, než vypršel časový limit.

Použití *počet* a *posun* parametry, může být velký počet výsledků získávají postupně prostřednictvím více požadavků.

## <a name="response-json"></a>Odpověď (JSON)

JSONPath     | Popis
---------|---------
$.query |*dotaz* parametr z požadavku.
$.interpretations   |Pole 0 nebo více způsobů, jak odpovídají vstupní dotaz vůči gramatiky.
$.interpretations [\*] .logprob   |Relativní protokolu pravděpodobnost výklad (< = 0).  Vyšší hodnoty budou pravděpodobně.
$.interpretations [\*] .parse |Řetězec XML, který ukazuje, jak byl interpretován každá část dotazu.
$.interpretations [\*] .rules |Pole 1 nebo více pravidel definovaných v gramatice vyvolána při interpretaci.
$.interpretations [\*] .rules [\*] .name    |Název pravidla.
$.interpretations[\*].rules[\*].output  |Sémantické výstup pravidla.
$.interpretations[\*].rules[\*].output.type |Datový typ sémantického výstupu.
$.interpretations [\*] .rules [\*]. output.value|Hodnota sémantické výstupu.  
$.aborted | True, pokud vypršel časový limit žádosti.

### <a name="parse-xml"></a>Analyzovat soubor XML

Analýzy XML označí (dokončený) dotazu s informacemi o tom, jak odpovídá proti pravidla v gramatice a atributy v indexu.  Tady je příklad z akademického publikace domény:

```xml
<rule name="#GetPapers">
  papers by 
  <attr name="academic#Author.Name" canonical="heungyeung shum">harry shum</attr>
  <rule name="#GetPaperYear">
    written in
    <attr name="academic#Year">2000</attr>
  </rule>
</rule>
```

`<rule>` Element vymezuje rozsahu v dotazu odpovídající zadané pravidlo podle jeho `name` atribut.  Může být vnořena v případě odkazů pravidlo v gramatice zahrnuje analýzu.

`<attr>` Element vymezuje rozsahu v dotazu odpovídající atribut index určené jeho `name` atribut.  Při porovnávání zahrnuje synonym ve vstupní dotaz `canonical` atribut bude obsahovat Kanonická hodnota odpovídající synonymum z indexu.

## <a name="example"></a>Příklad:

V příkladu academic publikace vrátí následující požadavek až 2 návrhy automatického dokončování pro dotaz předponu "Paper podle jaime":

`http://<host>/interpret?query=papers by jaime&complete=1&count=2`

Odpověď obsahuje dva ("počet = 2") pravděpodobně interpretace, které dokončení částečné dotazu "Paper podle jaime": "papíry podle jaime teevan" a "papíry zelenou barvou jaime".  Dokončování dotazů služby generované místo vzhledem k tomu jenom přesné shody autora "jaime", protože zadaný požadavek "dokončení = 1". Všimněte si, že Kanonická hodnota "zelené j l" odpovídá prostřednictvím synonymum "jamie green", jak je uvedeno v analýzy.


```json
{
  "query": "papers by jaime",
  "interpretations": [
    {
      "logprob": -5.615,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#Author.Name\">jaime teevan</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(Author.Name=='jaime teevan')"
          }
        }
      ]
    },
    {
      "logprob": -5.849,
      "parse": "<rule name=\"#GetPapers\">papers by <attr name=\"academic#Author.Name\" canonical=\"j l green\">jaime green</attr></rule>",
      "rules": [
        {
          "name": "#GetPapers",
          "output": {
            "type": "query",
            "value": "Composite(Author.Name=='j l green')"
          }
        }
      ]
    }
  ]
}
```  

Pokud je typ sémantického výstupu "dotazování", jako v následujícím příkladu odpovídajících objektů lze načíst předáním *output.value* k [ *vyhodnotit* ](evaluateMethod.md) API prostřednictvím *expr* parametru.

`http://<host>/evaluate?expr=Composite(AA.AuN=='jaime teevan')`
  
