---
title: Ingestování telemetrie z IoT Hubu
titleSuffix: Azure Digital Twins
description: Podívejte se, jak ingestovat zprávy telemetrie zařízení z IoT Hub.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 8/11/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 8d720d77773e506a13f176723ab4583613f1e625
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89291751"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Ingestování IoT Hub telemetrie do digitálních vláken Azure

Digitální vlákna Azure se řídí daty ze zařízení IoT a dalších zdrojů. Běžný zdroj dat zařízení, která se mají používat v rámci digitálních vláken Azure, je [IoT Hub](../iot-hub/about-iot-hub.md).

Proces pro ingestování dat do digitálních vláken Azure je nastavení externího výpočetního prostředku, jako je třeba [funkce Azure](../azure-functions/functions-overview.md), která obdrží data a pomocí [rozhraní DigitalTwins API](how-to-use-apis-sdks.md) nastavují vlastnosti nebo události telemetrie pro [digitální vlákna](concepts-twins-graph.md) . 

Tento postup popisuje, jak dokumentovat pomocí procesu vytváření funkce Azure, která může ingestovat telemetrii od IoT Hub.

## <a name="prerequisites"></a>Požadavky

Než budete pokračovat v tomto příkladu, budete muset nastavit následující prostředky jako požadavky:
* **Centrum IoT**. Pokyny najdete v části *vytvoření IoT Hub* [tohoto IoT Hub rychlé](../iot-hub/quickstart-send-telemetry-cli.md)spuštění.
* **Funkce Azure** se správnými oprávněními pro volání vaší digitální zdvojené instance. Pokyny najdete v tématu [*Postup: nastavení funkce Azure pro zpracování dat*](how-to-create-azure-function.md). 
* **Instance digitálního vlákna Azure** , která bude přijímat telemetrii zařízení. Pokyny najdete v tématu [*Postup: nastavení instance a ověřování digitálních vláken Azure*](./how-to-set-up-instance-portal.md).

### <a name="example-telemetry-scenario"></a>Ukázkový scénář telemetrie

Tento postup popisuje, jak odesílat zprávy z IoT Hub do digitálních vláken Azure pomocí funkce Azure Functions. Existuje mnoho možných konfigurací a vyhovujících strategií, které můžete použít, ale příklad tohoto článku obsahuje následující části:
* Zařízení teploměr v IoT Hub se známým ID zařízení.
* Digitální vlákna představující zařízení s ID odpovídajícího

> [!NOTE]
> V tomto příkladu se používá jednoznačné ID mezi ID zařízení a odpovídajícím ID digitálního vlákna, ale je možné poskytnout propracovanější mapování ze zařízení na jeho dvojitou hodnotu (například s tabulkou mapování).

Pokaždé, když zařízení teploměr odešle událost telemetrie teploty, vlastnost *teplota* digitálního vlákna by se měla aktualizovat. Tento scénář je popsaný v diagramu níže:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Diagram znázorňující vývojový diagram V grafu IoT Hub zařízení odesílá telemetrii teploty prostřednictvím IoT Hub do funkce Azure, která aktualizuje vlastnost teploty u vlákna v digitálních událostech Azure." border="false":::

## <a name="add-a-model-and-twin"></a>Přidání modelu a vlákna

K aktualizaci informací o službě IoT Hub budete potřebovat dvojitou hodnotu.

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

```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

Pak budete muset **vytvořit jeden z těchto vláken pomocí tohoto modelu**. Pomocí následujícího příkazu vytvořte dvojitou hodnotu a nastavte 0,0 jako počáteční hodnotu teploty.

```azurecli-interactive
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

Teď, když rozumíte kódu ze starších ukázek, otevřete Visual Studio a nahraďte kód vaší funkce Azure pomocí tohoto ukázkového kódu.

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

## <a name="connect-your-function-to-iot-hub"></a>Připojte funkci k IoT Hub

1. Nastavte cíl události pro data centra. V [Azure Portal](https://portal.azure.com/)přejděte k instanci IoT Hub. V části **události**Vytvořte předplatné pro funkci Azure Functions. 

    :::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Snímek obrazovky Azure Portal, který ukazuje přidání odběru události":::

2. Na stránce **vytvořit odběr události** vyplňte pole následujícím způsobem:
    1. V části **název**zadejte název předplatného, co byste chtěli.
    2. V části **schéma události**vyberte **Event Grid schéma**.
    3. V části **Název systémového tématu**vyberte jedinečný název.
    4. V části **typy událostí**vyberte **telemetrie zařízení** jako typ události, na který se má filtrovat.
    5. V části **Podrobnosti o koncovém bodu**vyberte Azure Function jako koncový bod.

    :::image type="content" source="media/how-to-ingest-iot-hub-data/event-subscription-2.png" alt-text="Snímek obrazovky Azure Portal zobrazující podrobnosti odběru události":::

## <a name="send-simulated-iot-data"></a>Odeslat Simulovaná data IoT

Pokud chcete otestovat novou funkci příchozího přenosu dat, použijte simulátor zařízení z [*kurzu: připojení kompletního řešení*](./tutorial-end-to-end.md). Tento kurz se řídí ukázkovým projektem napsaným v jazyce C#. Vzorový kód je umístěný tady: [ukázky digitálních vláken Azure](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples). V tomto úložišti budete používat projekt **DeviceSimulator** .

V tomto koncovém kurzu proveďte následující kroky:
1. [*Zaregistrujte simulované zařízení s IoT Hub*](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [*Konfigurace a spuštění simulace*](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>Ověřit výsledky

Při současném spuštění simulátoru zařízení se změní hodnota teplota digitálního vlákna. V Azure CLI spuštěním následujícího příkazu zobrazte hodnotu teploty.

[!INCLUDE [digital-twins-known-issue-cloud-shell](../../includes/digital-twins-known-issue-cloud-shell.md)]

```azurecli-interactive
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
