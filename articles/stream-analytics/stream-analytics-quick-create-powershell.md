---
title: Vytvoření úlohy Stream Analytics pomocí Azure PowerShellu
description: Tento rychlý start podrobně popisuje nasazení a spuštění úlohy Azure Stream Analytics pomocí modulu Azure PowerShell.
services: stream-analytics
author: sidramadoss
ms.author: sidram
ms.date: 05/14/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
manager: kfile
ms.openlocfilehash: 126677df01ad34d488863dd83e2f8c9a2d947824
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49958886"
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-azure-powershell"></a>Rychlý start: Vytvoření úlohy Stream Analytics pomocí Azure PowerShellu

Modul Azure PowerShell slouží k vytváření a správě prostředků Azure pomocí rutin a skriptů PowerShellu. Tento rychlý start podrobně popisuje nasazení a spuštění úlohy Azure Stream Analytics pomocí modulu Azure PowerShell. 
 
Ukázková úloha čte data streamovaná z objektu blob ve službě Azure Blob Storage. Vstupní datový soubor použitý v tomto rychlém startu obsahuje statická data, která jsou určena pouze pro ilustraci. Ve skutečném scénáři použijete pro úlohu Stream Analytics streamovaná vstupní data. Potom úloha data transformuje pomocí dotazovacího jazyka Stream Analytics a vypočte průměrnou teplotu, pokud překročí 100°. Nakonec zapíše výsledné výstupní události do jiného souboru. 

## <a name="before-you-begin"></a>Než začnete

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).  

* Tento rychlý start vyžaduje modul Azure PowerShell verze 3.6 nebo novější. Pomocí příkazu `Get-Module -ListAvailable AzureRM` vyhledejte verzi, která je nainstalovaná na místním počítači. Pokud potřebujete provést instalaci nebo upgrade, přečtěte si článek [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). 


## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Pomocí příkazu `Connect-AzureRmAccount` se přihlaste ke svému předplatnému Azure a do prohlížeče, který se automaticky otevře, zadejte své přihlašovací údaje do Azure:

```powershell
# Log in to your Azure account
Connect-AzureRmAccount
```

Pokud máte několik předplatných, po přihlášení vyberte to, které chcete použít pro tento rychlý start. Výběr proveďte spuštěním následujících rutin. Ujistěte se, že jste <your subscription name> nahradili názvem vašeho předplatného:  

```powershell
# List all available subscriptions.
Get-AzureRmSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzureRmSubscription -SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure pomocí příkazu [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

```powershell
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzureRmResourceGroup `
   -Name $resourceGroup `
   -Location $location 
```

## <a name="prepare-the-input-data"></a>Příprava vstupních dat

Než začnete definovat úlohu Stream Analytics, připravte si data nakonfigurovaná jako vstup pro tuto úlohu.

1. Z GitHubu si stáhněte [vzorová data snímačů](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json). Klikněte pravým tlačítkem myši na odkaz a na **Uložit odkaz jako…** nebo **Uložit cíl jako**.

2. Následující blok kódu prostředí PowerShell provede několik příkazů, které připraví vstupní data vyžadovaná úlohou. Prohlédněte si jednotlivé části a seznamte se s kódem. 

   1. Vytvořte standardní obecný účet úložiště pomocí rutiny [New-AzureRmStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.storage/New-AzureRmStorageAccount).  V tomto příkladu vytvoříte účet úložiště s názvem mystorageaccount s místně redundantním úložištěm (LRS) a šifrováním objektů blob (které bude ve výchozím nastavení povolené).  

   2. Načtěte kontext účtu úložiště `$storageAccount.Context` určující účet úložiště, který chcete použít. Když pracujete s účtem úložiště, namísto opakovaného zadávání přihlašovacích údajů odkazujete na jeho kontext. 

   3. Vytvořte kontejner úložiště pomocí rutiny [New-AzureStorageContainer](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer) a načtěte [vzorová data snímačů](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json), která jste předtím stáhli. 

   4. Zkopírujte klíč úložiště, který je výstupem tohoto kódu, a vložte ho do souborů JSON, které budou později vytvářet vstup a výstupy úlohy streamování.

   ```powershell
   $storageAccountName = "mystorageaccount"
   $storageAccount = New-AzureRmStorageAccount `
     -ResourceGroupName $resourceGroup `
     -Name $storageAccountName `
     -Location $location `
     -SkuName Standard_LRS `
     -Kind Storage
   
   $ctx = $storageAccount.Context
   $containerName = "streamanalytics"
   
   New-AzureStorageContainer `
     -Name $containerName `
     -Context $ctx
   
   Set-AzureStorageBlobContent `
     -File "c:\HelloWorldASA-InputStream.json" `
     -Blob "input/HelloWorldASA-InputStream.json" `
     -Container $containerName `
     -Context $ctx  
   
   $storageAccountKey = (Get-AzureRmStorageAccountKey `
     -ResourceGroupName $resourceGroup `
     -Name $storageAccountName).Value[0]
   
   Write-Host "The <storage account key> placeholder needs to be replaced in your input and output json files with this key value:" 
   Write-Host $storageAccountKey -ForegroundColor Cyan
   ```

