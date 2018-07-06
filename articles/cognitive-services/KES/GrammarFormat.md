---
title: Formát gramatiky v Knowledge Exploration Service API | Dokumentace Microsoftu
description: Další informace o formát gramatiky v znalostní báze zkoumání služby (KES) rozhraní API ve službě Cognitive Services.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: b64025be2f5a9708162da475c1f037d7f253d2c6
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37865749"
---
# <a name="grammar-format"></a>Formát gramatiky
Gramatika je soubor XML, který určuje vážený sadu, které služba dokáže interpretovat, a také jak se tyto dotazy v přirozeném jazyce jsou přeloženy do sémantického dotazu výrazy dotazů v přirozeném jazyce.  Gramatika syntaxe je založena na [SRGS](http://www.w3.org/TR/speech-grammar/), W3C standard pro gramatiky rozpoznávání řeči, pomocí rozšíření pro podporu integrace dat index a sémantické funkce.

Následující část popisuje jednotlivých syntaktické prvků, které lze použít v gramatika.  Zobrazit [v tomto příkladu](#example) pro dokončení gramatiku, která ukazuje použití těchto prvků v kontextu.

### <a name="grammar-element"></a>Gramatika – Element 
`grammar` Prvek je element nejvyšší úrovně ve specifikaci gramatika XML.  Požadované `root` atribut určuje název kořenové pravidlo definující počáteční bod gramatiky.

```xml
<grammar root="GetPapers">
```

### <a name="import-element"></a>Import – Element
`import` Element importuje definici schématu z externího souboru povolení odkazů na atributy. Element musí být podřízeným na nejvyšší úrovni `grammar` elementu a zobrazí před jakoukoli `attrref` elementy. Požadované `schema` atribut určuje název souboru schématu, které se nachází ve stejném adresáři jako soubor XML gramatiky. Požadovaný `name` prvek určuje schéma alias, který následné `attrref` prvky použít při odkazování na atributy definované v rámci tohoto schématu.

```xml
  <import schema="academic.schema" name="academic"/>
```

### <a name="rule-element"></a>pravidlo – Element
`rule` Element definuje gramatické pravidlo strukturální jednotka, která určuje sadu výrazy dotazů, které systém dokáže interpretovat.  Element musí být podřízeným na nejvyšší úrovni `grammar` elementu.  Požadovaný `id` atribut určuje název pravidla, který se odkazuje z `grammar` nebo `ruleref` elementy.

A `rule` element definuje sadu právní rozšíření.  Text tokeny porovnání vstupní dotaz přímo.  `item` prvky zadejte opakování a alter interpretace pravděpodobnosti.  `one-of` prvky určit alternativní možnosti.  `ruleref` prvky umožňují vytváření složitějších rozšíření z jednodušší ty.  `attrref` prvky umožňují shody s hodnotami atributu z indexu.  `tag` prvky zadejte sémantiku výklad a můžete změnit pravděpodobnost výkladu.

```xml
<rule id="GetPapers">...</rule>
```

### <a name="example-element"></a>Příklad – Element
Volitelný `example` prvek určuje příklad fráze, které může být přijata obsahující `rule` definice.  To může být použita pro dokumentaci a/nebo automatizované testování.

```xml
<example>papers about machine learning by michael jordan</example>
```

### <a name="item-element"></a>položka – Element
`item` Prvek skupiny pořadí konstrukce gramatiky.  Slouží k označení opakování pořadí rozšíření nebo zadat ve spojení s alternativami `one-of` elementu.

Při `item` prvek není podřízeným prvkem `one-of` elementu, opakování uzavřené pořadí lze zadat pomocí přiřazení `repeat` atribut na hodnotu count.  Počet hodnotu "*n*" (kde *n* je celé číslo) označuje, že pořadí se musí vyskytovat přesně *n* časy.  Počet hodnotu "*m*-*n*" umožňuje pořadí mezi *m* a *n* vyprší (včetně).  Počet hodnotu "*m*–" Určuje, že je pořadí musí být aspoň *m* časy.  Volitelný `repeat-logprob` atribut lze použít ke změně pravděpodobnost interpretaci pro každý další opakování za minimální.

```xml
<item repeat="1-" repeat-logprob="-10">...</item>
```

Když `item` zobrazení prvků jako podřízených prvků `one-of` elementu, definují sadu rozšíření alternativy.  V tomto použití nepovinného `logprob` atribut určuje pravděpodobnost relativní protokolu mezi různé možnosti.  Zadaný pravděpodobnost *p* mezi 0 a 1, nelze vypočítat odpovídající pravděpodobnost protokolu protokolu (*p*), kde je log() funkci přirozený protokolu.  Pokud není zadán, `logprob` výchozí hodnota je 0, což nezmění pravděpodobnost výkladu.  Mějte na paměti, že pravděpodobnost protokolu je vždy zápornou hodnotu s plovoucí desetinnou čárkou nebo 0.

```xml
<one-of>
  <item>by</item>
  <item logprob="-0.5">written by</item>
  <item logprob="-1">authored by</item>
</one-of>
```

### <a name="one-of-element"></a>jeden – elementu
`one-of` Prvek Určuje alternativní rozšíření mezi jednou z podřízených `item` elementy.  Pouze `item` prvků může být použit v rámci `one-of` elementu.  Prostřednictvím je možné zadat relativní pravděpodobnosti mezi různé možnosti `logprob` atribut v jednotlivých podřízených `item`.

```xml
<one-of>
  <item>by</item>
  <item logprob="-0.5">written by</item>
  <item logprob="-1">authored by</item>
</one-of>
```

### <a name="ruleref-element"></a>ruleref – Element
`ruleref` Prvek určuje platné rozšíření přes odkazy na jiné `rule` elementu.  Prostřednictvím `ruleref` elementy, může být sestaven složitější výrazy z pravidel pro jednodušší.  Požadovaný `uri` atribut označuje název odkazované `rule` pomocí syntaxe "#*rulename*".  K zachycení sémantické výstupu odkazované pravidlo, použít nepovinný `name` atributy název proměnné, ke kterému je přiřazena sémantické výstup.
 
```xml
<ruleref uri="#GetPaperYear" name="year"/>
```

### <a name="attrref-element"></a>attrref – Element
`attrref` Element odkazuje atribut index, což porovnání hodnoty atributů pozorovaná v indexu.  Požadovaný `uri` atribut určuje název schématu indexu a název atributu pomocí syntaxe "*%{schemaname/*#*%{attrname/*".  Musí existovat předchozí `import` element, který importuje schéma s názvem *%{schemaname/*.  Název atributu se názvu atributu definovaná ve schématu odpovídající.

Kromě odpovídající vstup uživatele `attrref` element také vrátí objekt strukturovaných dotazů jako výstup, který vybere podmnožinu objektů v indexu odpovídající vstupní hodnota.  Použít nepovinný `name` atributy název proměnné, kde se má uložit výstup objektu dotazu.  Objekt dotazu se může skládat s jinými objekty dotaz k vytvoření více složité výrazy.  Zobrazit [sémantického výkladu](SemanticInterpretation.md) podrobnosti.  

```xml
<attrref uri="academic#Keyword" name="keyword"/>
```

#### <a name="query-completion"></a>Dokončení dotazu 
Pro podporu dokončování dotazů při interpretaci částečné uživatelských dotazů, každý Odkazovaný atribut musí obsahovat "starts_with" jako operaci v definici schématu.  Zadaný dotaz předponu uživatele `attrref` se shodují s hodnotami v indexu, které dokončení předponu a yield každou kompletní hodnotu jako samostatné výklad gramatiky.  

Příklady:
* Odpovídající `<attrref uri="academic#Keyword" name="keyword"/>` proti dotaz předponu "dat" generuje jeden výkladu pro dokladů o "databázi", jeden výkladu pro dokladů o "dolování dat" atd.
* Odpovídající `<attrref uri="academic#Year" name="year"/>` proti dotaz předponu "200" generuje jeden výkladu pro dokumentů do "2000", jeden výkladu pro Paper v "2001" atd.

#### <a name="matching-operations"></a>Odpovídající operace
Kromě přesná shoda, vyberte typy atributů také podporu předponu a nerovnost odpovídá prostřednictvím nepovinný `op` atribut.  Pokud žádný objekt v indexu má hodnotu, která odpovídá, cesta gramatiky blokovaný a služba nebude generovat žádné interpretace prochází přes tuto cestu gramatiky.   `op` Atributu výchozí hodnoty na "eq".

```xml
in <attrref uri="academic#Year" name="year"/>
before <attrref uri="academic#Year" op="lt" name="year"/
```

V následující tabulce jsou uvedeny podporované `op` hodnoty pro každý typ atributu.  Jejich používání vyžaduje odpovídající operaci indexu mají být zahrnuty schématu definice atributu.

| Typ atributu | Hodnota OP | Popis | Operace s indexem
|----|----|----|----|
| Řetězec | EQ | Přesná shoda pro řetězec | rovná se |
| Řetězec | starts_with | Porovnání předpony řetězce | starts_with |
| Datový typ Int32, Int64, Double | EQ |  Číselné přesnou shodu | rovná se |
| Datový typ Int32, Int64, Double | lt, le, gt, ge | Porovnání číselných nerovnost (<, < =, >, > =) | is_between |
| Datový typ Int32, Int64, Double | starts_with | Shodu předpony hodnoty v desítkovém zápisu | starts_with |

Příklady:
* `<attrref uri="academic#Year" op="lt" name="year"/>` shoduje se vstupním řetězcem "2000" a vrátí všech dokumentů výhradně zveřejněny do roku 2000.
* `<attrref uri="academic#Year" op="lt" name="year"/>` vstupní řetězec "20" neodpovídá, protože nejsou žádné Paper v indexu publikovaného před rokem 20.
* `<attrref uri="academic#Keyword" op="starts_with" name="keyword"/>` shoduje se vstupním řetězcem "dat" a vrátí jeden interpretace dokladů o "databázi", "dolování dat" atd.  Toto je případ výjimečných používání.
* `<attrref uri="academic#Year" op="starts_with" name="year"/>` shoduje se vstupním řetězcem "20" a vrátí v jedné interpretace Paper zveřejněné v 200 299, 2000-2999, atd.  Toto je případ výjimečných používání.

### <a name="tag-element"></a>značka elementu
`tag` Element určuje, jak má být interpretován cestu gramatiky.  Obsahuje posloupnost příkazy ukončit středníkem.  Příkaz může být přiřazení literál nebo proměnná do jiné proměnné.  Výstup z funkce s 0 nebo více parametrů je také může přiřadit proměnné.  Každý parametr funkce lze zadat pomocí literál nebo proměnná.  Pokud funkce nevrací žádný výstup, přiřazení je vynechán.  Obor proměnné je místní pro obsahovém pravidlu.

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

Každý `rule` v gramatice obsahuje předdefinovanou proměnnou s názvem "out", představující sémantické výstup pravidla.  Její hodnota je vypočítanými vyhodnocením všech sémantických příkazy Procházet podle cesty prostřednictvím `rule` porovnání s uživatelem dotazování vstup.  Hodnota přiřazená k proměnné "out" na konci hodnocení je sémantické výstup pravidla.  Sémantické výstup interpretace uživatelský dotaz proti gramatiku je sémantické výstup kořenové pravidlo.

Některé příkazy mohou změnit pravděpodobnost cestu k interpretaci zavedením posun pravděpodobnost sčítání protokolu.  Některé příkazy může zamítnout výklad úplně, pokud zadané podmínky nejsou splněné.

Seznam podporovaných sémantické funkcí najdete v tématu [sémantické funkce](SemanticInterpretation.md#semantic-functions).

## <a name="interpretation-probability"></a>Interpretace pravděpodobnosti
Pravděpodobnost cestu k interpretaci prostřednictvím gramatiku je kumulativní protokolu pravděpodobnost všech `<item>` elementy a sémantické funkce došlo k cestě.  Popisuje relativní pravděpodobnost, že odpovídající konkrétní vstupní sekvence.

Zadaný pravděpodobnost *p* mezi 0 a 1, nelze vypočítat odpovídající pravděpodobnost protokolu protokolu (*p*), kde je log() funkci přirozený protokolu.  Pomocí protokolu pravděpodobnosti umožňuje systému shromažďují společné pravděpodobnosti cestu k interpretaci prostřednictvím jednoduchého přidání.  Také se vyhnete společné pro takové společné pravděpodobnosti výpočtů s plovoucí desetinnou čárkou podtečení.  Všimněte si, že návrh, pravděpodobnost protokolu je vždy zápornou hodnotu s plovoucí desetinnou čárkou nebo 0, kde vyšší hodnoty znamenat vyšší pravděpodobnost, že.

<a name="example"></a>
## <a name="example"></a>Příklad:
Následuje příklad z akademického publikace domény, který ukazuje různé prvky gramatika XML:

```xml
<grammar root="GetPapers">

  <!-- Import academic data schema-->
  <import schema="academic.schema" name="academic"/>
  
  <!-- Define root rule-->
  <rule id="GetPapers">
    <example>papers about machine learning by michael jordan</example>
    
    papers
    <tag>
      yearOnce = false;
      isBeyondEndOfQuery = false;
      query = All();
    </tag>
  
    <item repeat="1-" repeat-logprob="-10">
      <!-- Do not complete additional attributes beyond end of query -->
      <tag>AssertEquals(isBeyondEndOfQuery, false);</tag>
        
      <one-of>
        <!-- about <keyword> -->
        <item logprob="-0.5">
          about <attrref uri="academic#Keyword" name="keyword"/>
          <tag>query = And(query, keyword);</tag>
        </item>
        
        <!-- by <authorName> [while at <authorAffiliation>] -->
        <item logprob="-1">
          by <attrref uri="academic#Author.Name" name="authorName"/>
          <tag>authorQuery = authorName;</tag>
          <item repeat="0-1" repeat-logprob="-1.5">
            while at <attrref uri="academic#Author.Affiliation" name="authorAffiliation"/>
            <tag>authorQuery = And(authorQuery, authorAffiliation);</tag>
          </item>
          <tag>
            authorQuery = Composite(authorQuery);
            query = And(query, authorQuery);
          </tag>
        </item>
        
        <!-- written (in|before|after) <year> -->
        <item logprob="-1.5">
          <!-- Allow this grammar path to be traversed only once -->
          <tag>
            AssertEquals(yearOnce, false);
            yearOnce = true;
          </tag>
          <ruleref uri="#GetPaperYear" name="year"/>
          <tag>query = And(query, year);</tag>
        </item>
      </one-of>

      <!-- Determine if current parse position is beyond end of query -->
      <tag>isBeyondEndOfQuery = GetVariable("IsBeyondEndOfQuery", "system");</tag>
    </item>
    <tag>out = query;</tag>
  </rule>
  
  <rule id="GetPaperYear">
    <tag>year = All();</tag>
    written
    <one-of>
      <item>
        in <attrref uri="academic#Year" name="year"/>
      </item>
      <item>
        before
        <one-of>
          <item>[year]</item>
          <item><attrref uri="academic#Year" op="lt" name="year"/></item>
        </one-of>
      </item>
      <item>
        after
        <one-of>
          <item>[year]</item>
          <item><attrref uri="academic#Year" op="gt" name="year"/></item>
        </one-of>
      </item>
    </one-of>
    <tag>out = year;</tag>
  </rule>
</grammar>
```
