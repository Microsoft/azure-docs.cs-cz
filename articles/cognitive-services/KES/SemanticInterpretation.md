---
title: Sémantická interpretace – Knowledge Exploration Service rozhraní API
titlesuffix: Azure Cognitive Services
description: Naučte se používat sémantickou interpretaci v rozhraní API pro Knowledge Exploration Service (KES).
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 26f8d885f8cf85ab849ba221392df206e492aac4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79220058"
---
# <a name="semantic-interpretation"></a>Sémantická interpretace

Sémantická interpretace spojuje sémantický výstup s každou interpretovánou cestou gramatiky.  Konkrétně služba vyhodnocuje sekvenci příkazů v `tag`ch prvcích předaných výkladem, aby vypočítal konečný výstup.  

Příkaz může být přiřazením literálu nebo proměnné jiné proměnné.  Může také přiřadit výstup funkce s 0 nebo více parametry proměnné.  Každý parametr funkce může být zadán pomocí literálu nebo proměnné.  Pokud funkce nevrátí žádný výstup, je přiřazení vynecháno.

```xml
<tag>x = 1; y = x;</tag>
<tag>q = All(); q = And(q, q2);</tag>
<tag>AssertEquals(x, 1);</tag>
```

Proměnná je určena pomocí identifikátoru názvu, který začíná písmenem a skládá se pouze z písmen (A-Z), čísel (0-9) a podtržítka (\_).  Jeho typ je implicitně odvozený z výstupní hodnoty literálu nebo funkce, která k němu byla přiřazena. 

Níže je uveden seznam aktuálně podporovaných datových typů:

|Typ|Popis|Příklady|
|----|----|----|
|Řetězec|Sekvence 0 nebo více znaků|"Hello World!"<br/>""|
|Bool|Hodnota typu Boolean|true<br/>false|
|Datový typ Int32|32 – celé číslo se znaménkem.  -2.1 E9 na 2.1 E9|123<br/>-321|
|Int64|64 – celé číslo se znaménkem. -9.2 E18 a 9.2 E18|9876543210|
|Double|Dvojitá přesnost s plovoucí desetinnou čárkou. 1.7 e +/-308 (15 číslic)|123,456789<br/>1.23456789 E2|
|identifikátor GUID|Globálně jedinečný identifikátor|"602DD052-CC47-4B23-A16A-26B52D30C05B"|
|Dotaz|Výraz dotazu, který určuje podmnožinu datových objektů v indexu|All()<br/>And (*Q1*, *Q2*)|

## <a name="semantic-functions"></a>Sémantické funkce

Existuje integrovaná sada sémantických funkcí.  Umožňují konstrukci propracovanéch dotazů a poskytují kontextově citlivý ovládací prvek pro gramatické interpretace.

### <a name="and-function"></a>And – funkce

`query = And(query1, query2);`

Vrátí dotaz složený z průniku dvou vstupních dotazů.

### <a name="or-function"></a>Or – funkce

`query = Or(query1, query2);`

Vrátí dotaz složený ze sjednocení dvou vstupních dotazů.

### <a name="all-function"></a>All – funkce

`query = All();`

Vrátí dotaz, který obsahuje všechny datové objekty.

V následujícím příkladu používáme funkci All () k iterativnímu sestavení dotazu založeného na průsečíku 1 nebo více klíčových slov.

```
<tag>query = All();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = And(query, keyword);</tag>
</item>
```

### <a name="none-function"></a>None – funkce

`query = None();`

Vrátí dotaz, který neobsahuje žádné datové objekty.

V následujícím příkladu používáme funkci None () k opakovanému sestavení dotazu založeného na sjednocení 1 nebo více klíčových slov.

```
<tag>query = None();</tag>
<item repeat="1-">
  <attrref uri="academic#Keyword" name="keyword">
  <tag>query = Or(query, keyword);</tag>
</item>
```

### <a name="query-function"></a>Funkce dotazu

```
query = Query(attrName, value)
query = Query(attrName, value, op)
```

Vrátí dotaz, který obsahuje pouze datové objekty, jejichž atribut *attrName* odpovídá *hodnotě Value podle zadané* operace *op*, což je výchozí hodnota EQ.  Obvykle použijte `attrref` element k vytvoření dotazu na základě odpovídajícího vstupního řetězce dotazu.  Pokud je hodnota předána nebo získána jinými způsoby, lze pomocí funkce Query () vytvořit dotaz, který odpovídá této hodnotě.

