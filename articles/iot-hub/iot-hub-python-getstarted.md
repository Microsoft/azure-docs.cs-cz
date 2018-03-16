---
title: "Začínáme se službou Azure IoT Hub (Python) | Dokumentace Microsoftu"
description: "Zjistěte, jak odesílat zprávy typu zařízení-cloud do služby Azure IoT Hub pomocí sad IoT SDK pro Python. Vytvořte simulované zařízení a aplikace služeb pro registraci vašeho zařízení, odesílání zpráv a čtení zpráv ze služby IoT Hub."
services: iot-hub
author: dsk-2015
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: python
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/02/2018
ms.author: dkshir
ms.custom: na
ms.openlocfilehash: 2c14176a3d45b85ae94c27549e5d6448e240ed98
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="connect-your-simulated-device-to-your-iot-hub-using-python"></a>Připojení simulovaného zařízení ke službě IoT Hub pomocí Pythonu
[!INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

Na konci tohoto kurzu budete mít dvě aplikace v Pythonu:

* **CreateDeviceIdentity.py** vytváří identitu zařízení a přiřazený bezpečnostní klíč k připojení aplikace simulovaného zařízení.
* **SimulatedDevice.py** propojuje službu IoT Hub s dříve vytvořenou identitou zařízení a pravidelně zasílá telemetrickou zprávu pomocí protokolu MQTT.

> [!NOTE]
> Informace o sadách Azure IoT SDK, s jejichž pomocí můžete vytvářet aplikace pro zařízení i back-end vašeho řešení, najdete v tématu [Sady SDK služby Azure IoT][lnk-hub-sdks].
> 
> 

Pro absolvování tohoto kurzu potřebujete:

* [Python 2.x nebo 3.x][lnk-python-download]. Ujistěte se, že používáte 32bitovou, nebo 64bitovou instalaci podle požadavků vašeho nastavení. Po zobrazení výzvy v průběhu instalace nezapomeňte přidat Python do proměnné prostředí pro konkrétní platformu. Pokud používáte Python 2.x, možná bude nutné [nainstalovat nebo upgradovat *pip*, systém pro správu balíčků Pythonu][lnk-install-pip].
* Pokud používáte operační systém Windows, je k povolení používání nativních knihoven DLL z Pythonu potřeba [balíček distribuovatelných součástí Visual C++][lnk-visual-c-redist].
* [Node.js 4.0 nebo novější][lnk-node-download]. Ujistěte se, že používáte 32bitovou, nebo 64bitovou instalaci podle požadavků vašeho nastavení. To je nutné k instalaci [nástroje IoT Hub Explorer][lnk-iot-hub-explorer].
* Aktivní účet Azure. Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].

> [!NOTE]
> Balíčky *pip* pro `azure-iothub-service-client` a `azure-iothub-device-client` jsou v současné době dostupné jenom pro operační systém Windows. Informace o operačních systémech Linux a Mac OS najdete v částech věnovaných těmto operačním systémům v příspěvku, v němž se autor věnuje [přípravě vývojových prostředí pro Python][lnk-python-devbox].
> 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Nyní jste vytvořili svůj IoT Hub. Ve zbývající části tohoto kurzu použijte název hostitele a připojovací řetězec služby IoT Hub.

> [!NOTE]
> Službu IoT Hub můžete také snadno vytvořit v příkazovém řádku, a to pomocí Azure CLI založeném na Pythonu nebo Node.js. Rychlé kroky, podle kterých to můžete udělat, najdete v článku [Vytvoření služby IoT Hub pomocí Azure CLI 2.0][lnk-azure-cli-hub]. 
> 

## <a name="create-a-device-identity"></a>Vytvoření identity zařízení
Tato část uvádí kroky k vytvoření konzolové aplikace v Pythonu, která v registru identit ve službě IoT Hub vytvoří identitu zařízení. Zařízení lze připojit ke službě IoT Hub, pouze pokud má záznam v registru zařízení. Další informace najdete v části **Registr identit** v [Příručce pro vývojáře pro službu IoT Hub][lnk-devguide-identity]. Tato konzolová aplikace po spuštění vygeneruje jedinečné ID zařízení a klíč, s jehož pomocí se zařízení může identifikovat při posílání zpráv typu zařízení-cloud do služby IoT Hub. 

1. Otevřete příkazový řádek a nainstalujte **sadu SDK služby Azure IoT Hub pro Python**. Po dokončení instalace sady SDK zavřete příkazový řádek.

    ```
    pip install azure-iothub-service-client
    ```

