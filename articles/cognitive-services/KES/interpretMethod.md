---
title: Interpretace metoda v rozhraní API služby zkoumání znalostní báze | Microsoft Docs
description: Zjistěte, jak lze pomocí této metody Interpret v znalostní báze zkoumání služby (KES) rozhraní API v kognitivní služby.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: ef68d98dacf393abf8d030b9312217ea380947d2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342475"
---
# <a name="interpret-method"></a>Interpretace – metoda
*Interpretovat* metoda přebírá řetězec dotazu přirozeného jazyka a vrátí formátovaný interpretace záměru uživatele na základě dat gramatika a index.  Zajistit interaktivní vyhledávání prostředí, může být tato metoda volána, jak každý znak je zadán uživatel s *dokončení* parametr nastavit na hodnotu 1 pro povolení automatického dokončování návrhy.

## <a name="request"></a>Žádost
`http://<host>/interpret?query=<query>[&<options>]`

Název|Hodnota| Popis
----|----|----
query    | Textový řetězec | Dotaz zadaný uživatelem.  Pokud dokončení nastavena na hodnotu 1, dotaz, bude vyhodnocen jako předponu pro generování návrhy automatické dokončování dotazů.        
dokončení | 0 (výchozí) nebo 1 | 1 znamená, že jsou automatické doplňování návrzích vydány na základě dat gramatika a index.         
count    | Číslo (výchozí = 10) | Maximální počet interpretace vrátit.         
Posun   | Číslo (výchozí = 0) | Index prvního interpretace vrátit.  Například *počet = 2 & Posun = 0* vrátí interpretace 0 a 1. *Počet = 2 & Posun = 2* vrátí interpretace 2 a 3.       
timeout  | Číslo (výchozí = 1 000) | Časový limit v milisekundách. Vrátí se pouze interpretace nalezen předtím, než je časový limit uplynul.

Pomocí *počet* a *posun* parametry, může být velkého počtu výsledků získat přírůstkově více požadavků.

## <a name="response-json"></a>Odpověď (JSON)
JSONPath     | Popis
---------|---------
$.query |*dotaz* parametr z požadavku.
$.interpretations   |Pole 0 nebo další způsoby, jak odpovídají vstupní dotaz gramatiky.
$.interpretations [\*] .logprob   |Relativní protokolu pravděpodobnosti rozdělení se výklad (< = 0).  Vyšší hodnoty budou s větší pravděpodobností.
$.interpretations [\*] .parse |Řetězec XML, který ukazuje, jak byl interpretován jednotlivých součástí dotazu.
$.interpretations [\*] .rules |Pole je definováno v gramatika volána při interpretaci 1 nebo více pravidel.
$.interpretations [\*] .rules [\*] .název    |Název pravidla.
$.interpretations [\*] .rules [\*] .output  |Sémantické výstup pravidla.
$.interpretations [\*] .rules [\*]. output.type |Datový typ sémantického výstupu.
$.interpretations [\*] .rules [\*]. output.value|Hodnota sémantického výstupu.  
$.aborted | Hodnota TRUE, pokud vypršel časový limit požadavku.

### <a name="parse-xml"></a>Analyzovat soubor XML
Analýzy XML označí (dokončené) dotaz s informacemi o tom, jak odpovídá proti pravidla v gramatiky a atributů v indexu.  Dole je příklad z domény academic publikace:

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

`<rule>` Element vymezuje rozsahu v dotazu odpovídající zadané pravidlo podle jeho `name` atribut.  Může být vnořena analýzy součástí je pravidlo odkazy v gramatice.

`<attr>` Element vymezuje rozsahu v dotazu odpovídající atribut index určeného jeho `name` atribut.  V případě shody zahrnuje synonymum ve vstupní dotaz `canonical` atribut bude obsahovat kanonický hodnotu odpovídající synonymum z indexu.

## <a name="example"></a>Příklad:
V příkladu academic publikace vrátí následující požadavek až 2 Automatické dokončování návrhy pro dotaz předponu "dokumenty Paper podle jaime":

`http://<host>/interpret?query=papers by jaime&complete=1&count=2`

Odpověď obsahuje horních dvou ("count = 2") pravděpodobně interpretace, která dokončí částečné dotaz "dokumenty Paper podle jaime": "papíry podle jaime teevan" a "papíry podle jaime zelená".  Dokončování dotazů služby generované místo pouze s přesnou odpovídá od autora "jaime", protože zadaný požadavek "dokončení = 1". Všimněte si, že kanonické hodnoty "zelená j l" odpovídá prostřednictvím synonymum "jamie green", jak je uvedeno v analýzy.


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

Pokud je typ sémantického výstupu "dotaz", jako v následujícím příkladě odpovídajících objektů může načíst předávání *output.value* k [ *vyhodnotit* ](evaluateMethod.md) rozhraní API prostřednictvím *expr* parametr.

`http://<host>/evaluate?expr=Composite(AA.AuN=='jaime teevan')`
  
