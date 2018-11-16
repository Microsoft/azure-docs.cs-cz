---
title: Práce s řetězci v dotazech Azure Log Analytics | Dokumentace Microsoftu
description: Tento článek obsahuje kurz pro používání portálu Analytics psát dotazy v Log Analytics.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 2bf5593507d99f6fb45994eb73333b21ca8904a5
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51706309"
---
# <a name="working-with-strings-in-log-analytics-queries"></a>Práce s řetězci v dotazech Log Analytics


> [!NOTE]
> By se měla Dokončit [začít používat portál Analytics](get-started-analytics-portal.md) a [Začínáme s dotazy](get-started-queries.md) před tímto kurzem.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

Tento článek popisuje, jak upravit, porovnat, vyhledávat a provádět řadu dalších operací s řetězci. 

Každý znak v řetězci má pořadové číslo, podle místa. První znak, který je v indexu 0, že následující znak je 1 a tedy. Funkce různých řetězců pomocí čísla indexů, jak je znázorněno v následujících částech. Mnoho z následujících příkladů použití **tisk** příkazu pro předvedení zacházení s řetězci bez použití konkrétnímu zdroji dat.


## <a name="strings-and-escaping-them"></a>Řetězce a uvození jejich
Řetězcové hodnoty jsou zabaleny s buď pomocí jednoduchých nebo dvojitých uvozovek. Zpětné lomítko (\) slouží k řídicí znaky pro znak následující, jako je například \t pro kartu, \n pro nový řádek, a \" znak uvozovek, samotného.

```Kusto
print "this is a 'string' literal in double \" quotes"
```

Aby se zabránilo "\\"z funguje jako řídicí znak, přidejte"\@" jako předponu řetězec:

```Kusto
print @"C:\backslash\not\escaped\with @ prefix"
```


## <a name="string-comparisons"></a>Porovnávání řetězců

Operátor       |Popis                         |Malá a velká písmena|Příklad (vrací `true`)
---------------|------------------------------------|--------------|-----------------------
`==`           |Rovná se                              |Ano           |`"aBc" == "aBc"`
`!=`           |Nerovná se                          |Ano           |`"abc" != "ABC"`
`=~`           |Rovná se                              |Ne            |`"abc" =~ "ABC"`
`!~`           |Nerovná se                          |Ne            |`"aBc" !~ "xyz"`
`has`          |Pravé straně je celý výraz v levé straně |Ne|`"North America" has "america"`
`!has`         |Pravé straně není úplný výraz v levé straně       |Ne            |`"North America" !has "amer"` 
`has_cs`       |Pravé straně je celý výraz v levé straně |Ano|`"North America" has_cs "America"`
`!has_cs`      |Pravé straně není úplný výraz v levé straně       |Ano            |`"North America" !has_cs "amer"` 
`hasprefix`    |Pravé straně je předpona termín v levé straně         |Ne            |`"North America" hasprefix "ame"`
`!hasprefix`   |Pravé straně není předponu termín v levé straně     |Ne            |`"North America" !hasprefix "mer"` 
`hasprefix_cs`    |Pravé straně je předpona termín v levé straně         |Ano            |`"North America" hasprefix_cs "Ame"`
`!hasprefix_cs`   |Pravé straně není předponu termín v levé straně     |Ano            |`"North America" !hasprefix_cs "CA"` 
`hassuffix`    |Pravé straně je výraz přípony v levé straně         |Ne            |`"North America" hassuffix "ica"`
`!hassuffix`   |Pravé straně není výraz přípony v levé straně     |Ne            |""Severní Amerika"!"americ"hassuffix
`hassuffix_cs`    |Pravé straně je výraz přípony v levé straně         |Ano            |`"North America" hassuffix_cs "ica"`
`!hassuffix_cs`   |Pravé straně není výraz přípony v levé straně     |Ano            |""Severní Amerika"! hassuffix_cs"icA"
`contains`     |Dojde k pravé straně jako dílčí sekvenci z levé straně  |Ne            |`"FabriKam" contains "BRik"`
`!contains`    |V levé straně nedojde, pravé straně           |Ne            |`"Fabrikam" !contains "xyz"`
`contains_cs`   |Dojde k pravé straně jako dílčí sekvenci z levé straně  |Ano           |`"FabriKam" contains_cs "Kam"`
`!contains_cs`  |V levé straně nedojde, pravé straně           |Ano           |`"Fabrikam" !contains_cs "Kam"`
`startswith`   |Pravé straně je počáteční dílčí sekvenci z levé straně|Ne            |`"Fabrikam" startswith "fab"`
`!startswith`  |Pravé straně není počáteční dílčí sekvenci z levé straně|Ne        |`"Fabrikam" !startswith "kam"`
`startswith_cs`   |Pravé straně je počáteční dílčí sekvenci z levé straně|Ano            |`"Fabrikam" startswith_cs "Fab"`
`!startswith_cs`  |Pravé straně není počáteční dílčí sekvenci z levé straně|Ano        |`"Fabrikam" !startswith_cs "fab"`
`endswith`     |Pravé straně je uzavírací dílčí sekvenci z levé straně|Ne             |`"Fabrikam" endswith "Kam"`
`!endswith`    |Pravé straně není uzavírací dílčí sekvenci z levé straně|Ne         |`"Fabrikam" !endswith "brik"`
`endswith_cs`     |Pravé straně je uzavírací dílčí sekvenci z levé straně|Ano             |`"Fabrikam" endswith "Kam"`
`!endswith_cs`    |Pravé straně není uzavírací dílčí sekvenci z levé straně|Ano         |`"Fabrikam" !endswith "brik"`
`matches regex`|levé straně obsahuje hodnotu odpovídající pravé straně        |Ano           |`"Fabrikam" matches regex "b.*k"`
`in`           |Se rovná jeden z elementů       |Ano           |`"abc" in ("123", "345", "abc")`
`!in`          |Nerovná se na některý z prvků   |Ano           |`"bca" !in ("123", "345", "abc")`


