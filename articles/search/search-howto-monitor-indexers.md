---
title: Sledování stavu a výsledků indexeru
titleSuffix: Azure Cognitive Search
description: Pomocí REST API nebo sady .NET SDK Sledujte stav, průběh a výsledky indexerů služby Azure Kognitivní hledání v Azure Portal.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: a94720e6b84821d53a3bfdcbdce249390078940f
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063236"
---
# <a name="how-to-monitor-azure-cognitive-search-indexer-status-and-results"></a>Jak monitorovat stav a výsledky služby Azure Kognitivní hledání indexer

Můžete monitorovat zpracování indexerem v Azure Portal nebo programově prostřednictvím volání REST nebo sady Azure SDK. Kromě stavu o samotném indexeru můžete kontrolovat počáteční a koncové časy a podrobné chyby a upozornění z konkrétního běhu.

## <a name="monitor-using-azure-portal"></a>Monitorování pomocí Azure Portal

Aktuální stav všech indexerů můžete zobrazit na stránce Přehled vyhledávací služby. Stránky portálu se aktualizují každých několik minut, takže se vám nezobrazuje důkaz o novém spuštění indexeru hned.

   ![Seznam indexerů](media/search-monitor-indexers/indexers-list.png "Seznam indexerů")

| Status | Popis |
|--------|-------------|
| **Probíhá** | Indikuje aktivní spuštění. Portál bude hlásit částečné informace. S průběhem indexování můžete sledovat, že hodnota **úspěšného** povýšení dokumentů byla zvětšena v reakci. Indexery, které zpracovávají velké objemy dat, může trvat dlouhou dobu. Například indexery, které zpracovávají miliony zdrojových dokumentů, můžou běžet po dobu 24 hodin a pak téměř okamžitě začít. Stav indexerů s vysokým objemem **může na portálu vždycky vyslovit** . I když je indexer spuštěný, jsou k dispozici podrobnosti o probíhajícím průběhu a předchozích spuštěních. |
| **Success** | Indikuje, že spuštění proběhlo úspěšně. Spuštění indexeru může být úspěšné i v případě, že v jednotlivých dokumentech dojde k chybám, pokud je počet chyb menší, než je nastavení **maximálního počtu nezdařených položek** indexeru. |
| **Neúspěšný** | Počet chyb překročil maximální počet **položek, které selhaly** , a indexování bylo zastaveno. |
| **Reset** | Stav interního sledování změn indexeru se resetoval. Indexer se spustí v plném rozsahu, aktualizuje všechny dokumenty a ne pouze ty s novějšími časovými razítky. |

Kliknutím na indexer v seznamu můžete zobrazit další podrobnosti o aktuálním a nedávném běhu indexeru.

   ![Souhrn indexeru a historie spouštění](media/search-monitor-indexers/indexer-summary.png "Souhrn indexeru a historie spouštění")

**Souhrnný graf indexeru** zobrazuje graf počtu dokumentů zpracovávaných v posledních spuštěních.

V seznamu **Podrobnosti spuštění** se zobrazí až 50 posledních výsledků spuštění. Kliknutím na výsledek spuštění v seznamu zobrazíte konkrétní informace o daném spuštění. Sem patří počáteční a koncové časy a všechny chyby a varování, ke kterým došlo.

   ![Podrobnosti spuštění indexeru](media/search-monitor-indexers/indexer-execution.png "Podrobnosti spuštění indexeru")

Pokud během běhu došlo k určitým dokumentům, budou uvedeny v poli chyby a upozornění.

   ![Podrobnosti indexeru s chybami](media/search-monitor-indexers/indexer-execution-error.png "Podrobnosti indexeru s chybami")

Upozornění jsou společná u některých typů indexerů a neznamenají vždy problém. Například indexery, které používají službu rozpoznávání, mohou hlásit upozornění, když obrázky nebo soubory PDF neobsahují žádný text ke zpracování. 

Další informace o zkoumání chyb a upozornění indexeru najdete v tématu [řešení běžných potíží indexerů v Azure kognitivní hledání](search-indexer-troubleshooting.md).

## <a name="monitor-using-get-indexer-status-rest-api"></a>Monitorování pomocí get status indexeru (REST API)

Můžete načíst stav a historii provádění indexeru pomocí [příkazu Get indexer status](/rest/api/searchservice/get-indexer-status):

```http
GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2020-06-30
api-key: [Search service admin key]
```

