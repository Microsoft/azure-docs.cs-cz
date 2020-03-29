---
title: Práce s řetězci v dotazech protokolu Azure Monitor | Dokumenty společnosti Microsoft
description: Popisuje, jak upravit, porovnat, prohledat a provádět řadu dalších operací na řetězce v dotazech protokolu Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: a394fee7178b2e3e167c8bd905ab175b25d1d813
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75397466"
---
# <a name="work-with-strings-in-azure-monitor-log-queries"></a>Práce s řetězci v dotazech protokolu Azure Monitor


> [!NOTE]
> Před dokončením tohoto kurzu byste měli dokončit [Začínáme s Azure Monitor Log Analytics](get-started-portal.md) a [Začínáme s dotazy protokolu Azure Monitor.](get-started-queries.md)

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Tento článek popisuje, jak upravit, porovnat, hledat a provádět řadu dalších operací na řetězce.

Každý znak v řetězci má číslo indexu podle jeho umístění. První znak je v indexu 0, další znak je 1 a tak dále. Různé funkce řetězce používají čísla indexu, jak je znázorněno v následujících částech. Mnoho z následujících příkladů používá příkaz **print** k zobrazení manipulace s řetězci bez použití určitého zdroje dat.


## <a name="strings-and-escaping-them"></a>Struny a jejich únik
Řetězcové hodnoty jsou zabaleny buď jednoduchými nebo dvojitými uvozovkami. Zpětné lomítko (\\) se používá k úniku znaků na následující znak, například \t pro kartu, \n pro nový řádek a \" samotný znak nabídky.

```Kusto
print "this is a 'string' literal in double \" quotes"
```

```Kusto
print 'this is a "string" literal in single \' quotes'
```

Chcete-li\\zabránit tomu, aby "\@" působil jako řídicí znak, přidejte " " jako předponu řetězce:

```Kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


## <a name="string-comparisons"></a>Porovnání řetězců

Operátor       |Popis                         |Case-sensitive|Příklad (výnosy) `true`
---------------|------------------------------------|--------------|-----------------------
`==`           |Rovná se                              |Ano           |`"aBc" == "aBc"`
`!=`           |Nerovná se                          |Ano           |`"abc" != "ABC"`
`=~`           |Rovná se                              |Ne            |`"abc" =~ "ABC"`
`!~`           |Nerovná se                          |Ne            |`"aBc" !~ "xyz"`
`has`          |Pravá strana je celý termín v levém-straně |Ne|`"North America" has "america"`
`!has`         |Pravá strana není plné termínv levém-straně       |Ne            |`"North America" !has "amer"` 
`has_cs`       |Pravá strana je celý termín v levém-straně |Ano|`"North America" has_cs "America"`
`!has_cs`      |Pravá strana není plné termínv levém-straně       |Ano            |`"North America" !has_cs "amer"` 
`hasprefix`    |Pravá strana je termín oválná předpona na levé straně         |Ne            |`"North America" hasprefix "ame"`
`!hasprefix`   |Pravá strana není předponou termínu na levé straně     |Ne            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |Pravá strana je termín oválná předpona na levé straně         |Ano            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |Pravá strana není předponou termínu na levé straně     |Ano            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |Pravá strana je přípona termínu v levé straně         |Ne            |`"North America" hassuffix "ica"`
`!hassuffix`   |Pravá strana není příponou termínu na levé straně     |Ne            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |Pravá strana je přípona termínu v levé straně         |Ano            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |Pravá strana není příponou termínu na levé straně     |Ano            |`"North America" !hassuffix_cs "icA"`
`contains`     |Pravá strana se vyskytuje jako dílčí sekvence levé strany  |Ne            |`"FabriKam" contains "BRik"`
`!contains`    |Pravá strana se nevyskytuje na levé straně           |Ne            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |Pravá strana se vyskytuje jako dílčí sekvence levé strany  |Ano           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |Pravá strana se nevyskytuje na levé straně           |Ano           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |Pravá strana je počáteční dílčí sekvence levé strany|Ne            |`"Fabrikam" startswith "fab"`
`!startswith`  |Pravá strana není počáteční dílčí sekvence levé strany|Ne        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |Pravá strana je počáteční dílčí sekvence levé strany|Ano            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |Pravá strana není počáteční dílčí sekvence levé strany|Ano        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |Pravá strana je závěrečná dílčí sekvence levé strany|Ne             |`"Fabrikam" endswith "Kam"`
`!endswith`    |Pravá strana není závěrečná dílčí sekvence levé strany|Ne         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |Pravá strana je závěrečná dílčí sekvence levé strany|Ano             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |Pravá strana není závěrečná dílčí sekvence levé strany|Ano         |`"Fabrikam" !endswith "brik"`
`matches regex`|levá strana obsahuje shodu pro pravou stranu        |Ano           |`"Fabrikam" matches regex "b.*k"`
`in`           |Rovná se jednomu z prvků       |Ano           |`"abc" in ("123", "345", "abc")`
`!in`          |Nerovná se žádnému z prvků   |Ano           |`"bca" !in ("123", "345", "abc")`


## <a name="countof"></a>počet

Počítá výskyty podřetězce v řetězci. Může odpovídat prostým řetězcům nebo použít regulární výraz. Prostý řetězec shody se mohou překrývat, zatímco regulární shody nejsou.

### <a name="syntax"></a>Syntaxe
```
countof(text, search [, kind])
```

### <a name="arguments"></a>Argumenty:
- `text`- Vstupní řetězec 
- `search`- Prostý řetězec nebo regulární výraz tak, aby odpovídaly uvnitř textu.
- `kind` - _normální_ | _regulární výraz_ (výchozí: normální).

### <a name="returns"></a>Vrací

Počet, kolikrát může být vyhledávací řetězec spárován v kontejneru. Prostý řetězec shody se mohou překrývat, zatímco regulární shody nejsou.

### <a name="examples"></a>Příklady

#### <a name="plain-string-matches"></a>Prosté shody řetězců

```Kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>Regex zápasy

```Kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


## <a name="extract"></a>Extrahovat

Získá shodu pro regulární výraz z daného řetězce. Volitelně také převede extrahovaný podřetězec na zadaný typ.

### <a name="syntax"></a>Syntaxe

```Kusto
extract(regex, captureGroup, text [, typeLiteral])
```

### <a name="arguments"></a>Argumenty

- `regex`- Regulární výraz.
- `captureGroup`- Kladná celočíselná konstanta označující skupinu zachycení k extrakci. 0 pro celou shodu, 1 pro hodnotu odpovídající první '('závorky')' v regulárním výrazu, 2 nebo více pro následující závorky.
- `text`- Řetězec k hledání.
- `typeLiteral`- Volitelný typ literálu (například typeof(long)). Pokud je k dispozici, extrahovaný podřetězec je převeden na tento typ.

### <a name="returns"></a>Vrací
Podřetězec odpovídá uvedené skupině zachycení, volitelně převedené na typeLiteral.
Pokud neexistuje žádná shoda nebo převod typu se nezdaří, vrátí null.

### <a name="examples"></a>Příklady

Následující příklad extrahuje poslední oktet *programu ComputerIP* ze záznamu prezenčního signálu:
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

Následující příklad extrahuje poslední oktet, přehodí jej na *skutečný* typ (číslo) a vypočítá další hodnotu IP
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

V níže uvedeném příkladu *je* hledána definice řetězce "Doba trvání". Shoda je přetypována na *hodnotu real* a vynásobena časovou konstantou (1 s), *která přetypuje duration na type timespan*.
```Kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


## <a name="isempty-isnotempty-notempty"></a>je prázdný, není prázdný, není prázdný

- *isempty* vrátí true, pokud je argument prázdný řetězec nebo null (viz také *isnull).*
- *isnotempty* vrátí true, pokud argument není prázdný řetězec nebo null (viz také *isnotnull).* alias: *není prázdný*.

### <a name="syntax"></a>Syntaxe

```Kusto
isempty(value)
isnotempty(value)
```

### <a name="examples"></a>Příklady

```Kusto
print isempty("");  // result: true

print isempty("0");  // result: false

print isempty(0);  // result: false

print isempty(5);  // result: false

Heartbeat | where isnotempty(ComputerIP) | take 1  // return 1 Heartbeat record in which ComputerIP isn't empty
```


## <a name="parseurl"></a>parseurl

Rozdělí adresu URL na její části (protokol, hostitel, port atd.) a vrátí objekt slovníku obsahující součásti jako řetězce.

### <a name="syntax"></a>Syntaxe

```
parseurl(urlstring)
```

### <a name="examples"></a>Příklady

```Kusto
print parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")
```

