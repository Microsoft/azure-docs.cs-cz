---
title: Dotazování na graf dvojčat
titleSuffix: Azure Digital Twins
description: Informace najdete v tématu Postup dotazování grafu s dvojitou podseznamem digitálních vláken Azure.
author: baanders
ms.author: baanders
ms.date: 11/19/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: 3fd504ec36abae3f00cd2a7eb4e1f7b639be0cea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462673"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Dotazování na vyzdvojený graf digitálních vláken Azure

Tento článek nabízí příklady dotazů a podrobnější pokyny k použití **jazyka dotazů digitálních vláken Azure** k dotazování [na informace](concepts-twins-graph.md) . (Úvod do dotazovacího jazyka a úplný seznam jeho funkcí najdete v tématu [*Koncepty: dotazovací jazyk*](concepts-query-language.md).)

Tento článek začíná vzorovými dotazy, které ilustrují strukturu dotazovacího jazyka a běžné operace dotazů pro digitální vlákna. Pak popisuje, jak spustit dotazy po jejich zápisu pomocí [rozhraní API pro dotazování](/rest/api/digital-twins/dataplane/query) digitálních vláken Azure nebo [sady SDK](how-to-use-apis-sdks.md#overview-data-plane-apis).

> [!NOTE]
> Pokud používáte ukázkové dotazy níže s voláním rozhraní API nebo SDK, budete muset text dotazu zhuštěnit na jeden řádek.

## <a name="show-all-digital-twins"></a>Zobrazit všechny digitální vlákna

Tady je základní dotaz, který vrátí seznam všech digitálních vláken v instanci:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="GetAllTwins":::

## <a name="query-by-property"></a>Dotaz podle vlastnosti

Získat digitální vlákna podle **vlastností** (včetně ID a metadat):

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty1":::

Jak je znázorněno v dotazu výše, ID digitálního vlákna je dotazováno pomocí pole metadata `$dtId` .

>[!TIP]
> Pokud používáte Cloud Shell pro spuštění dotazu s poli metadat, která začínají `$` na, měli byste řídicí znak s zpětným lomítkem nechat Cloud Shell, že se nejedná `$` o proměnnou a měl by být spotřebovaný jako literál v textu dotazu.

Můžete také získat vlákna na základě **toho, zda je definována určitá vlastnost**. Tady je dotaz, který vrací vlákna, která mají definovanou vlastnost *Location* :

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty2":::

To vám může přispět k získání vláken podle jejich vlastností *značek* , jak je popsáno v tématu [Přidání značek do digitálních vláken](how-to-use-tags.md). Tady je dotaz, který získá všechny vlákna označené *červeně*:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

Můžete také získat vlákna na základě **typu vlastnosti**. Tady je dotaz, který získá vlákna, jejichž vlastnost *teploty* je číslo:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty3":::

>[!TIP]
> Pokud je vlastnost typu `Map` , můžete použít klíče a hodnoty mapy přímo v dotazu, například takto:
> :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty4":::

## <a name="query-by-model"></a>Dotaz podle modelu

`IS_OF_MODEL`Operátor lze použít k filtrování na základě [**modelu**](concepts-models.md)vlákna.

Bere v úvahu [Dědičnost](concepts-models.md#model-inheritance) a [správu verzí](how-to-manage-model.md#update-models)modelů a vyhodnocuje hodnotu true pro danou dvojitou podmínku, pokud je **splněna** jedna z těchto podmínek:

* Vlákna přímo implementuje model poskytovaný do `IS_OF_MODEL()` a číslo verze modelu na vlákna je *větší nebo rovno* číslu verze poskytnutého modelu.
* Zdvojený implementuje model, který *rozšiřuje* daný model `IS_OF_MODEL()` a číslo verze rozšířeného modelu je *větší než nebo rovno* číslu verze poskytnutého modelu.

Pokud například použijete dotaz na vlákna v modelu `dtmi:example:widget;4` , dotaz vrátí všechny vlákna založené na **verzi 4 nebo vyšší** modelu **widgetu** a také vytvoří vlákna na základě verze **4 nebo vyšší** z **modelů, které dědí z widgetu**.

`IS_OF_MODEL` může mít několik různých parametrů a zbytek této části je vyhrazen pro různé možnosti přetížení.

Nejjednodušší použití `IS_OF_MODEL` přebírá pouze `twinTypeName` parametr: `IS_OF_MODEL(twinTypeName)` .
Tady je příklad dotazu, který předává hodnotu v tomto parametru:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel1":::

Chcete-li zadat dvojitou kolekci, která bude prohledána, je-li použit více než jeden (například při `JOIN` použití), přidejte `twinCollection` parametr: `IS_OF_MODEL(twinCollection, twinTypeName)` .
Tady je příklad dotazu, který přidá hodnotu pro tento parametr:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel2":::

Chcete-li provést přesnou shodu, přidejte `exact` parametr: `IS_OF_MODEL(twinTypeName, exact)` .
Tady je příklad dotazu, který přidá hodnotu pro tento parametr:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel3":::

Můžete také předat všechny tři argumenty společně: `IS_OF_MODEL(twinCollection, twinTypeName, exact)` .
Tady je příklad dotazu, který určuje hodnotu pro všechny tři parametry:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel4":::

## <a name="query-by-relationship"></a>Dotaz podle vztahu

Při dotazování na základě **vztahů** digitálních vláken má dotazovací jazyk pro digitální vlákna Azure speciální syntaxi.

Relace jsou načteny do oboru dotazu v klauzuli `FROM`. Důležité rozlišení od "klasických" jazyků typu SQL je to, že každý výraz v této `FROM` klauzuli není tabulka. místo toho `FROM` klauzule vyjadřuje křížové vztahy mezi entitami a je zapsána ve verzi služby Azure Digital revlákens `JOIN` .

Zavoláte se s možnostmi modelu digitálních vláken Azure, relace neexistují nezávisle na zdvojených [objektech](concepts-models.md) . To znamená, že `JOIN` dotazovacího jazyka Azure Digital Twins se trochu liší od `JOIN` obecného SQL, protože na relace se zde nelze dotazovat nezávisle a musí být vázány na dvojče.
Za účelem začlenění tohoto rozdílu se v klauzuli `JOIN` používá klíčové slovo `RELATED` pro odkazování na sadu relací dvojčete.

Následující část obsahuje několik příkladů toho, co vypadá.

> [!TIP]
> V koncepčním důsledku Tato funkce napodobuje funkci CosmosDB orientované na dokumenty, kde `JOIN` lze provádět na podřízených objektech v dokumentu. CosmosDB používá `IN` klíčové slovo k označení toho, že má `JOIN` iterovat přes prvky pole v rámci aktuálního kontextu dokumentu.

### <a name="relationship-based-query-examples"></a>Příklady dotazů založených na relacích

Chcete-li získat datovou sadu, která obsahuje relace, použijte jeden `FROM` příkaz následovaný N `JOIN` příkazy N, kde `JOIN` příkazy Express na výsledek předchozího `FROM` nebo `JOIN` příkazu.

Tady je ukázkový dotaz založený na relacích. Tento fragment kódu vybere všechny digitální vlákna s vlastností *ID* ABC a všechny digitální vlákna, které souvisejí s těmito digitálními podmnožinami prostřednictvím relace *obsahující* .

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByRelationship1":::

> [!NOTE]
> Vývojář není muset korelovat `JOIN` s hodnotou klíče v `WHERE` klauzuli (nebo zadat hodnotu klíče vloženou s `JOIN` definicí). Tato korelace se počítá automaticky systémem, protože samotné vlastnosti relace identifikují cílovou entitu.

### <a name="query-the-properties-of-a-relationship"></a>Dotazování vlastností relace

Podobně jako mají digitální dvojčata vlastnosti popisované pomocí DTDL i relace mohou mít vlastnosti. Můžete se dotazovat na vlákna na **základě vlastností jejich vztahů**.
Jazyk dotazů digitálních vláken Azure umožňuje filtrování a projekci vztahů přiřazením aliasu k relaci v rámci `JOIN` klauzule.

Můžete například zvážit vztah *servicedBy* , který má vlastnost *reportedCondition* . V níže uvedeném dotazu je tomuto vztahu přiřazen alias R, aby odkazoval na jeho vlastnost.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByRelationship2":::

V předchozím příkladu si všimněte, že *reportedCondition* je vlastnost samotného vztahu *servicedBy* (ne některé digitální vlákna, která má vztah *servicedBy* ).

### <a name="query-with-multiple-joins"></a>Dotaz s více spojeními

`JOIN`V jednom dotazu je podporováno až pět s. To umožňuje procházet více úrovní vztahů najednou.

Zde je příklad dotazu s vícenásobnými spojeními, který získá všechny žárovky obsažené na světelných panelech v místnostech 1 a 2.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByRelationship3":::

## <a name="count-items"></a>Počet položek

Počet položek v sadě výsledků můžete spočítat pomocí `Select COUNT` klauzule:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectCount1":::

Přidejte `WHERE` klauzuli pro zjištění počtu položek, které splňují určitá kritéria. Tady je několik příkladů, jak počítat s použitým filtrem na základě typu dvojitě známého modelu (Další informace najdete v tématu [*dotaz podle modelu*](#query-by-model) níže):

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectCount2":::

`COUNT`Spolu s klauzulí můžete také použít `JOIN` . Tady je dotaz, který počítá všechny žárovky obsažené v světelných panelech místností 1 a 2:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectCount3":::

## <a name="filter-results-select-top-items"></a>Výsledky filtru: Vyberte horní položky.

Můžete vybrat několik "horních" položek v dotazu pomocí `Select TOP` klauzule.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectTop":::

## <a name="filter-results-specify-return-set-with-projections"></a>Výsledky filtru: určení návratové sady s projekcemi

Pomocí projekce v `SELECT` příkazu můžete zvolit, které sloupce bude dotaz vracet.

>[!NOTE]
>V současné době nejsou složité vlastnosti podporovány. Chcete-li zajistit, aby vlastnosti projekce byly platné, zkombinujte projekce s `IS_PRIMITIVE` kontrolou.

Tady je příklad dotazu, který pomocí projekce vrací vlákna a vztahy. Následující dotaz projektuje *spotřebitel*, *továrnu* a *hranu* z scénáře, kdy *továrna* s ID *ABC* souvisí s *příjemcem* prostřednictvím vztahu *Factory. Customer.* tento vztah je zobrazený jako *okraj*.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections1":::

Můžete také použít projekci k vrácení vlastnosti vlákna. Následující dotaz projektuje vlastnost *Name* pro *uživatele* , kteří se vztahují k *továrně* s ID *ABC* prostřednictvím vztahu objektu *Factory. Customer*.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections2":::

Můžete také použít projekci a vrátit vlastnost vztahu. Podobně jako v předchozím příkladu následující dotaz projektuje vlastnost *Name* *příjemců* souvisejících s *objektem Factory* s ID *ABC* prostřednictvím vztahu objektu *Factory. Customer.* nyní ale vrátí také dvě vlastnosti vztahu, *Prop1* a *prop2*. Provádí pojmenování *hraničních* vztahů a shromáždění jeho vlastností.  

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections3":::

Pomocí aliasů můžete také zjednodušit dotazy pomocí projekce.

Následující dotaz provede stejné operace jako v předchozím příkladu, ale aliasuje názvy vlastností do `consumerName` ,, `first` `second` a `factoryArea` .

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections4":::

Zde je podobný dotaz, který se dotazuje stejné sady jako v předchozím příkladu, ale projektuje pouze vlastnost *Consumer.Name* jako `consumerName` a projekty, které tvoří kompletní *objekt pro vytváření* , jako dvojitou hodnotu.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections5":::

## <a name="build-efficient-queries-with-the-in-operator"></a>Vytváření efektivních dotazů pomocí operátoru IN

Můžete významně snížit počet dotazů, které potřebujete, vytvořením pole dvojitých vláken a dotazování pomocí `IN` operátoru. 

Představte si například scénář, ve kterém *budovy* obsahují *podlahu* a *podlahu* , obsahují *místnosti*. Chcete-li hledat místnosti v rámci budovy, která jsou horká, jedním ze způsobů, jak postupovat podle těchto kroků.

1. Najděte podlahu v budově na základě `contains` vztahu.

    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="INOperatorWithout":::

2. Chcete-li najít místnosti místo toho, aby se v jednom z nich používal `JOIN` dotaz pro vyhledání místností pro každý z nich, můžete zadat dotaz pomocí kolekce podlah v budově (s názvem *Floor* v dotazu níže).

    V klientské aplikaci:
    
    ```csharp
    var floors = "['floor1','floor2', ..'floorn']"; 
    ```
    
    V dotazu:
    
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="INOperatorWith":::

## <a name="other-compound-query-examples"></a>Další příklady složených dotazů

Můžete **zkombinovat** libovolný z výše uvedených typů dotazu pomocí operátorů kombinace pro zahrnutí více podrobností v jednom dotazu. Tady jsou některé další příklady složených dotazů, které dotazují na více než jeden typ zdvojeného popisovače najednou.

* Ze zařízení, která jsou v *místnosti 123* , se vrátí zařízení MxChip, která obsluhují roli operátora.
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="OtherExamples1":::
* Získejte vlákna, která mají relaci s názvem, *obsahuje* další nevlákenný identifikátor *ID1*
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="OtherExamples2":::
* Získat všechny místnosti tohoto modelu místnosti, které jsou obsaženy v *floor11*
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="OtherExamples3":::

## <a name="run-queries-with-the-api"></a>Spouštění dotazů pomocí rozhraní API

Jakmile se rozhodnete pro řetězec dotazu, provedete to tak, že zavoláte [**rozhraní API pro dotazy**](/rest/api/digital-twins/dataplane/query).

Můžete zavolat rozhraní API přímo nebo použít jednu ze [sad SDK](how-to-use-apis-sdks.md#overview-data-plane-apis) dostupných pro digitální vlákna Azure.

Následující fragment kódu ilustruje volání [rozhraní .NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client) z klientské aplikace:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="RunQuery":::

Dotaz použitý v tomto volání vrátí seznam digitálních vláken, které výše uvedený příklad představuje s [BasicDigitalTwin](/dotnet/api/azure.digitaltwins.core.basicdigitaltwin) objekty. Návratový typ dat pro každý dotaz bude záviset na tom, jaké výrazy určíte pomocí `SELECT` příkazu:
* Dotazy, které začínají `SELECT * FROM ...` na, vrátí seznam digitálních vláken (které mohou být serializovány jako `BasicDigitalTwin` objekty nebo jiné vlastní digitální typy vláken, které jste pravděpodobně vytvořili).
* Dotazy, které začínají ve formátu, `SELECT <A>, <B>, <C> FROM ...` vrátí slovník s klíči `<A>` , `<B>` a `<C>` .
* Další formáty `SELECT` příkazů mohou být vytvořené pro vrácení vlastních dat. Můžete zvážit vytvoření vlastních tříd pro zpracování velmi přizpůsobených sad výsledků. 

### <a name="query-with-paging"></a>Dotaz se stránkováním

Volání na podporu stránkování. Tady je kompletní příklad použití `BasicDigitalTwin` jako typ výsledku dotazu s zpracováním chyb a stránkováním:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="FullQuerySample":::

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [rozhraních API a sadách SDK digitálních vláken Azure](how-to-use-apis-sdks.md), včetně rozhraní API pro dotazy, které se používá ke spouštění dotazů z tohoto článku.
