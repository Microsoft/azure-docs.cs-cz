---
title: Data zkosení problémy můžete vyřešit pomocí nástroje Azure Data Lake pro Visual Studio
description: Řešení potíží s možná řešení problémů zkosení dat pomocí nástroje Azure Data Lake pro Visual Studio.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
manager: kfile
editor: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 12/16/2016
ms.openlocfilehash: 43a050251a6945d6886930a2a76fcb9f833b78f0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623379"
---
# <a name="resolve-data-skew-problems-by-using-azure-data-lake-tools-for-visual-studio"></a>Data zkosení problémy můžete vyřešit pomocí nástroje Azure Data Lake pro Visual Studio

## <a name="what-is-data-skew"></a>Co je dat zkreslit?

Stručně jsme uvedli, zkosení dat je přepsání reprezentována hodnota. Představte si, zda jste přiřadili 50 referentů daň auditovat daň vrátí, jeden revizor pro každý stav USA. Wyoming referentů, protože naplňování existuje je malý, má málo udělat. V kalifornské ale zkoušející je udržováno velmi zaneprázdněny z důvodu stavu velké naplnění.
    ![Příklad dat zkosení problém](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/data-skew-problem.png)

V našem scénáři data se nerovnoměrně distribuuje mezi všechny daň kontrolorů, což znamená, že některé referentů musí fungovat více než jiné. Ve vaší vlastní úloze setkáte často situacích jako v příkladu daň revizor sem. V další technické podmínky jednoho vrcholu získá mnohem víc dat, než jeho partnerské uzly, situaci, která vytváří Vrchol pracovní víc než ostatní a že nakonec zpomaluje celé úlohy. Co je zhoršení, úloha se nemusí podařit, protože může mít vrcholy, například 5 hodin runtime omezení a omezení 6 GB paměti.

## <a name="resolving-data-skew-problems"></a>Řešení problémů s zkosení dat

Azure nástrojů Data Lake pro Visual Studio může pomoct zjistit, jestli vaše úlohy došlo k problému s zkosení data. Pokud existuje problém, abyste ho mohli vyřešit tak, že zkusíte řešení v této části.

## <a name="solution-1-improve-table-partitioning"></a>Řešení 1: Zlepšení, vytváření oddílů tabulky

### <a name="option-1-filter-the-skewed-key-value-in-advance"></a>Možnost 1: Předem filtrovat zkreslilo hodnota klíče

Pokud nemá vliv na obchodní logiku, můžete předem filtrovat hodnoty vyšší frekvence. Například pokud je celá řada 000 000 000 ve sloupci identifikátoru GUID, nemusí chcete agregovat tuto hodnotu. Předtím, než agregační, můžete napsat "kde GUID! ="000 000 000"" filtrovat hodnotu Vysoká frekvence.

### <a name="option-2-pick-a-different-partition-or-distribution-key"></a>Možnost 2: Vyberte jiný klíč oddílu, nebo distribuce

V předchozím příkladu Pokud chcete jenom zkontrolovat zatížení daň auditu po celém země, můžete zlepšit distribuci dat, výběrem číslo ID jako klíč. Výběr jiný oddíl nebo distribučního klíče můžete někdy distribuovat data více rovnoměrně, ale musíte zajistit, že tato volba nemá vliv obchodní logiky. Například k výpočtu daň součet pro každý stav, můžete určit _stavu_ jako klíč oddílu. Pokud budete pokračovat k tomuto problému dojde, použijte možnost 3.

### <a name="option-3-add-more-partition-or-distribution-keys"></a>Možnost 3: Přidejte další oddíl nebo distribučního klíče

Místo použití pouze _stavu_ jako klíč oddílu, můžete použít více než jeden klíč pro dělení. Zvažte například přidávání _PSČ_ jako klíčem další oddíl a snížení velikosti dat oddílu a více rovnoměrně distribuovat data.

### <a name="option-4-use-round-robin-distribution"></a>Možnost 4: Použití distribučních kruhového dotazování

