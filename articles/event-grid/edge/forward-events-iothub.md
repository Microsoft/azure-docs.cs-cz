---
title: Předávat události v síti událostí na IoTHub – Azure Event Grid IoT Edge | Dokumenty společnosti Microsoft
description: Události mřížky událostí přeposílání na IoTHub
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d0034810ff86de2a40e275ca54a2f0f9cbc856c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844696"
---
# <a name="tutorial-forward-events-to-iothub"></a>Kurz: Předávání událostí na IoTHub

Tento článek vás provede všechny kroky potřebné k předávání událostí event grid u jiných modulů IoT Edge, IoTHub pomocí tras. Můžete to chtít udělat z následujících důvodů:

* Nadále používat všechny stávající investice, které již existují, s směrováním edgeHub
* Upřednostňujte směrování všech událostí ze zařízení pouze přes Službu IoT Hub

Chcete-li dokončit tento kurz, musíte pochopit následující pojmy:

- [Koncepty mřížky událostí](concepts.md)
- [Centrum IoT Edge](../../iot-edge/module-composition.md) 

## <a name="prerequisites"></a>Požadavky 
Chcete-li dokončit tento výukový program, budete potřebovat:

* **Předplatné Azure** – vytvořte [bezplatný účet,](https://azure.microsoft.com/free) pokud ho ještě nemáte. 
* **Azure IoT Hub a zařízení IoT Edge** – postupujte podle pokynů v rychlém startu pro [Linux](../../iot-edge/quickstart-linux.md) nebo [windows zařízení,](../../iot-edge/quickstart.md) pokud ještě nemáte.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-topic"></a>Vytvořit téma

Jako vydavatel události je třeba vytvořit téma mřížky událostí. Téma odkazuje na koncový bod, do kterého mohou vydavatelé odesílat události.

1. Vytvořte topic4.json s následujícím obsahem. Podrobnosti o datové části najdete v naší dokumentaci k [rozhraní API.](api.md)

   ```json
    {
          "name": "sampleTopic4",
          "properties": {
            "inputschema": "eventGridSchema"
          }
    }
    ```
1. Chcete-li vytvořit téma, spusťte následující příkaz. Měl by být vrácen stavový kód HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

1. Spusťte následující příkaz k ověření, že téma bylo úspěšně vytvořeno. Měl by být vrácen stavový kód HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

   Ukázkový výstup:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4",
            "name": "sampleTopic4",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-event-subscription"></a>Vytvořit odběr událostí

Odběratelé se mohou zaregistrovat pro události publikované na téma. Chcete-li získat jakoukoli událost, budete muset vytvořit odběr mřížky událostí na téma, které by je zajímavé.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Vytvořte subscription4.json s níže uvedeným obsahem. Podrobnosti o datové části naleznete v naší dokumentaci k [rozhraní API.](api.md)

   ```json
    {
          "properties": {
                "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
    }
   ```

   >[!NOTE]
   > Určuje, `endpointType` že odběratel `edgeHub`je . Určuje `outputName` výstup, na kterém modul Event Grid bude směrovat události, které odpovídají tomuto předplatnému edgeHub. Například události, které odpovídají výše uvedené `/messages/modules/eventgridmodule/outputs/sampleSub4`předplatné budou zapsány do .
2. Chcete-li vytvořit odběr, spusťte následující příkaz. Měl by být vrácen stavový kód HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```
3. Spusťte následující příkaz k ověření, že odběr byl úspěšně vytvořen. Měl by být vrácen stavový kód HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```

    Ukázkový výstup:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic4/eventSubscriptions/sampleSubscription4",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription4",
          "properties": {
            "Topic": "sampleTopic4",
            "destination": {
                      "endpointType": "edgeHub",
                      "properties": {
                            "outputName": "sampleSub4"
                      }
                }
          }
        }
    ```

## <a name="set-up-an-edge-hub-route"></a>Nastavení trasy hraničního rozbočovače

Aktualizujte trasu edge hubu k předávání událostí předplatného událostí, které mají být předány do IoTHubu takto:

1. Přihlášení k [portálu Azure](https://ms.portal.azure.com)
1. Přejděte do **centra IoT Hub**.
1. Výběr **IoT Edge** z nabídky
1. Ze seznamu zařízení vyberte ID cílového zařízení.
1. Vyberte **možnost Nastavit moduly**.
1. Vyberte **Další** a do oddílu trasy.
1. Na trasách přidejte novou trasu

  ```sh
  "fromEventGridToIoTHub":"FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
  ```

  Například:

  ```json
  {
      "routes": {
        "fromEventGridToIoTHub": "FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
      }
  }
  ```

   >[!NOTE]
   > Výše uvedená trasa předá všechny události odpovídající tomuto předplatnému, které mají být předány do služby IoT hub. Pomocí funkcí [směrování rozbočovače Edge](../../iot-edge/module-composition.md) můžete dále filtrovat a směrovat události Event Grid do jiných modulů IoT Edge.

## <a name="setup-iot-hub-route"></a>Instalace trasy služby IoT Hub

V kurzu [směrování služby IoT Hub](../../iot-hub/tutorial-routing.md) najdete nastavení trasy z centra IoT, abyste mohli zobrazit události předávané z modulu Event Grid. Použijte `true` pro dotaz, aby výuka jednoduché.  



## <a name="publish-an-event"></a>Publikování události

1. Vytvořte event4.json s následujícím obsahem. Podrobnosti o datové části najdete v naší dokumentaci k [rozhraní API.](api.md)

    ```json
        [
          {
            "id": "eventId-iothub-1",
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

1. Chcete-li publikovat událost, spusťte následující příkaz:

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Ověřit doručení události

Postup zobrazení událostí najdete v [kurzu směrování služby](../../iot-hub/tutorial-routing.md) IoT Hub.

## <a name="cleanup-resources"></a>Vyčištění prostředků

* Chcete-li odstranit téma a všechna jeho předplatná na hraničních zařízeních, spusťte následující příkaz:

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```
* Odstraňte všechny prostředky vytvořené při nastavování směrování IoTHub v cloudu také.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili téma mřížky událostí, předplatné hraničního centra a publikované události. Teď, když znáte základní kroky k přeposílání okrajového centra, přečtěte si následující články:

* Problémy s používáním Azure Event Grid na IoT Edge najdete v [tématu Poradce při potížích](troubleshoot.md)s průvodcem .
* Použití filtrů směrování [rozbočovače](../../iot-edge/module-composition.md) okrajů k událostem oddílů
* Nastavení trvalosti modulu Event Grid v [Linuxu](persist-state-linux.md) nebo [Windows](persist-state-windows.md)
* Postupujte [podle dokumentace](configure-client-auth.md) ke konfiguraci ověřování klienta
* Předávat události do Azure Event Grid v cloudu podle tohoto [kurzu](forward-events-event-grid-cloud.md)
* [Sledování témat a předplatných na okraji](monitor-topics-subscriptions.md)