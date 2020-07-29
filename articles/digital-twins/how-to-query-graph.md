---
title: Dotazování na graf dvojčat
titleSuffix: Azure Digital Twins
description: Informace najdete v tématu Postup dotazování grafu s dvojitou podseznamem digitálních vláken Azure.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 05bcbf8df695ba308a6eaff5e7401f0a6d638747
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337598"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Dotazování na vyzdvojený graf digitálních vláken Azure

Tento článek obsahuje příklady a další podrobnosti o používání [jazyka úložiště dotazů digitálních vláken Azure](concepts-query-language.md) k dotazování na [dvojitou graf](concepts-twins-graph.md) pro informace. Dotazy můžete spouštět v grafu pomocí [**rozhraní API pro dotazování**](how-to-use-apis-sdks.md)digitálních vláken Azure.

## <a name="query-syntax"></a>Syntaxe dotazů

Tady je několik ukázkových dotazů, které ilustrují strukturu dotazovacího jazyka a provádějí možné operace dotazů.

Získat [digitální vlákna](concepts-twins-graph.md) podle vlastností (včetně ID a metadat):
```sql
SELECT  * 
FROM DigitalTwins T  
WHERE T.firmwareVersion = '1.1'
AND T.$dtId in ['123', '456']
AND T.Temperature = 70
```

Získat digitální vlákna podle [modelu](concepts-models.md)
```sql
SELECT  * 
FROM DigitalTwins T  
WHERE IS_OF_MODEL(T , 'dtmi:com:contoso:Space;3')
AND T.roomSize > 50
```

> [!TIP]
> ID digitálního vlákna se dotazuje pomocí pole metadata `$dtId` .

### <a name="query-based-on-relationships"></a>Dotaz na základě relací

Při dotazování na základě vztahů digitálních vláken má jazyk úložiště dotazů digitálních vláken Azure speciální syntaxi.

Relace jsou načteny do oboru dotazu v `FROM` klauzuli. Důležité rozlišení od "klasických" jazyků typu SQL je to, že každý výraz v této `FROM` klauzuli není tabulka. místo toho `FROM` klauzule vyjadřuje křížové vztahy mezi entitami a je zapsána ve verzi služby Azure Digital revlákens `JOIN` . 

Zavoláte se s možnostmi modelu digitálních vláken Azure, relace neexistují nezávisle na zdvojených [objektech](concepts-models.md) . To znamená, že jazyk úložiště dotazů digitálních vláken Azure `JOIN` je trochu odlišný od obecného SQL `JOIN` , protože relace, na které se tady nedají dotazovat nezávisle, a musí být vázané na vlákna.
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

Podobně jako digitální vlákna mají vlastnosti, které jsou popsány prostřednictvím DTDL, mohou mít relace také vlastnosti. Jazyk úložiště dotazů digitálních vláken Azure umožňuje filtrování a projekci vztahů přiřazením aliasu k relaci v rámci `JOIN` klauzule. 

Můžete například zvážit vztah *servicedBy* , který má vlastnost *reportedCondition* . V níže uvedeném dotazu je tomuto vztahu přiřazen alias R, aby odkazoval na jeho vlastnost.

```sql
SELECT T, SBT, R
FROM DIGITALTWINS T
JOIN SBT RELATED T.servicedBy R
WHERE T.$dtId = 'ABC' 
AND R.reportedCondition = 'clean'
```

V předchozím příkladu si všimněte, že *reportedCondition* je vlastnost samotného vztahu *servicedBy* (ne některé digitální vlákna, která má vztah *servicedBy* ).

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
* `OUTER JOIN`Sémantika není podporována, což znamená, že pokud má relace hodnotu nula, pak je celý "řádek" odstraněn z výstupní sady výsledků.
* Ve verzi Public Preview je hloubka procházení grafu omezená: `JOIN` na dotaz je povolená jenom jedna.
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
