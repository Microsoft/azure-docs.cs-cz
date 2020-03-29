---
title: Programové monitorování datové továrny Azure
description: Zjistěte, jak sledovat kanál v datové továrně pomocí různých sad pro vývoj softwaru (SDK).
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2018
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 43a31d588ff6616d7200d9773883ce5da570b100
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74927355"
---
# <a name="programmatically-monitor-an-azure-data-factory"></a>Programové monitorování datové továrny Azure
Tento článek popisuje, jak sledovat kanál v datové továrně pomocí různých sad pro vývoj softwaru (SDK). 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="data-range"></a>Rozsah dat

Data Factory ukládá pouze data spuštění kanálu po dobu 45 dnů. Při programovém dotazu na data o spuštění kanálu datové továrny – například pomocí `Get-AzDataFactoryV2PipelineRun` `LastUpdatedAfter` příkazu PowerShell – neexistují žádná maximální data pro volitelné a `LastUpdatedBefore` parametry. Ale pokud dotaz na data za poslední rok, například dotaz nevrátí chybu, ale vrátí pouze data spuštění kanálu z posledních 45 dnů.

Pokud chcete zachovat data spuštění kanálu po dobu delší než 45 dní, nastavte vlastní diagnostické protokolování pomocí [Azure Monitoru](monitor-using-azure-monitor.md).

## <a name="net"></a>.NET
Úplný návod vytváření a monitorování kanálu pomocí sady .NET SDK naleznete [v tématu Vytvoření datové továrny a kanálu pomocí rozhraní .NET](quickstart-create-data-factory-dot-net.md).

1. Přidejte následující kód, abyste průběžně kontrolovali stav spuštění kanálu, dokud nedokončí kopírování dat.

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

2. Přidejte následující kód, který načte podrobnosti spuštění aktivity kopírování, například velikost přečtených a zapsaných dat.

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

Úplnou dokumentaci k sada .NET SDK naleznete v [tématu Data Factory .NET SDK reference](/dotnet/api/microsoft.azure.management.datafactory?view=azure-dotnet).

## <a name="python"></a>Python
Úplný návod vytváření a monitorování kanálu pomocí sady Python SDK najdete [v tématu Vytvoření datové továrny a kanálu pomocí Pythonu](quickstart-create-data-factory-python.md).

Chcete-li sledovat spuštění kanálu, přidejte následující kód:

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

Kompletní dokumentaci k pythonu SDK najdete v [tématu Data Factory Python SDK reference](/python/api/overview/azure/datafactory?view=azure-python).

## <a name="rest-api"></a>REST API
Úplný návod vytváření a monitorování kanálu pomocí rozhraní REST API najdete v [tématu Vytvoření datové továrny a kanálu pomocí rozhraní REST API](quickstart-create-data-factory-rest-api.md).
 
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

Úplnou dokumentaci k rozhraní REST API najdete v [tématu Reference rozhraní REST API datové továrny](/rest/api/datafactory/).

## <a name="powershell"></a>PowerShell
Úplný návod vytváření a monitorování kanálu pomocí PowerShellu najdete [v tématu Vytvoření datové továrny a kanálu pomocí prostředí PowerShell](quickstart-create-data-factory-powershell.md).

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

Úplnou dokumentaci k rutinám prostředí PowerShell naleznete v [tématu Reference rutiny prostředí Data Factory PowerShell](/powershell/module/az.datafactory).

## <a name="next-steps"></a>Další kroky
Informace o používání Azure Monitoru ke sledování kanálů Datové továrny najdete v článku [Monitorování pomocí](monitor-using-azure-monitor.md) azure monitoru. 

