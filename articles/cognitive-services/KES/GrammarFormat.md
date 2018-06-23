---
title: Gramatika formátu v rozhraní API služby zkoumání znalostní báze | Microsoft Docs
description: Další informace o formátu gramatika v znalostní báze zkoumání služby (KES) rozhraní API v kognitivní služby.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 27202379b8c36696a380049336229cac040b0108
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342531"
---
# <a name="grammar-format"></a>Formát – gramatika
Gramatiky je soubor XML, který určuje vyvážené sadu přirozeného jazyka dotazů, které služba dokáže interpretovat, a také jak jsou tyto dotazy v přirozeném jazyce přeložit na výrazy sémantický dotaz.  Gramatika syntaxe je založena na [SRGS](http://www.w3.org/TR/speech-grammar/), W3C standard pro gramatika rozpoznávání řeči, s rozšířeními pro podporu integrace index dat a sémantické funkce.

Následující část popisuje každý syntaktické prvků, které mohou být používány gramatiku.  V tématu [v tomto příkladu](#example) pro dokončení gramatika, která demonstruje použití těchto prvků v kontextu.

### <a name="grammar-element"></a>Gramatika – Element 
`grammar` Element je element nejvyšší úrovně ve specifikaci gramatika XML.  Požadované `root` atribut určuje název kořenové pravidla, která definuje výchozí bod gramatiky.

```xml
<grammar root="GetPapers">
```

### <a name="import-element"></a>Import – Element
`import` Element importuje definici schématu z externího souboru povolit atribut odkazy. Element musí být podřízenou nejvyšší úrovně `grammar` elementu a zobrazí před spuštěním `attrref` elementy. Požadované `schema` atribut určuje název souboru schématu, který je umístěný ve stejném adresáři jako soubor XML gramatika. Požadované `name` element určuje schéma alias to následné `attrref` elementy použít při odkazování na atributy definované v tomto schématu.

```xml
  <import schema="academic.schema" name="academic"/>
```

### <a name="rule-element"></a>pravidlo – Element
`rule` Element definuje gramatika pravidlo, strukturální jednotkou, která určuje sadu výrazy dotazů, které dokáže interpretovat systému.  Element musí být podřízenou nejvyšší úrovně `grammar` elementu.  Požadované `id` atribut určuje název pravidla, kterou se odkazuje z `grammar` nebo `ruleref` elementy.

A `rule` element definuje sadu právní rozšíření.  Vstupní dotaz text tokeny porovnání přímo.  `item` elementy zadejte opakuje a alter pravděpodobnostech interpretace.  `one-of` elementy znamenat alternativní možnosti.  `ruleref` elementy povolit vytváření složitějších rozšíření jednodušší jednu z.  `attrref` prvky umožňují shoduje s hodnotami atributu z indexu.  `tag` elementy zadejte sémantika se výklad a změnit pravděpodobnost interpretace.

```xml
<rule id="GetPapers">...</rule>
```

### <a name="example-element"></a>Příklad – Element
Volitelné `example` element určuje frází příkladu, které může přijmout obsahující `rule` definice.  To mohou být použity pro dokumentaci nebo automatizované testování.

```xml
<example>papers about machine learning by michael jordan</example>
```

### <a name="item-element"></a>položka – Element
`item` Element skupiny posloupnost gramatika konstrukce.  Slouží k označení opakování pořadí rozšíření nebo k zadání alternativy ve spojení s `one-of` elementu.

Když `item` prvek není podřízeným `one-of` elementu, ho můžete určit opakování závorkách pořadí přiřazením `repeat` atributu na hodnotu count.  Počet hodnotu "*n*" (kde *n* je celé číslo) určuje, že je pořadí musí dojít přesně *n* časy.  Počet hodnotu "*m*-*n*" umožňuje pořadí se objeví mezi *m* a *n* krát (včetně).  Počet hodnotu "*m*-" Určuje, že sekvenci musí být alespoň *m* časy.  Volitelné `repeat-logprob` atribut lze použít ke změně interpretace pravděpodobnosti pro každý další opakování nad rámec minimální.

```xml
<item repeat="1-" repeat-logprob="-10">...</item>
```

Když `item` prvky objeví jako podřízené objekty `one-of` elementu, že definují sadu rozšíření alternativy.  V této využití, volitelné `logprob` atribut určuje relativní protokolu pravděpodobnost mezi různé možnosti.  Zadané pravděpodobnost *p* mezi 0 a 1, můžete vypočítat odpovídající pravděpodobnost protokolu jako protokolu (*p*), kde je log() funkci přirozené protokolu.  Pokud není zadaný, `logprob` výchozí hodnota je 0, která nemění pravděpodobnost interpretace.  Upozorňujeme, že pravděpodobnost protokolu se vždy záporné hodnoty s plovoucí desetinnou čárkou nebo 0.

```xml
<one-of>
  <item>by</item>
  <item logprob="-0.5">written by</item>
  <item logprob="-1">authored by</item>
</one-of>
```

### <a name="one-of-element"></a>jeden – elementu
`one-of` Element Určuje alternativní rozšíření mezi jednou z podřízených `item` elementy.  Pouze `item` elementy může vyskytovat v `one-of` elementu.  Je možné zadat relativní pravděpodobnostech mezi různé možnosti prostřednictvím `logprob` atribut v jednotlivých podřízených `item`.

```xml
<one-of>
  <item>by</item>
  <item logprob="-0.5">written by</item>
  <item logprob="-1">authored by</item>
</one-of>
```

### <a name="ruleref-element"></a>ruleref – Element
`ruleref` Element určuje platný rozšíření prostřednictvím odkazy na jiné `rule` elementu.  Prostřednictvím `ruleref` elementů složitější výrazy se dají vytvářet z jednodušší pravidel.  Požadované `uri` atribut označuje název odkazovaná `rule` pomocí syntaxe "#*rulename*".  K zachycení sémantického výstup odkazované pravidlo, použít nepovinný `name` atributu zadejte název proměnné, ke kterému je přiřazena sémantického výstup.
 
```xml
<ruleref uri="#GetPaperYear" name="year"/>
```

### <a name="attrref-element"></a>attrref – Element
`attrref` Element odkazuje na atribut index, což porovnání hodnot atributů zjištěnými v indexu.  Požadované `uri` atribut určuje název schématu indexu a název atributu pomocí syntaxe "*Schematický*#*%{attrname/*".  Musí existovat předcházející `import` element, který importuje schéma s názvem *Schematický*.  Název atributu je název atributu definovaným ve schématu sady odpovídající.

Kromě odpovídající vstup uživatele, `attrref` element také vrátí objekt strukturovaných dotazu jako výstup, který vybere podmnožinu objektů v indexu odpovídající vstupní hodnoty.  Použít nepovinný `name` atributu zadejte název proměnné, které se má uložit výstup objektu dotazu.  Objektu dotazu může být složené s jinými objekty dotazu a vytvořit další složité výrazy.  V tématu [sémantického interpretace](SemanticInterpretation.md) podrobnosti.  

```xml
<attrref uri="academic#Keyword" name="keyword"/>
```

#### <a name="query-completion"></a>Dokončení dotazu 
Pro podporu dokončených dotazu při interpretaci částečné uživatelských dotazů, musí každý Odkazovaný atribut obsahovat "starts_with" jako operace v definici schématu.  Zadaný dotaz předponu uživatele `attrref` odpovídající všechny hodnoty v indexu, které dokončení předponu a poskytne každou dokončení hodnotu jako samostatné výklad gramatiky.  

Příklady:
* Odpovídající `<attrref uri="academic#Keyword" name="keyword"/>` proti dotaz předponu "dat" generuje jeden interpretace pro dokumenty Paper o "databáze", jeden interpretace pro dokumenty Paper o "dolování dat", atd.
* Odpovídající `<attrref uri="academic#Year" name="year"/>` proti dotaz předponu "200" generuje jeden interpretace pro dokumentů do "2000", jeden interpretace pro papír v "2001", atd.

#### <a name="matching-operations"></a>Odpovídající operace
Kromě přesná shoda, vyberte atribut typy také podpora předponu a nerovnosti odpovídá prostřednictvím nepovinný `op` atribut.  Pokud žádný objekt v indexu má hodnotu, která odpovídá, gramatika cesta je blokována a službu nevygeneruje žádné interpretace procházející přes tuto cestu gramatika.   `op` Atribut výchozí nastavení "eq".

```xml
in <attrref uri="academic#Year" name="year"/>
before <attrref uri="academic#Year" op="lt" name="year"/
```

Následující tabulka uvádí podporovaném `op` hodnoty pro každý typ atributu.  Jejich používání vyžaduje odpovídající operace indexu, které mají být zahrnuty do atribut definice schématu.

| Typ atributu | Hodnota OP | Popis | Operace indexu
|----|----|----|----|
| Řetězec | EQ | Přesná shoda řetězce | rovná se |
| Řetězec | starts_with | Předpona shodu řetězce | starts_with |
| Int32, Int64, Double | EQ |  Porovnání rovnosti číselné | rovná se |
| Int32, Int64, Double | lt, le, gt, ge | Shoda číselné nerovnosti (<, < =, >, > =) | is_between |
| Int32, Int64, Double | starts_with | Předpona shody hodnoty v desítkovém zápisu | starts_with |

Příklady:
* `<attrref uri="academic#Year" op="lt" name="year"/>` odpovídá vstupní řetězec "2000" a vrátí všechny dokumenty Paper před v roce 2000, výhradně publikovat.
* `<attrref uri="academic#Year" op="lt" name="year"/>` vstupní řetězec "20" neodpovídá, protože nejsou k dispozici žádné dokumenty Paper v indexu před roku 20 publikovat.
* `<attrref uri="academic#Keyword" op="starts_with" name="keyword"/>` odpovídá vstupní řetězec "dat" a vrátí jednu interpretace papír o "databáze", "dolování dat", atd.  Toto je případ výjimečných použití.
* `<attrref uri="academic#Year" op="starts_with" name="year"/>` odpovídá vstupní řetězec "20" a vrátí v dokumenty Paper jeden interpretace publikované v 200 299, 2000-2999, atd.  Toto je případ výjimečných použití.

### <a name="tag-element"></a>značka elementu
`tag` Element určuje, jak se budou interpretovat cestu prostřednictvím gramatiky.  Obsahuje posloupnost ukončena středník příkazy.  Příkaz může být přiřazení literál nebo proměnná jiné proměnné.  Výstup funkce s 0 nebo více parametrů je také může přiřadit proměnné.  Každý parametr funkce může být zadán pomocí literál nebo proměnná.  Pokud funkce nevrací žádný výstup, je tento parametr vynechán přiřazení.  Obor proměnné je místní pro obsahující pravidlo.

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

Každý `rule` v gramatiky má předdefinované proměnné s názvem "na", představující výstup sémantického pravidla.  Její hodnota je vypočítána vyhodnocením každý sémantického příkazy provázán cestě prostřednictvím `rule` porovnání s uživatelem dotaz na vstup.  Hodnotu přiřazenou proměnnou "na" na konci vyhodnocení je sémantický výstup pravidla.  Sémantické výstup interpretace dotaz uživatele vůči gramatiky je sémantického výstup kořenové pravidla.

Některé příkazy může změnit pravděpodobnost cesta k interpretaci zavedením posun pravděpodobnosti sčítání protokolu.  Některé příkazy může odmítnout se výklad úplně-li zadána, nejsou splněné podmínky.

Seznam podporovaných sémantického funkcí najdete v tématu [sémantického funkce](SemanticInterpretation.md#semantic-functions).

## <a name="interpretation-probability"></a>Interpretace pravděpodobnosti
Pravděpodobnost cesta k interpretaci prostřednictvím gramatiky je pravděpodobnost kumulativní protokolu všech `<item>` elementy a sémantické funkce došlo na cestě.  Popisuje relativní pravděpodobnost odpovídající konkrétní vstupní pořadí.

Zadané pravděpodobnost *p* mezi 0 a 1, můžete vypočítat odpovídající pravděpodobnost protokolu jako protokolu (*p*), kde log() je funkce přirozené protokolu.  Pomocí protokolu pravděpodobnostech umožňuje systému hromadit joint pravděpodobnost cesta k interpretaci prostřednictvím jednoduchého přidání.  Zabraňuje také s plovoucí desetinnou čárkou podtečení společné pro tyto výpočty pravděpodobnosti joint.  Všimněte si, že návrhu, pravděpodobnost protokolu je vždy záporné hodnoty s plovoucí desetinnou čárkou nebo 0, kde vyšší hodnoty znamenat vyšší pravděpodobnost.

<a name="example"></a>
## <a name="example"></a>Příklad:
Následuje příklad XML z domény academic publikace, které ukazuje různé elementy gramatiku:

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
