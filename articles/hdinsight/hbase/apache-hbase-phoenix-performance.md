---
title: Phoenix výkon v Azure HDInsight | Microsoft Docs
description: Osvědčené postupy za účelem optimalizace výkonu Phoenix.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: b4c1e3fb919ab9ad88a15b51a5e204290a7a12cf
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="phoenix-performance-best-practices"></a>Osvědčené postupy pro Phoenix z hlediska výkonu

Nejdůležitější aspekt výkonu Phoenix je za účelem optimalizace základní HBase. Phoenix vytvoří model relačních dat na HBase, který převádí dotazy SQL do HBase operací, jako je například kontroly. Phoenix výkon ovlivnit návrh schéma tabulky, výběr a pořadí polí v primární klíč a používání všechny indexy.

## <a name="table-schema-design"></a>Návrh schéma tabulky

Když vytvoříte tabulku v Phoenix, tato tabulka je uložené v tabulce HBase. V tabulce HBase obsahuje skupiny sloupců (rodin sloupců), které jsou přístupné společně. Řádek v tabulce Phoenix je řádek v tabulce HBase, kde každý řádek obsahuje verzí buněk přidružený jeden nebo více sloupců. Logicky jeden řádek HBase je kolekce dvojic klíč hodnota, které mají stejnou hodnotu rowkey. To znamená jednotlivé páry klíč hodnota obsahuje atribut rowkey a hodnota tohoto atributu rowkey je stejný pro konkrétního řádku.

Návrh schéma tabulky Phoenix obsahuje primární klíč návrhu, rodiny návrhu sloupce, návrhu jednotlivé sloupce a jak dat rozdělena na oddíly.

### <a name="primary-key-design"></a>Primární klíče návrhu

Primární klíč definovaném v tabulce Phoenix určuje způsob uložení dat v rámci rowkey základní tabulky HBase. V HBase je jediným způsobem, jak získat přístup k konkrétního řádku s rowkey. Kromě toho data uložená v tabulce HBase je seřazen podle rowkey. Phoenix sestavení hodnota rowkey zřetězením hodnoty jednotlivých sloupců v řádku v pořadí, ve kterém jsou definovány v primární klíč.

Například tabulku pro kontakty má křestní jméno, poslední název, telefonní číslo a adresa ve stejné rodin sloupců. Můžete třeba definovat primární klíč založený na rostoucí pořadové číslo:

|rowkey|       Adresa|   telefon| FirstName| Příjmení|
|------|--------------------|--------------|-------------|--------------|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    Jan|Dole|
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvinovým|Raji|

Ale pokud často dotazování podle lastName tento primární klíč nemusí provést dobře, protože každý dotaz vyžaduje prohledání úplnou tabulky načíst hodnotu každých lastName. Místo toho můžete definovat primární klíč na lastName, firstName a číslo sociálního pojištění sloupce. Tento poslední sloupec je k rozlišení dvou obyvatele na stejné adrese se stejným názvem, jako je například otec a Syn.

|rowkey|       Adresa|   telefon| FirstName| Příjmení| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    Jan|Dole| 111 |
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvinovým|Raji| 222 |

Klíče generované Phoenix by být tento nový primární klíč řádku:

|rowkey|       Adresa|   telefon| FirstName| Příjmení| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole. Jan 111|1111 San Gabriel Dr.|1-425-000-0002|    Jan|Dole| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvinovým|Raji| 222 |

V prvním řádku výše je reprezentována data rowkey, jak je znázorněno:

|rowkey|       key|   hodnota| 
|------|--------------------|---|
|  Dole. Jan 111|Adresa |1111 San Gabriel Dr.|  
|  Dole. Jan 111|telefon |1-425-000-0002|  
|  Dole. Jan 111|FirstName |Jan|  
|  Dole. Jan 111|Příjmení |Dole|  
|  Dole. Jan 111|socialSecurityNum |111| 

