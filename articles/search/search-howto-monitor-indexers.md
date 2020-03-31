---
title: Sledování stavu a výsledků indexeru
titleSuffix: Azure Cognitive Search
description: Sledujte stav, průběh a výsledky indexerů Azure Cognitive Search na webu Azure Portal pomocí rozhraní REST API nebo sady .NET SDK.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 699b5a4e5a7f10c883667ca5030dd971855467f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112978"
---
# <a name="how-to-monitor-azure-cognitive-search-indexer-status-and-results"></a>Jak sledovat stav a výsledky indexeru Azure Cognitive Search

Azure Cognitive Search poskytuje informace o stavu a monitorování o aktuálních a historických spuštěních každého indexeru.

Sledování indexeru je užitečné, pokud chcete:

* Sledujte průběh indexeru během probíhajícího běhu.
* Zkontrolujte výsledky probíhajícího nebo předchozího spuštění indexeru.
* Identifikujte chyby indexeru nejvyšší úrovně a chyby nebo upozornění na jednotlivé indexované dokumenty.

## <a name="get-status-and-history"></a>Získání stavu a historie

K informacím o monitorování indexeru můžete přistupovat různými způsoby, například:

* Na webu [Azure Portal](#portal)
* Použití [rozhraní REST API](#restapi)
* Použití sady [.NET SDK](#dotnetsdk)

Dostupné informace o monitorování indexeru zahrnují všechny následující (i když se formáty dat liší v závislosti na použité metodě přístupu):

* Informace o stavu samotného indexeru
* Informace o posledním spuštění indexeru, včetně jeho stavu, počátečního a koncového času a podrobných chyb a upozornění.
* Seznam historických indexeru spustí a jejich stavy, výsledky, chyby a upozornění.

Spuštění indexerů, které zpracovávají velké objemy dat, může trvat dlouhou dobu. Například indexery, které zpracovávají miliony zdrojových dokumentů, mohou být spuštěny po dobu 24 hodin a restartovány téměř okamžitě. Stav pro velkoobjemové indexery může vždy říkat **Probíhá** na portálu. I v případě, že je spuštěn indexer, podrobnosti jsou k dispozici o probíhající průběh a předchozí spuštění.

<a name="portal"></a>

## <a name="monitor-using-the-portal"></a>Sledování pomocí portálu

Aktuální stav všech indexerů můžete zobrazit v seznamu **indexery** na stránce Přehled vyhledávací služby.

   ![Seznam indexerů](media/search-monitor-indexers/indexers-list.png "Seznam indexerů")

Při provádění indexeru se ve stavu v seznamu zobrazí hodnota **Probíhá**a hodnota **Docs Succeeded** zobrazuje počet dosud zpracovaných dokumentů. Může trvat několik minut, než portál aktualizuje hodnoty stavu indexeru a počty dokumentů.

Indexer, jehož poslední spuštění bylo úspěšné, ukazuje **úspěch**. Spuštění indexeru může být úspěšné i v případě, že jednotlivé dokumenty mají chyby, pokud je počet chyb menší než nastavení **Maximální počet položek indexeru.**

Pokud poslední spuštění skončilo chybou, zobrazí se stav **Nezdařilo se**. Stav **Reset** znamená, že indexer ustal stavu sledování změn byl resetován.

Kliknutím na indexer v seznamu zobrazíte další podrobnosti o aktuálních a posledních spuštěních indexeru.

   ![Souhrn indexeru a historie spuštění](media/search-monitor-indexers/indexer-summary.png "Souhrn indexeru a historie spuštění")

Souhrnný graf **indexeru** zobrazuje graf počtu dokumentů zpracovaných v posledních spuštěních.

Seznam **podrobností spuštění** zobrazuje až 50 nejnovějších výsledků provádění.

Kliknutím na výsledek spuštění v seznamu zobrazíte podrobnosti o tomto spuštění. To zahrnuje jeho počáteční a koncový čas a všechny chyby a upozornění, ke kterým došlo.

   ![Podrobnosti spuštění indexeru](media/search-monitor-indexers/indexer-execution.png "Podrobnosti spuštění indexeru")

Pokud během běhu došlo k problémům specifickým pro dokument, budou uvedeny v polích Chyby a upozornění.

   ![Podrobnosti indexeru s chybami](media/search-monitor-indexers/indexer-execution-error.png "Podrobnosti indexeru s chybami")

Upozornění jsou běžné u některých typů indexerů a ne vždy označují problém. Například indexery, které používají kognitivní služby, mohou hlásit upozornění, když soubory obrazu nebo PDF neobsahují žádný text ke zpracování.

Další informace o zkoumání chyb a upozornění indexeru najdete [v tématu Řešení problémů s běžným indexerem v Azure Cognitive Search](search-indexer-troubleshooting.md).

<a name="restapi"></a>

## <a name="monitor-using-rest-apis"></a>Monitorování pomocí rest API

Historii stavu a spuštění indexeru můžete načíst pomocí [příkazu Získat stav indexeru](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

Odpověď obsahuje celkový stav indexeru, poslední (nebo probíhající) vyvolání indexeru a historii nedávné vyvolání indexeru.

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

Historie spuštění obsahuje až 50 posledních spuštění, které jsou seřazeny v obráceném chronologickém pořadí (poslední první).

Všimněte si, že existují dvě různé hodnoty stavu. Stav nejvyšší úrovně je pro samotný indexer. Stav indexeru **spuštění** znamená, že indexer je správně nastaven a je k dispozici ke spuštění, ale ne proto, že je aktuálně spuštěn.

Každé spuštění indexeru má také svůj vlastní stav, který označuje, zda konkrétní spuštění probíhá **(spuštěno)** nebo již bylo dokončeno s **úspěchem**, **příkazem TransientFailure**nebo **stavem persistentFailure.** 

Při obnovení indexeru, aby se aktualizoval stav sledování změn, je přidána samostatná položka historie provádění se stavem **Reset.**

Další podrobnosti o stavových kódech a datech monitorování indexeru naleznete v tématu [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

<a name="dotnetsdk"></a>

## <a name="monitor-using-the-net-sdk"></a>Monitorování pomocí sady .NET SDK

Můžete definovat plán pro indexer pomocí Azure Cognitive Search .NET SDK. Chcete-li to provést, zahrnout **vlastnost plánu** při vytváření nebo aktualizaci indexeru.

Následující příklad jazyka C# zapisuje informace o stavu indexeru a výsledky jeho poslední (nebo probíhající) spuštění do konzoly.

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

Výstup v konzoli bude vypadat nějak takto:

    Indexer has run 18 times.
    Indexer Status: Running
    Latest run
      Run Status: Success
      Total Documents: 7, Failed: 0
      StartTime: 10:02:46 PM, EndTime: 10:02:47 PM, Elapsed: 00:00:01.0990000
      ErrorMessage: none
      Document Errors: 0, Warnings: 0

Všimněte si, že existují dvě různé hodnoty stavu. Stav nejvyšší úrovně je stav samotného indexeru. Indexer stav **Spuštěno** znamená, že indexer je nastaven správně a je k dispozici pro spuštění, ale ne, že je aktuálně spuštěna.

Každé spuštění indexeru má také svůj vlastní stav pro to, zda toto konkrétní spuštění probíhá (**Spuštěno**), nebo již bylo dokončeno se stavem **Success** nebo **TransientError.** 

Při obnovení indexeru, aby se aktualizoval stav sledování změn, je přidána samostatná položka historie se stavem **Reset.**

Další podrobnosti o stavových kódech a informacích o monitorování indexeru najdete v tématu [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) v rozhraní REST API.

Podrobnosti o chybách nebo upozorněních specifických pro dokument `IndexerExecutionResult.Errors` `IndexerExecutionResult.Warnings`lze načíst výčetem seznamů a .

Další informace o třídách sady .NET SDK používaných ke sledování indexerů naleznete v [tématech IndexerExecutionInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo?view=azure-dotnet) a [IndexerExecutionResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet).
