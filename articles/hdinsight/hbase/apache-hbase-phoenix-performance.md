---
title: Výkon služby Phoenix v Azure HDInsight
description: Osvědčené postupy pro optimalizaci Apache Phoenix výkonu pro clustery Azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/27/2019
ms.openlocfilehash: ffba3b986b35c375d0404d9d2bae5af79f93c54f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944807"
---
# <a name="apache-phoenix-performance-best-practices"></a>Osvědčené postupy pro Apache Phoenix z hlediska výkonu

Nejdůležitější aspektem [Apache Phoenix](https://phoenix.apache.org/) výkonu je optimalizace základních [HBA pro Apache](https://hbase.apache.org/). Phoenix vytvoří model relačních dat základem HBA, který převede dotazy SQL na operace HBA, jako jsou například kontroly. Návrh schématu tabulky, výběr a řazení polí v primárním klíči a vaše používání indexů má vliv na výkon v Phoenixu.

## <a name="table-schema-design"></a>Návrh schématu tabulky

Když vytvoříte tabulku v Phoenixu, tato tabulka je uložená v tabulce HBA. Tabulka HBA obsahuje skupiny sloupců (rodin sloupců), ke kterým se přistupuje dohromady. Řádek v tabulce Phoenixu je řádek v tabulce HBA, kde každý řádek obsahuje buňky s verzemi, které jsou přidružené k jednomu nebo více sloupcům. Logicky, jeden řádek HBA je kolekcí párů klíč-hodnota, z nichž každá má stejnou hodnotu rowkey. To znamená, že každá dvojice klíč-hodnota má atribut rowkey a hodnota tohoto atributu rowkey je stejná pro určitý řádek.

Návrh schématu tabulky Phoenix zahrnuje návrh primárního klíče, návrh rodiny sloupců, jednotlivý návrh sloupce a způsob rozdělení dat do oddílů.

### <a name="primary-key-design"></a>Návrh primárního klíče

Primární klíč definovaný v tabulce v Phoenixu určuje, jak jsou data uložená v rowkey podkladové tabulky HBA. V rámci adaptérů HBA je jediným způsobem, jak získat přístup k určitému řádku, rowkey. Kromě toho jsou data uložená v tabulce HBA řazena podle rowkey. Phoenix sestaví hodnotu rowkey zřetězením hodnot každého sloupce v řádku v pořadí, v jakém jsou definovány v primárním klíči.

Například tabulka pro kontakty má jméno, příjmení, telefonní číslo a adresu, a to vše ve stejné rodině sloupců. Můžete definovat primární klíč na základě rostoucího pořadového čísla:

|rowkey|       adresa|   Android| firstName| lastName|
|------|--------------------|--------------|-------------|--------------|
|  1000|1111 síť San Gabrielem Dr.|1-425-000-0002|    John|Dole|
|  8396|5415 síť San Gabrielem Dr.|1-230-555-0191|  Calvin|Raji|

Pokud se ale často dotazuje podle oprávnění lastName, nemusí tento primární klíč správně fungovat, protože každý dotaz vyžaduje, aby všechny dotazy načetly hodnotu každé příjmení. Místo toho můžete definovat primární klíč ve sloupcích lastName, firstName a číslo rodného čísla. Posledním sloupcem je nejednoznačnost dvou rezidentů na stejné adrese, jako je například otců a syn.

|rowkey|       adresa|   Android| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  1000|1111 síť San Gabrielem Dr.|1-425-000-0002|    John|Dole| 111 |
|  8396|5415 síť San Gabrielem Dr.|1-230-555-0191|  Calvin|Raji| 222 |

S tímto novým primárním klíčem klíče řádků generované v Phoenixu budou:

|rowkey|       adresa|   Android| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole – Jan až 111|1111 síť San Gabrielem Dr.|1-425-000-0002|    John|Dole| 111 |
|  Raji-Calvin-222|5415 síť San Gabrielem Dr.|1-230-555-0191|  Calvin|Raji| 222 |

V prvním řádku výše jsou data pro rowkey reprezentovaná, jak je znázorněno níže:

|rowkey|       key|   hodnota|
|------|--------------------|---|
|  Dole – Jan až 111|adresa |1111 síť San Gabrielem Dr.|  
|  Dole – Jan až 111|Android |1-425-000-0002|  
|  Dole – Jan až 111|firstName |John|  
|  Dole – Jan až 111|lastName |Dole|  
|  Dole – Jan až 111|socialSecurityNum |111|

Tato rowkey nyní ukládá duplicitní kopii dat. Vezměte v úvahu velikost a počet sloupců, které zahrnete do primárního klíče, protože tato hodnota je zahrnutá do každé buňky v podkladové tabulce HBA.

Také pokud má primární klíč hodnoty, které se rovnoměrně zvětšující, měli byste vytvořit tabulku s kontejnery *Salt* , abyste se vyhnuli vytváření hotspotů pro zápis – viz [data oddílu](#partition-data).

### <a name="column-family-design"></a>Návrh rodiny sloupců

Pokud jsou některé sloupce dostupné častěji než jiné, měli byste vytvořit více rodin sloupců, které oddělují často používané sloupce ze sloupců s zřídka použitou výjimkou.

Také pokud jsou k určitým sloupcům přicházet společně, umístěte tyto sloupce do stejné rodiny sloupců.

### <a name="column-design"></a>Návrh sloupce

* Ponechte sloupce VARCHAR pod přibližně 1 MB z důvodu nákladů na vstupně-výstupní operace velkých sloupců. Při zpracování dotazů vyplní materializuje buňky v plném rozsahu před jejich odesláním klientovi a klient je zaplní před tím, než ho dostanou do kódu aplikace.
* Uložte hodnoty sloupce pomocí kompaktního formátu, jako je například protobuf, Avro, msgpack nebo BSON. JSON se nedoporučuje, protože je větší.
* Zvažte komprimaci dat před úložištěm za účelem snížení latence a vstupně-výstupních nákladů.

### <a name="partition-data"></a>Dělení dat

Phoenix vám umožňuje řídit počet oblastí, ve kterých jsou vaše data distribuována, což může významně zvýšit výkon čtení a zápisu. Když vytváříte tabulku v Phoenixu, můžete data buď nasoleit, nebo předem rozdělit.

Chcete-li během vytváření nasoleit tabulku, zadejte počet sad Salt:

```sql
CREATE TABLE CONTACTS (...) SALT_BUCKETS = 16
```

Toto nasolení rozdělí tabulku podél hodnot primárních klíčů a automaticky zvolí hodnoty. 

Chcete-li určit, kde dojde k rozdělení tabulky, můžete tabulku před rozrozdělením zadáním hodnot rozsahu, podél kterých se rozdělení provádí. Chcete-li například vytvořit tabulku rozdělenou podél tří oblastí:

```sql
CREATE TABLE CONTACTS (...) SPLIT ON ('CS','EU','NA')
```

## <a name="index-design"></a>Návrh indexu

Index Phoenix je tabulka HBA, která ukládá kopii některých nebo všech dat z indexované tabulky. Index zvyšuje výkon pro konkrétní typy dotazů.

Pokud máte definováno více indexů a pak dotaz na tabulku, aplikace Phoenix automaticky vybere nejlepší index pro dotaz. Primární index se vytvoří automaticky v závislosti na vybraných primárních klíčích.

U předpokládaných dotazů můžete také vytvořit sekundární indexy zadáním jejich sloupců.

Při navrhování indexů:

* Vytvářejte pouze indexy, které potřebujete.
* Omezte počet indexů na často aktualizovaných tabulkách. Aktualizace tabulky se přeloží na zápisy do hlavní tabulky i tabulek indexu.

## <a name="create-secondary-indexes"></a>Vytváření sekundárních indexů

Sekundární indexy můžou zlepšit výkon při čtení tím, že se na základě toho, co by představovalo úplné prohledávání tabulky, provedou vyhledávání bodů, a to za cenu úložného prostoru a rychlosti zápisu. Sekundární indexy je možné přidat nebo odebrat po vytvoření tabulky a nevyžadují změny existujících dotazů – dotazy stačí spustit rychleji. V závislosti na vašich potřebách zvažte vytvoření zahrnutých indexů, funkčních indexů nebo obojího.

### <a name="use-covered-indexes"></a>Použití zahrnutých indexů

Zahrnuté indexy jsou indexy, které zahrnují data z řádku kromě indexovaných hodnot. Po nalezení požadované položky indexu není nutné přístup k primární tabulce.

Například v tabulce příklad kontaktu byste mohli vytvořit sekundární index pouze v socialSecurityNum sloupci. Tento sekundární index by urychlil dotazy, které filtrují podle hodnot socialSecurityNum, ale načítání ostatních hodnot polí bude vyžadovat další čtení v hlavní tabulce.

|rowkey|       adresa|   Android| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole – Jan až 111|1111 síť San Gabrielem Dr.|1-425-000-0002|    John|Dole| 111 |
|  Raji-Calvin-222|5415 síť San Gabrielem Dr.|1-230-555-0191|  Calvin|Raji| 222 |

Pokud ale obvykle chcete vyhledat pole firstName a lastName s daným socialSecurityNum, mohli byste vytvořit zahrnutý index, který obsahuje pole firstName a lastName jako skutečná data v tabulce index:

```sql
CREATE INDEX ssn_idx ON CONTACTS (socialSecurityNum) INCLUDE(firstName, lastName);
```

Tento zahrnutý index umožňuje, aby následující dotaz získal všechna data pouhým čtením z tabulky obsahující sekundární index:

```sql
SELECT socialSecurityNum, firstName, lastName FROM CONTACTS WHERE socialSecurityNum > 100;
```

### <a name="use-functional-indexes"></a>Použití funkčních indexů

Funkční indexy umožňují vytvořit index pro libovolný výraz, který očekáváte, že se bude používat v dotazech. Jakmile budete mít funkční index a dotaz použije tento výraz, může se index použít k načtení výsledků, nikoli k tabulce dat.

Můžete například vytvořit index, který vám umožní provádět hledání bez rozlišování velkých a malých písmen na základě kombinovaného křestního jména a příjmení osoby:

```sql
CREATE INDEX FULLNAME_UPPER_IDX ON "Contacts" (UPPER("firstName"||' '||"lastName"));
```

## <a name="query-design"></a>Návrh dotazu

Hlavními hledisky při návrhu dotazů jsou:

* Pochopení plánu dotazů a ověření očekávaného chování.
* Připojte se efektivně.

### <a name="understand-the-query-plan"></a>Pochopení plánu dotazu

V [SQLLine](http://sqlline.sourceforge.net/)použijte vysvětlení následovaný vaším dotazem SQL k zobrazení plánu operací, které bude provádět Phoenix. Ověřte, že plán:

* V případě potřeby použije primární klíč.
* Používá vhodné sekundární indexy místo tabulky dat.
* Nástroj používá kontrolu rozsahu nebo PŘESKOČENí kontroly, kdykoli je to možné, nikoli při prohledávání tabulky.

#### <a name="plan-examples"></a>Příklady plánů

Řekněme například, že máte tabulku s názvem lety, která ukládá informace o zpoždění letu.

Pokud chcete vybrat všechny lety s airlineid `19805` , kde airlineid je pole, které není v primárním klíči nebo v žádném indexu:

```sql
select * from "FLIGHTS" where airlineid = '19805';
```

Spusťte příkaz vysvětlit následujícím způsobem:

```sql
explain select * from "FLIGHTS" where airlineid = '19805';
```

Plán dotazu vypadá takto:

```sql
CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN FULL SCAN OVER FLIGHTS
   SERVER FILTER BY AIRLINEID = '19805'
```

V tomto plánu si poznamenejte frázi úplná kontrola nad lety. Tato fráze indikuje, že při provádění se v tabulce prohledává všechny řádky v tabulce místo použití možnosti zefektivnit kontrolu rozsahu nebo přeskočit kontrolu.

Nyní řekněme, že chcete zadat dotaz na lety 2. ledna 2014 pro přepravce, `AA` kde jeho flightnum bylo větší než 1. Řekněme, že sloupce year, month, DayOfMonth, přepravce a flightnum existují v tabulce příkladů a jsou všechny součástí složeného primárního klíče. Dotaz by vypadal takto:

```sql
select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;
```

Podívejme se na plán tohoto dotazu:

```sql
explain select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;
```

Výsledný plán:

```sql
CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER FLIGHTS [2014,1,2,'AA',2] - [2014,1,2,'AA',*]
```

Hodnoty v hranatých závorkách jsou rozsahem hodnot pro primární klíče. V tomto případě jsou hodnoty rozsahu opraveny s rokem 2014, měsíc 1 a DayOfMonth 2, ale umožňují hodnoty pro flightnum počínaje 2 a na začátku ( `*` ). Tento plán dotazu potvrdí, že se primární klíč používá podle očekávání.

Dále v tabulce lety vytvořte index s názvem `carrier2_idx` , který je pouze v poli přepravce. Tento index zahrnuje také flightdate, tailnum, Origin a flightnum jako zahrnuté sloupce, jejichž data jsou také uložená v indexu.

```sql
CREATE INDEX carrier2_idx ON FLIGHTS (carrier) INCLUDE(FLIGHTDATE,TAILNUM,ORIGIN,FLIGHTNUM);
```

Řekněme, že chcete získat přepravce spolu s flightdate a tailnum, jak je znázorněno v následujícím dotazu:

```sql
explain select carrier,flightdate,tailnum from "FLIGHTS" where carrier = 'AA';
```

Měli byste vidět, že se používá tento index:

```sql
CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER CARRIER2_IDX ['AA']
```

Úplný seznam položek, které se mohou objevit v tématu Vysvětlení výsledků plánu, najdete v části vysvětlující plány v příručce pro [ladění Apache Phoenix](https://phoenix.apache.org/tuning_guide.html).

### <a name="join-efficiently"></a>Efektivní připojení

Obecně platí, že chcete vyhnout spojení, pokud jedna strana není malá, zejména u častých dotazů.

V případě potřeby můžete s pomocným nástrojem provádět velké spojení `/*+ USE_SORT_MERGE_JOIN */` , ale velký počet spojení je náročná operace nad velkým počtem řádků. Pokud celková velikost všech tabulek na pravé straně by překročila dostupnou paměť, použijte `/*+ NO_STAR_JOIN */` pomocný parametr.

## <a name="scenarios"></a>Scénáře

Následující pokyny popisují některé běžné vzory.

### <a name="read-heavy-workloads"></a>Čtení – těžké úlohy

Pro případy použití s vysokým využitím se ujistěte, že používáte indexy. Kromě toho, pokud chcete ušetřit režijní náklady při čtení, zvažte vytvoření zahrnutých indexů.

### <a name="write-heavy-workloads"></a>Zátěžové úlohy náročné na zápis

Pro úlohy náročné na zápis, u kterých je primární klíč rovnoměrně zvětšující, vytvořte bloky Salt, které vám pomůžou vyhnout se psaní hotspotů, a to za cenu celkové propustnosti čtení z důvodu dalších potřebných kontrol. Také při použití UPSERT k zápisu velkého počtu záznamů vypněte automatické potvrzení a dávkujte záznamy.

### <a name="bulk-deletes"></a>Hromadné odstranění

Při odstraňování velkých datových sad zapněte funkci autocommit před vydáním dotazu DELETE, aby klient nemusel pamatovat klíče řádků pro všechny odstraněné řádky. Automatický zápis znemožňuje klientovi ukládat do vyrovnávací paměti řádky ovlivněné ODSTRANĚNÍm, aby je mohl v Phoenixu odstranit přímo na serverech oblastí bez nutnosti jejich vrácení klientovi.

### <a name="immutable-and-append-only"></a>Unmutable a pouze připojení

Pokud váš scénář přinese rychlost zápisu přes integritu dat, zvažte možnost zakázat protokol zápisu při vytváření tabulek:

```sql
CREATE TABLE CONTACTS (...) DISABLE_WAL=true;
```

Podrobnosti o této a dalších možnostech najdete v tématu [Apache Phoenix gramatiky](https://phoenix.apache.org/language/index.html#options).

## <a name="next-steps"></a>Další kroky

* [Průvodce optimalizací Apache Phoenix](https://phoenix.apache.org/tuning_guide.html)
* [Sekundární indexy](https://phoenix.apache.org/secondary_indexing.html)