Pokud nemůžete najít příslušný klíč pro oddíl a distribuci, můžete zkusit použít distribuci kruhového dotazování. Kruhové dotazování distribuční zpracovává všechny řádky stejně a náhodně umístí je do odpovídajících intervalů. Získá rovnoměrně data, ale ztratí polohu informace, nevýhodou, který může také snížit výkon úlohy pro některé operace. Kromě toho agregace zkreslilo klíče chcete přesto provést, problém zkosení dat zachová. Další informace o distribuci kruhového dotazování, najdete v části distribuce tabulky U-SQL v [CREATE TABLE (U-SQL): vytvoření tabulky se schématem](https://msdn.microsoft.com/library/mt706196.aspx#dis_sch).

## <a name="solution-2-improve-the-query-plan"></a>Řešení 2: Plán dotazu zlepšit

### <a name="option-1-use-the-create-statistics-statement"></a>Možnost 1: Použití příkazu CREATE STATISTICS

U-SQL obsahuje příkaz CREATE STATISTICS tabulky. Tento příkaz poskytuje další informace k Optimalizátor dotazů o data charakteristiky, třeba hodnotu rozdělení, které jsou uložené v tabulce. Pro většinu dotazů Optimalizátor dotazů již generuje nezbytné statistiku plán dotazu vysoké kvality. V některých případech budete muset zlepšit výkon dotazu tak, že vytvoříte další statistiky s CREATE STATISTICS nebo změnou návrhu dotazu. Další informace najdete v tématu [CREATE STATISTICS (U-SQL)](https://msdn.microsoft.com/library/azure/mt771898.aspx) stránky.

Příklad kódu:

    CREATE STATISTICS IF NOT EXISTS stats_SampleTable_date ON SampleDB.dbo.SampleTable(date) WITH FULLSCAN;

>[!NOTE]
>Statistické informace se automaticky neaktualizuje. Pokud aktualizujete data v tabulce bez nutnosti znovu vytvářet statistiku, může odmítnout výkon dotazů.

### <a name="option-2-use-skewfactor"></a>Možnost 2: Použijte SKEWFACTOR

Pokud chcete součet daň pro každý stav, musíte použít Seskupit podle stavu, postup, který není vyhnout potížím zkosení data. Však může poskytnout nápovědu dat v dotazu k identifikaci dat zkosení v klíče, aby Optimalizátor můžete připravit plán spuštění za vás.

Obvykle můžete nastavit parametr jako 0,5 a 1, s 0,5 znamená velkou zkosení nevěnuje význam zkosení a 1. Protože pomocný parametr ovlivňuje plán spuštění optimalizace pro aktuální příkaz a všechny podřízené příkazy, ujistěte se, že jste před potenciálně nesouměrně rozdělí key-wise agregace Přidat pomocný parametr.

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

### <a name="option-3-use-rowcount"></a>Možnost 3: Použití počtu řádků  
Kromě SKEWFACTOR konkrétní nesouměrně rozdělí klíč připojení v případech, pokud víte, že druhá sada připojené k řádku malé, můžete zjistit Optimalizátor přidáním pomocný parametr počtu řádků v příkazu U-SQL před spojení. Tímto způsobem můžete Optimalizátor všesměrového vysílání spojení strategie pro zlepšení výkonu. Uvědomte si, že atribut ROWCOUNT nepodaří data zkosení problém vyřešit, ale může nabídnout další pomoc.

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

## <a name="solution-3-improve-the-user-defined-reducer-and-combiner"></a>Řešení 3: Zlepšení uživatelem definované reduktorem a kombinační

Někdy může zapisovat uživatelem definovaný operátor řešit složité proces logiky a kvalitně reduktorem a kombinační může zmírnit data zkosení problém v některých případech.

### <a name="option-1-use-a-recursive-reducer-if-possible"></a>Možnost 1: Použití rekurzivní reduktorem, pokud je to možné

Ve výchozím nastavení uživatelem definované reduktorem běží v režimu tohoto nerekurzivního, což znamená, že snížit pracovní pro klíč je distribuován do jednoho vrcholu. Ale pokud vaše data se nesouměrně rozdělí, obrovských sad dat mohou být zpracovány v jednom vrchol a spustit po dlouhou dobu.

Chcete-li zvýšit výkon, můžete přidat atribut ve vašem kódu k definování reduktorem spouštění v režimu rekurzivní. Potom obrovských sad dat můžete distribuovat do více vrcholy a spustit souběžně, což urychlí úlohu.

Chcete-li změnit reduktorem tohoto nerekurzivního na rekurzivní, ujistěte se, že je vaše algoritmus asociativní. Například je součet asociativní a Medián není. Budete také muset Ujistěte se, že vstupní a výstupní pro reduktorem zachovat stejné schéma.

Atribut rekurzivní reduktorem:

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

### <a name="option-2-use-row-level-combiner-mode-if-possible"></a>Možnost 2: Použití režimu kombinační na úrovni řádků, pokud je to možné

Podobně jako pomocný parametr počtu řádků pro konkrétní připojení k nesouměrně rozdělí klíč případy, kombinační režimu se pokusí distribuovat nastaví velký nesouměrně rozdělí klíč hodnota k více vrcholy tak, aby práce mohou být provedeny souběžně. Režim kombinační nelze vyřešit problémy zkosení data, ale může nabídnout některé další nápovědu k nastaví velký nesouměrně rozdělí klíč hodnota.

Ve výchozím režimu kombinační je úplná, což znamená, že nemohou být odděleny sady řádků levého a pravého řádku sady. Nastavení režimu jako doleva nebo doprava nebo vnitřní umožňuje připojení k úrovni řádků. Systém odděluje odpovídající sady řádků a distribuuje je do více vrcholy, které spustit souběžně. Než začnete konfigurovat kombinační režimu, ale buďte opatrní zajistit, že je možné oddělit odpovídající sady řádků.

Následující příklad ukazuje sadu oddělených levého řádku. Každý řádek výstupu závisí na jednom řádku vstupní zleva a potenciálně závisí na všechny řádky z pravé se stejnou hodnotou klíče. Pokud nastavíte režim kombinační jako vlevo, systém odděluje obrovské doleva řádek nastavení do malé a přiřadí více vrcholy.

![Obrázek kombinační režimu](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/combiner-mode-illustration.png)

>[!NOTE]
>Pokud nastavíte režim nesprávný kombinační, kombinace sice méně efektivní a výsledky mohou být další potíže.

Atributy kombinační režimu:

- [SqlUserDefinedCombiner(Mode=CombinerMode.Full)]: Every output row potentially depends on all the input rows from left and right with the same key value.

- SqlUserDefinedCombiner(Mode=CombinerMode.Left): Každý řádek výstupu závisí na jeden vstupní řádek z doleva (a potenciálně všechny řádky z pravé se stejnou hodnotou klíče).

- qlUserDefinedCombiner(Mode=CombinerMode.Right): každý řádek výstupu závisí na jeden vstupní řádek z vpravo (a potenciálně všechny řádky z levé straně se stejnou hodnotou klíče).

- SqlUserDefinedCombiner(Mode=CombinerMode.Inner): Každý řádek výstupu závisí na jednom řádku vstupní vlevo a vpravo se stejnou hodnotou.

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
