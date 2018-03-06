---
title: "Použití vlastních aktivit v kanálu Azure Data Factory"
description: "Zjistěte, jak vytvořit vlastní aktivity a použít je v kanál služby Azure Data Factory."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: shengc
ms.openlocfilehash: 4b9714bc456ad28d9dd46742ca16f52e68c61399
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Použití vlastních aktivit v kanálu Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Verze 1 – GA](v1/data-factory-use-custom-activities.md)
> * [Verze 2 – Preview](transform-data-using-dotnet-custom-activity.md)

Existují dva typy aktivit, které můžete použít v kanál služby Azure Data Factory.

- [Aktivity přesunu dat](copy-activity-overview.md) pro přesun dat mezi [podporovanými úložišti dat zdroj a jímka](copy-activity-overview.md#supported-data-stores-and-formats).
- [Aktivity transformace dat](transform-data.md) k transformaci dat pomocí výpočetních služeb, například Azure HDInsight, Azure Batch a Azure Machine Learning. 

Pro přesun úložiště dat do nebo z dat objektu pro vytváření dat nepodporuje, nebo transformace nebo zpracovat data způsobem, který není podporován službou Data Factory, můžete vytvořit **vlastní aktivity** s vlastní přesun dat nebo logiku transformaci a použití aktivity v kanálu. Vlastní aktivita běží na logice vlastní kód **Azure Batch** fondu virtuálních počítačů.

> [!NOTE]
> Tento článek se týká verze 2 služby Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), přečtěte si téma [(vlastní) DotNet aktivity v datové továrně verze 1](v1/data-factory-use-custom-activities.md).
 

Viz následující články, pokud začínáte používat službu Azure Batch:

* [Základy Azure Batch](../batch/batch-technical-overview.md) Přehled služby Azure Batch.
* [New-AzureRmBatchAccount](/powershell/module/azurerm.batch/New-AzureRmBatchAccount?view=azurermps-4.3.1) rutiny k vytvoření účtu Azure Batch (nebo) [portál Azure](../batch/batch-account-create-portal.md) vytvoření účtu Azure Batch pomocí portálu Azure. V tématu [pomocí prostředí PowerShell ke správě účtu Azure Batch](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) článku podrobné pokyny k použití rutiny.
* [Nový-AzureBatchPool](/powershell/module/azurerm.batch/New-AzureBatchPool?view=azurermps-4.3.1) rutiny vytvoření fondu Azure Batch.

