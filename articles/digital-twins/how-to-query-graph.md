---
title: Dotazování na graf dvojčat
titleSuffix: Azure Digital Twins
description: Informace najdete v tématu Postup dotazování grafu s dvojitou podseznamem digitálních vláken Azure.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 3e3dce20f447b47ad78deea617b513c50f552733
ms.sourcegitcommit: b437bd3b9c9802ec6430d9f078c372c2a411f11f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893624"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Dotazování na vyzdvojený graf digitálních vláken Azure

Tento článek obsahuje příklady a další podrobnosti o použití [jazyka dotazů digitálních vláken Azure](concepts-query-language.md) k dotazování na [nevlákenný graf](concepts-twins-graph.md) pro informace. Dotazy můžete spouštět v grafu pomocí [**rozhraní API pro dotazování**](how-to-use-apis-sdks.md)digitálních vláken Azure.

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

Ve zbývající části tohoto článku najdete příklady použití těchto operací.

## <a name="query-syntax"></a>Syntaxe dotazů

Tato část obsahuje ukázkové dotazy, které ilustrují strukturu dotazovacího jazyka a provádějí možné operace dotazů u [digitálních vláken](concepts-twins-graph.md).

### <a name="show-all-existing-digital-twins"></a>Zobrazit všechny existující digitální vlákna

Tady je základní dotaz, který vrátí seznam všech digitálních vláken v instanci:

```sql
SELECT *
FROM DIGITALTWINS
```

### <a name="select-top-items"></a>Vybrat horní položky

Můžete vybrat několik "horních" položek v dotazu pomocí `Select TOP` klauzule.

```sql
SELECT TOP (5)
FROM DIGITALTWINS
WHERE ...
```

### <a name="count-items"></a>Počet položek

Počet vláken v sadě výsledků můžete spočítat pomocí `Select COUNT` klauzule:

```sql
SELECT COUNT() 
FROM DIGITALTWINS
``` 

