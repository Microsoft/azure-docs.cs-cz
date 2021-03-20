---
title: Publikování, přihlášení k odběru událostí lokálně Azure Event Grid IoT Edge | Microsoft Docs
description: Publikování, přihlášení k odběru událostí místně pomocí Webhooku s Event Grid v IoT Edge
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 2a7cc864366bd9a35c96dd453c0dc68f77d8abd9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171444"
---
# <a name="tutorial-publish-subscribe-to-events-locally"></a>Kurz: publikování, přihlášení k odběru událostí v místním prostředí

Tento článek vás provede všemi kroky potřebnými k publikování a přihlášení k odběru událostí pomocí Event Grid v IoT Edge.

> [!NOTE]
> Další informace o Azure Event Grid tématech a předplatných najdete v tématu [Event Grid koncepty](concepts.md).

## <a name="prerequisites"></a>Předpoklady 
Aby bylo možné dokončit tento kurz, budete potřebovat:

* **Předplatné Azure** – Pokud ho ještě nemáte, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) . 
* **Zařízení Azure IoT Hub a IoT Edge** – postupujte podle kroků v rychlém startu pro zařízení se systémem [Linux](../../iot-edge/quickstart-linux.md) nebo [Windows](../../iot-edge/quickstart.md) , pokud ho ještě nemáte.

## <a name="deploy-event-grid-iot-edge-module"></a>Nasadit modul Event Grid IoT Edge

Existuje několik způsobů, jak nasadit moduly do zařízení IoT Edge, a všechny z nich fungují pro Azure Event Grid na IoT Edge. Tento článek popisuje postup nasazení Event Grid IoT Edge z Azure Portal.

>[!NOTE]
> V tomto kurzu nasadíte modul Event Grid bez trvalosti. To znamená, že všechna témata a odběry, které v tomto kurzu vytvoříte, budou při opětovném nasazení modulu odstraněny. Další informace o tom, jak nastavit trvalost, najdete v následujících článcích: [trvalé uložení stavu v systému Linux](persist-state-linux.md) nebo [trvalého stavu ve Windows](persist-state-windows.md). Pro produkční úlohy doporučujeme nainstalovat modul Event Grid s persistencí.


### <a name="select-your-iot-edge-device"></a>Vyberte zařízení IoT Edge

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
1. Přejděte na IoT Hub.
1. V nabídce v části **Automatická správa zařízení** vyberte **IoT Edge** . 
1. V seznamu zařízení klikněte na ID cílového zařízení.
1. Vyberte možnost **nastavit moduly**. Nechejte stránku otevřenou. Budete pokračovat kroky v další části.

### <a name="configure-a-deployment-manifest"></a>Konfigurace manifestu nasazení

Manifest nasazení je dokument JSON, který popisuje, které moduly se mají nasadit, způsob, jakým jsou toky dat mezi moduly a požadované vlastnosti v modulu vlákna. Azure Portal má průvodce, který vás provede vytvořením manifestu nasazení místo ručního vytváření dokumentu JSON.  Má tři kroky: **přidat moduly**, **zadat trasy** a **zkontrolovat nasazení**.

### <a name="add-modules"></a>Přidat moduly

1. V části **moduly nasazení** vyberte **Přidat** .
1. Z typů modulů v rozevíracím seznamu vyberte **IoT Edge modul** .
1. Zadejte název, obrázek a možnosti vytvoření kontejneru kontejneru:

   * **Název**: eventgridmodule
   * **Identifikátor URI image**: `mcr.microsoft.com/azure-event-grid/iotedge:latest`
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
 1. Klikněte na **Uložit**.
 1. Přejděte k další části a přidejte modul předplatitele Azure Event Grid před jejich nasazením dohromady.

    >[!IMPORTANT]
    > V tomto kurzu nasadíte modul Event Grid s povoleným ověřováním klienta. Pro produkční úlohy doporučujeme povolit ověřování klientů. Další informace o tom, jak bezpečně nakonfigurovat Event Grid modul, najdete v tématu [zabezpečení a ověřování](security-authentication.md).
    > 
    > Pokud jako hraniční zařízení používáte virtuální počítač Azure, přidejte pravidlo příchozího portu, které povolí příchozí provoz na portu 4438. Pokyny k přidání pravidla najdete v tématu [Postup otevření portů pro virtuální počítač](../../virtual-machines/windows/nsg-quickstart-portal.md).
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>Nasazení IoT Edge modulu předplatitele Event Grid

V této části se dozvíte, jak nasadit jiný modul IoT, který by sloužil jako obslužná rutina události, do které se můžou události doručovat.

### <a name="add-modules"></a>Přidat moduly