Výsledkem bude:
```
{
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
}
```


## <a name="replace"></a>Nahradit

Nahradí všechny shody regulárních výrazů jiným řetězcem. 

### <a name="syntax"></a>Syntaxe

```
replace(regex, rewrite, input_text)
```

### <a name="arguments"></a>Argumenty

- `regex`- Regulární výraz, podle kterého se má shodovat. Může obsahovat skupiny zachycení v '('závorky')'.
- `rewrite`- Náhradní regulární výraz pro všechny shody provedené odpovídající regulární výraz. Použijte \0 k odkazování na celou shodu, \1 pro první skupinu zachycení\ 2 a tak dále pro následné skupiny zachycení.
- `input_text`- Vstupní řetězec, ve který chcete hledat.

### <a name="returns"></a>Vrací
Text po nahrazení všech shod regex s hodnocení přepsání. Shody se nepřekrývají.

### <a name="examples"></a>Příklady

```Kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

Může mít následující výsledky:

Aktivita                                        |Nahrazen
------------------------------------------------|----------------------------------------------------------
4663 - Došlo k pokusu o přístup k objektu.  |ID aktivity 4663: Došlo k pokusu o přístup k objektu.


## <a name="split"></a>split

Rozdělí daný řetězec podle zadaného oddělovače a vrátí pole výsledných podřetězců.

### <a name="syntax"></a>Syntaxe
```
split(source, delimiter [, requestedIndex])
```

### <a name="arguments"></a>Argumenty:

- `source`- Řetězec, který má být rozdělen podle zadaného oddělovače.
- `delimiter`- Oddělovač, který bude použit k rozdělení zdrojového řetězce.
- `requestedIndex`- Volitelný index založený na nule. Pokud je k dispozici, bude vrácené pole řetězce obsahovat pouze tuto položku (pokud existuje).


### <a name="examples"></a>Příklady

```Kusto
print split("aaa_bbb_ccc", "_");    // result: ["aaa","bbb","ccc"]
print split("aa_bb", "_");          // result: ["aa","bb"]
print split("aaa_bbb_ccc", "_", 1); // result: ["bbb"]
print split("", "_");               // result: [""]
print split("a__b", "_");           // result: ["a","","b"]
print split("aabbcc", "bb");        // result: ["aa","cc"]
```

## <a name="strcat"></a>strcat

Zřetězí argumenty řetězce (podporuje argumenty 1-16).

### <a name="syntax"></a>Syntaxe
```
strcat("string1", "string2", "string3")
```

### <a name="examples"></a>Příklady
```Kusto
print strcat("hello", " ", "world") // result: "hello world"
```


## <a name="strlen"></a>strlen

Vrátí délku řetězce.

### <a name="syntax"></a>Syntaxe
```
strlen("text_to_evaluate")
```

### <a name="examples"></a>Příklady
```Kusto
print strlen("hello")   // result: 5
```


## <a name="substring"></a>Podřetězec

Extrahuje podřetězec z daného zdrojového řetězce, počínaje zadaným indexem. Volitelně lze zadat délku požadovaného podřetězce.

### <a name="syntax"></a>Syntaxe
```
substring(source, startingIndex [, length])
```

### <a name="arguments"></a>Argumenty:

- `source`- Zdrojový řetězec, ze kterého bude odebrán podřetězec.
- `startingIndex`- Počáteční pozice počátečního znaku na základě nuly požadovaného podřetězce.
- `length`- Volitelný parametr, který lze použít k určení požadované délky vráceného podřetězce.

### <a name="examples"></a>Příklady
```Kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


## <a name="tolower-toupper"></a>tolower, toupper

Převede daný řetězec na všechna malá nebo velká písmena.

### <a name="syntax"></a>Syntaxe
```
tolower("value")
toupper("value")
```

### <a name="examples"></a>Příklady
```Kusto
print tolower("HELLO"); // result: "hello"
print toupper("hello"); // result: "HELLO"
```



## <a name="next-steps"></a>Další kroky
Pokračujte v pokročilých kurzech:
* [Agregační funkce](aggregations.md)
* [Pokročilé agregace](advanced-aggregations.md)
* [Grafy a diagramy](charts.md)
* [Práce s JSON a datovými strukturami](json-data-structures.md)
* [Psaní rozšířených dotazů](advanced-query-writing.md)
* [Spojení - křížová analýza](joins.md)
