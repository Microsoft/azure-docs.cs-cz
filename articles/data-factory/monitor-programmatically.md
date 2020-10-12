---
title: Programové sledování v Azure Data Factory
description: Naučte se monitorovat kanál v datové továrně pomocí různých sad SDK (Software Development Kit).
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2018
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.custom: devx-track-python
ms.openlocfilehash: 8c13e0e3f1cb8809a126b11409becd2211864c32
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87876542"
---
# <a name="programmatically-monitor-an-azure-data-factory"></a>Programové sledování v Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Tento článek popisuje, jak monitorovat kanál v datové továrně pomocí různých sad SDK (Software Development Kit). 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="data-range"></a>Rozsah dat

Data Factory ukládá pouze data spuštění kanálu po dobu 45 dnů. Při dotazování programově na data týkající se Data Factory běhu kanálu – například pomocí příkazu PowerShellu `Get-AzDataFactoryV2PipelineRun` nejsou k dispozici žádná maximální data pro volitelné `LastUpdatedAfter` a `LastUpdatedBefore` parametry. Pokud se ale dotaz na data za minulý rok nevrátí, například dotaz nevrátí chybu, ale vrátí jenom data spuštění kanálu za posledních 45 dní.

Pokud chcete zachovat data běhu kanálu po dobu více než 45 dnů, nastavte své vlastní diagnostické protokolování pomocí [Azure monitor](monitor-using-azure-monitor.md).

## <a name="net"></a>.NET
Úplný návod k vytvoření a monitorování kanálu pomocí sady .NET SDK najdete v tématu [Vytvoření datové továrny a kanálu pomocí .NET](quickstart-create-data-factory-dot-net.md).

1. Přidejte následující kód, který bude nepřetržitě kontrolovat stav spuštění kanálu, dokud nedokončí kopírování dat.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Přidejte následující kód, který načte podrobnosti o spuštění aktivity kopírování, například velikost načtených/zapsaných dat.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");
   
    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
    if (pipelineRun.Status == "Succeeded")
        Console.WriteLine(activityRuns.First().Output);
    else
        Console.WriteLine(activityRuns.First().Error);
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

Úplnou dokumentaci k sadě .NET SDK naleznete v tématu [Data Factory .NET SDK Reference](/dotnet/api/microsoft.azure.management.datafactory?view=azure-dotnet).

## <a name="python"></a>Python
Úplný návod k vytvoření a monitorování kanálu pomocí Python SDK najdete v tématu [Vytvoření datové továrny a kanálu pomocí Pythonu](quickstart-create-data-factory-python.md).

Pokud chcete monitorovat spuštění kanálu, přidejte následující kód:

```python
# Monitor the pipeline run
time.sleep(30)
pipeline_run = adf_client.pipeline_runs.get(
    rg_name, df_name, run_response.run_id)
print("\n\tPipeline run status: {}".format(pipeline_run.status))
activity_runs_paged = list(adf_client.activity_runs.list_by_pipeline_run(
    rg_name, df_name, pipeline_run.run_id, datetime.now() - timedelta(1),  datetime.now() + timedelta(1)))
print_activity_run_details(activity_runs_paged[0])
```

Kompletní dokumentaci k sadě Python SDK najdete v tématu Referenční informace k [sadě SDK](/python/api/overview/azure/datafactory?view=azure-python)pro Python Data Factory.

## <a name="rest-api"></a>REST API
Úplný návod k vytváření a monitorování kanálu pomocí REST API najdete v tématu [Vytvoření datové továrny a kanálu pomocí REST API](quickstart-create-data-factory-rest-api.md).
 
1. Spusťte následující skript, který bude nepřetržitě kontrolovat stav spuštění kanálu, dokud nedokončí kopírování dat.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
    while ($True) {
        $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
        Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"

        if ($response.Status -eq "InProgress") {
            Start-Sleep -Seconds 15
        }
        else {
            $response | ConvertTo-Json
            break
        }
    }
    ```
2. Spusťte následující skript, který načte podrobnosti o spuštění aktivity kopírování, například velikost načtených/zapsaných dat.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}/activityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```

Úplnou dokumentaci k REST API najdete v [referenčních informacích Data Factory REST API](/rest/api/datafactory/).

## <a name="powershell"></a>PowerShell
Úplný návod k vytváření a monitorování kanálu pomocí PowerShellu najdete v tématu [Vytvoření datové továrny a kanálu pomocí PowerShellu](quickstart-create-data-factory-powershell.md).

1. Spusťte následující skript, který bude nepřetržitě kontrolovat stav spuštění kanálu, dokud nedokončí kopírování dat.

    ```powershell
    while ($True) {
        $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 30
    }
    ```
2. Spusťte následující skript, který načte podrobnosti o spuštění aktivity kopírování, například velikost načtených/zapsaných dat.

    ```powershell
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```

Úplnou dokumentaci k rutinám PowerShellu najdete v tématu [Data Factory powershellové Reference k rutinám](/powershell/module/az.datafactory).

## <a name="next-steps"></a>Další kroky
V tématu [monitorování kanálů pomocí Azure monitor](monitor-using-azure-monitor.md) článku se dozvíte, jak pomocí Azure monitor monitorovat Data Factory kanály. 

