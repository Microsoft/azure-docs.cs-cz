---
title: Zprávy z cloudu na zařízení pomocí služby Azure IoT Hub (Python) | Microsoft Docs
description: Postup posílání zpráv z cloudu na zařízení ze služby Azure IoT Hub pomocí sad SDK Azure IoT pro Python. Aplikaci simulovaného zařízení upravíte tak, aby přijímala zprávy z cloudu na zařízení a upravila back-end aplikaci pro posílání zpráv z cloudu na zařízení.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: 4cda59448856630468076ef63c51b8a216a31bd0
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001945"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Posílání zpráv z cloudu na zařízení pomocí IoT Hub (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub je plně spravovaná služba, která pomáhá zajistit spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení a back-endu řešení. V rychlém startu [pro odeslání ze zařízení do služby IoT Hub](quickstart-send-telemetry-python.md) se dozvíte, jak vytvořit centrum IoT, zřídit v něm identitu zařízení a zakódovat aplikaci simulovaného zařízení, která odesílá zprávy typu zařízení-Cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Tento kurz sestaví na [odeslání telemetrie ze zařízení do služby IoT Hub](quickstart-send-telemetry-python.md). Ukazuje, jak:

* Z back-endu vašeho řešení odesílají zprávy typu cloud-zařízení do jediného zařízení prostřednictvím IoT Hub.

* Příjem zpráv typu cloud-zařízení na zařízení.

* Z back-endu vašeho řešení požadavek na doručení zprávodesílaných do zařízení z IoT Hub.

Další informace o zprávách z cloudu na zařízení najdete v [příručce pro vývojáře IoT Hub](iot-hub-devguide-messaging.md).

Na konci tohoto kurzu spustíte dvě konzolové aplikace Pythonu:

* **SimulatedDevice.py**upravenou verzi aplikace vytvořenou v části [odeslání telemetrie ze zařízení do služby IoT Hub](quickstart-send-telemetry-python.md), která se připojí ke službě IoT Hub a přijímá zprávy typu cloud-zařízení.

* **SendCloudToDeviceMessage.py**, která pošle zprávu typu cloud-zařízení do aplikace simulovaného zařízení prostřednictvím IoT Hub a potom obdrží potvrzení o doručení.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="receive-messages-in-the-simulated-device-app"></a>Přijímání zpráv v aplikaci simulovaného zařízení

V této části vytvoříte konzolovou aplikaci v Pythonu pro simulaci zařízení a příjem zpráv z cloudu na zařízení ze služby IoT Hub.

1. Pomocí textového editoru vytvořte soubor **SimulatedDevice.py** .

2. Na začátek souboru `import` **SimulatedDevice.py** přidejte následující příkazy a proměnné:

   ```python
    import threading
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

3. Do souboru **SimulatedDevice.py** přidejte následující kód. Nahraďte zástupný symbol {deviceConnectionString} připojovacím řetězcem zařízení pro zařízení, které jste vytvořili v části [poslat telemetrii ze zařízení, do rychlého startu služby IoT Hub](quickstart-send-telemetry-python.md) :

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Přidejte následující funkci, která vytiskne přijaté zprávy do konzoly:

    ```python
    def message_listener(client):
        global RECEIVED_MESSAGES
        while True:
            message = client.receive_message()
            RECEIVED_MESSAGES += 1
            print("Message received")
            print( "    Data: <<{}>>".format(message.data) )
            print( "    Properties: {}".format(message.custom_properties))
            print( "    Total calls received: {}".format(RECEIVED_MESSAGES))
    ```

5. Přidejte následující kód pro inicializaci klienta a počkejte na přijetí zprávy z cloudu na zařízení:

    ```python
    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            message_listener_thread = threading.Thread(target=message_listener, args=(client,))
            message_listener_thread.daemon = True
            message_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )
    ```

6. Přidejte následující hlavní funkci:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Uložte a zavřete soubor **SimulatedDevice.py** .

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT Hub

V tomto článku vytvoříte back-end službu pro posílání zpráv z cloudu na zařízení prostřednictvím služby IoT Hub, kterou jste vytvořili v tématu [odeslání telemetrie ze zařízení do služby IoT Hub](quickstart-send-telemetry-python.md). K posílání zpráv z cloudu na zařízení potřebuje služba oprávnění **připojit** se. Ve výchozím nastavení se všechny IoT Hub vytvoří pomocí zásad sdíleného přístupu s názvem **Služba** , která toto oprávnění udělí.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Odeslání zprávy typu cloud-zařízení

V této části vytvoříte konzolovou aplikaci v Pythonu, která posílá zprávy typu cloud-zařízení do aplikace simulovaného zařízení. Budete potřebovat ID zařízení, které jste přidali v části [odeslání telemetrie ze zařízení do rychlého startu služby IoT Hub](quickstart-send-telemetry-python.md) . Také potřebujete připojovací řetězec centra IoT, který jste zkopírovali dříve, v [části získání připojovacího řetězce centra IoT Hub](#get-the-iot-hub-connection-string).

1. Pomocí textového editoru vytvořte soubor **SendCloudToDeviceMessage.py** .

2. Na začátek souboru `import` **SendCloudToDeviceMessage.py** přidejte následující příkazy a proměnné:

    ```python
    import random
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubMessaging, IoTHubMessage, IoTHubError

    OPEN_CONTEXT = 0
    FEEDBACK_CONTEXT = 1
    MESSAGE_COUNT = 1
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

