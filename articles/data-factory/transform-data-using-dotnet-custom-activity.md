---
title: Použití vlastních aktivit v kanálu
description: Naučte se vytvářet vlastní aktivity pomocí .NET a pak aktivity používat v kanálu Azure Data Factory.
services: data-factory
ms.service: data-factory
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/26/2018
ms.openlocfilehash: e84f7a2ee8c2f7a57ce1734ad3392a217d6de5fe
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92632103"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Použití vlastních aktivit v kanálu Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-use-custom-activities.md)
> * [Aktuální verze](transform-data-using-dotnet-custom-activity.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Existují dva typy aktivit, které můžete použít v kanálu Azure Data Factory.

- [Aktivity přesunu dat](copy-activity-overview.md) pro přesun dat mezi [podporovanými úložišti dat a úložišti dat jímky](copy-activity-overview.md#supported-data-stores-and-formats).
- [Aktivity transformace dat](transform-data.md) pro transformaci dat pomocí výpočetních služeb, jako je Azure HDInsight, Azure Batch a Azure Machine Learning.

Chcete-li přesunout data do nebo z úložiště dat, které Data Factory nepodporuje nebo transformovat/zpracovávat data způsobem, který není podporován Data Factory, můžete vytvořit **vlastní aktivitu** s vlastní logikou přesunu dat nebo transformaci a použít aktivitu v kanálu. Vlastní aktivita spustí vlastní logiku kódu ve fondu **Azure Batch** virtuálních počítačů.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pokud Azure Batch službu nepoužíváte, přečtěte si následující články:

* [Azure Batch základy](../batch/batch-technical-overview.md) pro přehled služby Azure Batch.
* Rutina [New-AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) pro vytvoření účtu Azure Batch (nebo) [Azure Portal](../batch/batch-account-create-portal.md) k vytvoření účtu Azure Batch pomocí Azure Portal. Podrobné pokyny k používání rutiny najdete v článku [použití PowerShellu ke správě Azure Batch účtu](/archive/blogs/windowshpc/using-azure-powershell-to-manage-azure-batch-account) .
* Rutina [New-AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) pro vytvoření fondu Azure Batch.

## <a name="azure-batch-linked-service"></a>Propojená služba Azure Batch

Následující JSON definuje ukázkovou Azure Batch propojenou službu. Podrobnosti najdete v tématu [výpočetní prostředí podporovaná nástrojem Azure Data Factory](compute-linked-services.md)

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
    }
}
```

 Další informace o Azure Batch propojených službách najdete v článku věnovaném [COMPUTE propojeným službám](compute-linked-services.md) .

## <a name="custom-activity"></a>Vlastní aktivita

Následující fragment kódu JSON definuje kanál s jednoduchou vlastní aktivitou. Definice aktivity obsahuje odkaz na propojenou službu Azure Batch.

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

V této ukázce je helloworld.exe vlastní aplikací uloženou ve složce customactv2/HelloWorld účtu Azure Storage, který se používá v resourceLinkedService. Vlastní aktivita odesílá tuto vlastní aplikaci k provedení Azure Batch. Příkaz můžete nahradit pro libovolnou upřednostňovanou aplikaci, kterou lze spustit v cílovém operačním systému uzlů Azure Batchch fondů.

V následující tabulce jsou popsány názvy a popisy vlastností, které jsou specifické pro tuto aktivitu.

| Vlastnost              | Popis                              | Povinné |
| :-------------------- | :--------------------------------------- | :------- |
| name                  | Název aktivity v kanálu     | Ano      |
| description           | Text popisující, co aktivita dělá.  | Ne       |
| typ                  | U vlastní aktivity je typ aktivity **vlastní** . | Ano      |
| linkedServiceName     | Propojená služba s Azure Batch. Další informace o této propojené službě najdete v článku věnovaném [propojeným službám COMPUTE](compute-linked-services.md) .  | Ano      |
| command               | Příkaz vlastní aplikace, která má být provedena. Pokud je aplikace již k dispozici na uzlu Azure Batch fondu, lze resourceLinkedService a folderPath přeskočit. Můžete například zadat příkaz `cmd /c dir` , který bude nativně podporován uzlem fondu služby Batch systému Windows. | Ano      |
| resourceLinkedService | Azure Storage propojených služeb k účtu úložiště, ve kterém je vlastní aplikace uložená. | Bez &#42;       |
| folderPath            | Cesta ke složce vlastní aplikace a všech jejích závislostí<br/><br/>Pokud máte závislosti uložené v podsložkách – to znamená, že v hierarchické struktuře složek pod *FolderPath* – struktura složek se v současnosti při kopírování souborů do Azure Batch nesloučí. To znamená, že všechny soubory se zkopírují do jediné složky bez podsložek. Chcete-li toto chování obejít, zvažte komprimaci souborů, kopírování komprimovaného souboru a jeho rozzipovává pomocí vlastního kódu v požadovaném umístění. | Bez &#42;       |
| referenceObjects      | Pole existujících propojených služeb a datových sad. Odkazované propojené služby a datové sady jsou předány do vlastní aplikace ve formátu JSON, aby váš vlastní kód mohl odkazovat na prostředky Data Factory | Ne       |
| extendedProperties    | Uživatelsky definované vlastnosti, které se dají předat vlastní aplikaci ve formátu JSON, aby váš vlastní kód mohl odkazovat na další vlastnosti | Ne       |
| retentionTimeInDays | Doba uchování souborů odeslaných pro vlastní aktivitu. Výchozí hodnota je 30 dní. | Ne |

&#42; vlastnosti `resourceLinkedService` a `folderPath` musí být buď zadány, nebo musí být obě vynechány.

> [!NOTE]
> Pokud předáváte propojené služby jako referenceObjects v rámci vlastní aktivity, je dobrým postupem zabezpečení předat Azure Key Vault propojenou službu s povoleným povolením (protože neobsahuje žádné zabezpečené řetězce) a načíst přihlašovací údaje pomocí tajného názvu přímo z Key Vault z kódu. [Tady](https://github.com/nabhishek/customactivity_sample/tree/linkedservice) najdete příklad, který odkazuje na propojenou službu s podporou integrace, načítá přihlašovací údaje z Key Vault a pak přistupuje k úložišti v kódu.

## <a name="custom-activity-permissions"></a>Vlastní oprávnění aktivity

Vlastní aktivita nastaví Azure Batch účet automatického uživatele na *přístup bez oprávnění správce s oborem úkolu* (výchozí specifikace automatického uživatele). Úroveň oprávnění účtu automatického uživatele nemůžete změnit. Další informace najdete v tématu [spuštění úloh v části uživatelské účty v dávce | Účty automatických uživatelů](../batch/batch-user-accounts.md#auto-user-accounts).

## <a name="executing-commands"></a>Provádění příkazů

Můžete přímo spustit příkaz pomocí vlastní aktivity. Následující příklad spustí příkaz "echo Hello World" v cílových uzlech Azure Batch fondu a vytiskne výstup do STDOUT.

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

Tento příklad ukazuje, jak můžete použít referenceObjects a extendedProperties k předání Data Factory objektů a uživatelsky definovaných vlastností do vlastní aplikace.

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

Po spuštění aktivity jsou referenceObjects a extendedProperties uloženy v následujících souborech, které jsou nasazeny do stejné spouštěcí složky SampleApp.exe:

- `activity.json`

  Ukládá extendedProperties a vlastnosti vlastní aktivity.

- `linkedServices.json`

  Ukládá pole propojených služeb definovaných ve vlastnosti referenceObjects.

- `datasets.json`

  Ukládá pole datových sad definovaných ve vlastnosti referenceObjects.

Následující vzorový kód ukazuje, jak SampleApp.exe získat přístup k požadovaným informacím ze souborů JSON:

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
            Console.WriteLine(linkedServices[0].properties.typeProperties.accountName);
        }
    }
}
```

