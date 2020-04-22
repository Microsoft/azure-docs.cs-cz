---
title: Zprávy z cloudu na zařízení s Azure IoT Hub (Python) | Dokumenty společnosti Microsoft
description: Jak odesílat zprávy z cloudu na zařízení do zařízení z centra Azure IoT pomocí sad Azure IoT SDK pro Python. Můžete upravit simulované zařízení aplikace přijímat zprávy z cloudu na zařízení a upravit back-end aplikace pro odesílání zpráv z cloudu na zařízení.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: f0760f6e61904295771ba349f8101e2d6dc6afe3
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759742"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Odesílání zpráv z cloudu na zařízení pomocí služby IoT Hub (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub je plně spravovaná služba, která pomáhá umožnit spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení a back-endem řešení. [Odeslat telemetrii ze zařízení do služby IoT hub](quickstart-send-telemetry-python.md) rychlý start ukazuje, jak vytvořit službu IoT hub, zřízení identity zařízení v něm a kód simulované zařízení aplikace, která odesílá zprávy zařízení cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Tento kurz staví na [odesílání telemetrie ze zařízení do služby IoT hub](quickstart-send-telemetry-python.md). To vám ukáže, jak:

* Z back-endu vašeho řešení můžete odesílat zprávy z cloudu na zařízení do jednoho zařízení prostřednictvím služby IoT Hub.

* Příjem zpráv z cloudu na zařízení na zařízení.

Další informace o zprávách mezi cloudy najdete v [průvodci vývojáři služby IoT Hub](iot-hub-devguide-messaging.md).

Na konci tohoto kurzu spustíte dvě konzolové aplikace Pythonu:

* **SimulatedDevice.py**, upravená verze aplikace vytvořená v [aplikaci Send telemetrie ze zařízení do služby IoT hub](quickstart-send-telemetry-python.md), která se připojuje k centru IoT hub a přijímá zprávy z cloudu na zařízení.

