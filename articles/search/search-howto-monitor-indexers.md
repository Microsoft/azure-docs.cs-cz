---
title: Jak monitorovat stav indexeru a výsledky – Azure Search
description: Monitorujte stav, průběh a výsledky indexerů Azure Search na webu Azure Portal, pomocí rozhraní REST API nebo .NET SDK.
ms.date: 06/28/2019
author: RobDixon22
manager: HeidiSteen
ms.author: v-rodixo
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 07b4fe2ef830c3ce09b655cf4b433d14923229a9
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486282"
---
# <a name="how-to-monitor-azure-search-indexer-status-and-results"></a>Jak monitorovat stav indexeru Azure Search a výsledky

Služba Azure Search poskytuje stav a monitorování informace o aktuálním a historickém spustí každý indexer.

Monitorování indexeru je užitečná, když chcete:

* Sledovat průběh indexeru během průběžně spustit.
* Zkontrolujte výsledky probíhající nebo předchozí kterého se spuštění indexeru.
* Identifikujte nejvyšší úrovně indexer chyby a chyby nebo varování o jednotlivé dokumenty indexované.

## <a name="find-indexer-status-and-history-details"></a>Najít podrobnosti o stavu a historie indexeru

Můžete přistupovat k informacím o monitorování indexer různými způsoby, včetně:

