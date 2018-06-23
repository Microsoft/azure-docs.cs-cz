---
title: Sémantické interpretace v rozhraní API služby zkoumání znalostní báze | Microsoft Docs
description: Další informace o použití sémantického interpretace v znalostní báze zkoumání služby (KES) rozhraní API v kognitivní služby.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 022188464eb7269b69f96a058b444167b587387c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342507"
---
# <a name="semantic-interpretation"></a>Sémantické interpretace
Sémantické interpretace přidruží každá interpretovaný cesta prostřednictvím gramatiky sémantického výstup.  Konkrétně služba vyhodnocuje pořadí příkazy v `tag` elementy procházené pomocí interpretace k výpočtu finální výstup.  

Příkaz může být přiřazení literál nebo proměnná jiné proměnné.  Výstup funkce s 0 nebo více parametrů je také může přiřadit proměnné.  Každý parametr funkce může být zadán pomocí literál nebo proměnná.  Pokud funkce nevrací žádný výstup, je tento parametr vynechán přiřazení.

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

Proměnné je zadán pomocí identifikátoru název, který začíná písmenem a obsahuje pouze písmena (A-Z), čísla (0-9) a podtržítko (\_).  Typ je implicitně odvodit z literál nebo funkce výstupní hodnotu přiřazenou k němu. 

Dále je uveden seznam aktuálně podporované datové typy:

|Typ|Popis|Příklady|
|----|----|----|
|Řetězec|Pořadí 0 nebo více znaků|"Hello, World!"<br/>""|
|BOOL|Logická hodnota|true (pravda)<br/>false (nepravda)|
|Int32|32bitové číslo se znaménkem.  -2.1e9 k 2.1e9|123<br/>-321|
|Int64|64bitové číslo se znaménkem. -9.2e18 a 9.2e18|9876543210|
|Double|Dvojitá přesnost s plovoucí desetinnou čárkou. 1, 7E +/-308 (15 číslic)|123.456789<br/>1.23456789e2|
|Guid|Globálně jedinečný identifikátor|"602DD052-CC47-4B23-A16A-26B52D30C05B"|
|Dotaz|Výraz dotazu, který určuje podmnožinu dat objektů v indexu|All()<br/>A (*Otázka č. 1*, *Dotaz č. 2*)|

<a name="semantic-functions"></a>
## <a name="semantic-functions"></a>Sémantické funkce
Není integrovanou sadu sémantického funkce.  Povolit vytváření složitých dotazů a poskytují závislé na kontextu kontrolu nad gramatika interpretace.

### <a name="and-function"></a>A funkce
`query = And(query1, query2);`

Vrátí dotaz skládající se z průnik dvou vstupních dotazů.

### <a name="or-function"></a>Nebo – funkce
`query = Or(query1, query2);`

Vrátí dotaz skládající se z sjednocení dva vstupní dotazy.

### <a name="all-function"></a>Všechny funkce
`query = All();`

Vrátí dotaz, který obsahuje všechny objekty data.

V následujícím příkladu používáme funkce All() interaktivně vytvořit dotaz založený na průnik 1 nebo více klíčová slova.

```
<tag>query = All();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = And(query, keyword);</tag>
</item>
```

### <a name="none-function"></a>Žádné funkce
`query = None();`

Vrátí dotaz, který zahrnuje žádné datové objekty.

V následujícím příkladu používáme funkce None() interaktivně vytvořit dotaz založený na sjednocení 1 nebo více klíčová slova.

```
<tag>query = None();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = Or(query, keyword);</tag>
</item>
```

### <a name="query-function"></a>Dotaz – funkce
```
query = Query(attrName, value)
query = Query(attrName, value, op)
```

