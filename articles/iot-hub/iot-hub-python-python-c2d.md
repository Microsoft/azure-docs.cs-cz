---
title: Zprávy typu cloud zařízení s Azure IoT Hub (Python) | Microsoft Docs
description: Postupy pro odesílání zpráv typu cloud zařízení pro zařízení ze služby Azure IoT hub pomocí sady Azure IoT SDK pro jazyk Python. Můžete upravit aplikaci simulovaného zařízení příjem zpráv typu cloud zařízení a úpravě back-end aplikace k odesílání zpráv typu cloud zařízení.
services: iot-hub
documentationcenter: python
author: kgremban
manager: timlt
editor: ''
ms.assetid: 3ca8a78f-ade2-46e8-8a49-d5d599cdf1f1
ms.service: iot-hub
ms.devlang: javascript
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2018
ms.author: v-masebo;kgremban
ms.openlocfilehash: 9cd2423d2d235ed97e51178528dddd7419a718c2
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="send-cloud-to-device-messages-with-iot-hub-python"></a>Odesílání zpráv typu cloud zařízení službou IoT Hub (Python)
[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]


## <a name="introduction"></a>Úvod
Azure IoT Hub je plně spravovaná služba, která pomáhá povolit spolehlivou a zabezpečenou obousměrnou komunikaci mezi miliony zařízení a back-end řešení. [Začínáme se službou IoT Hub] kurz ukazuje, jak k vytvoření služby IoT hub, zřídit identitu zařízení v ní a kódu aplikaci ze simulovaného zařízení, která odesílá zprávy typu zařízení cloud.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

V tomto kurzu vychází [Začínáme se službou IoT Hub]. Jak ukazuje na:

* Z back-end vašeho řešení odesílání zpráv typu cloud zařízení na jedno zařízení prostřednictvím služby IoT Hub.
* Příjem zpráv typu cloud zařízení na zařízení.
* Z back-end vašeho řešení, žádosti o potvrzení o doručení (*zpětné vazby*) pro zprávy odeslané do zařízení ze služby IoT Hub.

Můžete najít další informace o zprávy typu cloud zařízení v [Příručka vývojáře pro službu IoT Hub][IoT Hub developer guide - C2D].

Na konci tohoto kurzu můžete spustit dvě aplikace Python konzoly:

* **SimulatedDevice.py**, upravenou verzi aplikace vytvořená v [Začínáme se službou IoT Hub], který se připojuje ke službě IoT hub a přijímá zprávy typu cloud zařízení.
* **SendCloudToDeviceMessage.py**, který odešle zprávu cloud zařízení na aplikaci simulovaného zařízení prostřednictvím služby IoT Hub a pak obdrží jeho potvrzení o doručení.

> [!NOTE]
> IoT Hub je podpora v sadě SDK pro mnoho zařízení platformy a jazyky (například C, Javy a JavaScriptu) prostřednictvím SDK pro zařízení Azure IoT. Podrobné pokyny o tom, jak připojit zařízení ke kódu v tomto kurzu a obecně do služby Azure IoT Hub, najdete v článku [Azure střediska pro vývojáře IoT].
> 

Pro absolvování tohoto kurzu potřebujete:

* [Python 2.x nebo 3.x][lnk-python-download]. Ujistěte se, že používáte 32bitovou, nebo 64bitovou instalaci podle požadavků vašeho nastavení. Po zobrazení výzvy v průběhu instalace nezapomeňte přidat Python do proměnné prostředí pro konkrétní platformu. Pokud používáte Python 2.x, možná bude nutné [nainstalovat nebo upgradovat *pip*, systém pro správu balíčků Pythonu][lnk-install-pip].
* Pokud používáte operační systém Windows, je k povolení používání nativních knihoven DLL z Pythonu potřeba [balíček distribuovatelných součástí Visual C++][lnk-visual-c-redist].
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)

> [!NOTE]
> Balíčky *pip* pro `azure-iothub-service-client` a `azure-iothub-device-client` jsou v současné době dostupné jenom pro operační systém Windows. Linux nebo Mac OS naleznete v části Linux a specifické pro Mac OS na post [lnk-python-devbox] [Příprava vývojového prostředí pro jazyk Python].
> 