* Na webu [Azure Portal](#portal)
* Použití [rozhraní REST API](#restapi)
* Použití [sady .NET SDK](#dotnetsdk)

K dispozici indexer monitorování zahrnuje všechny následující (v případě, že data, která se liší formáty založené na použité metodě přístup):

* Stavové informace o samotné indexeru
* Informace o nejnovější položkou Spustit indexer, včetně stavu, počáteční a koncové časy a podrobné chyby a upozornění.
* Seznam historických indexer spuštění a jejich stavy, výsledky, chyby a upozornění.

Indexery, které zpracovávají velké objemy dat může trvat dlouhou dobu pro spuštění. Indexery, které zpracovávají miliony zdroje dokumentů například můžete spustit po dobu 24 hodin a restartujte téměř okamžitě. Stav pro vysoký počet indexerů může vždy jednat **probíhá** na portálu. I v případě, že indexer je spuštěná, podrobnosti jsou dostupné informace o probíhající průběh a předchozích spuštění.

<a name="portal"></a>

## <a name="monitor-indexers-in-the-portal"></a>Indexery monitorování na portálu

Můžete zobrazit aktuální stav všech vašich indexery ve službě **indexery** seznamu na stránce Přehled vyhledávací služby.

   ![Seznam indexerů](media/search-monitor-indexers/indexers-list.png "seznam indexerů")

Když provádí indexer, stav v seznamu zobrazí **probíhá**a **dokumentace byla úspěšná** hodnota ukazuje počet dokumentů zpracovaných zatím. Může trvat několik minut, než se na portál a aktualizujte hodnoty stavu indexeru a počty dokumentů.

Indexer, jejichž posledního spuštění bylo úspěšné ukazuje **úspěch**. Spustit indexer může být úspěšné i v případě, že jednotlivé dokumenty obsahují chyby, pokud počet chyb je menší než indexeru **maximální počet selhání** nastavení.

Pokud bylo poslední spuštění byl ukončen s chybou, zobrazuje stav **neúspěšné**. Stav **resetování** znamená, že byl resetován stavy sledování změn indexeru.

Klikněte na tlačítko pro indexovacího člena v seznamu zobrazíte další podrobnosti o indexer aktuálních a nedávných spuštění.

   ![Historie summary a spuštění indexeru](media/search-monitor-indexers/indexer-summary.png "historie summary a spuštění indexeru")

**Shrnutí indexeru** graf zobrazuje graf počet dokumentů zpracovaných v jeho posledního spuštění.

**Podrobnosti provádění** seznamu zobrazí až 50 nejnovější výsledky spuštění.

Klikněte na výsledek spuštění v seznamu zobrazíte podrobnosti o spuštění. To zahrnuje jeho počáteční a koncové časy a všechny chyby a varování, ke kterým došlo.

   ![Podrobnosti o spuštění indexeru](media/search-monitor-indexers/indexer-execution.png "podrobnosti spuštění indexeru")

Pokud byly nějaké problémy specifické pro dokument za běhu, budou uvedené do pole chyby a upozornění.

   ![Podrobnosti o indexer s chybami](media/search-monitor-indexers/indexer-execution-error.png "podrobnosti Indexer s chybami")

Upozornění jsou běžné u některých typů indexery a vždy neznamenají problém. Například indexerů, které používají služby cognitive services můžete ohlásit upozornění, když image nebo soubory PDF neobsahují žádný text k zpracování.

Další informace o analýze indexer chyby a upozornění najdete v tématu [řešení běžných potíží indexeru ve službě Azure Search](search-indexer-troubleshooting.md).

<a name="restapi"></a>

## <a name="monitor-indexers-using-the-rest-api"></a>Monitorování pomocí rozhraní REST API

Historie stavu a spuštění použití indexeru můžete načíst [příkazu získat stav Indexer](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

Odpověď obsahuje celkový stav indexer, indexer poslední (nebo probíhající) volání a historii posledních vyvolání indexeru.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

Historie provádění obsahuje až 50 posledního spuštění, které jsou seřazeny v chronologickém pořadí reverzní (nejnovější je první).

Všimněte si, že existují dvě hodnoty jiný stav. Stav na nejvyšší úrovni je pro indexer, samotný. Indexer stav **systémem** znamená, že indexer je správně nastavený a k dispozici ke spuštění, ale nebude je aktuálně spuštěna.

Každé spuštění indexeru má také svůj vlastní stav, který určuje, zda je aktuálnímu tohoto konkrétního spuštění (**systémem**), nebo již byla dokončena s **úspěch**, **transientFailure**, nebo **persistentFailure** stav. 

Když aktualizovat stav sledování změn je resetovat indexer, přidá se položka historie spuštění samostatné s **resetování** stav.

Další podrobnosti o stavové kódy a indexer dat monitorování najdete v tématu [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

<a name="dotnetsdk"></a>

## <a name="monitor-indexers-using-the-net-sdk"></a>Monitorování pomocí sady .NET SDK

Můžete definovat plán pro indexer pomocí .NET SDK služby Azure Search. Chcete-li to provést, patří **plán** vlastnost při vytváření nebo aktualizaci indexeru.

Následující C# příklad zapíše informace o stavu indexeru a výsledky jeho nejnovějšího (nebo probíhající) spusťte do konzoly.

```csharp
static void CheckIndexerStatus(Indexer indexer, SearchServiceClient searchService)
{
    try
    {
        IndexerExecutionInfo execInfo = searchService.Indexers.GetStatus(indexer.Name);

        Console.WriteLine("Indexer has run {0} times.", execInfo.ExecutionHistory.Count);
        Console.WriteLine("Indexer Status: " + execInfo.Status.ToString());

        IndexerExecutionResult result = execInfo.LastResult;

        Console.WriteLine("Latest run");
        Console.WriteLine("  Run Status: {0}", result.Status.ToString());
        Console.WriteLine("  Total Documents: {0}, Failed: {1}", result.ItemCount, result.FailedItemCount);

        TimeSpan elapsed = result.EndTime.Value - result.StartTime.Value;
        Console.WriteLine("  StartTime: {0:T}, EndTime: {1:T}, Elapsed: {2:t}", result.StartTime.Value, result.EndTime.Value, elapsed);

        string errorMsg = (result.ErrorMessage == null) ? "none" : result.ErrorMessage;
        Console.WriteLine("  ErrorMessage: {0}", errorMsg);
        Console.WriteLine("  Document Errors: {0}, Warnings: {1}\n", result.Errors.Count, result.Warnings.Count);
    }
    catch (Exception e)
    {
        // Handle exception
    }
}
```

Výstup v konzole bude vypadat přibližně takto:

    Indexer has run 18 times.
    Indexer Status: Running
    Latest run
      Run Status: Success
      Total Documents: 7, Failed: 0
      StartTime: 10:02:46 PM, EndTime: 10:02:47 PM, Elapsed: 00:00:01.0990000
      ErrorMessage: none
      Document Errors: 0, Warnings: 0

Všimněte si, že existují dvě hodnoty jiný stav. Stav nejvyšší úrovně je stav samotný indexeru. Stav indexer **systémem** znamená, že je správně nastavené indexeru a k dispozici pro spuštění, ale nebude právě probíhá.

Každé spuštění indexeru má také svůj vlastní stav určuje, zda je aktuálnímu tohoto konkrétního spuštění (**systémem**), nebo už byla dokončena s **úspěch** nebo **TransientError** stav. 

Když indexer je tovární nastavení, chcete-li aktualizovat stav sledování změn, položku pro jednotlivé historie se přidá s **resetování** stav.

Další podrobnosti o stavové kódy a indexeru monitorování, najdete v části [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) v rozhraní REST API.

Podrobnosti o konkrétní dokumenty chyby nebo varování je možné načíst podle výčet seznamy `IndexerExecutionResult.Errors` a `IndexerExecutionResult.Warnings`.

Další informace o třídách sady .NET SDK používá ke sledování indexery, naleznete v tématu [IndexerExecutionInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo?view=azure-dotnet) a [IndexerExecutionResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet).