Vrátí dotaz, který obsahuje pouze data objekty, jehož atribut *%{attrname/* odpovídá hodnotě *hodnotu* podle zadanou operaci *op*, což výchozí nastavení "eq".  Obvykle používají `attrref` elementu, který chcete vytvořit dotaz založený na řetězec dotazu odpovídající vstupní.  Pokud hodnotu je zadána nebo získat prostřednictvím jiným způsobem, funkci Query() lze použít k vytvoření dotazu odpovídající tuto hodnotu.

V následujícím příkladu jak implementovat podporu pro zadání academic publikace z konkrétní deset používáme Query() funkce.

```xml
written in the 90s
<tag>
  beginYear = Query("academic#Year", 1990, "ge");
  endYear = Query("academic#Year", 2000, "lt");
  year = And(beginYear, endYear);
</tag>
```

<a name="composite-function"/>
### <a name="composite-function"></a>Složené – funkce
`query = Composite(innerQuery);`

Vrací dotaz, který zapouzdřuje *innerQuery* tvořený odpovídá proti dílčí atributy společný atribut složené *line*.  Zapouzdření vyžaduje atribut složené *line* žádné odpovídající datového objektu do mají alespoň jednu hodnotu, která splňuje jednotlivě *innerQuery*.  Všimněte si, že dotaz na dílčí atributy složené atributu musí být zapouzdřené pomocí funkce Composite() předtím, než ho mohou být kombinovány s jiné dotazy.

Například následující dotaz vrátí academic publikace podle "harry shum" při mu byl v "microsoft":
```
Composite(And(Query("academic#Author.Name", "harry shum"), 
              Query("academic#Author.Affiliation", "microsoft")));
```

Na druhé straně následující dotaz vrátí academic publikace, kde jeden autorů je "harry shum" a jeden názorům uživatele je "společnost microsoft":
```
And(Composite(Query("academic#Author.Name", "harry shum"), 
    Composite(Query("academic#Author.Affiliation", "microsoft")));
```

### <a name="getvariable-function"></a>GetVariable – funkce
`value = GetVariable(name, scope);`

Vrátí hodnotu proměnné *název* definované v zadané *oboru*.  *název* je identifikátor, který začíná písmenem a obsahuje pouze písmena (A-Z), čísla (0-9) a podtržítko (_).  *obor* může být nastaven na "žádost" nebo "systém".  Všimněte si, že jsou od sebe liší, včetně těch, které jsou definované pomocí výstup sémantického funkce proměnné definované v různých oborech.

Proměnné oboru požadavku jsou sdíleny ve všech interpretace v rámci aktuální interpret požadavek.  Se může použít k řízení hledat interpretace přes gramatiky.

Systémové proměnné jsou předdefinovány službou a slouží k načtení různé statistické údaje o aktuálním stavu systému.  Níže je sada aktuálně podporované systémové proměnné:

|Název|Typ|Popis|
|----|----|----|
|IsAtEndOfQuery|BOOL|Hodnota TRUE, pokud se aktuální výklad má odpovídající všechny vstupní dotaz text|
|IsBeyondEndOfQuery|BOOL|Hodnota TRUE, pokud se aktuální výklad navrhl dokončených nad rámec text vstupní dotazu|

### <a name="setvariable-function"></a>SetVariable – funkce
`SetVariable(name, value, scope);`

Přiřadí *hodnotu* proměnné *název* pod zadaným *oboru*.  *název* je identifikátor, který začíná písmenem a obsahuje pouze písmena (A-Z), čísla (0-9) a podtržítko (_).  V současné době jedinou platnou hodnotou pro *oboru* je "vyžádat".  Neexistují žádné proměnné nastavit systému.

Proměnné oboru požadavku jsou sdíleny ve všech interpretace v rámci aktuální interpret požadavek.  Se může použít k řízení hledat interpretace přes gramatiky.

### <a name="assertequals-function"></a>AssertEquals – funkce
`AssertEquals(value1, value2);`

Pokud *value1* a *value2* jsou ekvivalentní funkce úspěšné a nemá žádné vedlejší účinky.  Funkce, jinak se nezdaří a odmítne se výklad.

### <a name="assertnotequals-function"></a>AssertNotEquals – funkce
`AssertNotEquals(value1, value2);`

Pokud *value1* a *value2* jsou není ekvivalentní, funkce úspěšné a nemá žádné vedlejší účinky.  Funkce, jinak se nezdaří a odmítne se výklad.