* **SendCloudToDeviceMessage.py**, který odesílá zprávy z cloudu na zařízení do aplikace simulovaných zařízení prostřednictvím služby IoT Hub.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Zkontrolujte, zda je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokován v některých prostředích podnikové a vzdělávací sítě. Další informace a způsoby, jak tento problém vyřešit, najdete [v tématu připojení k centru IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="receive-messages-in-the-simulated-device-app"></a>Příjem zpráv v aplikaci pro simulované zařízení

V této části vytvoříte konzolovou aplikaci Pythonu, která simuluje zařízení a přijímá zprávy z cloudu na zařízení z centra IoT Hub.

1. Z příkazového řádku ve vašem pracovním adresáři nainstalujte **sadku Azure IoT Hub Device SDK pro Python**:

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Pomocí textového editoru vytvořte soubor s názvem **SimulatedDevice.py**.

1. Na začátek **SimulatedDevice.py** souboru přidejte následující `import` příkazy a proměnné:

    ```python
    import threading
    import time
    from azure.iot.device import IoTHubDeviceClient

    RECEIVED_MESSAGES = 0
    ```

1. Do **SimulatedDevice.py** souboru přidejte následující kód. `{deviceConnectionString}` Nahraďte zástupnou hodnotu připojovacím řetězcem zařízení pro zařízení, které jste vytvořili v [možnosti Odeslat telemetrii ze zařízení do rychlého startu centra IoT:](quickstart-send-telemetry-python.md)

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. Přidejte následující funkci pro tisk přijatých zpráv do konzoly:

    ```python
    def message_listener(client):
        global RECEIVED_MESSAGES
        while True:
            message = client.receive_message()
            RECEIVED_MESSAGES += 1
            print("\nMessage received:")

            #print data and both system and application (custom) properties
            for property in vars(message).items():
                print ("    {0}".format(property))

            print( "Total calls received: {}".format(RECEIVED_MESSAGES))
            print()
    ```

1. Přidejte následující kód pro inicializaci klienta a počkejte na příjem zprávy z cloudu na zařízení:

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
            print ( "IoT Hub C2D Messaging device sample stopped" )
    ```

1. Přidejte následující hlavní funkci:

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub C2D Messaging device sample..." )
        print ( "Waiting for C2D messages, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

1. Uložte a zavřete soubor **SimulatedDevice.py**.

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT hub

V tomto článku vytvoříte back-endovou službu pro odesílání zpráv mezi cloudy prostřednictvím služby IoT hub, kterou jste vytvořili v [aplikaci Send telemetrie ze zařízení do služby IoT hub](quickstart-send-telemetry-python.md). K odesílání zpráv z cloudu na zařízení potřebuje vaše služba oprávnění **k připojení služby.** Ve výchozím nastavení je každé centrum IoT Hub vytvořeno pomocí zásady sdíleného přístupu s názvem **služba,** která uděluje toto oprávnění.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="send-a-cloud-to-device-message"></a>Odeslání zprávy z cloudu na zařízení

V této části vytvoříte konzolovou aplikaci Pythonu, která odesílá zprávy z cloudu na zařízení do aplikace simulovaných zařízení. Potřebujete ID zařízení zařízení, které jste přidali v [odesílání telemetrie ze zařízení do rychlého startu služby IoT hub.](quickstart-send-telemetry-python.md) Potřebujete také připojovací řetězec centra IoT, který jste dříve zkopírovali v [připojovacím řetězci Konzumního Centrum IoT](#get-the-iot-hub-connection-string).

1. Ve svém pracovním adresáři otevřete příkazový řádek a nainstalujte **sadu Azure IoT Hub Service SDK pro Python**.

   ```cmd/sh
   pip install azure-iot-hub
   ```

1. Pomocí textového editoru vytvořte soubor s názvem **SendCloudToDeviceMessage.py**.

1. Na začátek **SendCloudToDeviceMessage.py** souboru přidejte následující `import` příkazy a proměnné:

    ```python
    import random
    import sys
    from azure.iot.hub import IoTHubRegistryManager

    MESSAGE_COUNT = 2
    AVG_WIND_SPEED = 10.0
    MSG_TXT = "{\"service client sent a message\": %.2f}"
    ```

1. Do **SendCloudToDeviceMessage.py** souboru přidejte následující kód. `{iot hub connection string}` Nahraďte `{device id}` a zástupné hodnoty připojovacím řetězcem centra IoT a ID zařízení, které jste si dříve poznamenali:

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

1. Přidejte následující kód pro odesílání zpráv do zařízení:

    ```python
    def iothub_messaging_sample_run():
        try:
            # Create IoTHubRegistryManager
            registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            for i in range(0, MESSAGE_COUNT):
                print ( 'Sending message: {0}'.format(i) )
                data = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))

                props={}
                # optional: assign system properties
                props.update(messageId = "message_%d" % i)
                props.update(correlationId = "correlation_%d" % i)
                props.update(contentType = "application/json")

                # optional: assign application properties
                prop_text = "PropMsg_%d" % i
                props.update(testProperty = prop_text)

                registry_manager.send_c2d_message(DEVICE_ID, data, properties=props)

            try:
                # Try Python 2.xx first
                raw_input("Press Enter to continue...\n")
            except:
                pass
                # Use Python 3.xx in the case of exception
                input("Press Enter to continue...\n")

        except Exception as ex:
            print ( "Unexpected error {0}" % ex )
            return
        except KeyboardInterrupt:
            print ( "IoT Hub C2D Messaging service sample stopped" )
    ```

1. Přidejte následující hlavní funkci:

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub C2D Messaging service sample..." )

        iothub_messaging_sample_run()
    ```

1. Uložit a zavřít **soubor SendCloudToDeviceMessage.py.**

## <a name="run-the-applications"></a>Spuštění aplikací

Nyní můžete spustit aplikace.

1. Na příkazovém řádku v pracovním adresáři spusťte následující příkaz pro poslech zpráv mezi cloudy a zařízení:

    ```shell
    python SimulatedDevice.py
    ```

    ![Spuštění aplikace simulované zařízení](./media/iot-hub-python-python-c2d/device-1.png)

1. Otevřete nový příkazový řádek v pracovním adresáři a spusťte následující příkaz pro odesílání zpráv z cloudu na zařízení:

    ```shell
    python SendCloudToDeviceMessage.py
    ```

    ![Spuštění aplikace pro odeslání příkazu cloud-to-device](./media/iot-hub-python-python-c2d/service.png)

1. Poznamenejte si zprávy přijaté zařízením.

    ![Přijatá zpráva](./media/iot-hub-python-python-c2d/device-2.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak odesílat a přijímat zprávy z cloudu na zařízení.

Příklady kompletních komplexních řešení, která používají službu IoT Hub, najdete v [tématu Akcelerátor řešení vzdáleného monitorování Azure IoT](https://azure.microsoft.com/documentation/suites/iot-suite/).

Další informace o vývoji řešení pomocí služby IoT Hub najdete v [průvodci vývojáři služby IoT Hub](iot-hub-devguide.md).
