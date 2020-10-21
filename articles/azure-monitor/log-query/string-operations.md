---
title: Práce s řetězci v Azure Monitorch dotazech protokolu | Microsoft Docs
description: Popisuje, jak upravit, porovnat, vyhledat a provést různé operace s řetězci v Azure Monitorch dotazech protokolu.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/19/2020
ms.openlocfilehash: 7838f9f1febcab073633dbb4af011e99acbe22d3
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92310297"
---
# <a name="work-with-strings-in-azure-monitor-log-queries"></a>Práce s řetězci v Azure Monitorch dotazech protokolu


> [!NOTE]
> Před dokončením tohoto kurzu byste měli dokončit [Začínáme s Azure Monitor Log Analytics](get-started-portal.md) a [Začínáme s dotazy protokolu Azure monitor](get-started-queries.md) .

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Tento článek popisuje, jak upravit, porovnat, vyhledat a provést různé operace s řetězci.

Každý znak v řetězci má číslo indexu v závislosti na jeho umístění. První znak je v indexu 0, další znak je 1 a tak dále. Různé řetězcové funkce používají čísla indexů, jak je znázorněno v následujících oddílech. Mnohé z následujících příkladů používají příkaz **Print** pro ukázku manipulace s řetězci bez použití konkrétního zdroje dat.


## <a name="strings-and-escaping-them"></a>Řetězce a uvozovací znaky
Řetězcové hodnoty jsou zabaleny buď pomocí jednoduchých nebo dvojitých uvozovek. Zpětné lomítko ( \\ ) se používá k řídicím znakům na znak, který následuje, jako je například \t pro tabulátor, \n pro nový řádek a \" znak citace.

```Kusto
print "this is a 'string' literal in double \" quotes"
```

```Kusto
print 'this is a "string" literal in single \' quotes'
```

Chcete-li zabránit " \\ " v tom, aby jednal jako řídicí znak, přidejte " \@ " jako předponu do řetězce:

```Kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


## <a name="string-comparisons"></a>Porovnání řetězců

Operátor       |Popis                         |Case-Sensitive|Příklad (výnosy `true` )
---------------|------------------------------------|--------------|-----------------------
`==`           |Je rovno                              |Ano           |`"aBc" == "aBc"`
`!=`           |Nerovná se                          |Ano           |`"abc" != "ABC"`
`=~`           |Je rovno                              |Ne            |`"abc" =~ "ABC"`
`!~`           |Nerovná se                          |Ne            |`"aBc" !~ "xyz"`
`has`          |Pravá strana je celý výraz na levé straně |Ne|`"North America" has "america"`
`!has`         |Pravá strana není úplným termínem na levé straně.       |Ne            |`"North America" !has "amer"` 
`has_cs`       |Pravá strana je celý výraz na levé straně |Ano|`"North America" has_cs "America"`
`!has_cs`      |Pravá strana není úplným termínem na levé straně.       |Ano            |`"North America" !has_cs "amer"` 
`hasprefix`    |Pravá strana je předpona termínu na levé straně         |Ne            |`"North America" hasprefix "ame"`
`!hasprefix`   |Pravá strana není předpona termínu na levé straně.     |Ne            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |Pravá strana je předpona termínu na levé straně         |Ano            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |Pravá strana není předpona termínu na levé straně.     |Ano            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |Pravá strana je přípona termínu na levé straně.         |Ne            |`"North America" hassuffix "ica"`
`!hassuffix`   |Pravá strana není přípona termínu na levé straně.     |Ne            |`"North America" !hassuffix "americ"`
`hassuffix_cs`    |Pravá strana je přípona termínu na levé straně.         |Ano            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |Pravá strana není přípona termínu na levé straně.     |Ano            |`"North America" !hassuffix_cs "icA"`
`contains`     |Pravá strana probíhá jako dílčí sekvence levé části.  |Ne            |`"FabriKam" contains "BRik"`
`!contains`    |Pravá strana se nevyskytuje na levé straně.           |Ne            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |Pravá strana probíhá jako dílčí sekvence levé části.  |Ano           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |Pravá strana se nevyskytuje na levé straně.           |Ano           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |Pravá strana je počáteční dílčí sekvence na levé straně.|Ne            |`"Fabrikam" startswith "fab"`
`!startswith`  |Pravá strana není počáteční podsekvence na levé straně.|Ne        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |Pravá strana je počáteční dílčí sekvence na levé straně.|Ano            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |Pravá strana není počáteční podsekvence na levé straně.|Ano        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |Pravá strana je pravá dílčí sekvence levé části na straně druhé.|Ne             |`"Fabrikam" endswith "Kam"`
`!endswith`    |Pravá strana není uzavírací podsekvence levé části.|Ne         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |Pravá strana je pravá dílčí sekvence levé části na straně druhé.|Ano             |`"Fabrikam" endswith_cs "kam"`
`!endswith_cs`    |Pravá strana není uzavírací podsekvence levé části.|Ano         |`"Fabrikam" !endswith_cs "brik"`
`matches regex`|levá strana obsahuje shodu pro pravou stranu.        |Ano           |`"Fabrikam" matches regex "b.*k"`
`in`           |Je rovno jednomu elementu       |Ano           |`"abc" in ("123", "345", "abc")`
`!in`          |Nerovná se žádnému elementu   |Ano           |`"bca" !in ("123", "345", "abc")`


## <a name="countof"></a>CountOf

Spočítá výskyty podřetězce v řetězci. Může odpovídat prostým řetězcům nebo použít regulární výraz. Shody s prostým řetězcem se mohou překrývat, zatímco se neshodují regulární výrazy

### <a name="syntax"></a>Syntaxe
```
countof(text, search [, kind])
```

### <a name="arguments"></a>Náhodné
- `text` – Vstupní řetězec 
- `search` – Prostý řetězec nebo regulární výraz pro spárování uvnitř textu
- `kind` - _normální_  |  _Regex_ (výchozí: Normal).

### <a name="returns"></a>Vrací

Počet, kolikrát může být hledaný řetězec v kontejneru spárován. Shody s prostým řetězcem se mohou překrývat, zatímco se neshodují regulární výrazy

### <a name="examples"></a>Příklady

#### <a name="plain-string-matches"></a>Shody jednoduchých řetězců

```Kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>Regulární výraz odpovídá

```Kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


## <a name="extract"></a>extrakční

Načte shodu regulárního výrazu z daného řetězce. Volitelně také převede extrahovaný podřetězec na určený typ.

### <a name="syntax"></a>Syntaxe

```Kusto
extract(regex, captureGroup, text [, typeLiteral])
```

### <a name="arguments"></a>Argumenty

- `regex` – Regulární výraz.
- `captureGroup` – Kladná celočíselná konstanta označující skupinu zachycení k extrakci. 0 pro celou shodu, hodnota 1 pro hodnotu odpovídající prvnímu (' závorce ') ' v regulárním výrazu, 2 nebo více pro následující závorky.
- `text` – Řetězec, který chcete vyhledat.
- `typeLiteral` – Volitelný literál typu (například typeof (Long)). Je-li tento příkaz zadán, extrahovaný dílčí řetězec je převeden na tento typ.

### <a name="returns"></a>Vrací
Podřetězec se shoduje s určenou skupinou zachycení skupiny zachycení, volitelně převedena na typeLiteral.
Pokud se neshoduje, nebo převod typu se nezdařil, vrátí hodnotu null.

### <a name="examples"></a>Příklady

Následující příklad extrahuje poslední oktet *ComputerIP* ze záznamu prezenčního signálu:
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

Následující příklad extrahuje poslední oktet, přetypování na *skutečný* typ (číslo) a vypočítá další hodnotu IP.
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

V následujícím příkladu je vyhledáno *trasování* řetězce s definicí "Duration". Porovnávání je přetypování na *reálné* a vynásobené časovou konstantou (1 s) *, která přetypování doby trvání na typ TimeSpan*.
```Kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


## <a name="isempty-isnotempty-notempty"></a>neprázdný, isnotempty, a.

- atribut *Empty* vrátí hodnotu true, pokud je argumentem prázdný řetězec nebo hodnota null (viz také *IsNull*).
- *isnotempty* vrátí hodnotu true, pokud argument není prázdný řetězec nebo hodnota null (viz také *IsNotNull*). alias: *a*.

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

Rozdělí adresu URL do jejích částí (protokol, hostitel, port atd.) a vrátí objekt Dictionary, který obsahuje části jako řetězce.

### <a name="syntax"></a>Syntaxe

```
parseurl(urlstring)
```

### <a name="examples"></a>Příklady

```Kusto
print parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")
```

Výsledek bude:
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


## <a name="replace"></a>náhrady

Nahradí všechny regulární výrazy shodný s jiným řetězcem. 

### <a name="syntax"></a>Syntaxe

```
replace(regex, rewrite, input_text)
```

### <a name="arguments"></a>Argumenty

- `regex` – Regulární výraz, který se má shodovat s. Může obsahovat skupiny zachycení v ' (' závorky ') '.
- `rewrite` – Nahrazující regulární výraz pro všechny shody provedené porovnáváním regulárním výrazem. Pomocí \ 0 se můžete podívat na celou shodu, \ 1 pro první skupinu zachycení, \ 2 atd. pro další skupiny zachycení.
- `input_text` – Vstupní řetězec, ve kterém se má hledat.

### <a name="returns"></a>Vrací
Text po nahrazení všech shod regulárního výrazu pomocí vyhodnocení přepisu Shoda se nekryje.

### <a name="examples"></a>Příklady

```Kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

Může mít následující výsledky:

Aktivita                                        |znění
------------------------------------------------|----------------------------------------------------------
4663 – byl proveden pokus o přístup k objektu.  |ID aktivity 4663: byl proveden pokus o přístup k objektu.


## <a name="split"></a>split

Rozdělí daný řetězec na základě zadaného oddělovače a vrátí pole výsledných podřetězců.

### <a name="syntax"></a>Syntaxe
```
split(source, delimiter [, requestedIndex])
```

### <a name="arguments"></a>Náhodné

- `source` – Řetězec, který má být rozdělen podle zadaného oddělovače.
- `delimiter` – Oddělovač, který bude použit k rozdělení zdrojového řetězce.
- `requestedIndex` – Volitelný index založený na nule. Pokud je zadáno, bude vrácené pole řetězce obsahovat pouze tuto položku (pokud existuje).


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

Zřetězí řetězcové argumenty (podporuje 1-16 argumenty).

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


## <a name="substring"></a>podřetězec

Extrahuje podřetězec z daného zdrojového řetězce, počínaje zadaným indexem. Volitelně lze zadat délku požadovaného podřetězce.

### <a name="syntax"></a>Syntaxe
```
substring(source, startingIndex [, length])
```

### <a name="arguments"></a>Náhodné

- `source` – Zdrojový řetězec, ze kterého se bude podřetězec považovat.
- `startingIndex` – Počáteční pozice znaku požadovaného podřetězce na základě nuly.
- `length` – Volitelný parametr, který lze použít k určení požadované délky vráceného podřetězce.

### <a name="examples"></a>Příklady
```Kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


## <a name="tolower-toupper"></a>ToLower, ToUpper

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
* [Spojení – křížová analýza](joins.md)