## <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics

Pomocí rutiny [New-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsjob?view=azurermps-5.4.0) vytvořte úlohu Stream Analytics. Tato rutina použije název úlohy, název skupiny prostředků a definici úlohy jako parametry. Jako název úlohy můžete zadat jakýkoli popisný název, který identifikuje vaši úlohu. Smí obsahovat jenom alfanumerické znaky, spojovníky a podtržítka a musí být dlouhý 3 až 63 znaků. Definici úlohy představuje soubor JSON, který obsahuje vlastnosti potřebné k vytvoření úlohy. Na místním počítači vytvořte soubor s názvem `JobDefinition.json` a přidejte do něj následující data JSON:

```json
{    
   "location":"WestUS2",  
   "properties":{    
      "sku":{    
         "name":"standard"  
      },  
      "eventsOutOfOrderPolicy":"adjust",  
      "eventsOutOfOrderMaxDelayInSeconds":10,  
      "compatibilityLevel": 1.1
   }
}
```

Potom spusťte rutinu `New-AzureRmStreamAnalyticsJob`. Nezapomeňte nahradit hodnotu proměnné `jobDefinitionFile` cestou, do které jste uložili soubor JSON definice úlohy. 

```powershell
$jobName = "MyStreamingJob"
$jobDefinitionFile = "C:\JobDefinition.json"
New-AzureRmStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -File $jobDefinitionFile `
  -Name $jobName `
  -Force 
```

## <a name="configure-input-to-the-job"></a>Konfigurace vstupu do úlohy

Přidejte do úlohy vstup pomocí rutiny [New-AzureRmStreamAnalyticsInput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsinput?view=azurermps-5.4.0). Tato rutina použije název úlohy, název vstupu úlohy, název skupiny prostředků a definici vstupu úlohy jako parametry. Definici vstupu úlohy představuje soubor JSON, který obsahuje vlastnosti potřebné ke konfiguraci vstupu úlohy. V tomto příkladu vytvoříte jako vstup úložiště objektů blob. 

Na místním počítači vytvořte soubor s názvem `JobInputDefinition.json` a přidejte do něj následující data JSON. Nezapomeňte nahradit hodnotu položky `accountKey` přístupovým klíčem k vašemu účtu úložiště, který je uložený v hodnotě $storageAccountKey. 

```json
{
    "properties": {
        "type": "Stream",
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                {
                   "accountName": "mystorageaccount",
                   "accountKey":"<storage account key>"
                }],
                "container": "streamanalytics",
                "pathPattern": "input/",
                "dateFormat": "yyyy/MM/dd",
                "timeFormat": "HH"
            }
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8"
            }
        }
    },
    "name": "MyBlobInput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/inputs"
}
```

Potom spusťte rutinu `New-AzureRmStreamAnalyticsInput` a nezapomeňte při tom nahradit hodnotu proměnné `jobDefinitionFile` cestou, do které jste uložili soubor JSON definice vstupu úlohy. 

```powershell
$jobInputName = "MyBlobInput"
$jobInputDefinitionFile = "C:\JobInputDefinition.json"
New-AzureRmStreamAnalyticsInput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobInputDefinitionFile `
  -Name $jobInputName 
```

## <a name="configure-output-to-the-job"></a>Konfigurace výstupu do úlohy

Přidejte do úlohy výstup pomocí rutiny [New-AzureRmStreamAnalyticsOutput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsoutput?view=azurermps-5.4.0). Tato rutina použije název úlohy, název výstupu úlohy, název skupiny prostředků a definici výstupu úlohy jako parametry. Definici výstupu úlohy představuje soubor JSON, který obsahuje vlastnosti potřebné ke konfiguraci výstupu úlohy. V tomto příkladu je výstupem úložiště objektů blob. 

Na místním počítači vytvořte soubor s názvem `JobOutputDefinition.json` a přidejte do něj následující data JSON. Nezapomeňte nahradit hodnotu položky `accountKey` přístupovým klíčem k vašemu účtu úložiště, který je uložený v hodnotě $storageAccountKey. 

```json
{
    "properties": {
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                    {
                      "accountName": "mystorageaccount",
                      "accountKey": "<storage account key>"
                    }],
                "container": "streamanalytics",
                "pathPattern": "output/",
                "dateFormat": "yyyy/MM/dd",
                "timeFormat": "HH"
            }
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8",
                "format": "LineSeparated"
            }
        }
    },
    "name": "MyBlobOutput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/outputs"
}
```

Potom spusťte rutinu `New-AzureRmStreamAnalyticsOutput`. Nezapomeňte nahradit hodnotu proměnné `jobOutputDefinitionFile` cestou, do které jste uložili soubor JSON definice výstupu úlohy. 

```powershell
$jobOutputName = "MyBlobOutput"
$jobOutputDefinitionFile = "C:\JobOutputDefinition.json"
New-AzureRmStreamAnalyticsOutput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobOutputDefinitionFile `
  -Name $jobOutputName -Force 
```

