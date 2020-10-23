---
title: Ingestování telemetrie z IoT Hubu
titleSuffix: Azure Digital Twins
description: Podívejte se, jak ingestovat zprávy telemetrie zařízení z IoT Hub.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 9/15/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 0123a89c4ec1c2c70326de1a2f685b08278333ab
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461545"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Ingestování IoT Hub telemetrie do digitálních vláken Azure

Digitální vlákna Azure se řídí daty ze zařízení IoT a dalších zdrojů. Běžný zdroj dat zařízení, která se mají používat v rámci digitálních vláken Azure, je [IoT Hub](../iot-hub/about-iot-hub.md).

Proces pro ingestování dat do digitálních vláken Azure je nastavení externího výpočetního prostředku, jako je třeba [funkce Azure](../azure-functions/functions-overview.md), která obdrží data a pomocí [rozhraní DigitalTwins API](/rest/api/digital-twins/dataplane/twins) nastavují vlastnosti nebo události telemetrie pro [digitální vlákna](concepts-twins-graph.md) . 

Tento postup popisuje, jak dokumentovat pomocí procesu vytváření funkce Azure, která může ingestovat telemetrii od IoT Hub.

## <a name="prerequisites"></a>Požadavky

Než budete pokračovat v tomto příkladu, budete muset nastavit následující prostředky jako požadavky:
* **Centrum IoT**. Pokyny najdete v části *vytvoření IoT Hub* [tohoto IoT Hub rychlé](../iot-hub/quickstart-send-telemetry-cli.md)spuštění.
* **Funkce Azure** se správnými oprávněními pro volání vaší digitální zdvojené instance. Pokyny najdete v tématu [*Postup: nastavení funkce Azure pro zpracování dat*](how-to-create-azure-function.md). 
* **Instance digitálního vlákna Azure** , která bude přijímat telemetrii zařízení. Pokyny najdete v tématu [*Postup: nastavení instance a ověřování digitálních vláken Azure*](./how-to-set-up-instance-portal.md).

### <a name="example-telemetry-scenario"></a>Ukázkový scénář telemetrie

Tento postup popisuje, jak odesílat zprávy z IoT Hub do digitálních vláken Azure pomocí funkce Azure Functions. K dispozici je celá řada možných konfigurací a vyhovujících strategií, které můžete použít pro posílání zpráv, ale příklad tohoto článku obsahuje následující části:
* Zařízení teploměr v IoT Hub se známým ID zařízení
* Digitální vlákna představující zařízení s ID odpovídajícího

> [!NOTE]
> V tomto příkladu se používá jednoznačné ID mezi ID zařízení a odpovídajícím ID digitálního vlákna, ale je možné poskytnout propracovanější mapování ze zařízení na jeho dvojitou hodnotu (například s tabulkou mapování).

Pokaždé, když je událost telemetrie teploty odeslána termostatem zařízení, funkce Azure zpracuje telemetrii a vlastnost *teploty* digitálního vlákna by měla aktualizovat. Tento scénář je popsaný v diagramu níže:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Diagram znázorňující vývojový diagram V grafu IoT Hub zařízení odesílá telemetrii teploty prostřednictvím IoT Hub do funkce Azure, která aktualizuje vlastnost teploty v případě, že se v digitálních událostech Azure pracuje s dvojitou teplotou." border="false":::

## <a name="add-a-model-and-twin"></a>Přidání modelu a vlákna

Model můžete přidat nebo nahrát pomocí příkazu CLI níže a pak vytvořit dvojitou hodnotu pomocí tohoto modelu, který se bude aktualizovat informacemi z IoT Hub.

Model vypadá takto:
```JSON
{
  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",
  "@type": "Interface",
  "@context": "dtmi:dtdl:context;2",
  "contents": [
    {
      "@type": "Property",
      "name": "Temperature",
      "schema": "double"
    }
  ]
}
```

