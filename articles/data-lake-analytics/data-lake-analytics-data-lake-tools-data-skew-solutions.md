---
title: Vyřešit data – zkosit – Nástroje Azure Data Lake pro Visual Studio
description: Řešení problémů s případnými řešeními pro problémy s pozkosením dat pomocí Nástroje Azure Data Lake pro Visual Studio.
services: data-lake-analytics
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 12/16/2016
ms.openlocfilehash: 5e9359582c07a78feb2d04b2ea87f046887238a1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87129929"
---
# <a name="resolve-data-skew-problems-by-using-azure-data-lake-tools-for-visual-studio"></a>Řešení potíží s nerovnoměrnou distribucí dat pomocí Nástrojů Azure Data Lake pro Visual Studio

## <a name="what-is-data-skew"></a>Co je zkosení dat?

Krátce řečeno, zkosení dat je nadlimitní hodnota. Představte si, že jste přiřadili kontrolní průzkumy 50 pro audit vrácených daňových vratek, jeden kontroler pro každý stav USA. Wyoming prozkoumávání, protože populace má malý výskyt, má málo. V Kalifornii se však zkoušející udržuje velmi zaneprázdněný kvůli velkému naplnění populace stavu.
    ![Příklad problému s pozkosením dat](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/data-skew-problem.png)

V našem scénáři jsou data rovnoměrně distribuována napříč všemi daňovými průzkumy, což znamená, že některé průzkumy musí fungovat více než jiné. Ve vaší vlastní úloze často dochází k situaci, jako je příklad daňového přezkoumání. V rámci více technických podmínek jeden vrchol získá mnohem více dat než jejich partneři, což je situace, kdy vrchol funguje více než ostatní a kdy nakonec zpomaluje celou úlohu. Čím je horší, úloha může selhat, protože vrcholy můžou mít například omezení za běhu a velikost 6 GB paměti.

## <a name="resolving-data-skew-problems"></a>Řešení problémů s pozkosením dat

Nástroje Azure Data Lake pro Visual Studio může pomáhat zjistit, jestli má vaše úloha problém s časovým posunem dat. Pokud problém existuje, můžete ho vyřešit vyzkoušením řešení v této části.

## <a name="solution-1-improve-table-partitioning"></a>Řešení 1: vylepšení dělení tabulek

### <a name="option-1-filter-the-skewed-key-value-in-advance"></a>Možnost 1: vyfiltrování hodnoty zkresleného klíče předem

Pokud to nemá vliv na obchodní logiku, můžete hodnoty vyšších frekvencí filtrovat předem. Pokud je například identifikátor GUID sloupce hodně 000-000-000, možná nebudete chtít tuto hodnotu agregovat. Před agregací můžete napsat "WHERE GUID! =" 000-000-000 "" k filtrování hodnoty s vysokou frekvencí.

### <a name="option-2-pick-a-different-partition-or-distribution-key"></a>Možnost 2: vyberte jiný oddíl nebo distribuční klíč.

Pokud chcete, aby se v předchozím příkladu kontrolovaly jenom úlohy pro audit DPH přes zemi nebo oblast, můžete zlepšit distribuci dat tak, že jako svůj klíč vyberete číslo ID. Výběr jiného oddílu nebo distribučního klíče může někdy data distribuovat vícekrát, ale je nutné se ujistit, že tato volba nemá vliv na obchodní logiku. Chcete-li například vypočítat daňový součet pro každý stav, je vhodné určit _stav_ jako klíč oddílu. Pokud se tento problém bude opakovat, zkuste použít možnost 3.

### <a name="option-3-add-more-partition-or-distribution-keys"></a>Možnost 3: Přidání dalších oddílů nebo distribučních klíčů

Místo použití pouze _stavu_ jako klíče oddílu můžete pro dělení použít více než jeden klíč. Zvažte například přidání _PSČ_ jako dalšího klíče oddílu pro snížení velikosti oddílů dat a rovnoměrné distribuci dat.

### <a name="option-4-use-round-robin-distribution"></a>Možnost 4: použití distribuce s kruhovým dotazováním