V následujícím příkladu používáme funkci Query () k implementaci podpory pro určení školních publikací z určitého desetiletí.

```xml
written in the 90s
<tag>
  beginYear = Query("academic#Year", 1990, "ge");
  endYear = Query("academic#Year", 2000, "lt");
  year = And(beginYear, endYear);
</tag>
```

### <a name="composite-function"></a>Složená funkce

`query = Composite(innerQuery);`

Vrátí dotaz, který zapouzdřuje *innerQuery* skládající se z shody s podatributy společného složeného atributu *ATTR*.  Zapouzdření vyžaduje, *aby atribut* složeného atributu libovolného odpovídajícího datového objektu měl alespoň jednu hodnotu, která bude mít k *innerQueryu*individuálně.  Všimněte si, že dotaz na dílčí atributy složeného atributu musí být zapouzdřený pomocí funkce složené () předtím, než může být kombinován s jinými dotazy.

Například následující dotaz vrátí akademické publikace "Harry Shum", zatímco byl v programu "Microsoft":
```
Composite(And(Query("academic#Author.Name", "harry shum"), 
              Query("academic#Author.Affiliation", "microsoft")));
```

Na druhé straně následující dotaz vrátí akademické publikace, u nichž jeden z autorů je "Harry Shum" a jedna z afilací je "Microsoft":
```
And(Composite(Query("academic#Author.Name", "harry shum"), 
    Composite(Query("academic#Author.Affiliation", "microsoft")));
```

### <a name="getvariable-function"></a>Getvariable – funkce

`value = GetVariable(name, scope);`

Vrátí hodnotu *názvu* proměnné definovanou v rámci zadaného *oboru*.  *název* je identifikátor, který začíná písmenem a skládá se pouze z písmen (a-Z), čísel (0-9) a podtržítka (_).  *Rozsah* lze nastavit na hodnotu "Request" nebo "System".  Všimněte si, že proměnné definované v různých oborech jsou odlišné od sebe, včetně těch, které jsou definovány prostřednictvím výstupu sémantických funkcí.

Proměnné oboru žádosti jsou sdíleny ve všech výkladech v rámci aktuální žádosti o interpretaci.  Dají se použít k řízení vyhledávání interpretace na gramatiku.

Systémové proměnné jsou předdefinovány službou a lze je použít k načtení různých statistik o aktuálním stavu systému.  Níže je uvedená sada aktuálně podporovaných systémových proměnných:

|Název|Typ|Popis|
|----|----|----|
|IsAtEndOfQuery|Bool|true, pokud aktuální výklad odpovídá veškerému vstupnímu textu dotazu|
|IsBeyondEndOfQuery|Bool|true, pokud aktuální výklad má navrhovaná dokončení mimo text dotazu Input|

### <a name="setvariable-function"></a>SetVariable – funkce

`SetVariable(name, value, scope);`

Přiřadí *hodnotu* proměnné *názvu* v zadaném *oboru*.  *název* je identifikátor, který začíná písmenem a skládá se pouze z písmen (a-Z), čísel (0-9) a podtržítka (_).  V současné době je jediná platná hodnota *oboru* "Request".  Neexistují žádné nastavitelné systémové proměnné.

Proměnné oboru žádosti jsou sdíleny ve všech výkladech v rámci aktuální žádosti o interpretaci.  Dají se použít k řízení vyhledávání interpretace na gramatiku.

### <a name="assertequals-function"></a>AssertEquals – funkce

`AssertEquals(value1, value2);`

Pokud jsou *argumenty hodnota1* a *hodnota2* ekvivalentní, funkce bude úspěšná a nemá žádné vedlejší účinky.  V opačném případě funkce selže a odmítne výklad.

### <a name="assertnotequals-function"></a>AssertNotEquals – funkce

`AssertNotEquals(value1, value2);`

Pokud *argumenty hodnota1* a *hodnota2* nejsou ekvivalentní, funkce bude úspěšná a nemá žádné vedlejší účinky.  V opačném případě funkce selže a odmítne výklad.


