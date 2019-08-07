---
title: Postup sledování stavu a výsledků indexeru – Azure Search
description: Pomocí REST API nebo sady .NET SDK Sledujte stav, průběh a výsledky Azure Search indexerů v Azure Portal.
ms.date: 06/28/2019
author: RobDixon22
manager: HeidiSteen
ms.author: heidist
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 52e9e96598f429bcd57bba23d035d0d341731a9c
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840738"
---
# <a name="how-to-monitor-azure-search-indexer-status-and-results"></a>Jak monitorovat stav a výsledky indexeru Azure Search

Azure Search poskytuje informace o stavu a monitorování o aktuálním a historickém spuštění každého indexeru.

Monitorování indexeru je užitečné v případě, že chcete:

* Sledovat průběh indexeru během probíhajícího běhu.
* Zkontrolujte výsledky probíhajícího nebo předchozího indexerového spuštění.
* Identifikujte chyby indexerů nejvyšší úrovně a chyby nebo varování týkající se indexovaných jednotlivých dokumentů.

## <a name="find-indexer-status-and-history-details"></a>Vyhledání podrobností o stavu a historii indexeru

Informace o monitorování indexeru můžete získat různými způsoby, včetně těchto:

* Na webu [Azure Portal](#portal)
* Použití [REST API](#restapi)
* Používání [sady .NET SDK](#dotnetsdk)

K dispozici jsou dostupné informace o monitorování indexeru, včetně všech těchto formátů dat, které se liší v závislosti na použité metodě přístupu:

* Stavové informace o samotném indexeru
* Informace o posledním spuštění indexeru, včetně jeho stavu, času zahájení a ukončení a podrobných chyb a upozornění.
* Zobrazí se seznam historických indexerů a jejich stavů, výsledků, chyb a upozornění.

Indexery, které zpracovávají velké objemy dat, může trvat dlouhou dobu. Například indexery, které zpracovávají miliony zdrojových dokumentů, můžou běžet po dobu 24 hodin a pak téměř okamžitě začít. Stav indexerů s vysokým objemem může na portálu vždycky vyslovit. I když je indexer spuštěný, jsou k dispozici podrobnosti o probíhajícím průběhu a předchozích spuštěních.

<a name="portal"></a>

## <a name="monitor-indexers-in-the-portal"></a>Monitorování indexerů na portálu

Aktuální stav všech indexerů můžete zobrazit v seznamu indexerů na stránce s přehledem služby Search.

   ![Seznam indexerů](media/search-monitor-indexers/indexers-list.png "Seznam indexerů")

Při provádění indexeru se **v**seznamu zobrazí stav a hodnota **úspěšné dokumentace** zobrazuje počet zpracovaných dokumentů. Může trvat několik minut, než portál aktualizuje hodnoty stavu indexeru a počty dokumentů.

Indexer, jehož poslední spuštění bylo úspěšné, ukazuje **úspěch**. Spuštění indexeru může být úspěšné i v případě, že v jednotlivých dokumentech dojde k chybám, pokud je počet chyb menší, než je nastavení maximálního počtu **nezdařených položek** indexeru.

Pokud poslední spuštění skončilo s chybou, zobrazí se stav **nezdařilo se**. Stav resetování znamená, že došlo k resetování stavu sledování změn indexeru.

Kliknutím na indexer v seznamu zobrazíte další podrobnosti o aktuálním a nedávném běhu indexeru.

   ![Souhrn indexeru a historie spouštění](media/search-monitor-indexers/indexer-summary.png "Souhrn indexeru a historie spouštění")

**Souhrnný** graf indexeru zobrazuje graf počtu dokumentů zpracovávaných v posledních spuštěních.

V seznamu **Podrobnosti spuštění** se zobrazí až 50 posledních výsledků spuštění.

Kliknutím na výsledek spuštění v seznamu zobrazíte konkrétní informace o daném spuštění. Sem patří počáteční a koncové časy a všechny chyby a varování, ke kterým došlo.

   ![Podrobnosti spuštění indexeru](media/search-monitor-indexers/indexer-execution.png "Podrobnosti spuštění indexeru")

Pokud během běhu došlo k určitým dokumentům, budou uvedeny v poli chyby a upozornění.

   ![Podrobnosti indexeru s chybami](media/search-monitor-indexers/indexer-execution-error.png "Podrobnosti indexeru s chybami")

Upozornění jsou společná u některých typů indexerů a neznamenají vždy problém. Například indexery, které používají službu rozpoznávání, mohou hlásit upozornění, když obrázky nebo soubory PDF neobsahují žádný text ke zpracování.

Další informace o zkoumání chyb a upozornění indexeru najdete v tématu [řešení běžných potíží indexeru v Azure Search](search-indexer-troubleshooting.md).

<a name="restapi"></a>

## <a name="monitor-indexers-using-the-rest-api"></a>Monitorování indexerů pomocí REST API

Můžete načíst stav a historii provádění indexeru pomocí [příkazu Get indexer status](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

Odpověď obsahuje celkový stav indexeru, vyvolání posledního (nebo probíhajícího) indexeru a historii posledních vyvolání indexeru.

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

Historie spouštění obsahuje až 50 nejaktuálnějších běhů, které jsou seřazené v opačném chronologickém pořadí (nejnovější první).

Všimněte si, že existují dvě různé stavové hodnoty. Stav nejvyšší úrovně je pro indexer samotný. Stav indexeru spuštěno znamená, že indexer je nastaven správně a je k dispozici pro spuštění, ale ne v současnosti je spuštěn.

Každé spuštění indexeru má také svůj vlastní stav, který označuje, jestli je toto konkrétní spuštění probíhající (**spuštěné**), nebo jestli se už dokončilo se stavem **úspěch**, **transientFailure**nebo **persistentFailure** . 

Když je indexer obnovený tak, aby aktualizoval stav sledování změn, přidá se samostatná položka historie spouštění se stavem **resetování** .

Další podrobnosti o stavových kódech a datech monitorování indexeru najdete v tématu [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status).

<a name="dotnetsdk"></a>

## <a name="monitor-indexers-using-the-net-sdk"></a>Monitorování indexerů pomocí .NET SDK

Můžete definovat plán pro indexer pomocí sady Azure Search .NET SDK. Chcete-li to provést, zahrňte při vytváření nebo aktualizaci indexeru vlastnost **Schedule** .

Následující C# Příklad zapisuje informace o stavu indexeru a výsledcích jeho posledního (nebo probíhajícího) běhu do konzoly.

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

Všimněte si, že existují dvě různé stavové hodnoty. Stav nejvyšší úrovně je stav indexeru, který je sám sebou. Stav indexeru je **spuštěný** znamená, že indexer je nastaven správně a je k dispozici pro spuštění, ale není aktuálně spuštěn.

Každé spuštění indexeru má také svůj vlastní stav pro to, zda konkrétní spuštění probíhá (spuštěno), nebo bylo již dokončeno se stavem **úspěch** nebo **TransientError** . 

Když je indexer obnovený tak, aby aktualizoval stav sledování změn, přidá se samostatná položka historie se stavem **resetování** .

Další podrobnosti o stavových kódech a informacích o monitorování indexerů najdete v tématu [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) v REST API.

Podrobnosti o chybách a upozorněních specifických pro dokument lze získat vytvořením výčtu seznamů `IndexerExecutionResult.Errors` a. `IndexerExecutionResult.Warnings`

Další informace o třídách .NET SDK používaných pro monitorování indexerů naleznete v tématu [IndexerExecutionInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo?view=azure-dotnet) a [IndexerExecutionResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet).