Pokud chcete **Tento model nahrát do instance vláken**, otevřete Azure CLI a spusťte následující příkaz:

```azurecli
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

Pak budete muset **vytvořit jeden z těchto vláken pomocí tohoto modelu**. Pomocí následujícího příkazu vytvořte dvojitou hodnotu a nastavte 0,0 jako počáteční hodnotu teploty.

```azurecli
az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}' --dt-name {digital_twins_instance_name}
```

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

Výstup úspěšného zdvojeného příkazu CREATE by měl vypadat takto:
```json
{
  "$dtId": "thermostat67",
  "$etag": "W/\"0000000-9735-4f41-98d5-90d68e673e15\"",
  "$metadata": {
    "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
    "Temperature": {
      "ackCode": 200,
      "ackDescription": "Auto-Sync",
      "ackVersion": 1,
      "desiredValue": 0.0,
      "desiredVersion": 1
    }
  },
  "Temperature": 0.0
}
```

## <a name="create-an-azure-function"></a>Vytvořit funkci Azure

V této části se používají stejné kroky při spuštění sady Visual Studio a Osnova funkcí Azure Functions v tématu [*Postupy: nastavení funkce Azure pro zpracování dat*](how-to-create-azure-function.md). Kostra zpracovává ověřování a vytváří klienta služby, který je připravený na zpracování dat a volání rozhraní API digitálních vláken Azure v reakci. 

V následujících krocích přidáte konkrétní kód pro zpracování událostí telemetrie IoT z IoT Hub.  

### <a name="add-telemetry-processing"></a>Přidat zpracování telemetrie
    
Události telemetrie přicházejí do formy zpráv ze zařízení. Prvním krokem při přidávání kódu pro zpracování telemetrie je extrakce příslušné části této zprávy zařízení z události Event Grid. 

Různá zařízení mohou strukturovat své zprávy různě, takže kód pro **Tento krok závisí na připojeném zařízení.** 

Následující kód ukazuje příklad jednoduchého zařízení, které odesílá telemetrii jako JSON. Tato ukázka je plně proprozkoumána v [*kurzu: připojení kompletního řešení*](./tutorial-end-to-end.md). Následující kód vyhledá ID zařízení, které zprávu odeslalo, a také hodnotu teploty.

```csharp
JObject deviceMessage = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
string deviceId = (string)deviceMessage["systemProperties"]["iothub-connection-device-id"];
var temperature = deviceMessage["body"]["Temperature"];
```

Další ukázka kódu vezme ID a teplotu a použije je k opravě (provedení aktualizací), které jsou vytvářené.

```csharp
//Update twin using device temperature
var uou = new UpdateOperationsUtility();
uou.AppendReplaceOp("/Temperature", temperature.Value<double>());
await client.UpdateDigitalTwinAsync(deviceId, uou.Serialize());
...
```

### <a name="update-your-azure-function-code"></a>Aktualizace kódu funkce Azure

Teď, když rozumíte kódu z předchozích ukázek, otevřete Azure Functions v části [*požadavky*](#prerequisites) v aplikaci Visual Studio. (Pokud nemáte funkci Azure Functions, navštivte odkaz v části požadavky a vytvořte si ho hned teď).

Nahraďte kód vaší funkce Azure pomocí tohoto ukázkového kódu.

```csharp
using System;
using System.Net.Http;
using Azure.Core.Pipeline;
using Azure.DigitalTwins.Core;
using Azure.DigitalTwins.Core.Serialization;
using Azure.Identity;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace IotHubtoTwins
{
    public class IoTHubtoTwins
    {
        private static readonly string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("IoTHubtoTwins")]
        public async void Run([EventGridTrigger] EventGridEvent eventGridEvent, ILogger log)
        {
            if (adtInstanceUrl == null) log.LogError("Application setting \"ADT_SERVICE_URL\" not set");

            try
            {
                //Authenticate with Digital Twins
                ManagedIdentityCredential cred = new ManagedIdentityCredential("https://digitaltwins.azure.net");
                DigitalTwinsClient client = new DigitalTwinsClient(
                    new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions 
                    { Transport = new HttpClientTransport(httpClient) });
                log.LogInformation($"ADT service client connection created.");
            
                if (eventGridEvent != null && eventGridEvent.Data != null)
                {
                    log.LogInformation(eventGridEvent.Data.ToString());

                    // Reading deviceId and temperature for IoT Hub JSON
                    JObject deviceMessage = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
                    string deviceId = (string)deviceMessage["systemProperties"]["iothub-connection-device-id"];
                    var temperature = deviceMessage["body"]["Temperature"];
                    
                    log.LogInformation($"Device:{deviceId} Temperature is:{temperature}");

                    //Update twin using device temperature
                    var uou = new UpdateOperationsUtility();
                    uou.AppendReplaceOp("/Temperature", temperature.Value<double>());
                    await client.UpdateDigitalTwinAsync(deviceId, uou.Serialize());
                }
            }
            catch (Exception e)
            {
                log.LogError($"Error in ingest function: {e.Message}");
            }
        }
    }
}
```
Uložte kód funkce a publikujte aplikaci Function App do Azure. Můžete to udělat tak, že odkazujete na téma [*publikování Function App*](./how-to-create-azure-function.md#publish-the-function-app-to-azure) tématu [*Postupy: nastavení funkce Azure pro zpracování dat*](how-to-create-azure-function.md).

Po úspěšném publikování se zobrazí výstup v okně příkazového řádku sady Visual Studio, jak je znázorněno níže:

```cmd
1>------ Build started: Project: adtIngestFunctionSample, Configuration: Release Any CPU ------
1>adtIngestFunctionSample -> C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\bin\Release\netcoreapp3.1\bin\adtIngestFunctionSample.dll
2>------ Publish started: Project: adtIngestFunctionSample, Configuration: Release Any CPU ------
2>adtIngestFunctionSample -> C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\bin\Release\netcoreapp3.1\bin\adtIngestFunctionSample.dll
2>adtIngestFunctionSample -> C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\obj\Release\netcoreapp3.1\PubTmp\Out\
2>Publishing C:\Users\source\repos\Others\adtIngestFunctionSample\adtIngestFunctionSample\obj\Release\netcoreapp3.1\PubTmp\adtIngestFunctionSample - 20200911112545669.zip to https://adtingestfunctionsample20200818134346.scm.azurewebsites.net/api/zipdeploy...
========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
========== Publish: 1 succeeded, 0 failed, 0 skipped ==========
```
Můžete také ověřit stav procesu publikování v [Azure Portal](https://portal.azure.com/). Vyhledejte _skupinu prostředků_ , přejděte do _protokolu aktivit_ a vyhledejte _profil publikování webové aplikace_ v seznamu a ověřte, jestli je stav úspěšný.

:::image type="content" source="media/how-to-ingest-iot-hub-data/azure-function-publish-activity-log.png" alt-text="Diagram znázorňující vývojový diagram V grafu IoT Hub zařízení odesílá telemetrii teploty prostřednictvím IoT Hub do funkce Azure, která aktualizuje vlastnost teploty v případě, že se v digitálních událostech Azure pracuje s dvojitou teplotou.":::

## <a name="connect-your-function-to-iot-hub"></a>Připojte funkci k IoT Hub

Nastavte cíl události pro data centra.
V [Azure Portal](https://portal.azure.com/)přejděte na instanci IoT Hub, kterou jste vytvořili v části [*požadavky*](#prerequisites) . V části **události**Vytvořte předplatné pro funkci Azure Functions.

:::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Diagram znázorňující vývojový diagram V grafu IoT Hub zařízení odesílá telemetrii teploty prostřednictvím IoT Hub do funkce Azure, která aktualizuje vlastnost teploty v případě, že se v digitálních událostech Azure pracuje s dvojitou teplotou.":::

Na stránce **vytvořit odběr události** vyplňte pole následujícím způsobem:
  1. V části **název**zadejte název předplatného, co byste chtěli.
  2. V části **schéma události**vyberte _Event Grid schéma_.
  3. V části **typy událostí**vyberte zaškrtávací políčko _telemetrie zařízení_ a zrušte zaškrtnutí ostatních typů událostí.
  4. V části **Typ koncového bodu**vyberte _Azure Function_.
  5. V části **koncový bod**zvolte _možnost vybrat odkaz na koncový bod_ a vytvořte koncový bod.
    
:::image type="content" source="media/how-to-ingest-iot-hub-data/create-event-subscription.png" alt-text="Diagram znázorňující vývojový diagram V grafu IoT Hub zařízení odesílá telemetrii teploty prostřednictvím IoT Hub do funkce Azure, která aktualizuje vlastnost teploty v případě, že se v digitálních událostech Azure pracuje s dvojitou teplotou.":::

Na stránce _Vybrat funkci Azure_ , která se otevře, ověřte níže uvedené podrobnosti.
 1. **Předplatné:** Vaše předplatné Azure.
 2. **Skupina prostředků**: vaše skupina prostředků
 3. **Aplikace Function App**: název vaší aplikace Function App
 4. **Slot**: _Výroba_
 5. **Funkce**: z rozevíracího seznamu vyberte svou funkci Azure.

Kliknutím na tlačítko _potvrdit výběr_ uložte podrobnosti.            
      
:::image type="content" source="media/how-to-ingest-iot-hub-data/select-azure-function.png" alt-text="Diagram znázorňující vývojový diagram V grafu IoT Hub zařízení odesílá telemetrii teploty prostřednictvím IoT Hub do funkce Azure, která aktualizuje vlastnost teploty v případě, že se v digitálních událostech Azure pracuje s dvojitou teplotou.":::

Vyberte _vytvořit_ a vytvořte odběr událostí.

## <a name="send-simulated-iot-data"></a>Odeslat Simulovaná data IoT

Pokud chcete otestovat novou funkci příchozího přenosu dat, použijte simulátor zařízení z [*kurzu: připojení kompletního řešení*](./tutorial-end-to-end.md). Tento kurz se řídí ukázkovým projektem napsaným v jazyce C#. Vzorový kód je umístěný tady: [Azure Digital dokončí ukázky](/samples/azure-samples/digital-twins-samples/digital-twins-samples). V tomto úložišti budete používat projekt **DeviceSimulator** .

V tomto koncovém kurzu proveďte následující kroky:
1. [*Zaregistrujte simulované zařízení s IoT Hub*](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [*Konfigurace a spuštění simulace*](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>Ověřit výsledky

Při současném spuštění simulátoru zařízení se změní hodnota teplota digitálního vlákna. V Azure CLI spuštěním následujícího příkazu zobrazte hodnotu teploty.

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

```azurecli
az dt twin query -q "select * from digitaltwins" -n {digital_twins_instance_name}
```

Výstup by měl obsahovat hodnotu teploty, například:

```json
{
  "result": [
    {
      "$dtId": "thermostat67",
      "$etag": "W/\"0000000-1e83-4f7f-b448-524371f64691\"",
      "$metadata": {
        "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
        "Temperature": {
          "ackCode": 200,
          "ackDescription": "Auto-Sync",
          "ackVersion": 1,
          "desiredValue": 69.75806974934324,
          "desiredVersion": 1
        }
      },
      "Temperature": 69.75806974934324
    }
  ]
}
```

Chcete-li zobrazit změnu hodnoty, opakovaně spusťte příkaz dotazu výše.

## <a name="next-steps"></a>Další kroky

Přečtěte si o příchozím a odchozím přenosu dat pomocí digitálních vláken Azure:
* [*Koncepty: integrace s jinými službami*](concepts-integration.md)