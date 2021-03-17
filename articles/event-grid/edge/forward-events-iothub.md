---
title: Dopředné události Event Grid IoTHub-Azure Event Grid IoT Edge | Microsoft Docs
description: Dopředné události Event Grid IoTHub
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 36dc7d098892fb2be7c2ba3d75de7c7adef1a4f1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171546"
---
# <a name="tutorial-forward-events-to-iothub"></a>Kurz: přeposílání událostí do IoTHub

Tento článek vás provede všemi kroky potřebnými k předání Event Grid událostí do jiných modulů IoT Edge, IoTHub pomocí tras. Můžete to chtít udělat z následujících důvodů:

* Nadále používat stávající investice, které už jsou v rámci směrování edgeHub
* Preferovat směrování všech událostí ze zařízení jenom pomocí IoT Hub

K dokončení tohoto kurzu potřebujete pochopit následující koncepty:

- [Event Grid koncepty](concepts.md)
- [Centrum IoT Edge](../../iot-edge/module-composition.md) 

## <a name="prerequisites"></a>Požadavky 
Aby bylo možné dokončit tento kurz, budete potřebovat:

* **Předplatné Azure** – Pokud ho ještě nemáte, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) . 
* **IoT Hub Azure a IoT Edge zařízení** – postupujte podle kroků v části rychlý Start pro [Linux](../../iot-edge/quickstart-linux.md) nebo [zařízení s Windows](../../iot-edge/quickstart.md) , pokud ho ještě nemáte.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-deploy-iot-edge.md)]

## <a name="create-topic"></a>Vytvořit téma

Jako vydavatel události je třeba vytvořit téma Event Grid. Téma odkazuje na koncový bod, do kterého mohou vydavatelé Odeslat události.

1. Vytvořte topic4.jss následujícím obsahem. Podrobnosti o datové části najdete v naší [dokumentaci k rozhraní API](api.md) .

   ```json
    {
          "name": "sampleTopic4",
          "properties": {
            "inputschema": "eventGridSchema"
          }
    }
    ```
1. Spuštěním následujícího příkazu vytvořte téma. Měl by se vrátit stavový kód HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```

1. Spusťte následující příkaz, který ověří úspěšné vytvoření tématu. Měl by se vrátit stavový kód HTTP 200 OK.

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

## <a name="create-event-subscription"></a>Vytvořit odběr události

Předplatitelé se můžou zaregistrovat pro události publikované v tématu. Aby bylo možné přijímat jakékoli události, musí vytvořit odběr služby Event Grid na téma, které vás zajímá.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Pomocí níže uvedeného obsahu vytvořte subscription4.js. Podrobnosti o datové části najdete v naší [dokumentaci k rozhraní API](api.md) .

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
   > `endpointType`Určuje, že je předplatitel `edgeHub` . `outputName`Určuje výstup, ve kterém bude modul Event Grid směrovat události, které odpovídají tomuto předplatnému edgeHub. Například události, které se shodují s výše uvedeným předplatným, budou zapsány do `/messages/modules/eventgridmodule/outputs/sampleSub4` .
2. Spuštěním následujícího příkazu vytvořte odběr. Měl by se vrátit stavový kód HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/eventSubscriptions/sampleSubscription4?api-version=2019-01-01-preview
    ```
3. Spusťte následující příkaz pro ověření, že se předplatné úspěšně vytvořilo. Měl by se vrátit stavový kód HTTP 200 OK.

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

## <a name="set-up-an-edge-hub-route"></a>Nastavení trasy hraničního centra

Aktualizujte trasu hraničního centra pro přeposílání událostí odběru událostí do IoTHub takto:

1. Přihlaste se k portálu [Azure Portal](https://ms.portal.azure.com).
1. Přejděte na **IoT Hub**.
1. V nabídce vyberte **IoT Edge**
1. V seznamu zařízení vyberte ID cílového zařízení.
1. Vyberte možnost **nastavit moduly**.
1. Vyberte možnost **Další** a do části trasy.
1. V části trasy přidejte novou trasu.

  ```sh
  "fromEventGridToIoTHub":"FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
  ```

  Příklad:

  ```json
  {
      "routes": {
        "fromEventGridToIoTHub": "FROM /messages/modules/eventgridmodule/outputs/sampleSub4 INTO $upstream"
      }
  }
  ```

   >[!NOTE]
   > Výše uvedená trasa přepošle všechny události, které se shodují s tímto předplatným, aby se předaly do služby IoT Hub. K dalšímu filtrování a směrování událostí Event Grid do jiných modulů IoT Edge můžete použít funkce [Směrování centra Edge](../../iot-edge/module-composition.md) .

## <a name="setup-iot-hub-route"></a>Nastavit IoT Hub trasu

V [kurzu IoT Hub směrování](../../iot-hub/tutorial-routing.md) můžete nastavit trasu ze služby IoT Hub, abyste mohli zobrazit události předané z modulu Event Grid. Použijte `true` pro dotaz, aby byl kurz jednoduchý.  



## <a name="publish-an-event"></a>Publikování události

1. Vytvořte event4.jss následujícím obsahem. Podrobnosti o datové části najdete v naší [dokumentaci k rozhraní API](api.md) .

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

1. Spusťte následující příkaz pro publikování události:

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event4.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Ověření doručení události

Postup zobrazení událostí najdete v [kurzu IoT Hub směrování](../../iot-hub/tutorial-routing.md) .

## <a name="cleanup-resources"></a>Vyčištění prostředků

* Spuštěním následujícího příkazu odstraňte téma a všechna jeho předplatná na hraničních zařízeních:

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic4?api-version=2019-01-01-preview
    ```
* V cloudu taky odstraňte všechny prostředky vytvořené při nastavování směrování IoTHub.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste vytvořili téma Event gridu, předplatné centra Edge a publikované události. Teď, když znáte základní kroky pro přeposílání do hraničního centra, si přečtěte následující články:

* Řešení potíží s používáním Azure Event Grid v IoT Edge najdete v tématu [Průvodce odstraňováním potíží](troubleshoot.md).
* Použití filtrů tras [hraničního centra](../../iot-edge/module-composition.md) k dělení událostí
* Nastavení trvalosti modulu Event Grid v systému [Linux](persist-state-linux.md) nebo [Windows](persist-state-windows.md)
* Podle [dokumentace](configure-client-auth.md) nakonfigurujte ověřování klientů.
* Předejte události do Azure Event Grid v cloudu pomocí tohoto [kurzu](forward-events-event-grid-cloud.md) .
* [Monitorování témat a odběrů na hraničních zařízeních](monitor-topics-subscriptions.md)