## <a name="define-the-transformation-query"></a>Definice transformačního dotazu

Přidejte do úlohy transformaci pomocí rutiny [New-AzureRmStreamAnalyticsTransformation](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticstransformation?view=azurermps-5.4.0). Tato rutina použije název úlohy, název transformace úlohy, název skupiny prostředků a definici transformace úlohy jako parametry. Na místním počítači vytvořte soubor s názvem `JobTransformationDefinition.json` a přidejte do něj následující data JSON. Soubor JSON obsahuje parametr dotazu, který definuje transformaci dotazu:

```json
{     
   "name":"MyTransformation",  
   "type":"Microsoft.StreamAnalytics/streamingjobs/transformations",  
   "properties":{    
      "streamingUnits":1,  
      "script":null,  
      "query":" SELECT System.Timestamp AS OutputTime, dspl AS SensorName, Avg(temp) AS AvgTemperature INTO MyBlobOutput FROM MyBlobInput TIMESTAMP BY time GROUP BY TumblingWindow(second,30),dspl HAVING Avg(temp)>100"  
   }  
}
```

Potom spusťte rutinu `New-AzureRmStreamAnalyticsTransformation`. Nezapomeňte nahradit hodnotu proměnné `jobTransformationDefinitionFile` cestou, do které jste uložili soubor JSON definice transformace úlohy. 

```powershell
$jobTransformationName = "MyJobTransformation"
$jobTransformationDefinitionFile = "C:\JobTransformationDefinition.json"
New-AzureRmStreamAnalyticsTransformation `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobTransformationDefinitionFile `
  -Name $jobTransformationName -Force
```

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Spuštění úlohy Stream Analytics a kontrola výstupu

Spusťte úlohu pomocí rutiny [Start-AzureRMStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0). Tato rutina použije název úlohy, název skupiny prostředků, režim spuštění výstupu a čas spuštění jako parametry. Parametr `OutputStartMode` připouští hodnoty `JobStartTime`, `CustomTime` nebo `LastOutputEventTime`. Další informace o tom, na co tyto hodnoty odkazují, najdete v části [Parametry](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0) v dokumentaci k prostředí PowerShell. V tomto příkladu určete režim `CustomTime` a zadejte hodnotu `OutputStartTime`. 

Jako časovou hodnotu vyberte `2018-01-01`. Toto datum je zvoleno, protože předchází časovému razítku události z ukázkových dat. Jakmile spustíte následující rutinu, vrátí jako výstup hodnotu `True`, pokud se úloha spustí. V kontejneru úložiště se vytvoří výstupní složku s transformovanými daty. 

```powershell
Start-AzureRmStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode CustomTime `
  -OutputStartTime 2018-01-01T00:00:00Z 
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte skupinu prostředků, úlohu streamování a všechny související prostředky, pokud je už nepotřebujete. Odstraněním úlohy se zabrání zaúčtování jednotek streamování, které daná úloha spotřebovává. Pokud máte v plánu tuto úlohu ještě někdy používat, nemusíte ji odstraňovat a prozatím ji jenom zastavte. Pokud nebudete tuto úlohu nadále používat, odstraňte všechny prostředky vytvořené podle tohoto rychlého startu spuštěním následující rutiny:

```powershell
Remove-AzureRmResourceGroup `
  -Name $resourceGroup 
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste pomocí PowerShellu nasadili jednoduchou úlohu Stream Analytics. Úlohy Stream Analytics můžete nasadit také pomocí webu [Azure Portal](stream-analytics-quick-create-portal.md) a sady [Visual Studio](stream-analytics-quick-create-vs.md).

Pokud se chcete dozvědět o konfiguraci dalších vstupních zdrojů a provádění detekce v reálném čase, pokračujte na další článek:

> [!div class="nextstepaction"]
> [Zjišťování možných podvodů v reálném čase pomocí Stream Analytics](stream-analytics-real-time-fraud-detection.md)
