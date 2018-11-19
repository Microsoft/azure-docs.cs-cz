---
title: Zprávy typu cloud zařízení pomocí služby Azure IoT Hub (Python) | Dokumentace Microsoftu
description: Jak odesílat zprávy typu cloud zařízení na zařízení ze služby Azure IoT hub pomocí sad Azure IoT SDK pro Python. Upravíte aplikaci simulovaného zařízení pro příjem zpráv z cloudu do zařízení a upravovat back endové aplikace odesílat zprávy typu cloud zařízení.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: kgremban
ms.openlocfilehash: 8c8cf77107f87522f9ae121845f53d8993449651
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/16/2018
ms.locfileid: "51824791"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Odesílání zpráv typu cloud zařízení pomocí služby IoT Hub (Python)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]


## <a name="introduction"></a>Úvod
Azure IoT Hub je plně spravovaná služba, která pomáhá povolit spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení a back-endu řešení. [Začínáme se službou IoT Hub] kurz ukazuje postupy při vytvoření služby IoT hub, zřídit identitu zařízení v něm a kódu aplikace simulovaného zařízení, která odesílá zprávy typu zařízení cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Tento kurz vychází [Začínáme se službou IoT Hub]. To se dozvíte, jak do:

* Z back-end vašeho řešení odesílat zprávy typu cloud zařízení na jediné zařízení prostřednictvím služby IoT Hub.
* Příjem zpráv typu cloud zařízení na zařízení.
* Z back-end vašeho řešení, požádat o doručení potvrzení (*zpětnou vazbu*) pro zprávy odeslané do zařízení ze služby IoT Hub.

Můžete najít další informace o zprávy typu cloud zařízení v [Příručka vývojáře pro IoT Hub][IoT Hub developer guide - C2D].

Na konci tohoto kurzu spustíte dvě aplikace konzoly v Pythonu:

* **SimulatedDevice.py**, upravenou verzi aplikaci vytvořenou v [Začínáme se službou IoT Hub], který se připojí ke službě IoT hub a přijímá zprávy typu cloud zařízení.
* **SendCloudToDeviceMessage.py**, která odesílá zprávy typu cloud zařízení do aplikace simulovaného zařízení prostřednictvím služby IoT Hub a potom přijímá jeho doručení potvrzení.

> [!NOTE]
> IoT Hub má sady SDK podporují mnoho platforem zařízení a jazyky (včetně C, Javy a JavaScriptu) prostřednictvím sady SDK pro zařízení Azure IoT. Podrobné pokyny o tom, jak připojit zařízení ke kódu v tomto kurzu a obecně pro službu Azure IoT Hub, najdete v článku [centrum pro vývojáře Azure IoT].
> 

Pro absolvování tohoto kurzu potřebujete:

* [Python 2.x nebo 3.x][lnk-python-download]. Ujistěte se, že používáte 32bitovou, nebo 64bitovou instalaci podle požadavků vašeho nastavení. Po zobrazení výzvy v průběhu instalace nezapomeňte přidat Python do proměnné prostředí pro konkrétní platformu. Pokud používáte Python 2.x, možná bude nutné [nainstalovat nebo upgradovat *pip*, systém pro správu balíčků Pythonu][lnk-install-pip].
* Pokud používáte operační systém Windows, je k povolení používání nativních knihoven DLL z Pythonu potřeba [balíček distribuovatelných součástí Visual C++][lnk-visual-c-redist].
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)

> [!NOTE]
> Balíčky *pip* pro `azure-iothub-service-client` a `azure-iothub-device-client` jsou v současné době dostupné jenom pro operační systém Windows. Linux/Mac OS najdete v části operačních systémů Linux a Mac OS specifické pro příspěvek [Příprava vývojového prostředí pro jazyk Python] [lnk-python-devbox].
> 


## <a name="receive-messages-in-the-simulated-device-app"></a>Příjem zpráv v aplikaci simulovaného zařízení
V této části vytvoříte konzolovou aplikaci v Pythonu pro simulaci zařízení a přijímat zprávy typu cloud zařízení ze služby IoT hub.

1. Pomocí textového editoru, vytvořte **SimulatedDevice.py** souboru.

1. Přidejte následující `import` příkazy a proměnné na začátku **SimulatedDevice.py** souboru:
   
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

