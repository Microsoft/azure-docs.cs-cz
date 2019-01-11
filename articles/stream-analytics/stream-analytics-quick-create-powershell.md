---
title: Vytvoření úlohy Stream Analytics pomocí Azure Powershellu
description: Tento rychlý start ukazuje, jak použít modul Azure PowerShell k nasazení a spuštění úlohy Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 12/20/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
ms.openlocfilehash: c3392c3fd169ceed84368ed9e8b8d81cab7baea4
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54212921"
---
# <a name="quickstart-create-a-stream-analytics-job-using-azure-powershell"></a>Rychlý start: Vytvoření úlohy Stream Analytics pomocí Azure Powershellu

Modul Azure PowerShell slouží k vytváření a správě prostředků Azure pomocí rutin a skriptů Powershellu. Tento rychlý start podrobně popisuje nasazení a spuštění úlohy Azure Stream Analytics pomocí modulu Azure PowerShell. 

Úlohy příklad přečte streamovaná data ze služby IoT Hub device. Online simulátor Raspberry Pi vygeneruje vstupní data. Potom úlohu Stream Analytics transformuje data pomocí dotazovací jazyk Stream Analytics k filtrování zpráv s teplotu vyšší než 27. A konečně zapíše výsledný výstupních událostí do souboru v úložišti objektů blob. 

## <a name="before-you-begin"></a>Před zahájením

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).  

* Tento rychlý start vyžaduje modul Azure PowerShell verze 3.6 nebo novější. Pomocí příkazu `Get-Module -ListAvailable AzureRM` vyhledejte verzi, která je nainstalovaná na místním počítači. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).

* Některé služby IoT Hub akce nepodporuje prostředí Azure PowerShell a musí být dokončena, pomocí Azure CLI verze 2.0.24 nebo novější a rozšíření IoT pro Azure CLI. [Instalace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) a použít `az extension add --name azure-cli-iot-ext` k instalaci rozšíření IoT.


## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k předplatnému Azure pomocí `Connect-AzureRmAccount` příkaz a zadejte přihlašovací údaje Azure v místním prohlížeče:

```powershell
# Connect to your Azure account
Connect-AzureRmAccount
```

Pokud máte více předplatných, vyberte předplatné, které chcete použít pro účely tohoto rychlého startu spuštěním následující rutiny. Ujistěte se, že jste `<your subscription name>` nahradili názvem vašeho předplatného:  

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

Následující blok kódu rozhraní příkazového řádku Azure nemá mnoho příkazů připravíte vstupní data vyžadovaná úlohou. Prohlédněte si jednotlivé části a seznamte se s kódem.

