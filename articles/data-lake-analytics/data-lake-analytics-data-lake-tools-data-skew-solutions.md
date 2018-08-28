---
title: Vyřešit problémy – Nerovnoměrná distribuce dat pomocí Azure Data Lake Tools pro Visual Studio
description: Řešení potíží s možnými řešeními pro problémy – Nerovnoměrná distribuce dat pomocí Azure Data Lake Tools pro Visual Studio.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 12/16/2016
ms.openlocfilehash: b3079a7f2e71e26164d96cf167b67f1a60f7a23b
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43046469"
---
# <a name="resolve-data-skew-problems-by-using-azure-data-lake-tools-for-visual-studio"></a>Vyřešit problémy – Nerovnoměrná distribuce dat pomocí Azure Data Lake Tools pro Visual Studio

## <a name="what-is-data-skew"></a>Co je data zkosení?

Krátce jsme uvedli, Nerovnoměrná distribuce dat je nadměrně zastoupené hodnota. Představte si, že jste přiřadili 50 referentů daně auditovat vrátí daně, jeden zkoušející pro každý stav USA. Zkoušející Wyoming vzhledem k tomu, že je naplnění existuje malá, má málo provést. V akci ale zkoušející zůstane velmi vytížený kvůli velké zdravotního stavu.
    ![Příklad problému Nerovnoměrná distribuce dat](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/data-skew-problem.png)

V tomto scénáři data nerovnoměrně distribuovaná napříč všechny daně kontrolorů, což znamená, že některé referentů musí fungovat více než jiné. Vlastní úlohy často nastat situace, stejně jako u příkladu daně zkoušející tady. V další technické podmínky jeden vrchol získá mnohem většímu množství dat než jeho partnerské uzly, situaci, která vytvoří Vrchol pracovní více než ostatní a že nakonec může zpomalit celý projekt. Co je horší, může úloha selhat, protože může mít vrcholy, třeba o 5 hodin runtime omezení a omezení 6 GB paměti.

## <a name="resolving-data-skew-problems"></a>Řešení potíží s Nerovnoměrná distribuce dat

Azure Data Lake Tools pro Visual Studio může pomoct zjistit, jestli má vaše úloha Nerovnoměrná distribuce dat problému. Pokud existuje problém, ho mohli vyřešit tak, že zkusíte řešení v této části.

## <a name="solution-1-improve-table-partitioning"></a>Řešení 1: Zvýšení dělení tabulky

### <a name="option-1-filter-the-skewed-key-value-in-advance"></a>Možnost 1: Předem filtrovat výrazně nerovnoměrnou distribucí hodnota klíče

Pokud to nemá vliv na obchodní logiku, můžete filtrovat hodnoty vyšší frekvence předem. Například pokud existuje velké množství 000-000-000 ve sloupci identifikátoru GUID, nemusí chcete agregovat tuto hodnotu. Před agregace, můžete psát "kde GUID! ="000-000-000"" filtrování hodnotu vysokou frekvencí.

### <a name="option-2-pick-a-different-partition-or-distribution-key"></a>Možnost 2: Vyberte jiný klíč oddílu nebo distribuce

V předchozím příkladu Pokud chcete jenom zkontrolovat daně auditu zatížení po zemi, můžete zlepšit rozdělení dat tak, že vyberete identifikační číslo jako svůj klíč. Vybrat jiný oddíl nebo klíč rozdělení může někdy rovnoměrnější distribuci dat, ale potřebujete, abyste měli jistotu, že tato volba nemá vliv na obchodní logiku. Například k výpočtu daně součtu pro každý stav, můžete chtít určit _stavu_ jako klíč oddílu. Pokud budete pokračovat k tomuto problému dochází, zkuste použít možnost 3.

### <a name="option-3-add-more-partition-or-distribution-keys"></a>Možnost 3: Přidejte další klíče oddílů nebo distribuce

Namísto použití pouze _stavu_ jako klíč oddílu, můžete použít více než jeden klíč pro dělení. Představte si třeba přidání _PSČ_ jako klíčem další oddíl pro snížení velikosti dat oddílu a více rovnoměrně distribuovat data.

### <a name="option-4-use-round-robin-distribution"></a>Možnost 4: Použití kruhové dotazování na distribuci