2. Vytvořte soubor Pythonu s názvem **CreateDeviceIdentity.py**. Otevřete ho v [editoru Pythonu nebo integrovaném vývojovém prostředí (IDE) podle vašeho výběru][lnk-python-ide-list], například ve výchozím prostředí [IDLE][lnk-idle].

3. Přidejte následující kód, který importuje požadované moduly ze sady SDK služby:

    ```python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceStatus, IoTHubError
    ```
2. Přidejte následující kód a nahraďte zástupnou hodnotu `[IoTHub Connection String]` připojovacím řetězcem pro službu IoT Hub, kterou jste vytvořili v předchozí části. Jako `DEVICE_ID` můžete použít libovolný název.
   
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "MyFirstPythonDevice"
    ```
   [!INCLUDE [iot-hub-pii-note-naming-device](../../includes/iot-hub-pii-note-naming-device.md)]

3. Přidejte následující funkci, která vypíše některé informace o zařízení.

    ```python
    def print_device_info(title, iothub_device):
        print ( title + ":" )
        print ( "iothubDevice.deviceId                    = {0}".format(iothub_device.deviceId) )
        print ( "iothubDevice.primaryKey                  = {0}".format(iothub_device.primaryKey) )
        print ( "iothubDevice.secondaryKey                = {0}".format(iothub_device.secondaryKey) )
        print ( "iothubDevice.connectionState             = {0}".format(iothub_device.connectionState) )
        print ( "iothubDevice.status                      = {0}".format(iothub_device.status) )
        print ( "iothubDevice.lastActivityTime            = {0}".format(iothub_device.lastActivityTime) )
        print ( "iothubDevice.cloudToDeviceMessageCount   = {0}".format(iothub_device.cloudToDeviceMessageCount) )
        print ( "iothubDevice.isManaged                   = {0}".format(iothub_device.isManaged) )
        print ( "iothubDevice.authMethod                  = {0}".format(iothub_device.authMethod) )
        print ( "" )
    ```
3. Přidejte následující funkci, která vytvoří identifikaci zařízení pomocí Správce registru. 

    ```python
    def iothub_createdevice():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)
            auth_method = IoTHubRegistryManagerAuthMethod.SHARED_PRIVATE_KEY
            new_device = iothub_registry_manager.create_device(DEVICE_ID, "", "", auth_method)
            print_device_info("CreateDevice", new_device)

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "iothub_createdevice stopped" )
    ```
4. Nakonec přidejte následujícím způsobem hlavní funkci a soubor uložte.

    ```python
    if __name__ == '__main__':
        print ( "" )
        print ( "Python {0}".format(sys.version) )
        print ( "Creating device using the Azure IoT Hub Service SDK for Python" )
        print ( "" )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_createdevice()
    ```
5. Na příkazovém řádku spusťte soubor **CreateDeviceIdentity.py** následujícím způsobem:

    ```python
    python CreateDeviceIdentity.py
    ```
6. Měli byste vidět vytváření simulovaného zařízení. Poznamenejte si hodnoty **deviceId** (ID zařízení) a **primaryKey** (primární klíč) tohoto zařízení. Tyto hodnoty budete potřebovat později při vytváření aplikace, která se ke službě IoT Hub připojí jako zařízení.

    ![Úspěšné vytvoření zařízení][1]

> [!NOTE]
> V registru identit služby IoT Hub se uchovávají pouze identity zařízení za účelem bezpečného přístupu ke službě IoT Hub. Ukládají se tady ID zařízení a jejich klíče, které slouží jako zabezpečené přihlašovací údaje, a příznak povoleno/zakázáno, s jehož pomocí můžete zakázat přístup k jednotlivým zařízením. Pokud aplikace potřebuje pro zařízení ukládat další metadata, měla by používat úložiště pro konkrétní aplikaci. Další informace najdete v [Příručce pro vývojáře pro službu IoT Hub][lnk-devguide-identity].
> 
> 


## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení
V této části vytvoříte konzolovou aplikaci v Pythonu, která simuluje zařízení a odesílá zprávy typu zařízení-cloud do služby IoT Hub.

1. Otevřete nový příkazový řádek a nainstalujte sadu SDK pro zařízení Azure IoT Hub pro Python následujícím způsobem. Po dokončení instalace zavřete příkazový řádek.

    ```
    pip install azure-iothub-device-client
    ```
2. Vytvořte soubor s názvem **SimulatedDevice.py**. Otevřete tento soubor v editoru Pythonu nebo integrovaném vývojovém prostředí (IDE) podle vašeho výběru (například IDLE).

3. Přidejte následující kód, který importuje požadované moduly ze sady SDK pro zařízení.

    ```python
    import random
    import time
    import sys
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubMessage, IoTHubMessageDispositionResult, IoTHubError, DeviceMethodReturnValue
    ```
4. Přidejte následující kód a nahraďte zástupnou hodnotu `[IoTHub Device Connection String]` připojovacím řetězcem pro vaše zařízení. Připojovací řetězec zařízení je obvykle ve formátu `HostName=<hostName>;DeviceId=<deviceId>;SharedAccessKey=<primaryKey>`. Použijte hodnoty **deviceId** a **primaryKey** zařízení, které jste vytvořili v předchozí části, k nahrazení zástupných hodnot `<deviceId>` a `<primaryKey>`. Nahraďte `<hostName>` názvem hostitele služby IoT Hub, který je obvykle ve formátu `<IoT hub name>.azure-devices.net`.

    ```python
    # String containing Hostname, Device Id & Device Key in the format
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    # choose HTTP, AMQP or MQTT as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT
    MESSAGE_TIMEOUT = 10000
    AVG_WIND_SPEED = 10.0
    SEND_CALLBACKS = 0
    MSG_TXT = "{\"deviceId\": \"MyFirstPythonDevice\",\"windSpeed\": %.2f}"    
    ```
5. Přidejte následující kód, který definuje zpětné volání pro potvrzení odeslání. 

    ```python
    def send_confirmation_callback(message, result, user_context):
        global SEND_CALLBACKS
        print ( "Confirmation[%d] received for message with result = %s" % (user_context, result) )
        map_properties = message.properties()
        print ( "    message_id: %s" % message.message_id )
        print ( "    correlation_id: %s" % message.correlation_id )
        key_value_pair = map_properties.get_internals()
        print ( "    Properties: %s" % key_value_pair )
        SEND_CALLBACKS += 1
        print ( "    Total calls confirmed: %d" % SEND_CALLBACKS )
    ```
6. Přidejte následující kód, který inicializuje klienta zařízení.

    ```python
    def iothub_client_init():
        # prepare iothub client
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)
        # set the time until a message times out
        client.set_option("messageTimeout", MESSAGE_TIMEOUT)
        client.set_option("logtrace", 0)
        client.set_option("product_info", "HappyPath_Simulated-Python")
        return client
    ```
7. Přidejte následující funkci, která naformátuje a odešle zprávu ze simulovaného zařízení do služby IoT Hub.

    ```python
    def iothub_client_telemetry_sample_run():

        try:
            client = iothub_client_init()
            print ( "IoT Hub device sending periodic messages, press Ctrl-C to exit" )
            message_counter = 0

            while True:
                msg_txt_formatted = MSG_TXT % (AVG_WIND_SPEED + (random.random() * 4 + 2))
                # messages can be encoded as string or bytearray
                if (message_counter & 1) == 1:
                    message = IoTHubMessage(bytearray(msg_txt_formatted, 'utf8'))
                else:
                    message = IoTHubMessage(msg_txt_formatted)
                # optional: assign ids
                message.message_id = "message_%d" % message_counter
                message.correlation_id = "correlation_%d" % message_counter
                # optional: assign properties
                prop_map = message.properties()
                prop_text = "PropMsg_%d" % message_counter
                prop_map.add("Property", prop_text)

                client.send_event_async(message, send_confirmation_callback, message_counter)
                print ( "IoTHubClient.send_event_async accepted message [%d] for transmission to IoT Hub." % message_counter )

                status = client.get_send_status()
                print ( "Send status: %s" % status )
                time.sleep(30)

                status = client.get_send_status()
                print ( "Send status: %s" % status )

                message_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
    ```
8. Nakonec přidejte hlavní funkci. 

    ```python
    if __name__ == '__main__':
        print ( "Simulating a device using the Azure IoT Hub Device SDK for Python" )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_client_telemetry_sample_run()
    ```
9. Uložte a zavřete soubor **SimulatedDevice.py**. Nyní jste připraveni aplikaci spustit.

> [!NOTE]
> Za účelem zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu byte měli implementovat zásady opakování (například exponenciální opakování), jak je navrženo v článku [Řešení přechodných chyb][lnk-transient-faults] na webu MSDN.
> 
> 

## <a name="receive-messages-from-your-simulated-device"></a>Příjem zpráv ze simulovaného zařízení
Pokud chcete přijímat telemetrické zprávy z vašeho zařízení, musíte použít koncový bod kompatibilní se službou [Event Hubs][lnk-event-hubs-overview] a vystavený službou IoT Hub, který čte zprávy typu zařízení-cloud. V kurzu [Začínáme se službou Event Hubs][lnk-eventhubs-tutorial] najdete informace o zpracování zpráv ze služby Event Hubs pro koncové body kompatibilní s centrem událostí služby IoT Hub. Služba Event Hubs zatím nepodporuje telemetrii v Pythonu, takže ke čtení zpráv typu zařízení-cloud ze služby IoT Hub můžete vytvořit konzolovou aplikaci založenou na službě Event Hubs buď v [Node.js](iot-hub-node-node-getstarted.md#D2C_node), nebo v prostředí [.NET](iot-hub-csharp-csharp-getstarted.md#D2C_csharp). V tomto kurzu se dozvíte, jak můžete číst tyto zprávy pomocí [nástroje IoT Hub Explorer][lnk-iot-hub-explorer].

1. Otevřete příkazový řádek a nainstalujte nástroj IoT Hub Explorer. 

    ```
    npm install -g iothub-explorer
    ```

2. Na příkazovém řádku spusťte následující příkaz, abyste začali monitorovat zprávy typu zařízení-cloud z vašeho zařízení. V zástupném symbolu následujícím po `--login` použijte připojovací řetězec vaší služby IoT Hub.

    ```
    iothub-explorer monitor-events MyFirstPythonDevice --login "[IoTHub connection string]"
    ```

3. Otevřete nový příkazový řádek a přejděte do adresáře, který obsahuje soubor **SimulatedDevice.py**.

4. Spusťte soubor **SimulatedDevice.py**, který pravidelně odesílá telemetrická data do služby IoT Hub. 
   
    ```
    python SimulatedDevice.py
    ```
5. Podívejte se na zprávy zařízení na příkazovém řádku se spuštěným nástrojem IoT Hub Explorer z předchozí části. 

    ![Zprávy typu zařízení-cloud v Pythonu][2]

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste nakonfigurovali novou službu IoT Hub na webu Azure Portal a potom jste vytvořili identitu zařízení v registru identit ve službě IoT Hub. Pomocí identity zařízení jste aplikaci simulovaného zařízení povolili odesílání zpráv typu zařízení-cloud do služby IoT Hub. S pomocí nástroje IoT Hub Explorer jste sledovali zprávy přijímané službou IoT Hub. 

Pokud chcete podrobněji prozkoumat použití sady SDK pro Python pro službu Azure IoT Hub, přejděte do [tohoto úložiště GitHub][lnk-python-github]. Pokud si chcete projít možnosti zasílání zpráv v sadě SDK služby Azure IoT Hub pro Python, můžete si stáhnout a spustit ukázkový soubor [iothub_messaging_sample.py][lnk-messaging-sample]. Pro simulaci na straně zařízení pomocí sady SDK pro zařízení Azure IoT Hub pro Python si můžete stáhnout a spustit ukázkový soubor [iothub_client_sample.py][lnk-client-sample].

Chcete-li pokračovat v seznamování se službou IoT Hub a prozkoumat další scénáře IoT, podívejte se na tato témata:

* [Připojení zařízení][lnk-connect-device]
* [Začínáme se správou zařízení][lnk-device-management]
* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge][lnk-iot-edge]

Další informace o tom, jak rozšířit vaše řešení internetu věcí a zpracovávat škálované zprávy typu zařízení-cloud, najdete v kurzu [Zpracování zpráv typu zařízení-cloud][lnk-process-d2c-tutorial].
[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

<!-- Images. -->
[1]: ./media/iot-hub-python-getstarted/createdevice.png
[2]: ./media/iot-hub-python-getstarted/sendd2cmessage.png

<!-- Links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-node-download]: https://nodejs.org/en/download/
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-azure-cli-hub]: https://docs.microsoft.com/azure/iot-hub/iot-hub-create-using-cli
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-idle]: https://docs.python.org/3/library/idle.html
[lnk-python-ide-list]: https://wiki.python.org/moin/IntegratedDevelopmentEnvironments
[lnk-iot-hub-explorer]: https://github.com/Azure/iothub-explorer
[lnk-python-github]: https://github.com/Azure/azure-iot-sdk-python
[lnk-messaging-sample]: https://github.com/Azure/azure-iot-sdk-python/blob/master/service/samples/iothub_messaging_sample.py
[lnk-client-sample]: https://github.com/Azure/azure-iot-sdk-python/blob/master/device/samples/iothub_client_sample.py

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md
[lnk-python-devbox]: https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md

[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
