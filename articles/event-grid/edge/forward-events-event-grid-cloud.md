---
title: Události předních okrajů pro Event Grid Azure Event Grid cloudu IoT Edge | Microsoft Docs
description: Dopředné události Edge do cloudu Event Grid
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: bfe150a45c70bc5bed18f8e929c9567905cd38f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171597"
---
# <a name="tutorial-forward-events-to-event-grid-cloud"></a>Kurz: přeposílání událostí do cloudu Event Grid

Tento článek vás provede všemi kroky potřebnými k předání hraničních událostí Event Grid v cloudu Azure. Můžete to chtít udělat z následujících důvodů:

* Reagují na hraniční události v cloudu.
* Předejte události do Event Grid v cloudu a použijte Azure Event Hubs nebo Azure Storage fronty k ukládání událostí do vyrovnávací paměti před jejich zpracováním v cloudu.

 K dokončení tohoto kurzu potřebujete rozumět konceptům Event Grid na [Edge](concepts.md) a v [Azure](../concepts.md). Další cílové typy naleznete v tématu [obslužné rutiny událostí](event-handlers.md). 

## <a name="prerequisites"></a>Požadavky 
Aby bylo možné dokončit tento kurz, budete potřebovat:

* **Předplatné Azure** – Pokud ho ještě nemáte, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) . 
* **IoT Hub Azure a IoT Edge zařízení** – postupujte podle kroků v části rychlý Start pro [Linux](../../iot-edge/quickstart-linux.md) nebo [zařízení s Windows](../../iot-edge/quickstart.md) , pokud ho ještě nemáte.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)] 
## <a name="create-event-grid-topic-and-subscription-in-cloud"></a>Vytvoření tématu a odběru Event gridu v cloudu

Pomocí [tohoto kurzu](../custom-event-quickstart-portal.md)vytvořte v cloudu téma a odběr služby Event Grid. Poznamenejte `topicURL` si `sasKey` `topicName` z nově vytvořeného tématu, které použijete později v tomto kurzu.

Například pokud jste vytvořili téma s názvem `testegcloudtopic` v západní USA, budou hodnoty vypadat přibližně takto:

* **TopicUrl**: `https://testegcloudtopic.westus2-1.eventgrid.azure.net/api/events`
* **Téma**: `testegcloudtopic`
* **SasKey**: k dispozici v rámci **AccessKey** vašeho tématu. Použijte **klíč1**.

## <a name="create-event-grid-topic-at-the-edge"></a>Vytvoření tématu Event gridu na hraničních zařízeních

1. Vytvořte topic3.jss následujícím obsahem. Podrobnosti o datové části najdete v naší [dokumentaci k rozhraní API](api.md) .

    ```json
        {
          "name": "sampleTopic3",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```
1. Spuštěním následujícího příkazu vytvořte téma. Měl by se vrátit stavový kód HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```
1. Spusťte následující příkaz, který ověří úspěšné vytvoření tématu. Měl by se vrátit stavový kód HTTP 200 OK.

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
  
## <a name="create-event-grid-subscription-at-the-edge"></a>Vytvoření předplatného Event Grid na hraničních zařízeních

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Vytvořte subscription3.jss následujícím obsahem. Podrobnosti o datové části najdete v naší [dokumentaci k rozhraní API](api.md) .

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
   > **EndpointUrl** určuje, že adresa URL tématu Event Grid v cloudu. **SasKey** odkazuje na klíč tématu Event Grid cloudu. Hodnota v **části** prokládá se použije k označení všech odchozích událostí, které se mají Event Grid. To může být užitečné při odeslání do tématu Event Grid domény. Další informace o Event Grid doméně najdete v tématu věnovaném [doménám událostí](../event-domains.md) .

    Třeba
  
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

2. Spuštěním následujícího příkazu vytvořte odběr. Měl by se vrátit stavový kód HTTP 200 OK.

     ```sh
     curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription3.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3/eventSubscriptions/sampleSubscription3?api-version=2019-01-01-preview
     ```

3. Spusťte následující příkaz pro ověření, že se předplatné úspěšně vytvořilo. Měl by se vrátit stavový kód HTTP 200 OK.

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

## <a name="publish-an-event-at-the-edge"></a>Publikování události na hraničních zařízeních

1. Vytvořte event3.jss následujícím obsahem. Podrobnosti o datové části najdete v [dokumentaci k rozhraní API](api.md) .

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

## <a name="verify-edge-event-in-cloud"></a>Ověřit událost Edge v cloudu

Informace o zobrazení událostí doručených v tématu o cloudu najdete v tomto [kurzu](../custom-event-quickstart-portal.md).

## <a name="cleanup-resources"></a>Vyčištění prostředků

* Spuštěním následujícího příkazu odstraňte téma a všechny jeho odběry.

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic3?api-version=2019-01-01-preview
    ```

* Odstraňte také téma a předplatná vytvořená v cloudu (Azure Event Grid).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste publikovali událost na hranici a předáváte ji Event Grid v cloudu Azure. Teď, když znáte základní kroky pro přeposílání do Event Grid v cloudu:

* Řešení potíží s používáním Azure Event Grid v IoT Edge najdete v tématu [Průvodce odstraňováním potíží](troubleshoot.md).
* Předejte události do IoTHub pomocí tohoto [kurzu](forward-events-iothub.md) .
* Předejte události do Webhooku v cloudu pomocí tohoto [kurzu](pub-sub-events-webhook-cloud.md) .
* [Monitorování témat a odběrů na hraničních zařízeních](monitor-topics-subscriptions.md)