1. Přidejte následující kód, který **SimulatedDevice.py** souboru. Nahraďte hodnotu zástupného symbolu "{deviceConnectionString}" připojovacím řetězcem zařízení pro zařízení, kterou jste vytvořili v [Začínáme se službou IoT Hub] kurzu:
   
    ```python
    # choose AMQP or AMQP_WS as transport protocol
    PROTOCOL = IoTHubTransportProvider.AMQP
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. Přidejte následující funkci, která vytiskne přijaté zprávy do konzoly:
   
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

1. Přidejte následující kód pro inicializaci klienta a čekat na zprávy typu cloud zařízení:
   
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

1. Přidejte následující hlavní funkce:
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_sample_run()
    ```

1. Uložte a zavřete **SimulatedDevice.py** souboru.


## <a name="send-a-cloud-to-device-message"></a>Odeslání zprávy typu cloud zařízení
V této části vytvoříte konzolovou aplikaci v Pythonu, která odesílá zprávy typu cloud zařízení do aplikace simulovaného zařízení. ID zařízení, zařízení, které jste přidali v kroku budete potřebovat [Začínáme se službou IoT Hub] kurzu. Budete potřebovat připojovací řetězec služby IoT Hub pro vaše centrum, které můžete najít v [Azure Portal].

1. Pomocí textového editoru, vytvořte **SendCloudToDeviceMessage.py** souboru.

1. Přidejte následující `import` příkazy a proměnné na začátku **SendCloudToDeviceMessage.py** souboru:
   
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

1. Přidejte následující kód, který **SendCloudToDeviceMessage.py** souboru. Nahraďte hodnotu zástupného symbolu "{IoTHubConnectionString}" připojovacím řetězcem služby IoT Hub pro rozbočovač, kterou jste vytvořili v [Začínáme se službou IoT Hub] kurzu. Nahraďte zástupný text "{deviceId}" s ID zařízení na zařízení, které jste přidali v kroku [Začínáme se službou IoT Hub] kurzu:
   
    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

1. Přidejte následující funkci, která vytiskne zprávy se zpětnou vazbou do konzoly:
   
    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

1. Přidejte následující kód k odeslání zprávy do zařízení a zpracovala zpráva zpětnou vazbu, když zařízení uznává zprávy typu cloud zařízení:
   
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

1. Přidejte následující hlavní funkce:
   
    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client Messaging Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_messaging_sample_run()
    ```

1. Uložte a zavřete **SendCloudToDeviceMessage.py** souboru.


## <a name="run-the-applications"></a>Spuštění aplikací
Nyní můžete spustit aplikace.

1. Otevřete příkazový řádek a nainstalujte **SDK zařízení Azure IoT Hub pro Python**.

    ```
    pip install azure-iothub-device-client
    ```

1. Na příkazovém řádku spusťte následující příkaz k naslouchání pro zprávy typu cloud zařízení:
   
    ```shell
    python SimulatedDevice.py 
    ```
   
    ![Spusťte aplikaci simulovaného zařízení][img-simulated-device]

1. Otevřete nový příkazový řádek a nainstalujte **Service SDK Azure IoT Hub pro Python**.

    ```
    pip install azure-iothub-service-client
    ```

1. Na příkazovém řádku spusťte následující příkaz k odeslání zprávy typu cloud zařízení a počkat na zpětnou vazbu zpráva:
   
    ```shell
    python SendCloudToDeviceMessage.py 
    ```
   
    ![Spuštění aplikace odesílat příkazy typu cloud zařízení][img-send-command]
   
1. Poznámka: ve zprávě přijaté v zařízení.

    ![Byla přijata zpráva][img-message-received]


## <a name="next-steps"></a>Další postup
V tomto kurzu jste zjistili, jak posílat a přijímat zprávy typu cloud zařízení. 

Příklady kompletní řešení začátku do konce, které používají služby IoT Hub najdete v tématu [Akcelerátor řešení Azure IoT vzdálené monitorování].

Další informace o vývoji řešení s využitím služby IoT Hub, najdete v článku [Příručka vývojáře pro IoT Hub].

<!-- Images -->
[img-simulated-device]: media/iot-hub-python-python-c2d/simulated-device.png
[img-send-command]:  media/iot-hub-python-python-c2d/send-command.png
[img-message-received]: media/iot-hub-python-python-c2d/message-received.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[Začínáme se službou IoT Hub]: quickstart-send-telemetry-node.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[Příručka vývojáře pro IoT Hub]: iot-hub-devguide.md
[Centrum pro vývojáře Azure IoT]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[Azure Portal]: https://portal.azure.com
[Akcelerátor řešení Azure IoT vzdálené monitorování]: https://azure.microsoft.com/documentation/suites/iot-suite/
