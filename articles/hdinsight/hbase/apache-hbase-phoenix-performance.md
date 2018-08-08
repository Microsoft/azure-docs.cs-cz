---
title: Phoenix výkon v Azure HDInsight
description: Osvědčené postupy pro optimalizaci výkonu Phoenix.
services: hdinsight
author: ashishthaps
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: db00dcad8f3dffbb958158bef9fdb75423eba2f7
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39592267"
---
# <a name="phoenix-performance-best-practices"></a>Osvědčené postupy pro Phoenix z hlediska výkonu

Nejdůležitější aspekty Phoenix výkonu je k optimalizaci základní HBase. Phoenix vytvoří relačním datovým modelem nad HBase, který převádí dotazy SQL HBase operace, jako je prohledávání. Phoenix výkon ovlivnit návrh schéma tabulky, výběr a pořadí polí ve primárního klíče a vaše využití všechny indexy.

## <a name="table-schema-design"></a>Návrh schématu tabulky

Při vytváření tabulky Phoenix tabulky je uložená v tabulce HBase. Na tabulku HBase obsahuje skupiny sloupců (rodin sloupců), které jsou přístupné společně. Řádek v tabulce Phoenix je řádek v tabulce HBase, kde každý řádek obsahuje systémovou správou verzí buňky přidružené k jedné nebo více sloupců. Jeden řádek HBase je logicky, kolekce párů klíč hodnota, mají stejnou hodnotu rowkey. To znamená rowkey atribut má každý pár klíč hodnota a hodnota tohoto atributu rowkey je stejná pro konkrétní řádek.

Návrh schématu tabulky Phoenix obsahuje primární klíč návrhu, návrh rodiny sloupců, návrhu jednotlivé sloupce a jak dělení dat.

### <a name="primary-key-design"></a>Primární klíče návrhu

Primární klíč pro tabulku Phoenix definované určuje způsob uložení dat v rámci rowkey na základní tabulku HBase. V HBase je jediný způsob, jak přistupovat k konkrétního řádku rowkey. Kromě toho data uložená v tabulce HBase je seřazený podle rowkey. Phoenix vytvoří hodnotu rowkey zřetězením hodnoty každého sloupce v řádku v pořadí, ve kterém jsou definovány ve primárního klíče.

Například tabulku kontaktů má křestní jméno, poslední název, telefonní číslo a adresu, všechny ve stejné rodině sloupců. Můžete definovat primární klíč založený na rostoucí pořadové číslo:

|rowkey|       Adresa|   telefon| Jméno| Příjmení|
|------|--------------------|--------------|-------------|--------------|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    Jan|Dole|
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvinovým|Raji|

Ale pokud často dotazování podle příjmení tento primární klíč nemusí provést, protože každý dotaz vyžaduje ke skenování celé tabulky načíst hodnotu každé příjmení. Místo toho můžete definovat primární klíč na lastName, firstName a sloupce číslo sociálního pojištění. K rozlišení dvou pobytem na stejné adrese se stejným názvem, jako je například otcem a tak je tento poslední sloupec.

|rowkey|       Adresa|   telefon| Jméno| Příjmení| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    Jan|Dole| 111 |
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvinovým|Raji| 222 |

Pomocí tohoto nového primárního klíče řádku by generovaných Phoenix klíče:

|rowkey|       Adresa|   telefon| Jméno| Příjmení| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole – John-111|1111 San Gabriel Dr.|1-425-000-0002|    Jan|Dole| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvinovým|Raji| 222 |

V prvním řádku výše je reprezentována data rowkey jak je znázorněno:

|rowkey|       key|   hodnota| 
|------|--------------------|---|
|  Dole – John-111|Adresa |1111 San Gabriel Dr.|  
|  Dole – John-111|telefon |1-425-000-0002|  
|  Dole – John-111|Jméno |Jan|  
|  Dole – John-111|Příjmení |Dole|  
|  Dole – John-111|socialSecurityNum |111| 