1. V části **moduly nasazení** vyberte **Přidat** znovu. 
1. Z typů modulů v rozevíracím seznamu vyberte **IoT Edge modul** .
1. Zadejte název, obrázek a možnosti vytvoření kontejneru kontejneru:

   * **Název**: odběratel
   * **Identifikátor URI image**: `mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **Možnosti vytvoření kontejneru**: žádné
1. Klikněte na **Uložit**.
1. Kliknutím na tlačítko **Další** pokračujte v části trasy.

 ### <a name="setup-routes"></a>Nastavení tras

Ponechte výchozí trasy a vyberte **Další** , abyste pokračovali v části Kontrola.

### <a name="submit-the-deployment-request"></a>Odeslat žádost o nasazení

1. V části Kontrola se dozvíte, který manifest nasazení JSON byl vytvořen na základě vašich výběrů v předchozí části. Ověřte, že se zobrazují oba moduly: **eventgridmodule** a **předplatitelé** uvedené ve formátu JSON. 
1. Zkontrolujte informace o svém nasazení a pak vyberte **Odeslat**. Po odeslání nasazení se vrátíte na stránku **zařízení** .
1. V **části moduly** ověřte, že jsou uvedené moduly **eventgrid** a **předplatitelé** . A ověřte, zda je **zadaná hodnota ve sloupci nasazení** a **hlášená pomocí sloupce zařízení** nastavena na **hodnotu Ano**.

    Může chvíli trvat, než se modul na zařízení spustí a pak se znovu oznámí IoT Hub. Aktualizujte stránku, aby se zobrazil aktualizovaný stav.

## <a name="create-a-topic"></a>Vytvoření tématu

Jako vydavatel události je třeba vytvořit téma Event Grid. V Azure Event Grid téma odkazuje na koncový bod, do kterého mohou vydavatelé odesílat události.

1. Vytvořte topic.jss následujícím obsahem. Podrobnosti o datové části najdete v naší [dokumentaci k rozhraní API](api.md).

    ```json
        {
          "name": "sampleTopic1",
          "properties": {
            "inputschema": "eventGridSchema"
          }
        }
    ```

1. Spuštěním následujícího příkazu vytvořte téma Event Grid. Ověřte, že se zobrazí stavový kód HTTP `200 OK` .

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @topic.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```

1. Spusťte následující příkaz, který ověří úspěšné vytvoření tématu. Měl by se vrátit stavový kód HTTP 200 OK.

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

Předplatitelé se můžou zaregistrovat pro události publikované v tématu. Pokud chcete přijímat jakékoli události, budete muset vytvořit předplatné Event Grid pro téma zájmu.

[!INCLUDE [event-grid-deploy-iot-edge](../../../includes/event-grid-edge-persist-event-subscriptions.md)]

1. Vytvořte subscription.jss následujícím obsahem. Podrobnosti o datové části najdete v naší [dokumentaci k rozhraní API](api.md) .

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
    > Vlastnost **endpointType** určuje, že předplatitel je **Webhook**.  **EndpointUrl** Určuje adresu URL, na které předplatitel naslouchá pro události. Tato adresa URL odpovídá ukázce Azure předplatitele, kterou jste předtím nasadili.
2. Spuštěním následujícího příkazu vytvořte odběr pro téma. Ověřte, že se zobrazí stavový kód HTTP `200 OK` .

    ```sh
    curl -k -H "Content-Type: application/json" -X PUT -g -d @subscription.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/eventSubscriptions/sampleSubscription1?api-version=2019-01-01-preview
    ```
3. Spusťte následující příkaz pro ověření, že se předplatné úspěšně vytvořilo. Měl by se vrátit stavový kód HTTP 200 OK.

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

1. Vytvořte event.jss následujícím obsahem. Podrobnosti o datové části najdete v naší [dokumentaci k rozhraní API](api.md).

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
1. Spusťte následující příkaz, který publikuje událost.

    ```sh
    curl -k -H "Content-Type: application/json" -X POST -g -d @event.json https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1/events?api-version=2019-01-01-preview
    ```

## <a name="verify-event-delivery"></a>Ověření doručení události

1. SSH nebo RDP do virtuálního počítače s IoT Edge.
1. Podívejte se na protokoly předplatitele:

    V systému Windows spusťte následující příkaz:

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine container logs subscriber
    ```

   V systému Linux spusťte následující příkaz:

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

* Spuštěním následujícího příkazu odstraňte téma a všechny jeho odběry.

    ```sh
    curl -k -H "Content-Type: application/json" -X DELETE https://<your-edge-device-public-ip-here>:4438/topics/sampleTopic1?api-version=2019-01-01-preview
    ```
* Odstraňte ze zařízení IoT Edge modul předplatitele.


## <a name="next-steps"></a>Další kroky
V tomto kurzu jste vytvořili téma Event gridu, předplatné a publikované události. Teď, když znáte základní postup, najdete v následujících článcích: 

- Řešení potíží s používáním Azure Event Grid v IoT Edge najdete v tématu [Průvodce odstraňováním potíží](troubleshoot.md).
- Vytvoří nebo aktualizuje předplatné s [filtry](advanced-filtering.md).
- Povolení trvalosti modulu Event Grid v systému [Linux](persist-state-linux.md) nebo [Windows](persist-state-windows.md)
- Podle [dokumentace](configure-client-auth.md) nakonfigurujte ověřování klientů.
- Předejte události do Azure Functions v cloudu pomocí tohoto [kurzu](pub-sub-events-webhook-cloud.md) .
- [Reakce na události Blob Storage v IoT Edge](react-blob-storage-events-locally.md)
- [Monitorování témat a odběrů na hraničních zařízeních](monitor-topics-subscriptions.md)

