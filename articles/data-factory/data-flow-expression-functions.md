---
title: Funkce výrazu v funkci toku dat mapování Azure Data Factory
description: Přečtěte si o funkcích výrazů v mapování toku dat.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/15/2019
ms.openlocfilehash: c062a75516a1b865c1ff6c35f00d4fbf7c4881c6
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029372"
---
# <a name="data-transformation-expressions-in-mapping-data-flow"></a>Výrazy transformace dat v toku mapování dat 



## <a name="expression-functions"></a>Funkce výrazu

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
Vypočítá inverzní hodnotu kosinus * ``acos(1) -> 0.0`` @ no__t-1 @ no__t-2.<br/><br/>
Přidá dvojici řetězců nebo čísel. Přidá datum do počtu dní. Přidá dobu trvání do časového razítka. Připojí jedno pole podobného typu k druhému. Stejné jako operátor + * ``add(10, 20) -> 30`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9<br/><br/>
Přidejte dny k datu nebo časovému razítku. Stejné jako operátor + pro datum * ``addDays(toDate('2016-08-08'), 1) -> toDate('2016-08-09')`` @ no__t-1 @ no__t-2<br/><br/>
Přidejte měsíce k datu nebo časovému razítku. Můžete volitelně předat časové pásmo * ``addMonths(toDate('2016-08-31'), 1) -> toDate('2016-09-30')`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Logický operátor AND Stejné jako & & * ``and(true, false) -> false`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Vypočítá inverzní hodnotu sinus * ``asin(0) -> 0.0`` @ no__t-1 @ no__t-2.<br/><br/>
Vypočítá hodnotu inverzní tangens * ``atan(0) -> 0.0`` @ no__t-1 @ no__t-2.<br/><br/>
Vrátí úhel v radiánech mezi kladnou osou x rovinou a bodem zadaným souřadnicemi * ``atan2(0, 0) -> 0.0`` @ no__t-1 @ no__t-2.<br/><br/>
Získá průměr hodnot sloupce * ``avg(sales)`` @ no__t-1 @ no__t-2.<br/><br/>
Na základě kritérií získá průměr hodnot sloupce * ``avgIf(region == 'West', sales)`` @ no__t-1 @ no__t-2.<br/><br/>
Vybere hodnotu sloupce podle názvu v datovém proudu. Jako druhý argument můžete předat volitelný název datového proudu. Pokud existuje více shod, je vrácena první shoda. Pokud žádná shoda není, vrátí hodnotu NULL. Vrácená hodnota musí být typu převedený jednou z funkcí pro převod typu (TO_DATE, TO_STRING...).  Názvy sloupců, které jsou známy v době návrhu, by měly být řešeny stejným názvem. Počítané vstupy se nepodporují, ale můžete použít náhrady parametrů * ``toString(byName('parent'))`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7<br/><br/>
Vybere hodnotu sloupce podle relativní pozice (na základě 1) v datovém proudu. Pokud je pozice mimo rozsah, vrátí hodnotu NULL. Vrácená hodnota musí být typu převedený jednou z funkcí pro převod typu (TO_DATE, TO_STRING...). Počítané vstupy se nepodporují, ale můžete použít náhrady parametrů * ``toString(byPosition(1))`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
Na základě střídajících se podmínek používá jedna hodnota nebo druhá. Pokud je počet vstupů sudý, druhý je pro poslední podmínku nastaven na hodnotu NULL * ``case(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Vypočítat kořenovou složku datové krychle s číslem * ``cbrt(8) -> 2.0`` @ no__t-1 @ no__t-2<br/><br/>
Vrátí nejmenší celé číslo, které není menší než číslo * ``ceil(-0.1) -> 0`` @ no__t-1 @ no__t-2.<br/><br/>
Vrátí první nenulovou hodnotu ze sady vstupů. Všechny vstupy by měly být stejného typu * ``coalesce(10, 20) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Porovná dvě hodnoty stejného typu. Vrátí záporné celé číslo, pokud hodnota1 < hodnota2, 0 if hodnota1 = = hodnota2, kladné hodnoty if hodnota1 > hodnota2 * ``(compare(12, 24) < 1) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Zřetězí proměnný počet řetězců dohromady. Stejné jako u operátoru + s řetězci * ``concat('dataflow', 'is', 'awesome') -> 'dataflowisawesome'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Zřetězí proměnlivý počet řetězců spolu s oddělovačem. První parametr je oddělovač * ``concatWS(' ', 'dataflow', 'is', 'awesome') -> 'dataflow is awesome'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Vrátí hodnotu true, pokud je libovolný prvek v zadaném poli vyhodnocen jako true v zadaném predikátu. Obsahuje očekává odkaz na jeden prvek ve funkci predikátu as #item * ``contains([1, 2, 3, 4], #item == 3) -> true`` @ no__t-1 @ no__t-2 @ no__t-3.<br/><br/>
Vypočítá hodnotu kosinus * ``cos(10) -> -0.8390715290764524`` @ no__t-1 @ no__t-2.<br/><br/>
Vypočítá hyperbolický kosinus hodnoty * ``cosh(0) -> 1.0`` @ no__t-1 @ no__t-2.<br/><br/>
Získá agregovaný počet hodnot. Pokud jsou zadány volitelné sloupce, ignorují se hodnoty NULL v Count * ``count(custId)`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5.<br/><br/>
Získá agregovaný počet jedinečných hodnot pro sadu sloupců * ``countDistinct(custId, custName)`` @ no__t-1 @ no__t-2.<br/><br/>
Na základě kritérií získá agregovaný počet hodnot. Pokud je zadán volitelný sloupec, ignoruje hodnoty NULL v Count * ``countIf(state == 'CA' && commission < 10000, name)`` @ no__t-1 @ no__t-2.<br/><br/>
Získá kovarianci populace mezi dvěma sloupci * ``covariancePopulation(sales, profit)`` @ no__t-1 @ no__t-2.<br/><br/>
Na základě kritérií získá kovarianci populace dvou sloupců * ``covariancePopulationIf(region == 'West', sales)`` @ no__t-1 @ no__t-2.<br/><br/>
Získá ukázkovou odchylku dvou sloupců * ``covarianceSample(sales, profit)`` @ no__t-1 @ no__t-2.<br/><br/>
Na základě kritérií Získá vzorek kovariance dvou sloupců * ``covarianceSampleIf(region == 'West', sales, profit)`` @ no__t-1 @ no__t-2.<br/><br/>
Vypočítá hodnotu hash CRC32 sady sloupců různých primitivních DataTypes dané bitové délky, která může být pouze z hodnot 0 (256), 224, 256, 384, 512. Dá se použít k výpočtu otisku prstu pro řádek * ``crc32(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 3630253689L`` @ no__t-1 @ no__t-2<br/><br/>
Funkce CumeDist vypočítá pozici hodnoty relativně ke všem hodnotám v oddílu. Výsledkem je počet řádků předcházejících nebo rovných aktuálnímu řádku v pořadí oddílu dělený celkovým počtem řádků v oddílu okna. Všechny hodnoty propojení v řazení se vyhodnotí na stejnou pozici.
* ``cumeDist()``
___
### <code>currentDate</code>
<code><b>currentDate([<i>&lt;value1&gt;</i> : string]) => date</b></code><br/><br/>
Získá aktuální datum spuštění této úlohy. Můžete předat volitelné časové pásmo ve formátu "GMT", "PST", "UTC", "Amerika/Kajmanské". Místní časové pásmo se používá jako výchozí. Pro dostupné formáty se podívejte na SimpleDateFormat Java. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``currentDate() == toDate('2250-12-31') -> false`` @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Získá aktuální časové razítko, kdy se úloha začne spouštět s místním časovým pásmem * ``currentTimestamp() == toTimestamp('2250-12-31 12:12:12') -> false`` @ no__t-1 @ no__t-2.<br/><br/>
Získá aktuální časové razítko jako UTC. Pokud chcete, aby byl aktuální čas interpretován v jiném časovém pásmu než časové pásmo clusteru, můžete předat volitelné časové pásmo ve formátu "GMT", "PST", "UTC", "Amerika/Kajmanské". Ve výchozím nastavení je nastavené na aktuální časové pásmo. Pro dostupné formáty se podívejte na SimpleDateFormat Java. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.htmlTo převede čas UTC na jiné časové pásmo. použijte fromUTC () * ``currentUTC() == toTimestamp('2050-12-12 19:18:12') -> false`` @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5.<br/><br/>
Vrátí den v měsíci, který má dané datum * ``dayOfMonth(toDate('2018-06-08')) -> 8`` @ no__t-1 @ no__t-2.<br/><br/>
Vrátí den v týdnu daného data. 1 – neděle, 2-pondělí..., 7 – sobota * ``dayOfWeek(toDate('2018-06-08')) -> 6`` @ no__t-1 @ no__t-2<br/><br/>
Vrátí den v roce, který je zadaný jako datum * ``dayOfYear(toDate('2016-04-09')) -> 100`` @ no__t-1 @ no__t-2.<br/><br/>
Doba trvání v milisekundách v počtu dní * ``days(2) -> 172800000L`` @ no__t-1 @ no__t-2<br/><br/>
Převede radiány na stupně * ``degrees(3.141592653589793) -> 180`` @ no__t-1 @ no__t-2.<br/><br/>
Vypočítá pořadí hodnoty ve skupině hodnot. Výsledkem je jedna hodnota plus počet řádků předcházejících nebo rovných aktuálnímu řádku v pořadí oddílu. Hodnoty nebudou v sekvenci vydávat žádné mezery. Zhuštěné řazení funguje i v případě, že data nejsou seřazená a vyhledávají změny v hodnotách * ``denseRank(salesQtr, salesAmt)`` @ no__t-1 @ no__t-2.<br/><br/>
Vydělí dvojici čísel. Stejné jako operátor/* ``divide(20, 10) -> 2`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Zkontroluje, jestli řetězec končí zadaným řetězcem * ``endsWith('dumbo', 'mbo') -> true`` @ no__t-1 @ no__t-2.<br/><br/>
Operátor porovnání je rovno. Stejné jako = = operator * ``equals(12, 24) -> false`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
Porovnání se rovná operátoru bez ohledu na velikost písmen. Totéž jako < = > operátor * ``'abc'<=>'Abc' -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Vypočítat faktoriál čísla * ``factorial(5) -> 120`` @ no__t-1 @ no__t-2<br/><br/>
Vždy vrátí hodnotu false. Použijte syntaxi funkce (false ()), pokud je sloupec s názvem "false" * ``(10 + 20 > 30) -> false`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Filtruje prvky z pole, které nesplňuje zadaný predikát. Filtr očekává odkaz na jeden prvek ve funkci predikátu as #item * ``filter([1, 2, 3, 4], #item > 2) -> [3, 4]`` @ no__t-1 @ no__t-2 @ no__t-3.<br/><br/>
Získá první hodnotu skupiny sloupců. Pokud je vynechán druhý parametr ignoreNulls, předpokládá se hodnota false * ``first(sales)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Vrátí největší celé číslo, které není větší než číslo * ``floor(-0.1) -> -1`` @ no__t-1 @ no__t-2.<br/><br/>
Převede na časové razítko od času UTC. Časové pásmo můžete volitelně předat ve formátu "GMT", "PST", "UTC", "Amerika/Kajmanské". Pro dostupné formáty je standardně nastavená na aktuální SimpleDateFormat Java timezoneRefer. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``fromUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Porovnání většího operátoru. Totéž jako operátor > * ``greater(12, 24) -> false`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Porovnání větší než nebo rovno operátoru. Totéž jako > = operator * ``greaterOrEqual(12, 12) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Vrátí nejvyšší hodnotu ze seznamu hodnot jako vstup, který přeskakuje hodnoty null. Vrátí hodnotu null, pokud jsou všechny vstupy null * ``greatest(10, 30, 15, 20) -> 30`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5.<br/><br/>
Kontroluje hodnotu sloupce podle názvu v datovém proudu. Jako druhý argument můžete předat volitelný název datového proudu.  Názvy sloupců, které jsou známy v době návrhu, by měly být řešeny stejným názvem. Počítané vstupy se nepodporují, ale můžete použít náhrady parametrů * ``hasColumn('parent')`` @ no__t-1 @ no__t-2.<br/><br/>
Získá hodnotu hodiny časového razítka. Můžete předat volitelné časové pásmo ve formátu "GMT", "PST", "UTC", "Amerika/Kajmanské". Místní časové pásmo se používá jako výchozí. Pro dostupné formáty se podívejte na SimpleDateFormat Java. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``hour(toTimestamp('2009-07-30 12:58:59')) -> 12`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Doba trvání v milisekundách pro počet hodin * ``hours(2) -> 7200000L`` @ no__t-1 @ no__t-2<br/><br/>
Mapuje každý prvek pole na nový prvek pomocí poskytnutého výrazu. Mapa očekává odkaz na jeden prvek ve funkci Expression jako #item a odkaz na index elementu jako #index * ``iMap([1, 2, 3, 4], #item + 2 + #index) -> [4, 6, 8, 10]`` @ no__t-1 @ no__t-2.<br/><br/>
Na základě podmínky platí jedna nebo druhá hodnota. Pokud není uvedeno jinak, považuje se za NULL. Obě hodnoty musí být kompatibilní (číselná, String...) * ``iif(10 + 20 == 30, 'dumbo', 'gumbo') -> 'dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Zkontroluje, zda hodnota není NULL a vrátí hodnotu else, jinak vrátí jinou hodnotu. Testuje všechny vstupy, dokud nenajde první hodnotu, která není null, * ``iifNull(10, 20) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Zkontroluje, jestli je položka v poli * ``in([10, 20, 30], 10) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Převede první písmeno každého slova na velká. Slova jsou označená jako oddělená prázdným znakem * ``initCap('cool iceCREAM') -> 'Cool Icecream'`` @ no__t-1 @ no__t-2.<br/><br/>
Vyhledá pozici (na základě 1) podřetězce v rámci řetězce. 0 se vrátí, pokud se nenajde * ``instr('dumbo', 'mbo') -> 3`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Zkontroluje, jestli je řádek označený k odstranění. Pro transformace s více než jedním vstupním datovým proudem můžete index (založený na 1) předat tomuto datovému proudu. Výchozí hodnota indexu streamu je 1 * ``isDelete()`` @ no__t-1 @ no__t-2 @ no__t-3.<br/><br/>
Zkontroluje, jestli je řádek označený jako chyba. Pro transformace s více než jedním vstupním datovým proudem můžete index (založený na 1) předat tomuto datovému proudu. Výchozí hodnota indexu streamu je 1 * ``isError()`` @ no__t-1 @ no__t-2 @ no__t-3.<br/><br/>
Zkontroluje, jestli je řádek označený jako ignorovaný. Pro transformace s více než jedním vstupním datovým proudem můžete index (založený na 1) předat tomuto datovému proudu. Výchozí hodnota indexu streamu je 1 * ``isIgnore()`` @ no__t-1 @ no__t-2 @ no__t-3.<br/><br/>
Zkontroluje, jestli je řádek označený pro vložení. Pro transformace s více než jedním vstupním datovým proudem můžete index (založený na 1) předat tomuto datovému proudu. Výchozí hodnota indexu streamu je 1 * ``isInsert()`` @ no__t-1 @ no__t-2 @ no__t-3.<br/><br/>
Zkontroluje, jestli se řádek při vyhledávání shodoval. Pro transformace s více než jedním vstupním datovým proudem můžete index (založený na 1) předat tomuto datovému proudu. Výchozí hodnota indexu streamu je 1 * ``isMatch()`` @ no__t-1 @ no__t-2 @ no__t-3.<br/><br/>
Zkontroluje, jestli je hodnota NULL * ``isNull(NULL()) -> true`` @ no__t-1 @ no__t-2 @ no__t-3.<br/><br/>
Zkontroluje, jestli je řádek označený k aktualizaci. Pro transformace s více než jedním vstupním datovým proudem můžete index (založený na 1) předat tomuto datovému proudu. Výchozí hodnota indexu streamu je 1 * ``isUpdate()`` @ no__t-1 @ no__t-2 @ no__t-3.<br/><br/>
Zkontroluje, jestli je řádek označený pro vložení. Pro transformace s více než jedním vstupním datovým proudem můžete index (založený na 1) předat tomuto datovému proudu. Výchozí hodnota indexu streamu je 1 * ``isUpsert()`` @ no__t-1 @ no__t-2 @ no__t-3.<br/><br/>
Získá špičatost sloupce * ``kurtosis(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Na základě kritérií získá špičatost sloupce * ``kurtosisIf(region == 'West', sales)`` @ no__t-1 @ no__t-2.<br/><br/>
Získá hodnotu prvního parametru vyhodnoceného n řádků před aktuálním řádkem. Druhým parametrem je počet řádků, které se mají vyhledat, a výchozí hodnota je 1. Pokud není k dispozici tolik řádků, je vrácena hodnota null, pokud není zadána výchozí hodnota * ``lag(amount, 2)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Načte poslední hodnotu skupiny sloupců. Pokud je vynechán druhý parametr ignoreNulls, předpokládá se hodnota false * ``last(sales)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Získá poslední datum v měsíci za zadané datum * ``lastDayOfMonth(toDate('2009-01-12')) -> toDate('2009-01-31')`` @ no__t-1 @ no__t-2.<br/><br/>
Získá hodnotu prvního parametru vyhodnoceného n řádků za aktuálním řádkem. Druhým parametrem je počet řádků, které se mají hledat, a výchozí hodnota je 1. Pokud není k dispozici tolik řádků, je vrácena hodnota null, pokud není zadána výchozí hodnota * ``lead(amount, 2)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Porovnání menší než nebo rovno operátoru. Totéž jako < = operator * ``least(10, 30, 15, 20) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Extrahuje podřetězec začínající na indexu 1 s počtem znaků. Stejné jako podřetězec (str, 1, n) * ``left('bojjus', 2) -> 'bo'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Vrátí délku řetězce * ``length('dumbo') -> 5`` @ no__t-1 @ no__t-2.<br/><br/>
Operátor porovnání less. Totéž jako operátor < * ``lesser(12, 24) -> true`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Porovnání menší než nebo rovno operátoru. Totéž jako < = operator * ``lesserOrEqual(12, 12) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Získá levenshtein vzdálenost mezi dvěma řetězci * ``levenshtein('boys', 'girls') -> 4`` @ no__t-1 @ no__t-2.<br/><br/>
Vzor je řetězec, který se bude shodovat s doslova. Výjimky jsou následující speciální symboly: _ odpovídá jakémukoli jednomu znaku ve vstupu (podobně jako. v regulárních výrazech POSIX)% odpovídá nule nebo více znakům ve vstupu (podobně jako. * v regulárních výrazech POSIX).
Řídicí znak je ' '. Pokud řídicí znak před speciálním symbolem nebo jiným řídicím znakem, je následující znak porovnán. Řídicí znak není v žádném jiném znaku neplatný.
* ``like('icecream', 'ice%') -> true``
___
### <code>locate</code>
<code><b>locate(<i>&lt;substring to find&gt;</i> : string, <i>&lt;string&gt;</i> : string, [<i>&lt;from index - 1-based&gt;</i> : integral]) => integer</b></code><br/><br/>
Vyhledá pozici (na základě 1) podřetězce v řetězci, který spouští určitou pozici. Pokud je pozice vynechána, je považována za začátek řetězce. 0 se vrátí, pokud se nenajde * ``locate('mbo', 'dumbo') -> 3`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Vypočítá hodnotu protokolu. Nepovinná základna se dá zadat jinak než Eulerova číslo, pokud se používá * ``log(100, 10) -> 2`` @ no__t-1 @ no__t-2.<br/><br/>
Vypočítá hodnotu protokolu založenou na 10 základních * ``log10(100) -> 2`` @ no__t-1 @ no__t-2.<br/><br/>
Malá písmena v řetězci * ``lower('GunChus') -> 'gunchus'`` @ no__t-1 @ no__t-2<br/><br/>
Vlevo vyplní řetězec doplněním zadaného odsazení, dokud nemá určitou délku. Pokud je řetězec větší nebo roven délce, pak se ořízne na délku * ``lpad('dumbo', 10, '-') -> '-----dumbo'`` @ no__t-1 @ no__t-2 ' ' Lpad (' Dumbo ', 8, ' < > ')-> ' < > <dumbo'``
___
### <code>ltrim @ no__t-4 @ no__t-5<br/><br/>
Levá ořízne řetězec úvodních znaků. Pokud není zadán druhý parametr, ořízne prázdné znaky. Jinak ořízne libovolný znak zadaný ve druhém parametru * ``ltrim('  dumbo  ') -> 'dumbo  '`` @ no__t-1 @ no__t-2 @ no__t-3.<br/><br/>
Mapuje každý prvek pole na nový prvek pomocí poskytnutého výrazu. Mapa očekává odkaz na jeden prvek ve funkci Expression jako #item * ``map([1, 2, 3, 4], #item + 2) -> [3, 4, 5, 6]`` @ no__t-1 @ no__t-2 @ no__t-3.<br/><br/>
Získá maximální hodnotu sloupce * ``max(sales)`` @ no__t-1 @ no__t-2.<br/><br/>
Na základě kritérií získá maximální hodnotu sloupce * ``maxIf(region == 'West', sales)`` @ no__t-1 @ no__t-2.<br/><br/>
Vypočítá výtah MD5 sady sloupců různých primitivních typů DataTypes a vrátí šestnáctkový řetězec 32 znaků. Dá se použít k výpočtu otisku prstu pro řádek * ``md5(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '4ce8a880bd621a1ffad0bca905e1bc5a'`` @ no__t-1 @ no__t-2<br/><br/>
Získá průměrnou hodnotu sloupce. Stejné jako prům * ``mean(sales)`` @ no__t-1 @ no__t-2<br/><br/>
Na základě kritérií získá průměrnou hodnotu sloupce. Stejné jako avgIf * ``meanIf(region == 'West', sales)`` @ no__t-1 @ no__t-2<br/><br/>
Získá hodnotu milisekundy pro datum. Můžete předat volitelné časové pásmo ve formátu "GMT", "PST", "UTC", "Amerika/Kajmanské". Místní časové pásmo se používá jako výchozí. Pro dostupné formáty se podívejte na SimpleDateFormat Java. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``millisecond(toTimestamp('2009-07-30 12:58:59.871', 'yyyy-MM-dd HH:mm:ss.SSS')) -> 871`` @ no__t-2 @ no__t-3<br/><br/>
Doba trvání v milisekundách pro počet milisekund * ``seconds(2) -> 2L`` @ no__t-1 @ no__t-2<br/><br/>
Získá minimální hodnotu sloupce * ``min(sales)`` @ no__t-1 @ no__t-2.<br/><br/>
Na základě kritérií získá minimální hodnotu sloupce * ``minIf(region == 'West', sales)`` @ no__t-1 @ no__t-2.<br/><br/>
Odečte čísla. Odečte se od počtu dní od data. Substract trvání z časového razítka. Substract dvě časová razítka k získání rozdílů v milisekundách. Stejné jako operátor-operator * ``minus(20, 10) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7<br/><br/>
Získá hodnotu minuty časového razítka. Můžete předat volitelné časové pásmo ve formátu "GMT", "PST", "UTC", "Amerika/Kajmanské". Místní časové pásmo se používá jako výchozí. Pro dostupné formáty se podívejte na SimpleDateFormat Java. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``minute(toTimestamp('2009-07-30 12:58:59')) -> 58`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Doba trvání v milisekundách pro počet minut * ``minutes(2) -> 120000L`` @ no__t-1 @ no__t-2<br/><br/>
Zbytek páru čísel. Stejné jako% operator * ``mod(20, 8) -> 4`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Vrátí hodnotu měsíce data nebo časového razítka * ``month(toDate('2012-8-8')) -> 8`` @ no__t-1 @ no__t-2.<br/><br/>
Vrátí počet měsíců mezi dvěma kalendářními daty. Výpočet můžete zaokrouhlit. Můžete předat volitelné časové pásmo ve formátu "GMT", "PST", "UTC", "Amerika/Kajmanské". Místní časové pásmo se používá jako výchozí. Pro dostupné formáty se podívejte na SimpleDateFormat Java. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``monthsBetween(toTimestamp('1997-02-28 10:30:00'), toDate('1996-10-30')) -> 3.94959677`` @ no__t-2 @ no__t-3<br/><br/>
Vynásobí dvojici čísel. Stejné jako u operátoru * * ``multiply(20, 10) -> 200`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Funkce NTile rozdělí řádky pro každý oddíl okna do intervalů `n` v rozsahu od 1 do maximálně `n`. Hodnoty plechovky se budou lišit o nejvýše 1. Pokud počet řádků v oddílu není rovnoměrně rozdělen do počtu intervalů, pak jsou zbývající hodnoty distribuovány po jednotlivých kontejnerech počínaje prvním intervalem. Funkce NTile je užitečná pro výpočet tertiles, Kvartily, deciles a dalších běžných souhrnných statistik. Funkce vypočítá dvě proměnné během inicializace: velikost regulárního intervalu bude mít přidán jeden řádek navíc. Obě proměnné jsou založené na velikosti aktuálního oddílu. Během procesu výpočtu funkce udržuje sledovat aktuální číslo řádku, aktuální číslo sady a číslo řádku, na kterém se bude interval měnit (bucketThreshold). Když číslo aktuálního řádku dosáhne prahové hodnoty intervalu, hodnota intervalu se zvýší o jednu a prahová hodnota se zvýší o velikost intervalu (plus jeden další, pokud je aktuální kontejner doplněn).
* ``nTile()``
* ``nTile(numOfBuckets)``
___
### <code>negate</code>
<code><b>negate(<i>&lt;value1&gt;</i> : number) => number</b></code><br/><br/>
Negace čísla. Změní kladné hodnoty na záporné a naopak * ``negate(13) -> -13`` @ no__t-1 @ no__t-2.<br/><br/>
Vrátí další jedinečnou sekvenci. Číslo je po sobě po sobě pouze v rámci oddílu a má předponu partitionId * ``nextSequence() == 12313112 -> false`` @ no__t-1 @ no__t-2.<br/><br/>
Normalizuje řetězcovou hodnotu pro oddělení akcentů znaků Unicode * ``regexReplace(normalize('bo²s'), `\p{M}`, '') -> 'boys'`` @ no__t-1 @ no__t-2.<br/><br/>
Logický operátor negace * ``not(true) -> false`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Operátor porovnání není rovno. Totéž jako! = – operátor * ``12 != 24 -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Vrací hodnotu NULL. Použijte syntaxi funkce (null ()), pokud je sloupec s názvem null. Výsledkem jakékoli operace, která používá, bude NULL * ``isNull('dumbo' + null) -> true`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
Logický operátor OR Stejné jako | | * ``or(true, false) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Kladné zbytky páru čísel.
* ``pmod(-20, 8) -> 4``
___
### <code>partitionId</code>
<code><b>partitionId() => integer</b></code><br/><br/>
Vrátí aktuální ID oddílu, ve kterém je vstupní řádek * ``partitionId()`` @ no__t-1 @ no__t-2.<br/><br/>
Umocní jedno číslo na mocninu jiného * ``power(10, 2) -> 100`` @ no__t-1 @ no__t-2.<br/><br/>
Vypočítá pořadí hodnoty ve skupině hodnot. Výsledkem je jedna hodnota plus počet řádků předcházejících nebo rovných aktuálnímu řádku v pořadí oddílu. Hodnoty budou v posloupnosti vydávat mezery. Pořadí funguje i v případě, že data nejsou seřazena a vyhledávají změny v hodnotách * ``rank(salesQtr, salesAmt)`` @ no__t-1 @ no__t-2<br/><br/>
Shromáždí prvky v poli. Omezení očekává odkaz na Akumulovaná a jeden element ve funkci First Expression jako #acc a #item a očekává výslednou hodnotu jako #result pro použití ve druhé funkci výrazu * ``reduce([1, 2, 3, 4], 0, #acc + #item, #result) -> 10`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Extrahuje shodný dílčí řetězec pro daný vzor regulárního výrazu. Poslední parametr identifikuje skupinu shod a je-li vynechán, je nastaven na hodnotu 1. Použijte <regex> (back-quot) k vyhledání řetězce bez uvozovacího příkazu * ``regexExtract('Cost is between 600 and 800 dollars', '(\\d+) and (\\d+)', 2) -> '800'`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Zkontroluje, zda řetězec odpovídá danému vzoru regulárního výrazu. Použijte <regex> (back-quot) k vyhledání řetězce bez uvozovacího příkazu * ``regexMatch('200.50', '(\\d+).(\\d+)') -> true`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Nahradí všechny výskyty vzorového regulárního výrazu jiným podřetězcem v daném řetězci. použijte <regex> (back-quot), aby odpovídala řetězci bez uvozovacího * ``regexReplace('100 and 200', '(\\d+)', 'bojjus') -> 'bojjus and bojjus'`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Rozdělí řetězec na základě oddělovače na základě regulárního výrazu a vrátí pole řetězců * ``regexSplit('bojjusAgunchusBdumbo', `[CAB]`) -> ['bojjus', 'gunchus', 'dumbo']`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Nahradí všechny výskyty podřetězce jiným podřetězcem v daném řetězci. Pokud je poslední parametr vynechán, je výchozí hodnotou prázdný řetězec * ``replace('doggie dog', 'dog', 'cat') -> 'catgie cat'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Obrátí řetězec * ``reverse('gunchus') -> 'suhcnug'`` @ no__t-1 @ no__t-2.<br/><br/>
Extrahuje podřetězec s počtem znaků zprava. Totéž jako podřetězec (str, LENGTH (str)-n, n) * ``right('bojjus', 2) -> 'us'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Zkontroluje, jestli řetězec odpovídá danému vzorci regulárního výrazu * ``rlike('200.50', `(\d+).(\d+)`) -> true`` @ no__t-1 @ no__t-2 @ no__t-3.<br/><br/>
Zaokrouhlí číslo z daného volitelného měřítka a volitelného režimu zaokrouhlení. Pokud je měřítko vynecháno, je nastavené na hodnotu 0.  Pokud je tento režim vynechán, je nastaven jako výchozí na ROUND_HALF_UP (5). Mezi hodnoty zaokrouhlení patří 1-ROUND_UP 2-ROUND_DOWN 3-ROUND_CEILING 4-ROUND_FLOOR 5-ROUND_HALF_UP 6-ROUND_HALF_DOWN 7-ROUND_HALF_EVEN 8-ROUND_UNNECESSARY * ``round(100.123) -> 100.0`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Přiřadí pořadové číslování řádků v okně od 1 * ``rowNumber()`` @ no__t-1 @ no__t-2.<br/><br/>
Doplní řetězec doplněním zadaného odsazení, dokud nemá určitou délku. Pokud je řetězec větší nebo roven délce, pak se ořízne na délku * ``rpad('dumbo', 10, '-') -> 'dumbo-----'`` @ no__t-1 @ no__t-2 @ no__t-3rtrim @ no__t-4 @ no__t-5<br/><br/>
Pravá ořízne řetězec úvodních znaků. Pokud není zadán druhý parametr, ořízne prázdné znaky. Jinak ořízne libovolný znak zadaný ve druhém parametru * ``rtrim('  dumbo  ') -> '  dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3.<br/><br/>
Získá druhou hodnotu data. Můžete předat volitelné časové pásmo ve formátu "GMT", "PST", "UTC", "Amerika/Kajmanské". Místní časové pásmo se používá jako výchozí. Pro dostupné formáty se podívejte na SimpleDateFormat Java. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``second(toTimestamp('2009-07-30 12:58:59')) -> 59`` @ no__t-2 @ no__t-3<br/><br/>
Doba trvání v milisekundách v sekundách * ``seconds(2) -> 2000L`` @ no__t-1 @ no__t-2<br/><br/>
Vypočítá hodnotu hash SHA-1 sady sloupců různých primitivních typů DataTypes a vrátí šestnáctkový řetězec 40 znaků. Dá se použít k výpočtu otisku prstu pro řádek * ``sha1(5, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> '46d3b478e8ec4e1f3b453ac3d8e59d5854e282bb'`` @ no__t-1 @ no__t-2<br/><br/>
Vypočítá hodnotu hash SHA-2 sady sloupců různých primitivních datových typů s délkou, která může být pouze z hodnot 0 (256), 224, 256, 384, 512. Dá se použít k výpočtu otisku prstu pro řádek * ``sha2(256, 'gunchus', 8.2, 'bojjus', true, toDate('2010-4-4')) -> 'afe8a553b1761c67d76f8c31ceef7f71b66a1ee6f4e6d3b5478bf68b47d06bd3'`` @ no__t-1 @ no__t-2<br/><br/>
Vypočítá hodnotu sinus * ``sin(2) -> 0.9092974268256817`` @ no__t-1 @ no__t-2.<br/><br/>
Vypočítá hodnotu hyperbolický sinus * ``sinh(0) -> 0.0`` @ no__t-1 @ no__t-2.<br/><br/>
Získá zešikmení sloupce * ``skewness(sales)`` @ no__t-1 @ no__t-2.<br/><br/>
Na základě kritérií získá zešikmení sloupce * ``skewnessIf(region == 'West', sales)`` @ no__t-1 @ no__t-2.<br/><br/>
Extrahuje podmnožinu pole z pozice. Pozice je na 1. Pokud je délka vynechána, je nastavena na konec řetězce * ``slice([10, 20, 30, 40], 1, 2) -> [10, 20]`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7<br/><br/>
Seřadí pole pomocí zadané funkce predikátu. Řazení očekává odkaz na dva po sobě jdoucí prvky ve funkci Expression jako #item1 a #item2 * ``sort([4, 8, 2, 3], compare(#item1, #item2)) -> [2, 3, 4, 8]`` @ no__t-1 @ no__t-2 @ no__t-3.<br/><br/>
Získá SOUNDEX kód pro řetězec * ``soundex('genius') -> 'G520'`` @ no__t-1 @ no__t-2<br/><br/>
Rozdělí řetězec na základě oddělovače a vrátí pole řetězců * ``split('bojjus,guchus,dumbo', ',') -> ['bojjus', 'guchus', 'dumbo']`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8<br/><br/>
Vypočítá druhou odmocninu čísla * ``sqrt(9) -> 3`` @ no__t-1 @ no__t-2.<br/><br/>
Zkontroluje, jestli řetězec začíná zadaným řetězcem * ``startsWith('dumbo', 'du') -> true`` @ no__t-1 @ no__t-2.<br/><br/>
Získá směrodatnou odchylku sloupce * ``stdDev(sales)`` @ no__t-1 @ no__t-2.<br/><br/>
Na základě kritérií získá směrodatnou odchylku sloupce * ``stddevIf(region == 'West', sales)`` @ no__t-1 @ no__t-2.<br/><br/>
Získá směrodatnou odchylku základního souboru sloupce * ``stddevPopulation(sales)`` @ no__t-1 @ no__t-2.<br/><br/>
Na základě kritérií získá směrodatnou odchylku základního souboru sloupce * ``stddevPopulationIf(region == 'West', sales)`` @ no__t-1 @ no__t-2.<br/><br/>
Získá směrodatnou odchylku sloupce * ``stddevSample(sales)`` @ no__t-1 @ no__t-2.<br/><br/>
Na základě kritérií získá vzorovou směrodatnou odchylku sloupce * ``stddevSampleIf(region == 'West', sales)`` @ no__t-1 @ no__t-2.<br/><br/>
Odečíst měsíce od data nebo časového razítka. Stejné jako operátor--operator pro datum * ``subDays(toDate('2016-08-08'), 1) -> toDate('2016-08-07')`` @ no__t-1 @ no__t-2<br/><br/>
Odečíst měsíce od data nebo časového razítka * ``subMonths(toDate('2016-09-30'), 1) -> toDate('2016-08-31')`` @ no__t-1 @ no__t-2<br/><br/>
Extrahuje podřetězec určité délky z pozice. Pozice je na 1. Pokud je délka vynechána, je nastavena na konec řetězce * ``substring('Cat in the hat', 5, 2) -> 'in'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Získá agregovaný součet číselného sloupce * ``sum(col)`` @ no__t-1 @ no__t-2.<br/><br/>
Získá agregovaný součet jedinečných hodnot číselného sloupce * ``sumDistinct(col)`` @ no__t-1 @ no__t-2.<br/><br/>
Na základě kritérií získá agregační součet číselného sloupce. Podmínka může být založená na jakémkoli sloupci * ``sumDistinctIf(state == 'CA' && commission < 10000, sales)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Na základě kritérií získá agregační součet číselného sloupce. Podmínka může být založená na jakémkoli sloupci * ``sumIf(state == 'CA' && commission < 10000, sales)`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Vypočítá hodnotu tangens * ``tan(0) -> 0.0`` @ no__t-1 @ no__t-2.<br/><br/>
Vypočítá hodnotu hyperbolický tangens * ``tanh(0) -> 0.0`` @ no__t-1 @ no__t-2.<br/><br/>
Převede jakékoli číslo, datum/časové razítko nebo řetězec na binární reprezentaci * ``toBinary(3) -> [0x11]`` @ no__t-1 @ no__t-2.<br/><br/>
Převede hodnotu (t ', ' true ', ' y ', ' yes ', ' 1 ') na true a (' f ', ' false ', ' n ', ' ne ', ' 0 ') na false a hodnotu NULL pro jakoukoliv jinou hodnotu * ``toBoolean('true') -> true`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Převede řetězec vstupního data na datum pomocí volitelného formátu data a času. Pro dostupné formáty se podívejte na SimpleDateFormat Java. Pokud je formát vstupního data vynechán, výchozí formát je rrrr-[M] M-[d] d. Přijatelné formáty jsou: [rrrr, rrrr-[M] M, rrrr-[M] M-[d] d, rrrr-[M] M-[d] dT *] * ``toDate('2012-8-18') -> toDate('2012-08-18')`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Převede libovolný číselný řetězec nebo řetězec na desítkovou hodnotu. Pokud není Zadaná přesnost a škálování, je nastavená na (10, 2). Pro převod lze použít volitelný formát desítkového čísla Java. Volitelný formát národního prostředí ve formě jazyka BCP47, jako je en-US, de, zh-CN * ``toDecimal(123.45) -> 123.45`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Převede libovolný číselný řetězec nebo řetězec na hodnotu Double. Pro převod lze použít volitelný formát desítkového čísla Java. Volitelný formát národního prostředí ve formě jazyka BCP47, jako je en-US, de, zh-CN * ``toDouble(123.45) -> 123.45`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5<br/><br/>
Převede libovolný číselný řetězec nebo řetězec na hodnotu float. Pro převod lze použít volitelný formát desítkového čísla Java. Zkrátí všechna dvojitá * ``toFloat(123.45) -> 123.45f`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Převede libovolný číselný řetězec nebo řetězec na celočíselnou hodnotu. Pro převod lze použít volitelný formát desítkového čísla Java. Zkrátí všechna dlouhá, float, Double * ``toInteger(123) -> 123`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Převede libovolný číselný řetězec nebo řetězec na dlouhou hodnotu. Pro převod lze použít volitelný formát desítkového čísla Java. Zkrátí libovolný float, Double * ``toLong(123) -> 123`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Převede libovolný číselný řetězec nebo řetězec na krátkou hodnotu. Pro převod lze použít volitelný formát desítkového čísla Java. Zkrátí celé číslo, Long, float, Double * ``toShort(123) -> 123`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Převede primitivní datový typ na řetězec. Pro čísla a datum, kdy lze zadat formát. Je-li tento parametr zadán, je vybrána výchozí hodnota systému. Pro čísla se používá desítkový formát Java. Všechny možné formáty data najdete v Java SimpleDateFormat. výchozí formát je RRRR-MM-DD * ``toString(10) -> '10'`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6 @ no__t-7 @ no__t-8 @ no__t-9<br/><br/>
Převede řetězec na časové razítko s ohledem na volitelný formát časového razítka. Všechny možné formáty najdete v Java SimpleDateFormat. Pokud je časové razítko vynecháno ve výchozím vzoru. rrrr-[M] M-[d] d hh: mm: ss [. f...] se používá. Můžete předat volitelné časové pásmo ve formátu "GMT", "PST", "UTC", "Amerika/Kajmanské". Časové razítko podporuje přesnost až milisekund s hodnotou 999Refer Java SimpleDateFormat pro dostupné formáty. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``toTimestamp('2016-12-31 00:12:00') -> toTimestamp('2016-12-31 00:12:00')`` @ no__t-2 @ no__t-3 @ no__t-4 @ no__t-5 @ no__t-6<br/><br/>
Převede časové razítko na UTC. Můžete předat volitelné časové pásmo ve formátu "GMT", "PST", "UTC", "Amerika/Kajmanské". Pro dostupné formáty je standardně nastavená na aktuální SimpleDateFormat Java timezoneRefer. https://docs.oracle.com/javase/8/docs/api/java/text/SimpleDateFormat.html * ``toUTC(currentTimeStamp()) == toTimestamp('2050-12-12 19:18:12') -> false`` @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Nahraďte jednu sadu znaků jinou sadou znaků v řetězci. Znaky mají 1 až 1 náhradu * ``translate('(bojjus)', '()', '[]') -> '[bojjus]'`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Ořízne řetězec úvodních a koncových znaků. Pokud není zadán druhý parametr, ořízne prázdné znaky. Jinak ořízne libovolný znak zadaný ve druhém parametru * ``trim('  dumbo  ') -> 'dumbo'`` @ no__t-1 @ no__t-2 @ no__t-3.<br/><br/>
Vždy vrátí hodnotu true. Použijte syntaxi funkce (true ()), pokud je sloupec s názvem "true" * ``(10 + 20 == 30) -> true`` @ no__t-1 @ no__t-2 @ no__t-3<br/><br/>
Odpovídá typu sloupce. Dá se použít jenom ve výrazech vzoru. číslo odpovídá krátkému, celému číslu, dlouhému, dvojitému, floatu nebo desetinnému číslu, které odpovídá krátkému, celému číslu, dlouhému, desetinné číslo odpovídá typu datum nebo časové razítko, * ``typeMatch(type, 'number')`` @ no__t-1 @ no__t-2 @ no__ t-3<br/><br/>
Velká písmena v řetězci * ``upper('bojjus') -> 'BOJJUS'`` @ no__t-1 @ no__t-2<br/><br/>
Vrátí generovaný identifikátor UUID * ``uuid()`` @ no__t-1 @ no__t-2.<br/><br/>
Získá odchylku sloupce * ``variance(sales)`` @ no__t-1 @ no__t-2.<br/><br/>
Na základě kritérií získá odchylku sloupce * ``varianceIf(region == 'West', sales)`` @ no__t-1 @ no__t-2.<br/><br/>
Získá odchylku populace sloupce * ``variancePopulation(sales)`` @ no__t-1 @ no__t-2.<br/><br/>
Na základě kritérií získá odchylku populace sloupce * ``variancePopulationIf(region == 'West', sales)`` @ no__t-1 @ no__t-2.<br/><br/>
Získá neposunutou odchylku sloupce * ``varianceSample(sales)`` @ no__t-1 @ no__t-2.<br/><br/>
Na základě kritérií získá neposunutou odchylku sloupce * ``varianceSampleIf(region == 'West', sales)`` @ no__t-1 @ no__t-2.<br/><br/>
Získá týden v roce zadané datum * ``weekOfYear(toDate('2008-02-20')) -> 8`` @ no__t-1 @ no__t-2.<br/><br/>
Doba trvání v milisekundách pro počet týdnů * ``weeks(2) -> 1209600000L`` @ no__t-1 @ no__t-2<br/><br/>
Logický operátor XOR Stejné jako ^ operator * ``xor(true, false) -> true`` @ no__t-1 @ no__t-2 @ no__t-3 @ no__t-4<br/><br/>
Získá hodnotu roku data * ``year(toDate('2012-8-8')) -> 2012``.

## <a name="next-steps"></a>Další kroky

[Naučte se používat Tvůrce výrazů](concepts-data-flow-expression-builder.md).
