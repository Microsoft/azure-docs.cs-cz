---
title: Úvodní příručka – vytvoření úlohy Stream Analytics pomocí Azure PowerShellu
description: Tento rychlý start ukazuje, jak použít modul Azure PowerShell k nasazení a spuštění úlohy Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.date: 12/20/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
ms.openlocfilehash: 84c132c333e4d6ba052029350f275ebf499a906f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79536798"
---
# <a name="quickstart-create-a-stream-analytics-job-using-azure-powershell"></a>Úvodní příručka: Vytvoření úlohy Stream Analytics pomocí Azure PowerShellu

Modul Azure PowerShell se používá k vytváření a správě prostředků Azure pomocí rutin nebo skriptů PowerShellu. Tento rychlý start podrobně popisuje nasazení a spuštění úlohy Azure Stream Analytics pomocí modulu Azure PowerShell.

Ukázková úloha čte streamovaná data ze zařízení služby IoT Hub. Vstupní data jsou generována online simulátorem Raspberry Pi. Úloha Stream Analytics dále transformuje data pomocí dotazovacího jazyka Stream Analytics k filtrování zpráv s teplotou vyšší než 27°. Nakonec zapíše výsledné výstupní události do souboru v úložišti objektů blob.

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet.](https://azure.microsoft.com/free/)

* Tento rychlý start vyžaduje modul Azure PowerShell. Pomocí příkazu `Get-Module -ListAvailable Az` vyhledejte verzi, která je nainstalovaná na místním počítači. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

* Některé akce ioT hubu nejsou podporované Azure PowerShell a musí být dokončena pomocí Azure CLI verze 2.0.70 nebo novější a rozšíření IoT pro Azure CLI. [Nainstalujte azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) a použít `az extension add --name azure-iot` k instalaci rozšíření IoT.


## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k `Connect-AzAccount` předplatnému Azure pomocí příkazu a zadejte svá přihlašovací údaje Azure v rozbalovacím prohlížeči:

```powershell
# Connect to your Azure account
Connect-AzAccount
```

Pokud máte více než jedno předplatné, vyberte předplatné, které chcete použít pro tento rychlý start spuštěním následujících rutin. Ujistěte se, že jste `<your subscription name>` nahradili názvem vašeho předplatného:

```powershell
# List all available subscriptions.
Get-AzSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzSubscription -SubscriptionName "<your subscription name>" | Select-AzSubscription
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure s [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

```powershell
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzResourceGroup `
    -Name $resourceGroup `
    -Location $location
```

## <a name="prepare-the-input-data"></a>Příprava vstupních dat

Než začnete definovat úlohu Stream Analytics, připravte si data nakonfigurovaná jako vstup pro tuto úlohu.

Následující blok kódu Azure CLI provádí mnoho příkazů k přípravě vstupních dat požadovaných úlohou. Prohlédněte si jednotlivé části a seznamte se s kódem.

1. V okně PowerShellu spusťte příkaz [az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) a přihlaste se ke svému účtu Azure.

    Když se úspěšně přihlásíte, Azure CLI vrátí seznam vašich předplatných. Zkopírujte předplatné, které používáte pro tento rychlý start, a spusťte příkaz [az account set](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#change-the-active-subscription) a vyberte toto předplatné. Vyberte stejné předplatné, které jste vybrali v předchozí části s PowerShellem. Nezapomeňte nahradit `<your subscription name>` název předplatného.

    ```azurecli
    az login

    az account set --subscription "<your subscription>"
    ```

2. Vytvořte službu IoT Hub pomocí příkazu [az iot hub create](../iot-hub/iot-hub-create-using-cli.md#create-an-iot-hub) . Tento příklad vytvoří iot hub s názvem **MyASAIoTHub**. Vzhledem k tomu, že názvy ioT hubu jsou jedinečné, musíte přijít s vlastním názvem ioT hubu. Nastavte skladovou položku na F1 tak, aby používala úroveň free, pokud je k dispozici s vaším předplatným. Pokud ne, zvolte další nejnižší úroveň.

    ```azurecli
    az iot hub create --name "<your IoT Hub name>" --resource-group $resourceGroup --sku S1
    ```

    Po vytvoření služby IoT hub získáte připojovací řetězec služby IoT Hub pomocí příkazu [show-connection-string centra az iot hub.](https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest) Zkopírujte celý připojovací řetězec a uložte ho, když přidáte službu IoT Hub jako vstup do úlohy Stream Analytics.

    ```azurecli
    az iot hub show-connection-string --hub-name "MyASAIoTHub"
    ```

3. Přidejte zařízení do služby IoT Hub pomocí příkazu [az iothub device-identity create.](../iot-hub/quickstart-send-telemetry-c.md#register-a-device) Tento příklad vytvoří zařízení s názvem **MyASAIoTDevice**.

    ```azurecli
    az iot hub device-identity create --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice"
    ```

4. Získejte připojovací řetězec zařízení pomocí příkazu [az iot hub device-identity show-connection-string.](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity#ext-azure-cli-iot-ext-az-iot-hub-device-identity-show-connection-string) Zkopírujte celý připojovací řetězec a uložte jej při vytváření simulátoru Raspberry Pi.

    ```azurecli
    az iot hub device-identity show-connection-string --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice" --output table
    ```

    **Příklad výstupu:**

    ```output
    HostName=MyASAIoTHub.azure-devices.net;DeviceId=MyASAIoTDevice;SharedAccessKey=a2mnUsg52+NIgYudxYYUNXI67r0JmNubmfVafojG8=
    ```

## <a name="create-blob-storage"></a>Vytvoření úložiště objektů blob

Následující blok kódu Azure PowerShell používá příkazy k vytvoření úložiště objektů blob, které se používá pro výstup úlohy. Prohlédněte si jednotlivé části a seznamte se s kódem.

1. Vytvořte standardní účet úložiště pro obecné účely pomocí rutiny [New-AzStorageAccount.](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount)  Tento příklad vytvoří účet úložiště s názvem **myasaquickstartstorage** s místně redundantní úložiště (LRS) a šifrování objektů blob (ve výchozím nastavení povoleno).

2. Načtěte kontext účtu úložiště `$storageAccount.Context` určující účet úložiště, který chcete použít. Když pracujete s účtem úložiště, namísto opakovaného zadávání přihlašovacích údajů odkazujete na jeho kontext.

3. Vytvořte kontejner úložiště pomocí [New-AzStorageContainer](https://docs.microsoft.com/powershell/module/az.storage/new-azstoragecontainer).

4. Zkopírujte klíč úložiště, který je výstupem kódu, a uložte jej, abyste později vytvořili výstup úlohy streamování.

    ```powershell
    $storageAccountName = "myasaquickstartstorage"
    $storageAccount = New-AzStorageAccount `
      -ResourceGroupName $resourceGroup `
      -Name $storageAccountName `
      -Location $location `
      -SkuName Standard_LRS `
      -Kind Storage

    $ctx = $storageAccount.Context
    $containerName = "container1"

    New-AzStorageContainer `
      -Name $containerName `
      -Context $ctx

    $storageAccountKey = (Get-AzStorageAccountKey `
      -ResourceGroupName $resourceGroup `
      -Name $storageAccountName).Value[0]

    Write-Host "The <storage account key> placeholder needs to be replaced in your output json files with this key value:"
    Write-Host $storageAccountKey -ForegroundColor Cyan
    ```

## <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics

Vytvořte úlohu Stream Analytics s [rutinou New-AzStreamAnalyticsJob.](https://docs.microsoft.com/powershell/module/az.streamanalytics/new-azstreamanalyticsjob) Tato rutina bere název úlohy, název skupiny prostředků a definici úlohy jako parametry. Jako název úlohy můžete zadat jakýkoli popisný název, který identifikuje vaši úlohu. Může mít pouze alfanumerické znaky, pomlčky a podtržítka a musí mít dlouhou 3 až 63 znaků. Definici úlohy představuje soubor JSON, který obsahuje vlastnosti potřebné k vytvoření úlohy. Na místním počítači vytvořte soubor s názvem `JobDefinition.json` a přidejte do něj následující data JSON:

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

Potom spusťte rutinu `New-AzStreamAnalyticsJob`. Nahraďte `jobDefinitionFile` hodnotu proměnné cestou, do které jste uložili soubor JSON definice úlohy.

```powershell
$jobName = "MyStreamingJob"
$jobDefinitionFile = "C:\JobDefinition.json"
New-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -File $jobDefinitionFile `
  -Name $jobName `
  -Force
```

## <a name="configure-input-to-the-job"></a>Konfigurace vstupu do úlohy

Přidejte vstup do úlohy pomocí [rutiny New-AzStreamAnalyticsInput.](https://docs.microsoft.com/powershell/module/az.streamanalytics/new-azstreamanalyticsinput) Tato rutina použije název úlohy, název vstupu úlohy, název skupiny prostředků a definici vstupu úlohy jako parametry. Definici vstupu úlohy představuje soubor JSON, který obsahuje vlastnosti potřebné ke konfiguraci vstupu úlohy. V tomto příkladu vytvoříte úložiště objektů blob jako vstup.

Na místním počítači vytvořte soubor s názvem `JobInputDefinition.json` a přidejte do něj následující data JSON. Nezapomeňte nahradit hodnotu `accesspolicykey` pro `SharedAccessKey` s částí připojovacího řetězce služby IoT Hub, kterou jste uložili v předchozí části.

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

Dále spusťte rutinu, `New-AzStreamAnalyticsInput` ujistěte se, `jobDefinitionFile` že nahradíte hodnotu proměnné cestou, do které jste uložili soubor JSON definice vstupu úlohy.

```powershell
$jobInputName = "IoTHubInput"
$jobInputDefinitionFile = "C:\JobInputDefinition.json"
New-AzStreamAnalyticsInput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobInputDefinitionFile `
  -Name $jobInputName
```

## <a name="configure-output-to-the-job"></a>Konfigurace výstupu do úlohy

Přidejte výstup do úlohy pomocí [rutiny New-AzStreamAnalyticsOutput.](https://docs.microsoft.com/powershell/module/az.streamanalytics/new-azstreamanalyticsoutput) Tato rutina použije název úlohy, název výstupu úlohy, název skupiny prostředků a definici výstupu úlohy jako parametry. Definici výstupu úlohy představuje soubor JSON, který obsahuje vlastnosti potřebné ke konfiguraci výstupu úlohy. V tomto příkladu je výstupem úložiště objektů blob.

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
                    }
                ],
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

Potom spusťte rutinu `New-AzStreamAnalyticsOutput`. Nezapomeňte nahradit hodnotu proměnné `jobOutputDefinitionFile` cestou, do které jste uložili soubor JSON definice výstupu úlohy.

```powershell
$jobOutputName = "BlobOutput"
$jobOutputDefinitionFile = "C:\JobOutputDefinition.json"
New-AzStreamAnalyticsOutput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobOutputDefinitionFile `
  -Name $jobOutputName -Force
```

## <a name="define-the-transformation-query"></a>Definice transformačního dotazu

Přidejte transformaci úlohy pomocí [rutiny New-AzStreamAnalyticsTransformation.](https://docs.microsoft.com/powershell/module/az.streamanalytics/new-azstreamanalyticstransformation) Tato rutina použije název úlohy, název transformace úlohy, název skupiny prostředků a definici transformace úlohy jako parametry. Na místním počítači vytvořte soubor s názvem `JobTransformationDefinition.json` a přidejte do něj následující data JSON. Soubor JSON obsahuje parametr dotazu, který definuje transformaci dotazu:

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

Potom spusťte rutinu `New-AzStreamAnalyticsTransformation`. Nezapomeňte nahradit hodnotu `jobTransformationDefinitionFile` proměnné cestou, do které jste uložili soubor JSON definice transformace úlohy.

```powershell
$jobTransformationName = "MyJobTransformation"
$jobTransformationDefinitionFile = "C:\JobTransformationDefinition.json"
New-AzStreamAnalyticsTransformation `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobTransformationDefinitionFile `
  -Name $jobTransformationName -Force
```

## <a name="run-the-iot-simulator"></a>Spuštění simulátoru IoT

1. Otevřete [Online simulátor Raspberry Pi Azure IoT](https://azure-samples.github.io/raspberry-pi-web-simulator/).

2. Nahraďte zástupný symbol na lince 15 celým připojovacím řetězcem zařízení Azure IoT Hub, který jste uložili v předchozí části.

3. Klepněte na tlačítko **Spustit**. Výstup by měl zobrazovat data senzorů a zprávy, které jsou odesílány do vašeho ioT hubu.

    ![Online simulátor Raspberry Pi Azure IoT](./media/stream-analytics-quick-create-powershell/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Spuštění úlohy Stream Analytics a kontrola výstupu

Spusťte úlohu pomocí rutiny [Start-AzStreamAnalyticsJob.](https://docs.microsoft.com/powershell/module/az.streamanalytics/start-azstreamanalyticsjob) Tato rutina použije název úlohy, název skupiny prostředků, režim spuštění výstupu a čas spuštění jako parametry. Parametr `OutputStartMode` připouští hodnoty `JobStartTime`, `CustomTime` nebo `LastOutputEventTime`. Další informace o tom, na co tyto hodnoty odkazují, najdete v části [Parametry](https://docs.microsoft.com/powershell/module/az.streamanalytics/start-azstreamanalyticsjob) v dokumentaci k prostředí PowerShell.

Jakmile spustíte následující rutinu, vrátí jako výstup hodnotu `True`, pokud se úloha spustí. V kontejneru úložiště se vytvoří výstupní složku s transformovanými daty.

```powershell
Start-AzStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode 'JobStartTime'
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte skupinu prostředků, úlohu streamování a všechny související prostředky, pokud je už nepotřebujete. Odstraněním úlohy se zabrání zaúčtování jednotek streamování, které daná úloha spotřebovává. Pokud máte v plánu tuto úlohu ještě někdy používat, nemusíte ji odstraňovat a prozatím ji jenom zastavte. Pokud nebudete pokračovat v používání této úlohy, odstraňte všechny prostředky vytvořené tímto rychlým startem spuštěním následující rutiny:

```powershell
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste pomocí PowerShellu nasadili jednoduchou úlohu Stream Analytics. Úlohy Stream Analytics můžete nasadit také pomocí webu [Azure Portal](stream-analytics-quick-create-portal.md) a sady [Visual Studio](stream-analytics-quick-create-vs.md).

Pokud se chcete dozvědět o konfiguraci dalších vstupních zdrojů a provádění detekce v reálném čase, pokračujte na další článek:

> [!div class="nextstepaction"]
> [Zjišťování možných podvodů v reálném čase pomocí Stream Analytics](stream-analytics-real-time-fraud-detection.md)