Tato rowkey nyní ukládá jejich kopii duplicitní data. Vezměte v úvahu velikost a počet sloupců, které jsou ve primárního klíče, protože je tato hodnota zahrnuje všechny buňky v podkladové tabulce HBase.

Také, pokud primární klíč obsahuje hodnoty, které monotónně roste, měli byste vytvořit tabulku s *salt kbelíků* pomoct vyhnout se vytváření hotspotů zápisu-naleznete v tématu [dělit data](#partition-data).

### <a name="column-family-design"></a>Návrh rodiny sloupců

Pokud některé sloupce se využívají častěji než jiné, měli byste vytvořit více rodin sloupců k oddělení často používané sloupce z zřídka využívaných sloupců.

Pokud některé sloupce, obvykle bude přistupovat společně, vložte tyto sloupce ve stejné rodině sloupců.

### <a name="column-design"></a>Návrh sloupce

* Zachovejte sloupce VARCHAR pod přibližně 1 MB z důvodu náklady na vstupně-výstupních operací velké sloupce. Při zpracování dotazů, bude buněk v plné výši HBase realizována před jejich odesláním prostřednictvím klienta a klient obdrží v plné výši před jejich předání do kódu aplikace.
* Hodnoty ve sloupcích pomocí kompaktní podobě jako protobuf, Avro, msgpack nebo BSON Store. JSON se nedoporučuje, protože je větší.
* Vezměte v úvahu komprese dat před úložiště se vyjmout latence a nákladů vstupně-výstupních operací.

### <a name="partition-data"></a>Dělení dat

Phoenix umožňuje řídit počet oblasti, kde jsou vaše data distribuovaná, ve kterých může výrazně zvýšit výkon pro čtení a zápisu. Při vytváření tabulky Phoenix, můžete salt nebo předem rozdělit data.

Chcete-li salt tabulku během vytváření, zadejte počet kbelíků řetězce salt:

    CREATE TABLE CONTACTS (...) SALT_BUCKETS = 16

Tato solení rozdělí tabulku podél hodnoty primárního klíče, výběrem hodnoty automaticky. 

K řízení, pokud dojde k rozdělení tabulky, můžete předem rozdělit tabulku zadáním rozsahu hodnot, na které dojde k rozdělení. Například pokud chcete vytvořit tabulku rozdělit podél třech oblastech:

    CREATE TABLE CONTACTS (...) SPLIT ON ('CS','EU','NA')

## <a name="index-design"></a>Návrh indexu

Phoenix index je tabulky HBase, který ukládá kopie některá nebo všechna data z indexované tabulky. Index zlepšuje výkon pro konkrétní typy dotazů.

Pokud máte více indexů, definice a potom dotaz na tabulku, Phoenix automaticky vybere nejlepší index dotazu. Primární index se vytvoří automaticky v závislosti na primární klíče, které vyberete.

Pro očekávané dotazy můžete také vytvořit sekundární indexy zadáním jejich sloupce.

Při navrhování indexů:

* Vytvořte pouze indexy, které potřebujete.
* Omezte počet indexů na potřeba často aktualizovat tabulky. Aktualizace tabulky se převedou zápisy do hlavní tabulka a tabulky indexů.

## <a name="create-secondary-indexes"></a>Vytváření sekundárních indexů

Co by se ke skenování celé tabulky do vyhledávání bodu, za cenu úložný prostor a rychlost zápisu zapínání čtení výkon lze zvýšit sekundární indexy. Sekundární indexy lze přidat nebo odebrat po vytvoření tabulky a nevyžadují žádné změny existujících dotazů – dotazy jen běžel rychleji. V závislosti na potřebách zvažte vytvoření zahrnuté indexů a indexy funkční.

### <a name="use-covered-indexes"></a>Použití zahrnutých indexů

Zahrnuté indexy jsou indexy, které zahrnují data z řádků kromě hodnoty, které jsou indexovány. Když se najde záznam požadovaný index, není nutné pro přístup k primární tabulce.

Například v příkladu obraťte se na tabulku může vytvořit sekundární index pro pouze sloupec socialSecurityNum. Tento sekundární index by zrychlení dotazů, které filtrovat podle hodnoty socialSecurityNum, ale načítají se další pole hodnoty bude vyžadovat další čtení s hlavní tabulkou.

|rowkey|       Adresa|   telefon| Jméno| Příjmení| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole – John-111|1111 San Gabriel Dr.|1-425-000-0002|    Jan|Dole| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvinovým|Raji| 222 |

Ale pokud chcete obvykle vyhledat firstName a lastName zadaný socialSecurityNum, můžete vytvořit zahrnuté indexu, která bude obsahovat skutečná data v tabulce indexu firstName a lastName:

    CREATE INDEX ssn_idx ON CONTACTS (socialSecurityNum) INCLUDE(firstName, lastName);

Tento index zahrnuté umožňuje následující dotaz pro získání všechna data stejně, přečtěte si téma z tabulky obsahující sekundární index:

    SELECT socialSecurityNum, firstName, lastName FROM CONTACTS WHERE socialSecurityNum > 100;

### <a name="use-functional-indexes"></a>Použít funkční indexů

Funkční indexy umožňují vytvoření indexu na libovolný výraz, který očekáváte, že v dotazech použít nedají. Jakmile budete mít funkční index na místě a používá tento výraz dotazu, lze načíst výsledky než v tabulce dat index.

Například můžete vytvořit index tak, aby bylo možné provést hledání velkých a malých písmen na kombinované křestní jméno a příjmení osoby:

     CREATE INDEX FULLNAME_UPPER_IDX ON "Contacts" (UPPER("firstName"||' '||"lastName"));

## <a name="query-design"></a>Návrh dotazu

Je nutné zvážit v návrhu dotazu jsou:

* Vysvětlení plánu dotazu a ověřte jeho očekávané chování.
* Připojte se k efektivní.

### <a name="understand-the-query-plan"></a>Vysvětlení plánu dotazu

V [SQLLine](http://sqlline.sourceforge.net/), použijte k zobrazení plánu operace, které budou provádět Phoenix VYSVĚTLIT, za nímž následuje dotaz SQL. Zkontrolujte, že tento plán:

* Používá váš primární klíč v případě potřeby.
* Použití vhodné sekundární indexy, nikoli data tabulky.
* Používá KONTROLOVAT rozsah nebo Přeskočit kontrolu, kdykoli je to možné, namísto PROHLEDÁVÁNÍ tabulky.

#### <a name="plan-examples"></a>Příklady plán

Například Řekněme, že máte tabulku volá LETY, která ukládá informace o zpoždění letů.

Chcete-li vybrat všechny letů s airlineid z `19805`, kde je pole, které se nenachází v primární klíč nebo v libovolném indexu airlineid:

    select * from "FLIGHTS" where airlineid = '19805';

Spusťte příkaz vysvětlit následujícím způsobem:

    explain select * from "FLIGHTS" where airlineid = '19805';

Plán dotazu vypadá takto:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN FULL SCAN OVER FLIGHTS
        SERVER FILTER BY AIRLINEID = '19805'

V tomto plánu mějte na paměti frázi úplné kontroly nad LETY. Tato fráze označuje, že provedení nepodporuje tabulku SKENOVÁNÍ přes všechny řádky v tabulce, a ne pomocí možnosti efektivnější KONTROLOVAT rozsah nebo přeskočit kontroly.

Nyní Řekněme, že chcete zadat dotaz pro lety v 2. ledna 2014 dopravce `AA` kde jeho flightnum byla větší než 1. Předpokládejme, že sloupce rok, měsíc, dayofmonth, operátora a flightnum existovat v tabulce příklad a jsou všech součástí složený primární klíč. Dotaz by vypadalo takto:

    select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

Podívejme se na plán pro tento dotaz s:

    explain select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

Výsledný plán je:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER FLIGHTS [2014,1,2,'AA',2] - [2014,1,2,'AA',*]

Rozsah hodnot pro primární klíče jsou hodnoty do hranatých závorek. V tomto případě jsou opraveny s rok 2014, měsíc 1 a dayofmonth 2 hodnoty rozsahu, ale umožňují hodnoty flightnum spuštění s 2 a na (`*`). Tento plán dotazu potvrdí, že se používá primární klíč podle očekávání.

V dalším kroku vytvoření indexu pro tabulku letů s názvem `carrier2_idx` , který je v poli operátora. Tento index také zahrnuje flightdate, tailnum, původu a flightnum jako zahrnuté sloupce, jejichž data jsou také uložena v indexu.

    CREATE INDEX carrier2_idx ON FLIGHTS (carrier) INCLUDE(FLIGHTDATE,TAILNUM,ORIGIN,FLIGHTNUM);

Řekněme, že chcete získat dopravce spolu s flightdate a tailnum, stejně jako v následující dotaz:

    explain select carrier,flightdate,tailnum from "FLIGHTS" where carrier = 'AA';

Měli byste vidět tento index se používá:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER CARRIER2_IDX ['AA']

Úplný seznam položek, které se mohou objevit v vysvětlit výsledky plánu, naleznete v části vysvětlují, plány [Průvodci optimalizací Apache Phoenix](https://phoenix.apache.org/tuning_guide.html).

### <a name="join-efficiently"></a>Připojte se k efektivnímu

Obecně platí chcete se vyhnout spojení, pokud je malé, zejména u časté dotazy na jedné straně.

Pokud třeba, můžete provést velká spojení s `/*+ USE_SORT_MERGE_JOIN */` pomocný parametr, ale velká spojení je náročná operace nad velký počet řádků. Pokud celková velikost všech tabulek na pravé straně by překročily dostupnou paměť, použijte `/*+ NO_STAR_JOIN */` pomocný parametr.

## <a name="scenarios"></a>Scénáře

Následující pokyny popisují některé běžné vzory.

### <a name="read-heavy-workloads"></a>Úlohy náročné na čtení

Případy použití pro náročné na čtení, ujistěte se, že používáte indexy. Kromě toho uložte čtení – čas režie, zvažte vytvoření zahrnuté indexy.

### <a name="write-heavy-workloads"></a>Úlohy náročné na zápis

Pro úlohy náročné na zápis, kde monotónně roste primární klíč vytváření řetězce salt kontejnerů, která pomáhá zabránit zápisu hotspotům za cenu celkovou propustnost čtení kvůli další kontroly, které jsou potřeba. Navíc při použití funkcí UPSERT zapisovat velké množství záznamů, vypněte automatický zápis a služby batch se záznamy.

### <a name="bulk-deletes"></a>Hromadné odstranění

Při odstraňování velkých sadách dat, zapněte automatický zápis před vydáním dotazu odstranit tak, aby klient není potřeba pamatovat si klíče řádku pro všechny odstraněné řádky. Automatický zápis klienta zabrání ukládání do vyrovnávací paměti řádky ovlivněné příkazy DELETE, tak že Phoenix můžete odstranit přímo na oblastní servery, aniž byste museli jejich vrácením klientovi.

### <a name="immutable-and-append-only"></a>Neměnné a jen pro připojení

Pokud váš scénář upřednostňuje rychlost zápisu před integritu dat, zvažte zakázání protokolu zápisu dávky, při vytváření tabulek:

    CREATE TABLE CONTACTS (...) DISABLE_WAL=true;

Podrobnosti o tomto a dalších možností najdete v tématu [Phoenix gramatiky](http://phoenix.apache.org/language/index.html#options).

## <a name="next-steps"></a>Další postup

* [Průvodci optimalizací Phoenix](https://phoenix.apache.org/tuning_guide.html)
* [Sekundární indexy](http://phoenix.apache.org/secondary_indexing.html)
