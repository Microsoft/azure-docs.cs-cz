---
title: Zprávy z cloudu na zařízení s Azure IoT Hub (Python) | Dokumenty společnosti Microsoft
description: Jak odesílat zprávy z cloudu na zařízení do zařízení z centra Azure IoT pomocí sad Azure IoT SDK pro Python. Můžete upravit simulované zařízení aplikace přijímat zprávy z cloudu na zařízení a upravit back-end aplikace pro odesílání zpráv z cloudu na zařízení.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: robinsh
ms.openlocfilehash: 3613062cf8765a4aec3327b660bb5818898f2dd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110431"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Odesílání zpráv z cloudu na zařízení pomocí služby IoT Hub (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub je plně spravovaná služba, která pomáhá umožnit spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení a back-endem řešení. [Odeslat telemetrii ze zařízení do služby IoT hub](quickstart-send-telemetry-python.md) rychlý start ukazuje, jak vytvořit službu IoT hub, zřízení identity zařízení v něm a kód simulované zařízení aplikace, která odesílá zprávy zařízení cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Tento kurz staví na [odesílání telemetrie ze zařízení do služby IoT hub](quickstart-send-telemetry-python.md). To vám ukáže, jak:

* Z back-endu vašeho řešení můžete odesílat zprávy z cloudu na zařízení do jednoho zařízení prostřednictvím služby IoT Hub.

* Příjem zpráv z cloudu na zařízení na zařízení.

* Z back-endu vašeho řešení požádejte o potvrzení doručení *(zpětnou vazbu)* pro zprávy odeslané do zařízení ze služby IoT Hub.

Další informace o zprávách mezi cloudy najdete v [průvodci vývojáři služby IoT Hub](iot-hub-devguide-messaging.md).

Na konci tohoto kurzu spustíte dvě konzolové aplikace Pythonu:

* **SimulatedDevice.py**, upravená verze aplikace vytvořená v [aplikaci Send telemetrie ze zařízení do služby IoT hub](quickstart-send-telemetry-python.md), která se připojuje k centru IoT hub a přijímá zprávy z cloudu na zařízení.

* **SendCloudToDeviceMessage.py**, který odešle zprávu z cloudu na zařízení do aplikace simulované zařízení prostřednictvím služby IoT Hub a pak obdrží potvrzení o doručení.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

* Zkontrolujte, zda je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokován v některých prostředích podnikové a vzdělávací sítě. Další informace a způsoby, jak tento problém vyřešit, najdete [v tématu připojení k centru IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Příjem zpráv v aplikaci pro simulované zařízení

V této části vytvoříte konzolovou aplikaci Pythonu, která simuluje zařízení a přijímá zprávy z cloudu na zařízení z centra IoT Hub.

1. Pomocí textového editoru vytvořte **soubor SimulatedDevice.py.**

2. Na začátek **SimulatedDevice.py** souboru přidejte následující `import` příkazy a proměnné:

   ```python
    import threading
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

3. Do **SimulatedDevice.py** souboru přidejte následující kód. Nahraďte zástupnou hodnotu {deviceConnectionString}připojovacím řetězcem zařízení pro zařízení, které jste vytvořili v [modulu Odeslat telemetrii ze zařízení do rychlého startu služby IoT hub:](quickstart-send-telemetry-python.md)

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Přidejte následující funkci pro tisk přijatých zpráv do konzoly:

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

5. Přidejte následující kód pro inicializaci klienta a počkejte na příjem zprávy z cloudu na zařízení:

    ```python
    def iothub_client_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

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

7. Uložit a zavřít **SimulatedDevice.py** soubor.

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT hub

V tomto článku vytvoříte back-endovou službu pro odesílání zpráv mezi cloudy prostřednictvím služby IoT hub, kterou jste vytvořili v [aplikaci Send telemetrie ze zařízení do služby IoT hub](quickstart-send-telemetry-python.md). K odesílání zpráv z cloudu na zařízení potřebuje vaše služba oprávnění **k připojení služby.** Ve výchozím nastavení je každé centrum IoT Hub vytvořeno pomocí zásady sdíleného přístupu s názvem **služba,** která uděluje toto oprávnění.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Odeslání zprávy z cloudu na zařízení

V této části vytvoříte konzolovou aplikaci Pythonu, která odesílá zprávy z cloudu na zařízení do aplikace simulovaných zařízení. Potřebujete ID zařízení zařízení, které jste přidali v [odesílání telemetrie ze zařízení do rychlého startu služby IoT hub.](quickstart-send-telemetry-python.md) Potřebujete také připojovací řetězec centra IoT, který jste dříve zkopírovali v [připojovacím řetězci Centra IoT](#get-the-iot-hub-connection-string).

1. Pomocí textového editoru vytvořte **soubor SendCloudToDeviceMessage.py.**

2. Na začátek **SendCloudToDeviceMessage.py** souboru přidejte následující `import` příkazy a proměnné:

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

3. Do **SendCloudToDeviceMessage.py** souboru přidejte následující kód. Nahraďte zástupné hodnoty "{iot hub connection string}" a {device id}zástupným symbolem připojovacím řetězcem ioT hubu a ID zařízení, které jste si dříve poznamenali:

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

5. Přidejte následující kód, který odešle zprávu do zařízení a zpracovat zprávu o zpětné vazbě, když zařízení potvrdí zprávu z cloudu na zařízení:

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

7. Uložit a zavřít **soubor SendCloudToDeviceMessage.py.**

## <a name="run-the-applications"></a>Spuštění aplikací

Nyní můžete spustit aplikace.

1. Otevřete příkazový řádek a nainstalujte **sadku Azure IoT Hub Device SDK pro Python**.

    ```shell
    pip install azure-iothub-device-client
    ```

2. Na příkazovém řádku spusťte následující příkaz pro poslech zpráv z cloudu na zařízení:

    ```shell
    python SimulatedDevice.py
    ```

    ![Spuštění aplikace simulované zařízení](./media/iot-hub-python-python-c2d/simulated-device.png)

3. Otevřete nový příkazový řádek a nainstalujte **sadu Azure IoT Hub Service SDK pro Python**.

    ```shell
    pip install azure-iothub-service-client
    ```

4. Na příkazovém řádku spusťte následující příkaz, který odešle zprávu z cloudu na zařízení a vyčká te na zpětnou vazbu:

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![Spuštění aplikace pro odeslání příkazu cloud-to-device](./media/iot-hub-python-python-c2d/send-command.png)

5. Poznamenejte si zprávu přijatou zařízením.

    ![Přijatá zpráva](./media/iot-hub-python-python-c2d/message-received.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak odesílat a přijímat zprávy z cloudu na zařízení.

Příklady kompletních komplexních řešení, která používají službu IoT Hub, najdete v [tématu Akcelerátor řešení vzdáleného monitorování Azure IoT](https://azure.microsoft.com/documentation/suites/iot-suite/).

Další informace o vývoji řešení pomocí služby IoT Hub najdete v [průvodci vývojáři služby IoT Hub](iot-hub-devguide.md).
