---
title: Publikovat, přihlásit se k odběru událostí v cloudu – Azure Event Grid IoT Edge | Dokumenty společnosti Microsoft
description: Publikovat, přihlásit se k odběru událostí v cloudu pomocí Webhooku s Event Grid na IoT Edge
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: c82f1edfc3acd73c1d38425f963aaaf2976a1cc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844581"
---
# <a name="tutorial-publish-subscribe-to-events-in-cloud"></a>Kurz: Publikovat, přihlásit se k odběru událostí v cloudu

Tento článek vás provede všemi kroky potřebnými k publikování a přihlášení k odběru událostí pomocí služby Event Grid na ioT Edge. Tento kurz používá a Azure function jako obslužná rutina události. Další typy cílů naleznete v tématu [obslužné rutiny událostí](event-handlers.md).

Viz [Koncepty mřížky událostí,](concepts.md) kde zjistíte, jaké jsou téma a předplatné mřížky událostí, než budete pokračovat.

## <a name="prerequisites"></a>Požadavky 
Chcete-li dokončit tento výukový program, budete potřebovat:

* **Předplatné Azure** – vytvořte [bezplatný účet,](https://azure.microsoft.com/free) pokud ho ještě nemáte. 
* **Azure IoT Hub a zařízení IoT Edge** – postupujte podle pokynů v rychlém startu pro [Linux](../../iot-edge/quickstart-linux.md) nebo [windows zařízení,](../../iot-edge/quickstart.md) pokud ještě nemáte.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-an-azure-function-in-the-azure-portal"></a>Vytvoření funkce Azure na webu Azure Portal

Podle pokynů uvedených v [kurzu](../../azure-functions/functions-create-first-azure-function.md) vytvořte funkci Azure. 

Nahraďte fragment kódu následujícím kódem:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);

    log.LogInformation($"C# HTTP trigger received {data}.");
    return data != null
        ? (ActionResult)new OkResult()
        : new BadRequestObjectResult("Please pass in the request body");
}
```

V nové funkci vyberte v pravém horním rohu možnost **Získat adresu URL funkce,** vyberte výchozí **(funkční klávesa)** a pak vyberte **Kopírovat**. Hodnotu URL funkce použijete později v kurzu.

> [!NOTE]
> Další ukázky a kurzy reakce na události, které se aktivují pomocí události EventGrid, najdete v dokumentaci k [funkcím Azure.](../../azure-functions/functions-overview.md)

## <a name="create-a-topic"></a>Vytvoření tématu

Jako vydavatel události je třeba vytvořit téma mřížky událostí. Téma odkazuje na koncový bod, do kterého mohou vydavatelé odesílat události.

1. Vytvořte topic2.json s následujícím obsahem. Podrobnosti o datové části najdete v naší dokumentaci k [rozhraní API.](api.md)

    ```json
         {
          "name": "sampleTopic2",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. Chcete-li vytvořit téma, spusťte následující příkaz. Měl by být vrácen stavový kód HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```
1. Spusťte následující příkaz k ověření, že téma bylo úspěšně vytvořeno. Měl by být vrácen stavový kód HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

   Ukázkový výstup:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2",
            "name": "sampleTopic2",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>Vytvoření odběru událostí

Odběratelé se mohou zaregistrovat pro události publikované na téma. Chcete-li přijímat jakékoli události, předplatitelé budou muset vytvořit odběr mřížky událostí na téma zájmu.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Vytvořte subscription2.json s následujícím obsahem. Podrobnosti o datové části naleznete v naší dokumentaci k [rozhraní API.](api.md)

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

   >[!NOTE]
   > **EndpointType** určuje, že odběratel je Webhook.  **EndpointUrl** určuje adresu URL, na které odběratel naslouchá události. Tato adresa URL odpovídá ukázce funkce Azure, kterou jste nastavili dříve.
2. Chcete-li vytvořit odběr, spusťte následující příkaz. Měl by být vrácen stavový kód HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```
3. Spusťte následující příkaz k ověření, že odběr byl úspěšně vytvořen. Měl by být vrácen stavový kód HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```

    Ukázkový výstup:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic2/eventSubscriptions/sampleSubscription2",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription2",
          "properties": {
            "Topic": "sampleTopic2",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-az-func-cloud-url>"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>Publikování události

1. Vytvořte event2.json s následujícím obsahem. Podrobnosti o datové části naleznete v naší dokumentaci k [rozhraní API.](api.md)

    ```json
        [
          {
            "id": "eventId-func-1",
            "eventType": "recordInserted",
            "subject": "myapp/vehicles/motorcycles",
            "eventTime": "2019-07-28T21:03:07+00:00",
            "dataVersion": "1.0",
            "data": {
              "make": "Ducati",
              "model": "Monster"
            }
          }
        ]
    ```
1. Spuštěním následujícího příkazu pro publikování události

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Ověřit doručení události

Událost dodanou na webu Azure Portal můžete zobrazit v části **Monitor** možnost vaší funkce.

## <a name="cleanup-resources"></a>Vyčištění prostředků

* Spuštěním následujícího příkazu odstraňte téma a všechna jeho předplatná

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

* Odstraňte funkci Azure vytvořenou na webu Azure Portal.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili téma mřížky událostí, předplatné a publikované události. Nyní, když znáte základní kroky, naleznete v následujících článcích:

* Problémy s používáním Azure Event Grid na IoT Edge najdete v [tématu Poradce při potížích](troubleshoot.md)s průvodcem .
* Vytvořit/aktualizovat odběr s [filtry](advanced-filtering.md).
* Nastavení trvalosti modulu Event Grid v [Linuxu](persist-state-linux.md) nebo [Windows](persist-state-windows.md)
* Postupujte [podle dokumentace](configure-client-auth.md) ke konfiguraci ověřování klienta
* Předávat události do Azure Event Grid v cloudu podle tohoto [kurzu](forward-events-event-grid-cloud.md)
* [Sledování témat a předplatných na okraji](monitor-topics-subscriptions.md)