## <a name="retrieve-execution-outputs"></a>Načíst výstupy spuštění

Spuštění kanálu můžete spustit pomocí následujícího příkazu PowerShellu:

```powershell
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
```

Když je kanál spuštěný, můžete kontrolovat výstup spouštění pomocí následujících příkazů:

```powershell
while ($True) {
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

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

**Stdout** a **stderr** vlastní aplikace se uloží do kontejneru **adfjobs** v propojené službě Azure Storage, kterou jste definovali při vytváření Azure Batch propojených služeb s identifikátorem GUID úlohy. Podrobnou cestu můžete získat z výstupu spuštění aktivity, jak je znázorněno v následujícím fragmentu kódu:

```
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
  "https://<container>.blob.core.windows.net/adfjobs/<GUID>/output/stdout.txt",
  "https://<container>.blob.core.windows.net/adfjobs/<GUID>/output/stderr.txt"
]
"effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)"
Activity Error section:
"errorCode": ""
"message": ""
"failureType": ""
"target": "MyCustomActivity"
```

Pokud chcete využívat obsah stdout.txt v rámci navazujících aktivit, můžete získat cestu k souboru stdout.txt ve výrazu " \@ Activity (' MyCustomActivity '). Output. Outputs [0]".

> [!IMPORTANT]
> - activity.json, linkedServices.json a datasets.json, se ukládají do složky runtime dávkové úlohy. V tomto příkladu jsou uloženy v cestě activity.json, linkedServices.json a datasets.json `"https://adfv2storage.blob.core.windows.net/adfjobs/\<GUID>/runtime/"` . V případě potřeby je potřeba je vyčistit samostatně.
> - U propojených služeb, které používají Self-Hosted Integration Runtime, jsou citlivé informace, jako jsou klíče nebo hesla, šifrované Integration Runtime Self-Hosted, aby bylo zajištěno, že přihlašovací údaje zůstanou v prostředí privátní sítě definované zákazníkem. Některá citlivá pole by mohla chybět, když na ni odkazuje vlastní kód aplikace tímto způsobem. V případě potřeby použijte v extendedProperties místo použití odkazu na propojenou službu SecureString.

## <a name="pass-outputs-to-another-activity"></a>Předání výstupů jiné aktivitě

Můžete odesílat vlastní hodnoty z kódu v rámci vlastní aktivity zpátky do Azure Data Factory. Můžete to udělat tak, že je zapíšete do `outputs.json` aplikace z aplikace. Data Factory zkopíruje obsah `outputs.json` a připojí ho do výstupu aktivity jako hodnotu `customOutput` Vlastnosti. (Omezení velikosti je 2 MB.) Pokud chcete spotřebovat obsah `outputs.json` v rámci podřízených aktivit, můžete získat hodnotu pomocí výrazu `@activity('<MyCustomActivity>').output.customOutput` .

## <a name="retrieve-securestring-outputs"></a>Načtení výstupů SecureString

Hodnoty citlivých vlastností určené jako typ *SecureString* , jak je znázorněno v některých příkladech v tomto článku, jsou maskovány na kartě monitorování v uživatelském rozhraní Data Factory.  V samotném spuštění kanálu je však vlastnost *SecureString* serializována jako JSON v `activity.json` souboru jako prostý text. Příklad:

```json
"extendedProperties": {
  "connectionString": {
    "type": "SecureString",
    "value": "aSampleSecureString"
  }
}
```

Tato serializace není skutečně zabezpečená a není určena k zabezpečení. Záměrem je pokyn Data Factory k maskování hodnoty na kartě monitorování.

Chcete-li získat přístup k vlastnostem typu *SecureString* z vlastní aktivity, přečtěte si `activity.json` soubor, který je umístěn ve stejné složce jako vaše. EXE, deserializace JSON a pak přístup k vlastnosti JSON (extendedProperties => [propertyName] => Value).

## <a name="compare-v2-custom-activity-and-version-1-custom-dotnet-activity"></a><a name="compare-v2-v1"></a> Porovnání aktivity vlastní aktivity v2 a verze 1 (vlastní)

V Azure Data Factory verze 1 implementujete (vlastní) aktivitu DotNet tím, že vytvoříte projekt knihovny tříd .NET s třídou, která implementuje `Execute` metodu `IDotNetActivity` rozhraní. Propojené služby, datové sady a rozšířené vlastnosti v datové části JSON aktivity (vlastní) DotNet jsou předány metodě spuštění jako objekty silného typu. Podrobnosti o chování verze 1 najdete [v tématu (vlastní) dotnet ve verzi 1](v1/data-factory-use-custom-activities.md). Z důvodu této implementace musí kód aktivity DotNet verze 1 cílit na .NET Framework 4.5.2. Aktivita rozhraní DotNet verze 1 musí být také spuštěna v uzlech Azure Batch se systémem Windows.

V rámci vlastní aktivity Azure Data Factory v2 není nutné implementovat rozhraní .NET. Nyní můžete přímo spouštět příkazy, skripty a vlastní kód, kompilovány jako spustitelný soubor. Chcete-li konfigurovat tuto implementaci, určete `Command` vlastnost spolu s `folderPath` vlastností. Vlastní aktivita nahraje spustitelný soubor a jeho závislosti do `folderpath` a provede příkaz za vás.

Propojené služby, datové sady (definované v referenceObjects) a rozšířené vlastnosti definované v datové části JSON vlastní aktivity služby Data Factory v2 jsou k dispozici ve spustitelném souboru jako soubory JSON. K požadovaným vlastnostem můžete přistupovat pomocí serializátoru JSON, jak je znázorněno v předchozím příkladu kódu SampleApp.exe.

Změny zavedené ve vlastní aktivitě Data Factory v2 vám umožní napsat vlastní logiku kódu v preferovaném jazyce a spustit ji v operačních systémech Windows a Linux podporovaných Azure Batch.

Následující tabulka popisuje rozdíly mezi vlastní aktivitou Data Factory v2 a aktivitou rozhraní Data Factory verze 1 (vlastní).

|Rozdíly      | Vlastní aktivita      | Aktivita verze 1 (vlastní) DotNet      |
| ---- | ---- | ---- |
|Jak je definována vlastní logika      |Poskytnutím spustitelného souboru      |Implementací knihovny DLL .NET      |
|Spouštěcí prostředí vlastní logiky      |Windows nebo Linux      |Windows (.NET Framework 4.5.2)      |
|Spouštění skriptů      |Podporuje spouštěné skripty přímo (například "cmd/c echo Hello World" na virtuálním počítači s Windows).      |Vyžaduje implementaci v knihovně DLL .NET.      |
|Požadovaná datová sada      |Volitelné      |Požadováno pro řetězení aktivit a předávání informací      |
|Předání informací z aktivity do vlastní logiky      |Prostřednictvím ReferenceObjects (LinkedServices a datových sad) a ExtendedProperties (vlastní vlastnosti)      |Prostřednictvím ExtendedProperties (vlastní vlastnosti), vstupních a výstupních datových sad      |
|Načtení informací v vlastní logice      |Analyzuje activity.js, linkedServices.jsna a datasets.jsna uložených ve stejné složce spustitelného souboru.      |Přes .NET SDK (.NET Frame 4.5.2)      |
|protokolování      |Zápisy přímo do STDOUT      |Implementace protokolovacího nástroje v knihovně DLL .NET      |

Pokud máte existující kód .NET napsaný pro aktivitu rozhraní DotNet verze 1 (vlastní), musíte upravit kód, aby fungoval s aktuální verzí vlastní aktivity. Aktualizujte kód podle následujících pokynů vysoké úrovně:

  - Změňte projekt z knihovny tříd .NET na konzolovou aplikaci.
  - Spusťte aplikaci pomocí `Main` metody. `Execute`Metoda `IDotNetActivity` rozhraní již není požadována.
  - Přečtěte si a analyzujte propojené služby, datové sady a aktivity pomocí serializátoru JSON a ne jako objekty silného typu. Předejte hodnoty požadovaných vlastností do vaší hlavní logiky vlastního kódu. Jako příklad se podívejte na předchozí kód SampleApp.exe.
  - Objekt protokolovacího nástroje již není podporován. Výstup z spustitelného souboru může být vytištěn do konzoly a uložen do stdout.txt.
  - Balíček NuGet Microsoft. Azure. Management. DataFactory už není potřeba.
  - Zkompilujte kód, nahrajte spustitelný soubor a jeho závislosti do Azure Storage a definujte cestu ve `folderPath` Vlastnosti.

Kompletní vzorek, jak se komplexní ukázka knihovny DLL a kanálu, která je popsaná v článku Data Factory verze 1, [používá vlastní aktivity v kanálu Azure Data Factory](./v1/data-factory-use-custom-activities.md) může být přepsána jako Data Factory vlastní aktivita, viz [Data Factory ukázka vlastní aktivity](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFv2CustomActivitySample).

## <a name="auto-scaling-of-azure-batch"></a>Automatické škálování Azure Batch

Můžete také vytvořit fond Azure Batch s funkcí **automatického škálování** . Můžete například vytvořit fond služby Azure Batch s 0 vyhrazenými virtuálními počítači a vzorcem automatického škálování na základě počtu nevyřízených úkolů.

Vzorový vzorec v tomto případě dosáhne následujícího chování: při počátečním vytvoření fondu se spustí s 1 virtuálním počítačem. $PendingTasksová metrika definuje počet úloh ve stavu spuštěno a aktivní (ve frontě). Vzorec nalezne průměrný počet nedokončených úloh za posledních 180 sekund a nastaví TargetDedicated odpovídajícím způsobem. Zajišťuje, že TargetDedicated nikdy nepřekračuje 25 virtuálních počítačů. Když se tedy odešlou nové úlohy, fond se automaticky rozrůstá a jakmile se dokončí úkoly, virtuální počítače se uvolní jednou a automatické škálování tyto virtuální počítače zmenší. startingNumberOfVMs a maxNumberofVMs je možné upravit podle svých potřeb.

Vzorec automatického škálování:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Podrobnosti najdete v tématu [Automatické škálování výpočetních uzlů ve fondu Azure Batch](../batch/batch-automatic-scaling.md) .

Pokud fond používá výchozí [autoScaleEvaluationInterval](/rest/api/batchservice/pool/enableautoscale), může služba Batch trvat 15-30 minut, než se virtuální počítač připraví před spuštěním vlastní aktivity. Pokud fond používá jiný autoScaleEvaluationInterval, může služba Batch trvat autoScaleEvaluationInterval + 10 minut.

## <a name="next-steps"></a>Další kroky
Podívejte se na následující články, které vysvětlují, jak transformovat data jinými způsoby:

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita v podregistru](transform-data-using-hadoop-hive.md)
* [Aktivita prasete](transform-data-using-hadoop-pig.md)
* [Aktivita MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Aktivita streamování Hadoop](transform-data-using-hadoop-streaming.md)
* [Aktivita Sparku](transform-data-using-spark.md)
* [Aktivita spuštění dávky Azure Machine Learning Studio (Classic)](transform-data-using-machine-learning.md)
* [Aktivita uložené procedury](transform-data-using-stored-procedure.md)