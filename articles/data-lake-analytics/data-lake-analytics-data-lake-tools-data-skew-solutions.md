---
title: Řešení zkosení dat – Nástroje datového jezera Azure pro Visual Studio
description: Řešení potíží s potenciálními řešeními problémů se zkosením dat pomocí nástrojů Azure Data Lake Tools pro Visual Studio.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 12/16/2016
ms.openlocfilehash: 9ff7ba5f04a8c1862f8ef136f8f3f6900f00a431
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71802555"
---
# <a name="resolve-data-skew-problems-by-using-azure-data-lake-tools-for-visual-studio"></a>Řešení potíží s nerovnoměrnou distribucí dat pomocí Nástrojů Azure Data Lake pro Visual Studio

## <a name="what-is-data-skew"></a>Co je zkosení dat?

Stručně uvedeno, zkosení dat je přereprezentovaná hodnota. Představte si, že jste přiřadili 50 daňových kontrolérů k auditu daňových přiznání, jeden zkoušející pro každý stát USA. Wyomingský zkoušející, protože tamní populace je malá, má málo co dělat. V Kalifornii, nicméně, zkoušející je stále velmi zaneprázdněn, protože stát je velká populace.
    ![Příklad problému se zkosením dat](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/data-skew-problem.png)

V našem scénáři jsou data nerovnoměrně rozložena mezi všechny daňové kontroloře, což znamená, že někteří průzkumoví referenti musí pracovat více než jiní. Ve své vlastní práci často dochází k situacím, jako je například daňový zkoušející. Z technického hlediska jeden vrchol získá mnohem více dat než jeho vrstevníci, což je situace, která činí vrchol pracovat více než ostatní a který nakonec zpomaluje celou práci. Co je horší, úloha může selhat, protože vrcholy mohou mít například 5hodinové omezení běhu a omezení paměti 6 GB.

## <a name="resolving-data-skew-problems"></a>Řešení problémů se zkosením dat

Nástroje datového jezera Azure pro Visual Studio můžou pomoct zjistit, jestli má vaše úloha problém se zkosením dat. Pokud problém existuje, můžete jej vyřešit pomocí řešení v této části.

## <a name="solution-1-improve-table-partitioning"></a>Řešení 1: Zlepšení dělení tabulek

### <a name="option-1-filter-the-skewed-key-value-in-advance"></a>Možnost 1: Předem filtrovat zkosenou hodnotu klíče

Pokud to nemá vliv na obchodní logiku, můžete předem filtrovat hodnoty s vyšší frekvencí. Například pokud existuje velké množství 000-000-000 ve sloupci GUID, pravděpodobně nebudete chtít agregovat tuto hodnotu. Před agregací můžete napsat "WHERE GUID != "000-000-000", abyste filtrovali vysokofrekvenční hodnotu.

### <a name="option-2-pick-a-different-partition-or-distribution-key"></a>Možnost 2: Vyberte jiný oddíl nebo distribuční klíč

V předchozím příkladu, pokud chcete pouze zkontrolovat zatížení daňového auditu v celé zemi nebo oblasti, můžete zlepšit distribuci dat výběrem id číslo jako klíč. Výběr jiného oddílu nebo distribučního klíče může někdy distribuovat data rovnoměrněji, ale je třeba se ujistit, že tato volba nemá vliv na obchodní logiku. Například pro výpočet součet daně pro každý stát, můžete chtít určit _stát_ jako klíč oddílu. Pokud se nadále setkáte s tímto problémem, zkuste použít možnost 3.

### <a name="option-3-add-more-partition-or-distribution-keys"></a>Možnost 3: Přidání dalších oddílových nebo distribučních klíčů

Namísto použití pouze _state_ jako klíč oddílu, můžete použít více než jeden klíč pro dělení. Zvažte například přidání _PSČ_ jako dalšího klíče oddílu, abyste zmenšili velikost oddílů dat a rovnoměrněji distribuovali data.

### <a name="option-4-use-round-robin-distribution"></a>Možnost 4: Použití distribuce kruhového dotazování

