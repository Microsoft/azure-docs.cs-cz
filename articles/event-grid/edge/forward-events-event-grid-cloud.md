---
title: Události předsunení okrajů do cloudu Event Grid – Azure Event Grid IoT Edge | Dokumenty společnosti Microsoft
description: Události přeposílání okrajů do cloudu Event Grid
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 7184fb5c45ce41de2bd63b55fb67cbd9ba6361e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844713"
---
# <a name="tutorial-forward-events-to-event-grid-cloud"></a>Kurz: Předávání událostí do cloudu Event Grid

Tento článek vás provede všechny kroky potřebné k předávání událostí okraje do Event Grid v cloudu Azure. Můžete to chtít udělat z následujících důvodů:

* Reagujte na okrajové události v cloudu.
* Přesměrujte události do Event Grid v cloudu a použijte Azure Event Hubs nebo fronty Azure Storage k ukládání událostí do vyrovnávací paměti před jejich zpracováním v cloudu.

 K dokončení tohoto kurzu, musíte mít pochopení konceptů Event Grid na [edge](concepts.md) a [Azure](../concepts.md). Další typy cílů naleznete v tématu [obslužné rutiny událostí](event-handlers.md). 

## <a name="prerequisites"></a>Požadavky 
Chcete-li dokončit tento výukový program, budete potřebovat:

* **Předplatné Azure** – vytvořte [bezplatný účet,](https://azure.microsoft.com/free) pokud ho ještě nemáte. 
* **Azure IoT Hub a zařízení IoT Edge** – postupujte podle pokynů v rychlém startu pro [Linux](../../iot-edge/quickstart-linux.md) nebo [windows zařízení,](../../iot-edge/quickstart.md) pokud ještě nemáte.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)] 
## <a name="create-event-grid-topic-and-subscription-in-cloud"></a>Vytvoření tématu a předplatného mřížky událostí v cloudu

Vytvořte téma mřížky událostí a předplatné v cloudu podle [tohoto kurzu](../custom-event-quickstart-portal.md). Poznamenejte si `topicURL`, `sasKey`a `topicName` nově vytvořené téma, které budete používat později v kurzu.

Pokud jste například vytvořili `testegcloudtopic` téma s názvem V USA – západ, budou hodnoty vypadat podobně:

* **TopicUrl**:`https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events`
* **Název_ tématu**:`testegcloudtopic`
* **SasKey**: K dispozici v části **AccessKey** vašeho tématu. Použijte **klíč1**.

## <a name="create-event-grid-topic-at-the-edge"></a>Vytvořit téma mřížky událostí na okraji

1. Vytvořte topic3.json s následujícím obsahem. Podrobnosti o datové části najdete v naší dokumentaci k [rozhraní API.](api.md)

    ```json
        {
          "name": "sampleTopic3",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. Chcete-li vytvořit téma, spusťte následující příkaz. Měl by být vrácen stavový kód HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```
1. Spusťte následující příkaz k ověření, že téma bylo úspěšně vytvořeno. Měl by být vrácen stavový kód HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

   Ukázkový výstup:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3",
            "name": "sampleTopic3",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```
  
## <a name="create-event-grid-subscription-at-the-edge"></a>Vytvoření předplatného event gridu na hraničních zařízeních

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Vytvořte subscription3.json s následujícím obsahem. Podrobnosti o datové části najdete v naší dokumentaci k [rozhraní API.](api.md)

   ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                        "endpointUrl": "<your-event-grid-cloud-topic-endpoint-url>?api-version=2018-01-01",
                        "sasKey": "<your-event-grid-topic-saskey>",
                        "topicName": null
              }
            }
          }
    }
   ```

   >[!NOTE]
   > **EndpointUrl** určuje, že adresa URL tématu Event Grid v cloudu. **SasKey** odkazuje na klíč cloudu gridu event gridu. Hodnota v **topicName** bude použita k razítkování všech odchozích událostí do mřížky událostí. To může být užitečné při odesílání do tématu domény Mřížka událostí. Další informace o tématu domény Event Grid naleznete v [tématu Domény událostí](../event-domains.md)

    Například:
  
    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                 "sasKey": "<your-event-grid-topic-saskey>",
                 "topicName": null
              }
            }
          }
        }
    ```

2. Chcete-li vytvořit odběr, spusťte následující příkaz. Měl by být vrácen stavový kód HTTP 200 OK.

     ```sh
     curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
     ```

3. Spusťte následující příkaz k ověření, že odběr byl úspěšně vytvořen. Měl by být vrácen stavový kód HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
    ```

    Ukázkový výstup:

    ```json
         {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic3/eventSubscriptions/sampleSubscription3",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription3",
          "properties": {
            "Topic": "sampleTopic3",
            "destination": {
              "endpointType": "eventGrid",
              "properties": {
                "endpointUrl": "https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01",
                "sasKey": "<your-event-grid-topic-saskey>",
                "topicName": null
              }
            }
          }
        }
    ```

## <a name="publish-an-event-at-the-edge"></a>Publikování události na okraji

1. Vytvořte event3.json s následujícím obsahem. Podrobnosti o datové části najdete v [dokumentaci](api.md) k rozhraní API.

    ```json
        [
          {
            "id": "eventId-egcloud-0",
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

1. Spusťte následující příkaz:

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/events?api-version=2019-01-01-preview
    ```

## <a name="verify-edge-event-in-cloud"></a>Ověření události hraniční ho v cloudu

Informace o zobrazení událostí dodaných v tématu cloudu naleznete v [kurzu](../custom-event-quickstart-portal.md).

## <a name="cleanup-resources"></a>Vyčištění prostředků

* Spuštěním následujícího příkazu odstraňte téma a všechna jeho předplatná

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

* Odstraňte téma a předplatná vytvořená v cloudu (Azure Event Grid) také.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste publikovali událost na okraji a přeposlali do Event Grid v cloudu Azure. Teď, když znáte základní kroky k přeposílání do Event Grid v cloudu:

* Problémy s používáním Azure Event Grid na IoT Edge najdete v [tématu Poradce při potížích](troubleshoot.md)s průvodcem .
* Přeposílání událostí ioTHubu podle tohoto [kurzu](forward-events-iothub.md)
* Předávat události webhooku v cloudu podle tohoto [kurzu](pub-sub-events-webhook-cloud.md)
* [Sledování témat a předplatných na okraji](monitor-topics-subscriptions.md)