## <a name="countof"></a>countof

Počty výskyt podřetězce v řetězci. Můžete odpovídat prostý řetězce nebo regulárního výrazu. Prostý řetězec odpovídá mohou překrývat nemusí regulárního výrazu shody.

### <a name="syntax"></a>Syntaxe
```
countof(text, search [, kind])
```

### <a name="arguments"></a>Argumenty:
- `text` -Vstupní řetězec 
- `search` -Prostý řetězec nebo regulární výraz tak, aby odpovídaly uvnitř text.
- `kind` - _Normální_ | _regulární výraz_ (výchozí: normální).

### <a name="returns"></a>Vrací

Počet případů, kdy se hledaný řetězec může být shoda v kontejneru. Prostý řetězec odpovídá mohou překrývat, zatímco regulárního výrazu shody tomu tak není.

### <a name="examples"></a>Příklady

#### <a name="plain-string-matches"></a>Prostý řetězec odpovídá

```Kusto
print countof("The cat sat on the mat", "at");  //result: 3
print countof("aaa", "a");  //result: 3
print countof("aaaa", "aa");  //result: 3 (not 2!)
print countof("ababa", "ab", "normal");  //result: 2
print countof("ababa", "aba");  //result: 2
```

#### <a name="regex-matches"></a>Regulárního výrazu shody

```Kusto
print countof("The cat sat on the mat", @"\b.at\b", "regex");  //result: 3
print countof("ababa", "aba", "regex");  //result: 1
print countof("abcabc", "a.c", "regex");  // result: 2
```


## <a name="extract"></a>Extrakce

Získá odpovídající regulární výraz ze zadaného řetězce. Volitelně můžete také převádí extrahované podřetězec zadaného typu.

### <a name="syntax"></a>Syntaxe

```Kusto
extract(regex, captureGroup, text [, typeLiteral])
```

### <a name="arguments"></a>Argumenty

- `regex` -Regulární výraz.
- `captureGroup` – Konstanta kladné celé číslo určující skupinu zachycení k extrakci. 0 pro celou shodu, 1 pro hodnotu odpovídající první "(" závorka")" v regulárním výrazu, 2 nebo více pro následné závorky.
- `text` -Hledaný řetězec.
- `typeLiteral` – Nepovinný typu literál (například typeof(long)). Pokud je zadán, extrahovaného podřetězce je převést na tento typ.

### <a name="returns"></a>Vrací
Podřetězec odpovídající proti captureGroup skupiny uvedeného zachycení, Volitelně můžete převést na typeLiteral.
Pokud není nalezena žádná shoda, nebo typ převodu nezdaří, vrátí hodnotu null.

### <a name="examples"></a>Příklady