1. V prostředí PowerShell, spusťte [az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) příkaz pro přihlášení ke svému účtu Azure. 

   Po úspěšném přihlášení, Azure CLI vrátí seznam vašich předplatných. Zkopírujte předplatného, který používáte pro tento rychlý start a spustit [az účet sady](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#change-the-active-subscription) příkaz k výběru příslušného předplatného. Zvolte stejné předplatné, které jste vybrali v předchozím oddílu pomocí Powershellu. Nezapomeňte nahradit `<your subscription name>` s názvem vašeho předplatného.

   ```azurecli
   az login
   
   az account set --subscription "<your subscription>"
   ```

2. Vytvoření IoT Hubu pomocí [az iot hub vytvořit](../iot-hub/iot-hub-create-using-cli.md#create-an-iot-hub) příkazu. Tento příklad vytvoří službu IoT Hub volá **MyASAIoTHub**. Protože jsou jedinečné názvy služby IoT Hub, musíte přijít s vlastním názvem služby IoT Hub. Nastavte skladovou Položku F1 má použít na úrovni free, pokud je k dispozici ve vašem předplatném. V opačném případě zvolte další nejnižší úroveň.

   ```azurecli
   az iot hub create --name "<your IoT Hub name>" --resource-group $resourceGroup --sku S1
   ```

   Po vytvoření služby IoT hub získat řetězec připojení služby IoT Hub pomocí [az iot hub show-connection-string](https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest) příkazu. Zkopírujte celý připojovací řetězec a uložte jej pro po přidání služby IoT Hub jako vstup do úlohy Stream Analytics.
   
   ```azurecli
   az iot hub show-connection-string --hub-name "MyASAIoTHub"
   ```

3. Přidat zařízení do IoT Hubu pomocí [az IOT hub-identity zařízení vytvořit](../iot-hub/quickstart-send-telemetry-c.md#register-a-device) příkazu. Tento příklad vytvoří zařízení s názvem **MyASAIoTDevice**.

   ```azurecli
   az iot hub device-identity create --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice"
   ```

4. Získat řetězec připojení zařízení pomocí [az iot hub identitu zařízení show-connection-string](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) příkazu. Zkopírujte celý připojovací řetězec a uložte ho při vytváření simulátor Raspberry Pi.

   ```azurecli
   az iot hub device-identity show-connection-string --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice" --output table
   ```

   **Příklad výstupu:**

   ```azurecli
   HostName=MyASAIoTHub.azure-devices.net;DeviceId=MyASAIoTDevice;SharedAccessKey=a2mnUsg52+NIgYudxYYUNXI67r0JmNubmfVafojG8=
   ```

## <a name="create-blob-storage"></a>Vytvořit úložiště objektů blob

Následující blok kódu prostředí Azure PowerShell k vytvoření úložiště objektů blob, který se používá pro výstup úlohy používá příkazy. Prohlédněte si jednotlivé části a seznamte se s kódem.

1. Vytvořte standardní obecný účet úložiště pomocí rutiny [New-AzureRmStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.storage/New-AzureRmStorageAccount).  Tento příklad vytvoří účet úložiště s názvem **myasaquickstartstorage** s místně redundantním úložištěm a šifrováním objektů blob (ve výchozím nastavení povolena.  
   
2. Načtěte kontext účtu úložiště `$storageAccount.Context` určující účet úložiště, který chcete použít. Když pracujete s účtem úložiště, namísto opakovaného zadávání přihlašovacích údajů odkazujete na jeho kontext. 

3. Vytvoření kontejneru úložiště pomocí [New-AzureStorageContainer](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer).

4. Klíč úložiště, která je výstupem kódu zkopírujte a uložte tento klíč k vytvoření výstupu úlohy streamování později.

   ```powershell
   $storageAccountName = "myasaquickstartstorage"
   $storageAccount = New-AzureRmStorageAccount `
     -ResourceGroupName $resourceGroup `
     -Name $storageAccountName `
     -Location $location `
     -SkuName Standard_LRS `
     -Kind Storage
   
   $ctx = $storageAccount.Context
   $containerName = "container1"
   
   New-AzureStorageContainer `
     -Name $containerName `
     -Context $ctx
   
   $storageAccountKey = (Get-AzureRmStorageAccountKey `
     -ResourceGroupName $resourceGroup `
     -Name $storageAccountName).Value[0]
   
   Write-Host "The <storage account key> placeholder needs to be replaced in your output json files with this key value:" 
   Write-Host $storageAccountKey -ForegroundColor Cyan
   ```

## <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics

Pomocí rutiny [New-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsjob?view=azurermps-5.4.0) vytvořte úlohu Stream Analytics. Tato rutina použije název úlohy, název skupiny prostředků a definici úlohy jako parametry. Jako název úlohy můžete zadat jakýkoli popisný název, který identifikuje vaši úlohu. Může obsahovat alfanumerické znaky, spojovníky, a pouze podtržítka a musí být dlouhý 3 až 63 znaků. Definici úlohy představuje soubor JSON, který obsahuje vlastnosti potřebné k vytvoření úlohy. Na místním počítači vytvořte soubor s názvem `JobDefinition.json` a přidejte do něj následující data JSON:

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

Potom spusťte rutinu `New-AzureRmStreamAnalyticsJob`. Nahraďte hodnotu `jobDefinitionFile` proměnné s cestu, kam jste uložili soubor JSON s definicí úlohy. 

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

Přidejte do úlohy vstup pomocí rutiny [New-AzureRmStreamAnalyticsInput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsinput?view=azurermps-5.4.0). Tato rutina použije název úlohy, název vstupu úlohy, název skupiny prostředků a definici vstupu úlohy jako parametry. Definici vstupu úlohy představuje soubor JSON, který obsahuje vlastnosti potřebné ke konfiguraci vstupu úlohy. V tomto příkladu vytvoříte úložiště objektů blob jako vstup. 

Na místním počítači vytvořte soubor s názvem `JobInputDefinition.json` a přidejte do něj následující data JSON. Nezapomeňte nahradit hodnotu `accesspolicykey` s `SharedAccessKey` část připojovací řetězec služby IoT Hub, jste si uložili v předchozí části.

```json
{
    "properties": {
        "type": "Stream",
        "datasource": {
            "type": "Microsoft.Devices/IotHubs",
            "properties": {
                "iotHubNamespace": "MyASAIoTHub",
                "sharedAccessPolicyName": "iothubowner",
                "sharedAccessPolicyKey": "accesspolicykey",
                "endpoint": "messages/events",
                "consumerGroupName": "$Default"
                }
        },
        "compression": {
            "type": "None"
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8"
            }
        }
    },
    "name": "IoTHubInput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/inputs"
}
```

V dalším kroku spusťte `New-AzureRmStreamAnalyticsInput` rutiny, nezapomeňte nahradit hodnotu `jobDefinitionFile` proměnné s cestu, kam jste uložili soubor JSON s definicí vstupu úlohy. 

```powershell
$jobInputName = "IoTHubInput"
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
                      "accountName": "asaquickstartstorage",
                      "accountKey": "<storage account key>"
                    }],
                "container": "container1",
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
    "name": "BlobOutput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/outputs"
}
```

Potom spusťte rutinu `New-AzureRmStreamAnalyticsOutput`. Nezapomeňte nahradit hodnotu proměnné `jobOutputDefinitionFile` cestou, do které jste uložili soubor JSON definice výstupu úlohy. 

```powershell
$jobOutputName = "BlobOutput"
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
      "query":" SELECT * INTO BlobOutput FROM IoTHubInput HAVING Temperature > 27"  
   }  
}
```

Potom spusťte rutinu `New-AzureRmStreamAnalyticsTransformation`. Nezapomeňte nahradit hodnotu `jobTransformationDefinitionFile` proměnné s cestu, kam jste uložili soubor JSON s definicí transformace úlohy. 

```powershell
$jobTransformationName = "MyJobTransformation"
$jobTransformationDefinitionFile = "C:\JobTransformationDefinition.json"
New-AzureRmStreamAnalyticsTransformation `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobTransformationDefinitionFile `
  -Name $jobTransformationName -Force
