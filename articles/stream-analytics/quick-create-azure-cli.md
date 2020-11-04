---
title: Rychlý Start – vytvoření Azure Stream Analytics úlohy pomocí Azure CLI
description: V tomto rychlém startu se dozvíte, jak pomocí rozhraní příkazového řádku Azure vytvořit úlohu Azure Stream Analytics.
services: stream-analytics
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.workload: big-data
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 07/01/2020
ms.openlocfilehash: 8c2e7b6a02c0a0fea32fb1effb30b682971c3f6f
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348768"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-using-the-azure-cli"></a>Rychlý Start: vytvoření úlohy Azure Stream Analytics pomocí Azure CLI

V tomto rychlém startu použijete rozhraní příkazového řádku Azure CLI k definování Stream Analytics úlohy, která filtruje zprávy snímačů v reálném čase s teplotou, která je čtena více než 27. Vaše úloha Stream Analytics načte data z IoT Hub, transformují data a zapíše data zpátky do kontejneru v úložišti objektů BLOB. Vstupní data použitá v tomto rychlém startu se generují simulátoru malinu PI v online režimu.

## <a name="before-you-begin"></a>Než začnete

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-your-environment"></a>Příprava prostředí

1. Přihlaste se.

   Pokud používáte místní instalaci rozhraní příkazového řádku, přihlaste se pomocí příkazu [az login](/cli/azure/reference-index#az-login).

    ```azurecli
    az login
    ```

    Dokončete proces ověřování podle kroků zobrazených v terminálu.

2. Nainstalujte rozšíření Azure CLI.

   Když pracujete s odkazy na rozšíření rozhraní příkazového řádku Azure (Azure CLI), musíte nejdřív rozšíření nainstalovat.  Rozšíření rozhraní příkazového řádku Azure poskytují přístup k experimentálním a předběžným příkazům, které ještě nebyly dodány jako součást základního rozhraní příkazového řádku.  Další informace o rozšířeních, včetně aktualizace a odinstalace, najdete v tématu [Využití rozšíření v Azure CLI](/cli/azure/azure-cli-extensions-overview).

   [Rozšíření pro Stream Analytics](/cli/azure/ext/stream-analytics/stream-analytics) nainstalujete spuštěním následujícího příkazu:

    ```azurecli
    az extension add --name stream-analytics
    ```

   [Rozšíření pro Azure IoT](/cli/azure/ext/azure-iot) nainstalujete spuštěním následujícího příkazu:

    ```azurecli
    az extension add --name azure-iot
    ```

3. Vytvořte skupinu prostředků.

   Všechny prostředky Azure musí být nasazené do skupiny prostředků. Skupiny prostředků vám umožňují organizaci a správu souvisejících prostředků Azure.

   V tomto rychlém startu vytvořte skupinu prostředků s názvem *streamanalyticsrg* v umístění *eastus* pomocí následujícího příkazu [AZ Group Create](/cli/azure/group#az-group-create) :

   ```azurecli
   az group create --name streamanalyticsrg --location eastus
   ```

## <a name="prepare-the-input-data"></a>Příprava vstupních dat

Před definováním Stream Analytics úlohy připravte data, která se používají pro vstup úlohy.

Následující bloky kódu Azure CLI jsou příkazy, které připravují vstupní data požadovaná úlohou. Prohlédněte si jednotlivé části a seznamte se s kódem.

1. Pomocí příkazu [AZ IoT Hub Create](../iot-hub/iot-hub-create-using-cli.md#create-an-iot-hub) vytvořte IoT Hub. Tento příklad vytvoří IoT Hub s názvem **MyASAIoTHub**. Vzhledem k tomu, že názvy IoT Hub jsou jedinečné, je třeba se s vlastním názvem IoT Hub. Nastavte SKU na F1 na použití bezplatné úrovně, pokud je k dispozici v rámci vašeho předplatného. Pokud ne, vyberte nejbližší nejnižší úroveň.

    ```azurecli
    az iot hub create --name "MyASAIoTHub" --resource-group streamanalyticsrg --sku S1
    ```

    Po vytvoření centra IoT Získejte připojovací řetězec IoT Hub pomocí příkazu [AZ IoT Hub show-Connection-String](/cli/azure/iot/hub) . Zkopírujte celý připojovací řetězec a uložte ho pro při přidání IoT Hub jako vstupu do úlohy Stream Analytics.

    ```azurecli
    az iot hub show-connection-string --hub-name "MyASAIoTHub"
    ```

2. Pomocí příkazu [AZ iothub Device-identity Create](../iot-hub/quickstart-send-telemetry-c.md#register-a-device) přidejte zařízení, které chcete IoT Hub. Tento příklad vytvoří zařízení s názvem **MyASAIoTDevice**.

    ```azurecli
    az iot hub device-identity create --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice"
    ```

3. Pomocí příkazu [AZ IoT Hub Device-identity show-Connection-String](/cli/azure/ext/azure-iot/iot/hub/device-identity#ext-azure-iot-az-iot-hub-device-identity-show-connection-string) Získejte připojovací řetězec zařízení. Zkopírujte celý připojovací řetězec a při vytváření simulátoru pro maliny PI ho uložte.

    ```azurecli
    az iot hub device-identity show-connection-string --hub-name "MyASAIoTHub" --device-id "MyASAIoTDevice" --output table
    ```

    **Příklad výstupu:**

    ```output
    HostName=MyASAIoTHub.azure-devices.net;DeviceId=MyASAIoTDevice;SharedAccessKey=a2mnUsg52+NIgYudxYYUNXI67r0JmNubmfVafojG8=
    ```

## <a name="create-a-blob-storage-account"></a>Vytvoření účtu úložiště BLOB

Následující bloky kódu Azure CLI vytvoří účet Blob Storage, který se používá pro výstup úlohy. Prohlédněte si jednotlivé části a seznamte se s kódem.

1. Účet úložiště pro obecné účely vytvoříte příkazem [az storage account create](/cli/azure/storage/account). Účet úložiště pro obecné účely můžete použít pro všechny čtyři služby: objekty blob, soubory, tabulky a fronty.

   Nezapomeňte nahradit zástupné hodnoty v lomených závorkách vlastními hodnotami:

   ```azurecli
   az storage account create \
       --name <storage-account> \
       --resource-group streamanalyticsrg \
       --location eastus \
       --sku Standard_ZRS \
       --encryption-services blob
   ```

2. Klíč pro účet úložiště získáte spuštěním příkazu [AZ Storage Account Keys list](/cli/azure/storage/account/keys) . Uložte tento klíč pro použití v dalším kroku.

   ```azurecli
   az storage account keys list -g streamanalyticsrg -n <storage-account>
   ```

3. K vytvoření kontejneru pro ukládání objektů blob použijte příkaz [az storage container create](/cli/azure/storage/container). Klíč účtu úložiště použijete k autorizaci operace vytvoření kontejneru. Další informace o autorizaci operací s daty pomocí Azure CLI najdete v tématu [autorizace přístupu k objektům blob nebo Queue data ve frontě pomocí Azure CLI](../storage/common/authorize-data-operations-cli.md).

   ```azurecli
   az storage container create \
       --account-name <storage-account> \
       --name sample-container \
       --account-key <key>
       --auth-mode key
   ```

## <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics

Následující bloky kódu Azure CLI vytvoří úlohu Stream Analytics. Přečtěte si oddíly, abyste porozuměli kódu

1. Vytvořte Stream Analyticsovou úlohu pomocí příkazu [AZ Stream-Analytics Create](/cli/azure/ext/stream-analytics/stream-analytics/job#ext-stream-analytics-az-stream-analytics-job-create) .

```azurecli
az stream-analytics job create \
    --resource-group streamanalyticsrg 
    --name streamanalyticsjob \
    --location eastus \
    --output-error-policy "Drop" \
    --events-outoforder-policy "Drop" \
    --events-outoforder-max-delay 5 \
    --events-late-arrival-max-delay 16 \     
    --data-locale "en-US"
```

## <a name="configure-input-to-the-job"></a>Konfigurace vstupu do úlohy

Přidejte vstup do úlohy pomocí rutiny [AZ Stream-Analytics Input](/cli/azure/ext/stream-analytics/stream-analytics/input#ext-stream-analytics-az-stream-analytics-input-create) . Tato rutina použije název úlohy, název vstupu úlohy, název skupiny prostředků a definici vstupu úlohy jako parametry. Definice vstupu úlohy je soubor JSON, který obsahuje vlastnosti požadované ke konfiguraci vstupu úlohy. V tomto příkladu vytvoříte IoT Hub jako vstup.

Na místním počítači vytvořte soubor s názvem `datasource.json` a přidejte do něj následující data JSON. Nezapomeňte nahradit hodnotu v `sharedAccessPolicyKey` `SharedAccessKey` části připojovacího řetězce IoT Hub, který jste uložili v předchozí části.

```json
{
    "type": "Microsoft.Devices/IotHubs",
    "properties": {
        "iotHubNamespace": "iothub",
        "sharedAccessPolicyName": "iothubowner",
        "sharedAccessPolicyKey": "sharedAccessPolicyKey=",
        "consumerGroupName": "sdkconsumergroup",
        "endpoint": "messages/events"
    }
}
```

Na místním počítači vytvořte soubor s názvem `serialization.json` a přidejte do něj následující data JSON.

```json
{
     "type": "Json",
     "properties": {
         "encoding": "UTF8"
     }
}
```

Potom spusťte rutinu `az stream-analytics input create`. Nezapomeňte nahradit hodnotu `datasource` proměnné cestou, kam jste uložili soubor JSON definice vstupu úlohy, a hodnotou `serialization` proměnné s cestou, kam jste ULOŽILI soubor JSON serializace.

```azurecli
az stream-analytics input create 
    --resource-group streamanalyticsrg 
    --job-name streamanalyticsjob \
    --name asaiotinput \
    --type Stream \
    --datasource datasource.json \
    --serialization serialization.json
```

## <a name="configure-output-to-the-job"></a>Konfigurace výstupu do úlohy

Do úlohy přidejte výstup pomocí rutiny [AZ Stream-Analytics Output Create](/cli/azure/ext/stream-analytics/stream-analytics/output#ext-stream-analytics-az-stream-analytics-output-create) . Tato rutina použije název úlohy, název výstupu úlohy, název skupiny prostředků a definici výstupu úlohy jako parametry. Definice výstupu úlohy je soubor JSON, který obsahuje vlastnosti požadované ke konfiguraci výstupu úlohy. V tomto příkladu je výstupem úložiště objektů blob.

Na místním počítači vytvořte soubor s názvem `datasink.json` a přidejte do něj následující data JSON. Nezapomeňte nahradit hodnotu pro `accountKey` přístupový klíč účtu úložiště, který je hodnotou uloženou v $storageAccountKey Value.

```json
{
    "type": "Microsoft.Storage/Blob",
    "properties": {
        "storageAccounts": [
            {
                "accountName": "<storage-account>",
                "accountKey": "accountKey=="
            }
        ],
        "container": "state",
        "pathPattern": "{date}/{time}",
        "dateFormat": "yyyy/MM/dd",
        "timeFormat": "HH"
    }
}
```

Potom spusťte rutinu `az stream-analytics output`. Nezapomeňte nahradit hodnotu `datasource` proměnné cestou, kam jste uložili soubor JSON s definicí výstupu úlohy, a hodnotou `serialization` proměnné s cestou, kam jste ULOŽILI soubor JSON serializace.

```azurecli
az stream-analytics output create 
    --resource-group streamanalyticsrg \
    --job-name streamanalyticsjob \
    --name asabloboutput \
    --datasource datasink.json \
    --serialization serialization.json
```

## <a name="define-the-transformation-query"></a>Definice transformačního dotazu

Přidejte transformaci úlohy pomocí rutiny [AZ Stream-Analytics Transform Create](/cli/azure/ext/stream-analytics/stream-analytics/transformation#ext-stream-analytics-az-stream-analytics-transformation-create) . Tato rutina použije název úlohy, název transformace úlohy, název skupiny prostředků a definici transformace úlohy jako parametry. 

Spusťte `az stream-analytics transformation create` rutinu.

```azurecli
az stream-analytics transformation create 
    --resource-group streamanalyticsrg \
    --job-name streamanalyticsjob \
    --name Transformation \
    --streaming-units "6" \
    --transformation-query "SELECT * INTO asabloboutput FROM asaiotinput HAVING Temperature > 27"
```
## <a name="run-the-iot-simulator"></a>Spuštění simulátoru IoT

1. Otevřete [simulátor Azure IoT online pro maliny](https://azure-samples.github.io/raspberry-pi-web-simulator/).

2. Zástupný text na řádku 15 nahraďte celým připojovacím řetězcem zařízení Azure IoT Hub, který jste uložili v předchozí části.

3. Klikněte na **Spustit**. Výstup by měl ukazovat data a zprávy snímače, které se odesílají do vašeho IoT Hub.

    ![Online simulátor Raspberry Pi Azure IoT](./media/stream-analytics-quick-create-powershell/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Spuštění úlohy Stream Analytics a kontrola výstupu

Spusťte úlohu pomocí rutiny [AZ Stream-Analytics Start](/cli/azure/ext/stream-analytics/stream-analytics/job#ext-stream-analytics-az-stream-analytics-job-start) . Tato rutina použije název úlohy, název skupiny prostředků, režim spuštění výstupu a čas spuštění jako parametry. Parametr `OutputStartMode` připouští hodnoty `JobStartTime`, `CustomTime` nebo `LastOutputEventTime`.

Jakmile spustíte následující rutinu, vrátí jako výstup hodnotu `True`, pokud se úloha spustí. V kontejneru úložiště se vytvoří výstupní složku s transformovanými daty.

```azurecli
az stream-analytics job start 
    --resource-group streamanalyticsrg \
    --name streamanalyticsjob \
    --output-start-mode JobStartTime
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte skupinu prostředků, úlohu streamování a všechny související prostředky, pokud je už nepotřebujete. Odstraněním úlohy se zabrání zaúčtování jednotek streamování, které daná úloha spotřebovává. Pokud máte v plánu tuto úlohu ještě někdy používat, nemusíte ji odstraňovat a prozatím ji jenom zastavte. Pokud nebudete tuto úlohu nadále používat, odstraňte všechny prostředky vytvořené tímto rychlým startem spuštěním následující rutiny:

```powershell
az group delete \
    --name streamanalyticsrg \
    --no-wait
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili jednoduchou Stream Analyticsovou úlohu pomocí Azure CLI. Úlohy Stream Analytics můžete nasadit také pomocí webu [Azure Portal](stream-analytics-quick-create-portal.md) a sady [Visual Studio](stream-analytics-quick-create-vs.md).

Pokud se chcete dozvědět o konfiguraci dalších vstupních zdrojů a provádění detekce v reálném čase, pokračujte na další článek:

> [!div class="nextstepaction"]
> [Zjišťování možných podvodů v reálném čase pomocí Stream Analytics](stream-analytics-real-time-fraud-detection.md)