3. Do souboru **SendCloudToDeviceMessage.py** přidejte následující kód. Nahraďte zástupné hodnoty {IoT Hub Connection String} a {ID zařízení} připojovacím řetězcem centra IoT a ID zařízení, které jste si poznamenali dříve:

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

4. Přidejte následující funkci pro tisk zpráv zpětné vazby do konzoly:

    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

5. Přidejte následující kód, který odešle zprávu do zařízení a zpracuje zprávu o zpětné vazbě, když zařízení potvrdí zprávu typu cloud-zařízení:

    ```python
    def iothub_messaging_sample_run():
        try:
            iothub_messaging = IoTHubMessaging(CONNECTION_STRING)

            iothub_messaging.open(open_complete_callback, OPEN_CONTEXT)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))

                # optional: assign ids
                message.message_id = "message_%d" % i
                message.correlation_id = "correlation_%d" % i
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % i
                prop_map.add("Property", prop_text)

                iothub_messaging.send_async(DEVICE_ID, message, send_complete_callback, i)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

            iothub_messaging.close()

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubMessaging sample stopped" )
    ```

6. Přidejte následující hlavní funkci:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client Messaging Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_messaging_sample_run()
    ```

7. Uložte a zavřete soubor **SendCloudToDeviceMessage.py** .

## <a name="run-the-applications"></a>Spuštění aplikací

Nyní můžete spustit aplikace.

1. Otevřete příkazový řádek a nainstalujte **sadu SDK pro zařízení Azure IoT Hub pro Python**.

    ```shell
    pip install azure-iothub-device-client
    ```

2. Na příkazovém řádku spusťte následující příkaz pro příjem zpráv z cloudu na zařízení:

    ```shell
    python SimulatedDevice.py
    ```

    ![Spuštění aplikace simulovaného zařízení](./media/iot-hub-python-python-c2d/simulated-device.png)

3. Otevřete nový příkazový řádek a nainstalujte **sadu Azure IoT Hub Service SDK pro Python**.

    ```shell
    pip install azure-iothub-service-client
    ```

4. Na příkazovém řádku spusťte následující příkaz pro odeslání zprávy typu cloud-zařízení a počkejte na zpětnou vazbu zprávy:

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![Spuštění aplikace pro odeslání příkazu Cloud-zařízení](./media/iot-hub-python-python-c2d/send-command.png)

5. Poznamenejte si zprávu, kterou zařízení přijalo.

    ![Přijatá zpráva](./media/iot-hub-python-python-c2d/message-received.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak odesílat a přijímat zprávy z cloudu do zařízení.

Příklady kompletních řešení, která používají IoT Hub, najdete v tématu [akcelerátor řešení vzdáleného monitorování Azure IoT](https://azure.microsoft.com/documentation/suites/iot-suite/).

Další informace o vývoji řešení pomocí IoT Hub najdete v příručce pro [vývojáře IoT Hub](iot-hub-devguide.md).