Pokud pro oddíl a distribuci nemůžete najít vhodný klíč, můžete se pokusit použít distribuci kruhového dotazování. Distribuce kruhového dotazování zpracovává všechny řádky rovnoměrně a náhodně je umísťuje do odpovídajících sad. Data budou rovnoměrně distribuována, ale ztratí informace o velikosti, což je nevýhoda, která může také snížit výkon úlohy pro některé operace. Kromě toho, pokud provádíte agregaci pro zkosený klíč, bude problém s povýšení dat zachován. Další informace o distribuci s kruhovým dotazováním najdete v části distribuce tabulek U-SQL v [Create Table (U-SQL): vytvoření tabulky se schématem](/u-sql/ddl/tables/create/managed/create-table-u-sql-creating-a-table-with-schema#dis_sch).

## <a name="solution-2-improve-the-query-plan"></a>Řešení 2: vylepšení plánu dotazů

### <a name="option-1-use-the-create-statistics-statement"></a>Možnost 1: použití příkazu vytvořit STATISTIKu

U-SQL poskytuje příkaz CREATE STATISTICs pro tabulky. Tento příkaz poskytuje pro optimalizaci dotazů více informací o vlastnostech dat, jako je například rozdělení hodnoty, které jsou uloženy v tabulce. Pro většinu dotazů už Optimalizátor dotazů vygeneroval nezbytné statistiky pro vysoce kvalitní plán dotazů. V některých případech může být nutné zvýšit výkon dotazů vytvořením dalších statistik pomocí příkazu vytvořit STATISTIKu nebo úpravou návrhu dotazu. Další informace najdete na stránce [vytvoření statistiky (U-SQL)](/u-sql/ddl/statistics/create-statistics) .

Příklad kódu:

```usql
CREATE STATISTICS IF NOT EXISTS stats_SampleTable_date ON SampleDB.dbo.SampleTable(date) WITH FULLSCAN;
```

>[!NOTE]
>Informace o statistice se automaticky neaktualizují. Pokud aktualizujete data v tabulce bez opětovného vytváření statistik, může výkon dotazů odmítnout.

### <a name="option-2-use-skewfactor"></a>Možnost 2: použití SKEWFACTOR

Pokud chcete Vypočítat daň pro každý stav, musíte použít příkaz Seskupit podle stavu, přístup, který se vyhne problému s pozkosením dat. V dotazu však můžete zadat nápovědu k datům pro identifikaci zkosení dat v klíčích, aby Optimalizátor mohl připravit plán spouštění za vás.

Obvykle můžete nastavit parametr jako 0,5 a 1, přičemž 0,5 znamená, že není mnohem zkosený a 1 velké zešikmení. Vzhledem k tomu, že pomocný parametr má vliv na optimalizaci prováděcího plánu pro aktuální příkaz a všechny příkazy pro příjem dat, nezapomeňte přidat pomocný parametr před potenciálním zkoseným agregací klíče.

```usql
SKEWFACTOR (columns) = x
```

Poskytuje nápovědu, že dané sloupce mají faktor zkosení x z 0 (bez zešikmení) až 1 (velmi těžké zkosit).

Příklad kódu:

```usql
//Add a SKEWFACTOR hint.
@Impressions =
    SELECT * FROM
    searchDM.SML.PageView(@start, @end) AS PageView
    OPTION(SKEWFACTOR(Query)=0.5)
    ;
//Query 1 for key: Query, ClientId
@Sessions =
    SELECT
        ClientId,
        Query,
        SUM(PageClicks) AS Clicks
    FROM
        @Impressions
    GROUP BY
        Query, ClientId
    ;
//Query 2 for Key: Query
@Display =
    SELECT * FROM @Sessions
        INNER JOIN @Campaigns
            ON @Sessions.Query == @Campaigns.Query
    ;
```

### <a name="option-3-use-rowcount"></a>Možnost 3: použití ROWCOUNT
Pokud víte, že je ostatní propojená sada řádků malá, můžete pro určité případy spojené s kočárkami v kombinaci s SKEWFACTOR před připojením sdělit Optimalizátor přidáním parametru ROWCOUNT v příkazu U-SQL. Tímto způsobem může Optimalizátor vybrat strategii spojování všesměrového vysílání, která pomáhá zvýšit výkon. Počítejte s tím, že ROWCOUNT nedokáže vyřešit problém s časovým posunem dat, ale může nabízet další nápovědu.

```usql
OPTION(ROWCOUNT = n)
```

Zadáním odhadovaného počtu řádků na celé číslo určíte malou sadu řádků před připojením.

Příklad kódu:

```usql
//Unstructured (24-hour daily log impressions)
@Huge   = EXTRACT ClientId int, ...
            FROM @"wasb://ads@wcentralus/2015/10/30/{*}.nif"
            ;
//Small subset (that is, ForgetMe opt out)
@Small  = SELECT * FROM @Huge
            WHERE Bing.ForgetMe(x,y,z)
            OPTION(ROWCOUNT=500)
            ;
//Result (not enough information to determine simple broadcast JOIN)
@Remove = SELECT * FROM Bing.Sessions
            INNER JOIN @Small ON Sessions.Client == @Small.Client
            ;
```

## <a name="solution-3-improve-the-user-defined-reducer-and-combiner"></a>Řešení 3: vylepšení uživatelsky definovaného zpomalení a kombinování

Někdy můžete napsat uživatelem definovaný operátor, který bude zabývat se složitou procesní logikou, a v některých případech může zmírnit problémy s pozkosením dat.

### <a name="option-1-use-a-recursive-reducer-if-possible"></a>Možnost 1: použijte rekurzivní redukci, pokud je to možné

Ve výchozím nastavení se uživatelsky definované zpomalení spouští v nerekurzivním režimu, což znamená, že omezení práce pro klíč je distribuováno do jednoho vrcholu. Pokud jsou ale vaše data nakloněná, můžou se velké sady dat zpracovat v jednom vrcholu a spouštět po dlouhou dobu.

Chcete-li zvýšit výkon, můžete do kódu přidat atribut pro definování zpomalení pro spuštění v rekurzivním režimu. Velkou datovou sadu pak můžete distribuovat do několika vrcholů a spouštět paralelně, což zrychluje vaši úlohu.

Chcete-li změnit nerekurzivní redukci na rekurzivní, je nutné zajistit, aby byl algoritmus asociativní. Například suma je asociativní a medián není. Musíte také zajistit, aby vstup a výstup pro redukci zůstaly stejné schéma.

Atribut rekurzivního zúžení:

```usql
[SqlUserDefinedReducer(IsRecursive = true)]
```

Příklad kódu:

```usql
[SqlUserDefinedReducer(IsRecursive = true)]
public class TopNReducer : IReducer
{
    public override IEnumerable<IRow>
        Reduce(IRowset input, IUpdatableRow output)
    {
        //Your reducer code goes here.
    }
}
```

### <a name="option-2-use-row-level-combiner-mode-if-possible"></a>Možnost 2: Pokud je to možné, použijte režim kombinovaného režimu na úrovni řádků

Podobně jako pomocný parametr počtu řádků pro určité případy spojeného s zkoseným připojením se režim kombinovaného režimu pokusí distribuovat velmi hodnoty zkosených hodnot s klíčem na více vrcholů, aby bylo možné práci spustit současně. Režim kombinovaného spojení nemůže vyřešit problémy s časovým posunem dat, ale může nabízet další nápovědu pro velké hodnoty zkosených hodnot klíčů.

Režim kombinovaného režimu je ve výchozím nastavení plný, což znamená, že nelze oddělit levou sadu řádků a pravé sady řádků. Nastavení režimu jako levé/pravé/vnitřní umožňuje připojení na úrovni řádků. Systém odděluje odpovídající sady řádků a distribuuje je do několika vrcholů, které běží paralelně. Před konfigurací režimu kombinovaného přizpůsobování ale buďte opatrní, abyste se ujistili, že je možné odpovídající sady řádků oddělit.

Následující příklad ukazuje sadu odděleného levého řádku. Každý výstupní řádek závisí na jednom vstupním řádku zleva a potenciálně závisí na všech řádcích vpravo se stejnou hodnotou klíče. Pokud nastavíte režim kombinovaného režimu vlevo, systém oddělí velký levý řádek nastavený na malý a přiřadí ho k více vrcholům.

![Obrázek režimu kombinovaného spojení](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/combiner-mode-illustration.png)

>[!NOTE]
>Pokud nastavíte špatný režim kombinující, kombinace je méně efektivní a výsledky můžou být chybné.

Atributy režimu kombinovaného spojení:

- SqlUserDefinedCombiner (režim = CombinerMode. Full): každý výstupní řádek potenciálně závisí na všech vstupních řádcích zleva a vpravo se stejnou hodnotou klíče.

- SqlUserDefinedCombiner (režim = CombinerMode. Left): každý výstupní řádek závisí na jednom vstupním řádku zleva (a potenciálně všechny řádky napravo se stejnou hodnotou klíče).

- qlUserDefinedCombiner (režim = CombinerMode. Right): každý výstupní řádek závisí na jednom vstupním řádku zprava (a potenciálně všechny řádky vlevo se stejnou klíčovou hodnotou).

- SqlUserDefinedCombiner (režim = CombinerMode. Inner): každý výstupní řádek závisí na jednom vstupním řádku zleva a na pravé straně se stejnou hodnotou.

Příklad kódu:

```usql
[SqlUserDefinedCombiner(Mode = CombinerMode.Right)]
public class WatsonDedupCombiner : ICombiner
{
    public override IEnumerable<IRow>
        Combine(IRowset left, IRowset right, IUpdatableRow output)
    {
    //Your combiner code goes here.
    }
}
```
