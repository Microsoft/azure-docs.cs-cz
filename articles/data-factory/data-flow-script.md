---
title: Mapování skriptu toku dat
description: Přehled jazyka kódu Data Factoryho skriptu pro tok dat
author: kromerm
ms.author: nimoolen
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/29/2020
ms.openlocfilehash: 6802e3f6c0892993f9ffe4373f43274362b8a003
ms.sourcegitcommit: f796e1b7b46eb9a9b5c104348a673ad41422ea97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91569673"
---
# <a name="data-flow-script-dfs"></a>Skript toku dat (DFS)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Skript toku dat (DFS) je podkladová metadata, podobně jako kódovací jazyk, který se používá ke spouštění transformací, které jsou zahrnuty v toku dat mapování. Všechny transformace jsou reprezentovány řadou vlastností, které poskytují nezbytné informace pro správné spuštění úlohy. Skript je viditelný a upravitelný z ADF kliknutím na tlačítko "skript" na horním pásu karet uživatelského rozhraní prohlížeče.

![Tlačítko skriptu](media/data-flow/scriptbutton.png "Tlačítko skriptu")

Například `allowSchemaDrift: true,` ve zdrojové transformaci oznamuje službě, aby zahrnovala všechny sloupce ze zdrojové datové sady v toku dat, i když nejsou zahrnuté do projekce schématu.

## <a name="use-cases"></a>Případy použití
Systém souborů DFS je automaticky vytvořen uživatelským rozhraním. Kliknutím na tlačítko skript můžete zobrazit a přizpůsobit skript. Můžete také vygenerovat skripty mimo uživatelské rozhraní ADF a pak je předat do rutiny PowerShellu. Při ladění složitých toků dat může být snazší vyhledat kód skriptu, místo aby se kontrolovala reprezentace grafu uživatelského rozhraní.

Tady je několik příkladů případů použití:
- Programově vytváření mnoha toků dat, které jsou poměrně podobné, tj. toky dat "razítko".
- Složité výrazy, které se obtížně spravují v uživatelském rozhraní nebo mají za následek problémy s ověřením.
- Ladění a lepší porozumění různým chybám vráceným během provádění.

Když vytváříte skript toku dat pro použití s prostředím PowerShell nebo rozhraním API, je nutné sbalit formátovaný text na jeden řádek. Můžete ponechat tabulátory a newlines jako řídicí znaky. Ale text musí být formátovaný tak, aby se vešel do vlastnosti JSON. V dolní části uživatelského rozhraní editoru skriptů je tlačítko, ve kterém se skript naformátuje jako jeden řádek.

![Tlačítko pro kopírování](media/data-flow/copybutton.png "Tlačítko pro kopírování")

## <a name="how-to-add-transforms"></a>Postup přidání transformací
Přidání transformací vyžaduje tři základní kroky: Přidání základních transformačních dat, přesměrování vstupního datového proudu a následné přesměrování výstupního datového proudu. To se dá v příkladu zobrazit nejjednodušší.
Řekněme, že začneme s jednoduchým zdrojem pro tok dat, jako je následující:

```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Pokud se rozhodnete přidat odvozenou transformaci, nejdřív musíme vytvořit základní transformační text, který má jednoduchý výraz pro přidání nového sloupce velkými písmeny s názvem `upperCaseTitle` :
```
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
```

Pak převezmeme stávající DFS a přidáme transformaci:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

A teď přesměrujte příchozí datový proud tím, že určíte, kterou transformaci chceme, aby se nová transformace nacházela po (v tomto případě `source1` ) a kopírovala název streamu do nové transformace:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
source1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

Nakonec identifikujeme transformaci, kterou chceme přijít po této nové transformaci, a nahraďte její vstupní datový proud (v tomto případě `sink1` ) s názvem výstupního datového proudu naší nové transformace:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
source1 derive(upperCaseTitle = upper(title)) ~> deriveTransformationName
deriveTransformationName sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="dfs-fundamentals"></a>Základní informace o systému souborů DFS
DFS se skládá z řady propojených transformací, včetně zdrojů, umyvadel a různých dalších, které mohou přidávat nové sloupce, filtrovat data, spojovat data a mnoho dalšího. Skript obvykle začíná s jedním nebo více zdroji následovanými mnoha transformacemi a končí jednou nebo více jímkami.

Všechny zdroje mají stejnou základní konstrukci:
```
source(
  source properties
) ~> source_name
```

Například jednoduchý zdroj se třemi sloupci (movieId, title, žánry) by byl:
```
source(output(
        movieId as string,
        title as string,
        genres as string
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source1
```

Všechny transformace kromě zdrojů mají stejnou základní konstrukci:
```
name_of_incoming_stream transformation_type(
  properties
) ~> new_stream_name
```

Například jednoduchá odvozená transformace, která přebírá sloupec (title) a Přepisuje ho pomocí velké verze, by byla následující:
```
source1 derive(
  title = upper(title)
) ~> derive1
```

A jímka bez schématu by byla jednoduchá:
```
derive1 sink(allowSchemaDrift: true,
    validateSchema: false) ~> sink1
```

## <a name="script-snippets"></a>Fragmenty skriptů

Fragmenty skriptů jsou sdílet kód skriptu toku dat, který můžete použít ke sdílení napříč datovými toky. V tomto videu se dozvíte, jak používat fragmenty skriptů a použít skript toku dat ke zkopírování a vložení částí skriptu za vaše grafy toku dat:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tA9b]


### <a name="aggregated-summary-stats"></a>Agregované souhrnné statistiky
Přidejte agregační transformaci do toku dat s názvem "SummaryStats" a vložte následující kód níže pro agregační funkci ve skriptu a nahraďte existující SummaryStats. Tím se poskytne obecný vzor souhrnných statistik pro data Profile.

```
aggregate(each(match(true()), $$+'_NotNull' = countIf(!isNull($$)), $$ + '_Null' = countIf(isNull($$))),
        each(match(type=='double'||type=='integer'||type=='short'||type=='decimal'), $$+'_stddev' = round(stddev($$),2), $$ + '_min' = min ($$), $$ + '_max' = max($$), $$ + '_average' = round(avg($$),2), $$ + '_variance' = round(variance($$),2)),
        each(match(type=='string'), $$+'_maxLength' = max(length($$)))) ~> SummaryStats
```
Pomocí níže uvedeného příkladu můžete spočítat počet jedinečných řádků a počet jedinečných řádků ve vašich datech. Níže uvedený příklad lze vložit do toku dat pomocí agregační transformace s názvem ValueDistAgg. V tomto příkladu se používá sloupec s názvem "title". Nezapomeňte nahradit "title" sloupcem s řetězcem ve vašich datech, která chcete použít k získání počtů hodnot.

```
aggregate(groupBy(title),
    countunique = count()) ~> ValueDistAgg
ValueDistAgg aggregate(numofunique = countIf(countunique==1),
        numofdistinct = countDistinct(title)) ~> UniqDist
```

### <a name="include-all-columns-in-an-aggregate"></a>Zahrnout všechny sloupce v agregaci
Toto je obecný agregovaný vzor, který předvádí, jak můžete zachovat zbývající sloupce ve výstupních metadatech při vytváření agregací. V tomto případě používáme ```first()``` funkci k výběru první hodnoty v každém sloupci, jehož název není "film". Pokud to chcete použít, vytvořte agregovanou transformaci s názvem DistinctRows a vložte ji do skriptu nad existujícím agregovaným skriptem DistinctRows.

```
aggregate(groupBy(movie),
    each(match(name!='movie'), $$ = first($$))) ~> DistinctRows
```

### <a name="create-row-hash-fingerprint"></a>Vytvoření otisku hash řádku 
Použijte tento kód ve vašem skriptu toku dat k vytvoření nového odvozeného sloupce ```DWhash``` s názvem, který vytvoří ```sha1``` hodnotu hash se třemi sloupci.

```
derive(DWhash = sha1(Name,ProductNumber,Color))
```

Pomocí tohoto skriptu níže můžete vygenerovat hodnotu hash řádku pomocí všech sloupců, které se nachází ve vašem datovém proudu, aniž byste museli pojmenovat jednotlivé sloupce:

```
derive(DWhash = sha1(columns()))
```

### <a name="string_agg-equivalent"></a>Ekvivalent String_agg
Tento kód bude fungovat jako funkce T-SQL ```string_agg()``` a bude agregovat řetězcové hodnoty do pole. Toto pole pak můžete přetypovat na řetězec pro použití s cíli SQL.

```
source1 aggregate(groupBy(year),
    string_agg = collect(title)) ~> Aggregate1
Aggregate1 derive(string_agg = toString(string_agg)) ~> DerivedColumn2
```

### <a name="count-number-of-updates-upserts-inserts-deletes"></a>Počet aktualizací, upsertuje, vložení, odstranění
Když použijete transformaci ALTER Row, možná budete chtít spočítat počet aktualizací, upsertuje, vložení a odstraní je z vašich zásad ALTER Row. Přidejte agregační transformaci po změně řádku a vložte tento skript toku dat do agregované definice pro tyto počty.

```
aggregate(updates = countIf(isUpdate(), 1),
        inserts = countIf(isInsert(), 1),
        upserts = countIf(isUpsert(), 1),
        deletes = countIf(isDelete(),1)) ~> RowCount
```

### <a name="distinct-row-using-all-columns"></a>Řádek DISTINCT pomocí všech sloupců
Tento fragment kódu přidá novou agregovanou transformaci do toku dat, který převezme všechny příchozí sloupce, vygeneruje hodnotu hash, která se používá k seskupením, aby vyloučila duplicity, a pak poskytne první výskyt každého duplicitního jako výstup. Sloupce nemusíte explicitně pojmenovat, budou automaticky vygenerovány z příchozího datového proudu.

```
aggregate(groupBy(mycols = sha2(256,columns())),
    each(match(true()), $$ = first($$))) ~> DistinctRows
```

### <a name="check-for-nulls-in-all-columns"></a>Kontrolovat hodnoty NULL ve všech sloupcích
Toto je fragment kódu, který můžete vložit do toku dat a obecně kontrolovat všechny sloupce pro hodnoty NULL. Tato technika využívá posun schématu k prohlédnutí všech sloupců ve všech řádcích a používá podmíněné rozdělení k oddělení řádků s hodnotami NULL z řádků bez hodnot NULL. 

```
CreateColumnArray split(contains(array(columns()),isNull(#item)),
    disjoint: false) ~> LookForNULLs@(hasNULLs, noNULLs)
```

## <a name="next-steps"></a>Další kroky

Prozkoumejte toky dat od [článku Přehled toků dat](concepts-data-flow-overview.md)
