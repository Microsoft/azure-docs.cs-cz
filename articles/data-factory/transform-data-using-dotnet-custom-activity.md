---
title: Použití vlastních aktivit v kanálu
description: Zjistěte, jak vytvořit vlastní aktivity a používat je v kanálu Azure Data Factory.
services: data-factory
ms.service: data-factory
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/26/2018
ms.openlocfilehash: 4913152125b0fafd74db575f835d53fa992b075e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260576"
---
# <a name="use-custom-activities-in-an-azure-data-factory-pipeline"></a>Použití vlastních aktivit v kanálu Azure Data Factory

> [!div class="op_single_selector" title1="Vyberte verzi služby Data Factory, kterou používáte:"]
> * [Verze 1](v1/data-factory-use-custom-activities.md)
> * [Aktuální verze](transform-data-using-dotnet-custom-activity.md)

Existují dva typy aktivit, které můžete použít v kanálu Azure Data Factory.

- [Aktivity přesunu dat](copy-activity-overview.md) pro přesun dat mezi [podporovaným zdrojovým a jímačovými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
- [Aktivity transformace dat](transform-data.md) k transformaci dat pomocí výpočetních služeb, jako je Azure HDInsight, Azure Batch a Azure Machine Learning.

Chcete-li přesunout data do nebo z úložiště dat, které data factory nepodporuje, nebo transformovat/zpracovat data způsobem, který není podporován Data Factory, můžete vytvořit **vlastní aktivitu** s vlastní pohyb dat nebo transformace logiky a použít aktivitu v kanálu. Vlastní aktivita spustí vlastní logiku kódu ve fondu virtuálních počítačů **Azure Batch.**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pokud se službou Azure Batch tenete s azure batch, přečtěte si následující články:

* [Základy Azure Batch](../batch/batch-technical-overview.md) pro přehled služby Azure Batch.
* Nová rutina [AzBatchAccount](/powershell/module/az.batch/New-azBatchAccount) k vytvoření portálu Azure Batch (nebo) [portálu Azure](../batch/batch-account-create-portal.md) k vytvoření účtu Azure Batch pomocí portálu Azure Portal. Podrobný článek o použití [PowerShellu ke správě dávkového účtu Azure,](https://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx) kde najdete podrobné pokyny k používání rutiny.
* [Rutina New-AzBatchPool](/powershell/module/az.batch/New-AzBatchPool) pro vytvoření fondu Azure Batch.

## <a name="azure-batch-linked-service"></a>Propojená služba Azure Batch

Následující JSON definuje ukázkovou propojenou službu Azure Batch. Podrobnosti najdete [v tématu Výpočetní prostředí podporovaná Azure Data Factory.](compute-linked-services.md)

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

 Další informace o propojené službě Azure Batch najdete v článku [Výpočetní služby propojené.](compute-linked-services.md)

## <a name="custom-activity"></a>Vlastní aktivita

Následující úryvek JSON definuje kanál s jednoduchou vlastní aktivitou. Definice aktivity obsahuje odkaz na propojenou službu Azure Batch.

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

V této ukázce helloworld.exe je vlastní aplikace uložená ve složce customactv2/helloworld účtu Azure Storage používaného v resourceLinkedService. Vlastní aktivita odešle tuto vlastní aplikaci, která má být spuštěna v Azure Batch. Příkaz můžete nahradit libovolnou upřednostňovanou aplikací, která může být spuštěna v cílovém operačním systému uzlů fondu dávek Azure.

Následující tabulka popisuje názvy a popisy vlastností, které jsou specifické pro tuto aktivitu.

| Vlastnost              | Popis                              | Požaduje se |
| :-------------------- | :--------------------------------------- | :------- |
| jméno                  | Název aktivity v kanálu     | Ano      |
| description           | Text popisující, co aktivita dělá.  | Ne       |
| type                  | Pro vlastní aktivitu je typ **aktivity Vlastní**. | Ano      |
| linkedServiceName     | Propojená služba s Azure Batch. Další informace o této propojené službě najdete v článku [Výpočetní propojené služby.](compute-linked-services.md)  | Ano      |
| command               | Příkaz vlastní aplikace, která má být spuštěna. Pokud je aplikace již k dispozici v uzlu fondu dávek Azure, lze přeskočí resourceLinkedService a folderPath. Můžete například zadat příkaz `cmd /c dir`, který je nativně podporován uzětem fondu dávek systému Windows. | Ano      |
| resourceLinkedService | Azure Storage Propojená služba s účtem úložiště, kde je uložená vlastní aplikace | Žádná &#42;       |
| folderPath            | Cesta ke složce vlastní aplikace a všechny její závislosti<br/><br/>Pokud máte závislosti uložené v podsložkách – to znamená v hierarchické struktuře složek pod *folderPath* - struktura složek je aktuálně sloučí při kopírování souborů do Azure Batch. To znamená, že všechny soubory jsou zkopírovány do jedné složky bez podsložek. Chcete-li toto chování obejít, zvažte kompresi souborů, zkopírování komprimovaného souboru a jeho rozepnutí pomocí vlastního kódu v požadovaném umístění. | Žádná &#42;       |
| referenceObjekty      | Pole existujících propojených služeb a datových sad. Odkazované propojené služby a datové sady jsou předány vlastní aplikaci ve formátu JSON, takže váš vlastní kód může odkazovat na prostředky datové továrny | Ne       |
| extendedProperties    | Uživatelem definované vlastnosti, které lze předat vlastní aplikaci ve formátu JSON, takže váš vlastní kód může odkazovat na další vlastnosti | Ne       |
| retentionTimeInDays | Doba uchování souborů odeslaných pro vlastní aktivitu. Výchozí hodnota je 30 dní. | Ne |

&#42; `resourceLinkedService` Vlastnosti `folderPath` a musí být zadány nebo obě vynechány.

> [!NOTE]
> Pokud předáváte propojené služby jako referenceObjekty ve vlastní aktivitě, je vhodné předat propojenou službu azure key vault (protože neobsahuje žádné zabezpečené řetězce) a načíst pověření pomocí tajného názvu přímo z klíče Trezor z kódu. Zde najdete [příklad,](https://github.com/nabhishek/customactivity_sample/tree/linkedservice) který odkazuje na propojenou službu povolenou službou AKV, načte pověření z trezoru klíčů a pak přistupuje k úložišti v kódu.

## <a name="custom-activity-permissions"></a>Vlastní oprávnění aktivity

Vlastní aktivita nastaví účet automatického uživatele Azure Batch na *přístup bez oprávnění správce s rozsahem úloh* (výchozí specifikace automatického uživatele). Úroveň oprávnění účtu automatického uživatele nelze změnit. Další informace najdete v tématu [Spuštění úloh v části Uživatelské účty v části Dávka | Účty automatického uživatele](../batch/batch-user-accounts.md#auto-user-accounts).

## <a name="executing-commands"></a>Provádění příkazů

Příkaz můžete provést přímo pomocí vlastní aktivity. Následující příklad spustí příkaz "echo hello world" na cílových uzlech fondu dávek Azure a vytiskne výstup na stdout.

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

Tato ukázka ukazuje, jak můžete použít referenceObjects a extendedProperties k předání objektů Data Factory a uživatelem definovaných vlastností vlastní aplikaci.

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

Při spuštění aktivity referenceObjects a extendedProperties jsou uloženy v následujících souborech, které jsou nasazeny do stejné složky spuštění SampleApp.exe:

- `activity.json`

  Ukládá extendedProperties a vlastnosti vlastní aktivity.

- `linkedServices.json`

  Ukládá pole propojených služeb definované ve vlastnosti referenceObjects.

- `datasets.json`

  Ukládá pole datových sad definovaných ve vlastnosti referenceObjects.

Následující ukázkový kód ukazují, jak SampleApp.exe přístup k požadované informace ze souborů JSON:

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

Spuštění kanálu můžete spustit pomocí následujícího příkazu Prostředí PowerShell:

```powershell
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName $pipelineName
```

Když je kanál spuštěn, můžete zkontrolovat výstup spuštění pomocí následujících příkazů:

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

**Stdout** a **stderr** vaší vlastní aplikace se uloží do **kontejneru adfjobs** v propojené službě Azure Storage, kterou jste definovali při vytváření služby Azure Batch Linked Service s identifikátorem GUID úlohy. Podrobnou cestu můžete získat z výstupu Spustit aktivitu, jak je znázorněno v následujícím fragmentu:

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

Pokud chcete spotřebovávat obsah souboru stdout.txt v následných aktivitách, můžete získat cestu\@k souboru stdout.txt ve výrazu " activity('MyCustomActivity').output.outputs[0]".

> [!IMPORTANT]
> - Activity.json, linkedServices.json a datasets.json jsou uloženy ve složce runtime úlohy Batch. V tomto příkladu jsou v `"https://adfv2storage.blob.core.windows.net/adfjobs/\<GUID>/runtime/"` cestě uloženy soubory activity.json, linkedServices.json a datasets.json. V případě potřeby je musíte vyčistit samostatně.
> - U propojených služeb, které používají prostředí Runtime integrace s vlastním hostitelem, jsou citlivé informace, jako jsou klíče nebo hesla, šifrovány prostředím Self-Hosted Integration Runtime, aby bylo zajištěno, že přihlašovací údaje zůstanou v prostředí privátní sítě definované zákazníkem. Některá citlivá pole mohou chybět, pokud na ně odkazuje vlastní kód aplikace tímto způsobem. V případě potřeby použijte securestring v extendedProperties místo použití odkazu propojené služby.

## <a name="pass-outputs-to-another-activity"></a>Předání výstupů jiné aktivitě

Vlastní hodnoty z kódu ve vlastní aktivitě můžete odeslat zpět do Azure Data Factory. Můžete tak učinit zápisem `outputs.json` do z vaší aplikace. Data Factory zkopíruje `outputs.json` obsah a připojí jej do výstupu `customOutput` aktivity jako hodnotu vlastnosti. (Limit velikosti je 2 MB.) Pokud chcete spotřebovat obsah `outputs.json` v podřízené aktivity, můžete získat `@activity('<MyCustomActivity>').output.customOutput`hodnotu pomocí výrazu .

## <a name="retrieve-securestring-outputs"></a>Načíst výstupy SecureString

Hodnoty citlivých vlastností označené jako typ *SecureString*, jak je znázorněno v některých příkladech v tomto článku, jsou maskovány na kartě Monitorování v uživatelském rozhraní data factory.  Při skutečném spuštění kanálu je však vlastnost *SecureString* serializována jako JSON v souboru `activity.json` jako prostý text. Například:

```json
"extendedProperties": {
  "connectionString": {
    "type": "SecureString",
    "value": "aSampleSecureString"
  }
}
```

Tato serializace není skutečně bezpečná a není určena k zabezpečení. Záměrem je nápověda data factory maskovat hodnotu na kartě monitorování.

Chcete-li získat přístup k vlastnostem typu `activity.json` *SecureString* z vlastní aktivity, přečtěte si soubor, který je umístěn ve stejné složce jako váš . EXE, rekonstruovat JSON a potom přístup K Vlastnosti JSON (extendedProperties => [propertyName] => hodnota).

## <a name="compare-v2-custom-activity-and-version-1-custom-dotnet-activity"></a><a name="compare-v2-v1"></a>Porovnat vlastní aktivitu v2 a aktivitu verze 1 (vlastní) DotNet

V Azure Data Factory verze 1 implementovat (vlastní) DotNet aktivity vytvořením .NET `Execute` class library `IDotNetActivity` projektu s třídou, která implementuje metodu rozhraní. Propojené služby, datové sady a rozšířené vlastnosti v datové části JSON (vlastní) DotNet activity jsou předány metodě provádění jako objekty silného typu. Podrobnosti o chování verze 1 naleznete [v tématu (Vlastní) DotNet ve verzi 1](v1/data-factory-use-custom-activities.md). Z důvodu této implementace musí váš kód aktivity DotNet verze 1 cílit na rozhraní .NET Framework 4.5.2. Aktivita DotNet verze 1 musí být také spuštěna na uzlech fondu Azure založených na systému Windows.

Ve vlastní aktivitě Azure Data Factory V2 není nutné implementovat rozhraní .NET. Nyní můžete přímo spouštět příkazy, skripty a vlastní kód, který je kompilován jako spustitelný soubor. Chcete-li nakonfigurovat tuto `Command` implementaci, `folderPath` zadejte vlastnost společně s vlastností. Vlastní aktivita nahraje spustitelný soubor `folderpath` a jeho závislosti a provede příkaz za vás.

Propojené služby, datové sady (definované v referenčních objektech) a rozšířené vlastnosti definované v datové části JSON vlastní aktivity datové továrny v2 lze přistupovat pomocí spustitelného souboru jako soubory JSON. Můžete přistupovat k požadované vlastnosti pomocí serializátoru JSON, jak je znázorněno v předchozím SampleApp.exe kód vzorku.

Se změnami zavedenými ve vlastní aktivitě Data Factory V2 můžete napsat vlastní logiku kódu v upřednostňovaném jazyce a spustit ji v operačních systémech Windows a Linux podporovaných službou Azure Batch.

Následující tabulka popisuje rozdíly mezi vlastní aktivitou Data Factory V2 a aktivitou DotNet ve výrobě dat 1 (vlastní):

|Rozdíly      | Vlastní aktivita      | verze 1 (Vlastní) Aktivita DotNet      |
| ---- | ---- | ---- |
|Jak je definována vlastní logika      |Poskytnutím spustitelného      |Implementací dll .NET      |
|Spuštění prostředí vlastní logiky      |Windows nebo Linux      |Systém Windows (rozhraní.NET Framework 4.5.2)      |
|Provádění skriptů      |Podporuje provádění skriptů přímo (například "cmd /c echo hello world" na Virtuálním počítači se systémem Windows)      |Vyžaduje implementaci v dll .NET.      |
|Je vyžadována datová sada.      |Nepovinné      |Vyžadováno pro řetězové činnosti a předání informací      |
|Předání informací z aktivity do vlastní logiky      |Prostřednictvím ReferenceObjects (LinkedServices a datové sady) a ExtendedProperties (vlastní vlastnosti)      |Prostřednictvím ExtendedProperties (vlastní vlastnosti), vstupní a výstupní datové sady      |
|Načtení informací ve vlastní logice      |Analyzuje soubor activity.json, soubor linkedServices.json a datasets.json uložené ve stejné složce spustitelného souboru.      |Prostřednictvím sady .NET SDK (.NET Frame 4.5.2)      |
|protokolování      |Zápisy přímo do STDOUT      |Implementace protokolovacího nástroje v dll .NET      |

Pokud máte existující kód .NET napsaný pro aktivitu DotNet verze 1 (Vlastní), je třeba upravit kód, aby fungoval s aktuální verzí vlastní aktivity. Aktualizujte kód podle následujících pokynů na vysoké úrovni:

  - Změňte projekt z knihovny tříd .NET na konzolovou aplikaci.
  - Spusťte aplikaci `Main` pomocí metody. Metoda `Execute` `IDotNetActivity` rozhraní již není vyžadována.
  - Čtení a analýza propojených služeb, datových sad a aktivit pomocí serializátoru JSON a nikoli tak silně zadaných objektů. Předejte hodnoty požadovaných vlastností hlavní logice vlastního kódu. Jako příklad se podívejte na předchozí kód SampleApp.exe.
  - Objekt Logger již není podporován. Výstup z spustitelného souboru lze vytisknout do konzoly a uloží se do souboru stdout.txt.
  - Balíček Microsoft.Azure.Management.DataFactories NuGet už není potřeba.
  - Zkompilujte kód, nahrajte spustitelný soubor a jeho závislosti `folderPath` do Služby Azure Storage a definujte cestu ve vlastnosti.

Úplný ukázku toho, jak lze přepsání komplexní dll a ukázky kanálu popsané v článku Data Factory verze 1 [Použít vlastní aktivity v kanálu Azure Data Factory](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities) jako vlastní aktivitu datové továrny, naleznete v části Vlastní [aktivita datové továrny](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ADFv2CustomActivitySample).

## <a name="auto-scaling-of-azure-batch"></a>Automatické škálování Azure Batch

Můžete také vytvořit fond Azure Batch s funkcí **automatického škálování.** Můžete například vytvořit fond dávek azure s 0 vyhrazenými virtuálními počítači a vzorcem automatického škálování na základě počtu čekajících úloh.

Ukázkový vzorec zde dosahuje následující chování: Při fondu je původně vytvořen, začíná s 1 virtuální mk. $PendingTasks metrika definuje počet úloh ve spuštěném + aktivním (zařazeném) stavu. Vzorec vyhledá průměrný počet čekajících úkolů v posledních 180 sekundách a podle toho nastaví TargetDedicated. Zajišťuje, že TargetDedicated nikdy přesahuje 25 virtuálních ms. Takže jako nové úkoly jsou odesílány, fond automaticky roste a jako úkoly dokončit, virtuální chod se jeden po druhém zdarma a automatické škálování zmenšuje tyto virtuální chod. startingNumberOfVMs a maxNumberofVMs lze přizpůsobit vašim potřebám.

Vzorec automatického škálování:

```
startingNumberOfVMs = 1;
maxNumberofVMs = 25;
pendingTaskSamplePercent = $PendingTasks.GetSamplePercent(180 * TimeInterval_Second);
pendingTaskSamples = pendingTaskSamplePercent < 70 ? startingNumberOfVMs : avg($PendingTasks.GetSample(180 * TimeInterval_Second));
$TargetDedicated=min(maxNumberofVMs,pendingTaskSamples);
```

Podrobnosti najdete [v tématu Automatické škálování výpočetních uzlů ve fondu Azure Batch.](../batch/batch-automatic-scaling.md)

Pokud fond používá výchozí [autoScaleEvaluationInterval](https://msdn.microsoft.com/library/azure/dn820173.aspx), služba Batch může před spuštěním vlastní aktivity trvat 15 až 30 minut, než se virtuální počítač připraví. Pokud fond používá jiný autoScaleEvaluationInterval, služba Batch může trvat autoScaleEvaluationInterval + 10 minut.

## <a name="next-steps"></a>Další kroky
Další články vysvětlují, jak transformovat data jinými způsoby:

* [Aktivita U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktivita úlu](transform-data-using-hadoop-hive.md)
* [Aktivita prasat](transform-data-using-hadoop-pig.md)
* [Aktivita MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Aktivita streamování hadoopu](transform-data-using-hadoop-streaming.md)
* [Aktivita jiskry](transform-data-using-spark.md)
* [Aktivita dávkového spuštění strojového učení](transform-data-using-machine-learning.md)
* [Aktivita uložené procedury](transform-data-using-stored-procedure.md)
