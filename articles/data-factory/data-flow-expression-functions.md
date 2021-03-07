---
title: Funkce výrazu v toku dat mapování
description: Přečtěte si o funkcích výrazů v mapování toku dat.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/04/2021
ms.openlocfilehash: ec1ea7d727278076944b8cc11f47a1af587e6591
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102440152"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>Výrazy transformace dat v toku mapování dat

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="expression-functions"></a>Funkce výrazů

V Data Factory pomocí jazyka výrazů funkce mapování toku dat nakonfigurujte transformace dat.
___
### <code>abs</code>
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Absolutní hodnota čísla  
* ``abs(-20) -> 20``  
* ``abs(10) -> 10``  
___   
### <code>acos</code>
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá inverzní hodnotu kosinusu.  
* ``acos(1) -> 0.0``  
___
### <code>add</code>
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Přidá dvojici řetězců nebo čísel. Přidá datum do počtu dní. Přidá dobu trvání do časového razítka. Připojí jedno pole podobného typu k druhému. Stejné jako u operátoru +.  
* ``add(10, 20) -> 30``  
* ``10 + 20 -> 30``  
* ``add('ice', 'cream') -> 'icecream'``  
* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``  
* ``add(toDate('2012-12-12'), 3) -> toDate('2012-12-15')``  
* ``toDate('2012-12-12') + 3 -> toDate('2012-12-15')``  
* ``[10, 20] + [30, 40] -> [10, 20, 30, 40]``  
* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``  
___
### <code>addDays</code>
<code><b>addDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to add&gt;</i> : integral) => datetime</b></code><br/><br/>
Přidejte dny k datu nebo časovému razítku. Stejné jako u operátoru + pro datum.  
* ``addDays(toDate('2016-08-08'), 1) -> toDate('2016-08-09')``  
___
### <code>addMonths</code>
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral, [<i>&lt;value3&gt;</i> : string]) => datetime</b></code><br/><br/>
Přidejte měsíce k datu nebo časovému razítku. Volitelně můžete časové pásmo předat.  
* ``addMonths(toDate('2016-08-31'), 1) -> toDate('2016-09-30')``  
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> toTimestamp('2016-08-31 10:10:10')``  
___
### <code>and</code>
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logický operátor AND Stejné jako &&.  
* ``and(true, false) -> false``  
* ``true && false -> false``  
___
### <code>asin</code>
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá inverzní hodnotu sinus.  
* ``asin(0) -> 0.0``  
___
### <code>atan</code>
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá hodnotu inverzní tangens.  
* ``atan(0) -> 0.0``  
___
### <code>atan2</code>
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Vrátí úhel v radiánech mezi kladnou osou x rovinou a bodem daným souřadnicemi.  
* ``atan2(0, 0) -> 0.0``  
___
### <code>between</code>
<code><b>between(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any, <i>&lt;value3&gt;</i> : any) => boolean</b></code><br/><br/>
Kontroluje, zda je první hodnota mezi dvěma dalšími hodnotami (včetně). Hodnoty čísel, řetězců a hodnot DateTime se dají porovnat. * ``between(10, 5, 24)``
* ``true``
* ``between(currentDate(), currentDate() + 10, currentDate() + 20)``
* ``false``
___
### <code>bitwiseAnd</code>
<code><b>bitwiseAnd(<i>&lt;value1&gt;</i> : integral, <i>&lt;value2&gt;</i> : integral) => integral</b></code><br/><br/>
Bitový operátor and mezi celočíselnými typy. Stejné jako operátor & * ``bitwiseAnd(0xf4, 0xef)``
* ``0xe4``
* ``(0xf4 & 0xef)``
* ``0xe4``
___
### <code>bitwiseOr</code>
<code><b>bitwiseOr(<i>&lt;value1&gt;</i> : integral, <i>&lt;value2&gt;</i> : integral) => integral</b></code><br/><br/>
Bitový operátor OR mezi celočíselnými typy. Stejné jako | podnikatel * ``bitwiseOr(0xf4, 0xef)``
* ``0xff``
* ``(0xf4 | 0xef)``
* ``0xff``
___
### <code>bitwiseXor</code>
<code><b>bitwiseXor(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Bitový operátor OR mezi celočíselnými typy. Stejné jako | podnikatel * ``bitwiseXor(0xf4, 0xef)``
* ``0x1b``
* ``(0xf4 ^ 0xef)``
* ``0x1b``
* ``(true ^ false)``
* ``true``
* ``(true ^ true)``
* ``false``
___
### <code>blake2b</code>
<code><b>blake2b(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
Vypočítá Blake2 výtah sady sloupců s proměnlivými primitivními datovými typy s délkou bitu, který může být pouze násobkem 8 mezi 8 & 512. Dá se použít k výpočtu otisku prstu pro řádek. * ``blake2b(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4'))``
* ``'c9521a5080d8da30dffb430c50ce253c345cc4c4effc315dab2162dac974711d'``
___
### <code>blake2bBinary</code>
<code><b>blake2bBinary(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => binary</b></code><br/><br/>
Vypočítá Blake2 výtah sady sloupců s proměnlivými primitivními datovými typy s délkou bitu, který může být pouze násobkem 8 mezi 8 & 512. Dá se použít k výpočtu otisku prstu pro řádek. * ``blake2bBinary(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4'))``
* ``unHex('c9521a5080d8da30dffb430c50ce253c345cc4c4effc315dab2162dac974711d')``
___
### <code>case</code>
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
Na základě střídajících se podmínek používá jedna hodnota nebo druhá. Pokud je počet vstupů sudý, druhý je pro poslední podmínku nastaven na hodnotu NULL.  
* ``case(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``  
* ``case(10 + 20 == 25, 'bojjus', 'do' < 'go', 'gunchus') -> 'gunchus'``  
* ``isNull(case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus')) -> true``  
* ``case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus', 'dumbo') -> 'dumbo'``  
___
### <code>cbrt</code>
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá kořenovou složku datové krychle čísla.  
* ``cbrt(8) -> 2.0``  
___
### <code>ceil</code>
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Vrátí nejmenší celé číslo, které není menší než číslo.  
* ``ceil(-0.1) -> 0``  
___
### <code>coalesce</code>
<code><b>coalesce(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Vrátí první nenulovou hodnotu ze sady vstupů. Všechny vstupy by měly být stejného typu.  
* ``coalesce(10, 20) -> 10``  
* ``coalesce(toString(null), toString(null), 'dumbo', 'bo', 'go') -> 'dumbo'``  
___
### <code>collect</code>
<code><b>collect(<i>&lt;value1&gt;</i> : any) => array</b></code><br/><br/>
Shromáždí všechny hodnoty výrazu v agregované skupině do pole. Struktury je možné shromažďovat a transformovat do alternativních struktur během tohoto procesu. Počet položek bude stejný jako počet řádků v dané skupině a může obsahovat hodnoty null. Počet shromážděných položek by měl být malý.  
* ``collect(salesPerson)``
* ``collect(firstName + lastName))``
* ``collect(@(name = salesPerson, sales = salesAmount) )``
___
### <code>columnNames</code>
<code><b>columnNames(<i>&lt;value1&gt;</i> : string) => array</b></code><br/><br/>
Načte všechny výstupní sloupce pro datový proud. Jako druhý argument můžete předat volitelný název datového proudu.  
* ``columnNames()``
* ``columnNames('DeriveStream')``

___
### <code>columns</code>
<code><b>columns([<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
Načte všechny výstupní sloupce pro datový proud. Jako druhý argument můžete předat volitelný název datového proudu.   
* ``columns()``
* ``columns('DeriveStream')``
___
### <code>compare</code>
<code><b>compare(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => integer</b></code><br/><br/>
Porovná dvě hodnoty stejného typu. Vrátí záporné celé číslo, pokud hodnota1 < hodnota2, 0 Pokud hodnota1 = = hodnota2, kladné hodnoty, pokud hodnota1 > hodnota2.  
* ``(compare(12, 24) < 1) -> true``  
* ``(compare('dumbo', 'dum') > 0) -> true``  
___
### <code>concat</code>
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Zřetězí proměnný počet řetězců dohromady. Totéž jako operátor + s řetězci.  
* ``concat('dataflow', 'is', 'awesome') -> 'dataflowisawesome'``  
* ``'dataflow' + 'is' + 'awesome' -> 'dataflowisawesome'``  
* ``isNull('sql' + null) -> true``  
___
### <code>concatWS</code>
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Zřetězí proměnlivý počet řetězců spolu s oddělovačem. První parametr je oddělovač.  
* ``concatWS(' ', 'dataflow', 'is', 'awesome') -> 'dataflow is awesome'``  
* ``isNull(concatWS(null, 'dataflow', 'is', 'awesome')) -> true``  
* ``concatWS(' is ', 'dataflow', 'awesome') -> 'dataflow is awesome'``  
___
### <code>contains</code>
<code><b>contains(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => boolean</b></code><br/><br/>
Vrátí hodnotu true, pokud je libovolný prvek v zadaném poli vyhodnocen jako true v zadaném predikátu. Obsahuje očekává odkaz na jeden prvek ve funkci predikátu jako #item.  
* ``contains([1, 2, 3, 4], #item == 3) -> true``  
* ``contains([1, 2, 3, 4], #item > 5) -> false``  
___
### <code>cos</code>
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá hodnotu kosinusu.  
* ``cos(10) -> -0.8390715290764524``  
___
### <code>cosh</code>
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá hyperbolický kosinus hodnoty.  
* ``cosh(0) -> 1.0``  
___
### <code>crc32</code>
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
Vypočítá hodnotu hash CRC32 sady sloupců různých primitivních DataTypes dané bitové délky, která může být pouze z hodnot 0 (256), 224, 256, 384, 512. Dá se použít k výpočtu otisku prstu pro řádek.  
* ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689L``  
___
### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
Získá aktuální datum spuštění této úlohy. Můžete předat volitelné časové pásmo ve formátu "GMT", "PST", "UTC", "Amerika/Kajmanské". Místní časové pásmo se používá jako výchozí. `SimpleDateFormat`Pro dostupné formáty se podívejte na třídu Java. [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html). 
* ``currentDate() == toDate('2250-12-31') -> false``  
* ``currentDate('PST')  == toDate('2250-12-31') -> false``  
* ``currentDate('America/New_York')  == toDate('2250-12-31') -> false``  
___
### <code>currentTimestamp</code>
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
Získá aktuální časové razítko, kdy se úloha začne spouštět s místním časovým pásmem.  
* ``currentTimestamp() == toTimestamp('2250-12-31 12:12:12') -> false``  
___
### <code>currentUTC</code>
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
Získá aktuální časové razítko jako UTC. Pokud chcete, aby byl aktuální čas interpretován v jiném časovém pásmu než časové pásmo clusteru, můžete předat volitelné časové pásmo ve formátu "GMT", "PST", "UTC", "Amerika/Kajmanské". Ve výchozím nastavení je nastavené na aktuální časové pásmo. `SimpleDateFormat`Pro dostupné formáty se podívejte na třídu Java. [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html). Pro převod času UTC na jiné použití časového pásma `fromUTC()` .  
* ``currentUTC() == toTimestamp('2050-12-12 19:18:12') -> false``  
* ``currentUTC() != toTimestamp('2050-12-12 19:18:12') -> true``  
* ``fromUTC(currentUTC(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``  
___
### <code>dayOfMonth</code>
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Vrátí den v měsíci, který má dané datum.  
* ``dayOfMonth(toDate('2018-06-08')) -> 8``  
___
### <code>dayOfWeek</code>
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Vrátí den v týdnu daného data. 1 – neděle, 2 – pondělí..., 7 – sobota.  
* ``dayOfWeek(toDate('2018-06-08')) -> 6``  
___
### <code>dayOfYear</code>
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Načte den v roce na dané datum.  
* ``dayOfYear(toDate('2016-04-09')) -> 100``  
___
### <code>days</code>
<code><b>days(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Doba v milisekundách pro počet dní  
* ``days(2) -> 172800000L``  
___
### <code>degrees</code>
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Převede radiány na stupně.  
* ``degrees(3.141592653589793) -> 180``  
___
### <code>divide</code>
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Vydělí dvojici čísel. Stejné jako `/` operátor.  
* ``divide(20, 10) -> 2``  
* ``20 / 10 -> 2``  
___
### <code>endsWith</code>
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Kontroluje, zda řetězec končí zadaným řetězcem.  
* ``endsWith('dumbo', 'mbo') -> true``  
___
### <code>equals</code>
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operátor porovnání je rovno. Stejný jako operátor = =.  
* ``equals(12, 24) -> false``  
* ``12 == 24 -> false``  
* ``'bad' == 'bad' -> true``  
* ``isNull('good' == toString(null)) -> true``  
* ``isNull(null == null) -> true``  
___
### <code>equalsIgnoreCase</code>
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
Porovnání se rovná operátoru bez ohledu na velikost písmen. Stejné jako <= operátor>.  
* ``'abc'<=>'Abc' -> true``  
* ``equalsIgnoreCase('abc', 'Abc') -> true``  
___
### <code>escape</code>
<code><b>escape(<i>&lt;string_to_escape&gt;</i> : string, <i>&lt;format&gt;</i> : string) => string</b></code><br/><br/>
Řídí řetězec v závislosti na formátu. Hodnoty literálu pro přijatelný formát jsou "JSON", "XML", "ECMAScript", "HTML", "Java".
___
### <code>factorial</code>
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
Vypočítá faktoriál čísla.  
* ``factorial(5) -> 120``  
___
### <code>false</code>
<code><b>false() => boolean</b></code><br/><br/>
Vždy vrátí hodnotu false. Funkci použijte, `syntax(false())` Pokud je sloupec s názvem false.  
* ``(10 + 20 > 30) -> false``  
* ``(10 + 20 > 30) -> false()``
___
### <code>floor</code>
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Vrátí největší celé číslo, které není větší než číslo.  
* ``floor(-0.1) -> -1``  
___
### <code>fromBase64</code>
<code><b>fromBase64(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Zakóduje daný řetězec v base64.  
* ``fromBase64('Z3VuY2h1cw==') -> 'gunchus'``  
___
### <code>fromUTC</code>
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Převede na časové razítko od času UTC. Časové pásmo můžete volitelně předat ve formátu "GMT", "PST", "UTC", "Amerika/Kajmanské". Ve výchozím nastavení je nastavené na aktuální časové pásmo. `SimpleDateFormat`Pro dostupné formáty se podívejte na třídu Java. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``fromUTC(currentTimestamp()) == toTimestamp('2050-12-12 19:18:12') -> false``  
* ``fromUTC(currentTimestamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``  
___
### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Porovnání většího operátoru. Stejné jako operátor >.  
* ``greater(12, 24) -> false``  
* ``('dumbo' > 'dum') -> true``  
* ``(toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS') > toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``  
___
### <code>greaterOrEqual</code>
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Porovnání větší než nebo rovno operátoru. Stejné jako operátor >=.  
* ``greaterOrEqual(12, 12) -> true``  
* ``('dumbo' >= 'dum') -> true``  
___
### <code>greatest</code>
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Vrátí nejvyšší hodnotu ze seznamu hodnot jako vstup, který přeskakuje hodnoty null. Vrátí hodnotu null, pokud všechny vstupy mají hodnotu null.  
* ``greatest(10, 30, 15, 20) -> 30``  
* ``greatest(10, toInteger(null), 20) -> 20``  
* ``greatest(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2011-12-12')``  
* ``greatest(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS'), toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')``  
___
### <code>hasColumn</code>
<code><b>hasColumn(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => boolean</b></code><br/><br/>
Kontroluje hodnotu sloupce podle názvu v datovém proudu. Jako druhý argument můžete předat volitelný název datového proudu. Názvy sloupců, které jsou známy v době návrhu, by měly být řešeny stejným názvem. Počítané vstupy se nepodporují, ale můžete použít substituce parametrů.  
* ``hasColumn('parent')``  
___
### <code>hour</code>
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Získá hodnotu hodiny časového razítka. Můžete předat volitelné časové pásmo ve formátu "GMT", "PST", "UTC", "Amerika/Kajmanské". Místní časové pásmo se používá jako výchozí. `SimpleDateFormat`Pro dostupné formáty se podívejte na třídu Java. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``hour(toTimestamp('2009-07-30 12:58:59')) -> 12``  
* ``hour(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 12``  
___
### <code>hours</code>
<code><b>hours(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Doba trvání v milisekundách pro počet hodin  
* ``hours(2) -> 7200000L``  
___
### <code>iif</code>
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
Na základě podmínky platí jedna nebo druhá hodnota. Pokud není uvedeno jinak, považuje se za NULL. Obě hodnoty musí být kompatibilní (číselná, String...). * ``iif(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``  
* ``iif(10 > 30, 'dumbo', 'gumbo') -> 'gumbo'``  
* ``iif(month(toDate('2018-12-01')) == 12, 345.12, 102.67) -> 345.12``  
___
### <code>iifNull</code>
<code><b>iifNull(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => any</b></code><br/><br/>
Kontroluje, zda je první parametr null. Pokud není null, je vrácen první parametr. Pokud má hodnotu null, je vrácen druhý parametr. Pokud jsou zadány tři parametry, chování je stejné jako funkce IIF (isNull (hodnota1), hodnota2, hodnota3) a třetí parametr je vrácen, pokud první hodnota není null.  
* ``iifNull(10, 20) -> 10``  
* ``iifNull(null, 20, 40) -> 20``  
* ``iifNull('azure', 'data', 'factory') -> 'factory'``  
* ``iifNull(null, 'data', 'factory') -> 'data'``  
___
### <code>in</code>
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
Zkontroluje, zda je položka v poli.  
* ``in([10, 20, 30], 10) -> true``  
* ``in(['good', 'kid'], 'bad') -> false``  
___
### <code>initCap</code>
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Převede první písmeno každého slova na velká. Slova jsou označena jako oddělená prázdným znakem.  
* ``initCap('cool iceCREAM') -> 'Cool Icecream'``  
___
### <code>instr</code>
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
Vyhledá pozici (na základě 1) podřetězce v rámci řetězce. hodnota 0 se vrátí, pokud nebyla nalezena.  
* ``instr('dumbo', 'mbo') -> 3``  
* ``instr('microsoft', 'o') -> 5``  
* ``instr('good', 'bad') -> 0``  
___
### <code>isDelete</code>
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Zkontroluje, jestli je řádek označený k odstranění. Pro transformace s více než jedním vstupním datovým proudem můžete index (založený na 1) předat tomuto datovému proudu. Index datového proudu musí být buď 1, nebo 2 a výchozí hodnota je 1.  
* ``isDelete()``  
* ``isDelete(1)``  
___
### <code>isError</code>
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Zkontroluje, jestli je řádek označený jako chyba. Pro transformace s více než jedním vstupním datovým proudem můžete index (založený na 1) předat tomuto datovému proudu. Index datového proudu musí být buď 1, nebo 2 a výchozí hodnota je 1.  
* ``isError()``  
* ``isError(1)``  
___
### <code>isIgnore</code>
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Zkontroluje, jestli je řádek označený jako ignorovaný. Pro transformace s více než jedním vstupním datovým proudem můžete index (založený na 1) předat tomuto datovému proudu. Index datového proudu musí být buď 1, nebo 2 a výchozí hodnota je 1.  
* ``isIgnore()``  
* ``isIgnore(1)``  
___
### <code>isInsert</code>
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Zkontroluje, jestli je řádek označený pro vložení. Pro transformace s více než jedním vstupním datovým proudem můžete index (založený na 1) předat tomuto datovému proudu. Index datového proudu musí být buď 1, nebo 2 a výchozí hodnota je 1.  
* ``isInsert()``  
* ``isInsert(1)``  
___
### <code>isMatch</code>
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Zkontroluje, jestli se řádek při vyhledávání shodoval. Pro transformace s více než jedním vstupním datovým proudem můžete index (založený na 1) předat tomuto datovému proudu. Index datového proudu musí být buď 1, nebo 2 a výchozí hodnota je 1.  
* ``isMatch()``  
* ``isMatch(1)``  
___
### <code>isNull</code>
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Zkontroluje, jestli je hodnota NULL.  
* ``isNull(NULL()) -> true``  
* ``isNull('') -> false``  
___
### <code>isUpdate</code>
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Zkontroluje, jestli je řádek označený k aktualizaci. Pro transformace s více než jedním vstupním datovým proudem můžete index (založený na 1) předat tomuto datovému proudu. Index datového proudu musí být buď 1, nebo 2 a výchozí hodnota je 1.  
* ``isUpdate()``  
* ``isUpdate(1)``  
___
### <code>isUpsert</code>
<code><b>isUpsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Zkontroluje, jestli je řádek označený pro vložení. Pro transformace s více než jedním vstupním datovým proudem můžete index (založený na 1) předat tomuto datovému proudu. Index datového proudu musí být buď 1, nebo 2 a výchozí hodnota je 1.  
* ``isUpsert()``  
* ``isUpsert(1)``  
___
### <code>lastDayOfMonth</code>
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
Získá poslední datum měsíce daného data.  
* ``lastDayOfMonth(toDate('2009-01-12')) -> toDate('2009-01-31')``  
___
### <code>least</code>
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Porovnání menší než nebo rovno operátoru. Stejné jako operátor <=.  
* ``least(10, 30, 15, 20) -> 10``  
* ``least(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2000-12-12')``  
___
### <code>left</code>
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Extrahuje podřetězec začínající na indexu 1 s počtem znaků. Stejné jako podřetězec (str, 1, n).  
* ``left('bojjus', 2) -> 'bo'``  
* ``left('bojjus', 20) -> 'bojjus'``  
___
### <code>length</code>
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
Vrátí délku řetězce.  
* ``length('dumbo') -> 5``  
___
### <code>lesser</code>
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operátor porovnání less. Stejné jako operátor <.  
* ``lesser(12, 24) -> true``  
* ``('abcd' < 'abc') -> false``  
* ``(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') < toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``  
___
### <code>lesserOrEqual</code>
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Porovnání menší než nebo rovno operátoru. Stejné jako operátor <=.  
* ``lesserOrEqual(12, 12) -> true``  
* ``('dumbo' <= 'dum') -> false``  
___
### <code>levenshtein</code>
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
Získá levenshtein vzdálenost mezi dvěma řetězci.  
* ``levenshtein('boys', 'girls') -> 4``  
___
### <code>like</code>
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Vzor je řetězec, který se bude shodovat s doslova. Výjimky jsou následující speciální symboly: _ odpovídá jakémukoli jednomu znaku ve vstupu (podobně jako. v ```posix``` regulárních výrazech)% odpovídá nule nebo více znakům ve vstupu (podobně jako. * v ```posix``` regulárních výrazech).
Řídicí znak je ' '. Pokud řídicí znak před speciálním symbolem nebo jiným řídicím znakem, je následující znak porovnán. Řídicí znak není v žádném jiném znaku neplatný.  
* ``like('icecream', 'ice%') -> true``  
___
### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
Vyhledá pozici (na základě 1) podřetězce v řetězci, který spouští určitou pozici. Pokud je pozice vynechána, je považována za začátek řetězce. hodnota 0 se vrátí, pokud nebyla nalezena.  
* ``locate('mbo', 'dumbo') -> 3``  
* ``locate('o', 'microsoft', 6) -> 7``  
* ``locate('bad', 'good') -> 0``  
___
### <code>log</code>
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
Vypočítá hodnotu protokolu. Volitelná základní hodnota může být poskytnuta v jiném Eulerova čísle, pokud je použito.  
* ``log(100, 10) -> 2``  
___
### <code>log10</code>
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá hodnotu protokolu založenou na 10 Base.  
* ``log10(100) -> 2``  
___
### <code>lower</code>
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Malá písmena řetězce.  
* ``lower('GunChus') -> 'gunchus'``  
___
### <code>lpad</code>
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Vlevo vyplní řetězec doplněním zadaného odsazení, dokud nemá určitou délku. Pokud je řetězec větší nebo roven délce, je oříznut na délku.  
* ``lpad('dumbo', 10, '-') -> '-----dumbo'``  
* ``lpad('dumbo', 4, '-') -> 'dumb'``  
* ' ' Lpad (' Dumbo ', 8, ' <> ')-> ' <><dumbo'``  
___
### <code> LTrim</code>
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Levá ořízne řetězec úvodních znaků. Pokud není zadán druhý parametr, ořízne prázdné znaky. Jinak ořízne libovolný znak určený ve druhém parametru.  
* ``ltrim('  dumbo  ') -> 'dumbo  '``  
* ``ltrim('!--!du!mbo!', '-!') -> 'du!mbo!'``  
___
### <code>md5</code>
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Vypočítá výtah MD5 sady sloupců různých primitivních typů DataTypes a vrátí šestnáctkový řetězec 32 znaků. Dá se použít k výpočtu otisku prstu pro řádek.  
* ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '4ce8a880bd621a1ffad0bca905e1bc5a'``  
___
### <code>millisecond</code>
<code><b>millisecond(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Získá hodnotu milisekundy pro datum. Můžete předat volitelné časové pásmo ve formátu "GMT", "PST", "UTC", "Amerika/Kajmanské". Místní časové pásmo se používá jako výchozí. `SimpleDateFormat`Pro dostupné formáty se podívejte na třídu Java. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``  
___
### <code>milliseconds</code>
<code><b>milliseconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Doba v milisekundách pro počet milisekund  
* ``milliseconds(2) -> 2L``  
___
### <code>minus</code>
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Odečte čísla. Odečíst počet dní od data. Odečíst dobu trvání z časového razítka. Ododečte dvě časová razítka k získání rozdílů v milisekundách. Stejné jako operátor--.  
* ``minus(20, 10) -> 10``  
* ``20 - 10 -> 10``  
* ``minus(toDate('2012-12-15'), 3) -> toDate('2012-12-12')``  
* ``toDate('2012-12-15') - 3 -> toDate('2012-12-12')``  
* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``  
* ``toTimestamp('2019-02-03 05:21:34.851', 'yyyy-MM-dd HH:mm:ss.SSS') - toTimestamp('2019-02-03 05:21:36.923', 'yyyy-MM-dd HH:mm:ss.SSS') -> -2072``  
___
### <code>minute</code>
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Získá hodnotu minuty časového razítka. Můžete předat volitelné časové pásmo ve formátu "GMT", "PST", "UTC", "Amerika/Kajmanské". Místní časové pásmo se používá jako výchozí. `SimpleDateFormat`Pro dostupné formáty se podívejte na třídu Java. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``minute(toTimestamp('2009-07-30 12:58:59')) -> 58``  
* ``minute(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 58``  
___
### <code>minutes</code>
<code><b>minutes(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Doba trvání v milisekundách na počet minut  
* ``minutes(2) -> 120000L``  
___
### <code>mod</code>
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Zbytek páru čísel. Stejné jako operátor%.  
* ``mod(20, 8) -> 4``  
* ``20 % 8 -> 4``  
___
### <code>month</code>
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Získá hodnotu měsíce pro datum nebo časové razítko.  
* ``month(toDate('2012-8-8')) -> 8``  
___
### <code>monthsBetween</code>
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;roundoff&gt;</i> : boolean], [<i>&lt;time zone&gt;</i> : string]) => double</b></code><br/><br/>
Vrátí počet měsíců mezi dvěma kalendářními daty. Výpočet můžete zaokrouhlit. Můžete předat volitelné časové pásmo ve formátu "GMT", "PST", "UTC", "Amerika/Kajmanské". Místní časové pásmo se používá jako výchozí. `SimpleDateFormat`Pro dostupné formáty se podívejte na třídu Java. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``monthsBetween(toTimestamp('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``  
___
### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Vynásobí dvojici čísel. Stejné jako operátor *.  
* ``multiply(20, 10) -> 200``  
* ``20 * 10 -> 200``  
___
### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Negace čísla. Změní kladné hodnoty na záporné a naopak.  
* ``negate(13) -> -13``  
___
### <code>nextSequence</code>
<code><b>nextSequence() => long</b></code><br/><br/>
Vrátí další jedinečnou sekvenci. Číslo je po sobě po sobě pouze v rámci oddílu a je předponou partitionId.  
* ``nextSequence() == 12313112 -> false``  
___
### <code>normalize</code>
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></code><br/><br/>
Normalizuje řetězcovou hodnotu pro oddělení zvýrazněných znaků Unicode.  
* ``regexReplace(normalize('bo²s'), `\p{M}`, '') -> 'boys'``  
___
### <code>not</code>
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logický operátor negace.  
* ``not(true) -> false``  
* ``not(10 == 20) -> true``  
___
### <code>notEquals</code>
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operátor porovnání není rovno. Stejný jako operátor! =.  
* ``12 != 24 -> true``  
* ``'bojjus' != 'bo' + 'jjus' -> false``  
___
### <code>notNull</code>
<code><b>notNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Kontroluje, zda hodnota není NULL.  
* ``notNull(NULL()) -> false``  
* ``notNull('') -> true``  
___
### <code>null</code>
<code><b>null() => null</b></code><br/><br/>
Vrací hodnotu NULL. Funkci použijte, `syntax(null())` Pokud je sloupec s názvem null. Výsledkem jakékoli operace, která používá, je hodnota NULL.  
* ``isNull('dumbo' + null) -> true``  
* ``isNull(10 * null) -> true``  
* ``isNull('') -> false``  
* ``isNull(10 + 20) -> false``  
* ``isNull(10/0) -> true``  
___
### <code>or</code>
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logický operátor OR Stejné jako | |.  
* ``or(true, false) -> true``  
* ``true || false -> true``  
___
### <code>pMod</code>
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Kladné zbytky páru čísel.  
* ``pmod(-20, 8) -> 4``  
___
### <code>partitionId</code>
<code><b>partitionId() => integer</b></code><br/><br/>
Vrátí aktuální ID oddílu, ve kterém je vstupní řádek.  
* ``partitionId()``  
___
### <code>power</code>
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Umocní jedno číslo na jinou mocninu.  
* ``power(10, 2) -> 100``  
___
### <code>random</code>
<code><b>random(<i>&lt;value1&gt;</i> : integral) => long</b></code><br/><br/>
Vrátí náhodné číslo v rámci oddílu s nepovinným osivem. Počáteční hodnota musí být pevná a používá se ve spojení s identifikátorem partitionId pro vytvoření náhodných hodnot. * ``random(1) == 1 -> false``
___
### <code>regexExtract</code>
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
Extrahuje shodný dílčí řetězec pro daný vzor regulárního výrazu. Poslední parametr identifikuje skupinu shod a je-li vynechán, je nastaven na hodnotu 1. Použijte <regex> (back-quot) k vyhledání shody s řetězcem bez uvozovacího příkazu.  
* ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``  
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``  
___
### <code>regexMatch</code>
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
Zkontroluje, zda řetězec odpovídá danému vzoru regulárního výrazu. Použijte <regex> (back-quot) k vyhledání shody s řetězcem bez uvozovacího příkazu.  
* ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``  
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``  
___
### <code>regexReplace</code>
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Nahradí všechny výskyty vzorového regulárního výrazu jiným podřetězcem v daném řetězci <regex> . k vyhledání řetězce bez uvozovacích znaků se používá znaková uvozovka.  
* ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``  
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``  
___
### <code>regexSplit</code>
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
Rozdělí řetězec na základě oddělovače na základě regulárního výrazu a vrátí pole řetězců.  
* ``regexSplit('bojjusAgunchusBdumbo', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo']``  
* ``regexSplit('bojjusAgunchusBdumboC', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo', '']``  
* ``(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[1]) -> 'bojjus'``  
* ``isNull(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[20]) -> true``  
___
### <code>replace</code>
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, [<i>&lt;substring to replace&gt;</i> : string]) => string</b></code><br/><br/>
Nahradí všechny výskyty podřetězce jiným podřetězcem v daném řetězci. Pokud je poslední parametr vynechán, je výchozí hodnotou prázdný řetězec.  
* ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``  
* ``replace('doggie dog', 'dog', '') -> 'gie '``  
* ``replace('doggie dog', 'dog') -> 'gie '``  
___
### <code>reverse</code>
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Obrátí řetězec.  
* ``reverse('gunchus') -> 'suhcnug'``  
___
### <code>right</code>
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Extrahuje podřetězec s počtem znaků zprava. Totéž jako podřetězec (str, LENGTH (str)-n, n).  
* ``right('bojjus', 2) -> 'us'``  
* ``right('bojjus', 20) -> 'bojjus'``  
___
### <code>rlike</code>
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Zkontroluje, zda řetězec odpovídá danému vzoru regulárního výrazu.  
* ``rlike('200.50', `(\d+).(\d+)`) -> true``  
* ``rlike('bogus', `M[0-9]+.*`) -> false``  
___
### <code>round</code>
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
Zaokrouhlí číslo z daného volitelného měřítka a volitelného režimu zaokrouhlení. Pokud je měřítko vynecháno, je nastavené na hodnotu 0. Pokud je tento režim vynechán, je nastaven na hodnotu ROUND_HALF_UP (5). Mezi hodnoty zaokrouhlení patří 1-ROUND_UP 2-ROUND_DOWN 3-ROUND_CEILING 4-ROUND_FLOOR 5-ROUND_HALF_UP 6 – ROUND_HALF_DOWN 7-ROUND_HALF_EVEN 8-ROUND_UNNECESSARY.  
* ``round(100.123) -> 100.0``  
* ``round(2.5, 0) -> 3.0``  
* ``round(5.3999999999999995, 2, 7) -> 5.40``  
___
### <code>rpad</code>
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Doplní řetězec doplněním zadaného odsazení, dokud nemá určitou délku. Pokud je řetězec větší nebo roven délce, je oříznut na délku.  
* ``rpad('dumbo', 10, '-') -> 'dumbo-----'``  
* ``rpad('dumbo', 4, '-') -> 'dumb'``  
* ``rpad('dumbo', 8, '<>') -> 'dumbo<><'``  
___
### <code>rtrim</code>RTrim</code>
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Pravá ořízne řetězec koncových znaků. Pokud není zadán druhý parametr, ořízne prázdné znaky. Jinak ořízne libovolný znak určený ve druhém parametru.  
* ``rtrim('  dumbo  ') -> '  dumbo'``  
* ``rtrim('!--!du!mbo!', '-!') -> '!--!du!mbo'``  
___
### <code>second</code>
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Získá druhou hodnotu data. Můžete předat volitelné časové pásmo ve formátu "GMT", "PST", "UTC", "Amerika/Kajmanské". Místní časové pásmo se používá jako výchozí. `SimpleDateFormat`Pro dostupné formáty se podívejte na třídu Java. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``second(toTimestamp('2009-07-30 12:58:59')) -> 59``  
___
### <code>seconds</code>
<code><b>seconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Doba v milisekundách pro počet sekund  
* ``seconds(2) -> 2000L``  
___
### <code>sha1</code>
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Vypočítá hodnotu hash SHA-1 sady sloupců různých primitivních typů DataTypes a vrátí šestnáctkový řetězec 40 znaků. Dá se použít k výpočtu otisku prstu pro řádek.  
* ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '46d3b478e8ec4e1f3b453ac3d8e59d5854e282bb'``  
___
### <code>sha2</code>
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
Vypočítá hodnotu hash SHA-2 sady sloupců různých primitivních datových typů s délkou, která může být pouze z hodnot 0 (256), 224, 256, 384, 512. Dá se použít k výpočtu otisku prstu pro řádek.  
* ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'afe8a553b1761c67d76f8c31ceef7f71b66a1ee6f4e6d3b5478bf68b47d06bd3'``  
___
### <code>sin</code>
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá hodnotu sinus.  
* ``sin(2) -> 0.9092974268256817``  
___
### <code>sinh</code>
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá hodnotu hyperbolický sinus.  
* ``sinh(0) -> 0.0``  
___
### <code>soundex</code>
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Získá ```soundex``` kód pro řetězec.  
* ``soundex('genius') -> 'G520'``  
___
### <code>split</code>
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
Rozdělí řetězec na základě oddělovače a vrátí pole řetězců.  
* ``split('bojjus,guchus,dumbo', ',') -> ['bojjus', 'guchus', 'dumbo']``  
* ``split('bojjus,guchus,dumbo', '|') -> ['bojjus,guchus,dumbo']``  
* ``split('bojjus, guchus, dumbo', ', ') -> ['bojjus', 'guchus', 'dumbo']``  
* ``split('bojjus, guchus, dumbo', ', ')[1] -> 'bojjus'``  
* ``isNull(split('bojjus, guchus, dumbo', ', ')[0]) -> true``  
* ``isNull(split('bojjus, guchus, dumbo', ', ')[20]) -> true``  
* ``split('bojjusguchusdumbo', ',') -> ['bojjusguchusdumbo']``  
___
### <code>sqrt</code>
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá druhou odmocninu čísla.  
* ``sqrt(9) -> 3``  
___
### <code>startsWith</code>
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Kontroluje, zda řetězec začíná zadaným řetězcem.  
* ``startsWith('dumbo', 'du') -> true``  
___
### <code>subDays</code>
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Odečíst dny od data nebo časového razítka. Stejné jako operátor-operator pro datum.  
* ``subDays(toDate('2016-08-08'), 1) -> toDate('2016-08-07')``  
___
### <code>subMonths</code>
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Odečíst měsíce od data nebo časového razítka.  
* ``subMonths(toDate('2016-09-30'), 1) -> toDate('2016-08-31')``  
___
### <code>substring</code>
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
Extrahuje podřetězec určité délky z pozice. Pozice je na 1. Pokud je délka vynechána, je nastavena na konec řetězce.  
* ``substring('Cat in the hat', 5, 2) -> 'in'``  
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``  
* ``substring('Cat in the hat', 5) -> 'in the hat'``  
* ``substring('Cat in the hat', 100, 100) -> ''``  
___
### <code>tan</code>
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá tečnou hodnotu.  
* ``tan(0) -> 0.0``  
___
### <code>tanh</code>
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá hodnotu hyperbolický tangens.  
* ``tanh(0) -> 0.0``  
___
### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
Nahraďte jednu sadu znaků jinou sadou znaků v řetězci. Znaky mají 1 – 1 nahrazení.  
* ``translate('(bojjus)', '()', '[]') -> '[bojjus]'``  
* ``translate('(gunchus)', '()', '[') -> '[gunchus'``  
___
### <code>trim</code>
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Ořízne řetězec úvodních a koncových znaků. Pokud není zadán druhý parametr, ořízne prázdné znaky. Jinak ořízne libovolný znak určený ve druhém parametru.  
* ``trim('  dumbo  ') -> 'dumbo'``  
* ``trim('!--!du!mbo!', '-!') -> 'du!mbo'``  
___
### <code>true</code>
<code><b>true() => boolean</b></code><br/><br/>
Vždy vrátí hodnotu true. Funkci použijte, `syntax(true())` Pokud je sloupec s názvem "true".  
* ``(10 + 20 == 30) -> true``  
* ``(10 + 20 == 30) -> true()``  
___
### <code>typeMatch</code>
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
Odpovídá typu sloupce. Dá se použít jenom ve výrazech vzoru. číslo odpovídá krátkému, celému číslu, dlouhému, dvojitému, floatu nebo desetinnému číslu, které odpovídá krátkému, celému číslu, dlouhému a zlomku odpovídá typu datum nebo časové razítko.  
* ``typeMatch(type, 'number')``  
* ``typeMatch('date', 'datetime')``  
___
### <code>unescape</code>
<code><b>unescape(<i>&lt;string_to_escape&gt;</i> : string, <i>&lt;format&gt;</i> : string) => string</b></code><br/><br/>
Odřídí řetězec v závislosti na formátu. Hodnoty literálu pro přijatelný formát jsou "JSON", "XML", "ECMAScript", "HTML", "Java".
* ```unescape('{\\\\\"value\\\\\": 10}', 'json')```
* ```'{\\\"value\\\": 10}'```
___
### <code>upper</code>
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Velká písmena řetězce.  
* ``upper('bojjus') -> 'BOJJUS'``  
___
### <code>uuid</code>
<code><b>uuid() => string</b></code><br/><br/>
Vrátí generovaný UUID.  
* ``uuid()``  
___
### <code>weekOfYear</code>
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Získá týden v roce, který má dané datum.  
* ``weekOfYear(toDate('2008-02-20')) -> 8``  
___
### <code>weeks</code>
<code><b>weeks(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Doba trvání v milisekundách pro počet týdnů  
* ``weeks(2) -> 1209600000L``  
___
### <code>xor</code>
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logický operátor XOR Stejné jako u operátoru ^.  
* ``xor(true, false) -> true``  
* ``xor(true, true) -> false``  
* ``true ^ false -> true``  
___
### <code>year</code>
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Získá hodnotu roku pro datum.  
* ``year(toDate('2012-8-8')) -> 2012``  

## <a name="aggregate-functions"></a>Agregační funkce
Následující funkce jsou k dispozici pouze v transformacích agregace, Pivot, UNPIVOT a Window.
___
### <code>avg</code>
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Získá průměr hodnot sloupce.  
* ``avg(sales)``  
___
### <code>avgIf</code>
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Na základě kritérií získá průměrnou hodnotu sloupce.  
* ``avgIf(region == 'West', sales)``  
___
### <code>count</code>
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
Získá agregovaný počet hodnot. Pokud jsou zadány volitelné sloupce, ignorují se hodnoty NULL v počtu.  
* ``count(custId)``  
* ``count(custId, custName)``  
* ``count()``  
* ``count(iif(isNull(custId), 1, NULL))``  
___
### <code>countDistinct</code>
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
Získá agregovaný počet jedinečných hodnot sady sloupců.  
* ``countDistinct(custId, custName)``  
___
### <code>countIf</code>
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
Na základě kritérií získá agregovaný počet hodnot. Pokud je zadán volitelný sloupec, ignoruje hodnoty NULL v počtu.  
* ``countIf(state == 'CA' && commission < 10000, name)``  
___
### <code>covariancePopulation</code>
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Získá kovarianci populace mezi dvěma sloupci.  
* ``covariancePopulation(sales, profit)``  
___
### <code>covariancePopulationIf</code>
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií získá kovarianci populace dvou sloupců.  
* ``covariancePopulationIf(region == 'West', sales)``  
___
### <code>covarianceSample</code>
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Získá ukázkovou odchylku dvou sloupců.  
* ``covarianceSample(sales, profit)``  
___
### <code>covarianceSampleIf</code>
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií Získá vzorek kovariance dvou sloupců.  
* ``covarianceSampleIf(region == 'West', sales, profit)``  
___
### <code>first</code>
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Získá první hodnotu skupiny sloupců. Pokud je vynechán druhý parametr ignoreNulls, předpokládá se hodnota false.  
* ``first(sales)``  
* ``first(sales, false)``  
___
### <code>kurtosis</code>
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Získá špičatost sloupce.  
* ``kurtosis(sales)``  
___
### <code>kurtosisIf</code>
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií získá špičatost sloupce.  
* ``kurtosisIf(region == 'West', sales)``  
___
### <code>last</code>
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Načte poslední hodnotu skupiny sloupců. Pokud je vynechán druhý parametr ignoreNulls, předpokládá se hodnota false.  
* ``last(sales)``  
* ``last(sales, false)``  
___
### <code>max</code>
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Získá maximální hodnotu sloupce.  
* ``max(sales)``  
___
### <code>maxIf</code>
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Na základě kritérií získá maximální hodnotu sloupce.  
* ``maxIf(region == 'West', sales)``  
___
### <code>mean</code>
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Získá průměrnou hodnotu sloupce. Stejné jako prům.  
* ``mean(sales)``  
___
### <code>meanIf</code>
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Na základě kritérií získá průměrnou hodnotu sloupce. Stejné jako avgIf.  
* ``meanIf(region == 'West', sales)``  
___
### <code>min</code>
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Získá minimální hodnotu sloupce.  
* ``min(sales)``  
___
### <code>minIf</code>
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Na základě kritérií získá minimální hodnotu sloupce.  
* ``minIf(region == 'West', sales)``  
___
### <code>skewness</code>
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Získá zešikmení sloupce.  
* ``skewness(sales)``  
___
### <code>skewnessIf</code>
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií získá zešikmení sloupce.  
* ``skewnessIf(region == 'West', sales)``  
___
### <code>stddev</code>
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Získá směrodatnou odchylku sloupce.  
* ``stdDev(sales)``  
___
### <code>stddevIf</code>
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií získá směrodatnou odchylku sloupce.  
* ``stddevIf(region == 'West', sales)``  
___
### <code>stddevPopulation</code>
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Získá směrodatnou odchylku základního souboru sloupce.  
* ``stddevPopulation(sales)``  
___
### <code>stddevPopulationIf</code>
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií získá směrodatnou odchylku základního souboru sloupce.  
* ``stddevPopulationIf(region == 'West', sales)``  
___
### <code>stddevSample</code>
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Získá směrodatnou odchylku sloupce.  
* ``stddevSample(sales)``  
___
### <code>stddevSampleIf</code>
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií Získá vzorek směrodatnou odchylku sloupce.  
* ``stddevSampleIf(region == 'West', sales)``  
___
### <code>sum</code>
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Získá agregovaný součet číselného sloupce.  
* ``sum(col)``  
___
### <code>sumDistinct</code>
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Získá agregovaný součet jedinečných hodnot číselného sloupce.  
* ``sumDistinct(col)``  
___
### <code>sumDistinctIf</code>
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Na základě kritérií získá agregační součet číselného sloupce. Podmínka může být založena na jakémkoli sloupci.  
* ``sumDistinctIf(state == 'CA' && commission < 10000, sales)``  
* ``sumDistinctIf(true, sales)``  
___
### <code>sumIf</code>
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Na základě kritérií získá agregační součet číselného sloupce. Podmínka může být založena na jakémkoli sloupci.  
* ``sumIf(state == 'CA' && commission < 10000, sales)``  
* ``sumIf(true, sales)``  
___
### <code>variance</code>
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Získá odchylku sloupce.  
* ``variance(sales)``  
___
### <code>varianceIf</code>
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií získá odchylku sloupce.  
* ``varianceIf(region == 'West', sales)``  
___
### <code>variancePopulation</code>
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Získá odchylku populace sloupce.  
* ``variancePopulation(sales)``  
___
### <code>variancePopulationIf</code>
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií získá odchylku populace sloupce.  
* ``variancePopulationIf(region == 'West', sales)``  
___
### <code>varianceSample</code>
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Načte neposunutou odchylku sloupce.  
* ``varianceSample(sales)``  
___
### <code>varianceSampleIf</code>
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií získá neposunutou odchylku sloupce.  
* ``varianceSampleIf(region == 'West', sales)``  

## <a name="array-functions"></a>Funkce pro práci s poli
Funkce pole provádí transformace na datových strukturách, které jsou pole. Mezi ně patří speciální klíčová slova k adresování prvků a indexů pole:

* ```#acc``` představuje hodnotu, kterou chcete zahrnout do jednoho výstupu při omezení pole.
* ```#index``` představuje aktuální index pole spolu s čísly indexu pole. ```#index2, #index3 ...```
* ```#item``` představuje aktuální hodnotu prvku v poli.

### <code>array</code>
<code><b>array([<i>&lt;value1&gt;</i> : any], ...) => array</b></code><br/><br/>
Vytvoří pole položek. Všechny položky by měly být stejného typu. Nejsou-li zadány žádné položky, je výchozím polem pole prázdné řetězec. Stejné jako operátor vytvoření [].  
* ``array('Seattle', 'Washington')``
* ``['Seattle', 'Washington']``
* ``['Seattle', 'Washington'][1]``
* ``'Washington'``
___
### <code>filter</code>
<code><b>filter(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => array</b></code><br/><br/>
Filtruje prvky z pole, které nesplňuje zadaný predikát. Filtr očekává odkaz na jeden prvek ve funkci predikátu jako #item.  
* ``filter([1, 2, 3, 4], #item > 2) -> [3, 4]``  
* ``filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') -> ['a', 'b']``  
___
### <code>find</code>
<code><b>find(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => any</b></code><br/><br/>
Najde první položku z pole, které odpovídá podmínce. Převezme funkci filtru, kde můžete položku v poli adresovat jako #item. U hluboce vnořených map můžete odkazovat na nadřazené mapy pomocí notace #item_n (#item_1, #item_2...).  
* ``find([10, 20, 30], #item > 10) -> 20``
* ``find(['azure', 'data', 'factory'], length(#item) > 4) -> 'azure'``
* ``find([
      @(
         name = 'Daniel',
         types = [
                   @(mood = 'jovial', behavior = 'terrific'),
                   @(mood = 'grumpy', behavior = 'bad')
                 ]
        ),
      @(
         name = 'Mark',
         types = [
                   @(mood = 'happy', behavior = 'awesome'),
                   @(mood = 'calm', behavior = 'reclusive')
                 ]
        )
      ],
      contains(#item.types, #item.mood=='happy')  /*Filter out the happy kid*/
    )``
* ``
     @(
           name = 'Mark',
           types = [
                     @(mood = 'happy', behavior = 'awesome'),
                     @(mood = 'calm', behavior = 'reclusive')
                   ]
      )
    ``  
___
### <code>map</code>
<code><b>map(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => any</b></kód><br/><br/> mapuje každý prvek pole na nový prvek pomocí poskytnutého výrazu. Mapa očekává odkaz na jeden prvek ve výrazu Function as #item.  
* ``map([1, 2, 3, 4], #item + 2) -> [3, 4, 5, 6]``  
* ``map(['a', 'b', 'c', 'd'], #item + '_processed') -> ['a_processed', 'b_processed', 'c_processed', 'd_processed']``  
___
### <code>mapIndex</code>
<code><b>mapIndex(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => any</b></kód><br/><br/> mapuje každý prvek pole na nový prvek pomocí poskytnutého výrazu. Map očekává odkaz na jeden prvek ve funkci Expression jako #item a odkaz na element. index as #index.  
* ``mapIndex([1, 2, 3, 4], #item + 2 + #index) -> [4, 6, 8, 10]``  
___
### <code>reduce</code>
<code><b>reduce(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : any, <i>&lt;value3&gt;</i> : binaryfunction, <i>&lt;value4&gt;</i> : unaryfunction) => any</b></kód><br/><br/> shromažďuje prvky v poli. Omezení očekává odkaz na Akumulovaná a jeden element v prvním výrazu funkce jako #acc a #item a očekává výslednou hodnotu jako #result, která se má použít ve druhém výrazu.ession function.  
* ``toString(reduce(['1', '2', '3', '4'], '0', #acc + #item, #result)) -> '01234'``  
___
### <code>size</code>
<code><b>size(<i>&lt;value1&gt;</i> : any) => integer</b></kód><br/><br/> najde velikostrray or map type  
* ``size(['element1', 'element2']) -> 2``
* ``size([1,2,3]) -> 3``
___
### <code>slice</code>
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></kód><br/><br/> extrahuje podmnožinu pole z pozice. Pozice je na 1. Pokud je délka vynechána, je nastavena na hodnotu en.d of the string.  
* ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``  
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``  
* ``slice([10, 20, 30, 40], 2)[1] -> 20``  
* ``isNull(slice([10, 20, 30, 40], 2)[0]) -> true``  
* ``isNull(slice([10, 20, 30, 40], 2)[20]) -> true``  
* ``slice(['a', 'b', 'c', 'd'], 8) -> []``  
___
### <code>sort</code>
<code><b>sort(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => array</b></kód><br/><br/> seřadí pole pomocí zadané funkce predikátu. Řazení očekává odkaz na dva po sobě jdoucí elementy ve výrazu funkce Expression jako #item1 and #item2.  
* ``sort([4, 8, 2, 3], compare(#item1, #item2)) -> [2, 3, 4, 8]``  
* ``sort(['a3', 'b2', 'c1'], iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) -> ['c1', 'b2', 'a3']``* ``
 @(
       name = 'Mark',
       types = [
                 @(mood = 'happy', behavior = 'awesome'),
                 @(mood = 'calm', behavior = 'reclusive')
               ]
  )
``  
___
### <code>map</code>
<code><b>map(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => any</b></code><br/><br/>
Maps each element of the array to a new element using the provided expression. Map expects a reference to one element in the expression function as #item.  
* ``map([1, 2, 3, 4], #item + 2) -> [3, 4, 5, 6]``  
* ``map(['a', 'b', 'c', 'd'], #item + '_processed') -> ['a_processed', 'b_processed', 'c_processed', 'd_processed']``  
___
### <code>mapIndex</code>
<code><b>mapIndex(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => any</b></code><br/><br/>
Maps each element of the array to a new element using the provided expression. Map expects a reference to one element in the expression function as #item and a reference to the element index as #index.  
* ``mapIndex([1, 2, 3, 4], #item + 2 + #index) -> [4, 6, 8, 10]``  
___
### <code>reduce</code>
<code><b>reduce(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : any, <i>&lt;value3&gt;</i> : binaryfunction, <i>&lt;value4&gt;</i> : unaryfunction) => any</b></code><br/><br/>
Accumulates elements in an array. Reduce expects a reference to an accumulator and one element in the first expression function as #acc and #item and it expects the resulting value as #result to be used in the second expression function.  
* ``toString(reduce(['1', '2', '3', '4'], '0', #acc + #item, #result)) -> '01234'``  
___
### <code>size</code>
<code><b>size(<i>&lt;value1&gt;</i> : any) => integer</b></code><br/><br/>
Finds the size of an array or map type  
* ``size(['element1', 'element2']) -> 2``
* ``size([1,2,3]) -> 3``
___
### <code>slice</code>
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
Extracts a subset of an array from a position. Position is 1 based. If the length is omitted, it is defaulted to end of the string.  
* ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``  
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``  
* ``slice([10, 20, 30, 40], 2)[1] -> 20``  
* ``isNull(slice([10, 20, 30, 40], 2)[0]) -> true``  
* ``isNull(slice([10, 20, 30, 40], 2)[20]) -> true``  
* ``slice(['a', 'b', 'c', 'd'], 8) -> []``  
___
### <code>sort</code>
<code><b>sort(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => array</b></code><br/><br/>
Sorts the array using the provided predicate function. Sort expects a reference to two consecutive elements in the expression function as #item1 and #item2.  
* ``sort([4, 8, 2, 3], compare(#item1, #item2)) -> [2, 3, 4, 8]``  
* ``sort(['a3', 'b2', 'c1'], iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) -> ['c1', 'b2', 'a3']``  

## <a name="cached-lookup-functions"></a>Funkce vyhledávání v mezipaměti
Následující funkce jsou k dispozici, pouze pokud používáte vyhledávání v mezipaměti, pokud jste zahrnuli jímku uložených v mezipaměti.
___
### <code>lookup</code>
<code><b>lookup(key, key2, ...) => complex[]</b></code><br/><br/>
Vyhledá první řádek z jímky uložený v mezipaměti pomocí zadaných klíčů, které odpovídají klíčům z jímky uložené v mezipaměti.
* ``cacheSink#lookup(movieId)``  
___
### <code>mlookup</code>
<code><b>mlookup(key, key2, ...) => complex[]</b></code><br/><br/>
Vyhledá všechny odpovídající řádky z jímky uložené v mezipaměti pomocí zadaných klíčů, které odpovídají klíčům z jímky uložené v mezipaměti.
* ``cacheSink#mlookup(movieId)``  
___
### <code>output</code>
<code><b>output() => any</b></code><br/><br/>
Vrátí první řádek výsledků jímky mezipaměti. * ``cacheSink#output()``  
___
### <code>outputs</code>
<code><b>output() => any</b></code><br/><br/>
Vrátí celou sadu výstupních řádků výsledků jímky mezipaměti. * ``cacheSink#outputs()``
___


## <a name="conversion-functions"></a>Převodní funkce

Převodní funkce slouží k převodu dat a testování datových typů.

<code>isBoolean</code>
<code><b>isBoolean(<value1> : string) => boolean</b></code><br/><br/>
Kontroluje, zda je řetězcová hodnota logická hodnota podle pravidel ``toBoolean()``
* ``isBoolean('true') -> true``
* ``isBoolean('no') -> true``
* ``isBoolean('microsoft') -> false``

<code>isByte</code>
<code><b>isByte(<value1> : string) => boolean</b></code><br/><br/>
Kontroluje, zda je hodnota řetězce hodnota v bajtech, která odpovídá volitelnému formátu podle pravidel ``toByte()``
* ``isByte('123') -> true``
* ``isByte('chocolate') -> false``

<code>isDate</code>
<code><b>isDate (<value1> : string, [<format>: string]) => boolean</b></code><br/><br/>
Zkontroluje, zda je řetězec vstupního data kalendářní datum pomocí volitelného formátu vstupního data. Pro dostupné formáty se podívejte na SimpleDateFormat Java. Pokud je formát vstupního data vynechán, je výchozí formát ``yyyy-[M]M-[d]d`` . Přijaté formáty jsou ``[ yyyy, yyyy-[M]M, yyyy-[M]M-[d]d, yyyy-[M]M-[d]dT* ]``
* ``isDate('2012-8-18') -> true``
* ``isDate('12/18--234234' -> 'MM/dd/yyyy') -> false``

<code>isShort</code>
<code><b>isShort (<value1> : string, [<format>: string]) => boolean</b></code><br/><br/>
Kontroly řetězcové hodnoty jsou krátkou hodnotou podle pravidel ``toShort()``
* ``isShort('123') -> true``
* ``isShort('$123' -> '$###') -> true``
* ``isShort('microsoft') -> false``

<code>isInteger</code>
<code><b>isInteger (<value1> : string, [<format>: string]) => boolean</b></code><br/><br/>
Kontrola řetězcové hodnoty je celočíselná hodnota daného volitelného formátu podle pravidel ``toInteger()``
* ``isInteger('123') -> true``
* ``isInteger('$123' -> '$###') -> true``
* ``isInteger('microsoft') -> false``

<code>isLong</code>
<code><b>isLong (<value1> : string, [<format>: string]) => boolean</b></code><br/><br/>
Kontrola řetězcové hodnoty je dlouhodobá hodnota pro volitelný formát podle pravidel ``toLong()``
* ``isLong('123') -> true``
* ``isLong('$123' -> '$###') -> true``
* ``isLong('gunchus') -> false``

<code>isFloat</code>
<code><b>isFloat (<value1> : string, [<format>: string]) => boolean</b></code><br/><br/>
Kontrola řetězcové hodnoty je hodnota typu float, která přechází z volitelného formátu podle pravidel ``toFloat()``
* ``isFloat('123') -> true``
* ``isFloat('$123.45' -> '$###.00') -> true``
* ``isFloat('icecream') -> false``

<code>isDouble</code>
<code><b>isDouble (<value1> : string, [<format>: string]) => boolean</b></code><br/><br/>
Kontrola řetězcové hodnoty je dvojitá hodnota, která má v souladu s pravidly ``toDouble()``
* ``isDouble('123') -> true``
* ``isDouble('$123.45' -> '$###.00') -> true``
* ``isDouble('icecream') -> false``

<code>isDecimal</code>
<code><b>isDecimal (<value1> : string) => boolean</b></code><br/><br/>
Kontrola řetězcové hodnoty je desítková hodnota, která má formát podle pravidel ``toDecimal()``
* ``isDecimal('123.45') -> true``
* ``isDecimal('12/12/2000') -> false``

<code>isTimestamp</code>
<code><b>isTimestamp (<value1> : string, [<format>: string]) => boolean</b></code><br/><br/>
Kontroluje, zda je řetězec vstupního data časovou známkou pomocí volitelného vstupního formátu časového razítka. Dostupné formáty najdete v SimpleDateFormat Java. Pokud je časové razítko vynecháno, použije se výchozí vzor ``yyyy-[M]M-[d]d hh:mm:ss[.f...]`` . Můžete předat volitelné časové pásmo ve formátu "GMT", "PST", "UTC", "Amerika/Kajmanské". Časové razítko podporuje přesnost až milisekund s hodnotou 999. informace o dostupných formátech najdete v SimpleDateFormat Java.
* ``isTimestamp('2016-12-31 00:12:00') -> true``
* ``isTimestamp('2016-12-31T00:12:00' -> 'yyyy-MM-dd\\'T\\'HH:mm:ss' -> 'PST') -> true``
* ``isTimestamp('2012-8222.18') -> false``

### <code>toBase64</code>
<code><b>toBase64(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Zakóduje daný řetězec v base64.  
* ``toBase64('bojjus') -> 'Ym9qanVz'``  
___
### <code>toBinary</code>
<code><b>toBinary(<i>&lt;value1&gt;</i> : any) => binary</b></code><br/><br/>
Převede jakékoli číslo, datum/časové razítko nebo řetězec na binární reprezentaci.  
* ``toBinary(3) -> [0x11]``  
___
### <code>toBoolean</code>
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
Převede hodnotu (t ', ' true ', ' y ', ' yes ', ' 1 ') na true a (' f ', ' false ', ' n ', ' ne ', ' 0 ') na hodnotu false a hodnotu NULL pro jakoukoliv jinou hodnotu.  
* ``toBoolean('true') -> true``  
* ``toBoolean('n') -> false``  
* ``isNull(toBoolean('truthy')) -> true``  
___
### <code>toByte</code>
<code><b>toByte(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => byte</b></code><br/><br/>
Převede libovolný číselný řetězec nebo řetězec na hodnotu Byte. Pro převod lze použít volitelný formát desítkového čísla Java.  
* ``toByte(123)``
* ``123``
* ``toByte(0xFF)``
* ``-1``
* ``toByte('123')``
* ``123``
___
### <code>toDate</code>
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
Převede řetězec vstupního data na datum pomocí volitelného formátu data a času. `SimpleDateFormat`Pro dostupné formáty se podívejte na třídu Java. Pokud je formát vstupního data vynechán, výchozí formát je rrrr-[M] M-[d] d. Přijatelné formáty jsou: [rrrr, rrrr-[M] M, rrrr-[M] M-[d] d, rrrr-[M] M-[d] dT *].  
* ``toDate('2012-8-18') -> toDate('2012-08-18')``  
* ``toDate('12/18/2012', 'MM/dd/yyyy') -> toDate('2012-12-18')``  
___
### <code>toDecimal</code>
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;precision&gt;</i> : integral], [<i>&lt;scale&gt;</i> : integral], [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
Převede libovolný číselný řetězec nebo řetězec na desítkovou hodnotu. Pokud není Zadaná přesnost a škálování, je nastavená na (10, 2). Pro převod lze použít volitelný formát desítkového čísla Java. Volitelný formát národního prostředí ve formě jazyka BCP47, jako je en-US, de, zh-CN.  
* ``toDecimal(123.45) -> 123.45``  
* ``toDecimal('123.45', 8, 4) -> 123.4500``  
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``  
* ``toDecimal('Ç123,45', 10, 2, 'Ç###,##', 'de') -> 123.45``  
___
### <code>toDouble</code>
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => double</b></code><br/><br/>
Převede libovolný číselný řetězec nebo řetězec na hodnotu Double. Pro převod lze použít volitelný formát desítkového čísla Java. Volitelný formát národního prostředí ve formě jazyka BCP47, jako je en-US, de, zh-CN.  
* ``toDouble(123.45) -> 123.45``  
* ``toDouble('123.45') -> 123.45``  
* ``toDouble('$123.45', '$###.00') -> 123.45``  
* ``toDouble('Ç123,45', 'Ç###,##', 'de') -> 123.45``  
___
### <code>toFloat</code>
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => float</b></code><br/><br/>
Převede libovolný číselný řetězec nebo řetězec na hodnotu float. Pro převod lze použít volitelný formát desítkového čísla Java. Zkrátí všechna dvojitá přesnost.  
* ``toFloat(123.45) -> 123.45f``  
* ``toFloat('123.45') -> 123.45f``  
* ``toFloat('$123.45', '$###.00') -> 123.45f``  
___
### <code>toInteger</code>
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => integer</b></code><br/><br/>
Převede libovolný číselný řetězec nebo řetězec na celočíselnou hodnotu. Pro převod lze použít volitelný formát desítkového čísla Java. Zkrátí všechna dlouhá, float, Double.  
* ``toInteger(123) -> 123``  
* ``toInteger('123') -> 123``  
* ``toInteger('$123', '$###') -> 123``  
___
### <code>toLong</code>
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => long</b></code><br/><br/>
Převede libovolný číselný řetězec nebo řetězec na dlouhou hodnotu. Pro převod lze použít volitelný formát desítkového čísla Java. Zkrátí libovolný float, Double.  
* ``toLong(123) -> 123``  
* ``toLong('123') -> 123``  
* ``toLong('$123', '$###') -> 123``  
___
### <code>toShort</code>
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => short</b></code><br/><br/>
Převede libovolný číselný řetězec nebo řetězec na krátkou hodnotu. Pro převod lze použít volitelný formát desítkového čísla Java. Zkrátí celé číslo, Long, float, Double.  
* ``toShort(123) -> 123``  
* ``toShort('123') -> 123``  
* ``toShort('$123', '$###') -> 123``  
___
### <code>toString</code>
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
Převede primitivní datový typ na řetězec. Pro čísla a datum, kdy lze zadat formát. Je-li tento parametr zadán, je vybrána výchozí hodnota systému. Pro čísla se používá desítkový formát Java. Všechny možné formáty data najdete v Java SimpleDateFormat. výchozí formát je RRRR-MM-DD.  
* ``toString(10) -> '10'``  
* ``toString('engineer') -> 'engineer'``  
* ``toString(123456.789, '##,###.##') -> '123,456.79'``  
* ``toString(123.78, '000000.000') -> '000123.780'``  
* ``toString(12345, '##0.#####E0') -> '12.345E3'``  
* ``toString(toDate('2018-12-31')) -> '2018-12-31'``  
* ``isNull(toString(toDate('2018-12-31', 'MM/dd/yy'))) -> true``  
* ``toString(4 == 20) -> 'false'``  
___
### <code>toTimestamp</code>
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code><br/><br/>
Převede řetězec na časové razítko s ohledem na volitelný formát časového razítka. Pokud je časové razítko vynecháno, použije se výchozí vzor yyyy-[M] M-[d] d hh: mm: ss [. f...]. Můžete předat volitelné časové pásmo ve formátu "GMT", "PST", "UTC", "Amerika/Kajmanské". Časové razítko podporuje přesnost až milisekund s hodnotou 999. `SimpleDateFormat`Pro dostupné formáty se podívejte na třídu Java. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``toTimestamp('2016-12-31 00:12:00') -> toTimestamp('2016-12-31 00:12:00')``  
* ``toTimestamp('2016-12-31T00:12:00', 'yyyy-MM-dd\'T\'HH:mm:ss', 'PST') -> toTimestamp('2016-12-31 00:12:00')``  
* ``toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss') -> toTimestamp('2016-12-31 00:12:00')``  
* ``millisecond(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``  
___
### <code>toUTC</code>
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Převede časové razítko na UTC. Můžete předat volitelné časové pásmo ve formátu "GMT", "PST", "UTC", "Amerika/Kajmanské". Ve výchozím nastavení je nastavené na aktuální časové pásmo. `SimpleDateFormat`Pro dostupné formáty se podívejte na třídu Java. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html.  
* ``toUTC(currentTimestamp()) == toTimestamp('2050-12-12 19:18:12') -> false``  
* ``toUTC(currentTimestamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``  

## <a name="metafunctions"></a>Metafunctions

Metafunctions primárně funguje na metadatech v toku dat

### <code>byItem</code>
<code><b>byItem(<i>&lt;parent column&gt;</i> : any, <i>&lt;column name&gt;</i> : string) => any</b></code><br/><br/>
Najít podřízenou položku v rámci struktury nebo pole struktury, pokud existuje více shod, je vrácena první shoda. Pokud žádná shoda není, vrátí hodnotu NULL. Vrácená hodnota musí být typu převedený jednou z akcí převodu typu (? Datum? řetězec...).  Názvy sloupců, které jsou známy v době návrhu, by měly být řešeny stejným názvem. Počítané vstupy se nepodporují, ale můžete použít substituce parametrů. * ``byItem( byName('customer'), 'orderItems') ? (itemName as string, itemQty as integer)``
* ````
* ``byItem( byItem( byName('customer'), 'orderItems'), 'itemName') ? string``
* ````
___
### <code>byOrigin</code>
<code><b>byOrigin(<i>&lt;column name&gt;</i> : string, [<i>&lt;origin stream name&gt;</i> : string]) => any</b></code><br/><br/>
Vybere hodnotu sloupce podle názvu v datovém proudu zdroje. Druhý argument je název zdrojového streamu. Pokud existuje více shod, je vrácena první shoda. Pokud žádná shoda není, vrátí hodnotu NULL. Vrácená hodnota musí být typu převedený jednou z funkcí pro převod typu (TO_DATE, TO_STRING...). Názvy sloupců, které jsou známy v době návrhu, by měly být řešeny stejným názvem. Počítané vstupy se nepodporují, ale můžete použít substituce parametrů.  
* ``toString(byOrigin('ancestor', 'ancestorStream'))``
___
### <code>byOrigins</code>
<code><b>byOrigins(<i>&lt;column names&gt;</i> : array, [<i>&lt;origin stream name&gt;</i> : string]) => any</b></code><br/><br/>
Vybere pole sloupců podle názvu v datovém proudu. Druhý argument je datový proud, ze kterého pochází. Pokud existuje více shod, je vrácena první shoda. Pokud žádná shoda není, vrátí hodnotu NULL. Vrácená hodnota musí být typu převedený jednou z funkcí pro převod typu (TO_DATE, TO_STRING...). Názvy sloupců, které jsou známy v době návrhu, by měly být řešeny stejným názvem. Počítané vstupy se nepodporují, ale můžete použít substituce parametrů.
* ``toString(byOrigins(['ancestor1', 'ancestor2'], 'ancestorStream'))``
___
### <code>byName</code>
<code><b>byName(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
Vybere hodnotu sloupce podle názvu v datovém proudu. Jako druhý argument můžete předat volitelný název datového proudu. Pokud existuje více shod, je vrácena první shoda. Pokud žádná shoda není, vrátí hodnotu NULL. Vrácená hodnota musí být typu převedený jednou z funkcí pro převod typu (TO_DATE, TO_STRING...).  Názvy sloupců, které jsou známy v době návrhu, by měly být řešeny stejným názvem. Počítané vstupy se nepodporují, ale můžete použít substituce parametrů.  
* ``toString(byName('parent'))``  
* ``toLong(byName('income'))``  
* ``toBoolean(byName('foster'))``  
* ``toLong(byName($debtCol))``  
* ``toString(byName('Bogus Column'))``  
* ``toString(byName('Bogus Column', 'DeriveStream'))``  
___
### <code>byNames</code>
<code><b>byNames(<i>&lt;column names&gt;</i> : array, [<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
Vyberte pole sloupců podle názvu v datovém proudu. Jako druhý argument můžete předat volitelný název datového proudu. Pokud existuje více shod, je vrácena první shoda. Pokud pro sloupec neexistují žádné shody, celý výstup je hodnota NULL. Vrácená hodnota vyžaduje funkce pro převod typu (do: do, toString,...).  Názvy sloupců, které jsou známy v době návrhu, by měly být řešeny stejným názvem. Počítané vstupy se nepodporují, ale můžete použít substituce parametrů.
* ``toString(byNames(['parent', 'child']))``
* ``byNames(['parent']) ? string``
* ``toLong(byNames(['income']))``
* ``byNames(['income']) ? long``
* ``toBoolean(byNames(['foster']))``
* ``toLong(byNames($debtCols))``
* ``toString(byNames(['a Column']))``
* ``toString(byNames(['a Column'], 'DeriveStream'))``
* ``byNames(['orderItem']) ? (itemName as string, itemQty as integer)``
___
### <code>byPosition</code>
<code><b>byPosition(<i>&lt;position&gt;</i> : integer) => any</b></code><br/><br/>
Vybere hodnotu sloupce podle relativní pozice (na základě 1) v datovém proudu. Pokud je pozice mimo rozsah, vrátí hodnotu NULL. Vrácená hodnota musí být typu převedený jednou z funkcí pro převod typu (TO_DATE, TO_STRING...). Počítané vstupy se nepodporují, ale můžete použít substituce parametrů.  
* ``toString(byPosition(1))``  
* ``toDecimal(byPosition(2), 10, 2)``  
* ``toBoolean(byName(4))``  
* ``toString(byName($colName))``  
* ``toString(byPosition(1234))``  

## <a name="window-functions"></a>Funkce okna
Následující funkce jsou k dispozici pouze v transformacích okna.
___
### <code>cumeDist</code>
<code><b>cumeDist() => integer</b></code><br/><br/>
Funkce CumeDist vypočítá pozici hodnoty relativně ke všem hodnotám v oddílu. Výsledkem je počet řádků předcházejících nebo rovných aktuálnímu řádku v pořadí oddílu dělený celkovým počtem řádků v oddílu okna. Všechny hodnoty propojení v řazení se vyhodnotí na stejnou pozici.  
* ``cumeDist()``  
___
### <code>denseRank</code>
<code><b>denseRank() => integer</b></code><br/><br/>
Vypočítá pořadí hodnoty ve skupině hodnot zadaných v klauzuli ORDER by v okně. Výsledkem je jedna hodnota plus počet řádků předcházejících nebo rovných aktuálnímu řádku v pořadí oddílu. Hodnoty nebudou v sekvenci vydávat žádné mezery. Zhuštěné řazení funguje i v případě, že data nejsou seřazena a vyhledávají změny v hodnotách.  
* ``denseRank()``  
___
### <code>lag</code>
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Získá hodnotu prvního parametru vyhodnoceného n řádků před aktuálním řádkem. Druhým parametrem je počet řádků, které se mají vyhledat, a výchozí hodnota je 1. Pokud není k dispozici tolik řádků, je vrácena hodnota null, pokud není zadána výchozí hodnota.  
* ``lag(amount, 2)``  
* ``lag(amount, 2000, 100)``  
___
### <code>lead</code>
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Získá hodnotu prvního parametru vyhodnoceného n řádků za aktuálním řádkem. Druhým parametrem je počet řádků, které se mají hledat, a výchozí hodnota je 1. Pokud není k dispozici tolik řádků, je vrácena hodnota null, pokud není zadána výchozí hodnota.  
* ``lead(amount, 2)``  
* ``lead(amount, 2000, 100)``  
___
### <code>nTile</code>
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
```NTile```Funkce rozdělí řádky pro každý oddíl okna do `n` intervalů v rozsahu od 1 do nejvýše `n` . Hodnoty plechovky se budou lišit o nejvýše 1. Pokud počet řádků v oddílu není rovnoměrně rozdělen do počtu intervalů, pak jsou zbývající hodnoty distribuovány po jednotlivých kontejnerech počínaje prvním intervalem. ```NTile```Funkce je užitečná pro výpočet ```tertiles``` , Kvartily, deciles a další běžné souhrnné statistiky. Funkce vypočítá dvě proměnné během inicializace: velikost regulárního intervalu bude mít přidán jeden řádek navíc. Obě proměnné jsou založené na velikosti aktuálního oddílu. Během procesu výpočtu funkce udržuje sledovat aktuální číslo řádku, aktuální číslo sady a číslo řádku, na kterém se bude interval měnit (bucketThreshold). Když číslo aktuálního řádku dosáhne prahové hodnoty intervalu, hodnota intervalu se zvýší o jednu a prahová hodnota se zvýší o velikost intervalu (plus jeden další, pokud je aktuální kontejner doplněn).  
* ``nTile()``  
* ``nTile(numOfBuckets)``  
___
### <code>rank</code>
<code><b>rank() => integer</b></code><br/><br/>
Vypočítá pořadí hodnoty ve skupině hodnot zadaných v klauzuli ORDER by v okně. Výsledkem je jedna hodnota plus počet řádků předcházejících nebo rovných aktuálnímu řádku v pořadí oddílu. Hodnoty budou v posloupnosti vydávat mezery. Pořadí funguje i v případě, že data nejsou seřazena a vyhledávají změny v hodnotách.  
* ``rank()``  
___
### <code>rowNumber</code>
<code><b>rowNumber() => integer</b></code><br/><br/>
Přiřadí pořadové číslování řádků v okně od 1.  
* ``rowNumber()``  

## <a name="next-steps"></a>Další kroky

[Naučte se používat Tvůrce výrazů](concepts-data-flow-expression-builder.md).