## <a name="azure-batch-linked-service"></a>Azure Batch propojené služby 
Následující kód JSON určuje ukázku Azure Batch propojené služby. Podrobnosti najdete v tématu [výpočetní prostředí podporovaných službou Azure Data Factory](compute-linked-services.md)

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "batchaccount",
            "accessKey": {
                "type": "SecureString",
                "value": "access key"
            },
            "batchUri": "https://batchaccount.region.batch.azure.com",
            "poolName": "poolname",
            "linkedServiceName": {
                "referenceName": "StorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

 Další informace o Azure Batch propojené služby, najdete v části [výpočetní propojené služby](compute-linked-services.md) článku. 

## <a name="custom-activity"></a>Vlastní aktivita

Následující fragment kódu JSON definuje kanál s aktivitou jednoduché vlastní. Definici aktivity obsahuje odkaz na službu Azure Batch propojený. 

```json
{
    "name": "MyCustomActivityPipeline",
    "properties": {
      "description": "Custom activity sample",
      "activities": [{
        "type": "Custom",
        "name": "MyCustomActivity",
        "linkedServiceName": {
          "referenceName": "AzureBatchLinkedService",
          "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "command": "helloworld.exe",
          "folderPath": "customactv2/helloworld",
          "resourceLinkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
          }
        }
      }]
    }
  }
```

V této ukázce helloworld.exe je uložen ve složce customactv2/helloworld účtu úložiště Azure používá v resourceLinkedService vlastní aplikaci. Vlastní aktivity odešle tento vlastní aplikace lze spustit v Azure Batch. Můžete nahradit příkaz do všech upřednostňovaných aplikací, které mohou být provedeny na cílové uzly fondu Azure Batch jako operační systém. 

Následující tabulka popisuje názvy a popisy vlastností, které jsou specifické pro tuto aktivitu. 

| Vlastnost              | Popis                              | Požaduje se |
| :-------------------- | :--------------------------------------- | :------- |
| jméno                  | Název aktivity v kanálu     | Ano      |
| description           | Popisuje, jakým způsobem aktivita naloží text.  | Ne       |
| type                  | Vlastní aktivity, typ aktivity je **vlastní**. | Ano      |
| linkedServiceName     | Propojené služby Azure batch. Další informace o této propojené služby najdete v tématu [výpočetní propojené služby](compute-linked-services.md) článku.  | Ano      |
| příkaz               | Příkaz vlastní aplikace má být proveden. Pokud aplikace je již k dispozici na uzlu fondu Azure Batch, mohou být přeskočeny resourceLinkedService a folderPath. Například můžete zadat příkaz, který má být `cmd /c dir`, která nativně podporuje uzlu fondu Batch systému Windows. | Ano      |
| resourceLinkedService | Propojená služba Azure úložiště k účtu úložiště se uloží vlastní aplikace | Ne       |
| folderPath            | Cesta ke složce vlastní aplikace a všechny jeho závislé součásti | Ne       |
| referenceObjects      | Pole existující propojené služby a datové sady. Odkazovaná propojené služby a datové sady se předávají do vlastní aplikace ve formátu JSON, vlastní kód můžete odkazovat na prostředky objektu pro vytváření dat | Ne       |
| ExtendedProperties    | Uživatelem definované vlastnosti, které lze předat vlastní aplikaci ve formátu JSON, takže další vlastnosti, můžete odkazovat vlastní kód | Ne       |

## <a name="executing-commands"></a>Provádění příkazů

Můžete přímo spustit příkaz pomocí vlastní aktivity. V následujícím příkladu spustí příkaz "echo hello world" v cílové uzly fondu Azure Batch a vytiskne výstup StdOut. 

  ```json
  {
    "name": "MyCustomActivity",
    "properties": {
      "description": "Custom activity sample",
      "activities": [{
        "type": "Custom",
        "name": "MyCustomActivity",
        "linkedServiceName": {
          "referenceName": "AzureBatchLinkedService",
          "type": "LinkedServiceReference"
        },
        "typeProperties": {
          "command": "cmd /c echo hello world"
        }
      }]
    }
  } 
  ```

## <a name="passing-objects-and-properties"></a>Předávání objektů a vlastností

Tento příklad ukazuje, jak můžete použít referenceObjects a extendedProperties předat objekty pro vytváření dat a uživatelem definované vlastnosti k vaší vlastní aplikaci. 


```json
{
  "name": "MyCustomActivityPipeline",
  "properties": {
    "description": "Custom activity sample",
    "activities": [{
      "type": "Custom",
      "name": "MyCustomActivity",
      "linkedServiceName": {
        "referenceName": "AzureBatchLinkedService",
        "type": "LinkedServiceReference"
      },
      "typeProperties": {
        "command": "SampleApp.exe",
        "folderPath": "customactv2/SampleApp",
        "resourceLinkedService": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        },
        "referenceObjects": {
          "linkedServices": [{
            "referenceName": "AzureBatchLinkedService",
            "type": "LinkedServiceReference"
          }]
        },
        "extendedProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "aSampleSecureString"
            },
            "PropertyBagPropertyName1": "PropertyBagValue1",
            "propertyBagPropertyName2": "PropertyBagValue2",
            "dateTime1": "2015-04-12T12:13:14Z"              
        }
      }
    }]
  }
}
```

Po provedení aktivity referenceObjects a extendedProperties jsou uloženy v následující soubory, které jsou nasazeny do stejné složky provádění SampleApp.exe: 

- activity.json

  Ukládá extendedProperties a vlastnosti vlastní aktivity. 

- linkedServices.json

  Ukládá pole propojené služby definované ve vlastnosti referenceObjects. 

- datasets.json

  Ukládá v vlastnost referenceObjects definována pole datové sady. 

Následující ukázka kódu ukazují, jak SampleApp.exe můžete přistupovat k požadované informace ze souborů JSON: 

```csharp
using Newtonsoft.Json;
using System;
using System.IO;

namespace SampleApp
{
    class Program
    {
        static void Main(string[] args)
        {
            //From Extend Properties
            dynamic activity = JsonConvert.DeserializeObject(File.ReadAllText("activity.json"));
            Console.WriteLine(activity.typeProperties.extendedProperties.connectionString.value);

            // From LinkedServices
            dynamic linkedServices = JsonConvert.DeserializeObject(File.ReadAllText("linkedServices.json"));
            Console.WriteLine(linkedServices[0].properties.typeProperties.connectionString.value);
        }
    }
}
```

## <a name="retrieve-execution-outputs"></a>Načtení výstupy provádění

  Můžete spustit spuštění kanálu pomocí následujícího příkazu Powershellu: 

  ```.powershell
  $runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
  ```
  Po spuštění kanálu, můžete zkontrolovat výstupu spuštění použijte následující příkazy: 

  ```.powershell
  while ($True) {
      $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

      if(!$result) {
          Write-Host "Waiting for pipeline to start..." -foregroundcolor "Yellow"
      }
      elseif (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
          Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
      }
      else {
          Write-Host "Pipeline '"$pipelineName"' run finished. Result:" -foregroundcolor "Yellow"
          $result
          break
      }
      ($result | Format-List | Out-String)
      Start-Sleep -Seconds 15
  }

  Write-Host "Activity `Output` section:" -foregroundcolor "Yellow"
  $result.Output -join "`r`n"

  Write-Host "Activity `Error` section:" -foregroundcolor "Yellow"
  $result.Error -join "`r`n"
  ```

  **Stdout** a **stderr** vlastní aplikace, které jsou uloženy do **adfjobs** kontejneru v propojené službě Azure Storage je definován při vytvoření propojené Azure Batch Služba s identifikátorem GUID úlohy. Podrobné cestu můžete získat z výstupu aktivita běžet, jak je znázorněno v následujícím fragmentu kódu: 

  ```shell
  Pipeline ' MyCustomActivity' run finished. Result:

  ResourceGroupName : resourcegroupname
  DataFactoryName   : datafactoryname
  ActivityName      : MyCustomActivity
  PipelineRunId     : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
  PipelineName      : MyCustomActivity
  Input             : {command}
  Output            : {exitcode, outputs, effectiveIntegrationRuntime}
  LinkedServiceName : 
  ActivityRunStart  : 10/5/2017 3:33:06 PM
  ActivityRunEnd    : 10/5/2017 3:33:28 PM
  DurationInMs      : 21203
  Status            : Succeeded
  Error             : {errorCode, message, failureType, target}

  Activity Output section:
  "exitcode": 0
  "outputs": [
    "https://shengcstorbatch.blob.core.windows.net/adfjobs/<GUID>/output/stdout.txt",
    "https://shengcstorbatch.blob.core.windows.net/adfjobs/<GUID>/output/stderr.txt"
  ]
  "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
  Activity Error section:
  "errorCode": ""
  "message": ""
  "failureType": ""
  "target": "MyCustomActivity"
  ```
Pokud chcete pracovat s obsahem stdout.txt v podřízené aktivity, můžete získat cestu k souboru stdout.txt ve výrazu "@activity(MyCustomActivity).output.outputs [0]". 

  > [!IMPORTANT]
  > - Activity.json, linkedServices.json a datasets.json jsou uloženy ve složce modulu runtime úlohy Batch. V tomto příkladu activity.json, linkedServices.json a datasets.json jsou uloženy v "https://adfv2storage.blob.core.windows.net/adfjobs/<GUID>/runtime/" cesta. V případě potřeby, musíte je vyčistit samostatně. 
  > - Pro účely propojené služby, Self-Hosted integrace Runtime, citlivé informace, jako jsou klíče nebo hesla zašifrován modulem Runtime integrace Self-Hosted zajistit přihlašovací údaje definované zůstane u zákazníka prostředí privátní sítě. Když odkazuje kód vlastní aplikace tímto způsobem, může být chybějící některých polí. Použijte SecureString v extendedProperties místo použití odkaz na propojenou službu, v případě potřeby. 

## <a name="difference-between-custom-activity-in-azure-data-factory-version-2-and-custom-dotnet-activity-in-azure-data-factory-version-1"></a>Rozdíl mezi vlastní aktivity v Azure Data Factory verze 2 a aktivity DotNet (vlastní) v Azure Data Factory verze 1

  V Azure Data Factory verze 1, implementovat kód aktivity DotNet (vlastní) tak, že vytvoříte .net projektu knihovny tříd s třídou, která implementuje rozhraní IDotNetActivity Metoda Execute. Propojené služby, datové sady a rozšířené vlastnosti v datové části JSON aktivity DotNet (vlastní) jsou předaný metodě provádění jako objektů se silným typem. Podrobnosti najdete v části [DotNet (vlastní) ve verzi 1](v1/data-factory-use-custom-activities.md). Z důvodu tato implementace vlastní kód musí být napsané v rozhraní .net Framework 4.5.2 a provést na uzlech fondu Batch systému Windows Azure. 

  V Azure Data Factory V2 vlastní aktivity není nutné k implementaci rozhraní .net. Můžete nyní přímo spustit příkazy, skripty a spustit vlastní vlastní kód vyhovuje podmínkám spustitelný soubor. Zadáním příkazu vlastností a folderPath dosáhnout tak. Vlastní aktivity odešle spustitelný soubor a závislosti v folderpath a spustí příkaz pro vás. 

  Propojené služby, datové sady (definovanou v referenceObjects) a rozšířené vlastnosti definované v datové části JSON vlastní aktivity lze přistupovat pomocí vašeho spustitelného souboru jako soubory JSON. Můžete přistupovat pomocí serializátor JSON, jak je znázorněno v předchozí ukázce kódu SampleApp.exe požadované vlastnosti. 

  S změny uváděné ve vlastní aktivita služby Azure Data Factory V2 jsou volně zapisovat logiku vlastního kódu ve vašem preferovaném jazyce a spouštět je v systému Windows a operační systémy Linux podporované nástrojem Azure Batch. 

  Následující tabulka popisuje rozdíly mezi Data Factory V2 vlastní aktivity a Data Factory verze 1 (vlastní) DotNet aktivity: 


|Rozdíly      |verze 2 vlastní aktivity      | verze 1 (vlastní) aktivity DotNet.      |
| ---- | ---- | ---- |
|Jak je definovaný vlastní logiky      |Spuštěním jakéhokoliv spustitelného souboru (stávající nebo implementace vlastní spustitelný soubor)      |Implementací .net knihovny DLL      |
|Prostředí pro spuštění vlastní logiky      |Windows nebo Linux      |Windows (rozhraní .net Framework 4.5.2)      |
|Provádění skriptů      |Podpora spuštění skriptů přímo (například "cmd /c echo hello world" na virtuální počítač s Windows)      |Vyžaduje implementaci v rozhraní .net knihovny DLL      |
|Datová sada vyžaduje      |Nepovinné      |Potřeba řetězu aktivit a předání informací      |
|Předávání informací z aktivity do vlastní logiky      |Prostřednictvím ReferenceObjects (LinkedServices a datové sady) a ExtendedProperties (vlastní vlastnosti)      |Prostřednictvím ExtendedProperties (vlastní vlastnosti), vstupní a výstupní datové sady      |
|Načtení informací v vlastní logiky      |Analyzovat activity.json, linkedServices.json a datasets.json uložené ve složce ke spustitelnému souboru      |Pomocí .net SDK (.Net rámce 4.5.2)      |
|Protokolování      |Zapíše přímo do STDOUT      |Implementace protokolovacího nástroje v rozhraní .net knihovny DLL      |


  Pokud máte existující kód .net vytvořené pro verzi aktivity DotNet 1 (vlastní), je třeba upravit kód pro ně pro práci s verze 2 vlastní aktivitu se podle následujících pokynů vysoké úrovně:  

   - Změnit projekt z .net knihovny tříd do konzoly aplikace. 
   - Spustit aplikaci s metodu Main, metoda spouštění rozhraní IDotNetActivity se už nevyžaduje. 
   - Číst a analyzovat propojené služby, datové sady a aktivity s serializátor JSON místo jako objektů se silným typem a předat hodnoty požadované vlastnosti logika hlavní vlastní kód. Naleznete v předchozích SampleApp.exe kód jako ukázka. 
   - Protokolovač objekt již není podporována, spustitelný soubor výstupy může být tisk do konzoly a je uložen do stdout.txt. 
   - Balíček Microsoft.Azure.Management.DataFactories NuGet se už nevyžaduje. 
   - Kompilace kódu, nahrát spustitelný soubor a závislosti do služby Azure Storage a zadejte cestu ve vlastnosti folderPath. 

Kompletní příklad, koncová DLL a kanál ukázku popisu v objektu pro vytváření dat verze 1 dokumentu [použít vlastní aktivity v kanálu Azure Data Factory](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities) do styl vlastní aktivity verze 2 objekt pro vytváření dat může být přepsán. Odkazovat [ukázkové aktivity vlastní objekt pro vytváření dat verze 2](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFv2CustomActivitySample). 

## <a name="auto-scaling-of-azure-batch"></a>Automatické škálování služby Azure Batch
Můžete také vytvořit fond Azure Batch s **škálování** funkce. Můžete například vytvořit fondu azure batch s 0 vyhrazených virtuálních počítačích a vzorec škálování na základě počtu úkolů čekajících na zpracování. 

Vzorec ukázka tady dosahuje následující chování: při vytvoření fondu, začne s virtuálním Počítačem 1. Metrika $PendingTasks definuje počet úloh v spuštěná + aktivní (zařazených do fronty) stavu.  Vzorec najde průměrný počet úkolů čekajících na zpracování v posledních 180 sekund a nastaví TargetDedicated odpovídajícím způsobem. Zajišťuje, že TargetDedicated nikdy překročí 25 virtuálních počítačů. Tak, jako jsou odeslány nové úkoly, fondu automaticky zvětšování a jako dokončení úkolů, virtuální počítače stane volné jeden po druhém a automatické škálování zmenšuje těchto virtuálních počítačů. startingNumberOfVMs a maxNumberofVMs lze upravit vašim potřebám.

Vzorec škálování:

``` 
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

V tématu [automatické škálování výpočetních uzlů ve fondu Azure Batch](../batch/batch-automatic-scaling.md) podrobnosti.

Pokud fondu používá výchozí [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), služba Batch může trvat 15 až 30 minut Příprava virtuálního počítače před spuštěním vlastní aktivity.  Pokud fondu používá jiný autoScaleEvaluationInterval, služba Batch může trvat autoScaleEvaluationInterval + 10 minut.


## <a name="next-steps"></a>Další postup
Najdete v následujících článcích, které vysvětlují, jak k transformaci dat jinými způsoby: 

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita Hive](transform-data-using-hadoop-hive.md)
* [Pig aktivity](transform-data-using-hadoop-pig.md)
* [Činnost MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streamované aktivitě](transform-data-using-hadoop-streaming.md)
* [Spark aktivity](transform-data-using-spark.md)
* [Machine Learning dávkového spuštění aktivity](transform-data-using-machine-learning.md)
* [Aktivita uložené procedury](transform-data-using-stored-procedure.md)