Odpověď obsahuje celkový stav indexeru, vyvolání posledního (nebo probíhajícího) indexeru a historii posledních vyvolání indexeru.

```output
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
```

Historie spouštění obsahuje až 50 nejaktuálnějších běhů, které jsou seřazené v opačném chronologickém pořadí (nejnovější první).

Všimněte si, že existují dvě různé stavové hodnoty. Stav nejvyšší úrovně je pro indexer samotný. Stav indexeru **spuštěno** znamená, že indexer je nastaven správně a je k dispozici pro spuštění, ale ne v současnosti je spuštěn.

Každé spuštění indexeru má také svůj vlastní stav, který označuje, jestli je toto konkrétní spuštění probíhající (**spuštěné**), nebo jestli se už dokončilo se stavem **úspěch**, **transientFailure** nebo **persistentFailure** . 

Když je indexer obnovený tak, aby aktualizoval stav sledování změn, přidá se samostatná položka historie spouštění se stavem **resetování** .

Další podrobnosti o stavových kódech a datech monitorování indexeru najdete v tématu [získání stavu indexeru](/rest/api/searchservice/get-indexer-status).

## <a name="monitor-using-net"></a>Monitorování pomocí .NET

Pomocí sady Azure Kognitivní hledání .NET SDK následující příklad C# zapisuje informace o stavu indexeru a výsledku jeho posledního (nebo probíhajícího) běhu do konzoly.

```csharp
static void CheckIndexerStatus(SearchIndexerClient indexerClient, SearchIndexer indexer)
{
    try
    {
        string indexerName = "hotels-sql-idxr";
        SearchIndexerStatus execInfo = indexerClient.GetIndexerStatus(indexerName);

        Console.WriteLine("Indexer has run {0} times.", execInfo.ExecutionHistory.Count);
        Console.WriteLine("Indexer Status: " + execInfo.Status.ToString());

        IndexerExecutionResult result = execInfo.LastResult;

        Console.WriteLine("Latest run");
        Console.WriteLine("Run Status: {0}", result.Status.ToString());
        Console.WriteLine("Total Documents: {0}, Failed: {1}", result.ItemCount, result.FailedItemCount);

        TimeSpan elapsed = result.EndTime.Value - result.StartTime.Value;
        Console.WriteLine("StartTime: {0:T}, EndTime: {1:T}, Elapsed: {2:t}", result.StartTime.Value, result.EndTime.Value, elapsed);

        string errorMsg = (result.ErrorMessage == null) ? "none" : result.ErrorMessage;
        Console.WriteLine("ErrorMessage: {0}", errorMsg);
        Console.WriteLine(" Document Errors: {0}, Warnings: {1}\n", result.Errors.Count, result.Warnings.Count);
    }
    catch (Exception e)
    {
        // Handle exception
    }
}
```

Výstup v konzole bude vypadat přibližně takto:

```output
Indexer has run 18 times.
Indexer Status: Running
Latest run
  Run Status: Success
  Total Documents: 7, Failed: 0
  StartTime: 11:29:31 PM, EndTime: 11:29:31 PM, Elapsed: 00:00:00.2560000
  ErrorMessage: none
  Document Errors: 0, Warnings: 0
```

Všimněte si, že existují dvě různé stavové hodnoty. Stav nejvyšší úrovně je stav indexeru, který je sám sebou. Stav indexeru je **spuštěný** znamená, že indexer je nastaven správně a je k dispozici pro spuštění, ale není aktuálně spuštěn.

Každé spuštění indexeru má také svůj vlastní stav pro to, zda konkrétní spuštění probíhá (**spuštěno**), nebo bylo již dokončeno se stavem **úspěch** nebo **TransientError** . 

Když je indexer obnovený tak, aby aktualizoval stav sledování změn, přidá se samostatná položka historie se stavem **resetování** .

## <a name="next-steps"></a>Další kroky

Další podrobnosti o stavových kódech a informacích o monitorování indexeru najdete v následujících referenčních informacích k rozhraní API:

* [GetIndexerStatus (REST API)](/rest/api/searchservice/get-indexer-status)
* [IndexerStatus](/dotnet/api/azure.search.documents.indexes.models.indexerstatus)
* [IndexerExecutionStatus](/dotnet/api/azure.search.documents.indexes.models.indexerexecutionstatus)
* [IndexerExecutionResult](/dotnet/api/azure.search.documents.indexes.models.indexerexecutionresult)