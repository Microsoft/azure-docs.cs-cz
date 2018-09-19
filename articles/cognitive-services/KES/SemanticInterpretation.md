---
title: Sémantického výkladu – Knowledge Exploration Service API
titlesuffix: Azure Cognitive Services
description: Další informace o použití sémantického výkladu v znalostní báze zkoumání služby (KES) rozhraní API.
services: cognitive-services
author: bojunehsu
manager: cgronlun
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 5fcc7b760b5445e57b41787d8818ef11ed926e6c
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129348"
---
# <a name="semantic-interpretation"></a>Sémantického výkladu

Sémantického výkladu přidruží sémantické výstup každé interpretovaný cesty prostřednictvím gramatiky.  Konkrétně se služba vyhodnocuje pořadí příkazů v `tag` prvky Procházet podle výkladu pro výpočet konečného výstupu.  

Příkaz může být přiřazení literál nebo proměnná do jiné proměnné.  Výstup z funkce s 0 nebo více parametrů je také může přiřadit proměnné.  Každý parametr funkce lze zadat pomocí literál nebo proměnná.  Pokud funkce nevrací žádný výstup, přiřazení je vynechán.

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

Proměnná je určen pomocí identifikátor názvu, který začíná písmenem a obsahuje pouze písmena (A – Z), číslice (0 – 9) a podtržítko (\_).  Jeho typ implicitně odvozuje z literál nebo výstupní funkci přiřazena hodnota. 

Dále je uveden seznam aktuálně podporované datové typy:

|Typ|Popis|Příklady|
|----|----|----|
|Řetězec|Posloupnost 0 nebo více znaků|"Hello World!"<br/>""|
|BOOL|Logická hodnota|true (pravda)<br/>false (nepravda)|
|Datový typ Int32|32bitové celé číslo se znaménkem.  -2.1e9 k 2.1e9|123<br/>-321|
|Int64|64bitové celé číslo se znaménkem. -9.2e18 a 9.2e18|9876543210|
|Double|Dvojité přesnosti s plovoucí desetinnou čárkou. 1, 7E +/-308 (15 číslic)|123.456789<br/>1.23456789e2|
|Guid|Globálně jedinečný identifikátor|"602DD052-CC47-4B23-A16A-26B52D30C05B"|
|Dotaz|Výraz dotazu, který určuje podmnožinu dat objektů do indexu|All()<br/>A (*q1*, *q2*)|

## <a name="semantic-functions"></a>Sémantické funkce

Je integrovaná sada sémantické funkcí.  Povolit vytváření sofistikovaných dotazy a poskytují kontextové a kontrolu nad interpretace gramatiky.

### <a name="and-function"></a>A funkce

`query = And(query1, query2);`

Vrátí dotaz skládající se z průnik dva vstupní dotazy.

### <a name="or-function"></a>Nebo – funkce

`query = Or(query1, query2);`

Vrátí dotaz skládající se z sjednocení dva vstupní dotazy.

### <a name="all-function"></a>All – funkce

`query = All();`

Vrátí dotaz, který zahrnuje všechny objekty data.

V následujícím příkladu používáme funkci All() zavádět postupně Vybudujte dotaz a je určena průsečíkem 1 nebo více klíčových slov.

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

V následujícím příkladu používáme funkci None() zavádět postupně vytvořit dotaz založený na sjednocení 1 nebo více klíčových slov.

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