Pokud nemůžete najít vhodný klíč pro oddíl a distribuci, můžete zkusit použít distribuci kruhového dotazování. Distribuce kruhového dotazování zachází se všemi řádky stejně a náhodně je vloží do odpovídajících bloků. Data se rovnoměrně distribuují, ale ztratí informace o lokalitě, což je nevýhoda, která může také snížit výkon úlohy u některých operací. Navíc pokud provádíte agregaci pro zkosený klíč stejně, bude problém zkosení dat přetrvávat. Další informace o distribuci kruhového dotazování naleznete v části Distribuce tabulek U-SQL v [tématu CREATE TABLE (U-SQL): Vytvoření tabulky se schématem](/u-sql/ddl/tables/create/managed/create-table-u-sql-creating-a-table-with-schema#dis_sch).

## <a name="solution-2-improve-the-query-plan"></a>Řešení 2: Vylepšete plán dotazů

### <a name="option-1-use-the-create-statistics-statement"></a>Možnost 1: Použití příkazu VYTVOŘIT STATISTIKU

U-SQL poskytuje příkaz VYTVOŘIT STATISTIKU v tabulkách. Tento příkaz poskytuje další informace o optimalizaci dotazu o charakteristiky dat, jako je například rozdělení hodnoty, které jsou uloženy v tabulce. U většiny dotazů optimalizátor dotazů již generuje potřebné statistiky pro plán dotazů s vysokou kvalitou. V některých případě může být nutné zlepšit výkon dotazu vytvořením další statistiky pomocí create statistiky nebo úpravou návrhu dotazu. Další informace naleznete na stránce [VYTVOŘIT STATISTIKU (U-SQL).](/u-sql/ddl/statistics/create-statistics)

Příklad kódu:

    CREATE STATISTICS IF NOT EXISTS stats_SampleTable_date ON SampleDB.dbo.SampleTable(date) WITH FULLSCAN;

>[!NOTE]
>Statistické informace nejsou automaticky aktualizovány. Pokud aktualizujete data v tabulce bez opětovného vytvoření statistiky, může dojít ke snížení výkonu dotazu.

### <a name="option-2-use-skewfactor"></a>Možnost 2: Použití SKEWFACTOR

Pokud chcete sečíst daň pro každý stát, musíte použít GROUP BY stav, přístup, který se nevyhýbá problém zkosení dat. Můžete však poskytnout nápovědu k datům v dotazu k identifikaci zkosení dat v klíčích tak, aby optimalizátor můžete připravit plán spuštění pro vás.

Obvykle můžete nastavit parametr jako 0,5 a 1, s 0,5 znamená, že není moc zkosení a 1 znamená těžké zkosení. Vzhledem k tomu, že nápověda ovlivňuje optimalizaci plánu spuštění pro aktuální příkaz a všechny příkazy navazující, nezapomeňte přidat nápovědu před potenciální zkosené agregace po dobu po zkosený klíč.

    SKEWFACTOR (columns) = x

    Provides a hint that the given columns have a skew factor x from 0 (no skew) through 1 (very heavy skew).

Příklad kódu:

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

### <a name="option-3-use-rowcount"></a>Možnost 3: Použití rowcount  
Kromě SKEWFACTOR, pro konkrétní případy spojení se zkosený klíč, pokud víte, že další spojené řádek sada je malá, můžete říct optimalizátor přidáním rowcount nápovědy v příkazu U-SQL před JOIN. Tímto způsobem může optimalizátor zvolit strategii spojení vysílání, která pomůže zlepšit výkon. Uvědomte si, že ROWCOUNT nevyřeší problém zkosení dat, ale může nabídnout některé další pomoc.

    OPTION(ROWCOUNT = n)

    Identify a small row set before JOIN by providing an estimated integer row count.

Příklad kódu:

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

## <a name="solution-3-improve-the-user-defined-reducer-and-combiner"></a>Řešení 3: Zlepšete uživatelsky definovaný reduktor a kombinátor

Někdy můžete napsat uživatelem definovaný operátor pro řešení složité logiky procesu a dobře napsaný reduktor a kombinátor může v některých případech zmírnit problém s překřivěním dat.

### <a name="option-1-use-a-recursive-reducer-if-possible"></a>Možnost 1: Pokud je to možné, použijte rekurzivní reduktor

Ve výchozím nastavení je indosaktovace definovaná uživatelem spuštěna v nerekurzivním režimu, což znamená, že snížení práce pro klíč je distribuováno do jednoho vrcholu. Ale pokud jsou vaše data zkosená, obrovské sady dat mohou být zpracovány v jednom vrcholu a běžet po dlouhou dobu.

Chcete-li zvýšit výkon, můžete přidat atribut v kódu definovat reduktor spustit v rekurzivním režimu. Poté mohou být obrovské datové sady distribuovány do více vrcholů a spuštěny paralelně, což urychluje vaši úlohu.

Chcete-li změnit nerekurzivní reduktor na rekurzivní, musíte se ujistit, že váš algoritmus je asociativní. Součet je například asociativní a medián není. Také je třeba se ujistit, že vstup a výstup pro reduktor zachovat stejné schéma.

Atribut rekurzivního reduktoru:

    [SqlUserDefinedReducer(IsRecursive = true)]

Příklad kódu:

    [SqlUserDefinedReducer(IsRecursive = true)]
    public class TopNReducer : IReducer
    {
        public override IEnumerable<IRow>
            Reduce(IRowset input, IUpdatableRow output)
        {
            //Your reducer code goes here.
        }
    }

### <a name="option-2-use-row-level-combiner-mode-if-possible"></a>Možnost 2: Pokud je to možné, použijte režim slučovače na úrovni řádků

Podobně jako nápověda ROWCOUNT pro konkrétní případy spojení se zkosením klíče se režim slučovače pokusí distribuovat obrovské sady hodnot zkoseného klíče na více vrcholů, aby bylo možné pracovat souběžně. Režim combiner nemůže vyřešit problémy se zkosením dat, ale může nabídnout další nápovědu pro sady hodnot obrovských zkosených klíčů.

Ve výchozím nastavení je režim slučování plný, což znamená, že sadu levého řádku a sadu pravých řádků nelze oddělit. Nastavení režimu jako vlevo/vpravo/vnitřní umožňuje spojení na úrovni řádku. Systém odděluje odpovídající sady řádků a distribuuje je do více vrcholů, které běží paralelně. Před konfigurací režimu slučovače však buďte opatrní, abyste zajistili, že odpovídající sady řádků lze oddělit.

Následující příklad ukazuje samostatnou sadu levých řádků. Každý výstupní řádek závisí na jednom vstupním řádku zleva a potenciálně závisí na všech řádcích zprava se stejnou hodnotou klíče. Pokud nastavíte režim slučovače jako levý, systém rozdělí obrovskou levou řadu nastavenou na malé a přiřadí je k více vrcholům.

![Ilustrace režimu combiner](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/combiner-mode-illustration.png)

>[!NOTE]
>Pokud nastavíte nesprávný režim slomy, kombinace je méně efektivní a výsledky mohou být nesprávné.

Atributy režimu součů:

- SqlUserDefinedCombiner(Mode=CombinerMode.Full): Každý výstupní řádek potenciálně závisí na všech vstupních řádcích zleva a vpravo se stejnou hodnotou klíče.

- SqlUserDefinedCombiner(Mode=CombinerMode.Left): Každý výstupní řádek závisí na jednom vstupním řádku zleva (a potenciálně na všech řádcích zprava se stejnou hodnotou klíče).

- qlUserDefinedCombiner(Mode=CombinerMode.Right): Každý výstupní řádek závisí na jednom vstupním řádku zprava (a potenciálně na všech řádcích zleva se stejnou hodnotou klíče).

- SqlUserDefinedCombiner(Mode=CombinerMode.Inner): Každý výstupní řádek závisí na jednom vstupním řádku zleva a vpravo se stejnou hodnotou.

Příklad kódu:

    [SqlUserDefinedCombiner(Mode = CombinerMode.Right)]
    public class WatsonDedupCombiner : ICombiner
    {
        public override IEnumerable<IRow>
            Combine(IRowset left, IRowset right, IUpdatableRow output)
        {
        //Your combiner code goes here.
        }
    }
