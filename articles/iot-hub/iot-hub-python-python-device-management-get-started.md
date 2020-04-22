---
title: Začínáme se správou zařízení Azure IoT Hub (Python) | Dokumenty společnosti Microsoft
description: Jak pomocí správy zařízení služby IoT Hub zahájit restartování vzdáleného zařízení. Pomocí sady Azure IoT SDK pro Python implementovat simulované zařízení aplikace, která obsahuje přímou metodu a aplikace služby, která vyvolá přímou metodu.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: f376831175840284fdfd15f367542d33ad9f7177
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759768"
---
# <a name="get-started-with-device-management-python"></a>Začínáme se správou zařízení (Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

V tomto kurzu získáte informace o následujících postupech:

* Pomocí portálu Azure vytvořte službu IoT Hub a vytvořte identitu zařízení ve svém centru IoT hub.

* Vytvořte simulovanou aplikaci zařízení, která obsahuje přímou metodu, která restartuje toto zařízení. Přímé metody jsou vyvolány z cloudu.

* Vytvořte konzolovou aplikaci Pythonu, která volá metodu přímého restartování v aplikaci simulovaných zařízení prostřednictvím služby IoT hub.

Na konci tohoto kurzu máte dvě konzolové aplikace Pythonu:

* **dmpatterns_getstarted_device.py**, který se připojuje k centru IoT s identitou zařízení vytvořenou dříve, obdrží metodu přímého restartování, simuluje fyzické restartování a hlásí čas posledního restartování.

