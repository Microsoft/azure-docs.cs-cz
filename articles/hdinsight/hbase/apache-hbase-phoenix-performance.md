---
title: Výkon Phoenixu v Azure HDInsight
description: Doporučené postupy pro optimalizaci výkonu Apache Phoenix pro clustery Azure HDInsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/27/2019
ms.openlocfilehash: 7f8f20be81e815414c283f7ec48aa6503e3b60ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552640"
---
# <a name="apache-phoenix-performance-best-practices"></a>Osvědčené postupy pro Apache Phoenix z hlediska výkonu

Nejdůležitějším aspektem výkonu [Apache Phoenix](https://phoenix.apache.org/) je optimalizace základní [Apache HBase](https://hbase.apache.org/). Phoenix vytvoří relační datový model na vrcholu HBase, který převádí dotazy SQL do operací HBase, jako je například prohledává. Návrh schématu tabulky, výběr a řazení polí v primárním klíči a použití indexů ovlivňují výkon společnosti Phoenix.

## <a name="table-schema-design"></a>Návrh schématu tabulky

Při vytváření tabulky ve Phoenixu je tato tabulka uložena v tabulce HBase. Tabulka HBase obsahuje skupiny sloupců (rodiny sloupců), ke kterým se přistupuje společně. Řádek v tabulce Phoenix je řádek v tabulce HBase, kde každý řádek se skládá z buněk s verzí přidružených k jednomu nebo více sloupcům. Logicky jeden řádek HBase je kolekce párů klíč hodnota, z nichž každý má stejnou hodnotu řádku. To znamená, že každý pár klíč hodnota má rowkey atribut a hodnota tohoto rowkey atribut je stejný pro konkrétní řádek.

Návrh schématu tabulky Phoenix zahrnuje návrh primárního klíče, návrh rodiny sloupců, návrh jednotlivých sloupců a způsob rozdělení dat na oddíly.

### <a name="primary-key-design"></a>Návrh primárního klíče

Primární klíč definovaný v tabulce ve Phoenixu určuje, jak jsou data uložena v řádku základní tabulky HBase. V HBase jediný způsob, jak získat přístup k určitému řádku je s rowkey. Kromě toho jsou data uložená v tabulce HBase seřazena podle řádku. Phoenix vytvoří hodnotu řádku zřetězením hodnot každého sloupce v řádku v pořadí, v jakém jsou definovány v primárním klíči.

Například tabulka pro kontakty má jméno, příjmení, telefonní číslo a adresu, všechny ve stejné rodině sloupců. Primární klíč můžete definovat na základě rostoucího pořadového čísla:

|řádek|       adresa|   Android| firstName| lastName|
|------|--------------------|--------------|-------------|--------------|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole|
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji|

Pokud se však často dotazujete podle lastName, nemusí tento primární klíč fungovat dobře, protože každý dotaz vyžaduje úplnou prohledávací tabulku, aby přečetl hodnotu every lastName. Místo toho můžete definovat primární klíč ve sloupcích číslo příjmení, jméno a číslo sociálního pojištění. Tento poslední sloupec je rozptýlit dva obyvatele na stejné adrese se stejným jménem, jako je otec a syn.

|řádek|       adresa|   Android| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

S tímto novým primárním klíčem by byly klíče řádků generované společností Phoenix:

|řádek|       adresa|   Android| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

V prvním řádku výše jsou data pro řádek reprezentována tak, jak je znázorněno:

|řádek|       key|   value|
|------|--------------------|---|
|  Dole-John-111|adresa |1111 San Gabriel Dr.|  
|  Dole-John-111|Android |1-425-000-0002|  
|  Dole-John-111|firstName |John|  
|  Dole-John-111|lastName |Dole|  
|  Dole-John-111|socialSecurityNum |111|

Tento řádek klíč nyní ukládá duplicitní kopii dat. Zvažte velikost a počet sloupců, které zahrnete do primárního klíče, protože tato hodnota je zahrnuta ve všech buňkách v podkladové tabulce HBase.

Také pokud primární klíč má hodnoty, které jsou monotonically zvýšení, měli byste vytvořit tabulku s *kbelíky soli,* aby se zabránilo vytváření zápisu hotspotů - viz [Partition data](#partition-data).

### <a name="column-family-design"></a>Návrh rodiny sloupců

Pokud jsou některé sloupce přístupné častěji než jiné, měli byste vytvořit více rodin sloupců, abyste oddělili často přístupné sloupce od zřídka používaných sloupců.

Také pokud některé sloupce mají tendenci přistupovat společně, vložte tyto sloupce do stejné rodiny sloupců.

### <a name="column-design"></a>Návrh sloupce

* Zachovat VARCHAR sloupce pod přibližně 1 MB z důvodu vstupně-vstupně-va nákladů velkých sloupců. Při zpracování dotazů HBase zhmotní buňky v plném rozsahu před jejich odesláním klientovi a klient je obdrží v plném rozsahu před jejich předáním kódu aplikace.
* Uložte hodnoty sloupců pomocí kompaktního formátu, například protobuf, Avro, msgpack nebo BSON. JSON se nedoporučuje, protože je větší.
* Zvažte kompresi dat před úložištěm snížit latenci a vstupně-výstupní náklady.

### <a name="partition-data"></a>Dělení dat

Phoenix umožňuje řídit počet oblastí, kde jsou distribuovány vaše data, což může výrazně zvýšit výkon čtení a zápisu. Při vytváření tabulky Phoenix můžete data solit nebo předem rozdělit.

Chcete-li při vytváření solit tabulku, zadejte počet solných kbelíků:

    CREATE TABLE CONTACTS (...) SALT_BUCKETS = 16

Toto solení rozdělí tabulku podle hodnot primárních klíčů a hodnoty se automaticky zvolí. 

Chcete-li určit, kde dojde k rozdělení tabulky, můžete tabulku předem rozdělit zadáním hodnot rozsahu, podél kterého dochází k rozdělení. Chcete-li například vytvořit tabulku rozdělenou podle tří oblastí:

    CREATE TABLE CONTACTS (...) SPLIT ON ('CS','EU','NA')

## <a name="index-design"></a>Návrh indexu

Phoenix index je hbase tabulka, která ukládá kopii některých nebo všech dat z indexované tabulky. Index zlepšuje výkon pro určité typy dotazů.

Pokud máte více indexů definované a potom dotaz na tabulku, Phoenix automaticky vybere nejlepší index pro dotaz. Primární index se vytvoří automaticky na základě primárních klíčů, které vyberete.

U očekávaných dotazů můžete také vytvořit sekundární indexy zadáním jejich sloupců.

Při navrhování indexů:

* Vytvořte pouze indexy, které potřebujete.
* Omezte počet indexů v často aktualizovaných tabulkách. Aktualizace tabulky přeložit do zápisy do hlavní tabulky a tabulky indexu.

## <a name="create-secondary-indexes"></a>Vytvořit sekundární indexy

Sekundární indexy můžete zlepšit výkon čtení otočením co by bylo úplné prohledávač tabulky do bodu vyhledávání, za cenu úložného prostoru a rychlost zápisu. Sekundární indexy lze přidat nebo odebrat po vytvoření tabulky a nevyžadují změny existujících dotazů – dotazy prostě běží rychleji. V závislosti na vašich potřebách zvažte vytvoření krytých indexů, funkčních indexů nebo obojího.

### <a name="use-covered-indexes"></a>Použití krytých indexů

Kryté indexy jsou indexy, které obsahují data z řádku kromě hodnot, které jsou indexovány. Po nalezení požadované položky indexu není nutné přistupovat k primární tabulce.

Například v tabulce příkladu kontaktu můžete vytvořit sekundární index pouze ve sloupci socialSecurityNum. Tento sekundární index by urychlil dotazy, které filtrují podle hodnot socialSecurityNum, ale načítání jiných hodnot polí bude vyžadovat další čtení oproti hlavní tabulce.

|řádek|       adresa|   Android| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

Pokud však obvykle chcete vyhledat firstName a lastName dané socialSecurityNum, můžete vytvořit krytý index, který obsahuje firstName a lastName jako skutečná data v tabulce indexu:

    CREATE INDEX ssn_idx ON CONTACTS (socialSecurityNum) INCLUDE(firstName, lastName);

Tento krytý index umožňuje následující dotaz získat všechna data pouze čtením z tabulky obsahující sekundární index:

    SELECT socialSecurityNum, firstName, lastName FROM CONTACTS WHERE socialSecurityNum > 100;

### <a name="use-functional-indexes"></a>Použití funkčních indexů

Funkční indexy umožňují vytvořit index na libovolný výraz, který očekáváte, že budou použity v dotazech. Jakmile máte funkční index na místě a dotaz používá tento výraz, index může být použit k načtení výsledků, nikoli tabulky dat.

Můžete například vytvořit index, který vám umožní vyhledávat malá a velká písmena na kombinovaném křestním jménu a příjmení osoby:

     CREATE INDEX FULLNAME_UPPER_IDX ON "Contacts" (UPPER("firstName"||' '||"lastName"));

## <a name="query-design"></a>Návrh dotazu

Hlavní aspekty v návrhu dotazu jsou:

* Seznamte se s plánem dotazů a ověřte jeho očekávané chování.
* Připojte se efektivně.

### <a name="understand-the-query-plan"></a>Principy plánu dotazů

V [SQLLine](http://sqlline.sourceforge.net/)použijte vysvětlit následuje dotaz SQL zobrazit plán operací, které Phoenix bude provádět. Zkontrolujte, zda plán:

* V případě potřeby použije primární klíč.
* Používá vhodné sekundární indexy, nikoli tabulku dat.
* Používá range scan nebo SKIP SCAN, kdykoli je to možné, spíše než table scan.

#### <a name="plan-examples"></a>Příklady plánování

Jako příklad řekněme, že máte tabulku s názvem LETY, která ukládá informace o zpoždění letu.

Výběr všech letů s leteckou `19805`společností , kde je airlineid pole, které není v primárním klíči nebo v žádném indexu:

    select * from "FLIGHTS" where airlineid = '19805';

Spusťte příkaz explain takto:

    explain select * from "FLIGHTS" where airlineid = '19805';

Plán dotazů vypadá takto:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN FULL SCAN OVER FLIGHTS
        SERVER FILTER BY AIRLINEID = '19805'

V tomto plánu si poznamenejte frázi FULL Scan OVER FLIGHTS. Tato fráze označuje spuštění provádí table scan přes všechny řádky v tabulce, spíše než pomocí efektivnější range scan nebo SKIP Procan možnost.

Nyní řekněme, že chcete dotaz na lety v lednu `AA` 2, 2014 pro dopravce, kde jeho flightnum byl větší než 1. Předpokládejme, že sloupce rok, měsíc, denměsíc, dopravce a flightnum existují v tabulce příkladu a jsou součástí složeného primárního klíče. Dotaz bude vypadat takto:

    select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

Podívejme se na plán pro tento dotaz pomocí:

    explain select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

Výsledný plán je:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER FLIGHTS [2014,1,2,'AA',2] - [2014,1,2,'AA',*]

Hodnoty v hranatých závorkách jsou rozsahem hodnot pro primární klíče. V tomto případě jsou hodnoty rozsahu stanoveny rokem 2014, měsícem 1 a dayofmonth 2, ale`*`povolte hodnoty pro flightnum začínající 2 a na nahoru ( ). Tento plán dotazu potvrzuje, že primární klíč se používá podle očekávání.

Dále vytvořte index v tabulce `carrier2_idx` FLIGHTS s názvem, který je pouze v poli dopravce. Tento index zahrnuje také flightdate, tailnum, origin a flightnum jako kryté sloupce, jejichž data jsou také uložena v indexu.

    CREATE INDEX carrier2_idx ON FLIGHTS (carrier) INCLUDE(FLIGHTDATE,TAILNUM,ORIGIN,FLIGHTNUM);

Řekněme, že chcete získat dopravce spolu s flightdate a tailnum, jako v následujícím dotazu:

    explain select carrier,flightdate,tailnum from "FLIGHTS" where carrier = 'AA';

Měli byste vidět tento index se používá:

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER CARRIER2_IDX ['AA']

Úplný seznam položek, které se mohou objevit ve světle výsledků plánu, najdete v části Vysvětlit plány v [Průvodci laděním Apache Phoenix](https://phoenix.apache.org/tuning_guide.html).

### <a name="join-efficiently"></a>Efektivně se připojte

Obecně chcete vyhnout spojení, pokud jedna strana je malý, zejména na časté dotazy.

Pokud je to nutné, můžete `/*+ USE_SORT_MERGE_JOIN */` provést velké spojení s nápovědou, ale velké spojení je nákladná operace přes obrovské množství řádků. Pokud by celková velikost všech pravostranných postranních tabulek `/*+ NO_STAR_JOIN */` překročila dostupnou paměť, použijte nápovědu.

## <a name="scenarios"></a>Scénáře

Následující pokyny popisují některé běžné vzory.

### <a name="read-heavy-workloads"></a>Úlohy náročné na čtení

U případů použití náročné na čtení se ujistěte, že používáte indexy. Chcete-li navíc ušetřit režii při čtení, zvažte vytvoření krytých indexů.

### <a name="write-heavy-workloads"></a>Úlohy náročné na zápis

Pro úlohy náročné na zápis, kde se primární klíč monotonically zvyšuje, vytvořte kbelíky soli, abyste se vyhnuli aktivním oblastem zápisu, na úkor celkové propustnosti pro čtení z důvodu dalších potřebných prohledávek. Také při použití UPSERT psát velký počet záznamů, vypněte autoCommit a dávky do záznamů.

### <a name="bulk-deletes"></a>Hromadné odstranění

Při odstraňování velké datové sady zapněte před vydáním dotazu DELETE automatické potvrzení, aby si klient nemusel pamatovat klíče řádků pro všechny odstraněné řádky. Funkce AutoCommit zabrání klientovi v ukládání řádků ovlivněných odstraněním do vyrovnávací paměti, takže phoenix je může odstranit přímo na serverech oblasti bez nákladů na jejich vrácení klientovi.

### <a name="immutable-and-append-only"></a>Neměnné a pouze pro připojení

Pokud váš scénář upřednostňuje rychlost zápisu před integritou dat, zvažte zakázání protokolu pro zápis napřed při vytváření tabulek:

    CREATE TABLE CONTACTS (...) DISABLE_WAL=true;

Podrobnosti o této a dalších možnostech naleznete [v tématu Apache Phoenix Grammar](https://phoenix.apache.org/language/index.html#options).

## <a name="next-steps"></a>Další kroky

* [Apache Phoenix Tuning Průvodce](https://phoenix.apache.org/tuning_guide.html)
* [Sekundární indexy](https://phoenix.apache.org/secondary_indexing.html)
