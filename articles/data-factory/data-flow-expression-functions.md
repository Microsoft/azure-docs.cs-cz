---
title: Mapování funkce výraz toku dat pro vytváření dat Azure
description: Mapování funkce výraz toku dat pro vytváření dat Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: 9a3d596d79aec2305dfc3ec61bd587da57f4a397
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56409016"
---
# <a name="mapping-data-flow-expression-functions"></a>Mapování funkce výraz toku dat

Jazyk výrazů, který slouží ke konfiguraci transformace dat data Factory mapování Data proudí mít.

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

<code>isNull</code>
==============================
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Kontroluje, zda je hodnota NULL * ``isNull(NULL()) -> true``
* ``isNull('') -> false'``
*********************************
<code>null</code>
==============================
<code><b>null() => null</b></code><br/><br/>
Vrátí hodnotu NULL. Funkce syntax(null()) použijte, pokud je sloupec s názvem "null". Všechny operace, která používá způsobí s hodnotou NULL * ``custId = NULL (for derived field)``
* ``custId == NULL -> NULL``
* ``'nothing' + NULL -> NULL``
* ``10 * NULL -> NULL'``
* ``NULL == '' -> NULL'``
*********************************
<code>iif</code>
==============================
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
Na základě podmínky platí jednu hodnotu, nebo druhé. Pokud neurčená "Ostatní" je považován za hodnotu NULL. Obě hodnoty musí být kompatibilní (číselné, řetězec...) * ``iif(custType == 'Premium', 10, 4.5)``
* ``iif(amount > 100, 'High')``
* ``iif(dayOfWeek(saleDate) == 6, 'Weekend', 'Weekday')``
*********************************
<code>case</code>
==============================
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
Na základě střídavých podmínky platí jednu hodnotu, nebo druhé. Pokud počet vstupů jsou i, druhý pro poslední podmínka má hodnotu NULL * ``case(custType == 'Premium', 10, 4.5)``
* ``case(custType == 'Premium', price*0.95, custType == 'Elite',   price*0.9, price*2)``
* ``case(dayOfWeek(saleDate) == 1, 'Sunday', dayOfWeek(saleDate) == 6, 'Saturday')``
*********************************
<code>equalsIgnoreCase</code>
==============================
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
Porovnání rovnosti operátor ignorování případu. Stejné jako <> = – operátor * ``'abc'=='abc' -> true``
* ``equalsIgnoreCase('abc', 'Abc') -> true``
*********************************
<code>concat</code>
==============================
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Proměnný počet řetězců zřetězí společně. Stejné jako + – operátor s řetězci * ``concat('Awesome', 'Cool', 'Product') -> 'AwesomeCoolProduct'``
* ``'Awesome' + 'Cool' + 'Product' -> 'AwesomeCoolProduct'``
* ``concat(addrLine1, ' ', addrLine2, ' ', city, ' ', state, ' ', zip)``
* ``addrLine1 + ' ' + addrLine2 + ' ' + city + ' ' + state + ' ' + zip``
*********************************
<code>concatWS</code>
==============================
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Zřetězí proměnný počet řetězců spolu s oddělovačem. První parametr je oddělovač * ``concatWS(' ', 'Awesome', 'Cool', 'Product') -> 'Awesome Cool Product'``
* ``concatWS(' ' , addrLine1, addrLine2, city, state, zip) -> ``
* ``concatWS(',' , toString(order_total), toString(order_discount))``
*********************************
<code>trim</code>
==============================
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Ořízne řetězec úvodní a koncové znaky. Pokud je druhý parametr nespecifikovaná, ořízne prázdné znaky. Jinak ořízne všechny znakem určeným ve druhém parametru * ``trim('!--!wor!ld!', '-!') -> 'wor!ld'``
*********************************
<code>ltrim</code>
==============================
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code><br/><br/>
Vlevo ořízne počáteční znaky řetězce. Pokud je druhý parametr nespecifikovaná, ořízne prázdné znaky. Jinak ořízne všechny znakem určeným ve druhém parametru * ``ltrim('!--!wor!ld!', '-!') -> 'wor!ld!'``
*********************************
<code>rtrim</code>
==============================
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code><br/><br/>
Pravé ořízne počáteční znaky řetězce. Pokud je druhý parametr nespecifikovaná, ořízne prázdné znaky. Jinak ořízne všechny znakem určeným ve druhém parametru * ``rtrim('!--!wor!ld!', '-!') -> '!--!wor!ld'``
*********************************
<code>substring</code>
==============================
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
Extrahuje podřetězec z určité délky od pozice. Pozice je 1 na základě. Pokud délka je vynechán, je převezme do řetězce * ``substring('Cat in the hat', 5, 2) -> 'in'``
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``
* ``substring('Cat in the hat', 5) -> 'in the hat'``
* ``substring('Cat in the hat', 100, 100) -> ''``
*********************************
<code>lower</code>
==============================
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Lowercases řetězec * ``lower('GunChus') -> 'gunchus'``
*********************************
<code>upper</code>
==============================
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Uppercases řetězec * ``upper('bojjus') -> 'BOJJUS'``
*********************************
<code>length</code>
==============================
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
Vrátí délku řetězce * ``length('kiddo') -> 5``
*********************************
<code>rpad</code>
==============================
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Pravé vyplní řetězec zadaný doplněním, dokud se z určité délky. Pokud řetězec je roven nebo větší než délka jde no-op * ``rpad('great', 10, '-') -> 'great-----'`` 
* ``rpad('great', 4, '-') -> 'great'`` 
* ``rpad('great', 8, '<>') -> 'great<><'`` 
*********************************
<code>lpad</code>lpad</code>
==============================
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Vlevo vyplní řetězec zadaný doplněním, dokud se z určité délky. Pokud řetězec je rovna nebo větší než délka jde no-op * ``lpad('great', 10, '-') -> '-----great'`` 
* ``lpad('great', 4, '-') -> 'great'`` 
* "lpad (skvělé, 8" <> ") ->" <><great'``
*********************************
<code>vlevo</code>
==============================
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Extrahuje podřetězec začíná na indexu 1 s počtem znaků. Stejné jako dílčí řetězec (str, 1, n) * ``left('bojjus', 2) -> 'bo'``
* ``left('bojjus', 20) -> 'bojjus'``
*********************************
<code>right</code>
==============================
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Extrahuje podřetězec s počtem znaků z pravé strany. Stejné jako dílčí řetězec (str, LENGTH(str) - n, n) * ``right('bojjus', 2) -> 'us'``
* ``right('bojjus', 20) -> 'bojjus'``
*********************************
<code>startsWith</code>
==============================
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Kontroluje, zda řetězec začíná předaný řetězec * ``startsWith('great', 'gr') -> true``
*********************************
<code>endsWith</code>
==============================
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Kontroluje, pokud řetězec končí předaný řetězec * ``endsWith('great', 'eat') -> true``
*********************************
<code>locate</code>
==============================
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
Najde pozici (1 na základě) podřetězce v řetězci od určité pozici. Pokud je vynechán pozice je považován za od začátku řetězce. 0 je vrácena, pokud není nalezen * ``locate('eat', 'great') -> 3``
* ``locate('o', 'microsoft', 6) -> 7``
* ``locate('bad', 'good') -> 0``
*********************************
<code>instr</code>
==============================
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
Najde pozici (1 na základě) podřetězce v řetězci. 0 je vrácena, pokud není nalezen * ``instr('great', 'eat') -> 3``
* ``instr('microsoft', 'o') -> 7``
* ``instr('good', 'bad') -> 0``
*********************************
<code>translate</code>
==============================
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
Nahraďte jednu sadu znaků pomocí jiné sady znaků v řetězci. Mají znaky nahrazení 1: 1 * ``translate('(Hello)', '()', '[]') -> '[Hello]'``
* ``translate('(Hello)', '()', '[') -> '[Hello'``
*********************************
<code>reverse</code>
==============================
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Vrátí řetězec * ``reverse('gunchus') -> 'suhcnug'``
*********************************
<code>initCap</code>
==============================
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Převede první písmeno každého slova na velká písmena. Slova jsou označena jako oddělené prázdné znaky * ``initCap('cool iceCREAM') -> 'Cool IceCREAM'``
*********************************
<code>replace</code>
==============================
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Nahraďte všechny výskyty podřetězce jiného podřetězce v daný řetězec * ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``
* ``replace('doggie dog', 'dog', '') -> 'gie'``
*********************************
<code>regexReplace</code>
==============================
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Nahraďte všechny výskyty vzor regulárního výrazu jiného podřetězce v daný řetězec použití "<regex>'(back quote) umožní vyhledávat řetězce bez uvozovacích znaků * ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``
*********************************
<code>regexExtract</code>
==============================
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
Extrahování odpovídajícího podřetězce dané regulárního výrazu vzoru. Poslední parametr identifikuje skupinu shody a je nastavena na výchozí 1-li tento parametr vynechán. Použití "<regex>'(back quote) umožní vyhledávat řetězce bez uvozovacích znaků * ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``
*********************************
<code>regexMatch</code>
==============================
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
Kontroluje se, pokud řetězec odpovídá vzoru regulárního výrazu daného. Použití "<regex>'(back quote) umožní vyhledávat řetězce bez uvozovacích znaků * ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``
*********************************
<code>like</code>
==============================
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Vzorek je řetězec, který je vyhledána přesná shoda s, s výjimkou následující speciální symboly: _ shoduje s libovolným znakem ve vstupním (podobně jako. v regulárních výrazech posix) % odpovídá nula nebo více znaků ve vstupním (podobný. * v regulárních výrazech posix).
Řídicí znak je ". Pokud řídicí znak předchází speciální symbol nebo jiný řídicí znak, následující znak je vyhledána přesná shoda. Je neplatné k uvození libovolného znaku.
* ``like('icecream', 'ice%') -> true``
*********************************
<code>rlike</code>
==============================
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Kontroluje, pokud řetězec odpovídá vzoru regulárního výrazu daného * ``rlike('200.50', '(\d+).(\d+)') -> true``
*********************************
<code>in</code>
==============================
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
Kontroluje, jestli je položka v poli * ``in([10, 20, 30], 10) -> true``
* ``in(['good', 'kid'], 'bad') -> false``
*********************************
<code>toString</code>
==============================
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
Převede primitivní datový typ na řetězec. Pro čísla a data je možné zadat formátu. Pokud tento parametr zadán výchozí systémové nastavení se vybere. Desítkový formát Java se používá pro čísla. Je výchozí formát data rrrr MM-dd * ``toString(10) -> '10'``
* ``toString('engineer') -> 'engineer'``
* ``toString(123456.789, '##,###.##') -> '123,456.79'``
* ``toString(123.78, '000000.000') -> '000123.780'``
* ``toString(12345, '##0.#####E0') -> '12.345E3'``
* ``toString(toDate('2018-12-31')) -> '2018-12-31'``
* ``toString(toDate('2018-12-31'), 'MM/dd/yy') -> '12/31/18'``
* ``toString(4 == 20) -> 'false'``
*********************************
<code>split</code>
==============================
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
Rozdělí řetězec na základě oddělovače a vrátí pole řetězců * ``split('100,200,300', ',') -> ['100', '200', '300']``
* ``split('100,200,300', '|') -> ['100,200,300']``
* ``split('100, 200, 300', ', ') -> ['100', '200', '300']``
* ``split('100, 200, 300', ', ')[1] -> '100'``
* ``split('100, 200, 300', ', ')[0] -> NULL``
* ``split('100, 200, 300', ', ')[20] -> NULL``
* ``split('100200300', ',') -> ['100200300']``
*********************************
<code>regexSplit</code>
==============================
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
Rozdělí řetězec na základě oddělovače podle regulárního výrazu a vrátí pole řetězců * ``regexSplit('oneAtwoBthreeC', '[CAB]') -> ['one', 'two', 'three']``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[1] -> 'one'``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[0] -> NULL``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[20] -> NULL``
*********************************
<code>soundex</code>
==============================
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Získá kód soundex řetězce * ``soundex('genius') -> 'G520'``
*********************************
<code>levenshtein</code>
==============================
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
Získá levenshtein vzdálenost mezi dva řetězce * ``levenshtein('boys', 'girls') -> 4``
*********************************
<code>slice</code>
==============================
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
Extrahuje podmnožinu pole od pozice. Pozice je 1 na základě. Pokud délka je vynechán, je převezme do řetězce * ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``
* ``slice([10, 20, 30, 40], 2)[1] -> 20``
* ``slice([10, 20, 30, 40], 2)[0] -> NULL``
* ``slice([10, 20, 30, 40], 2)[20] -> NULL``
* ``slice([10, 20, 30, 40], 8) -> []``
*********************************
<code>true</code>
==============================
<code><b>true() => boolean</b></code><br/><br/>
Vždy vrátí hodnotu true. Pokud je název sloupce s názvem "true", použijte syntax(true()) – funkce * ``isDiscounted == true()``
* ``isDiscounted() == true``
*********************************
<code>false</code>
==============================
<code><b>false() => boolean</b></code><br/><br/>
Vždy vrátí hodnotu false. Pokud je název sloupce s názvem "Nepravda", použijte syntax(false()) – funkce * ``isDiscounted == false()``
* ``isDiscounted() == false``
*********************************
<code>toBoolean</code>
==============================
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
Převede hodnotu (nejde ","true","y","yes", '1') na hodnotu true a ("f","Nepravda", n", 'ne', '0') na hodnotu false a NULL pro jakoukoli jinou hodnotu * ``toBoolean('true') -> true``
* ``toBoolean('n') -> false``
* ``toBoolean('truthy') -> NULL``
*********************************
<code>add</code>
==============================
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Přidá dvojici řetězce nebo čísla. Přidá data do počtu dnů. Připojí jedno pole podobného typu na jiný. Stejné jako + – operátor * ``add(10, 20) -> 30``
* ``10 + 20 -> 30``
* ``add('ice', 'cream') -> 'icecream'``
* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``
* ``add(toDate('2012-12-12'), 3) -> 2012-12-15 (date value)``
* ``toDate('2012-12-12') + 3 -> 2012-12-15 (date value)``
* ``[10, 20] + [30, 40] => [10, 20, 30, 40]``
*********************************
<code>minus</code>
==============================
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Odečte čísla. Odečte od datum počet dnů. Totéž jako-– operátor * ``minus(20, 10) -> 10``
* ``20 - 10 -> 10``
* ``minus(toDate('2012-12-15'), 3) -> 2012-12-12 (date value)``
* ``toDate('2012-12-15') - 3 -> 2012-12-13 (date value)``
*********************************
<code>multiply</code>
==============================
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Vynásobí dvojici čísel. Stejné jako * – operátor * ``multiply(20, 10) -> 200``
* ``20 * 10 -> 200``
*********************************
<code>divide</code>
==============================
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Rozdělí dvojici čísel. Stejné jako / – operátor * ``divide(20, 10) -> 2``
* ``20 / 10 -> 2``
*********************************
<code>mod</code>
==============================
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
MODULUS pár čísel. Stejné jako % – operátor * ``mod(20, 8) -> 4``
* ``20 % 8 -> 4``
*********************************
<code>pMod</code>
==============================
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Pozitivní Modulus pár čísel.
* ``pmod(-20, 8) -> 4``
*********************************
<code>abs</code>
==============================
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Pozitivní Modulus pár čísel.
* ``abs(-20) -> 20``
* ``abs(10) -> 10``
*********************************
<code>and</code>
==============================
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logický operátor AND. Stejné jako & & * ``and(true, false) -> false``
* ``true && false -> false``
*********************************
<code>or</code>
==============================
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logický operátor OR. Stejné jako || * ``or(true, false) -> true``
* ``true || false -> true``
*********************************
<code>xor</code>
==============================
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logického operátoru XOR. Stejné jako ^ – operátor * ``xor(true, false) -> true``
* ``xor(true, true) -> false``
* ``true ^ false -> true``
*********************************
<code>not</code>
==============================
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logický operátor negace * ``not(true) -> false``
* ``not(premium)``
*********************************
<code>typeMatch</code>
==============================
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
Odpovídá typu sloupce. Jde použít jenom ve vzoru expressions.number odpovídá krátké celé číslo, long, double, float nebo desetinné číslo, integrální odpovídá řečeno, celé číslo, desetinné části, dlouhý shody, double, float, decimal a data a času typu datum a časové razítko shody * ``typeMatch(type, 'number') -> true``
* ``typeMatch('date', 'number') -> false``
*********************************
<code>toShort</code>
==============================
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => short</b></code><br/><br/>
Převede všechny číselné nebo řetězcové na krátkou hodnotu. Volitelné desítkového formátu Java je možné pro převod. Ořízne všechny celé číslo, long float, double * ``toShort(123) -> 123``
* ``toShort('123') -> 123``
* ``toShort('$123', '$###') -> 123``
*********************************
<code>toInteger</code>
==============================
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => integer</b></code><br/><br/>
Převede všechny číselné nebo řetězcové na celočíselnou hodnotu. Volitelné desítkového formátu Java je možné pro převod. Ořízne všechny long float, double * ``toInteger(123) -> 123``
* ``toInteger('123') -> 123``
* ``toInteger('$123', '$###') -> 123``
*********************************
<code>toLong</code>
==============================
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => long</b></code><br/><br/>
Převede všechny číselné nebo řetězcové na dlouhou hodnotu. Volitelné desítkového formátu Java je možné pro převod. Ořízne všechny float, double * ``toLong(123) -> 123``
* ``toLong('123') -> 123``
* ``toLong('$123', '$###') -> 123``
*********************************
<code>toFloat</code>
==============================
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => float</b></code><br/><br/>
Převede všechny číselné nebo řetězcové hodnoty typu float. Volitelné desítkového formátu Java je možné pro převod. Ořízne všechny double * ``toFloat(123.45) -> 123.45``
* ``toFloat('123.45') -> 123.45``
* ``toFloat('$123.45', '$###.00') -> 123.45``
*********************************
<code>toDouble</code>
==============================
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string]) => double</b></code><br/><br/>
Převede všechny číselné nebo řetězcové na dvojitou hodnotu. Volitelné desítkového formátu Java je možné pro převod.
* ``toDouble(123.45) -> 123.45``
* ``toDouble('123.45') -> 123.45``
* ``toDouble('$123.45', '$###.00') -> 123.45``
*********************************
<code>toDecimal</code>
==============================
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : integral], [<i>&lt;value3&gt;</i> : integral], [<i>&lt;value4&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
Převede všechny číselné nebo řetězcové na desítkovou hodnotu. Pokud nejsou zadané hodnot precision a scale, je výchozí (10,2). Volitelné desítkového formátu Java je možné pro převod.
* ``toDecimal(123.45) -> 123.45``
* ``toDecimal('123.45', 8, 4) -> 123.4500``
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``
*********************************
<code>equals</code>
==============================
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Porovnání rovnosti operátor. Stejné, jako je == – operátor * ``equals(12, 24) -> false``
* ``12==24 -> false``
* ``'abc'=='abc' -> true``
*********************************
<code>notEquals</code>
==============================
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operátor nerovná porovnání. Stejné jako! = – operátor * ``12!=24 -> true``
* ``'abc'!='abc' -> false``
*********************************
<code>greater</code>
==============================
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Větší operátor porovnání. Stejné jako > – operátor * ``greater(12, 24) -> false``
* ``'abcd' > 'abc' -> true``
*********************************
<code>lesser</code>
==============================
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Porovnání méně operátor. Stejné jako < – operátor * ``lesser(12 < 24) -> true``
* ``'abcd' < 'abc' -> false``
*********************************
<code>greaterOrEqual</code>
==============================
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Porovnání větší než nebo rovno – operátor Stejné jako > = – operátor * ``greaterOrEqual(12, 12) -> false``
* ``'abcd' >= 'abc' -> true``
*********************************
<code>lesserOrEqual</code>
==============================
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Porovnání menší než nebo rovno – operátor. Stejné jako < = – operátor * ``lesserOrEqual(12, 12) -> true``
* ``'abcd' <= 'abc' -> false``
*********************************
<code>greatest</code>
==============================
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Vrátí největší hodnotu mezi seznam hodnot jako vstup. Vrátí hodnotu null, pokud všechny vstupy mají hodnotu null * ``greatest(10, 30, 15, 20) -> 30``
* ``greatest(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2011'``
*********************************
<code>least</code>
==============================
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Porovnání menší než nebo rovno – operátor. Stejné jako < = – operátor * ``least(10, 30, 15, 20) -> 10``
* ``least(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2000'``
*********************************
<code>power</code>
==============================
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Vyvolá jednu číslici k elektrické energie jiného * ``power(10, 2) -> 100``
*********************************
<code>sqrt</code>
==============================
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá druhou odmocninu čísla. * ``sqrt(9) -> 3``
*********************************
<code>cbrt</code>
==============================
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítat datové krychle odmocninu čísla. * ``cbrt(8) -> 2.0``
*********************************
<code>negate</code>
==============================
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Neguje číslo. Zapne kladná čísla, záporná a naopak * ``negate(13) -> -13``
*********************************
<code>cos</code>
==============================
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá hodnotu kosinus * ``cos(10) -> -0.83907152907``
*********************************
<code>acos</code>
==============================
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá hodnotu inverzní consine * ``acos(1) -> 0.0``
*********************************
<code>cosh</code>
==============================
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočte hyperbolický kosinus hodnoty * ``cosh(0) -> 1.0``
*********************************
<code>sin</code>
==============================
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá hodnotu sinu * ``sin(2) -> 0.90929742682``
*********************************
<code>asin</code>
==============================
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá hodnotu inverzní sinus * ``asin(0) -> 0.0``
*********************************
<code>sinh</code>
==============================
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá hodnotu hyperbolického sinu * ``sinh(0) -> 0.0``
*********************************
<code>tan</code>
==============================
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá hodnotu tangentu * ``tan(0) -> 0.0``
*********************************
<code>atan</code>
==============================
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá hodnotu inverzní tangens * ``atan(0) -> 0.0``
*********************************
<code>tanh</code>
==============================
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá hodnotu hyperbolického tangentu * ``tanh(0) -> 0.0``
*********************************
<code>atan2</code>
==============================
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Vrací úhel v radiánech mezi kladnou osu x v rovině a souřadnice bodu * ``atan2(0, 0) -> 0.0``
*********************************
<code>factorial</code>
==============================
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
Vypočítá faktoriál čísla. * ``factorial(5) -> 120``
*********************************
<code>floor</code>
==============================
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Vrátí největší celé číslo ne větší než počet * ``floor(-0.1) -> -1``
*********************************
<code>ceil</code>
==============================
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Vrátí nejmenší celé číslo menší než počet * ``ceil(-0.1) -> 0``
*********************************
<code>degrees</code>
==============================
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Převede radiány na stupně * ``degrees(3.141592653589793) -> 180``
*********************************
<code>log</code>
==============================
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
Vypočítá hodnotu protokolu. Volitelné základ může být zadáno jinak Eulerova číslo, pokud se používá * ``log(100, 10) -> 2``
*********************************
<code>log10</code>
==============================
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá hodnotu protokolu založené na základní 10 * ``log10(100) -> 2``
*********************************
<code>round</code>
==============================
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
Zaokrouhlí číslo daným volitelné škálování a volitelné režimu zaokrouhlování. Pokud je vynechán škálování je použita jako výchozí na hodnotu 0.  Pokud je vynechán režim je výchozí ROUND_HALF_UP(5). Hodnoty pro zaokrouhlení obsahoval: 1 – 2 ROUND_UP – ROUND_DOWN 3 - ROUND_CEILING 4 - ROUND_FLOOR 5 - ROUND_HALF_UP 6 - 7 - ROUND_HALF_EVEN 8 - ROUND_HALF_DOWN ROUND_UNNECESSARY * ``round(100.123) -> 100.0``
* ``round(2.5, 0) -> 3.0``
* ``round(5.3999999999999995, 2, 7) -> 5.40``
*********************************
<code>currentDate</code>
==============================
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
Získá aktuální datum, kdy se tato úloha se spustí. Můžete předat volitelné časového pásma ve formě 'GMT', "PST", "UTC", "Amerika/Kajmanské". Místní časové pásmo je použito jako výchozí.
* ``currentDate() -> 12-12-2030``
* ``currentDate('PST') -> 12-31-2050``
*********************************
<code>currentTimestamp</code>
==============================
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
Získá aktuální časové razítko, když se úloha spustí s místním časovém pásmu * ``currentTimestamp() -> 12-12-2030T12:12:12``
*********************************
<code>toDate</code>
==============================
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
Převede řetězce na datum zadaný formát volitelné data. Pokud je vynechán formát data, jsou přijaty následující kombinace. [ yyyy, yyyy-[M]M, yyyy-[M]M-[d]d, yyyy-[M]M-[d]d, yyyy-[M]M-[d]d, yyyy-[M]M-[d]dT* ] * ``toDate('2012-8-8') -> 2012-8-8``
* ``toDate('12/12/2012', 'MM/dd/yyyy') -> 2012-12-12``
*********************************
<code>toTimestamp</code>
==============================
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code><br/><br/>
Převede řetězec na datum zadané ve formátu volitelné časového razítka. Pokud je vynechán časové razítko výchozí vzor rrrr-[M] M [d]-d hh: mm: [. f...] se používá * ``toTimestamp('2016-12-31 00:12:00') -> 2012-8-8T00:12:00``
* ``toTimestamp('2016/12/31T00:12:00', 'MM/dd/yyyyThh:mm:ss') -> 2012-12-12T00:12:00``
*********************************
<code>toUTC</code>
==============================
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Časové razítko se převede na UTC. Můžete předat volitelné časového pásma ve formě 'GMT', "PST", "UTC", "Amerika/Kajmanské". Je použita jako výchozí na aktuální časové pásmo * ``toUTC(currentTimeStamp()) -> 12-12-2030T19:18:12``
* ``toUTC(currentTimeStamp(), 'Asia/Seoul') -> 12-13-2030T11:18:12``
*********************************
<code>currentUTC</code>
==============================
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
Získá aktuální časové razítko ve formátu UTC. Můžete předat volitelné časového pásma ve formě 'GMT', "PST", "UTC", "Amerika/Kajmanské". Je použita jako výchozí na aktuální časové pásmo * ``currentUTC() -> 12-12-2030T19:18:12``
* ``currentUTC('Asia/Seoul') -> 12-13-2030T11:18:12``
*********************************
<code>month</code>
==============================
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Získá hodnotu měsíce data nebo časového razítka * ``month(toDate('2012-8-8')) -> 8``
*********************************
<code>year</code>
==============================
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Získá hodnotu roku datum * ``year(toDate('2012-8-8')) -> 2012``
*********************************
<code>hour</code>
==============================
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Získá hodnotu představující hodinu časové razítko. Můžete předat volitelné časového pásma ve formě 'GMT', "PST", "UTC", "Amerika/Kajmanské". Místní časové pásmo je použito jako výchozí.
* ``hour(toTimestamp('2009-07-30T12:58:59')) -> 12``
* ``hour(toTimestamp('2009-07-30T12:58:59'), 'PST') -> 12``
*********************************
<code>minute</code>
==============================
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Získá hodnotu minut časového razítka. Můžete předat volitelné časového pásma ve formě 'GMT', "PST", "UTC", "Amerika/Kajmanské". Místní časové pásmo je použito jako výchozí.
* ``minute(toTimestamp('2009-07-30T12:58:59')) -> 58``
* ``minute(toTimestamp('2009-07-30T12:58:59', 'PST')) -> 58``
*********************************
<code>second</code>
==============================
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Získá druhý hodnotu data. Můžete předat volitelné časového pásma ve formě 'GMT', "PST", "UTC", "Amerika/Kajmanské". Místní časové pásmo je použito jako výchozí.
* ``second(toTimestamp('2009-07-30T12:58:59')) -> 59``
*********************************
<code>dayOfMonth</code>
==============================
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Načte den měsíce v hodnotě data * ``dayOfMonth(toDate('2018-06-08')) -> 08``
*********************************
<code>dayOfWeek</code>
==============================
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Načte den týdne v hodnotě data. 1 – neděle, 2 - pondělí..., 7 – sobota * ``dayOfWeek(toDate('2018-06-08')) -> 7``
*********************************
<code>dayOfYear</code>
==============================
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Načte den roku v hodnotě data * ``dayOfYear(toDate('2016-04-09')) -> 100``
*********************************
<code>weekOfYear</code>
==============================
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Získá týden roku v hodnotě data * ``weekOfYear(toDate('2008-02-20')) -> 8``
*********************************
<code>lastDayOfMonth</code>
==============================
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
Získá datum posledního měsíce v hodnotě data * ``lastDayOfMonth(toDate('2009-01-12')) -> 2009-01-31``
*********************************
<code>monthsBetween</code>
==============================
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;time zone&gt;</i> : boolean], [<i>&lt;value4&gt;</i> : string]) => double</b></code><br/><br/>
Získá počet měsíců mezi dvěma datesYou předáním volitelné časového pásma ve formě 'GMT', "PST", "UTC", "Amerika/Kajmanské". Místní časové pásmo je použito jako výchozí.
* ``monthsBetween(toDate('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``
*********************************
<code>addMonths</code>
==============================
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral) => datetime</b></code><br/><br/>
Přidat měsíců k datu nebo časového razítka * ``addMonths(toDate('2016-08-31'), 1) -> 2016-09-30``
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> 2016-08-31 10:10:10``
*********************************
<code>addDays</code>
==============================
<code><b>addDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to add&gt;</i> : integral) => datetime</b></code><br/><br/>
Přidání dní do data nebo časového razítka. Stejné jako + – operátor pro datum * ``addDays(toDate('2016-08-08'), 1) -> 2016-08-09``
*********************************
<code>subDays</code>
==============================
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Odečte měsíců od data. Totéž jako-– operátor pro datum * ``subDays(toDate('2016-08-08'), 1) -> 2016-08-09``
*********************************
<code>subMonths</code>
==============================
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Odečíst měsíců od data nebo časového razítka * ``subMonths(toDate('2016-09-30'), 1) -> 2016-08-31``
*********************************
<code>nextSequence</code>
==============================
<code><b>nextSequence() => long</b></code><br/><br/>
Vrátí další jedinečný pořadí. Číslo je po sobě jdoucích pouze v rámci oddílu a má předponu ID oddílu * ``nextSequence() -> 12313112``
*********************************
<code>md5</code>
==============================
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Vypočítá hodnotou hash MD5 sadu sloupců s různými primitivní datové typy a vrátí řetězec šestnáctkových 32 znaků. Je možné vypočítat otisk prstu pro řádek * ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'c1527622a922c83665e49835e46350fe'``
*********************************
<code>sha1</code>
==============================
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Vypočítá hodnotou hash SHA-1 sadu sloupců s různými primitivní datové typy a vrátí řetězec šestnáctkových 40 znaků. Je možné vypočítat otisk prstu pro řádek * ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '63849fd2abb65fbc626c60b1f827bd05573f0cea'``
*********************************
<code>sha2</code>
==============================
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
Vypočítá hodnotou hash SHA-2 sadu sloupců s různými primitivní datové typy, které daný bitovou délku, která mohou být pouze hodnoty 0(256), 224, 256, 384, 512. Je možné vypočítat otisk prstu pro řádek * ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'd3b2bff62c3a00e9370b1ac85e428e661a7df73959fa1a96ae136599e9ee20fd'``
*********************************
<code>crc32</code>
==============================
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
Vypočítá hodnotu hash CRC32 sadu sloupců s různými primitivní datové typy, které daný bitovou délku, která mohou být pouze hodnoty 0(256) 224, 256, 384, 512. Je možné vypočítat otisk prstu pro řádek * ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689``
*********************************
<code>isInsert</code>
==============================
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Kontroluje, pokud je označen řádek pro vložení. Transformace, neměl zabrat víc než jeden vstupní datový proud, můžete předat index (založený na jeden) datového proudu. Výchozí hodnota pro index datového proudu * ``isInsert() -> true``
* ``isInsert(1) -> false``
*********************************
<code>isUpdate</code>
==============================
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Kontroluje, pokud je označen řádek pro aktualizace. Transformace, neměl zabrat víc než jeden vstupní datový proud, můžete předat index (založený na jeden) datového proudu. Výchozí hodnota pro index datového proudu * ``isUpdate() -> true``
* ``isUpdate(1) -> false``
*********************************
<code>isDelete</code>
==============================
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Kontroluje se, pokud řádku je označená k odstranění. Transformace, neměl zabrat víc než jeden vstupní datový proud, můžete předat index (založený na jeden) datového proudu. Výchozí hodnota pro index datového proudu * ``isDelete() -> true``
* ``isDelete(1) -> false``
*********************************
<code>isMatch</code>
==============================
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Kontroluje, pokud je nalezena shoda řádku na vyhledávání. Transformace, neměl zabrat víc než jeden vstupní datový proud, můžete předat index (založený na jeden) datového proudu. Výchozí hodnota pro index datového proudu je 1 * ``isMatch() -> true``
* ``isMatch(1) -> false``
*********************************
<code>isError</code>
==============================
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Kontroluje, pokud je řádek označeny jako chyba. Transformace, neměl zabrat víc než jeden vstupní datový proud, můžete předat index (založený na jeden) datového proudu. Výchozí hodnota pro index datového proudu * ``isError() -> true``
* ``isError(1) -> false``
*********************************
<code>isIgnore</code>
==============================
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Kontroluje, pokud se mají ignorovat označí řádku. Transformace trvá víc než jeden vstupní datový proud, můžete předat index (založený na jeden) datového proudu. Výchozí hodnota pro index datového proudu * ``isIgnore() -> true``
* ``isIgnore(1) -> false``
*********************************
<code>sum</code>
==============================
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Získá agregace součet číselný sloupec * ``sum(col) -> value``
*********************************
<code>sumIf</code>
==============================
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Podle kritérií získá agregace součet číselný sloupec. Podmínka může být založen na libovolný sloupec * ``sumIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumIf(true, sales) -> SUM(sales) ``
*********************************
<code>sumDistinct</code>
==============================
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Získá agregace součet různých hodnot položky číselný sloupec * ``sumDistinct(col) -> value``
*********************************
<code>sumDistinctIf</code>
==============================
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Podle kritérií získá agregace součet číselný sloupec. Podmínka může být založen na libovolný sloupec * ``sumDistinctIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumDistinctIf(true, sales) -> SUM(sales) ``
*********************************
<code>count</code>
==============================
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
Získá souhrnný počet hodnot. Pokud není zadána volitelný sloupec, ignoruje hodnoty NULL v počtu * ``count(custId) -> 100``
* ``count(custId, custName) -> 50``
* ``count() -> 125``
* ``count(iif(isNull(custId), 1, NULL)) -> 5``
*********************************
<code>countIf</code>
==============================
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
Podle kritérií získá souhrnný počet hodnot. Pokud není zadána volitelný sloupec, ignoruje hodnoty NULL v počtu * ``countIf(state == 'CA' && commission < 10000, name) -> 100``
*********************************
<code>countDistinct</code>
==============================
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
Získá souhrnný počet různých hodnot položky sadu sloupců * ``countDistinct(custId, custName) -> 60``
*********************************
<code>avg</code>
==============================
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Získá průměrné hodnoty sloupce * ``avg(sales) -> 7523420.234``
*********************************
<code>avgIf</code>
==============================
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Podle kritérií získá průměrné hodnoty sloupce * ``avgIf(region == 'West', sales) -> 7523420.234``
*********************************
<code>mean</code>
==============================
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Získá střední hodnoty pro sloupec. Stejné jako průměr * ``mean(sales) -> 7523420.234``
*********************************
<code>meanIf</code>
==============================
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Podle kritérií získá střední hodnoty pro sloupec. Stejné jako avgIf * ``meanIf(region == 'West', sales) -> 7523420.234``
*********************************
<code>min</code>
==============================
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Získá minimální hodnotu sloupce * ``min(sales) -> 00.01``
* ``min(orderDate) -> 12/12/2000``
*********************************
<code>minIf</code>
==============================
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Na základě kritérií, získá minimální hodnotu sloupce * ``minIf(region == 'West', sales) -> 00.01``
*********************************
<code>max</code>
==============================
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Získá maximální hodnotu sloupce * ``MAX(sales) -> 12312131.12``
*********************************
<code>maxIf</code>
==============================
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Na základě kritérií, získá maximální hodnotu sloupce * ``maxIf(region == 'West', sales) -> 99999.56``
*********************************
<code>stddev</code>
==============================
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Získá směrodatné odchylky poměru sloupec * ``stdDev(sales) -> 122.12``
*********************************
<code>stddevIf</code>
==============================
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií, získá směrodatné odchylky poměru sloupec * ``stddevIf(region == 'West', sales) -> 122.12``
*********************************
<code>stddevPopulation</code>
==============================
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Získá směrodatná odchylka základního souboru sloupce * ``stddevPopulation(sales) -> 122.12``
*********************************
<code>stddevPopulationIf</code>
==============================
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií, získá směrodatná odchylka základního souboru sloupce * ``stddevPopulationIf(region == 'West', sales) -> 122.12``
*********************************
<code>stddevSample</code>
==============================
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Získá ukázkový směrodatné odchylky poměru sloupec * ``stddevSample(sales) -> 122.12``
*********************************
<code>stddevSampleIf</code>
==============================
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií, získá směrodatná odchylka vzorku sloupce * ``stddevSampleIf(region == 'West', sales) -> 122.12``
*********************************
<code>variance</code>
==============================
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Získá odchylku sloupce * ``variance(sales) -> 122.12``
*********************************
<code>varianceIf</code>
==============================
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií, získá odchylku sloupce * ``varianceIf(region == 'West', sales) -> 122.12``
*********************************
<code>variancePopulation</code>
==============================
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Získá odchylku základního souboru sloupce * ``variancePopulation(sales) -> 122.12``
*********************************
<code>variancePopulationIf</code>
==============================
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií, získá odchylku základního souboru sloupce * ``variancePopulationIf(region == 'West', sales) -> 122.12``
*********************************
<code>varianceSample</code>
==============================
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Získá neposunutého odchylku sloupce * ``varianceSample(sales) -> 122.12``
*********************************
<code>varianceSampleIf</code>
==============================
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií, získá neposunutého odchylku sloupce * ``varianceSampleIf(region == 'West', sales) -> 122.12``
*********************************
<code>covariancePopulation</code>
==============================
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Získá kovarianci mezi dvěma sloupci * ``covariancePopulation(sales, profit) -> 122.12``
*********************************
<code>covariancePopulationIf</code>
==============================
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií, získá kovarianci dvou sloupců * ``covariancePopulationIf(region == 'West', sales) -> 122.12``
*********************************
<code>covarianceSample</code>
==============================
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Získá kovarianci dvou sloupců * ``covarianceSample(sales, profit) -> 122.12``
*********************************
<code>covarianceSampleIf</code>
==============================
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií, získá kovarianci dvou sloupců * ``covarianceSampleIf(region == 'West', sales, profit) -> 122.12``
*********************************
<code>kurtosis</code>
==============================
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Získá míra fluktuace sloupce * ``kurtosis(sales) -> 122.12``
*********************************
<code>kurtosisIf</code>
==============================
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií, získá míra fluktuace sloupce * ``kurtosisIf(region == 'West', sales) -> 122.12``
*********************************
<code>skewness</code>
==============================
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Získá hodnoty sloupce * ``skewness(sales) -> 122.12``
*********************************
<code>skewnessIf</code>
==============================
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií, získá hodnoty sloupce * ``skewnessIf(region == 'West', sales) -> 122.12``
*********************************
<code>first</code>
==============================
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Získá první hodnotu sloupce skupiny. Pokud je vynechán druhý parametr, Ignorovat hodnoty Null, předpokládá se jako NEPRAVDA. * ``first(sales) -> 12233.23``
* ``first(sales, false) -> NULL``
*********************************
<code>last</code>
==============================
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Získá poslední hodnotu sloupce skupiny. Pokud je vynechán druhý parametr, Ignorovat hodnoty Null, předpokládá se false * ``last(sales) -> 523.12``
* ``last(sales, false) -> NULL``
*********************************
<code>lag</code>
==============================
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Získá hodnotu prvního parametru vyhodnotit n řádků před aktuální řádek. Druhý parametr je počet řádků do minulosti. Výchozí hodnota je 1. Pokud libovolný počet řádků nejsou určena výchozí hodnota vrátí hodnotu null * ``lag(amount, 2) -> 60``
* ``lag(amount, 2000, 100) -> 100``
*********************************
<code>lead</code>
==============================
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Získá hodnotu první parametr vyhodnotit n řádek po řádku aktuální. Druhý parametr je počet řádků, které mají těšit. Výchozí hodnota je 1. Pokud libovolný počet řádků nejsou určena výchozí hodnota vrátí hodnotu null * ``lead(amount, 2) -> 60``
* ``lead(amount, 2000, 100) -> 100``
*********************************
<code>cumeDist</code>
==============================
<code><b>cumeDist() => integer</b></code><br/><br/>
Funkce CumeDist vypočítá pozice hodnotu vzhledem k všechny hodnoty v oddílu. Výsledkem je počet řádků předchozí nebo rovna hodnotě aktuálního řádku v pořadí oddílu dělený celkovým počtem řádků v oddílu okna. Všechny hodnoty tie v pořadí vyhodnotí na stejné pozici.
* ``cumeDist() -> 1``
*********************************
<code>nTile</code>
==============================
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
Funkci ntile povolen rozděluje řádků pro každý oddíl okno do `n` kbelíků, v rozsahu od 1 do nejvýše `n`. Plechovka hodnoty se budou lišit podle nanejvýš 1. Pokud počet řádků v oddílu nejsou rovnoměrně rozdělit do počtu intervalů, zbývající hodnoty jsou distribuované jeden na kbelík, počínaje prvním bloku. Funkci ntile povolen je zvláště užitečná pro výpočet tertiles, Kvartily, deciles a další běžné souhrnnou statistiku. Funkce vypočítá dvě proměnné, které během inicializace: Velikost regulární intervalu a počtu intervalů, které budou mít jeden další řádek přidá k němu (když řádky rovnoměrně nehodí do počet kbelíků); obě proměnné jsou založeny na velikost aktuálního oddílu. Během výpočtu uchovává informace o funkci aktuální číslo řádku, aktuální číslo kbelíku a číslo řádku, ve kterém se do kbelíku změní (bucketThreshold). Pokud aktuální řádek číslo dosáhne kontejneru prahová hodnota, jeden je zvýšení hodnoty intervalu a prahová hodnota se zvyšuje podle velikosti intervalu (plus jeden navíc, pokud je, aby bylo vytvořeno v aktuálním bloku).
* ``nTile() -> 1``
* ``nTile(numOfBuckets) -> 1``
*********************************
<code>rank</code>
==============================
<code><b>rank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
Vypočítá pořadí hodnoty ve skupině hodnot. Výsledkem je jednu plus počet řádků předchozí nebo rovna hodnotě aktuálního řádku v pořadí oddílu. Hodnoty vytvoří mezery v posloupnosti. Počet rozměrů funguje, i když nejsou seřazená data a hledá změnu hodnoty * ``rank(salesQtr, salesAmt) -> 1``
*********************************
<code>denseRank</code>
==============================
<code><b>denseRank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
Vypočítá pořadí hodnoty ve skupině hodnot. Výsledkem je jednu plus počet řádků předchozí nebo rovna hodnotě aktuálního řádku v pořadí oddílu. Hodnoty nevytvoří mezery v posloupnosti. Kompaktní pořadí funguje i v případě, že data nejsou seřazená a hledá změnu hodnoty * ``denseRank(salesQtr, salesAmt) -> 1``
*********************************
<code>rowNumber</code>
==============================
<code><b>rowNumber() => integer</b></code><br/><br/>
Přiřadí sekvenční řádek číslování řádků v okně počínaje 1 * ``rowNumber() -> 1``