Pokud nemůžete najít vhodného klíče oddílu a distribuci, můžete použít kruhové dotazování na distribuci. Kruhové dotazování na distribuci stejně zpracovává všechny řádky a náhodně je zařadí do příslušné kbelíků. Získá data rovnoměrně distribuovat, ale ztratí informace o lokalitě, navracení, který může také snížit výkon úlohy pro některé operace. Kromě toho pokud provádíte agregace pro asymetrické klíče i přesto, problém Nerovnoměrná distribuce dat se zachová. Další informace o kruhové dotazování na distribuci, naleznete v části rozdělení tabulky U-SQL v [CREATE TABLE (U-SQL): vytvoření tabulky se schématem](https://msdn.microsoft.com/library/mt706196.aspx#dis_sch).

## <a name="solution-2-improve-the-query-plan"></a>Řešení 2: Vylepšení plán dotazu

### <a name="option-1-use-the-create-statistics-statement"></a>Možnost 1: Použití příkazu CREATE STATISTICS

U-SQL poskytuje příkazu CREATE STATISTICS na tabulkách. Tento příkaz poskytuje další informace k optimalizátoru dotazů charakteristiky data, jako je například distribuce hodnoty, které jsou uložené v tabulce. Pro většinu dotazů generuje optimalizátoru dotazů již potřebné statistické údaje o plán vysoké kvality dotazu. V některých případech můžete potřebovat pro zlepšení výkonu dotazů tím, že vytvoříte další statistiky s CREATE STATISTICS nebo úpravou návrh dotazu. Další informace najdete v tématu [CREATE STATISTICS (U-SQL)](https://msdn.microsoft.com/library/azure/mt771898.aspx) stránky.

Příklad kódu:

    CREATE STATISTICS IF NOT EXISTS stats_SampleTable_date ON SampleDB.dbo.SampleTable(date) WITH FULLSCAN;

>[!NOTE]
>Statistické informace se automaticky aktualizují. Při aktualizaci dat v tabulce bez nutnosti znovu vytvářet statistiky, může odmítnout výkon dotazů.

### <a name="option-2-use-skewfactor"></a>Možnost 2: Použití SKEWFACTOR

Pokud chcete součet daně pro každý stav, je nutné použít Seskupit podle stavu, přístup, který není vyhnout potížím Nerovnoměrná distribuce dat. Můžete však poskytnout nápovědu data v dotazu k identifikaci Nerovnoměrná distribuce dat v klíčích tak, aby Optimalizátor můžete připravit plán provádění za vás.

Obvykle můžete nastavit parametr jako 0,5 a 1, kde 0,5 znamená zkosení náročné nevěnuje význam nerovnoměrné rozdělení a 1. Protože pomocný parametr má vliv Optimalizace plánu spuštění pro aktuální příkaz a všech podřízených příkazů, je potřeba přidat před potenciál zešikmená zkosením agregace.

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
Kromě SKEWFACTOR konkrétní zkosený klíč spojení v případech, pokud víte, že druhá sada řádků připojené k doméně je malý, poznáte Optimalizátor přidáním pomocný parametr počtu řádků v příkazu U-SQL před spojení. Tímto způsobem, optimalizátor může volba strategie všesměrového vysílání spojení ke zlepšení výkonu. Mějte na paměti, počtu řádků Nerovnoměrná distribuce dat problém nevyřeší, ale může nabídnout další pomoc.

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

## <a name="solution-3-improve-the-user-defined-reducer-and-combiner"></a>Řešení 3: Zlepšení uživatelského redukční funkci a kombinační

Někdy můžete napsat uživatelem definovaný operátor vypořádat s logikou složitý proces, a kvalitně napsané redukční funkci a kombinační může zmírnit potíže Nerovnoměrná distribuce dat v některých případech.

### <a name="option-1-use-a-recursive-reducer-if-possible"></a>Možnost 1: Použijte rekurzivní redukční, pokud je to možné

Ve výchozím nastavení spouští redukční se uživatelem definované v režimu nerekurzivní, což znamená, že snížit objem práce pro klíč je distribuován do jednoho vrcholu. Ale pokud je zkosený vaše data, může být obrovských sad dat zpracovaných za jeden vrchol a spusťte po dlouhou dobu.

Ke zlepšení výkonu, můžete přidat atribut v kódu k definování redukční funkci pro spuštění v režimu rekurzivní. Obrovských sad dat potom můžete distribuovány do více vrcholy a běží paralelně, které urychlí vaši úlohu.

Chcete-li změnit rekurzivní redukční funkci nerekurzivní, ujistěte se, že algoritmus je asociativní. Například součet je asociativní a Medián není. Musíte také zajistit, že vstup a výstup pro redukční funkci zachovat stejné schéma.

Atribut rekurzivní redukční funkci:

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

### <a name="option-2-use-row-level-combiner-mode-if-possible"></a>Možnost 2: Použijte režim kombinační funkce na úrovni řádků, pokud je to možné

Podobně jako pomocný parametr počtu řádků pro konkrétní připojení k zkosený klíč případy režim kombinační funkce se pokusí distribuovat obrovské zkosený klíč hodnota nastaví několik vrcholů tak, aby práce mohou být provedeny souběžně. Režim kombinační funkce nelze vyřešit problémy – Nerovnoměrná distribuce dat, ale může nabídnout další pomoc pro velkou zkosený klíč hodnota sady.

Výchozí režim kombinační funkce je úplná, což znamená, že nemohou být odděleny sady řádků levého a pravého řádku sady. Nastavení režimu jako doleva nebo doprava/vnitřní umožňuje spojení na úrovni řádků. Systém odděluje odpovídající sady řádků a distribuuje do více vrcholy, které běží paralelně. Než začnete konfigurovat režim kombinační funkce, ale buďte opatrní a zajistit, že je možné oddělit odpovídající sady řádků.

Následující příklad ukazuje sadu oddělených levém řádku. Každý řádek výstupu závisí na jeden vstupní řádek z levé strany a potenciálně závisí na všechny řádky z pravé strany se stejnou hodnotou klíče. Pokud nastavíte režim kombinační funkce jako levou, systém odděluje obrovské vlevo – řádek nastavit do malé a přiřadí několik vrcholů.

![Obrázek režim kombinační](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/combiner-mode-illustration.png)

>[!NOTE]
>Pokud nastavíte režim nesprávné kombinační funkce, kombinace je méně efektivní a výsledky mohou být další potíže.

Atributy režim kombinační funkce:

- [SqlUserDefinedCombiner(Mode=CombinerMode.Full)]: Every output row potentially depends on all the input rows from left and right with the same key value.

- SqlUserDefinedCombiner(Mode=CombinerMode.Left): Každý řádek výstupu závisí na jeden vstupní řádek vlevo (a potenciálně všechny řádky z pravé strany se stejnou hodnotou klíče).

- qlUserDefinedCombiner(Mode=CombinerMode.Right): každý řádek výstupu závisí na jeden vstupní řádek z pravé straně (a potenciálně všechny řádky z levé strany se stejnou hodnotou klíče).

- SqlUserDefinedCombiner(Mode=CombinerMode.Inner): Každý řádek výstupu závisí na jeden vstupní řádek vlevo a vpravo se stejnou hodnotou.

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