Přidejte `WHERE` klauzuli pro zjištění počtu vláken, která splňují určitá kritéria. Tady je několik příkladů, jak počítat s použitým filtrem na základě typu dvojitě známého modelu (Další informace najdete v tématu [*dotaz podle modelu*](#query-by-model) níže):

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
SELECT COUNT(LightBulb)  
FROM DIGITALTWINS Room  
JOIN LightPanel RELATED Room.contains  
JOIN LightBulb RELATED LightPanel.contains  
WHERE IS_OF_MODEL(LightPanel, 'dtmi:contoso:com:lightpanel;1')  
AND IS_OF_MODEL(LightBulb, 'dtmi:contoso:com:lightbulb ;1')  
AND Room.$dtId IN ['room1', 'room2'] 
```

### <a name="query-by-property"></a>Dotaz podle vlastnosti

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
SELECT *
FROM DIGITALTWINS WHERE IS_DEFINED(Location)
```

To vám může přispět k získání vláken podle jejich vlastností *značek* , jak je popsáno v tématu [Přidání značek do digitálních vláken](how-to-use-tags.md). Tady je dotaz, který získá všechny vlákna označené *červeně*:

```sql
select * from digitaltwins where is_defined(tags.red) 
```

Můžete také získat vlákna na základě **typu vlastnosti**. Tady je dotaz, který získá vlákna, jejichž vlastnost *teploty* je číslo:

```sql
SELECT * FROM DIGITALTWINS T
WHERE IS_NUMBER(T.Temperature)
```

### <a name="query-by-model"></a>Dotaz podle modelu

`IS_OF_MODEL`Operátor lze použít k filtrování na základě [**modelu**](concepts-models.md)vlákna. Podporuje dědičnost a má několik možností přetížení.

Nejjednodušší použití `IS_OF_MODEL` přebírá pouze `twinTypeName` parametr: `IS_OF_MODEL(twinTypeName)` .
Tady je příklad dotazu, který předává hodnotu v tomto parametru:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:sample:thing;1')
```

Chcete-li zadat dvojitou kolekci, která bude prohledána, je-li použit více než jeden (například při `JOIN` použití), přidejte `twinCollection` parametr: `IS_OF_MODEL(twinCollection, twinTypeName)` .
Tady je příklad dotazu, který přidá hodnotu pro tento parametr:

```sql
SELECT * FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:sample:thing;1')
```

Chcete-li provést přesnou shodu, přidejte `exact` parametr: `IS_OF_MODEL(twinTypeName, exact)` .
Tady je příklad dotazu, který přidá hodnotu pro tento parametr:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:sample:thing;1', exact)
```

Můžete také předat všechny tři argumenty společně: `IS_OF_MODEL(twinCollection, twinTypeName, exact)` .
Tady je příklad dotazu, který určuje hodnotu pro všechny tři parametry:

```sql
SELECT ROOM FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:sample:thing;1', exact)
```

### <a name="query-based-on-relationships"></a>Dotaz na základě relací

Při dotazování na základě **vztahů**digitálních vláken má dotazovací jazyk pro digitální vlákna Azure speciální syntaxi.

Relace jsou načteny do oboru dotazu v `FROM` klauzuli. Důležité rozlišení od "klasických" jazyků typu SQL je to, že každý výraz v této `FROM` klauzuli není tabulka. místo toho `FROM` klauzule vyjadřuje křížové vztahy mezi entitami a je zapsána ve verzi služby Azure Digital revlákens `JOIN` . 

Zavoláte se s možnostmi modelu digitálních vláken Azure, relace neexistují nezávisle na zdvojených [objektech](concepts-models.md) . To znamená, že jazyk dotazu Azure Digital revlákens `JOIN` je trochu odlišný od obecného SQL `JOIN` , protože relace, na které se tady nejde dotázat, nezávisle na sobě a musí být vázané na vlákna.
Aby bylo možné tento rozdíl začlenit, klíčové slovo `RELATED` se používá v `JOIN` klauzuli pro odkazování na množinu vztahů typu vlákna. 

Následující část obsahuje několik příkladů toho, co vypadá.

> [!TIP]
> V koncepčním důsledku Tato funkce napodobuje funkci CosmosDB orientované na dokumenty, kde `JOIN` lze provádět na podřízených objektech v dokumentu. CosmosDB používá `IN` klíčové slovo k označení toho, že má `JOIN` iterovat přes prvky pole v rámci aktuálního kontextu dokumentu.

#### <a name="relationship-based-query-examples"></a>Příklady dotazů založených na relacích

Chcete-li získat datovou sadu, která obsahuje relace, použijte jeden `FROM` příkaz následovaný N `JOIN` příkazy N, kde `JOIN` příkazy Express na výsledek předchozího `FROM` nebo `JOIN` příkazu.

Tady je ukázkový dotaz založený na relacích. Tento fragment kódu vybere všechny digitální vlákna s vlastností *ID* ABC a všechny digitální vlákna, které souvisejí s těmito digitálními podmnožinami prostřednictvím relace *obsahující* . 

```sql
SELECT T, CT
FROM DIGITALTWINS T
JOIN CT RELATED T.contains
WHERE T.$dtId = 'ABC' 
```

>[!NOTE] 
> Vývojář není muset korelovat `JOIN` s hodnotou klíče v `WHERE` klauzuli (nebo zadat hodnotu klíče vloženou s `JOIN` definicí). Tato korelace je vypočítána automaticky systémem, protože samotné vlastnosti vztahu identifikují cílovou entitu.

#### <a name="query-the-properties-of-a-relationship"></a>Dotazování vlastností relace

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

V současnosti ve verzi Preview `JOIN` jsou v jednom dotazu podporovány až pět s. To umožňuje procházet více úrovní vztahů najednou.

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

### <a name="other-compound-query-examples"></a>Další příklady složených dotazů

Můžete **zkombinovat** libovolný z výše uvedených typů dotazu pomocí operátorů kombinace pro zahrnutí více podrobností v jednom dotazu. Tady jsou některé další příklady složených dotazů, které dotazují na více než jeden typ zdvojeného popisovače najednou.

| Description | Dotaz |
| --- | --- |
| Ze zařízení, která jsou v *místnosti 123* , se vrátí zařízení MxChip, která obsluhují roli operátora. | `SELECT device`<br>`FROM DigitalTwins space`<br>`JOIN device RELATED space.has`<br>`WHERE space.$dtid = 'Room 123'`<br>`AND device.$metadata.model = 'dtmi:contosocom:DigitalTwins:MxChip:3'`<br>`AND has.role = 'Operator'` |
| Získejte vlákna, která mají relaci s názvem, *obsahuje* další nevlákenný identifikátor *ID1* | `SELECT Room`<br>`FROM DIGITALTWINS Room`<br>`JOIN Thermostat RELATED Room.Contains`<br>`WHERE Thermostat.$dtId = 'id1'` |
| Získat všechny místnosti tohoto modelu místnosti, které jsou obsaženy v *floor11* | `SELECT Room`<br>`FROM DIGITALTWINS Floor`<br>`JOIN Room RELATED Floor.Contains`<br>`WHERE Floor.$dtId = 'floor11'`<br>`AND IS_OF_MODEL(Room, 'dtmi:contosocom:DigitalTwins:Room;1')` |

## <a name="reference-expressions-and-conditions"></a>Reference: výrazy a podmínky

Tato část obsahuje referenční informace o operátorech a funkcích, které jsou k dispozici při psaní dotazů digitálních vláken Azure.

### <a name="operators"></a>Operátory

Podporovány jsou následující operátory:

| Rodina | Operátory |
| --- | --- |
| Logické |A, NEBO, NOT |
| Porovnání |=,! =, <, >, <=, >= |
| Contains | V NZA |

### <a name="functions"></a>Funkce

Podporují se následující funkce kontroly a přetypování typů:

| Funkce | Description |
| -------- | ----------- |
| IS_DEFINED | Vrátí logickou hodnotu, která znamená, zda byla vlastnost přiřazena hodnota. To je podporováno pouze v případě, že je hodnota primitivního typu. Primitivní typy zahrnují řetězec, Boolean, Numeric nebo `null` . Hodnoty DateTime, typy objektů a pole nejsou podporovány. |
| IS_OF_MODEL | Vrátí logickou hodnotu, která označuje, jestli zadaný typ vlákna odpovídá zadanému typu modelu. |
| IS_BOOL | Vrací logickou hodnotu označující, zda je typ zadaného výrazu logická hodnota. |
| IS_NUMBER | Vrací logickou hodnotu označující, zda je typ zadaného výrazu číslo. |
| IS_STRING | Vrací logickou hodnotu označující, zda je typ zadaného výrazu řetězec. |
| IS_NULL | Vrací logickou hodnotu označující, zda je typ zadaného výrazu null. |
| IS_PRIMITIVE | Vrací logickou hodnotu označující, zda je typ zadaného výrazu primitivní (řetězec, logická hodnota, číselná hodnota nebo `null` ). |
| IS_OBJECT | Vrací logickou hodnotu označující, zda je typ zadaného výrazu objekt JSON. |

Podporovány jsou následující řetězcové funkce:

| Funkce | Description |
| -------- | ----------- |
| STARTSWITH (x, y) | Vrátí logickou hodnotu, která označuje, zda první řetězcový výraz začíná druhým. |
| ENDSWITH (x, y) | Vrátí logickou hodnotu, která označuje, zda první řetězcový výraz končí druhým. |

## <a name="run-queries-with-an-api-call"></a>Spouštění dotazů pomocí volání rozhraní API

Jakmile se rozhodnete pro řetězec dotazu, provedete to tak, že zavoláte **rozhraní API pro dotazy**.
Následující fragment kódu znázorňuje toto volání z klientské aplikace:

```csharp
var client = new AzureDigitalTwinsAPIClient(<your-credentials>);
client.BaseUri = new Uri(<your-Azure-Digital-Twins-instance-URL>);

QuerySpecification spec = new QuerySpecification("SELECT * FROM digitaltwins");
QueryResult result = await client.Query.QueryTwinsAsync(spec);
```

Toto volání vrátí výsledky dotazu ve formě objektu QueryResult. 

Volání na podporu stránkování. Tady je kompletní příklad s zpracováním chyb a stránkováním:

```csharp
string query = "SELECT * FROM digitaltwins";
try
{
    AsyncPageable<string> qresult = client.QueryAsync(query);
    await foreach (string item in qresult) 
    {
        // Do something with each result
    }
}
catch (RequestFailedException e)
{
    Log.Error($"Error {e.Status}: {e.Message}");
    return null;
}
```

## <a name="query-limitations"></a>Omezení dotazů

Změny ve vaší instanci se projeví v dotazech až na 10 sekund. Například pokud dokončíte operaci, jako je vytváření nebo odstraňování vláken s rozhraním API DigitalTwins, výsledek se v dotazech rozhraní API nemusí projevit okamžitě. Čekání na krátkou dobu by měla být dostačující pro vyřešení.

Při používání ve verzi Preview jsou k dispozici další omezení `JOIN` .
* V rámci příkazu nejsou podporovány žádné poddotazy `FROM` .
* `OUTER JOIN` Sémantika není podporována, což znamená, že pokud má relace hodnotu nula, pak je celý "řádek" odstraněn z výstupní sady výsledků.
* Během období Preview je hloubka procházení grafu omezená na pět `JOIN` úrovní na jeden dotaz.
* Zdroj pro `JOIN` operace je omezený: dotaz musí deklarovat vlákna, kde začíná dotaz.

## <a name="query-best-practices"></a>Osvědčené postupy pro dotazy

Níže najdete několik tipů pro dotazování pomocí digitálních vláken Azure.

* Zvažte vzor dotazu během fáze návrhu modelu. Pokuste se zajistit, aby relace, které musí být zodpovězeny v jednom dotazu, byly modelovány jako relace s jednou úrovní.
* Navrhněte vlastnosti tak, aby se předešlo velkým sadám výsledků z procházení grafů.
* Můžete významně snížit počet dotazů, které potřebujete, vytvořením pole dvojitých vláken a dotazování pomocí `IN` operátoru. Představte si například scénář, ve kterém *budovy* obsahují *podlahu* a *podlahu* , obsahují *místnosti*. Pokud chcete hledat místnosti v rámci budovy, která je horká, můžete:

    1. Nalezení podlahových poschodí v rámci vytváření na základě `contains` vztahu
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
* V názvech vlastností a hodnotách se rozlišují malá a velká písmena, proto je potřeba se starat o použití přesně názvů definovaných v modelech. Pokud jsou názvy vlastností špatně napsané nebo nesprávně použita, je sada výsledků prázdná a nevrátí se žádné chyby.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [rozhraních API a sadách SDK digitálních vláken Azure](how-to-use-apis-sdks.md), včetně rozhraní API pro dotazy, které se používá ke spouštění dotazů z tohoto článku.
