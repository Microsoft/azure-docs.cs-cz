---
title: Funkce výrazu v toku dat mapování
description: Další informace o funkcích výrazu v mapování toku dat.
author: kromerm
ms.author: makromer
manager: anandsub
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/15/2019
ms.openlocfilehash: ed5acf2e2d3ae09bcd629d9afbbb3b10d0bb3d44
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413706"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>Výrazy transformace dat v toku dat mapování

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="expression-functions"></a>Funkce výrazů

V Datové továrně použijte jazyk výrazu funkce toku dat mapování ke konfiguraci transformací dat.
___
### <code>abs</code>
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Absolutní hodnota čísla.
* ``abs(-20) -> 20``
* ``abs(10) -> 10``
___
### <code>acos</code>
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá nepřímou inverzní hodnotu.* ``acos(1) -> 0.0``
___
### <code>add</code>
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Přidá dvojici řetězců nebo čísel. Přidá datum na několik dní. Přidá dobu trvání časového razítka. Připojí jedno pole podobného typu k jinému. Stejné jako operátor +* ``add(10, 20) -> 30``
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
Přidejte dny k datu nebo časovému razítku. Stejné jako operátor + pro datum* ``addDays(toDate('2016-08-08'), 1) -> toDate('2016-08-09')``
___
### <code>addMonths</code>
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral, [<i>&lt;value3&gt;</i> : string]) => datetime</b></code><br/><br/>
Přidejte měsíce k datu nebo časovému razítku. Volitelně můžete předat časové pásmo* ``addMonths(toDate('2016-08-31'), 1) -> toDate('2016-09-30')``
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> toTimestamp('2016-08-31 10:10:10')``
___
### <code>and</code>
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logický operátor AND. Stejné jako && * ``and(true, false) -> false``
* ``true && false -> false``
___
### <code>asin</code>
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá inverzní sinusovou hodnotu.* ``asin(0) -> 0.0``
___
### <code>atan</code>
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá inverzní tečnou hodnotu.* ``atan(0) -> 0.0``
___
### <code>atan2</code>
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Vrátí úhel v radiánech mezi kladnou osou x roviny a bodem daným souřadnicemi.* ``atan2(0, 0) -> 0.0``
___
### <code>byName</code>
<code><b>byName(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => any</b></code><br/><br/>
Vybere hodnotu sloupce podle názvu v datovém proudu. Jako druhý argument můžete předat volitelný název datového proudu. Pokud existuje více shod, je vrácena první shoda. Pokud žádná shoda vrátí hodnotu NULL. Vrácená hodnota musí být typu převedena jedním z funkce převodu typu (TO_DATE, TO_STRING ...).  Názvy sloupců známé v době návrhu by měly být adresovány pouze jejich jménem. Vypočítané vstupy nejsou podporovány, ale můžete použít náhrady parametrů* ``toString(byName('parent'))``
* ``toLong(byName('income'))``
* ``toBoolean(byName('foster'))``
* ``toLong(byName($debtCol))``
* ``toString(byName('Bogus Column'))``
* ``toString(byName('Bogus Column', 'DeriveStream'))``
___
### <code>byPosition</code>
<code><b>byPosition(<i>&lt;position&gt;</i> : integer) => any</b></code><br/><br/>
Vybere hodnotu sloupce podle jeho relativní pozice(1 na základě) v datovém proudu. Pokud pozice je mimo hranice vrátí hodnotu NULL. Vrácená hodnota musí být typu převedena jedním z funkcí převodu typu (TO_DATE, TO_STRING ...) Vypočítané vstupy nejsou podporovány, ale můžete použít náhrady parametrů* ``toString(byPosition(1))``
* ``toDecimal(byPosition(2), 10, 2)``
* ``toBoolean(byName(4))``
* ``toString(byName($colName))``
* ``toString(byPosition(1234))``
___
### <code>case</code>
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
Na základě střídavých podmínek platí jedna nebo druhá hodnota. Pokud počet vstupů jsou sudé, druhý je výchozí hodnotu NULL pro poslední podmínku* ``case(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``
* ``case(10 + 20 == 25, 'bojjus', 'do' < 'go', 'gunchus') -> 'gunchus'``
* ``isNull(case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus')) -> true``
* ``case(10 + 20 == 25, 'bojjus', 'do' > 'go', 'gunchus', 'dumbo') -> 'dumbo'``
___
### <code>cbrt</code>
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Výpočet kořenové krychle čísla* ``cbrt(8) -> 2.0``
___
### <code>ceil</code>
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Vrátí nejmenší celé číslo, které není menší než číslo.* ``ceil(-0.1) -> 0``
___
### <code>coalesce</code>
<code><b>coalesce(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Vrátí první ne nulovou hodnotu ze sady vstupů. Všechny vstupy by měly být stejného typu.* ``coalesce(10, 20) -> 10``
* ``coalesce(toString(null), toString(null), 'dumbo', 'bo', 'go') -> 'dumbo'``
___
### <code>columnNames</code>
<code><b>columnNames(<i>&lt;value1&gt;</i> : string) => array</b></code><br/><br/>
Získá všechny výstupní sloupce pro datový proud. Jako druhý argument můžete předat volitelný název datového proudu.
* ``columnNames()``
* ``columnNames('DeriveStream')``
___
### <code>compare</code>
<code><b>compare(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => integer</b></code><br/><br/>
Porovná dvě hodnoty stejného typu. Vrátí záporné celé číslo, pokud hodnota1 < hodnota2, 0 pokud hodnota1 == hodnota2, kladná hodnota, pokud hodnota1 > hodnota2* ``(compare(12, 24) < 1) -> true``
* ``(compare('dumbo', 'dum') > 0) -> true``
___
### <code>concat</code>
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Zřetězí proměnný počet řetězců dohromady. Stejné jako operátor + s řetězci* ``concat('dataflow', 'is', 'awesome') -> 'dataflowisawesome'``
* ``'dataflow' + 'is' + 'awesome' -> 'dataflowisawesome'``
* ``isNull('sql' + null) -> true``
___
### <code>concatWS</code>
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Zřetězí proměnný počet řetězců spolu s oddělovačem. Prvním parametrem je oddělovač* ``concatWS(' ', 'dataflow', 'is', 'awesome') -> 'dataflow is awesome'``
* ``isNull(concatWS(null, 'dataflow', 'is', 'awesome')) -> true``
* ``concatWS(' is ', 'dataflow', 'awesome') -> 'dataflow is awesome'``
___
### <code>contains</code>
<code><b>contains(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => boolean</b></code><br/><br/>
Vrátí hodnotu true, pokud se některý prvek v poskytnutém poli vyhodnotí jako pravdivý v poskytnutém predikátu. Obsahuje očekává odkaz na jeden prvek v predikátu funkce jako #item* ``contains([1, 2, 3, 4], #item == 3) -> true``
* ``contains([1, 2, 3, 4], #item > 5) -> false``
___
### <code>cos</code>
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá hodnotu kosinus.* ``cos(10) -> -0.8390715290764524``
___
### <code>cosh</code>
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá hyperbolický kosinus hodnoty.* ``cosh(0) -> 1.0``
___
### <code>crc32</code>
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
Vypočítá hodnotu hash CRC32 sady sloupců s různými primitivními datovými typy dané bitovou délkou, která může mít pouze hodnoty 0(256), 224, 256, 384, 512. Může být použit k výpočtu otisku prstu pro řádek* ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689L``
___
### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
Získá aktuální datum při spuštění této úlohy. Můžete předat volitelné časové pásmo ve formě "GMT", "PST", "UTC", "Amerika/Kajman". Jako výchozí se používá místní časové pásmo. Dostupné formáty naleznete v jazyce SimpleDateFormat v jazyce Java. ["https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html)
* ``currentDate() == toDate('2250-12-31') -> false``
* ``currentDate('PST')  == toDate('2250-12-31') -> false``
* ``currentDate('America/New_York')  == toDate('2250-12-31') -> false``
___
### <code>currentTimestamp</code>
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
Získá aktuální časové razítko při spuštění úlohy s místním časovým pásmem* ``currentTimestamp() == toTimestamp('2250-12-31 12:12:12') -> false``
___
### <code>currentUTC</code>
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
Získá aktuální časové razítko jako UTC. Pokud chcete, aby byl aktuální čas interpretován v jiném časovém pásmu než časové pásmo clusteru, můžete předat volitelné časové pásmo ve formě "GMT", "PST", "UTC", "Amerika/Kajman". Je výchozí aktuální časové pásmo. Dostupné formáty naleznete v jazyce SimpleDateFormat v jazyce Java. [https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html](https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html). Převod času UTC na jiné použití časového pásma fromUTC()* ``currentUTC() == toTimestamp('2050-12-12 19:18:12') -> false``
* ``currentUTC() != toTimestamp('2050-12-12 19:18:12') -> true``
* ``fromUTC(currentUTC(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>dayOfMonth</code>
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Získá den v měsíci dané datum* ``dayOfMonth(toDate('2018-06-08')) -> 8``
___
### <code>dayOfWeek</code>
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Získá den v týdnu dané datum. 1 - Neděle, 2 - Pondělí ..., 7 - Sobota* ``dayOfWeek(toDate('2018-06-08')) -> 6``
___
### <code>dayOfYear</code>
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Získá den v roce dané datum* ``dayOfYear(toDate('2016-04-09')) -> 100``
___
### <code>days</code>
<code><b>days(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Doba trvání v milisekundách pro počet dní* ``days(2) -> 172800000L``
___
### <code>degrees</code>
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Převede radiáty na stupně.* ``degrees(3.141592653589793) -> 180``
___
### <code>divide</code>
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Rozdělí dvojici čísel. Stejné jako operátor /* ``divide(20, 10) -> 2``
* ``20 / 10 -> 2``
___
### <code>endsWith</code>
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Zkontroluje, zda řetězec končí dodaným řetězcem.* ``endsWith('dumbo', 'mbo') -> true``
___
### <code>equals</code>
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Porovnání se rovná operátor. Stejné jako == operátor* ``equals(12, 24) -> false``
* ``12 == 24 -> false``
* ``'bad' == 'bad' -> true``
* ``isNull('good' == toString(null)) -> true``
* ``isNull(null == null) -> true``
___
### <code>equalsIgnoreCase</code>
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
Porovnání se rovná operátor ignoruje případ. Stejné jako <= operátor>* ``'abc'<=>'Abc' -> true``
* ``equalsIgnoreCase('abc', 'Abc') -> true``
___
### <code>factorial</code>
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
Výpočet faktoriálu čísla* ``factorial(5) -> 120``
___
### <code>false</code>
<code><b>false() => boolean</b></code><br/><br/>
Vždy vrátí falešnou hodnotu. Syntaxe funkce(false()) použijte, pokud existuje sloupec s názvem false.* ``(10 + 20 > 30) -> false``
* ``(10 + 20 > 30) -> false()``
___
### <code>filter</code>
<code><b>filter(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => array</b></code><br/><br/>
Filtruje prvky z pole, které nesplňují zadaný predikát. Filtr očekává odkaz na jeden prvek v predikátu funkce jako #item* ``filter([1, 2, 3, 4], #item > 2) -> [3, 4]``
* ``filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') -> ['a', 'b']``
___
### <code>floor</code>
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Vrátí největší celé číslo, které není větší než číslo.* ``floor(-0.1) -> -1``
___
### <code>fromBase64</code>
<code><b>fromBase64(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Zakóduje daný řetězec v base64* ``fromBase64('Z3VuY2h1cw==') -> 'gunchus'``
___
### <code>fromUTC</code>
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Převede na časové razítko z Času UTC. Volitelně můžete předat časové pásmo ve formě 'GMT', 'PST', 'UTC', 'Amerika/Kajman'. Je výchozí aktuální časové pásmoOdkazovat java simpledateformat pro dostupné formáty. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``fromUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``
* ``fromUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Porovnání větší operátor. Stejné jako > operátor* ``greater(12, 24) -> false``
* ``('dumbo' > 'dum') -> true``
* ``(toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS') > toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``
___
### <code>greaterOrEqual</code>
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Porovnání větší než nebo rovno operátor. Stejné jako >= operátor* ``greaterOrEqual(12, 12) -> true``
* ``('dumbo' >= 'dum') -> true``
___
### <code>greatest</code>
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Vrátí největší hodnotu ze seznamu hodnot jako vstupní přeskočení nulových hodnot. Vrátí hodnotu null, pokud jsou všechny vstupy null.* ``greatest(10, 30, 15, 20) -> 30``
* ``greatest(10, toInteger(null), 20) -> 20``
* ``greatest(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2011-12-12')``
* ``greatest(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS'), toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')``
___
### <code>hasColumn</code>
<code><b>hasColumn(<i>&lt;column name&gt;</i> : string, [<i>&lt;stream name&gt;</i> : string]) => boolean</b></code><br/><br/>
Zkontroluje hodnotu sloupce podle názvu v datovém proudu. Jako druhý argument můžete předat volitelný název datového proudu.  Názvy sloupců známé v době návrhu by měly být adresovány pouze jejich jménem. Vypočítané vstupy nejsou podporovány, ale můžete použít náhrady parametrů* ``hasColumn('parent')``
___
### <code>hour</code>
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Získá hodnotu hodiny časové razítko. Můžete předat volitelné časové pásmo ve formě "GMT", "PST", "UTC", "Amerika/Kajman". Jako výchozí se používá místní časové pásmo. Dostupné formáty naleznete v jazyce SimpleDateFormat v jazyce Java. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``hour(toTimestamp('2009-07-30 12:58:59')) -> 12``
* ``hour(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 12``
___
### <code>hours</code>
<code><b>hours(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Doba trvání v milisekundách pro počet hodin* ``hours(2) -> 7200000L``
___
### <code>iif</code>
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
Na základě podmínky platí jednu nebo druhou hodnotu. Pokud ostatní je nespecifikovaný je považován za NULL. Obě hodnoty musí být kompatibilní (číselné, řetězec...)* ``iif(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'``
* ``iif(10 > 30, 'dumbo', 'gumbo') -> 'gumbo'``
* ``iif(month(toDate('2018-12-01')) == 12, 345.12, 102.67) -> 345.12``
___
### <code>iifNull</code>
<code><b>iifNull(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => any</b></code><br/><br/>
Zkontroluje, zda hodnota není null a vrátí jiostatní vrátí alternativu. Testuje všechny vstupy, dokud nenajde první nenulovou hodnotu.* ``iifNull(10, 20) -> 10``
* ``iifNull(null, 20, 40) -> 20``
* ``iifNull('bojjus', 'bo', 'dumbo') -> 'dumbo'``
___
### <code>in</code>
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
Zkontroluje, zda je položka v poli.* ``in([10, 20, 30], 10) -> true``
* ``in(['good', 'kid'], 'bad') -> false``
___
### <code>initCap</code>
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Převede první písmeno každého slova na velká písmena. Slova jsou označena jako oddělená mezerami* ``initCap('cool iceCREAM') -> 'Cool Icecream'``
___
### <code>instr</code>
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
Najde pozici(1 na základě) podřetězce v řetězci. 0 je vrácena, pokud nebyl nalezen* ``instr('dumbo', 'mbo') -> 3``
* ``instr('microsoft', 'o') -> 5``
* ``instr('good', 'bad') -> 0``
___
### <code>isDelete</code>
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Zkontroluje, zda je řádek označen pro odstranění. Pro transformace s více než jeden vstupní datový proud můžete předat (1-based) index datového proudu. Index datového proudu by měl být 1 nebo 2 a výchozí hodnota je 1* ``isDelete()``
* ``isDelete(1)``
___
### <code>isError</code>
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Zkontroluje, zda je řádek označen jako chyba. Pro transformace s více než jeden vstupní datový proud můžete předat (1-based) index datového proudu. Index datového proudu by měl být 1 nebo 2 a výchozí hodnota je 1* ``isError()``
* ``isError(1)``
___
### <code>isIgnore</code>
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Zkontroluje, zda je řádek označen jako ignorován. Pro transformace s více než jeden vstupní datový proud můžete předat (1-based) index datového proudu. Index datového proudu by měl být 1 nebo 2 a výchozí hodnota je 1* ``isIgnore()``
* ``isIgnore(1)``
___
### <code>isInsert</code>
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Zkontroluje, zda je řádek označen pro vložení. Pro transformace s více než jeden vstupní datový proud můžete předat (1-based) index datového proudu. Index datového proudu by měl být 1 nebo 2 a výchozí hodnota je 1* ``isInsert()``
* ``isInsert(1)``
___
### <code>isMatch</code>
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Zkontroluje, zda je řádek spárován při vyhledávání. Pro transformace s více než jeden vstupní datový proud můžete předat (1-based) index datového proudu. Index datového proudu by měl být 1 nebo 2 a výchozí hodnota je 1* ``isMatch()``
* ``isMatch(1)``
___
### <code>isNull</code>
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Zkontroluje, zda je hodnota NULL.* ``isNull(NULL()) -> true``
* ``isNull('') -> false``
___
### <code>isUpdate</code>
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Zkontroluje, zda je řádek označen pro aktualizaci. Pro transformace s více než jeden vstupní datový proud můžete předat (1-based) index datového proudu. Index datového proudu by měl být 1 nebo 2 a výchozí hodnota je 1* ``isUpdate()``
* ``isUpdate(1)``
___
### <code>isUpsert</code>
<code><b>isUpsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Zkontroluje, zda je řádek označen pro vložení. Pro transformace s více než jeden vstupní datový proud můžete předat (1-based) index datového proudu. Index datového proudu by měl být 1 nebo 2 a výchozí hodnota je 1* ``isUpsert()``
* ``isUpsert(1)``
___
### <code>lastDayOfMonth</code>
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
Získá poslední datum v měsíci dané datum* ``lastDayOfMonth(toDate('2009-01-12')) -> toDate('2009-01-31')``
___
### <code>least</code>
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Porovnání menší než nebo rovno operátor. Stejné jako <= operátor* ``least(10, 30, 15, 20) -> 10``
* ``least(toDate('2010-12-12'), toDate('2011-12-12'), toDate('2000-12-12')) -> toDate('2000-12-12')``
___
### <code>left</code>
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Extrahuje podřetězec začíná na indexu 1 s počtem znaků. Stejné jako SUBSTRING(str, 1, n)* ``left('bojjus', 2) -> 'bo'``
* ``left('bojjus', 20) -> 'bojjus'``
___
### <code>length</code>
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
Vrátí délku řetězce.* ``length('dumbo') -> 5``
___
### <code>lesser</code>
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Porovnání méně operátor. Stejné jako < operátor* ``lesser(12, 24) -> true``
* ``('abcd' < 'abc') -> false``
* ``(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') < toTimestamp('2019-02-05 08:21:34.890', 'yyyy-MM-dd HH:mm:ss.SSS')) -> true``
___
### <code>lesserOrEqual</code>
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Porovnání menší než nebo rovno operátor. Stejné jako <= operátor* ``lesserOrEqual(12, 12) -> true``
* ``('dumbo' <= 'dum') -> false``
___
### <code>levenshtein</code>
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
Získá levenshtein vzdálenost mezi dvěma řetězci* ``levenshtein('boys', 'girls') -> 4``
___
### <code>like</code>
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Vzor je řetězec, který je uzavřeno doslova. Výjimky jsou následující speciální symboly: _ odpovídá libovolnému znaku ve vstupu (podobně jako . v regulárních výrazech posix) % odpovídá nule nebo více znakůve vstupu (podobně jako .* v regulárních výrazech posix).
Znak útěku je ''. Pokud řídicí znak předchází speciální symbol nebo jiný znak escape, následující znak je uzavřeno doslova. Je neplatný uniknout jakýkoli jiný znak.
* ``like('icecream', 'ice%') -> true``
___
### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
Najde pozici(1 na základě) podřetězce v řetězci začínající určité pozici. Pokud je pozice vynechána, je považována za od začátku řetězce. 0 je vrácena, pokud nebyl nalezen* ``locate('mbo', 'dumbo') -> 3``
* ``locate('o', 'microsoft', 6) -> 7``
* ``locate('bad', 'good') -> 0``
___
### <code>log</code>
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
Vypočítá hodnotu protokolu. Volitelnou základnu lze dodat jinak euler číslo, pokud je použito* ``log(100, 10) -> 2``
___
### <code>log10</code>
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá hodnotu protokolu na základě 10 základů.* ``log10(100) -> 2``
___
### <code>lower</code>
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Malá písmena řetězec* ``lower('GunChus') -> 'gunchus'``
___
### <code>lpad</code>
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Levý mačká řetězec podle dodaného polstrování, dokud není určité délky. Pokud je řetězec roven nebo větší než délka, pak je * ``lpad('dumbo', 10, '-') -> '-----dumbo'`` 
* ``lpad('dumbo', 4, '-') -> 'dumb'`` 
* oříznut na délku ''lpad('dumbo', 8, '<>') -> '<><dumbo'``
___
### <code>ltrim</code>
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Vlevo ořízne řetězec úvodních znaků. Pokud druhý parametr není zadán, ořízne prázdné znaky. Jinak ořízne libovolný znak zadaný v druhém parametru.* ``ltrim('  dumbo  ') -> 'dumbo  '``
* ``ltrim('!--!du!mbo!', '-!') -> 'du!mbo!'``
___
### <code>map</code>
<code><b>map(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : unaryfunction) => any</b></code><br/><br/>
Mapuje každý prvek pole na nový prvek pomocí zadaný výraz. Map očekává odkaz na jeden prvek ve funkci výrazu jako #item* ``map([1, 2, 3, 4], #item + 2) -> [3, 4, 5, 6]``
* ``map(['a', 'b', 'c', 'd'], #item + '_processed') -> ['a_processed', 'b_processed', 'c_processed', 'd_processed']``
___
### <code>mapIndex</code>
<code><b>mapIndex(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => any</b></code><br/><br/>
Mapuje každý prvek pole na nový prvek pomocí zadaný výraz. Map očekává odkaz na jeden prvek ve funkci výrazu jako #item a odkaz na index prvku jako #index* ``mapIndex([1, 2, 3, 4], #item + 2 + #index) -> [4, 6, 8, 10]``
___
### <code>md5</code>
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Vypočítá MD5 digest sadu sloupec různých primitivních datových typů a vrátí 32 znaků šestnáctkový řetězec. Může být použit k výpočtu otisku prstu pro řádek* ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '4ce8a880bd621a1ffad0bca905e1bc5a'``
___
### <code>millisecond</code>
<code><b>millisecond(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Získá milisekundovou hodnotu data. Můžete předat volitelné časové pásmo ve formě "GMT", "PST", "UTC", "Amerika/Kajman". Jako výchozí se používá místní časové pásmo. Dostupné formáty naleznete v jazyce SimpleDateFormat v jazyce Java. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``
___
### <code>milliseconds</code>
<code><b>milliseconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Doba trvání v milisekundách pro počet milisekund* ``milliseconds(2) -> 2L``
___
### <code>minus</code>
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Odečte čísla. Odečíst od data počtu dní. Odečíst dobu trvání od časového razítka. Odečtěte dvě časová razítka, abyste získali rozdíl v milisekundách. Stejné jako operátor -* ``minus(20, 10) -> 10``
* ``20 - 10 -> 10``
* ``minus(toDate('2012-12-15'), 3) -> toDate('2012-12-12')``
* ``toDate('2012-12-15') - 3 -> toDate('2012-12-12')``
* ``toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS') + (days(1) + hours(2) - seconds(10)) -> toTimestamp('2019-02-04 07:19:18.871', 'yyyy-MM-dd HH:mm:ss.SSS')``
* ``toTimestamp('2019-02-03 05:21:34.851', 'yyyy-MM-dd HH:mm:ss.SSS') - toTimestamp('2019-02-03 05:21:36.923', 'yyyy-MM-dd HH:mm:ss.SSS') -> -2072``
___
### <code>minute</code>
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Získá minutu hodnotu časového razítka. Můžete předat volitelné časové pásmo ve formě "GMT", "PST", "UTC", "Amerika/Kajman". Jako výchozí se používá místní časové pásmo. Dostupné formáty naleznete v jazyce SimpleDateFormat v jazyce Java. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``minute(toTimestamp('2009-07-30 12:58:59')) -> 58``
* ``minute(toTimestamp('2009-07-30 12:58:59'), 'PST') -> 58``
___
### <code>minutes</code>
<code><b>minutes(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Doba trvání v milisekundách po dobu několika minut* ``minutes(2) -> 120000L``
___
### <code>mod</code>
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Modul dvojice čísel. Stejné jako operátor %* ``mod(20, 8) -> 4``
* ``20 % 8 -> 4``
___
### <code>month</code>
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Získá hodnotu měsíce data nebo časového razítka.* ``month(toDate('2012-8-8')) -> 8``
___
### <code>monthsBetween</code>
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;roundoff&gt;</i> : boolean], [<i>&lt;time zone&gt;</i> : string]) => double</b></code><br/><br/>
Získá počet měsíců mezi dvěma daty. Výpočet můžete zaokrouhlit. Můžete předat volitelné časové pásmo ve formě "GMT", "PST", "UTC", "Amerika/Kajman". Jako výchozí se používá místní časové pásmo. Dostupné formáty naleznete v jazyce SimpleDateFormat v jazyce Java. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``monthsBetween(toTimestamp('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``
___
### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Vynásobí dvojici čísel. Stejné jako operátor ** ``multiply(20, 10) -> 200``
* ``20 * 10 -> 200``
___
### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Neguje číslo. Změní kladná čísla na záporná a naopak.* ``negate(13) -> -13``
___
### <code>nextSequence</code>
<code><b>nextSequence() => long</b></code><br/><br/>
Vrátí další jedinečnou sekvenci. Číslo je po sobě jdoucí pouze v rámci oddílu a je předponou partitionId* ``nextSequence() == 12313112 -> false``
___
### <code>normalize</code>
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></code><br/><br/>
Normalizovat hodnotu řetězce na samostatné znaky unicode s diakritikou* ``regexReplace(normalize('bo²s'), `\p{M}`, '') -> 'boys'``
___
### <code>not</code>
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logický operátor negace* ``not(true) -> false``
* ``not(10 == 20) -> true``
___
### <code>notEquals</code>
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Porovnání se nerovná operátor. Stejné jako != operátor* ``12 != 24 -> true``
* ``'bojjus' != 'bo' + 'jjus' -> false``
___
### <code>notNull</code>
<code><b>notNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Zkontroluje, zda hodnota není NULL.* ``notNull(NULL()) -> false``
* ``notNull('') -> true``
___
### <code>null</code>
<code><b>null() => null</b></code><br/><br/>
Vrátí hodnotu NULL. Syntaxe funkce(null()) použijte, pokud existuje sloupec s názvem null. Každá operace, která používá, bude mít za následek hodnotu NULL* ``isNull('dumbo' + null) -> true``
* ``isNull(10 * null) -> true``
* ``isNull('') -> false``
* ``isNull(10 + 20) -> false``
* ``isNull(10/0) -> true``
___
### <code>or</code>
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logický operátor OR. Stejné jako ||* ``or(true, false) -> true``
* ``true || false -> true``
___
### <code>pMod</code>
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Pozitivní modul dvojice čísel.
* ``pmod(-20, 8) -> 4``
___
### <code>partitionId</code>
<code><b>partitionId() => integer</b></code><br/><br/>
Vrátí aktuální id oddílu, ve kterých je vstupní řádek.* ``partitionId()``
___
### <code>power</code>
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Zvýší jedno číslo na sílu jiného* ``power(10, 2) -> 100``
___
### <code>reduce</code>
<code><b>reduce(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : any, <i>&lt;value3&gt;</i> : binaryfunction, <i>&lt;value4&gt;</i> : unaryfunction) => any</b></code><br/><br/>
Akumuluje prvky v poli. Reduce očekává odkaz na akumulátor a jeden prvek v první funkci výrazu jako #acc a #item a očekává výslednou hodnotu jako #result, která má být použita ve funkci druhého výrazu* ``toString(reduce(['1', '2', '3', '4'], '0', #acc + #item, #result)) -> '01234'``
___
### <code>regexExtract</code>
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
Extrahovat odpovídající podřetězec pro daný vzorek regulárnívýraz. Poslední parametr identifikuje skupinu shody a je výchozí hodnota 1, pokud je vynechána. Použití<regex>' '(zpět uvozovky) tak, aby odpovídaly řetězec bez úniku* ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``
___
### <code>regexMatch</code>
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
Zkontroluje, zda řetězec odpovídá danému vzoru regulárního výrazu. Použití<regex>' '(zpět uvozovky) tak, aby odpovídaly řetězec bez úniku* ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``
___
### <code>regexReplace</code>
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Nahradit všechny výskyty vzoru regulárního výrazu jiným<regex>podřetězcem v daném řetězci Použijte ' '(zpět uvozovky) tak, aby odpovídaly řetězci bez úniku* ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``
___
### <code>regexSplit</code>
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
Rozdělí řetězec na základě oddělovače na základě regulárního výrazu a vrátí pole řetězců.* ``regexSplit('bojjusAgunchusBdumbo', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo']``
* ``regexSplit('bojjusAgunchusBdumboC', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo', '']``
* ``(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[1]) -> 'bojjus'``
* ``isNull(regexSplit('bojjusAgunchusBdumboC', `[CAB]`)[20]) -> true``
___
### <code>replace</code>
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, [<i>&lt;substring to replace&gt;</i> : string]) => string</b></code><br/><br/>
Nahraďte všechny výskyty podřetězce jiným podřetězcem v daném řetězci. Pokud je poslední parametr vynechán, je ve výchozím nastavení prázdný řetězec* ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``
* ``replace('doggie dog', 'dog', '') -> 'gie '``
* ``replace('doggie dog', 'dog') -> 'gie '``
___
### <code>reverse</code>
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Obrátí řetězec.* ``reverse('gunchus') -> 'suhcnug'``
___
### <code>right</code>
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Extrahuje podřetězec s počtem znaků zprava. Stejné jako SUBSTRING(str, LENGTH(str) - n, n)* ``right('bojjus', 2) -> 'us'``
* ``right('bojjus', 20) -> 'bojjus'``
___
### <code>rlike</code>
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Zkontroluje, zda řetězec odpovídá danému vzoru regulárního výrazu.* ``rlike('200.50', `(\d+).(\d+)`) -> true``
* ``rlike('bogus', `M[0-9]+.*`) -> false``
___
### <code>round</code>
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
Zaokrouhlí číslo s volitelným měřítkem a volitelným režimem zaokrouhlení. Pokud je měřítko vynecháno, je výchozí hodnota 0.  Pokud je režim vynechán, je výchozí ROUND_HALF_UP(5). Hodnoty pro zaokrouhlení zahrnují 1 - ROUND_UP 2 - ROUND_DOWN 3 - ROUND_CEILING 4 - ROUND_FLOOR 5 - ROUND_HALF_UP 6 - ROUND_HALF_DOWN 7 - ROUND_HALF_EVEN 8 - ROUND_UNNECESSARY* ``round(100.123) -> 100.0``
* ``round(2.5, 0) -> 3.0``
* ``round(5.3999999999999995, 2, 7) -> 5.40``
___
### <code>rpad</code>
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Pravá podložka řetězec dodané polstrování, dokud je určité délky. Pokud je řetězec roven nebo větší než délka, je oříznut * ``rpad('dumbo', 10, '-') -> 'dumbo-----'`` 
* ``rpad('dumbo', 4, '-') -> 'dumb'`` 
* ``rpad('dumbo', 8, '<>') -> 'dumbo<><'`` 
___
### <code>rtrim</code>na délku rtrim</code>
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Doprava ořízne řetězec úvodních znaků. Pokud druhý parametr není zadán, ořízne prázdné znaky. Jinak ořízne libovolný znak zadaný v druhém parametru.* ``rtrim('  dumbo  ') -> '  dumbo'``
* ``rtrim('!--!du!mbo!', '-!') -> '!--!du!mbo'``
___
### <code>second</code>
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Získá druhou hodnotu data. Můžete předat volitelné časové pásmo ve formě "GMT", "PST", "UTC", "Amerika/Kajman". Jako výchozí se používá místní časové pásmo. Dostupné formáty naleznete v jazyce SimpleDateFormat v jazyce Java. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``second(toTimestamp('2009-07-30 12:58:59')) -> 59``
___
### <code>seconds</code>
<code><b>seconds(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Doba trvání v milisekundách po dobu několika sekund* ``seconds(2) -> 2000L``
___
### <code>sha1</code>
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Vypočítá výtah SHA-1 sady sloupců různých primitivních datových typů a vrátí šestnáctkový řetězec o 40 znaků. Může být použit k výpočtu otisku prstu pro řádek* ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '46d3b478e8ec4e1f3b453ac3d8e59d5854e282bb'``
___
### <code>sha2</code>
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
Vypočítá SHA-2 digest sady sloupec různých primitivních datových typů dané bitové délky, která může být pouze hodnoty 0(256), 224, 256, 384, 512. Může být použit k výpočtu otisku prstu pro řádek* ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'afe8a553b1761c67d76f8c31ceef7f71b66a1ee6f4e6d3b5478bf68b47d06bd3'``
___
### <code>sin</code>
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá sinusovou hodnotu.* ``sin(2) -> 0.9092974268256817``
___
### <code>sinh</code>
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá hodnotu hyperbolického sinusu.* ``sinh(0) -> 0.0``
___
### <code>slice</code>
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
Extrahuje podmnožinu pole z pozice. Pozice je založena na 1. Pokud je délka vynechána, je výchozí konec řetězce* ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``
* ``slice([10, 20, 30, 40], 2)[1] -> 20``
* ``isNull(slice([10, 20, 30, 40], 2)[0]) -> true``
* ``isNull(slice([10, 20, 30, 40], 2)[20]) -> true``
* ``slice(['a', 'b', 'c', 'd'], 8) -> []``
___
### <code>sort</code>
<code><b>sort(<i>&lt;value1&gt;</i> : array, <i>&lt;value2&gt;</i> : binaryfunction) => array</b></code><br/><br/>
Seřadí pole pomocí poskytované funkce predikátu. Řazení očekává odkaz na dva po sobě jdoucí prvky ve funkci výrazu jako #item1 a #item2* ``sort([4, 8, 2, 3], compare(#item1, #item2)) -> [2, 3, 4, 8]``
* ``sort(['a3', 'b2', 'c1'], iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) -> ['c1', 'b2', 'a3']``
___
### <code>soundex</code>
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Získá soundex kód pro řetězec* ``soundex('genius') -> 'G520'``
___
### <code>split</code>
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
Rozdělí řetězec na základě oddělovače a vrátí pole řetězců.* ``split('bojjus,guchus,dumbo', ',') -> ['bojjus', 'guchus', 'dumbo']``
* ``split('bojjus,guchus,dumbo', '|') -> ['bojjus,guchus,dumbo']``
* ``split('bojjus, guchus, dumbo', ', ') -> ['bojjus', 'guchus', 'dumbo']``
* ``split('bojjus, guchus, dumbo', ', ')[1] -> 'bojjus'``
* ``isNull(split('bojjus, guchus, dumbo', ', ')[0]) -> true``
* ``isNull(split('bojjus, guchus, dumbo', ', ')[20]) -> true``
* ``split('bojjusguchusdumbo', ',') -> ['bojjusguchusdumbo']``
___
### <code>sqrt</code>
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá druhou odmocninu čísla.* ``sqrt(9) -> 3``
___
### <code>startsWith</code>
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Zkontroluje, zda řetězec začíná dodaným řetězcem.* ``startsWith('dumbo', 'du') -> true``
___
### <code>subDays</code>
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Odečíst měsíce od data nebo časového razítka. Stejné jako operátor - pro datum* ``subDays(toDate('2016-08-08'), 1) -> toDate('2016-08-07')``
___
### <code>subMonths</code>
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Odečíst měsíce od data nebo časového razítka* ``subMonths(toDate('2016-09-30'), 1) -> toDate('2016-08-31')``
___
### <code>substring</code>
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
Extrahuje podřetězec určité délky z pozice. Pozice je založena na 1. Pokud je délka vynechána, je výchozí konec řetězce* ``substring('Cat in the hat', 5, 2) -> 'in'``
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``
* ``substring('Cat in the hat', 5) -> 'in the hat'``
* ``substring('Cat in the hat', 100, 100) -> ''``
___
### <code>tan</code>
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá hodnotu tečny.* ``tan(0) -> 0.0``
___
### <code>tanh</code>
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá hyperbolickou tečnou hodnotu.* ``tanh(0) -> 0.0``
___
### <code>toBase64</code>
<code><b>toBase64(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Zakóduje daný řetězec v base64* ``toBase64('bojjus') -> 'Ym9qanVz'``
___
### <code>toBinary</code>
<code><b>toBinary(<i>&lt;value1&gt;</i> : any) => binary</b></code><br/><br/>
Převede libovolnou číselnou/datum/časové razítko/řetězec na binární reprezentaci.* ``toBinary(3) -> [0x11]``
___
### <code>toBoolean</code>
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
Převede hodnotu ('t', 'true', 'y', 'yes', '1') na true a ('f', 'false', 'n', 'ne', '0') na false a NULL pro jakoukoli jinou hodnotu* ``toBoolean('true') -> true``
* ``toBoolean('n') -> false``
* ``isNull(toBoolean('truthy')) -> true``
___
### <code>toDate</code>
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
Převede vstupní řetězec data na datum pomocí volitelného formátu vstupního data. Dostupné formáty naleznete v jazyce SimpleDateFormat v jazyce Java. Pokud je formát vstupního data vynechán, výchozí formát je yyyy-[M]M-[d]d. Přijaté formáty jsou :[ yyyy, yyyy-[M]M, yyyy-[M]M-[d]d, yyyy-[M]M-[d]dT* ]* ``toDate('2012-8-18') -> toDate('2012-08-18')``
* ``toDate('12/18/2012', 'MM/dd/yyyy') -> toDate('2012-12-18')``
___
### <code>toDecimal</code>
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;precision&gt;</i> : integral], [<i>&lt;scale&gt;</i> : integral], [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
Převede libovolnou číselnou hodnotu nebo řetězec na desítkovou hodnotu. Pokud přesnost a měřítko nejsou zadány, je výchozí (10,2). Pro převod lze použít volitelný desítkový formát Javy. Volitelný formát národního prostředí ve formě jazyka BCP47, jako je en-US, de, zh-CN* ``toDecimal(123.45) -> 123.45``
* ``toDecimal('123.45', 8, 4) -> 123.4500``
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``
* ``toDecimal('Ç123,45', 10, 2, 'Ç###,##', 'de') -> 123.45``
___
### <code>toDouble</code>
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => double</b></code><br/><br/>
Převede libovolný číselný řetězec nebo řetězec na dvojitou hodnotu. Pro převod lze použít volitelný desítkový formát Javy. Volitelný formát národního prostředí ve formě jazyka BCP47, jako je en-US, de, zh-CN* ``toDouble(123.45) -> 123.45``
* ``toDouble('123.45') -> 123.45``
* ``toDouble('$123.45', '$###.00') -> 123.45``
* ``toDouble('Ç123,45', 'Ç###,##', 'de') -> 123.45``
___
### <code>toFloat</code>
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => float</b></code><br/><br/>
Převede libovolnou číselnou nebo řetězec na plovoucí hodnotu. Pro převod lze použít volitelný desítkový formát Javy. Zkrátí jakékoli dvojité* ``toFloat(123.45) -> 123.45f``
* ``toFloat('123.45') -> 123.45f``
* ``toFloat('$123.45', '$###.00') -> 123.45f``
___
### <code>toInteger</code>
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => integer</b></code><br/><br/>
Převede libovolnou číselnou nebo řetězec na hodnotu celého čísla. Pro převod lze použít volitelný desítkový formát Javy. Zkrátí všechny dlouhé, plovoucí, dvojité* ``toInteger(123) -> 123``
* ``toInteger('123') -> 123``
* ``toInteger('$123', '$###') -> 123``
___
### <code>toLong</code>
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => long</b></code><br/><br/>
Převede libovolný číselný řetězec nebo řetězec na dlouhou hodnotu. Pro převod lze použít volitelný desítkový formát Javy. Zkrátí jakýkoli plovák, dvojitý* ``toLong(123) -> 123``
* ``toLong('123') -> 123``
* ``toLong('$123', '$###') -> 123``
___
### <code>toShort</code>
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => short</b></code><br/><br/>
Převede libovolný číselný řetězec nebo řetězec na krátkou hodnotu. Pro převod lze použít volitelný desítkový formát Javy. Zkrátí celé číslo, dlouhé, plovoucí, dvojité* ``toShort(123) -> 123``
* ``toShort('123') -> 123``
* ``toShort('$123', '$###') -> 123``
___
### <code>toString</code>
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
Převede primitivní datový typ na řetězec. Pro čísla a datum lze zadat formát. Pokud není zadánvýchozí systém je vyskladněn. Pro čísla se používá desítkový formát Javy. Všechny možné formáty kalendářních dat naleznete v jazyce Java SimpleDateFormat. výchozí formát je yyyy-MM-dd* ``toString(10) -> '10'``
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
Převede řetězec na časové razítko s volitelným formátem časového razítka. Všechny možné formáty naleznete v jazyce Java SimpleDateFormat. Pokud je časové razítko vynecháno, použije se výchozí vzor.yyyy-[M]M-[d]d hh:mm:ss[.f...] . Můžete předat volitelné časové pásmo ve formě "GMT", "PST", "UTC", "Amerika/Kajman". Časové razítko podporuje až milisekundovou přesnost s hodnotou 999Refer Java SimpleDateFormat pro dostupné formáty. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``toTimestamp('2016-12-31 00:12:00') -> toTimestamp('2016-12-31 00:12:00')``
* ``toTimestamp('2016-12-31T00:12:00', 'yyyy-MM-dd\'T\'HH:mm:ss', 'PST') -> toTimestamp('2016-12-31 00:12:00')``
* ``toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss') -> toTimestamp('2016-12-31 00:12:00')``
* ``millisecond(toTimestamp('2019-02-03 05:19:28.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``
___
### <code>toUTC</code>
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Převede časové razítko na Čas UTC. Můžete předat volitelné časové pásmo ve formě "GMT", "PST", "UTC", "Amerika/Kajman". Je výchozí aktuální časové pásmoOdkazovat java simpledateformat pro dostupné formáty. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``toUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false``
* ``toUTC(currentTimeStamp(), 'Asia/Seoul') != toTimestamp('2050-12-12 19:18:12') -> true``
___
### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
Nahraďte jednu sadu znaků jinou sadou znaků v řetězci. Znaky mají 1 až 1 nahrazení* ``translate('(bojjus)', '()', '[]') -> '[bojjus]'``
* ``translate('(gunchus)', '()', '[') -> '[gunchus'``
___
### <code>trim</code>
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Ořízne řetězec úvodních a koncových znaků. Pokud druhý parametr není zadán, ořízne prázdné znaky. Jinak ořízne libovolný znak zadaný v druhém parametru.* ``trim('  dumbo  ') -> 'dumbo'``
* ``trim('!--!du!mbo!', '-!') -> 'du!mbo'``
___
### <code>true</code>
<code><b>true() => boolean</b></code><br/><br/>
Vždy vrátí skutečnou hodnotu. Syntaxe funkce(true()) použijte, pokud existuje sloupec s názvem true.* ``(10 + 20 == 30) -> true``
* ``(10 + 20 == 30) -> true()``
___
### <code>typeMatch</code>
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
Odpovídá typu sloupce. Lze použít pouze ve vzorexpressions.number odpovídá krátké, celé číslo, dlouhé, dvojité, plovoucí nebo desetinné, integrální shody krátké, celé, dlouhé, zlomkové shody dvojité, plovoucí, desetinné a datetime zápasy datum nebo typ časového razítka* ``typeMatch(type, 'number')``
* ``typeMatch('date', 'datetime')``
___
### <code>upper</code>
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Velká písmena řetězce* ``upper('bojjus') -> 'BOJJUS'``
___
### <code>uuid</code>
<code><b>uuid() => string</b></code><br/><br/>
Vrátí generované UUID.* ``uuid()``
___
### <code>weekOfYear</code>
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Získá týden v roce dané datum* ``weekOfYear(toDate('2008-02-20')) -> 8``
___
### <code>weeks</code>
<code><b>weeks(<i>&lt;value1&gt;</i> : integer) => long</b></code><br/><br/>
Doba trvání v milisekundách po dobu několika týdnů* ``weeks(2) -> 1209600000L``
___
### <code>xor</code>
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logický operátor XOR. Stejné jako operátor ^* ``xor(true, false) -> true``
* ``xor(true, true) -> false``
* ``true ^ false -> true``
___
### <code>year</code>
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Získá hodnotu roku * ``year(toDate('2012-8-8')) -> 2012`` 
## data Agregační funkce Následující funkce jsou k dispozici pouze v agregaci, pivot, unpivot a window transformace___
### <code>avg</code>
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Získá průměr hodnot sloupce* ``avg(sales)``
___
### <code>avgIf</code>
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Na základě kritérií získá průměr hodnot sloupce* ``avgIf(region == 'West', sales)``
___
### <code>count</code>
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
Získá agregační počet hodnot. Pokud je zadán volitelný sloupec(s), ignoruje hodnoty NULL v počtu* ``count(custId)``
* ``count(custId, custName)``
* ``count()``
* ``count(iif(isNull(custId), 1, NULL))``
___
### <code>countDistinct</code>
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
Získá agregační počet různých hodnot sady sloupců* ``countDistinct(custId, custName)``
___
### <code>countIf</code>
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
Na základě kritérií získá agregační počet hodnot. Pokud je zadán volitelný sloupec, ignoruje hodnoty NULL v počtu* ``countIf(state == 'CA' && commission < 10000, name)``
___
### <code>covariancePopulation</code>
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Získá kovariance základního souboru mezi dvěma sloupci* ``covariancePopulation(sales, profit)``
___
### <code>covariancePopulationIf</code>
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií získá kovariance populace dvou sloupců* ``covariancePopulationIf(region == 'West', sales)``
___
### <code>covarianceSample</code>
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Získá ukázkovou kovariance dvou sloupců* ``covarianceSample(sales, profit)``
___
### <code>covarianceSampleIf</code>
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií získá kovariance vzorku dvou sloupců* ``covarianceSampleIf(region == 'West', sales, profit)``
___
### <code>first</code>
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Získá první hodnotu skupiny sloupců. Pokud je druhý parametr ignoreNulls vynechán, předpokládá se, že je false* ``first(sales)``
* ``first(sales, false)``
___
### <code>kurtosis</code>
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Získá špičatost sloupce* ``kurtosis(sales)``
___
### <code>kurtosisIf</code>
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií dostane špičatost sloupce* ``kurtosisIf(region == 'West', sales)``
___
### <code>last</code>
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Získá poslední hodnotu skupiny sloupců. Pokud je druhý parametr ignoreNulls vynechán, předpokládá se, že je false* ``last(sales)``
* ``last(sales, false)``
___
### <code>max</code>
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Získá maximální hodnotu sloupce* ``max(sales)``
___
### <code>maxIf</code>
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Na základě kritérií získá maximální hodnotu sloupce* ``maxIf(region == 'West', sales)``
___
### <code>mean</code>
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Získá průměr hodnot sloupce. Stejné jako AVG* ``mean(sales)``
___
### <code>meanIf</code>
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Na základě kritérií získá průměr hodnot sloupce. Stejné jako avgIf* ``meanIf(region == 'West', sales)``
___
### <code>min</code>
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Získá minimální hodnotu sloupce* ``min(sales)``
___
### <code>minIf</code>
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Na základě kritérií získá minimální hodnotu sloupce* ``minIf(region == 'West', sales)``
___
### <code>skewness</code>
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Získá zkosení sloupce* ``skewness(sales)``
___
### <code>skewnessIf</code>
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií získá šikmost sloupce* ``skewnessIf(region == 'West', sales)``
___
### <code>stddev</code>
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Získá směrodatnou odchylku sloupce* ``stdDev(sales)``
___
### <code>stddevIf</code>
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií získá směrodatnou odchylku sloupce* ``stddevIf(region == 'West', sales)``
___
### <code>stddevPopulation</code>
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Získá směrodatnou odchylku základního souboru sloupce* ``stddevPopulation(sales)``
___
### <code>stddevPopulationIf</code>
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií získá směrodatná odchylka základního souboru sloupce* ``stddevPopulationIf(region == 'West', sales)``
___
### <code>stddevSample</code>
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Získá směrodatnou odchylku sloupce.* ``stddevSample(sales)``
___
### <code>stddevSampleIf</code>
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií získá směrodatná odchylka vzorku kolony* ``stddevSampleIf(region == 'West', sales)``
___
### <code>sum</code>
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Získá souhrnný součet číselného sloupce* ``sum(col)``
___
### <code>sumDistinct</code>
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Získá souhrnný součet různých hodnot číselného sloupce* ``sumDistinct(col)``
___
### <code>sumDistinctIf</code>
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Na základě kritérií získá souhrnný součet číselného sloupce. Podmínka může být založena na libovolném sloupci* ``sumDistinctIf(state == 'CA' && commission < 10000, sales)``
* ``sumDistinctIf(true, sales)``
___
### <code>sumIf</code>
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Na základě kritérií získá souhrnný součet číselného sloupce. Podmínka může být založena na libovolném sloupci* ``sumIf(state == 'CA' && commission < 10000, sales)``
* ``sumIf(true, sales)``
___
### <code>variance</code>
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Získá rozptyl sloupce* ``variance(sales)``
___
### <code>varianceIf</code>
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií získá rozptyl sloupce* ``varianceIf(region == 'West', sales)``
___
### <code>variancePopulation</code>
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Získá rozptyl základního souboru sloupce* ``variancePopulation(sales)``
___
### <code>variancePopulationIf</code>
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií získá rozptyl základního souboru sloupce* ``variancePopulationIf(region == 'West', sales)``
___
### <code>varianceSample</code>
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Získá nestranný rozptyl sloupce* ``varianceSample(sales)``
___
### <code>varianceSampleIf</code>
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií získá nestranný rozptyl funkce * ``varianceSampleIf(region == 'West', sales)`` 
## okna sloupce Následující funkce jsou k dispozici pouze v transformaci okna___
### <code>cumeDist</code>
<code><b>cumeDist() => integer</b></code><br/><br/>
Funkce CumeDist vypočítá pozici hodnoty vzhledem ke všem hodnotám v oddílu. Výsledkem je počet řádků, které předcházejí nebo se rovnají aktuálnímu řádku v pořadí oddílu vydělený celkovým počtem řádků v oddílu okna. Všechny hodnoty kravaty v řazení se vyhodnotí na stejnou pozici.
* ``cumeDist()``
___
### <code>denseRank</code>
<code><b>denseRank() => integer</b></code><br/><br/>
Vypočítá pořadí hodnoty ve skupině hodnot určených v pořadí okna podle klauzule. Výsledkem je jeden plus počet řádků předcházejících nebo rovno aktuální řádek v pořadí oddílu. Hodnoty nevytvoří mezery v sekvenci. Funkce Dense Rank funguje i v případě, že data nejsou seřazena a hledají změny hodnot.* ``denseRank()``
___
### <code>lag</code>
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Získá hodnotu první parametr vyhodnocenn n řádků před aktuální řádek. Druhý parametr je počet řádků, které je třeba ohlédnout zpět, a výchozí hodnota je 1. Pokud není k dispozici tolik řádků, je vrácena hodnota null, pokud není zadána výchozí hodnota.* ``lag(amount, 2)``
* ``lag(amount, 2000, 100)``
___
### <code>lead</code>
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Získá hodnotu první parametr vyhodnocenn n řádků za aktuální řádek. Druhý parametr je počet řádků, které se mají těšit dopředu, a výchozí hodnota je 1. Pokud není k dispozici tolik řádků, je vrácena hodnota null, pokud není zadána výchozí hodnota.* ``lead(amount, 2)``
* ``lead(amount, 2000, 100)``
___
### <code>nTile</code>
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
Funkce NTile rozděluje řádky pro každý `n` oddíl okna do bloků v `n`rozsahu od 1 do maximálně . Hodnoty segmentu se budou lišit maximálně o 1. Pokud se počet řádků v oddílu nerozdělí rovnoměrně na počet bloků, jsou zbývající hodnoty distribuovány jeden na segment, počínaje prvním kontejnerem. Funkce NTile je užitečná pro výpočet tertiles, quartiles, deciles a další společné souhrnné statistiky. Funkce vypočítá dvě proměnné během inicializace: Velikost běžného bloku bude mít jeden další řádek. Obě proměnné jsou založeny na velikosti aktuálního oddílu. Během procesu výpočtu funkce sleduje aktuální číslo řádku, aktuální číslo segmentu a číslo řádku, ve kterém se změní kontejner (bucketThreshold). Když aktuální číslo řádku dosáhne prahové hodnoty bloku, hodnota segmentu se zvýší o jednu a prahová hodnota se zvýší o velikost bloku (plus jeden další, pokud je aktuální kontejner doplněn).
* ``nTile()``
* ``nTile(numOfBuckets)``
___
### <code>rank</code>
<code><b>rank() => integer</b></code><br/><br/>
Vypočítá pořadí hodnoty ve skupině hodnot určených v pořadí okna podle klauzule. Výsledkem je jeden plus počet řádků předcházejících nebo rovno aktuální řádek v pořadí oddílu. Hodnoty vytvoří mezery v sekvenci. Pořadí funguje i v případě, že data nejsou seřazena a hledají změny hodnot* ``rank()``
___
### <code>rowNumber</code>
<code><b>rowNumber() => integer</b></code><br/><br/>
Přiřadí sekvenční číslování řádků v okně začínajících číslem 1.* ``rowNumber()``

## <a name="next-steps"></a>Další kroky

[Přečtěte si, jak používat Tvůrce výrazů](concepts-data-flow-expression-builder.md).
