---
title: Zprávy z cloudu na zařízení pomocí služby Azure IoT Hub (Python) | Microsoft Docs
description: Postup posílání zpráv z cloudu na zařízení ze služby Azure IoT Hub pomocí sad SDK Azure IoT pro Python. Aplikaci simulovaného zařízení upravíte tak, aby přijímala zprávy z cloudu na zařízení a upravila back-end aplikaci pro posílání zpráv z cloudu na zařízení.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: kgremban
ms.openlocfilehash: da5481af1086c14ce0961d0ac6b8ef55cfc73707
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68403874"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Posílání zpráv z cloudu na zařízení pomocí IoT Hub (Python)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Úvod

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

> [!NOTE]
> IoT Hub podporuje sadu SDK pro mnoho platforem a jazyků zařízení (včetně C, Java a JavaScriptu) prostřednictvím sad SDK pro zařízení Azure IoT. Podrobné pokyny, jak připojit zařízení k kódu tohoto kurzu a obecně k Azure IoT Hub, najdete v [centru pro vývojáře Azure IoT](https://www.azure.com/develop/iot).
>

Pro absolvování tohoto kurzu potřebujete:

* [Python 2. x nebo 3. x](https://www.python.org/downloads/). Ujistěte se, že používáte 32bitovou, nebo 64bitovou instalaci podle požadavků vašeho nastavení. Po zobrazení výzvy v průběhu instalace nezapomeňte přidat Python do proměnné prostředí pro konkrétní platformu. Pokud používáte Python 2.x, možná bude nutné [nainstalovat nebo upgradovat *pip*, systém správy balíčků Pythonu](https://pip.pypa.io/en/stable/installing/).

* Pokud používáte operační systém Windows, je k povolení používání nativních knihoven DLL z Pythonu potřeba [balíček distribuovatelných součástí Visual C++](https://www.microsoft.com/download/confirmation.aspx?id=48145).

* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) .)

> [!NOTE]
> Balíčky *pip* pro `azure-iothub-service-client` a `azure-iothub-device-client` jsou v současné době dostupné jenom pro operační systém Windows. Informace pro Linux a Mac OS najdete v oddílech týkajících se Linux a Mac OS na stránce [Příprava vývojového prostředí pro Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) .
>

## <a name="receive-messages-in-the-simulated-device-app"></a>Přijímání zpráv v aplikaci simulovaného zařízení

V této části vytvoříte konzolovou aplikaci v Pythonu pro simulaci zařízení a příjem zpráv z cloudu na zařízení ze služby IoT Hub.

1. Pomocí textového editoru vytvořte soubor **SimulatedDevice.py** .

2. Na začátek souboru `import` **SimulatedDevice.py** přidejte následující příkazy a proměnné:

   ```python
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError

    RECEIVE_CONTEXT = 0
    WAIT_COUNT = 10
    RECEIVED_COUNT = 0
    RECEIVE_CALLBACKS = 0
    ```

3. Do souboru **SimulatedDevice.py** přidejte následující kód. Nahraďte zástupný symbol {deviceConnectionString} připojovacím řetězcem zařízení pro zařízení, které jste vytvořili v části [poslat telemetrii ze zařízení, do rychlého startu služby IoT Hub](quickstart-send-telemetry-python.md) :

    ```python
    # choose AMQP or AMQP_WS as transport protocol
    PROTOCOL = IoTHubTransportProvider.AMQP
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Přidejte následující funkci, která vytiskne přijaté zprávy do konzoly:

    ```python
    def receive_message_callback(message, counter):
        global RECEIVE_CALLBACKS
        message_buffer = message.get_bytearray()
        size = len(message_buffer)
        print ( "Received Message [%d]:" % counter )
        print ( "    Data: <<<%s>>> & Size=%d" % (message_buffer[:size].decode('utf-8'), size) )
        map_properties = message.properties()
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        counter += 1
        RECEIVE_CALLBACKS += 1
        print ( "    Total calls received: %d" % RECEIVE_CALLBACKS )
        return IoTHubMessageDispositionResult.ACCEPTED

    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        client.set_message_callback(receive_message_callback, RECEIVE_CONTEXT)

        return client

    def print_last_message_time(client):
        try:
            last_message = client.get_last_message_receive_time()
            print ( "Last Message: %s" % time.asctime(time.localtime(last_message)) )
            print ( "Actual time : %s" % time.asctime() )
        except IoTHubClientError as iothub_client_error:
            if iothub_client_error.args[0].result == IoTHubClientResult.INDEFINITE_TIME:
                print ( "No message received" )
            else:
                print ( iothub_client_error )
    ```

5. Přidejte následující kód pro inicializaci klienta a počkejte na přijetí zprávy z cloudu na zařízení:

    ```python
    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        client.set_message_callback(receive_message_callback, RECEIVE_CONTEXT)

        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    status = client.get_send_status()
                    print ( "Send status: %s" % status )
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

        print_last_message_time(client)
    ```

6. Přidejte následující hlavní funkci:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

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