Následující příklad získá poslední oktet *ComputerIP* ze záznamu prezenčního signálu:
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| project ComputerIP, last_octet=extract("([0-9]*$)", 1, ComputerIP) 
```

Následující příklad extrahuje poslední oktet, přetypování na *skutečné* zadejte (číslo) a vypočte hodnota dalšího IP
```Kusto
Heartbeat
| where ComputerIP != "" 
| take 1
| extend last_octet=extract("([0-9]*$)", 1, ComputerIP, typeof(real)) 
| extend next_ip=(last_octet+1)%255
| project ComputerIP, last_octet, next_ip
```

V příkladu níže řetězec *trasování* prohledána definice "Trvání". Ke shodě přetypováno na *skutečné* a vynásobený časovou konstantou (1 s) *která přetypovává dobu trvání zadejte časový interval*.
```Kusto
let Trace="A=12, B=34, Duration=567, ...";
print Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real));  //result: 567
print Duration_seconds =  extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s);  //result: 00:09:27
```


## <a name="isempty-isnotempty-notempty"></a>IsEmpty isnotempty, notempty

- *IsEmpty* vrátí hodnotu true, pokud argument je prázdný řetězec nebo hodnotu null (viz také *isnull*).
- *isnotempty* vrací true, pokud argument není prázdný řetězec nebo hodnotu null (viz také *isnotnull*). Alias: *notempty*.

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

Rozdělí adresu URL do částí (protokol, hostitele, port atd.) a vrací objekt slovníku obsahující části jako řetězce.

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


## <a name="replace"></a>nahradit

Nahradí všechny shody regulárního výrazu jiným řetězcem. 

### <a name="syntax"></a>Syntaxe

```
replace(regex, rewrite, input_text)
```

### <a name="arguments"></a>Argumenty

- `regex` -Regulární výraz shodovat. Může obsahovat skupin zachycení v "("závorek")".
- `rewrite` Regex – nahrazení pro vyhledání jakékoli shody od odpovídající regulární výraz. \0 používejte k odkazování na celé shody, \1 pro první skupině zachycení, \2 a podobně pro následné zachycení skupiny.
- `input_text` -Vstupní řetězec pro vyhledávání.

### <a name="returns"></a>Vrací
Text, který po vyhodnocení revize nahradit všechny shody regulárního výrazu. Odpovídá nepřekrývají.

### <a name="examples"></a>Příklady

```Kusto
SecurityEvent
| take 1
| project Activity 
| extend replaced = replace(@"(\d+) -", @"Activity ID \1: ", Activity) 
```

Může mít následující výsledky:
Aktivita                                        |nahradit
------------------------------------------------|----------------------------------------------------------
4663 – byl k pokusu o přístup k objektu  |ID aktivity 4663: Byl proveden pokus o přístup k objektu.


## <a name="split"></a>split

Rozdělí daný řetězec podle zadaného oddělovače a vrátí pole z výsledného podřetězců.

### <a name="syntax"></a>Syntaxe
```
split(source, delimiter [, requestedIndex])
```

### <a name="arguments"></a>Argumenty:

- `source` -Řetězec, který se má rozdělit podle zadaného oddělovače.
- `delimiter` -Oddělovač, který se použije k rozdělení zdrojový řetězec.
- `requestedIndex` – Volitelný index založený na nule. Pokud je zadán, pole vráceného řetězce bude obsahovat pouze této položky (pokud existuje).


### <a name="examples"></a>Příklady

```Kusto
print split("aaa_bbb_ccc", "_");    // result: ["aaa","bbb","ccc"]
print split("aa_bb", "_");          // result: ["aa","bb"]
print split("aaa_bbb_ccc", "_", 1); // result: ["bbb"]
print split("", "_");               // result: [""]
print split("a__b", "_");           // result: ["a","","b"]
print split("aabbcc", "bb");        // result: ["aa","cc"]
```

## <a name="strcat"></a>strcat –

Zřetězí řetězcové argumenty (argumenty podporuje 1 – 16).

### <a name="syntax"></a>Syntaxe
```
strcat("string1", "string2", "string3")
```

### <a name="examples"></a>Příklady
```Kusto
print strcat("hello", " ", "world") // result: "hello world"
```


## <a name="strlen"></a>strlen –

Vrátí délku řetězce.

### <a name="syntax"></a>Syntaxe
```
strlen("text_to_evaluate")
```

### <a name="examples"></a>Příklady
```Kusto
print strlen("hello")   // result: 5
```


## <a name="substring"></a>dílčí řetězec

Extrahuje podřetězec z daného zdroje řetězce, počínaje zadaným indexem. Volitelně lze zadat délku požadovaný dílčí řetězec.

### <a name="syntax"></a>Syntaxe
```
substring(source, startingIndex [, length])
```

### <a name="arguments"></a>Argumenty:

- `source` -Zdrojový řetězec, který se provedou dílčí řetězec z.
- `startingIndex` -Počáteční znak pozice s nulovým základem požadovaný podřetězce.
- `length` -Volitelný parametr, který slouží k určení požadovaná délka vrácená podřetězec.

### <a name="examples"></a>Příklady
```Kusto
print substring("abcdefg", 1, 2);   // result: "bc"
print substring("123456", 1);       // result: "23456"
print substring("123456", 2, 2);    // result: "34"
print substring("ABCD", 0, 2);  // result: "AB"
```


## <a name="tolower-toupper"></a>ToLower, toupper

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



## <a name="next-steps"></a>Další postup
Pokračujte pokročilé kurzy:
* [Agregační funkce](aggregations.md)
* [Pokročilé agregace](advanced-aggregations.md)
* [Grafy a diagramy](charts.md)
* [Práce s formátem JSON a datovými strukturami](json-data-structures.md)
* [Zápis rozšířeného dotazu](advanced-query-writing.md)
* [Spojení - křížové analýzy](joins.md)
