---
title: Prostřednictvím kódu programu Sledování služby Azure data factory | Microsoft Docs
description: Naučte se monitorovat kanál v objekt pro vytváření dat pomocí různých software development Kit (SDK).
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: 5b7b473bdc411e11d6d1c90c63c4f05c6dac3280
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34619594"
---
# <a name="programmatically-monitor-an-azure-data-factory"></a>Prostřednictvím kódu programu Sledování služby Azure data factory
Tento článek popisuje, jak monitorovat kanál v objekt pro vytváření dat pomocí různých software development Kit (SDK). 

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [monitorování a Správa kanálů v objektu pro vytváření dat version1](v1/data-factory-monitor-manage-pipelines.md).

## <a name="data-range"></a>Oblast dat

Objekt pro vytváření dat ukládá jenom kanálu spouští dat 45 dní. Když dotazujete prostřednictvím kódu programu pro data o spuštění kanálu pro vytváření dat – například pomocí příkazu prostředí PowerShell `Get-AzureRmDataFactoryV2PipelineRun` -nejsou žádné maximální data pro volitelné `LastUpdatedAfter` a `LastUpdatedBefore` parametry. Ale pokud dotaz na data v minulém roce, například dotaz nevrátí chybu, ale vrátí jenom kanálu spuštění data z posledních 45 dní.

Pokud chcete zachovat kanálu spouští dat déle než 45 dní, nastavit vlastní protokolování diagnostiky s [Azure monitorování](monitor-using-azure-monitor.md).

## <a name="net"></a>.NET
Kompletní a podrobný postup vytváření a monitorování kanálu pomocí sady .NET SDK, naleznete v části [vytvořte objekt pro vytváření dat a kanál pomocí rozhraní .NET](quickstart-create-data-factory-dot-net.md).

1. Přidejte následující kód do nepřetržitě zkontrolovat stav spustit, dokud nedokončí kopírování dat kanálu.

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

2. Přidejte následující kód načte kopie aktivity spustit podrobnosti, například velikost dat číst nebo zapisovat.

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

Úplnou dokumentaci k rozhraní .NET SDK najdete v části [referenční Data Factory .NET SDK](/dotnet/api/microsoft.azure.management.datafactory?view=azure-dotnet).

## <a name="python"></a>Python
Kompletní a podrobný postup vytváření a monitorování kanálu pomocí sady SDK pro Python, najdete v části [vytvořte objekt pro vytváření dat a kanál pomocí Pythonu](quickstart-create-data-factory-python.md).

K monitorování kanálu spuštění, přidejte následující kód:

```python
#Monitor the pipeline run
time.sleep(30)
pipeline_run = adf_client.pipeline_runs.get(rg_name, df_name, run_response.run_id)
print("\n\tPipeline run status: {}".format(pipeline_run.status))
activity_runs_paged = list(adf_client.activity_runs.list_by_pipeline_run(rg_name, df_name, pipeline_run.run_id, datetime.now() - timedelta(1),  datetime.now() + timedelta(1)))
print_activity_run_details(activity_runs_paged[0])
```

Kompletní dokumentaci na Python SDK najdete v tématu [referenční Data Factory Python SDK](/python/api/overview/azure/datafactory?view=azure-python).

## <a name="rest-api"></a>REST API
Kompletní a podrobný postup vytváření a monitorování kanálu pomocí rozhraní REST API, najdete v části [vytvořte objekt pro vytváření dat a kanál pomocí rozhraní REST API](quickstart-create-data-factory-rest-api.md).
 
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

Úplnou dokumentaci k REST API, najdete v části [REST API služby Data Factory odkaz](/rest/api/datafactory/).

## <a name="powershell"></a>PowerShell
Kompletní a podrobný postup vytváření a monitorování kanálu pomocí prostředí PowerShell, najdete v části [vytvořte objekt pro vytváření dat a kanál pomocí prostředí PowerShell](quickstart-create-data-factory-powershell.md).

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

Kompletní dokumentaci o rutinách prostředí PowerShell najdete v tématu [odkazu na rutiny Powershellu objekt pro vytváření dat](/powershell/module/azurerm.datafactoryv2/?view=azurermps-4.4.1).

## <a name="next-steps"></a>Další postup
V tématu [monitorování kanálů pomocí Azure monitorování](monitor-using-azure-monitor.md) článku Další informace o použití Azure monitorování pro monitorování kanálů služby Data Factory. 

