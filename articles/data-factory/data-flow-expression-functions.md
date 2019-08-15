---
title: Funkce výrazu v funkci toku dat mapování Azure Data Factory
description: Přečtěte si o funkcích výrazů v mapování toku dat.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: 72c516f0a6e377cc16205917967482a29b4fdfbd
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036224"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>Výrazy transformace dat v toku mapování dat 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="expression-functions"></a>Funkce výrazu

V Data Factory pomocí jazyka výrazů funkce mapování toku dat nakonfigurujte transformace dat.

___
### <code>abs</code>
<code><b>abs(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Kladné zbytky páru čísel.
* ``abs(-20) -> 20``
* ``abs(10) -> 10``
___
### <code>acos</code>
<code><b>acos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá hodnotu inverzní kosinus.* ``acos(1) -> 0.0``
___
### <code>add</code>
<code><b>add(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Přidá dvojici řetězců nebo čísel. Přidá datum do počtu dní. Připojí jedno pole podobného typu k druhému. Stejné jako operátor +* ``add(10, 20) -> 30``
* ``10 + 20 -> 30``
* ``add('ice', 'cream') -> 'icecream'``
* ``'ice' + 'cream' + ' cone' -> 'icecream cone'``
* ``add(toDate('2012-12-12'), 3) -> 2012-12-15 (date value)``
* ``toDate('2012-12-12') + 3 -> 2012-12-15 (date value)``
* ``[10, 20] + [30, 40] => [10, 20, 30, 40]``
___
### <code>addDays</code>
<code><b>addDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to add&gt;</i> : integral) => datetime</b></code><br/><br/>
Přidejte dny k datu nebo časovému razítku. Stejné jako u operátoru + pro datum* ``addDays(toDate('2016-08-08'), 1) -> 2016-08-09``
___
### <code>addMonths</code>
<code><b>addMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to add&gt;</i> : integral) => datetime</b></code><br/><br/>
Přidání měsíců k datu nebo časovému razítku* ``addMonths(toDate('2016-08-31'), 1) -> 2016-09-30``
* ``addMonths(toTimestamp('2016-09-30 10:10:10'), -1) -> 2016-08-31 10:10:10``
___
### <code>and</code>
<code><b>and(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logický operátor AND Stejné jako & &* ``and(true, false) -> false``
* ``true && false -> false``
___
### <code>asin</code>
<code><b>asin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá hodnotu inverzní sinus.* ``asin(0) -> 0.0``
___
### <code>atan</code>
<code><b>atan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá hodnotu inverzní tangens.* ``atan(0) -> 0.0``
___
### <code>atan2</code>
<code><b>atan2(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Vrátí úhel v radiánech mezi kladnou osou x rovinou a bodem daným souřadnicemi.* ``atan2(0, 0) -> 0.0``
___
### <code>avg</code>
<code><b>avg(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Získá průměr hodnot sloupce.* ``avg(sales) -> 7523420.234``
___
### <code>avgIf</code>
<code><b>avgIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Na základě kritérií získá průměr hodnot sloupce.* ``avgIf(region == 'West', sales) -> 7523420.234``
___
### <code>byName</code>
<code><b>byName(<i>&lt;column name&gt;</i> : string) => any</b></code><br/><br/>
Vybere hodnotu sloupce podle názvu v datovém proudu. Pokud existuje více shod, je vrácena první shoda. Pokud žádná shoda není, vrátí hodnotu NULL. Vrácená hodnota musí být typu převedený jednou z funkcí pro převod typu (TO_DATE, TO_STRING...).  Názvy sloupců, které jsou známy v době návrhu, by měly být řešeny stejným názvem. Počítané vstupy se nepodporují, ale můžete použít substituce parametrů.* ``toString(byName('parent')) -> appa``
* ``toLong(byName('income')) -> 9000000000009``
* ``toBoolean(byName('foster')) -> false``
* ``toLong(byName($debtCol)) -> 123456890``
* ``birthDate -> 12/31/2050``
* ``toString(byName('Bogus Column')) -> NULL``
___
### <code>byPosition</code>
<code><b>byPosition(<i>&lt;position&gt;</i> : integer) => any</b></code><br/><br/>
Vybere hodnotu sloupce podle relativní pozice (na základě 1) v datovém proudu. Pokud je pozice mimo rozsah, vrátí hodnotu NULL. Vrácená hodnota musí být typu převedený jednou z funkcí pro převod typu (TO_DATE, TO_STRING...). Počítané vstupy se nepodporují, ale můžete použít substituce parametrů.* ``toString(byPosition(1)) -> amma``
* ``toDecimal(byPosition(2), 10, 2) -> 199990.99``
* ``toBoolean(byName(4)) -> false``
* ``toString(byName($colName)) -> family``
* ``toString(byPosition(1234)) -> NULL``
___
### <code>case</code>
<code><b>case(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, <i>&lt;false_expression&gt;</i> : any, ...) => any</b></code><br/><br/>
Na základě střídajících se podmínek používá jedna hodnota nebo druhá. Pokud je počet vstupů dokonce i pro poslední podmínku, druhý má hodnotu NULL.* ``case(custType == 'Premium', 10, 4.5)``
* ``case(custType == 'Premium', price*0.95, custType == 'Elite',   price*0.9, price*2)``
* ``case(dayOfWeek(saleDate) == 1, 'Sunday', dayOfWeek(saleDate) == 6, 'Saturday')``
___
### <code>cbrt</code>
<code><b>cbrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítat kořenovou složku krychle pro číslo* ``cbrt(8) -> 2.0``
___
### <code>ceil</code>
<code><b>ceil(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Vrátí nejmenší celé číslo, které není menší než číslo.* ``ceil(-0.1) -> 0``
___
### <code>coalesce</code>
<code><b>coalesce(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Vrátí první nenulovou hodnotu ze sady vstupů. Všechny vstupy by měly být stejného typu* ``coalesce(10, 20) -> 10``
* ``coalesce(toString(null), toString(null), 'dumbo', 'bo', 'go') -> 'dumbo'``
___
### <code>compare</code>
<code><b>compare(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => integer</b></code><br/><br/>
Porovná dvě hodnoty stejného typu. Vrátí záporné celé číslo, pokud hodnota1 < hodnota2, 0 if hodnota1 = = hodnota2, kladné hodnoty if hodnota1 > hodnota2.* ``(compare(12, 24) < 1) -> true``
* ``(compare('dumbo', 'dum') > 0) -> true``
___
### <code>concat</code>
<code><b>concat(<i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Zřetězí proměnný počet řetězců dohromady. Stejné jako u operátoru + s řetězci* ``concat('Awesome', 'Cool', 'Product') -> 'AwesomeCoolProduct'``
* ``'Awesome' + 'Cool' + 'Product' -> 'AwesomeCoolProduct'``
* ``concat(addrLine1, ' ', addrLine2, ' ', city, ' ', state, ' ', zip)``
* ``addrLine1 + ' ' + addrLine2 + ' ' + city + ' ' + state + ' ' + zip``
___
### <code>concatWS</code>
<code><b>concatWS(<i>&lt;separator&gt;</i> : string, <i>&lt;this&gt;</i> : string, <i>&lt;that&gt;</i> : string, ...) => string</b></code><br/><br/>
Zřetězí proměnlivý počet řetězců spolu s oddělovačem. První parametr je oddělovač.* ``concatWS(' ', 'Awesome', 'Cool', 'Product') -> 'Awesome Cool Product'``
* ``concatWS(' ' , addrLine1, addrLine2, city, state, zip) ->``
* ``concatWS(',' , toString(order_total), toString(order_discount))``
___
### <code>cos</code>
<code><b>cos(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá hodnotu kosinusu.* ``cos(10) -> -0.83907152907``
___
### <code>cosh</code>
<code><b>cosh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá hyperbolický kosinus hodnoty.* ``cosh(0) -> 1.0``
___
### <code>count</code>
<code><b>count([<i>&lt;value1&gt;</i> : any]) => long</b></code><br/><br/>
Získá agregovaný počet hodnot. Pokud jsou zadány volitelné sloupce, ignorují se hodnoty NULL v počtu.* ``count(custId) -> 100``
* ``count(custId, custName) -> 50``
* ``count() -> 125``
* ``count(iif(isNull(custId), 1, NULL)) -> 5``
___
### <code>countDistinct</code>
<code><b>countDistinct(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : any], ...) => long</b></code><br/><br/>
Získá agregovaný počet jedinečných hodnot sady sloupců.* ``countDistinct(custId, custName) -> 60``
___
### <code>countIf</code>
<code><b>countIf(<i>&lt;value1&gt;</i> : boolean, [<i>&lt;value2&gt;</i> : any]) => long</b></code><br/><br/>
Na základě kritérií získá agregovaný počet hodnot. Pokud je zadán volitelný sloupec, ignoruje hodnoty NULL v počtu.* ``countIf(state == 'CA' && commission < 10000, name) -> 100``
___
### <code>covariancePopulation</code>
<code><b>covariancePopulation(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Získá kovarianci populace mezi dvěma sloupci.* ``covariancePopulation(sales, profit) -> 122.12``
___
### <code>covariancePopulationIf</code>
<code><b>covariancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií získá kovarianci populace dvou sloupců.* ``covariancePopulationIf(region == 'West', sales) -> 122.12``
___
### <code>covarianceSample</code>
<code><b>covarianceSample(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Získá ukázkovou odchylku dvou sloupců.* ``covarianceSample(sales, profit) -> 122.12``
___
### <code>covarianceSampleIf</code>
<code><b>covarianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number, <i>&lt;value3&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií získá ukázková odchylka dvou sloupců.* ``covarianceSampleIf(region == 'West', sales, profit) -> 122.12``
___
### <code>crc32</code>
<code><b>crc32(<i>&lt;value1&gt;</i> : any, ...) => long</b></code><br/><br/>
Vypočítá hodnotu hash CRC32 sady sloupců různých primitivních DataTypes dané bitové délky, která může být pouze z hodnot 0 (256), 224, 256, 384, 512. Dá se použít k výpočtu otisku prstu pro řádek.* ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689``
___
### <code>cumeDist</code>
<code><b>cumeDist() => integer</b></code><br/><br/>
Funkce CumeDist vypočítá pozici hodnoty relativně ke všem hodnotám v oddílu. Výsledkem je počet řádků předcházejících nebo rovných aktuálnímu řádku v pořadí oddílu dělený celkovým počtem řádků v oddílu okna. Všechny hodnoty propojení v řazení se vyhodnotí na stejnou pozici.
* ``cumeDist() -> 1``
___
### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
Získá aktuální datum spuštění této úlohy. Můžete předat volitelné časové pásmo ve formátu "GMT", "PST", "UTC", "Amerika/Kajmanské". Místní časové pásmo se používá jako výchozí.
* ``currentDate() -> 12-12-2030``
* ``currentDate('PST') -> 12-31-2050``
___
### <code>currentTimestamp</code>
<code><b>currentTimestamp() => timestamp</b></code><br/><br/>
Získá aktuální časové razítko, kdy se úloha začne spouštět s místním časovým pásmem.* ``currentTimestamp() -> 12-12-2030T12:12:12``
___
### <code>currentUTC</code>
<code><b>currentUTC([<i>&lt;value1&gt;</i> : string]) => timestamp</b></code><br/><br/>
Získá aktuální časové razítko jako UTC. Můžete předat volitelné časové pásmo ve formátu "GMT", "PST", "UTC", "Amerika/Kajmanské". Ve výchozím nastavení je nastavené na aktuální časové pásmo.* ``currentUTC() -> 12-12-2030T19:18:12``
* ``currentUTC('Asia/Seoul') -> 12-13-2030T11:18:12``
___
### <code>dayOfMonth</code>
<code><b>dayOfMonth(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Vrátí den v měsíci, který má dané datum.* ``dayOfMonth(toDate('2018-06-08')) -> 08``
___
### <code>dayOfWeek</code>
<code><b>dayOfWeek(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Vrátí den v týdnu daného data. 1 – neděle, 2 – pondělí..., 7 – sobota* ``dayOfWeek(toDate('2018-06-08')) -> 7``
___
### <code>dayOfYear</code>
<code><b>dayOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Načte den v roce, který má dané datum.* ``dayOfYear(toDate('2016-04-09')) -> 100``
___
### <code>degrees</code>
<code><b>degrees(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Převede radiány na stupně.* ``degrees(3.141592653589793) -> 180``
___
### <code>denseRank</code>
<code><b>denseRank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
Vypočítá pořadí hodnoty ve skupině hodnot. Výsledkem je jedna hodnota plus počet řádků předcházejících nebo rovných aktuálnímu řádku v pořadí oddílu. Hodnoty nebudou v sekvenci vydávat žádné mezery. Zhuštěné řazení funguje i v případě, že data nejsou seřazená a vyhledávají změny v hodnotách.* ``denseRank(salesQtr, salesAmt) -> 1``
___
### <code>divide</code>
<code><b>divide(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Vydělí dvojici čísel. Stejné jako operátor/* ``divide(20, 10) -> 2``
* ``20 / 10 -> 2``
___
### <code>endsWith</code>
<code><b>endsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Zkontroluje, jestli řetězec končí zadaným řetězcem.* ``endsWith('great', 'eat') -> true``
___
### <code>equals</code>
<code><b>equals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operátor porovnání je rovno. Stejné jako = = – operátor* ``equals(12, 24) -> false``
* ``12==24 -> false``
* ``'bad'=='bad' -> true``
* ``'good'== NULL -> false``
* ``NULL===NULL -> false``
___
### <code>equalsIgnoreCase</code>
<code><b>equalsIgnoreCase(<i>&lt;value1&gt;</i> : string, <i>&lt;value2&gt;</i> : string) => boolean</b></code><br/><br/>
Porovnání se rovná operátoru bez ohledu na velikost písmen. Stejné jako < = operátor >* ``'abc'<==>'abc' -> true``
* ``equalsIgnoreCase('abc', 'Abc') -> true``
___
### <code>factorial</code>
<code><b>factorial(<i>&lt;value1&gt;</i> : number) => long</b></code><br/><br/>
Vypočítat faktoriál čísla* ``factorial(5) -> 120``
___
### <code>false</code>
<code><b>false() => boolean</b></code><br/><br/>
Vždy vrátí hodnotu false. Použijte syntaxi funkce (false ()), pokud je sloupec s názvem false* ``isDiscounted == false()``
* ``isDiscounted() == false``
___
### <code>first</code>
<code><b>first(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Získá první hodnotu skupiny sloupců. Pokud je vynechán druhý parametr ignoreNulls, předpokládá se hodnota false.* ``first(sales) -> 12233.23``
* ``first(sales, false) -> NULL``
___
### <code>floor</code>
<code><b>floor(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Vrátí největší celé číslo, které není větší než číslo.* ``floor(-0.1) -> -1``
___
### <code>fromUTC</code>
<code><b>fromUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Převede na časové razítko od času UTC. Časové pásmo můžete volitelně předat ve formátu "GMT", "PST", "UTC", "Amerika/Kajmanské". Ve výchozím nastavení je nastavené na aktuální časové pásmo.* ``fromUTC(currentTimeStamp()) -> 12-12-2030T19:18:12``
* ``fromUTC(currentTimeStamp(), 'Asia/Seoul') -> 12-13-2030T11:18:12``
___
### <code>greater</code>
<code><b>greater(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Porovnání většího operátoru. Stejné jako operátor >* ``greater(12, 24) -> false``
* ``'abcd' > 'abc' -> true``
___
### <code>greaterOrEqual</code>
<code><b>greaterOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Porovnání větší než nebo rovno operátoru. Stejné jako > = operátor* ``greaterOrEqual(12, 12) -> false``
* ``'abcd' >= 'abc' -> true``
___
### <code>greatest</code>
<code><b>greatest(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Vrátí největší hodnotu ze seznamu hodnot jako vstup. Vrátí hodnotu null, pokud všechny vstupy mají hodnotu null.* ``greatest(10, 30, 15, 20) -> 30``
* ``greatest(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2011'``
___
### <code>hour</code>
<code><b>hour(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Získá hodnotu hodiny časového razítka. Můžete předat volitelné časové pásmo ve formátu "GMT", "PST", "UTC", "Amerika/Kajmanské". Místní časové pásmo se používá jako výchozí.
* ``hour(toTimestamp('2009-07-30T12:58:59')) -> 12``
* ``hour(toTimestamp('2009-07-30T12:58:59'), 'PST') -> 12``
___
### <code>iif</code>
<code><b>iif(<i>&lt;condition&gt;</i> : boolean, <i>&lt;true_expression&gt;</i> : any, [<i>&lt;false_expression&gt;</i> : any]) => any</b></code><br/><br/>
Na základě podmínky platí jedna nebo druhá hodnota. Pokud není uvedeno jinak, považuje se za NULL. Obě hodnoty musí být kompatibilní (číselná, řetězec...).* ``iif(custType == 'Premium', 10, 4.5)``
* ``iif(amount > 100, 'High')``
* ``iif(dayOfWeek(saleDate) == 6, 'Weekend', 'Weekday')``
___
### <code>in</code>
<code><b>in(<i>&lt;array of items&gt;</i> : array, <i>&lt;item to find&gt;</i> : any) => boolean</b></code><br/><br/>
Zkontroluje, jestli je položka v poli.* ``in([10, 20, 30], 10) -> true``
* ``in(['good', 'kid'], 'bad') -> false``
___
### <code>initCap</code>
<code><b>initCap(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Převede první písmeno každého slova na velká. Slova jsou označená jako oddělená prázdným znakem.* ``initCap('cool iceCREAM') -> 'Cool IceCREAM'``
___
### <code>instr</code>
<code><b>instr(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string) => integer</b></code><br/><br/>
Vyhledá pozici (na základě 1) podřetězce v rámci řetězce. 0 se vrátí, pokud se nenajde.* ``instr('great', 'eat') -> 3``
* ``instr('microsoft', 'o') -> 7``
* ``instr('good', 'bad') -> 0``
___
### <code>isDelete</code>
<code><b>isDelete([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Zkontroluje, jestli je řádek označený k odstranění. Pro transformace s více než jedním vstupním datovým proudem můžete index (založený na 1) předat tomuto datovému proudu. Výchozí hodnota indexu streamu je 1.* ``isDelete() -> true``
* ``isDelete(1) -> false``
___
### <code>isError</code>
<code><b>isError([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Zkontroluje, jestli je řádek označený jako chyba. Pro transformace s více než jedním vstupním datovým proudem můžete index (založený na 1) předat tomuto datovému proudu. Výchozí hodnota indexu streamu je 1.* ``isError() -> true``
* ``isError(1) -> false``
___
### <code>isIgnore</code>
<code><b>isIgnore([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Zkontroluje, jestli je řádek označený jako ignorovaný. Pro transformace s více než jedním vstupním datovým proudem můžete index (založený na 1) předat tomuto datovému proudu. Výchozí hodnota indexu streamu je 1.* ``isIgnore() -> true``
* ``isIgnore(1) -> false``
___
### <code>isInsert</code>
<code><b>isInsert([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Zkontroluje, jestli je řádek označený pro vložení. Pro transformace s více než jedním vstupním datovým proudem můžete index (založený na 1) předat tomuto datovému proudu. Výchozí hodnota indexu streamu je 1.* ``isInsert() -> true``
* ``isInsert(1) -> false``
___
### <code>isMatch</code>
<code><b>isMatch([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Zkontroluje, jestli se řádek při vyhledávání shodoval. Pro transformace s více než jedním vstupním datovým proudem můžete index (založený na 1) předat tomuto datovému proudu. Výchozí hodnota indexu streamu je 1.* ``isMatch() -> true``
* ``isMatch(1) -> false``
___
### <code>isNull</code>
<code><b>isNull(<i>&lt;value1&gt;</i> : any) => boolean</b></code><br/><br/>
Zkontroluje, jestli je hodnota NULL.* ``isNull(NULL()) -> true``
* ``isNull('') -> false'``
___
### <code>isUpdate</code>
<code><b>isUpdate([<i>&lt;value1&gt;</i> : integer]) => boolean</b></code><br/><br/>
Zkontroluje, jestli je řádek označený k aktualizaci. Pro transformace s více než jedním vstupním datovým proudem můžete index (založený na 1) předat tomuto datovému proudu. Výchozí hodnota indexu streamu je 1.* ``isUpdate() -> true``
* ``isUpdate(1) -> false``
___
### <code>kurtosis</code>
<code><b>kurtosis(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Získá špičatost sloupce.* ``kurtosis(sales) -> 122.12``
___
### <code>kurtosisIf</code>
<code><b>kurtosisIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií získá špičatost sloupce.* ``kurtosisIf(region == 'West', sales) -> 122.12``
___
### <code>lag</code>
<code><b>lag(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look before&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Získá hodnotu prvního parametru vyhodnoceného n řádků před aktuálním řádkem. Druhým parametrem je počet řádků, které se mají vyhledat, a výchozí hodnota je 1. Pokud není k dispozici tolik řádků, je vrácena hodnota null, pokud není zadána výchozí hodnota.* ``lag(amount, 2) -> 60``
* ``lag(amount, 2000, 100) -> 100``
___
### <code>last</code>
<code><b>last(<i>&lt;value1&gt;</i> : any, [<i>&lt;value2&gt;</i> : boolean]) => any</b></code><br/><br/>
Načte poslední hodnotu skupiny sloupců. Pokud je vynechán druhý parametr ignoreNulls, předpokládá se hodnota false.* ``last(sales) -> 523.12``
* ``last(sales, false) -> NULL``
___
### <code>lastDayOfMonth</code>
<code><b>lastDayOfMonth(<i>&lt;value1&gt;</i> : datetime) => date</b></code><br/><br/>
Získá poslední datum měsíce daného data.* ``lastDayOfMonth(toDate('2009-01-12')) -> 2009-01-31``
___
### <code>lead</code>
<code><b>lead(<i>&lt;value&gt;</i> : any, [<i>&lt;number of rows to look after&gt;</i> : number], [<i>&lt;default value&gt;</i> : any]) => any</b></code><br/><br/>
Získá hodnotu prvního parametru vyhodnoceného n řádků za aktuálním řádkem. Druhým parametrem je počet řádků, které se mají hledat, a výchozí hodnota je 1. Pokud není k dispozici tolik řádků, je vrácena hodnota null, pokud není zadána výchozí hodnota.* ``lead(amount, 2) -> 60``
* ``lead(amount, 2000, 100) -> 100``
___
### <code>least</code>
<code><b>least(<i>&lt;value1&gt;</i> : any, ...) => any</b></code><br/><br/>
Porovnání menší než nebo rovno operátoru. Stejné jako < = operátor* ``least(10, 30, 15, 20) -> 10``
* ``least(toDate('12/12/2010'), toDate('12/12/2011'), toDate('12/12/2000')) -> '12/12/2000'``
___
### <code>left</code>
<code><b>left(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Extrahuje podřetězec začínající na indexu 1 s počtem znaků. Stejné jako podřetězec (str, 1, n)* ``left('bojjus', 2) -> 'bo'``
* ``left('bojjus', 20) -> 'bojjus'``
___
### <code>length</code>
<code><b>length(<i>&lt;value1&gt;</i> : string) => integer</b></code><br/><br/>
Vrátí délku řetězce.* ``length('kiddo') -> 5``
___
### <code>lesser</code>
<code><b>lesser(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operátor porovnání less. Stejné jako operátor <* ``lesser(12 < 24) -> true``
* ``'abcd' < 'abc' -> false``
___
### <code>lesserOrEqual</code>
<code><b>lesserOrEqual(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Porovnání menší než nebo rovno operátoru. Stejné jako < = operátor* ``lesserOrEqual(12, 12) -> true``
* ``'abcd' <= 'abc' -> false``
___
### <code>levenshtein</code>
<code><b>levenshtein(<i>&lt;from string&gt;</i> : string, <i>&lt;to string&gt;</i> : string) => integer</b></code><br/><br/>
Získá levenshtein vzdálenost mezi dvěma řetězci.* ``levenshtein('boys', 'girls') -> 4``
___
### <code>like</code>
<code><b>like(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Vzor je řetězec, který se bude shodovat s doslova. Výjimky jsou následující speciální symboly: _ odpovídá jakémukoli jednomu znaku ve vstupu (podobně jako. v regulárních výrazech POSIX)% odpovídá nule nebo více znakům ve vstupu (podobně jako. * v regulárních výrazech POSIX).
Řídicí znak je ' '. Pokud řídicí znak před speciálním symbolem nebo jiným řídicím znakem, je následující znak porovnán. Řídicí znak není v žádném jiném znaku neplatný.
* ``like('icecream', 'ice%') -> true``
___
### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
Vyhledá pozici (na základě 1) podřetězce v řetězci, který spouští určitou pozici. Pokud je pozice vynechána, je považována za začátek řetězce. 0 se vrátí, pokud se nenajde.* ``locate('eat', 'great') -> 3``
* ``locate('o', 'microsoft', 6) -> 7``
* ``locate('bad', 'good') -> 0``
___
### <code>log</code>
<code><b>log(<i>&lt;value1&gt;</i> : number, [<i>&lt;value2&gt;</i> : number]) => double</b></code><br/><br/>
Vypočítá hodnotu protokolu. Volitelná základní hodnota se dá zadat v jiném Eulerova čísle, pokud se používá.* ``log(100, 10) -> 2``
___
### <code>log10</code>
<code><b>log10(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá hodnotu protokolu založenou na 10 základu.* ``log10(100) -> 2``
___
### <code>lower</code>
<code><b>lower(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Malá písmena řetězce* ``lower('GunChus') -> 'gunchus'``
___
### <code>lpad</code>
<code><b>lpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Vlevo vyplní řetězec doplněním zadaného odsazení, dokud nemá určitou délku. Pokud je řetězec větší nebo roven délce, pak * ``lpad('great', 10, '-') -> '___--great'`` se považuje za Lpad "No-op 
* ``lpad('great', 4, '-') -> 'great'`` 
* " ' (' Great ', 8, ' < > ')-> ' < ><great'``
___
### <code>LTrim</code>
<code><b>ltrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code><br/><br/>
Levá ořízne řetězec úvodních znaků. Pokud není zadán druhý parametr, ořízne prázdné znaky. Jinak ořízne libovolný znak zadaný ve druhém parametru.* ``ltrim('!--!wor!ld!', '-!') -> 'wor!ld!'``
___
### <code>max</code>
<code><b>max(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Získá maximální hodnotu sloupce.* ``MAX(sales) -> 12312131.12``
___
### <code>maxIf</code>
<code><b>maxIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Na základě kritérií získá maximální hodnotu sloupce.* ``maxIf(region == 'West', sales) -> 99999.56``
___
### <code>md5</code>
<code><b>md5(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Vypočítá výtah MD5 sady sloupců různých primitivních typů DataTypes a vrátí šestnáctkový řetězec 32 znaků. Dá se použít k výpočtu otisku prstu pro řádek.* ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'c1527622a922c83665e49835e46350fe'``
___
### <code>mean</code>
<code><b>mean(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Získá průměrnou hodnotu sloupce. Stejné jako prům.* ``mean(sales) -> 7523420.234``
___
### <code>meanIf</code>
<code><b>meanIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Na základě kritérií získá průměrnou hodnotu sloupce. Stejné jako avgIf* ``meanIf(region == 'West', sales) -> 7523420.234``
___
### <code>millisecond</code>
<code><b>millisecond(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Získá hodnotu milisekundy pro datum. Můžete předat volitelné časové pásmo ve formátu "GMT", "PST", "UTC", "Amerika/Kajmanské". Místní časové pásmo se používá jako výchozí.
* ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871``
___
### <code>min</code>
<code><b>min(<i>&lt;value1&gt;</i> : any) => any</b></code><br/><br/>
Získá minimální hodnotu sloupce.* ``min(sales) -> 00.01``
* ``min(orderDate) -> 12/12/2000``
___
### <code>minIf</code>
<code><b>minIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Na základě kritérií získá minimální hodnotu sloupce.* ``minIf(region == 'West', sales) -> 00.01``
___
### <code>minus</code>
<code><b>minus(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Odečte čísla. Odečte se od počtu dní od data. Stejné jako operátor-operator* ``minus(20, 10) -> 10``
* ``20 - 10 -> 10``
* ``minus(toDate('2012-12-15'), 3) -> 2012-12-12 (date value)``
* ``toDate('2012-12-15') - 3 -> 2012-12-13 (date value)``
___
### <code>minute</code>
<code><b>minute(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Získá hodnotu minuty časového razítka. Můžete předat volitelné časové pásmo ve formátu "GMT", "PST", "UTC", "Amerika/Kajmanské". Místní časové pásmo se používá jako výchozí.
* ``minute(toTimestamp('2009-07-30T12:58:59')) -> 58``
* ``minute(toTimestamp('2009-07-30T12:58:59', 'PST')) -> 58``
___
### <code>mod</code>
<code><b>mod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Zbytek páru čísel. Stejné jako operátor%* ``mod(20, 8) -> 4``
* ``20 % 8 -> 4``
___
### <code>month</code>
<code><b>month(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Získá hodnotu měsíce pro datum nebo časové razítko.* ``month(toDate('2012-8-8')) -> 8``
___
### <code>monthsBetween</code>
<code><b>monthsBetween(<i>&lt;from date/timestamp&gt;</i> : datetime, <i>&lt;to date/timestamp&gt;</i> : datetime, [<i>&lt;time zone&gt;</i> : boolean], [<i>&lt;value4&gt;</i> : string]) => double</b></code><br/><br/>
Vrátí počet měsíců mezi dvěma datesYouy může předat volitelné časové pásmo ve formátu "GMT", "PST", "UTC", "Amerika/Kajmanské". Místní časové pásmo se používá jako výchozí.
* ``monthsBetween(toDate('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677``
___
### <code>multiply</code>
<code><b>multiply(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Vynásobí dvojici čísel. Stejné jako operátor ** ``multiply(20, 10) -> 200``
* ``20 * 10 -> 200``
___
### <code>nTile</code>
<code><b>nTile([<i>&lt;value1&gt;</i> : integer]) => integer</b></code><br/><br/>
Funkce NTile rozdělí řádky pro každý oddíl okna do `n` intervalů v rozsahu od 1 do `n`nejvýše. Hodnoty plechovky se budou lišit o nejvýše 1. Pokud počet řádků v oddílu není rovnoměrně rozdělen do počtu intervalů, pak jsou zbývající hodnoty distribuovány po jednotlivých kontejnerech počínaje prvním intervalem. Funkce NTile je užitečná pro výpočet tertiles, Kvartily, deciles a dalších běžných souhrnných statistik. Funkce vypočítá dvě proměnné během inicializace: K velikosti regulárního intervalu se přidá jeden řádek navíc. Obě proměnné jsou založené na velikosti aktuálního oddílu. Během procesu výpočtu funkce udržuje sledovat aktuální číslo řádku, aktuální číslo sady a číslo řádku, na kterém se bude interval měnit (bucketThreshold). Když číslo aktuálního řádku dosáhne prahové hodnoty intervalu, hodnota intervalu se zvýší o jednu a prahová hodnota se zvýší o velikost intervalu (plus jeden další, pokud je aktuální kontejner doplněn).
* ``nTile() -> 1``
* ``nTile(numOfBuckets) -> 1``
___
### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Negace čísla. Změní kladná čísla na záporná a naopak.* ``negate(13) -> -13``
___
### <code>nextSequence</code>
<code><b>nextSequence() => long</b></code><br/><br/>
Vrátí další jedinečnou sekvenci. Číslo je po sobě po sobě pouze v rámci oddílu a má předponu partitionId* ``nextSequence() -> 12313112``
___
### <code>normalize</code>
<code><b>normalize(<i>&lt;String to normalize&gt;</i> : string) => string</b></code><br/><br/>
Normalizuje řetězcovou hodnotu pro oddělení zvýrazněných znaků Unicode.* ``normalize('boys') -> 'boys'``
___
### <code>not</code>
<code><b>not(<i>&lt;value1&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logický operátor negace* ``not(true) -> false``
* ``not(premium)``
___
### <code>notEquals</code>
<code><b>notEquals(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => boolean</b></code><br/><br/>
Operátor porovnání není rovno. Stejné jako! = – operátor* ``12!=24 -> true``
* ``'abc'!='abc' -> false``
___
### <code>null</code>
<code><b>null() => null</b></code><br/><br/>
Vrací hodnotu NULL. Použijte syntaxi funkce (null ()), pokud je sloupec s názvem null. Výsledkem jakékoli operace, která používá, je hodnota NULL.* ``custId = NULL (for derived field)``
* ``custId == NULL -> NULL``
* ``'nothing' + NULL -> NULL``
* ``10 * NULL -> NULL'``
* ``NULL == '' -> NULL'``
___
### <code>or</code>
<code><b>or(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logický operátor OR Stejné jako | |* ``or(true, false) -> true``
* ``true || false -> true``
___
### <code>pMod</code>
<code><b>pMod(<i>&lt;value1&gt;</i> : any, <i>&lt;value2&gt;</i> : any) => any</b></code><br/><br/>
Kladné zbytky páru čísel.
* ``pmod(-20, 8) -> 4``
___
### <code>power</code>
<code><b>power(<i>&lt;value1&gt;</i> : number, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Umocní jedno číslo na mocninu jiného.* ``power(10, 2) -> 100``
___
### <code>rank</code>
<code><b>rank(<i>&lt;value1&gt;</i> : any, ...) => integer</b></code><br/><br/>
Vypočítá pořadí hodnoty ve skupině hodnot. Výsledkem je jedna hodnota plus počet řádků předcházejících nebo rovných aktuálnímu řádku v pořadí oddílu. Hodnoty budou v posloupnosti vydávat mezery. Pořadí funguje i v případě, že data nejsou seřazena a vyhledávají změny v hodnotách* ``rank(salesQtr, salesAmt) -> 1``
___
### <code>regexExtract</code>
<code><b>regexExtract(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, [<i>&lt;match group 1-based index&gt;</i> : integral]) => string</b></code><br/><br/>
Extrahuje shodný dílčí řetězec pro daný vzor regulárního výrazu. Poslední parametr identifikuje skupinu shod a je-li vynechán, je nastaven na hodnotu 1. Použijte (<regex>back-quot), aby odpovídala řetězci bez uvozovacího příkazu.* ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'``
* ``regexExtract('Cost is between 600 and 800 dollars', `(\d+) and (\d+)`, 2) -> '800'``
___
### <code>regexMatch</code>
<code><b>regexMatch(<i>&lt;string&gt;</i> : string, <i>&lt;regex to match&gt;</i> : string) => boolean</b></code><br/><br/>
Zkontroluje, zda řetězec odpovídá danému vzoru regulárního výrazu. Použijte (<regex>back-quot), aby odpovídala řetězci bez uvozovacího příkazu.* ``regexMatch('200.50', '(\\d+).(\\d+)') -> true``
* ``regexMatch('200.50', `(\d+).(\d+)`) -> true``
___
### <code>regexReplace</code>
<code><b>regexReplace(<i>&lt;string&gt;</i> : string, <i>&lt;regex to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Nahradí všechny výskyty vzorového regulárního výrazu jiným podřetězcem v daném řetězci. Pokud<regex>chcete porovnat řetězec bez uvozovacích znaků, použijte uvozovky (zpátky).* ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'``
* ``regexReplace('100 and 200', `(\d+)`, 'gunchus') -> 'gunchus and gunchus'``
___
### <code>regexSplit</code>
<code><b>regexSplit(<i>&lt;string to split&gt;</i> : string, <i>&lt;regex expression&gt;</i> : string) => array</b></code><br/><br/>
Rozdělí řetězec na základě oddělovače na základě regulárního výrazu a vrátí pole řetězců.* ``regexSplit('oneAtwoBthreeC', '[CAB]') -> ['one', 'two', 'three']``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[1] -> 'one'``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[0] -> NULL``
* ``regexSplit('oneAtwoBthreeC', '[CAB]')[20] -> NULL``
___
### <code>replace</code>
<code><b>replace(<i>&lt;string&gt;</i> : string, <i>&lt;substring to find&gt;</i> : string, <i>&lt;substring to replace&gt;</i> : string) => string</b></code><br/><br/>
Nahradí všechny výskyty podřetězce jiným podřetězcem v daném řetězci.* ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'``
* ``replace('doggie dog', 'dog', '') -> 'gie'``
___
### <code>reverse</code>
<code><b>reverse(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Obrátí řetězec.* ``reverse('gunchus') -> 'suhcnug'``
___
### <code>right</code>
<code><b>right(<i>&lt;string to subset&gt;</i> : string, <i>&lt;number of characters&gt;</i> : integral) => string</b></code><br/><br/>
Extrahuje podřetězec s počtem znaků zprava. Stejné jako podřetězec (str, LENGTH (str)-n, n)* ``right('bojjus', 2) -> 'us'``
* ``right('bojjus', 20) -> 'bojjus'``
___
### <code>rlike</code>
<code><b>rlike(<i>&lt;string&gt;</i> : string, <i>&lt;pattern match&gt;</i> : string) => boolean</b></code><br/><br/>
Zkontroluje, jestli řetězec odpovídá danému vzoru regulárního výrazu.* ``rlike('200.50', '(\d+).(\d+)') -> true``
___
### <code>round</code>
<code><b>round(<i>&lt;number&gt;</i> : number, [<i>&lt;scale to round&gt;</i> : number], [<i>&lt;rounding option&gt;</i> : integral]) => double</b></code><br/><br/>
Zaokrouhlí číslo z daného volitelného měřítka a volitelného režimu zaokrouhlení. Pokud je měřítko vynecháno, je nastavené na hodnotu 0.  Pokud je tento režim vynechán, je nastaven jako výchozí na ROUND_HALF_UP (5). Hodnoty pro zaokrouhlování zahrnují 1 – ROUND_UP 2-ROUND_DOWN 3-ROUND_CEILING 4-ROUND_FLOOR 5-ROUND_HALF_UP 6-ROUND_HALF_DOWN 7-ROUND_HALF_EVEN 8-ROUND_UNNECESSARY* ``round(100.123) -> 100.0``
* ``round(2.5, 0) -> 3.0``
* ``round(5.3999999999999995, 2, 7) -> 5.40``
___
### <code>rowNumber</code>
<code><b>rowNumber() => integer</b></code><br/><br/>
Přiřadí pořadové číslování řádků v okně od 1* ``rowNumber() -> 1``
___
### <code>rpad</code>
<code><b>rpad(<i>&lt;string to pad&gt;</i> : string, <i>&lt;final padded length&gt;</i> : integral, <i>&lt;padding&gt;</i> : string) => string</b></code><br/><br/>
Doplní řetězec doplněním zadaného odsazení, dokud nemá určitou délku. Pokud je řetězec větší nebo roven délce, pak se považuje za neop * ``rpad('great', 10, '-') -> 'great___--'``. 
* ``rpad('great', 4, '-') -> 'great'`` 
* ``rpad('great', 8, '<>') -> 'great<><'`` 
___
### <code>rtrim</code></code>
<code><b>rtrim(<i>&lt;string to trim&gt;</i> : string, <i>&lt;trim characters&gt;</i> : string) => string</b></code><br/><br/>
Pravá ořízne řetězec úvodních znaků. Pokud není zadán druhý parametr, ořízne prázdné znaky. Jinak ořízne libovolný znak zadaný ve druhém parametru.* ``rtrim('!--!wor!ld!', '-!') -> '!--!wor!ld'``
___
### <code>second</code>
<code><b>second(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => integer</b></code><br/><br/>
Získá druhou hodnotu data. Můžete předat volitelné časové pásmo ve formátu "GMT", "PST", "UTC", "Amerika/Kajmanské". Místní časové pásmo se používá jako výchozí.
* ``second(toTimestamp('2009-07-30T12:58:59')) -> 59``
___
### <code>sha1</code>
<code><b>sha1(<i>&lt;value1&gt;</i> : any, ...) => string</b></code><br/><br/>
Vypočítá hodnotu hash SHA-1 sady sloupců různých primitivních typů DataTypes a vrátí šestnáctkový řetězec 40 znaků. Dá se použít k výpočtu otisku prstu pro řádek.* ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '63849fd2abb65fbc626c60b1f827bd05573f0cea'``
___
### <code>sha2</code>
<code><b>sha2(<i>&lt;value1&gt;</i> : integer, <i>&lt;value2&gt;</i> : any, ...) => string</b></code><br/><br/>
Vypočítá hodnotu hash SHA-2 sady sloupců různých primitivních datových typů s délkou, která může být pouze z hodnot 0 (256), 224, 256, 384, 512. Dá se použít k výpočtu otisku prstu pro řádek.* ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'd3b2bff62c3a00e9370b1ac85e428e661a7df73959fa1a96ae136599e9ee20fd'``
___
### <code>sin</code>
<code><b>sin(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá hodnotu sinus.* ``sin(2) -> 0.90929742682``
___
### <code>sinh</code>
<code><b>sinh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá hodnotu hyperbolický sinus.* ``sinh(0) -> 0.0``
___
### <code>skewness</code>
<code><b>skewness(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Získá zešikmení sloupce.* ``skewness(sales) -> 122.12``
___
### <code>skewnessIf</code>
<code><b>skewnessIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií získá zešikmení sloupce.* ``skewnessIf(region == 'West', sales) -> 122.12``
___
### <code>slice</code>
<code><b>slice(<i>&lt;array to slice&gt;</i> : array, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of items&gt;</i> : integral]) => array</b></code><br/><br/>
Extrahuje podmnožinu pole z pozice. Pozice je na 1. Pokud je délka vynechána, je nastavena na konec řetězce.* ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]``
* ``slice([10, 20, 30, 40], 2) -> [20, 30, 40]``
* ``slice([10, 20, 30, 40], 2)[1] -> 20``
* ``slice([10, 20, 30, 40], 2)[0] -> NULL``
* ``slice([10, 20, 30, 40], 2)[20] -> NULL``
* ``slice([10, 20, 30, 40], 8) -> []``
___
### <code>soundex</code>
<code><b>soundex(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Získá kód SOUNDEX pro řetězec.* ``soundex('genius') -> 'G520'``
___
### <code>split</code>
<code><b>split(<i>&lt;string to split&gt;</i> : string, <i>&lt;split characters&gt;</i> : string) => array</b></code><br/><br/>
Rozdělí řetězec na základě oddělovače a vrátí pole řetězců.* ``split('100,200,300', ',') -> ['100', '200', '300']``
* ``split('100,200,300', '|') -> ['100,200,300']``
* ``split('100, 200, 300', ', ') -> ['100', '200', '300']``
* ``split('100, 200, 300', ', ')[1] -> '100'``
* ``split('100, 200, 300', ', ')[0] -> NULL``
* ``split('100, 200, 300', ', ')[20] -> NULL``
* ``split('100200300', ',') -> ['100200300']``
___
### <code>sqrt</code>
<code><b>sqrt(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá druhou odmocninu čísla.* ``sqrt(9) -> 3``
___
### <code>startsWith</code>
<code><b>startsWith(<i>&lt;string&gt;</i> : string, <i>&lt;substring to check&gt;</i> : string) => boolean</b></code><br/><br/>
Zkontroluje, jestli řetězec začíná zadaným řetězcem.* ``startsWith('great', 'gr') -> true``
___
### <code>stddev</code>
<code><b>stddev(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Získá směrodatnou odchylku sloupce.* ``stdDev(sales) -> 122.12``
___
### <code>stddevIf</code>
<code><b>stddevIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií získá směrodatnou odchylku sloupce.* ``stddevIf(region == 'West', sales) -> 122.12``
___
### <code>stddevPopulation</code>
<code><b>stddevPopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Získá směrodatnou odchylku základního souboru sloupce.* ``stddevPopulation(sales) -> 122.12``
___
### <code>stddevPopulationIf</code>
<code><b>stddevPopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií získá směrodatnou odchylku základního souboru sloupce.* ``stddevPopulationIf(region == 'West', sales) -> 122.12``
___
### <code>stddevSample</code>
<code><b>stddevSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Získá základní směrodatnou odchylku sloupce.* ``stddevSample(sales) -> 122.12``
___
### <code>stddevSampleIf</code>
<code><b>stddevSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií získá vzorovou směrodatnou odchylku sloupce.* ``stddevSampleIf(region == 'West', sales) -> 122.12``
___
### <code>subDays</code>
<code><b>subDays(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;days to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Odečíst měsíce od data. Stejné jako operátor-operator pro datum* ``subDays(toDate('2016-08-08'), 1) -> 2016-08-09``
___
### <code>subMonths</code>
<code><b>subMonths(<i>&lt;date/timestamp&gt;</i> : datetime, <i>&lt;months to subtract&gt;</i> : integral) => datetime</b></code><br/><br/>
Odečíst měsíce od data nebo časového razítka* ``subMonths(toDate('2016-09-30'), 1) -> 2016-08-31``
___
### <code>substring</code>
<code><b>substring(<i>&lt;string to subset&gt;</i> : string, <i>&lt;from 1-based index&gt;</i> : integral, [<i>&lt;number of characters&gt;</i> : integral]) => string</b></code><br/><br/>
Extrahuje podřetězec určité délky z pozice. Pozice je na 1. Pokud je délka vynechána, je nastavena na konec řetězce.* ``substring('Cat in the hat', 5, 2) -> 'in'``
* ``substring('Cat in the hat', 5, 100) -> 'in the hat'``
* ``substring('Cat in the hat', 5) -> 'in the hat'``
* ``substring('Cat in the hat', 100, 100) -> ''``
___
### <code>sum</code>
<code><b>sum(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Získá agregovaný součet číselného sloupce.* ``sum(col) -> value``
___
### <code>sumDistinct</code>
<code><b>sumDistinct(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Získá agregovaný součet jedinečných hodnot číselného sloupce.* ``sumDistinct(col) -> value``
___
### <code>sumDistinctIf</code>
<code><b>sumDistinctIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Na základě kritérií získá agregační součet číselného sloupce. Podmínka může být založená na jakémkoli sloupci.* ``sumDistinctIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumDistinctIf(true, sales) -> SUM(sales)``
___
### <code>sumIf</code>
<code><b>sumIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => number</b></code><br/><br/>
Na základě kritérií získá agregační součet číselného sloupce. Podmínka může být založená na jakémkoli sloupci.* ``sumIf(state == 'CA' && commission < 10000, sales) -> value``
* ``sumIf(true, sales) -> SUM(sales)``
___
### <code>tan</code>
<code><b>tan(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá tečnou hodnotu.* ``tan(0) -> 0.0``
___
### <code>tanh</code>
<code><b>tanh(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Vypočítá hodnotu hyperbolický tangens.* ``tanh(0) -> 0.0``
___
### <code>toBoolean</code>
<code><b>toBoolean(<i>&lt;value1&gt;</i> : string) => boolean</b></code><br/><br/>
Převede hodnotu (t ', ' true ', ' y ', ' yes ', ' 1 ') na true a (' f ', ' false ', ' n ', ' ne ', ' 0 ') na hodnotu false a hodnotu NULL pro jakoukoliv jinou hodnotu.* ``toBoolean('true') -> true``
* ``toBoolean('n') -> false``
* ``toBoolean('truthy') -> NULL``
___
### <code>toDate</code>
<code><b>toDate(<i>&lt;string&gt;</i> : any, [<i>&lt;date format&gt;</i> : string]) => date</b></code><br/><br/>
Převede řetězec na datum předaného volitelného formátu data. Všechny možné formáty najdete v Java SimpleDateFormat. Pokud je formát data vynechán, jsou přijaty kombinace následujících kombinací. [ yyyy, yyyy-[M]M, yyyy-[M]M-[d]d, yyyy-[M]M-[d]d, yyyy-[M]M-[d]d, yyyy-[M]M-[d]dT* ] * ``toDate('2012-8-8') -> 2012-8-8``
* ``toDate('12/12/2012', 'MM/dd/yyyy') -> 2012-12-12``
___
### <code>toDecimal</code>
<code><b>toDecimal(<i>&lt;value&gt;</i> : any, [<i>&lt;precision&gt;</i> : integral], [<i>&lt;scale&gt;</i> : integral], [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => decimal(10,0)</b></code><br/><br/>
Převede libovolný číselný řetězec nebo řetězec na desítkovou hodnotu. Pokud není Zadaná přesnost a škálování, je nastavená na (10, 2). Pro převod lze použít volitelný formát desítkového čísla Java. Volitelný formát národního prostředí ve formátu jazyka BCP47, jako je en-US, de, zh-CN* ``toDecimal(123.45) -> 123.45``
* ``toDecimal('123.45', 8, 4) -> 123.4500``
* ``toDecimal('$123.45', 8, 4,'$###.00') -> 123.4500``
* ``toDecimal('Ç123,45', 10, 2, 'Ç###,##', 'de') -> 123.45``
___
### <code>toDouble</code>
<code><b>toDouble(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => double</b></code><br/><br/>
Převede libovolný číselný řetězec nebo řetězec na hodnotu Double. Pro převod lze použít volitelný formát desítkového čísla Java. Volitelný formát národního prostředí ve formátu jazyka BCP47, jako je en-US, de, zh-CN* ``toDouble(123.45) -> 123.45``
* ``toDouble('123.45') -> 123.45``
* ``toDouble('$123.45', '$###.00') -> 123.45``
* ``toDouble('Ç123,45', 'Ç###,##', 'de') -> 123.45``
___
### <code>toFloat</code>
<code><b>toFloat(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => float</b></code><br/><br/>
Převede libovolný číselný řetězec nebo řetězec na hodnotu float. Pro převod lze použít volitelný formát desítkového čísla Java. Zkrátí všechna Dvojitá* ``toFloat(123.45) -> 123.45``
* ``toFloat('123.45') -> 123.45``
* ``toFloat('$123.45', '$###.00') -> 123.45``
___
### <code>toInteger</code>
<code><b>toInteger(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => integer</b></code><br/><br/>
Převede libovolný číselný řetězec nebo řetězec na celočíselnou hodnotu. Pro převod lze použít volitelný formát desítkového čísla Java. Zkrátí všechna dlouhá, float, Double.* ``toInteger(123) -> 123``
* ``toInteger('123') -> 123``
* ``toInteger('$123', '$###') -> 123``
___
### <code>toLong</code>
<code><b>toLong(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => long</b></code><br/><br/>
Převede libovolný číselný řetězec nebo řetězec na dlouhou hodnotu. Pro převod lze použít volitelný formát desítkového čísla Java. Zkrátí libovolný float, Double* ``toLong(123) -> 123``
* ``toLong('123') -> 123``
* ``toLong('$123', '$###') -> 123``
___
### <code>toShort</code>
<code><b>toShort(<i>&lt;value&gt;</i> : any, [<i>&lt;format&gt;</i> : string], [<i>&lt;locale&gt;</i> : string]) => short</b></code><br/><br/>
Převede libovolný číselný řetězec nebo řetězec na krátkou hodnotu. Pro převod lze použít volitelný formát desítkového čísla Java. Zkrátí celé číslo, Long, float, Double.* ``toShort(123) -> 123``
* ``toShort('123') -> 123``
* ``toShort('$123', '$###') -> 123``
___
### <code>toString</code>
<code><b>toString(<i>&lt;value&gt;</i> : any, [<i>&lt;number format/date format&gt;</i> : string]) => string</b></code><br/><br/>
Převede primitivní datový typ na řetězec. Pro čísla a datum, kdy lze zadat formát. Je-li tento parametr zadán, je vybrána výchozí hodnota systému. Pro čísla se používá desítkový formát Java. Všechny možné formáty data najdete v Java SimpleDateFormat. výchozí formát je RRRR-MM-DD.* ``toString(10) -> '10'``
* ``toString('engineer') -> 'engineer'``
* ``toString(123456.789, '##,###.##') -> '123,456.79'``
* ``toString(123.78, '000000.000') -> '000123.780'``
* ``toString(12345, '##0.#####E0') -> '12.345E3'``
* ``toString(toDate('2018-12-31')) -> '2018-12-31'``
* ``toString(toDate('2018-12-31'), 'MM/dd/yy') -> '12/31/18'``
* ``toString(4 == 20) -> 'false'``
___
### <code>toTimestamp</code>
<code><b>toTimestamp(<i>&lt;string&gt;</i> : any, [<i>&lt;timestamp format&gt;</i> : string], [<i>&lt;time zone&gt;</i> : string]) => timestamp</b></code><br/><br/>
Převede řetězec na datum předaného volitelného formátu časového razítka. Všechny možné formáty najdete v Java SimpleDateFormat. Pokud je v časovém razítku vynechán výchozí vzor yyyy-[M] M-[d] d: mm: ss [. f...], je použita hodnota.* ``toTimestamp('2016-12-31 00:12:00') -> 2012-8-8T00:12:00``
* ``toTimestamp('2016/12/31T00:12:00', 'MM/dd/yyyyThh:mm:ss') -> 2012-12-12T00:12:00``
___
### <code>toUTC</code>
<code><b>toUTC(<i>&lt;value1&gt;</i> : timestamp, [<i>&lt;value2&gt;</i> : string]) => timestamp</b></code><br/><br/>
Převede časové razítko na UTC. Můžete předat volitelné časové pásmo ve formátu "GMT", "PST", "UTC", "Amerika/Kajmanské". Ve výchozím nastavení je nastavené na aktuální časové pásmo.* ``toUTC(currentTimeStamp()) -> 12-12-2030T19:18:12``
* ``toUTC(currentTimeStamp(), 'Asia/Seoul') -> 12-13-2030T11:18:12``
___
### <code>translate</code>
<code><b>translate(<i>&lt;string to translate&gt;</i> : string, <i>&lt;lookup characters&gt;</i> : string, <i>&lt;replace characters&gt;</i> : string) => string</b></code><br/><br/>
Nahraďte jednu sadu znaků jinou sadou znaků v řetězci. Znaky mají 1 – 1 náhradu.* ``translate('(Hello)', '()', '[]') -> '[Hello]'``
* ``translate('(Hello)', '()', '[') -> '[Hello'``
___
### <code>trim</code>
<code><b>trim(<i>&lt;string to trim&gt;</i> : string, [<i>&lt;trim characters&gt;</i> : string]) => string</b></code><br/><br/>
Ořízne řetězec úvodních a koncových znaků. Pokud není zadán druhý parametr, ořízne prázdné znaky. Jinak ořízne libovolný znak zadaný ve druhém parametru.* ``trim('!--!wor!ld!', '-!') -> 'wor!ld'``
___
### <code>true</code>
<code><b>true() => boolean</b></code><br/><br/>
Vždy vrátí hodnotu true. Použijte syntaxi funkce (true ()), pokud je sloupec s názvem true.* ``isDiscounted == true()``
* ``isDiscounted() == true``
___
### <code>typeMatch</code>
<code><b>typeMatch(<i>&lt;type&gt;</i> : string, <i>&lt;base type&gt;</i> : string) => boolean</b></code><br/><br/>
Odpovídá typu sloupce. Dá se použít jenom ve výrazech vzoru. číslo odpovídá krátkému, celému číslu, dlouhému, dvojitému, floatu nebo desetinnému číslu, celé číslo odpovídá krátkému, celému číslu, dlouhému a zlomku odpovídá typu datum nebo časové razítko.* ``typeMatch(type, 'number') -> true``
* ``typeMatch('date', 'number') -> false``
___
### <code>upper</code>
<code><b>upper(<i>&lt;value1&gt;</i> : string) => string</b></code><br/><br/>
Velká písmena řetězce* ``upper('bojjus') -> 'BOJJUS'``
___
### <code>variance</code>
<code><b>variance(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Získá odchylku sloupce.* ``variance(sales) -> 122.12``
___
### <code>varianceIf</code>
<code><b>varianceIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií získá odchylku sloupce.* ``varianceIf(region == 'West', sales) -> 122.12``
___
### <code>variancePopulation</code>
<code><b>variancePopulation(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Získá odchylku populace sloupce.* ``variancePopulation(sales) -> 122.12``
___
### <code>variancePopulationIf</code>
<code><b>variancePopulationIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií získá odchylku populace sloupce.* ``variancePopulationIf(region == 'West', sales) -> 122.12``
___
### <code>varianceSample</code>
<code><b>varianceSample(<i>&lt;value1&gt;</i> : number) => double</b></code><br/><br/>
Načte neposunutou odchylku sloupce.* ``varianceSample(sales) -> 122.12``
___
### <code>varianceSampleIf</code>
<code><b>varianceSampleIf(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : number) => double</b></code><br/><br/>
Na základě kritérií získá neposunutou odchylku sloupce.* ``varianceSampleIf(region == 'West', sales) -> 122.12``
___
### <code>weekOfYear</code>
<code><b>weekOfYear(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Získá týden v roce s daným datem.* ``weekOfYear(toDate('2008-02-20')) -> 8``
___
### <code>xor</code>
<code><b>xor(<i>&lt;value1&gt;</i> : boolean, <i>&lt;value2&gt;</i> : boolean) => boolean</b></code><br/><br/>
Logický operátor XOR Stejné jako ^ operátor* ``xor(true, false) -> true``
* ``xor(true, true) -> false``
* ``true ^ false -> true``
___
### <code>year</code>
<code><b>year(<i>&lt;value1&gt;</i> : datetime) => integer</b></code><br/><br/>
Získá hodnotu roku pro datum.* ``year(toDate('2012-8-8')) -> 2012``

## <a name="next-steps"></a>Další postup

[Naučte se používat Tvůrce výrazů](concepts-data-flow-expression-builder.md).
