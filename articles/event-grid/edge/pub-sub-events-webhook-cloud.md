---
title: Publikování, přihlášení k odběru událostí v cloudu – Azure Event Grid IoT Edge | Microsoft Docs
description: Publikování, přihlášení k odběru událostí v cloudu pomocí Webhooku s Event Grid v IoT Edge
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: ec7ecb77d37ed1cdf1d13aa7191f5d50e0008c20
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98790795"
---
# <a name="tutorial-publish-subscribe-to-events-in-cloud"></a>Kurz: publikování, přihlášení k odběru událostí v cloudu

Tento článek vás provede všemi kroky potřebnými k publikování a přihlášení k odběru událostí pomocí Event Grid v IoT Edge. Tento kurz používá a funguje jako obslužná rutina události Azure Functions. Další cílové typy naleznete v tématu [obslužné rutiny událostí](event-handlers.md).

Další informace najdete v tématu [Event Grid koncepty](concepts.md) , které vám pomohou pochopit, co je téma a předplatné služby Event Grid.

## <a name="prerequisites"></a>Předpoklady 
Aby bylo možné dokončit tento kurz, budete potřebovat:

* **Předplatné Azure** – Pokud ho ještě nemáte, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) . 
* **IoT Hub Azure a IoT Edge zařízení** – postupujte podle kroků v části rychlý Start pro [Linux](../../iot-edge/quickstart-linux.md) nebo [zařízení s Windows](../../iot-edge/quickstart.md) , pokud ho ještě nemáte.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-an-azure-function-in-the-azure-portal"></a>Vytvoření funkce Azure Functions v Azure Portal

Pomocí kroků popsaných v tomto [kurzu](../../azure-functions/functions-get-started.md) vytvořte funkci Azure Functions. 

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

V nové funkci vyberte v pravém horním rohu **získat adresu URL funkce** , vyberte výchozí (**klíč funkce**) a pak vyberte **Kopírovat**. Později v tomto kurzu použijete hodnotu adresy URL funkce.

> [!NOTE]
> Další ukázky a kurzy týkající se reakce na události pomocí triggerů událostí EventGrid najdete v dokumentaci k [Azure Functions](../../azure-functions/functions-overview.md) .

## <a name="create-a-topic"></a>Vytvoření tématu

Jako vydavatel události je třeba vytvořit téma Event Grid. Téma odkazuje na koncový bod, do kterého mohou vydavatelé odesílat události.

1. Vytvořte topic2.jss následujícím obsahem. Podrobnosti o datové části najdete v naší [dokumentaci k rozhraní API](api.md) .

    ```json
         {
          "name": "sampleTopic2",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. Spuštěním následujícího příkazu vytvořte téma. Měl by se vrátit stavový kód HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```
1. Spusťte následující příkaz, který ověří úspěšné vytvoření tématu. Měl by se vrátit stavový kód HTTP 200 OK.

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

Předplatitelé se můžou zaregistrovat pro události publikované v tématu. Předplatitelé budou muset při přijímání jakékoli události vytvořit odběr služby Event Grid, a to v tématu o zájmu.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Vytvořte subscription2.jss následujícím obsahem. Podrobnosti o datové části najdete v naší [dokumentaci k rozhraní API](api.md) .

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
   > **EndpointType** určuje, že předplatitel je Webhook.  **EndpointUrl** Určuje adresu URL, na které předplatitel naslouchá pro události. Tato adresa URL odpovídá ukázce funkce Azure, kterou jste nastavili dříve.
2. Spuštěním následujícího příkazu vytvořte odběr. Měl by se vrátit stavový kód HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/eventSubscriptions/sampleSubscription2?api-version=2019-01-01-preview
    ```
3. Spusťte následující příkaz pro ověření, že se předplatné úspěšně vytvořilo. Měl by se vrátit stavový kód HTTP 200 OK.

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

1. Vytvořte event2.jss následujícím obsahem. Podrobnosti o datové části najdete v naší [dokumentaci k rozhraní API](api.md) .

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
1. Spusťte následující příkaz pro publikování události.

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event2.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Ověření doručení události

Událost Doručená v Azure Portal můžete zobrazit pod možností **monitorování** funkce.

## <a name="cleanup-resources"></a>Vyčištění prostředků

* Spuštěním následujícího příkazu odstraňte téma a všechny jeho odběry.

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic2?api-version=2019-01-01-preview
    ```

* Odstraňte funkci Azure vytvořenou v Azure Portal.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili téma Event gridu, předplatné a publikované události. Teď, když znáte základní postup, najdete v následujících článcích:

* Řešení potíží s používáním Azure Event Grid v IoT Edge najdete v tématu [Průvodce odstraňováním potíží](troubleshoot.md).
* Vytvoří nebo aktualizuje předplatné s [filtry](advanced-filtering.md).
* Nastavení trvalosti modulu Event Grid v systému [Linux](persist-state-linux.md) nebo [Windows](persist-state-windows.md)
* Podle [dokumentace](configure-client-auth.md) nakonfigurujte ověřování klientů.
* Předejte události do Azure Event Grid v cloudu pomocí tohoto [kurzu](forward-events-event-grid-cloud.md) .
* [Monitorování témat a odběrů na hraničních zařízeních](monitor-topics-subscriptions.md)