## <a name="receive-messages-in-the-simulated-device-app"></a>Příjem zpráv v aplikaci simulovaného zařízení
V této části vytvoříte konzolovou aplikaci Python pro simulaci zařízení a příjem zpráv typu cloud zařízení ze služby IoT hub.

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

1. Přidejte následující kód, který **SimulatedDevice.py** souboru. Nahraďte hodnotu zástupného symbolu "{deviceConnectionString}" připojovací řetězec zařízení pro zařízení, kterou jste vytvořili v [Začínáme se službou IoT Hub] kurzu:
   
    ```python
    # choose AMQP or AMQP_WS as transport protocol
    PROTOCOL = IoTHubTransportProvider.AMQP
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. Přidejte následující funkci k tisku přijatých zpráv do konzoly:
   
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

1. Přidejte následující kód pro inicializaci klienta a čekání na příjem zpráv typu cloud zařízení:
   
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
V této části vytvoříte konzolovou aplikaci Python, která posílání zpráv typu cloud zařízení do aplikaci simulovaného zařízení. Je třeba ID zařízení v zařízení, které jste přidali v [Začínáme se službou IoT Hub] kurzu. Připojovací řetězec služby IoT Hub pro vaše centrum, které můžete najít v musíte taky [portál Azure].

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

1. Přidejte následující kód, který **SendCloudToDeviceMessage.py** souboru. Nahraďte hodnotu zástupného symbolu "{IoTHubConnectionString}" připojovací řetězec služby IoT Hub pro rozbočovače, který jste vytvořili v [Začínáme se službou IoT Hub] kurzu. Nahraďte zástupný symbol "{deviceId}" s ID zařízení v zařízení, které jste přidali v [Začínáme se službou IoT Hub] kurzu:
   
    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"
    ```

1. Přidejte následující funkci k tisku zpětnou vazbu zpráv do konzoly:
   
    ```python
    def open_complete_callback(context):
        print ( 'open_complete_callback called with context: {0}'.format(context) )

    def send_complete_callback(context, messaging_result):
        context = 0
        print ( 'send_complete_callback called with context : {0}'.format(context) )
        print ( 'messagingResult : {0}'.format(messaging_result) )
    ```

1. Přidejte následující kód k odeslání zprávy do zařízení a zpracování zpráv zpětnou vazbu, je-li zařízení uznává zpráv typu cloud zařízení:
   
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

1. Otevřete příkazový řádek a nainstalujte **Azure IoT Hub zařízení SDK pro jazyk Python**.

    ```
    pip install azure-iothub-device-client
    ```

1. Na příkazovém řádku spusťte následující příkaz přijímat zprávy typu cloud zařízení:
   
    ```shell
    python SimulatedDevice.py 
    ```
   
    ![Spusťte aplikaci simulovaného zařízení][img-simulated-device]

1. Otevřete nový příkazový řádek a nainstalujte **SDK služby Azure IoT Hub pro jazyk Python**.

    ```
    pip install azure-iothub-service-client
    ```

1. Na příkazovém řádku spusťte následující příkaz k odeslání zprávy typu cloud zařízení a počkat na zpětnou vazbu zpráva:
   
    ```shell
    python SendCloudToDeviceMessage.py 
    ```
   
    ![Spusťte aplikaci odesílat příkaz cloud zařízení][img-send-command]
   
1. Poznámka: zařízení přijaté zprávy.

    ![Přijatá zpráva][img-message-recieved]


## <a name="next-steps"></a>Další postup
V tomto kurzu jste zjistili, jak odesílat a přijímat zprávy typu cloud zařízení. 

Příklady dokončení začátku do konce řešení, které pomocí služby IoT Hub, najdete v sekci [Azure IoT Suite].

Další informace o vývoji řešení službou IoT Hub, najdete v článku [Příručka vývojáře pro službu IoT Hub].

<!-- Images -->
[img-simulated-device]: media/iot-hub-python-python-c2d/simulated-device.png
[img-send-command]:  media/iot-hub-python-python-c2d/send-command.png
[img-message-recieved]: media/iot-hub-python-python-c2d/message-recieved.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[Začínáme se službou IoT Hub]: iot-hub-node-node-getstarted.md
[IoT Hub developer guide - C2D]: iot-hub-devguide-messaging.md
[Příručka vývojáře pro službu IoT Hub]: iot-hub-devguide.md
[Azure střediska pro vývojáře IoT]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[portál Azure]: https://portal.azure.com
[Azure IoT Suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
