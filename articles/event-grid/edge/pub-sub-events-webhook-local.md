---
title: Publikovat, přihlásit se k odběru událostí místně – Azure Event Grid IoT Edge | Dokumenty společnosti Microsoft
description: Publikovat, přihlásit se k odběru událostí místně pomocí Webhooku s event gridem na IoT Edge
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/29/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ba82b1bea4753cd51e275a78b248247032d79a01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280999"
---
# <a name="tutorial-publish-subscribe-to-events-locally"></a>Kurz: Publikovat, přihlásit se k odběru událostí místně

Tento článek vás provede všechny kroky potřebné k publikování a přihlásit se k odběru událostí pomocí Event Grid na IoT Edge.

> [!NOTE]
> Další informace o tématech a předplatných Služby Azure Event Grid najdete v [tématu Koncepty mřížky událostí](concepts.md).

## <a name="prerequisites"></a>Požadavky 
Chcete-li dokončit tento výukový program, budete potřebovat:

* **Předplatné Azure** – vytvořte [bezplatný účet,](https://azure.microsoft.com/free) pokud ho ještě nemáte. 
* **Azure IoT Hub a zařízení IoT Edge** – postupujte podle pokynů v rychlém startu pro [Linux](../../iot-edge/quickstart-linux.md) nebo [windows zařízení,](../../iot-edge/quickstart.md) pokud ještě nemáte.

## <a name="deploy-event-grid-iot-edge-module"></a>Nasadit modul IoT Edge sítě událostí

Existuje několik způsobů, jak nasadit moduly do zařízení IoT Edge a všechny z nich fungují pro Azure Event Grid na IoT Edge. Tento článek popisuje kroky nasazení Event Grid na IoT Edge z webu Azure Portal.

>[!NOTE]
> V tomto kurzu nasadíte modul Mřížka událostí bez trvalosti. To znamená, že všechna témata a odběry, které vytvoříte v tomto kurzu budou odstraněny při opětovném nasazení modulu. Další informace o nastavení trvalosti naleznete v následujících článcích: [Zachovat stav v Linuxu](persist-state-linux.md) nebo [Zachovat stav v systému Windows](persist-state-windows.md). Pro produkční úlohy doporučujeme nainstalovat modul Event Grid s trvalostí.


### <a name="select-your-iot-edge-device"></a>Výběr zařízení IoT Edge

1. Přihlášení k [portálu Azure](https://portal.azure.com)
1. Přejděte do svého iot hubu.
1. V nabídce v části **Automatická správa zařízení** vyberte **IoT Edge.** 
1. Klikněte na ID cílového zařízení ze seznamu zařízení
1. Vyberte **možnost Nastavit moduly**. Nechte stránku otevřenou. Budete pokračovat kroky v další části.

### <a name="configure-a-deployment-manifest"></a>Konfigurace manifestu nasazení

Manifest nasazení je dokument JSON, který popisuje, které moduly nasadit, jak toky dat mezi moduly a požadované vlastnosti dvojčat modulu. Portál Azure má průvodce, který vás provede vytvořením manifestu nasazení, namísto ručního sestavení dokumentu JSON.  Má tři kroky: **Přidat moduly**, **Zadat trasy**a **Zkontrolovat nasazení**.

### <a name="add-modules"></a>Přidat moduly

1. V části **Moduly nasazení** vyberte **Přidat**
1. Z typů modulů v rozevíracím seznamu vyberte **Modul IoT Edge**
1. Zadejte název, bitovou kopii, možnosti vytvoření kontejneru:

   * **Název**: eventgridmodule
   * **Identifikátor URI obrázku**:`mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Možnosti vytvoření kontejneru**:

   [!INCLUDE [event-grid-edge-module-version-update](../../../includes/event-grid-edge-module-version-update.md)]

    ```json
        {
          "Env": [
            "inbound__clientAuth__clientCert__enabled=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```    
 1. Klikněte na **Uložit.**
 1. Pokračujte k další části a před nasazením je přidáte modul odběratele mřížky událostí Azure.

    >[!IMPORTANT]
    > V tomto kurzu nasadíte modul Event Grid se zakázaným ověřováním klienta. Pro produkční úlohy doporučujeme povolit ověřování klienta. Další informace o bezpečné konfiguraci modulu Event Grid naleznete v [tématu Zabezpečení a ověřování](security-authentication.md).
    > 
    > Pokud používáte virtuální počítač Azure jako hraniční zařízení, přidejte pravidlo příchozího portu, které povolí příchozí provoz na portu 4438. Pokyny k přidání pravidla najdete v tématu [Jak otevřít porty k virtuálnímu počítači](../../virtual-machines/windows/nsg-quickstart-portal.md).
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>Nasadit modul IoT Edge odběratele sítě událostí

Tato část ukazuje, jak nasadit jiný modul IoT, který by fungovat jako obslužná rutina události, do kterého mohou být doručovány události.

### <a name="add-modules"></a>Přidat moduly

1. V části **Moduly nasazení** vyberte **Přidat** znovu. 
1. Z typů modulů v rozevíracím seznamu vyberte **Modul IoT Edge**
1. Zadejte možnosti vytvoření názvu, bitové kopie a kontejneru kontejneru:

   * **Jméno**: odběratel
   * **Identifikátor URI obrázku**:`mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **Možnosti vytvoření kontejneru:** Žádné
1. Klikněte na **Uložit.**
1. Chcete-li pokračovat v úseku trasy, klepněte na tlačítko **Další.**

 ### <a name="setup-routes"></a>Nastavení tras

Zachovat výchozí trasy a výběrem možnosti **Další** pokračovat do oddílu revize

### <a name="submit-the-deployment-request"></a>Odeslat žádost o nasazení

1. Část revize zobrazuje manifest nasazení JSON, který byl vytvořen na základě vašich výběrů v předchozí části. Potvrďte, že vidíte oba moduly: **eventgridmodule** a **odběratel** uvedené v JSON. 
1. Zkontrolujte informace o nasazení a vyberte **Odeslat**. Po odeslání nasazení se vrátíte na stránku **zařízení.**
1. V **části Moduly**ověřte, zda jsou uvedeny moduly **eventgrid** i **předplatitele.** A ověřte, zda **jsou sloupce Zadané v nasazení** a **Nahlášené zařízením** nastaveny na **ano**.

    Může trvat několik okamžiků, než se modul spustí na zařízení a pak se ohlásí zpět do ioT hubu. Aktualizujte stránku, abyste viděli aktualizovaný stav.

## <a name="create-a-topic"></a>Vytvoření tématu

Jako vydavatel události je třeba vytvořit téma mřížky událostí. Ve službě Azure Event Grid téma odkazuje na koncový bod, do kterého mohou vydavatelé odesílat události.

1. Vytvořte topic.json s následujícím obsahem. Podrobnosti o datové části naleznete v [naší dokumentaci k rozhraní API](api.md).

    ```json
        {
          "name": "sampleTopic1",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```

1. Chcete-li vytvořit téma mřížky událostí, spusťte následující příkaz. Zkontrolujte, zda se zobrazí `200 OK`stavový kód HTTP .

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

1. Spusťte následující příkaz k ověření, že téma bylo úspěšně vytvořeno. Měl by být vrácen stavový kód HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

   Ukázkový výstup:

   ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1",
            "name": "sampleTopic1",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
   ```

## <a name="create-an-event-subscription"></a>Vytvoření odběru událostí

Odběratelé se mohou zaregistrovat pro události publikované na téma. Chcete-li zobrazit jakoukoli událost, budete muset vytvořit předplatné služby Event Grid pro téma, které vás zajímá.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Vytvořte subscription.json s následujícím obsahem. Podrobnosti o datové části naleznete v naší [dokumentaci k rozhraní API](api.md)

    ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

    >[!NOTE]
    > Vlastnost **endpointType** určuje, že odběratel je **Webhook**.  **EndpointUrl** určuje adresu URL, na které odběratel naslouchá události. Tato adresa URL odpovídá ukázce odběratele Azure, kterou jste nasadili dříve.
2. Chcete-li vytvořit odběr pro toto téma, spusťte následující příkaz. Zkontrolujte, zda se zobrazí `200 OK`stavový kód HTTP .

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```
3. Spusťte následující příkaz k ověření, že odběr byl úspěšně vytvořen. Měl by být vrácen stavový kód HTTP 200 OK.

    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```

    Ukázkový výstup:

   ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/sampleTopic1/eventSubscriptions/sampleSubscription1",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription1",
          "properties": {
            "Topic": "sampleTopic1",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
    ```

## <a name="publish-an-event"></a>Publikování události

1. Vytvořte event.json s následujícím obsahem. Podrobnosti o datové části naleznete v [naší dokumentaci k rozhraní API](api.md).

    ```json
        [
          {
            "id": "eventId-func-0",
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
1. Chcete-li publikovat událost, spusťte následující příkaz.

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Ověřit doručení události

1. SSH nebo RDP do virtuálního počítače IoT Edge.
1. Zkontrolujte protokoly odběratele:

    V systému Windows spusťte následující příkaz:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine container logs subscriber
    ```

   Na Linuxu spusťte následující příkaz:

    ```sh
    sudo docker logs subscriber
    ```

    Ukázkový výstup:

    ```sh
        Received Event:
            {
              "id": "eventId-func-0",
              "topic": "sampleTopic1",
              "subject": "myapp/vehicles/motorcycles",
              "eventType": "recordInserted",
              "eventTime": "2019-07-28T21:03:07+00:00",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "make": "Ducati",
                "model": "Monster"
              }
            }
    ```

## <a name="cleanup-resources"></a>Vyčištění prostředků

* Spusťte následující příkaz k odstranění tématu a všech jeho odběrů.

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```
* Odstraňte modul odběratele ze zařízení IoT Edge.


## <a name="next-steps"></a>Další kroky
V tomto kurzu jste vytvořili téma mřížky událostí, předplatné a publikované události. Nyní, když znáte základní kroky, naleznete v následujících článcích: 

- Problémy s používáním Azure Event Grid na IoT Edge najdete v [tématu Poradce při potížích](troubleshoot.md)s průvodcem .
- Vytvořit/aktualizovat odběr s [filtry](advanced-filtering.md).
- Povolení trvalosti modulu Event Grid v [Linuxu](persist-state-linux.md) nebo [Windows](persist-state-windows.md)
- Postupujte [podle dokumentace](configure-client-auth.md) ke konfiguraci ověřování klienta
- Předávat události do funkce Azure v cloudu podle tohoto [kurzu](pub-sub-events-webhook-cloud.md)
- [Reakce na události úložiště objektů Blob na IoT Edge](react-blob-storage-events-locally.md)
- [Sledování témat a předplatných na okraji](monitor-topics-subscriptions.md)