Vrátí dotaz, který obsahuje pouze datové objekty, jejichž atribut *%{attrname/* odpovídá hodnotě *hodnotu* podle zadané operace *op*, která má výchozí hodnotu "eq".  Obvykle používají `attrref` prvek, který chcete vytvořit dotaz na základě řetězce odpovídající vstupní dotaz.  Pokud hodnota je předaný nebo získali prostřednictvím jiným způsobem, funkci Query() lze použít k vytvoření dotazu odpovídající této hodnotě.

V následujícím příkladu používáme funkci Query() k implementaci podpory pro zadání academic publikace z konkrétní deset let.

```xml
written in the 90s
<tag>
  beginYear = Query("academic#Year", 1990, "ge");
  endYear = Query("academic#Year", 2000, "lt");
  year = And(beginYear, endYear);
</tag>
```

### <a name="composite-function"></a>Složený – funkce

`query = Composite(innerQuery);`

Vrátí dotaz, který zapouzdřuje *innerQuery* tvořený shody proti dílčí atributy společný atribut složené *attr*.  Zapouzdření složené atribut requires *attr* žádné odpovídající datového objektu má alespoň jednu hodnotu, která splňuje jednotlivě *innerQuery*.  Všimněte si, že dotaz na dílčí atributy složené atributu musí být zapouzdřeny pomocí funkce Composite() předtím, než je možné kombinovat s další dotazy.

Například následující dotaz vrátí academic publikace podle "harry shum" zatímco pracoval bruno v "microsoft":
```
Composite(And(Query("academic#Author.Name", "harry shum"), 
              Query("academic#Author.Affiliation", "microsoft")));
```

Na druhé straně následující dotaz vrátí academic publikací, kde jedním z autorů je "harry shum" a jeden umístění je "microsoft":
```
And(Composite(Query("academic#Author.Name", "harry shum"), 
    Composite(Query("academic#Author.Affiliation", "microsoft")));
```

### <a name="getvariable-function"></a>GetVariable – funkce

`value = GetVariable(name, scope);`

Vrátí hodnotu proměnné *název* definované v rámci zadaného *oboru*.  *název* je identifikátor, který začíná písmenem a obsahuje pouze písmena (A – Z), číslice (0 – 9) a podtržítka (_).  *obor* můžete nastavit na "žádost o" nebo "systém".  Všimněte si, že se liší od sebe navzájem, včetně těch, které jsou definované prostřednictvím výstup sémantické funkce proměnné definované v různých oborech.

Proměnné s rozsahem žádosti jsou sdíleny napříč všechny interpretace v rámci aktuálního požadavku interpretaci.  Jejich lze ovládat hledat interpretace gramatiky.

Systémové proměnné jsou předdefinovány službou a slouží k načtení obsahují různé statistické údaje o aktuálním stavu systému.  Níže je sada proměnných aktuálně podporovaný systémový:

|Název|Typ|Popis|
|----|----|----|
|IsAtEndOfQuery|BOOL|Hodnota TRUE, pokud aktuální interpretace odpovídá všechny vstupní text|
|IsBeyondEndOfQuery|BOOL|Hodnota TRUE, pokud aktuální interpretace navrhl dokončování nad rámec vstupní text|

### <a name="setvariable-function"></a>Funkce SetVariable

`SetVariable(name, value, scope);`

Přiřadí *hodnotu* proměnné *název* pod zadaným *oboru*.  *název* je identifikátor, který začíná písmenem a obsahuje pouze písmena (A – Z), číslice (0 – 9) a podtržítka (_).  V současné době jedinou platnou hodnotou pro *oboru* je "request".  Nejsou žádné nastavitelné systémové proměnné.

Proměnné s rozsahem žádosti jsou sdíleny napříč všechny interpretace v rámci aktuálního požadavku interpretaci.  Jejich lze ovládat hledat interpretace gramatiky.

### <a name="assertequals-function"></a>AssertEquals – funkce

`AssertEquals(value1, value2);`

Pokud *hodnota1* a *hodnota2* jsou ekvivalentní funkce je úspěšné a nemá žádné vedlejší účinky.  V opačném případě funkce selže a odmítne výkladu.

### <a name="assertnotequals-function"></a>AssertNotEquals – funkce

`AssertNotEquals(value1, value2);`

Pokud *hodnota1* a *hodnota2* jsou není ekvivalentní, funkce úspěšná a nemá žádné vedlejší účinky.  V opačném případě funkce selže a odmítne výkladu.


