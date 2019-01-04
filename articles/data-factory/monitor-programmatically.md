---
title: Programově monitorovat objekt pro vytváření dat Azure | Dokumentace Microsoftu
description: Zjistěte, jak monitorovat kanál v datové továrně pomocí různých software development Kit (SDK).
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: e004dc6b7d78849705f8d3fa3545efe7318d3911
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54022797"
---
# <a name="programmatically-monitor-an-azure-data-factory"></a>Programové monitorování služby Azure data factory
Tento článek popisuje, jak monitorovat kanál v datové továrně pomocí různých software development Kit (SDK). 

## <a name="data-range"></a>Rozsah dat

Objekt pro vytváření dat se uchovávají pouze spuštění kanálu dat pro 45 dní. Při dotazování prostřednictvím kódu programu pro data o spuštění kanálu služby Data Factory – například pomocí příkazu Powershellu `Get-AzureRmDataFactoryV2PipelineRun` -nejsou žádné maximální data pro volitelné `LastUpdatedAfter` a `LastUpdatedBefore` parametry. Ale pokud dotaz na data v minulém roce, třeba dotaz nevrací chybu, ale pouze vrátí kanál spuštění data z uplynulých 45 dní.

Pokud chcete zachovat spuštění kanálu dat pro více než 45 dnů, nastavte si vlastní protokolování diagnostiky s [Azure Monitor](monitor-using-azure-monitor.md).

## <a name="net"></a>.NET
Kompletní postup vytváření a monitorování kanálu pomocí sady .NET SDK najdete v části [vytvoření datové továrny a kanálu pomocí rozhraní .NET](quickstart-create-data-factory-dot-net.md).

1. Přidejte následující kód, který bude nepřetržitě kontrolovat stav spuštění kanálu, dokud se kopírování dat nedokončí.

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

2. Přidejte následující kód do této načte aktivita kopírování nezobrazí údaje, například velikost načtených/zapsaných.

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

Úplnou dokumentaci k sadě .NET SDK najdete v části [referenční Data Factory .NET SDK](/dotnet/api/microsoft.azure.management.datafactory?view=azure-dotnet).

## <a name="python"></a>Python
Kompletní postup vytváření a monitorování kanálu pomocí sady Python SDK najdete v části [vytvoření datové továrny a kanálu pomocí Pythonu](quickstart-create-data-factory-python.md).

Pokud chcete monitorovat spuštění kanálu, přidejte následující kód:

```python
#Monitor the pipeline run
time.sleep(30)
pipeline_run = adf_client.pipeline_runs.get(rg_name, df_name, run_response.run_id)
print("\n\tPipeline run status: {}".format(pipeline_run.status))
activity_runs_paged = list(adf_client.activity_runs.list_by_pipeline_run(rg_name, df_name, pipeline_run.run_id, datetime.now() - timedelta(1),  datetime.now() + timedelta(1)))
print_activity_run_details(activity_runs_paged[0])
```

Úplnou dokumentaci k sadě Python SDK najdete v části [referenční informace k Data Factory Python SDK](/python/api/overview/azure/datafactory?view=azure-python).

## <a name="rest-api"></a>REST API
Kompletní postup vytváření a monitorování kanálu pomocí rozhraní REST API najdete v části [vytvoření datové továrny a kanálu pomocí rozhraní REST API](quickstart-create-data-factory-rest-api.md).
 
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

    ```PowerShell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}/activityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```

Úplnou dokumentaci k rozhraní REST API najdete v části [odkaz na rozhraní REST API služby Data Factory](/rest/api/datafactory/).

## <a name="powershell"></a>PowerShell
Kompletní postup vytváření a monitorování kanálu pomocí Powershellu najdete v části [vytvoření datové továrny a kanálu pomocí Powershellu](quickstart-create-data-factory-powershell.md).

1. Spusťte následující skript, který bude nepřetržitě kontrolovat stav spuštění kanálu, dokud nedokončí kopírování dat.

    ```powershell
    while ($True) {
        $run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

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
    $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```

Úplnou dokumentaci o rutinách prostředí PowerShell najdete v části [Reference k rutinám Powershellu objekt pro vytváření dat](/powershell/module/azurerm.datafactoryv2/?view=azurermps-4.4.1).

## <a name="next-steps"></a>Další postup
Zobrazit [monitorování kanálů pomocí Azure monitoru](monitor-using-azure-monitor.md) článku se dozvíte o použití Azure Monitor k monitorování kanálů Data Factory. 