* **dmpatterns_getstarted_service.py**, který volá přímou metodu v aplikaci simulované zařízení, zobrazí odpověď a zobrazí aktualizované hlášené vlastnosti.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Zkontrolujte, zda je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokován v některých prostředích podnikové a vzdělávací sítě. Další informace a způsoby, jak tento problém vyřešit, najdete [v tématu připojení k centru IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení v centru IoT hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení

V této části:

* Vytvoření konzolové aplikace Pythonu, která reaguje na přímou metodu volanou cloudem

* Simulace restartování zařízení

* Pomocí ohlášených vlastností povolte dotazy na dvojče zařízení k identifikaci zařízení a při jejich posledním restartování

1. Na příkazovém řádku spusťte následující příkaz k instalaci balíčku **azure-iot-device:**

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Pomocí textového editoru vytvořte soubor s názvem **dmpatterns_getstarted_device.py** v pracovním adresáři.

3. Na začátek souboru `import` **dmpatterns_getstarted_device.py** přidejte následující příkazy.

    ```python
    import threading
    import time
    import datetime
    from azure.iot.device import IoTHubDeviceClient, MethodResponse
    ```

4. Přidejte **proměnnou CONNECTION_STRING.** `{deviceConnectionString}` Nahraďte zástupnou hodnotu připojovacím řetězcem zařízení. Tento připojovací řetězec jste dříve zkopírovali v [části Registrovat nové zařízení v centru IoT](#register-a-new-device-in-the-iot-hub).  

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

5. Přidejte následující zpětná volání funkce k implementaci přímé metody na zařízení.

    ```python
    def reboot_listener(client):
        while True:
            # Receive the direct method request
            method_request = client.receive_method_request("rebootDevice")  # blocking call

            # Act on the method by rebooting the device...
            print( "Rebooting device" )
            time.sleep(20)
            print( "Device rebooted")

            # ...and patching the reported properties
            current_time = str(datetime.datetime.now())
            reported_props = {"rebootTime": current_time}
            client.patch_twin_reported_properties(reported_props)
            print( "Device twins updated with latest rebootTime")

            # Send a method response indicating the method request was resolved
            resp_status = 200
            resp_payload = {"Response": "This is the response from the device"}
            method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)
            client.send_method_response(method_response)
    ```

6. Spusťte naslouchací proces přímé metody a počkejte.

    ```python
    def iothub_client_init():
        client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            # Start a thread listening for "rebootDevice" direct method invocations
            reboot_listener_thread = threading.Thread(target=reboot_listener, args=(client,))
            reboot_listener_thread.daemon = True
            reboot_listener_thread.start()

            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Uložte a zavřete soubor **dmpatterns_getstarted_device.py.**

> [!NOTE]
> Za účelem zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu byste měli implementovat zásady opakování (například exponenciální backoff), jak je navrženo v článku [Přechodné zpracování chyb](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT hub

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Spuštění vzdáleného restartování zařízení pomocí přímé metody

V této části vytvoříte konzolovou aplikaci Pythonu, která iniciuje vzdálené restartování zařízení pomocí přímé metody. Aplikace používá dotazy na dvojče zařízení ke zjištění času posledního restartování pro toto zařízení.

1. Na příkazovém řádku spusťte následující příkaz pro instalaci balíčku **azure-iot-hub:**

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. Pomocí textového editoru vytvořte soubor s názvem **dmpatterns_getstarted_service.py** v pracovním adresáři.

3. Na začátek souboru `import` **dmpatterns_getstarted_service.py** přidejte následující příkazy.

    ```python
    import sys, time

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import CloudToDeviceMethod, CloudToDeviceMethodResult, Twin
    ```

4. Přidejte následující deklarace proměnných. Nahraďte `{IoTHubConnectionString}` zástupnou hodnotu připojovacím řetězcem služby IoT hub, který jste dříve zkopírovali v [připojovacím řetězci Služby IoT hub](#get-the-iot-hub-connection-string). Nahraďte `{deviceId}` zástupnou hodnotu ID zařízení, které jste zaregistrovali [v registru nového zařízení v centru IoT](#register-a-new-device-in-the-iot-hub)hub .

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

5. Přidejte následující funkci pro vyvolání metody zařízení pro restartování cílového zařízení, potom dotazujte na dvojčata zařízení a získejte čas posledního restartování.

    ```python
    def iothub_devicemethod_sample_run():
        try:
            # Create IoTHubRegistryManager
            registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            print ( "" )
            print ( "Invoking device to reboot..." )

            # Call the direct method.
            deviceMethod = CloudToDeviceMethod(method_name=METHOD_NAME, payload=METHOD_PAYLOAD)
            response = registry_manager.invoke_device_method(DEVICE_ID, deviceMethod)

            print ( "" )
            print ( "Successfully invoked the device to reboot." )

            print ( "" )
            print ( response.payload )

            while True:
                print ( "" )
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    twin_info = registry_manager.get_twin(DEVICE_ID)

                    if twin_info.properties.reported.get("rebootTime") != None :
                        print ("Last reboot time: " + twin_info.properties.reported.get("rebootTime"))
                    else:
                        print ("Waiting for device to report last reboot time...")

                    time.sleep(5)
                    status_counter += 1

        except Exception as ex:
            print ( "" )
            print ( "Unexpected error {0}".format(ex) )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubDeviceMethod sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Service Client DeviceManagement Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_devicemethod_sample_run()
    ```

6. Uložte a zavřete soubor **dmpatterns_getstarted_service.py.**

## <a name="run-the-apps"></a>Spouštění aplikací

Nyní jste připraveni ke spuštění aplikací.

1. Na příkazovém řádku spusťte následující příkaz a začněte naslouchat metodě přímého restartování.

    ```cmd/sh
    python dmpatterns_getstarted_device.py
    ```

2. Na jiném příkazovém řádku spusťte následující příkaz a spusťte vzdálené restartování a dotazna dvojče zařízení, abyste našli čas posledního restartování.

    ```cmd/sh
    python dmpatterns_getstarted_service.py
    ```

3. Zobrazí se odpověď zařízení na přímou metodu v konzole.

   Následující ukazuje odpověď zařízení na metodu přímého restartování:

   ![Výstup aplikace simulované zařízení](./media/iot-hub-python-python-device-management-get-started/device.png)

   Následující ukazuje službu, která volá metodu přímého restartování a dotazování dvojčete zařízení na stav:

   ![Spouštěcí výstup služby restartování](./media/iot-hub-python-python-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]