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
ms.openlocfilehash: df7462cf047dd113c34669d9a5f68f2589cc50f4
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2020
ms.locfileid: "97672989"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Dotazování na vyzdvojený graf digitálních vláken Azure

Tento článek nabízí příklady dotazů a podrobnější pokyny k použití **jazyka dotazů digitálních vláken Azure** k dotazování [na informace](concepts-twins-graph.md) . (Úvod do dotazovacího jazyka a úplný seznam jeho funkcí najdete v tématu [*Koncepty: dotazovací jazyk*](concepts-query-language.md).)

Tento článek začíná vzorovými dotazy, které ilustrují strukturu dotazovacího jazyka a běžné operace dotazů pro digitální vlákna. Pak popisuje, jak spustit dotazy po jejich zápisu pomocí [rozhraní API pro dotazování](/rest/api/digital-twins/dataplane/query) digitálních vláken Azure nebo [sady SDK](how-to-use-apis-sdks.md#overview-data-plane-apis).

> [!TIP]
> Pokud používáte ukázkové dotazy níže s voláním rozhraní API nebo SDK, budete muset text dotazu zhuštěnit na jeden řádek.

## <a name="show-all-digital-twins"></a>Zobrazit všechny digitální vlákna

Tady je základní dotaz, který vrátí seznam všech digitálních vláken v instanci:

```sql
SELECT *
FROM DIGITALTWINS
```

## <a name="query-by-property"></a>Dotaz podle vlastnosti

Získat digitální vlákna podle **vlastností** (včetně ID a metadat):

```sql
SELECT  *
FROM DigitalTwins T  
WHERE T.firmwareVersion = '1.1'
AND T.$dtId in ['123', '456']
AND T.Temperature = 70
```

> [!TIP]
> ID digitálního vlákna se dotazuje pomocí pole metadata `$dtId` .

Můžete také získat vlákna na základě **toho, zda je definována určitá vlastnost**. Tady je dotaz, který vrací vlákna, která mají definovanou vlastnost *Location* :

```sql
SELECT * FROM DIGITALTWINS WHERE IS_DEFINED(Location)
```

To vám může přispět k získání vláken podle jejich vlastností *značek* , jak je popsáno v tématu [Přidání značek do digitálních vláken](how-to-use-tags.md). Tady je dotaz, který získá všechny vlákna označené *červeně*:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_DEFINED(tags.red)
```

Můžete také získat vlákna na základě **typu vlastnosti**. Tady je dotaz, který získá vlákna, jejichž vlastnost *teploty* je číslo:

```sql
SELECT * FROM DIGITALTWINS T WHERE IS_NUMBER(T.Temperature)
```

## <a name="query-by-model"></a>Dotaz podle modelu

`IS_OF_MODEL`Operátor lze použít k filtrování na základě [**modelu**](concepts-models.md)vlákna.

Bere v úvahu [Dědičnost](concepts-models.md#model-inheritance) a [správu verzí](how-to-manage-model.md#update-models)modelů a vyhodnocuje hodnotu true pro danou dvojitou podmínku, pokud je **splněna** jedna z těchto podmínek:

* Vlákna přímo implementuje model poskytovaný do `IS_OF_MODEL()` a číslo verze modelu na vlákna je *větší nebo rovno* číslu verze poskytnutého modelu.
* Zdvojený implementuje model, který *rozšiřuje* daný model `IS_OF_MODEL()` a číslo verze rozšířeného modelu je *větší než nebo rovno* číslu verze poskytnutého modelu.

Pokud například použijete dotaz na vlákna v modelu `dtmi:example:widget;4` , dotaz vrátí všechny vlákna založené na **verzi 4 nebo vyšší** modelu **widgetu** a také vytvoří vlákna na základě verze **4 nebo vyšší** z **modelů, které dědí z widgetu**.

`IS_OF_MODEL` může mít několik různých parametrů a zbytek této části je vyhrazen pro různé možnosti přetížení.

Nejjednodušší použití `IS_OF_MODEL` přebírá pouze `twinTypeName` parametr: `IS_OF_MODEL(twinTypeName)` .
Tady je příklad dotazu, který předává hodnotu v tomto parametru:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:example:thing;1')
```

Chcete-li zadat dvojitou kolekci, která bude prohledána, je-li použit více než jeden (například při `JOIN` použití), přidejte `twinCollection` parametr: `IS_OF_MODEL(twinCollection, twinTypeName)` .
Tady je příklad dotazu, který přidá hodnotu pro tento parametr:

```sql
SELECT * FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:example:thing;1')
```

Chcete-li provést přesnou shodu, přidejte `exact` parametr: `IS_OF_MODEL(twinTypeName, exact)` .
Tady je příklad dotazu, který přidá hodnotu pro tento parametr:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:example:thing;1', exact)
```

Můžete také předat všechny tři argumenty společně: `IS_OF_MODEL(twinCollection, twinTypeName, exact)` .
Tady je příklad dotazu, který určuje hodnotu pro všechny tři parametry:

```sql
SELECT ROOM FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:example:thing;1', exact)
```

## <a name="query-by-relationship"></a>Dotaz podle vztahu

Při dotazování na základě **vztahů** digitálních vláken má dotazovací jazyk pro digitální vlákna Azure speciální syntaxi.

Relace jsou načteny do oboru dotazu v `FROM` klauzuli. Důležité rozlišení od "klasických" jazyků typu SQL je to, že každý výraz v této `FROM` klauzuli není tabulka. místo toho `FROM` klauzule vyjadřuje křížové vztahy mezi entitami a je zapsána ve verzi služby Azure Digital revlákens `JOIN` .

Zavoláte se s možnostmi modelu digitálních vláken Azure, relace neexistují nezávisle na zdvojených [objektech](concepts-models.md) . To znamená, že jazyk dotazu Azure Digital revlákens `JOIN` je trochu odlišný od obecného SQL `JOIN` , protože relace, na které se tady nejde dotázat, nezávisle na sobě a musí být vázané na vlákna.
Aby bylo možné tento rozdíl začlenit, klíčové slovo `RELATED` se používá v `JOIN` klauzuli pro odkazování na množinu vztahů typu vlákna.

Následující část obsahuje několik příkladů toho, co vypadá.

> [!TIP]
> V koncepčním důsledku Tato funkce napodobuje funkci CosmosDB orientované na dokumenty, kde `JOIN` lze provádět na podřízených objektech v dokumentu. CosmosDB používá `IN` klíčové slovo k označení toho, že má `JOIN` iterovat přes prvky pole v rámci aktuálního kontextu dokumentu.

### <a name="relationship-based-query-examples"></a>Příklady dotazů založených na relacích

Chcete-li získat datovou sadu, která obsahuje relace, použijte jeden `FROM` příkaz následovaný N `JOIN` příkazy N, kde `JOIN` příkazy Express na výsledek předchozího `FROM` nebo `JOIN` příkazu.

Tady je ukázkový dotaz založený na relacích. Tento fragment kódu vybere všechny digitální vlákna s vlastností *ID* ABC a všechny digitální vlákna, které souvisejí s těmito digitálními podmnožinami prostřednictvím relace *obsahující* .

```sql
SELECT T, CT
FROM DIGITALTWINS T
JOIN CT RELATED T.contains
WHERE T.$dtId = 'ABC'
```

> [!NOTE]
> Vývojář není muset korelovat `JOIN` s hodnotou klíče v `WHERE` klauzuli (nebo zadat hodnotu klíče vloženou s `JOIN` definicí). Tato korelace je vypočítána automaticky systémem, protože samotné vlastnosti vztahu identifikují cílovou entitu.

### <a name="query-the-properties-of-a-relationship"></a>Dotazování vlastností relace

Podobně jako digitální vlákna mají vlastnosti, které jsou popsány prostřednictvím DTDL, mohou mít relace také vlastnosti. Můžete se dotazovat na vlákna na **základě vlastností jejich vztahů**.
Jazyk dotazů digitálních vláken Azure umožňuje filtrování a projekci vztahů přiřazením aliasu k relaci v rámci `JOIN` klauzule.

Můžete například zvážit vztah *servicedBy* , který má vlastnost *reportedCondition* . V níže uvedeném dotazu je tomuto vztahu přiřazen alias R, aby odkazoval na jeho vlastnost.

```sql
SELECT T, SBT, R
FROM DIGITALTWINS T
JOIN SBT RELATED T.servicedBy R
WHERE T.$dtId = 'ABC'
AND R.reportedCondition = 'clean'
```

V předchozím příkladu si všimněte, že *reportedCondition* je vlastnost samotného vztahu *servicedBy* (ne některé digitální vlákna, která má vztah *servicedBy* ).

### <a name="query-with-multiple-joins"></a>Dotaz s více spojeními

`JOIN`V jednom dotazu je podporováno až pět s. To umožňuje procházet více úrovní vztahů najednou.

Zde je příklad dotazu s vícenásobnými spojeními, který získá všechny žárovky obsažené na světelných panelech v místnostech 1 a 2.

```sql
SELECT LightBulb
FROM DIGITALTWINS Room
JOIN LightPanel RELATED Room.contains
JOIN LightBulb RELATED LightPanel.contains
WHERE IS_OF_MODEL(LightPanel, 'dtmi:contoso:com:lightpanel;1')
AND IS_OF_MODEL(LightBulb, 'dtmi:contoso:com:lightbulb ;1')
AND Room.$dtId IN ['room1', 'room2']
```

## <a name="count-items"></a>Počet položek

Počet položek v sadě výsledků můžete spočítat pomocí `Select COUNT` klauzule:

```sql
SELECT COUNT()
FROM DIGITALTWINS
```

Přidejte `WHERE` klauzuli pro zjištění počtu položek, které splňují určitá kritéria. Tady je několik příkladů, jak počítat s použitým filtrem na základě typu dvojitě známého modelu (Další informace najdete v tématu [*dotaz podle modelu*](#query-by-model) níže):

```sql
SELECT COUNT()
FROM DIGITALTWINS
WHERE IS_OF_MODEL('dtmi:sample:Room;1')

SELECT COUNT()
FROM DIGITALTWINS c
WHERE IS_OF_MODEL('dtmi:sample:Room;1') AND c.Capacity > 20
```

`COUNT`Spolu s klauzulí můžete také použít `JOIN` . Tady je dotaz, který počítá všechny žárovky obsažené v světelných panelech místností 1 a 2:

```sql
SELECT COUNT()  
FROM DIGITALTWINS Room  
JOIN LightPanel RELATED Room.contains  
JOIN LightBulb RELATED LightPanel.contains  
WHERE IS_OF_MODEL(LightPanel, 'dtmi:contoso:com:lightpanel;1')  
AND IS_OF_MODEL(LightBulb, 'dtmi:contoso:com:lightbulb ;1')  
AND Room.$dtId IN ['room1', 'room2']
```

## <a name="filter-results-select-top-items"></a>Výsledky filtru: Vyberte horní položky.

Můžete vybrat několik "horních" položek v dotazu pomocí `Select TOP` klauzule.

```sql
SELECT TOP (5)
FROM DIGITALTWINS
WHERE ...
```

## <a name="filter-results-specify-return-set-with-projections"></a>Výsledky filtru: určení návratové sady s projekcemi

Pomocí projekce v `SELECT` příkazu můžete zvolit, které sloupce bude dotaz vracet.

>[!NOTE]
>V současné době nejsou složité vlastnosti podporovány. Chcete-li zajistit, aby vlastnosti projekce byly platné, zkombinujte projekce s `IS_PRIMITIVE` kontrolou.

Tady je příklad dotazu, který pomocí projekce vrací vlákna a vztahy. Následující dotaz projektuje *spotřebitel*, *továrnu* a *hranu* z scénáře, kdy *továrna* s ID *ABC* souvisí s *příjemcem* prostřednictvím vztahu *Factory. Customer.* tento vztah je zobrazený jako *okraj*.

```sql
SELECT Consumer, Factory, Edge
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
```

Můžete také použít projekci k vrácení vlastnosti vlákna. Následující dotaz projektuje vlastnost *Name* pro *uživatele* , kteří se vztahují k *továrně* s ID *ABC* prostřednictvím vztahu objektu *Factory. Customer*.

```sql
SELECT Consumer.name
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Consumer.name)
```

Můžete také použít projekci a vrátit vlastnost vztahu. Podobně jako v předchozím příkladu následující dotaz projektuje vlastnost *Name* *příjemců* souvisejících s *objektem Factory* s ID *ABC* prostřednictvím vztahu objektu *Factory. Customer.* nyní ale vrátí také dvě vlastnosti vztahu, *Prop1* a *prop2*. Provádí pojmenování *hraničních* vztahů a shromáždění jeho vlastností.  

```sql
SELECT Consumer.name, Edge.prop1, Edge.prop2, Factory.area
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name) AND IS_PRIMITIVE(Edge.prop1) AND IS_PRIMITIVE(Edge.prop2)
```

Pomocí aliasů můžete také zjednodušit dotazy pomocí projekce.

Následující dotaz provede stejné operace jako v předchozím příkladu, ale aliasuje názvy vlastností do `consumerName` ,, `first` `second` a `factoryArea` .

```sql
SELECT Consumer.name AS consumerName, Edge.prop1 AS first, Edge.prop2 AS second, Factory.area AS factoryArea
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name) AND IS_PRIMITIVE(Edge.prop1) AND IS_PRIMITIVE(Edge.prop2)
```

Zde je podobný dotaz, který se dotazuje stejné sady jako v předchozím příkladu, ale projektuje pouze vlastnost *Consumer.Name* jako `consumerName` a projekty, které tvoří kompletní *objekt pro vytváření* , jako dvojitou hodnotu.

```sql
SELECT Consumer.name AS consumerName, Factory
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name)
```

## <a name="build-efficient-queries-with-the-in-operator"></a>Vytváření efektivních dotazů pomocí operátoru IN

Můžete významně snížit počet dotazů, které potřebujete, vytvořením pole dvojitých vláken a dotazování pomocí `IN` operátoru. 

Představte si například scénář, ve kterém *budovy* obsahují *podlahu* a *podlahu* , obsahují *místnosti*. Chcete-li hledat místnosti v rámci budovy, která jsou horká, jedním ze způsobů, jak postupovat podle těchto kroků.

1. Najděte podlahu v budově na základě `contains` vztahu.

    ```sql
    SELECT Floor
    FROM DIGITALTWINS Building
    JOIN Floor RELATED Building.contains
    WHERE Building.$dtId = @buildingId
    ```

2. Chcete-li najít místnosti místo toho, aby se v jednom z nich používal `JOIN` dotaz pro vyhledání místností pro každý z nich, můžete zadat dotaz pomocí kolekce podlah v budově (s názvem *Floor* v dotazu níže).

    V klientské aplikaci:
    
    ```csharp
    var floors = "['floor1','floor2', ..'floorn']"; 
    ```
    
    V dotazu:
    
    ```sql
    
    SELECT Room
    FROM DIGITALTWINS Floor
    JOIN Room RELATED Floor.contains
    WHERE Floor.$dtId IN ['floor1','floor2', ..'floorn']
    AND Room. Temperature > 72
    AND IS_OF_MODEL(Room, 'dtmi:com:contoso:Room;1')
    
    ```

## <a name="other-compound-query-examples"></a>Další příklady složených dotazů

Můžete **zkombinovat** libovolný z výše uvedených typů dotazu pomocí operátorů kombinace pro zahrnutí více podrobností v jednom dotazu. Tady jsou některé další příklady složených dotazů, které dotazují na více než jeden typ zdvojeného popisovače najednou.

| Popis | Dotaz |
| --- | --- |
| Ze zařízení, která jsou v *místnosti 123* , se vrátí zařízení MxChip, která obsluhují roli operátora. | `SELECT device`<br>`FROM DigitalTwins space`<br>`JOIN device RELATED space.has`<br>`WHERE space.$dtid = 'Room 123'`<br>`AND device.$metadata.model = 'dtmi:contoso:com:DigitalTwins:MxChip:3'`<br>`AND has.role = 'Operator'` |
| Získejte vlákna, která mají relaci s názvem, *obsahuje* další nevlákenný identifikátor *ID1* | `SELECT Room`<br>`FROM DIGITALTWINS Room`<br>`JOIN Thermostat RELATED Room.Contains`<br>`WHERE Thermostat.$dtId = 'id1'` |
| Získat všechny místnosti tohoto modelu místnosti, které jsou obsaženy v *floor11* | `SELECT Room`<br>`FROM DIGITALTWINS Floor`<br>`JOIN Room RELATED Floor.Contains`<br>`WHERE Floor.$dtId = 'floor11'`<br>`AND IS_OF_MODEL(Room, 'dtmi:contoso:com:DigitalTwins:Room;1')` |

## <a name="run-queries-with-the-api"></a>Spouštění dotazů pomocí rozhraní API

Jakmile se rozhodnete pro řetězec dotazu, provedete to tak, že zavoláte [**rozhraní API pro dotazy**](/rest/api/digital-twins/dataplane/query).

Můžete zavolat rozhraní API přímo nebo použít jednu ze [sad SDK](how-to-use-apis-sdks.md#overview-data-plane-apis) dostupných pro digitální vlákna Azure.

Následující fragment kódu ilustruje volání [rozhraní .NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) z klientské aplikace:

```csharp
    string adtInstanceEndpoint = "https://<your-instance-hostname>";

    var credential = new DefaultAzureCredential();
    DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceEndpoint), credential);

    // Run a query for all twins   
    string query = "SELECT * FROM DIGITALTWINS";
    AsyncPageable<BasicDigitalTwin> result = client.QueryAsync<BasicDigitalTwin>(query);
```

Toto volání vrátí výsledky dotazu ve formě objektu [BasicDigitalTwin](/dotnet/api/azure.digitaltwins.core.basicdigitaltwin?view=azure-dotnet&preserve-view=true) .

Volání na podporu stránkování. Tady je kompletní příklad použití `BasicDigitalTwin` jako typ výsledku dotazu s zpracováním chyb a stránkováním:

```csharp
try
{
    await foreach(BasicDigitalTwin twin in result)
        {
            // You can include your own logic to print the result
            // The logic below prints the twin's ID and contents
            Console.WriteLine($"Twin ID: {twin.Id} \nTwin data");
            IDictionary<string, object> contents = twin.Contents;
            foreach (KeyValuePair<string, object> kvp in contents)
            {
                Console.WriteLine($"{kvp.Key}  {kvp.Value}");
            }
        }
}
catch (RequestFailedException e)
{
    Console.WriteLine($"Error {e.Status}: {e.Message}");
    throw;
}
```

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [rozhraních API a sadách SDK digitálních vláken Azure](how-to-use-apis-sdks.md), včetně rozhraní API pro dotazy, které se používá ke spouštění dotazů z tohoto článku.