```
## <a name="run-the-iot-simulator"></a>Spustit simulátor IoT

1. Otevřít [Raspberry Pi Online simulátoru Azure IoT](https://azure-samples.github.io/raspberry-pi-web-simulator/).

2. Nahraďte zástupný text v řádku 15 celý Azure IoT Hub Device připojovací řetězec, který jste uložili v předchozí části.

3. Klikněte na **Run** (Spustit). Výstup by měl zobrazit data ze senzorů a zprávy odeslané do služby IoT Hub.

   ![Online simulátor Raspberry Pi Azure IoT](./media/stream-analytics-quick-create-powershell/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Spuštění úlohy Stream Analytics a kontrola výstupu

Spusťte úlohu pomocí rutiny [Start-AzureRMStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0). Tato rutina použije název úlohy, název skupiny prostředků, režim spuštění výstupu a čas spuštění jako parametry. Parametr `OutputStartMode` připouští hodnoty `JobStartTime`, `CustomTime` nebo `LastOutputEventTime`. Další informace o tom, na co tyto hodnoty odkazují, najdete v části [Parametry](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0) v dokumentaci k prostředí PowerShell. 

Jakmile spustíte následující rutinu, vrátí jako výstup hodnotu `True`, pokud se úloha spustí. V kontejneru úložiště se vytvoří výstupní složku s transformovanými daty. 

```powershell
Start-AzureRmStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte skupinu prostředků, úlohu streamování a všechny související prostředky, pokud je už nepotřebujete. Odstraněním úlohy se zabrání zaúčtování jednotek streamování, které daná úloha spotřebovává. Pokud máte v plánu tuto úlohu ještě někdy používat, nemusíte ji odstraňovat a prozatím ji jenom zastavte. Pokud nebudete nadále používat tuto úlohu, odstraňte všechny prostředky vytvořené podle tohoto rychlého startu spuštěním následující rutiny:

```powershell
Remove-AzureRmResourceGroup `
  -Name $resourceGroup 
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste pomocí PowerShellu nasadili jednoduchou úlohu Stream Analytics. Úlohy Stream Analytics můžete nasadit také pomocí webu [Azure Portal](stream-analytics-quick-create-portal.md) a sady [Visual Studio](stream-analytics-quick-create-vs.md).

Pokud se chcete dozvědět o konfiguraci dalších vstupních zdrojů a provádění detekce v reálném čase, pokračujte na další článek:

> [!div class="nextstepaction"]
> [Zjišťování možných podvodů v reálném čase pomocí Stream Analytics](stream-analytics-real-time-fraud-detection.md)
