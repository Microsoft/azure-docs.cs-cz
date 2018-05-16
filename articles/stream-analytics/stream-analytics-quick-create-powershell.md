---
title: Vytvoření úlohy Stream Analytics pomocí Azure PowerShellu
description: Tento rychlý start podrobně popisuje nasazení a spuštění úlohy Azure Stream Analytics pomocí modulu Azure PowerShell.
services: stream-analytics
author: SnehaGunda
ms.author: sngun
ms.date: 03/16/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
manager: kfile
ms.openlocfilehash: 0be8cee9e6c7874282f4e8f43f75fa7f2490c14e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-azure-powershell"></a>Rychlý start: Vytvoření úlohy Stream Analytics pomocí Azure PowerShellu

Modul Azure PowerShell slouží k vytváření a správě prostředků Azure pomocí rutin a skriptů PowerShellu. Tento rychlý start podrobně popisuje nasazení a spuštění úlohy Azure Stream Analytics pomocí modulu Azure PowerShell.

## <a name="before-you-begin"></a>Než začnete

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).  

* Tento rychlý start vyžaduje modul Azure PowerShell verze 3.6 nebo novější. Pomocí příkazu `Get-Module -ListAvailable AzureRM` vyhledejte verzi, která je nainstalovaná na místním počítači. Pokud potřebujete provést instalaci nebo upgrade, přečtěte si článek [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Scénář v tomto článku popisuje, jak přečíst data z úložiště objektů blob, transformovat je a zapsat je zpět do jiného kontejneru ve stejném úložišti.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Pomocí příkazu `Connect-AzureRmAccount` se přihlaste ke svému předplatnému Azure a do prohlížeče, který se automaticky otevře, zadejte své přihlašovací údaje do Azure. Pokud máte několik předplatných, po přihlášení vyberte to, které chcete použít pro tento rychlý start. Výběr proveďte spuštěním následujících rutin. Ujistěte se, že jste <your subscription> nahradili názvem vašeho předplatného:  

```powershell
# Log in to your Azure account
Connect-AzureRmAccount

# List all available subscriptions.
Get-AzureRmSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzureRmSubscription -SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure pomocí příkazu [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

```powershell
$resourceGroup = "ASAPSRG"
$location = "WestUS2"
New-AzureRMResourceGroup `
  -Name $resourceGroup `
  -Location $location 
```

## <a name="prepare-the-input-data"></a>Příprava vstupních dat

Než začnete definovat úlohu Stream Analytics, připravte si data nakonfigurovaná jako vstup pro tuto úlohu. Proveďte následující kroky, pomocí kterých si připravíte vstupní data vyžadovaná úlohou. 

1. Z GitHubu si stáhněte [vzorová data snímačů](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json).  

2. Vytvoření standardního obecného účtu úložiště s replikací LRS pomocí rutiny [New-AzureRmStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.storage/New-AzureRmStorageAccount)  

3. Načte kontext účtu úložiště, který definuje účet úložiště, který chcete použít. Když pracujete s účtem úložiště, namísto opakovaného zadávání přihlašovacích údajů odkazujete na jeho kontext. V tomto příkladu vytvoříte účet úložiště s názvem mystorageaccount s místně redundantním úložištěm (LRS) a šifrováním objektů blob (které bude ve výchozím nastavení povolené).  

4. Následně vytvořte pomocí rutiny [New-AzureStorageContainer](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer) kontejner, nastavte oprávnění jako „blob“, abyste umožnili veřejný přístup k souborům, a nahrajte [vzorová data snímačů](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json), které jste předtím stáhli. 

Tyto kroky provedete spuštěním následujícího skriptu PowerShellu:

```powershell
$storageAccountName = "mystorageaccount"
$storageAccount = New-AzureRmStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
$containerName = "myinputcontainer"

New-AzureStorageContainer `
  -Name $containerName `
  -Context $ctx `
  -Permission blob

Set-AzureStorageBlobContent `
  -File "C:\HelloWorldASA-InputStream.json" `
  -Container $containerName `
  -Context $ctx  

$storageAccountKey = (Get-AzureRmStorageAccountKey `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName).Value[0]
```

## <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics

Vytvořte úlohu Stream Analytics pomocí rutiny [New-AzureRMStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsjob?view=azurermps-5.4.0). Tato rutina použije název úlohy, název skupiny prostředků a definici úlohy jako parametry. Úloha může mít libovolný příhodný název, podle kterého ji rozpoznáte. Název úlohy Stream Analytics může obsahovat jen alfanumerické znaky, spojovníky a podtržítka musí být dlouhý 3 až 63 znaků. Definici úlohy představuje soubor JSON, který obsahuje vlastnosti potřebné k vytvoření úlohy. Na místním počítače vytvořte soubor s názvem „JobDefinition.json“ a přidejte do něj následující data JSON:

```json
{    
   "location":"Central US",  
   "properties":{    
      "sku":{    
         "name":"standard"  
      },  
      "eventsOutOfOrderPolicy":"drop",  
      "eventsOutOfOrderMaxDelayInSeconds":10,  
      "compatibilityLevel": 1.1
}
}
```

Následně spusťte rutinu New-AzureRMStreamAnalyticsJob, hodnotu proměnné jobDefinitionFile nahraďte cestou do umístění, kam jste uložili soubor JSON s definicí úlohy. 

```powershell
$jobName = "MyStreamingJob"
$jobDefinitionFile = "C:\JobDefinition.json"
New-AzureRMStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  –File $jobDefinitionFile `
  –Name $jobName -Force 
```

## <a name="configure-input-to-the-job"></a>Konfigurace vstupu do úlohy

Přidejte do úlohy vstup pomocí rutiny [New-AzureRMStreamAnalyticsInput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsinput?view=azurermps-5.4.0). Tato rutina použije název úlohy, název vstupu úlohy, název skupiny prostředků a definici vstupu úlohy jako parametry. Definice vstupu úlohy představuje soubor JSON, který obsahuje vlastnosti požadované pro konfiguraci vstupu úlohy. V tomto případě vytvoříte jako vstup úložiště objektů blob. Na místním počítači vytvořte soubor s názvem „JobInputDefinition.json“ a přidejte do něj následující data JSON. Hodnotu **accountKey** nahraďte přístupovým klíčem vašeho účtu úložiště (tj. hodnotou uloženou v části $storageAccountKey). 

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
                   "accountKey":"<Your storage account key>"
                }],
                "container": "myinputcontainer",
                "pathPattern": "",
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

Následně spusťte rutinu New-AzureRMStreamAnalyticsInput, hodnotu proměnné jobDefinitionFile nahraďte cestou do umístění, kam jste uložili soubor JSON s definicí vstupu úlohy. 

```powershell
$jobInputName = "MyBlobInput"
$jobInputDefinitionFile = "C:\JobInputDefinition.json"
New-AzureRMStreamAnalyticsInput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobInputDefinitionFile `
  -Name $jobInputName 
```

## <a name="configure-output-to-the-job"></a>Konfigurace výstupu do úlohy

Přidejte do úlohy výstup pomocí rutiny [New-AzureRmStreamAnalyticsOutput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsoutput?view=azurermps-5.4.0). Tato rutina použije název úlohy, název výstupu úlohy, název skupiny prostředků a definici výstupu úlohy jako parametry. Definice výstupu úlohy představuje soubor JSON, který obsahuje vlastnosti požadované pro konfiguraci výstupu úlohy. V tomto případě vytvoříte jako výstup úložiště objektů blob. Na místním počítači vytvořte soubor s názvem „JobOutputDefinition.json“ a přidejte do něj následující data JSON. Hodnotu **accountKey** nahraďte přístupovým klíčem vašeho účtu úložiště (tj. hodnotou uloženou v části $storageAccountKey). 

```json
{
    "properties": {
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                    {
                      "accountName": "mystorageaccount",
                      "accountKey": "<Your storage account key>"
                    }],
                "container": "myoutputcontainer",
                "pathPattern": "",
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

Následně spusťte rutinu New-AzureRMStreamAnalyticsOutput, hodnotu proměnné jobDefinitionFile nahraďte cestou do umístění, kam jste uložili soubor JSON s definicí výstupu úlohy. 

```powershell
$jobOutputName = "MyBlobOutput"
$jobOutputDefinitionFile = "C:\JobOutputDefinition.json"
New-AzureRMStreamAnalyticsOutput `
  -ResourceGroupName $resourceGroup `
  –JobName $jobName `
  –File $jobOutputDefinitionFile `
  –Name $jobOutputName -Force 
```

## <a name="define-the-transformation-query"></a>Definice transformačního dotazu

Přidejte do úlohy transformaci pomocí rutiny [New-AzureRmStreamAnalyticsTransformation](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticstransformation?view=azurermps-5.4.0). Tato rutina použije název úlohy, název transformace úlohy, název skupiny prostředků a definici transformace úlohy jako parametry. Na místním počítače vytvořte soubor s názvem „JobTransformationDefinition.json“ a přidejte do něj následující data JSON. Soubor JSON obsahuje parametr dotazu, který definuje transformaci dotazu:

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

Následně spusťte rutinu New-AzureRMStreamAnalyticsTransformation, hodnotu proměnné jobTransformationDefinitionFile nahraďte cestou do umístění, kam jste uložili soubor JSON s definicí transformace úlohy. 

```powershell
$jobTransformationName = "MyJobTransformation"
$jobTransformationDefinitionFile = "C:\JobTransformationDefinition.json"
New-AzureRMStreamAnalyticsTransformation `
  -ResourceGroupName $resourceGroup `
  –JobName $jobName `
  –File $jobTransformationDefinitionFile `
  –Name $jobTransformationName -Force
```

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Spuštění úlohy Stream Analytics a kontrola výstupu

Spusťte úlohu pomocí rutiny [Start-AzureRMStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0). Tato rutina použije název úlohy, název skupiny prostředků, režim spuštění výstupu a čas spuštění jako parametry. Parametr OUtpputStartMode může obsahovat dvě z hodnot JobStartTime, CustomTime nebo LastOutputEventTime. Informace o tom, na co jednotlivé hodnoty odkazují, najdete v části [Parametry](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0) v dokumentaci k PowerShellu. V tomto příkladu můžete zadat režim jako parametr CustomTime a zadat hodnotu pro parametr OutputStartTime. 

Jako časovou hodnotu zvolte jeden den před datem nahrání souboru do úložiště objektů blob, protože čas nahrání předchází aktuálnímu času. Jakmile spustíte následující rutinu, vrátí jako výstup hodnotu „True“, pokud se úloha spustí. V účtu úložiště s transformovanými daty se vytvoří kontejner pojmenovaný „myoutputcontainer“. 

```powershell
Start-AzureRMStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode CustomTime `
  -OutputStartTime 2018-03-11T14:45:12Z 
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte skupinu prostředků, úlohu streamování a všechny související prostředky, pokud je už nepotřebujete. Odstraněním úlohy se zabrání zaúčtování jednotek streamování, které daná úloha spotřebovává. Pokud plánujete používat tuto úlohu v budoucnu, můžete ji zastavit a znovu ji spustit později, až ji budete potřebovat. Pokud nebudete tuto úlohu nadále používat, odstraňte všechny prostředky vytvořené podle tohoto rychlého startu spuštěním následující rutiny:

```powershell
Remove-AzureRmResourceGroup `
  -Name $resourceGroup 
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili jednoduchou úlohu Stream Analytics a dozvěděli se tak o konfiguraci dalších vstupních zdrojů a provádění detekce v reálném čase. Pokračujte na další článek:

> [!div class="nextstepaction"]
> [Zjišťování možných podvodů v reálném čase pomocí Stream Analytics](stream-analytics-real-time-fraud-detection.md)