Tato rowkey nyní ukládá duplicitní kopie data. Zvažte velikost a počet sloupců, které zahrnete ve vaší primární klíč, protože tato hodnota je zahrnuta v každé buňce v podkladové tabulce HBase.

Navíc pokud primární klíč obsahuje hodnoty, které jsou rovnoměrně zvýšení, měli byste vytvořit tabulku s *salt kbelíků* vyhnout vytváření zápisu hotspotům-najdete v části [rozdělení dat](#partition-data).

### <a name="column-family-design"></a>Sloupec rodiny návrhu

Pokud některé sloupce přistupuje častěji než jiná, měli byste vytvořit více rodin sloupců oddělit často používaná sloupce z málokdy načítaná sloupce.

Pokud některé sloupce obvykle přístupná společně, uveďte tyto sloupce ve stejné rodin sloupců.

### <a name="column-design"></a>Sloupec návrhu

* Zachovat VARCHAR sloupce v části o 1 MB kvůli náklady na vstupně-výstupních operací velké sloupců. Při zpracování dotazů, bude buněk v úplné HBase realizována před odesláním v klientovi a klient je obdrží plně před jejich předáním do kódu aplikace.
* Ukládání hodnot sloupců pomocí compact formátu například protobuf Avro, msgpack nebo BSON. JSON se nedoporučuje, protože je větší.
* Vezměte v úvahu komprese dat před úložiště vyjímání latenci a náklady na vstupně-výstupní operace.

### <a name="partition-data"></a>Dělení dat

Phoenix vám umožňuje řídit počtu oblastí, kde je distribuován vaše data, které může značně zvýšit výkon pro čtení a zápis. Při vytváření tabulky Phoenix, můžete salt nebo předem rozdělení vaše data.

Chcete-li salt tabulku během vytváření, zadejte počet intervalů, řetězce salt:

    CREATE TABLE CONTACTS (...) SALT_BUCKETS = 16

Tato solení rozdělí tabulku podél hodnot primárního klíče, výběr hodnoty automaticky. 

K řízení, kde dojde k rozdělení tabulky, můžete předem rozdělení tabulky zadáním hodnoty rozsahu, po nichž dojde k rozdělení. Například a vytvořte tabulku rozdělit podél tři oblasti:

    CREATE TABLE CONTACTS (...) SPLIT ON ('CS','EU','NA')

## <a name="index-design"></a>Index návrhu

Phoenix index je tabulky HBase, který ukládá kopie některých nebo všech dat z indexovanou tabulku. Index zlepšuje výkon pro konkrétní typy dotazů.

Pokud máte více indexů definované a pak dotazování tabulky, Phoenix automaticky vybere nejlepší index pro dotaz. Primární index se vytvoří automaticky v závislosti na primární klíče, které vyberete.

Pro předpokládaného dotazy můžete také vytvořit sekundární indexy zadáním jejich sloupce.

Při navrhování vaší indexy:

* Vytvořte pouze indexy, které potřebujete.
* Omezte počet indexů v tabulkách často aktualizované. Aktualizace tabulky převede na zápisy do hlavní tabulka a tabulky indexu.

## <a name="create-secondary-indexes"></a>Vytvořit sekundární indexy

Sekundární indexy může zvýšit rychlost čtení vypnutím co by být prohledávání úplnou tabulky do bodu vyhledávání, za cenu prostor úložiště a rychlost zápisu. Sekundární indexy lze přidat nebo odebrat po vytvoření tabulky a nevyžadují žádné změny existujících dotazů – dotazy stačí spustit rychlejší. Podle potřeby zvažte vytvoření zahrnuté indexy, funkční indexy nebo obojí.

### <a name="use-covered-indexes"></a>Použít zahrnuté indexy

Zahrnuté indexy jsou indexy, které zahrnují data řádku kromě hodnoty, které jsou uloženy. Když se najde záznam požadovaný index, není nutné pro přístup k primární tabulce.

Například v příkladu obraťte se na tabulky, můžete vytvořit sekundární index na právě sloupci socialSecurityNum. Tento sekundární index by urychlení dotazy, které filtrovat podle hodnoty socialSecurityNum, ale načítání další pole hodnot bude vyžadovat další čtení proti hlavní tabulka.

|rowkey|       Adresa|   telefon| FirstName| Příjmení| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole. Jan 111|1111 San Gabriel Dr.|1-425-000-0002|    Jan|Dole| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvinovým|Raji| 222 |

Pokud chcete, obvykle můžete vyhledat firstName a lastName zadané socialSecurityNum, můžete však vytvořit zahrnuté index, který obsahuje jméno a příjmení jako skutečná data tabulky indexu:

    CREATE INDEX ssn_idx ON CONTACTS (socialSecurityNum) INCLUDE(firstName, lastName);

Tento zahrnuté index umožňuje získat všechna data tak, že čtení z tabulky obsahující sekundární index následující dotaz:

    SELECT socialSecurityNum, firstName, lastName FROM CONTACTS WHERE socialSecurityNum > 100;

### <a name="use-functional-indexes"></a>Použít funkční indexy

Funkční indexy umožňují vytvořit index na libovolný výraz, který chcete použít v dotazech. Jakmile máte funkční indexu na místě a dotaz používá tento výraz, index lze načíst výsledky spíše než v tabulce data.

Například můžete vytvořit index pro umožňují provádět velká a malá písmena hledání na kombinovaný křestní jméno a příjmení osoby:

     CREATE INDEX FULLNAME_UPPER_IDX ON "Contacts" (UPPER("firstName"||' '||"lastName"));

## <a name="query-design"></a>Návrhu dotazu

Hlavní aspekty návrhu dotazu jsou:

* Rady pro pochopení plán dotazu a ověřte jeho očekávané chování.
* Připojení k efektivní.

### <a name="understand-the-query-plan"></a>Pochopení plán dotazu

V [SQLLine](http://sqlline.sourceforge.net/), použijte k zobrazení plánu operací, které provádí Phoenix VYSVĚTLIT, za nímž následuje dotaz SQL. Zkontrolujte, že plán:

* Používá vaše primární klíč v případě nutnosti.
* Používá příslušnou sekundární indexy, nikoli dat tabulky.
* Použije KONTROLOVAT rozsah nebo Přeskočit kontrolu, pokud je to možné, nikoli PROHLEDÁVÁNÍ tabulky.

#### <a name="plan-examples"></a>Příklady plán

Jako příklad stát, že máte názvem LETY tabulku, která ukládá informace o letu zpoždění.

Chcete-li vybrat všechny lety s airlineid z `19805`, kde je airlineid pole, které se nenachází v primární klíč nebo v jakékoli indexu:

    select * from "FLIGHTS" where airlineid = '19805';

Spusťte příkaz vysvětlit následujícím způsobem:

    explain select * from "FLIGHTS" where airlineid = '19805';

Plán dotazu vypadá takto:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN FULL SCAN OVER FLIGHTS
        SERVER FILTER BY AIRLINEID = '19805'

V tomto plánu Všimněte si frázi úplné kontroly nad LETY. Tato fráze označuje, že provádění zajišťuje tabulky KONTROLOVAT přes všechny řádky v tabulce, nikoli pomocí možnosti PŘESKOČENÍ kontroly nebo rozsah kontroly efektivnější.

Nyní, že chcete dotaz pro lety 2 ledna 2014 pro zařadit `AA` kde jeho flightnum je větší než 1. Předpokládejme existují v tabulce příklad sloupce rok, měsíc, dayofmonth, operátora a flightnum a jsou všechny součástí složené primární klíče. Dotaz by vypadat takto:

    select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

Podívejme se plán pro tento dotaz s:

    explain select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

Výsledný plán je:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER FLIGHTS [2014,1,2,'AA',2] - [2014,1,2,'AA',*]

Hodnoty v hranatých závorkách jsou v rozsahu hodnot pro primární klíče. V takovém případě hodnoty rozsahu opravuje 2014 rok, měsíc, 1 a dayofmonth 2, ale možné hodnoty pro flightnum spuštění s 2 a na (`*`). Tento plán dotazu potvrdí, že se používá primární klíč podle očekávání.

Dále vytvořte index v tabulce LETY s názvem `carrier2_idx` který je na pole poskytovatel. Tento index také zahrnuje flightdate, tailnum, původu a flightnum jako zahrnuté sloupce, jejichž data jsou také uložena v indexu.

    CREATE INDEX carrier2_idx ON FLIGHTS (carrier) INCLUDE(FLIGHTDATE,TAILNUM,ORIGIN,FLIGHTNUM);

Řekněme, že chcete získat poskytovatel spolu s flightdate a tailnum jako následující dotaz:

    explain select carrier,flightdate,tailnum from "FLIGHTS" where carrier = 'AA';

Měli byste vidět tento index používá:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER CARRIER2_IDX ['AA']

Úplný seznam položek, které se mohou objevit v vysvětlují plán výsledky, najdete v části vysvětlují plánů v [Apache Phoenix ladění průvodce](https://phoenix.apache.org/tuning_guide.html).

### <a name="join-efficiently"></a>Efektivní připojení

Obecně platí budete chtít vyhnout spojení, pokud je malý, zejména u časté dotazy na jedné straně.

Pokud třeba, můžete to udělat velké spojení s `/*+ USE_SORT_MERGE_JOIN */` pomocného parametru, ale velké spojení je náročná operace nad velmi velký počet řádků. Pokud celková velikost všech tabulek pravou stranu straně překročí dostupné paměti, použijte `/*+ NO_STAR_JOIN */` pomocný parametr.

## <a name="scenarios"></a>Scénáře

Následující pokyny popisují některé běžné vzory.

### <a name="read-heavy-workloads"></a>Úlohy náročné na čtení

Případy použití pro čtení náročné, ujistěte se, že používáte indexy. Kromě toho uložte čtení time režie, zvažte vytvoření zahrnuté indexy.

### <a name="write-heavy-workloads"></a>Úlohy náročné na zápis

Pro zápis náročné úlohy, kde monotónně roste primární klíč vytvořte řetězce salt kbelíků, abyste se vyhnuli hotspotům zápisu za cenu celkovou propustnost čtení z důvodu další kontroly, které jsou potřeba. Také při použití UPSERT zapisovat velké množství záznamů, vypněte režimu automatického zápisu a batch se záznamy.

### <a name="bulk-deletes"></a>Hromadné odstranění

Při odstraňování velké datové sady, zapněte režimu automatického zápisu před vydáním dotazu odstranit tak, aby klient není nutné pamatovat řádek klíče pro všechny řádky odstraněné. Režimu automatického zápisu klienta zabrání ukládání do vyrovnávací paměti řádky vliv odstranit, takže tento Phoenix můžete odstranit přímo na serverech oblast bez nákladů na vrácením klientovi.

### <a name="immutable-and-append-only"></a>Neměnné a pouze připojení

Pokud váš scénář upřednostňuje rychlost zápisu před integritu dat, zvažte zákaz předběžné protokolování, při vytváření tabulek:

    CREATE TABLE CONTACTS (...) DISABLE_WAL=true;

Podrobnosti o tomto a dalších možností najdete v tématu [Phoenix gramatika](http://phoenix.apache.org/language/index.html#options).

## <a name="next-steps"></a>Další postup

* [Phoenix ladění Průvodce](https://phoenix.apache.org/tuning_guide.html)
* [Sekundární indexy](http://phoenix.apache.org/secondary_indexing.html)
