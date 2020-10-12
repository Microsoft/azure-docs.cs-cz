---
title: Začínáme se správou zařízení v Azure IoT Hub (Python) | Microsoft Docs
description: Použití správy zařízení IoT Hub k zahájení restartování vzdáleného zařízení. Pomocí sady Azure IoT SDK pro Python můžete implementovat aplikaci simulovaného zařízení, která zahrnuje přímou metodu a aplikaci služby, která volá přímou metodu.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python
ms.openlocfilehash: d4c514042b89341b90b0bb9c939ef4b463741916
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87872714"
---
# <a name="get-started-with-device-management-python"></a>Začínáme se správou zařízení (Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

V tomto kurzu získáte informace o následujících postupech:

* Pomocí Azure Portal můžete vytvořit IoT Hub a vytvořit identitu zařízení ve službě IoT Hub.

* Vytvořte aplikaci simulovaného zařízení, která obsahuje přímou metodu, která toto zařízení restartuje. Přímé metody jsou vyvolány z cloudu.

* Vytvořte konzolovou aplikaci v Pythonu, která v aplikaci simulovaného zařízení zavolá přímou metodu restartu ve službě IoT Hub.

Na konci tohoto kurzu máte dvě konzolové aplikace v Pythonu:

* **dmpatterns_getstarted_device. py**, který se připojí ke službě IoT Hub s dříve vytvořenou identitou zařízení, obdrží přímou metodu restart, simuluje fyzické restartování a oznamuje čas posledního restartování.

* **dmpatterns_getstarted_service. py**, který volá přímou metodu v aplikaci simulovaného zařízení, zobrazuje odpověď a zobrazuje aktualizované hlášené vlastnosti.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Ujistěte se, že je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokovaný v některých podnikových a vzdělávacích prostředích sítě. Další informace a způsoby, jak tento problém obejít, najdete v tématu [připojení k IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT (neboli IoT Hubu)

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení ve službě IoT Hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení

V této části:

* Vytvoření konzolové aplikace v Pythonu, která reaguje na přímou metodu volanou cloudem

* Simulace restartování zařízení

* Pomocí hlášených vlastností povolit nefungující dotazy zařízení k identifikaci zařízení a jejich poslední restartování

1. Na příkazovém řádku spusťte následující příkaz pro instalaci balíčku **Azure-IoT-Device** :

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Pomocí textového editoru vytvořte v pracovním adresáři soubor s názvem **dmpatterns_getstarted_device. py** .

3. Na `import` začátek souboru **dmpatterns_getstarted_device. py** přidejte následující příkazy.

    ```python
    import threading
    import time
    import datetime
    from azure.iot.device import IoTHubDeviceClient, MethodResponse
    ```

4. Přidejte **CONNECTION_STRING** proměnnou. Nahraďte `{deviceConnectionString}` hodnotu zástupného symbolu připojovacím řetězcem zařízení. Tento připojovací řetězec jste dříve zkopírovali v [části registrace nového zařízení ve službě IoT Hub](#register-a-new-device-in-the-iot-hub).  

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

5. Přidejte následující zpětná volání funkcí k implementaci přímé metody v zařízení.

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

7. Uložte a zavřete soubor **dmpatterns_getstarted_device. py** .

> [!NOTE]
> Za účelem zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu byste měli implementovat zásady opakování (například exponenciální omezení rychlosti), jak je navrženo v článku, [zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults).

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT Hub

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Aktivace vzdáleného restartování zařízení pomocí přímé metody

V této části vytvoříte konzolovou aplikaci v Pythonu, která iniciuje vzdálené restartování zařízení pomocí přímé metody. Aplikace pomocí dvojitých dotazů na zařízení zjistí čas posledního restartování tohoto zařízení.

1. Na příkazovém řádku spusťte následující příkaz pro instalaci balíčku **Azure-IoT-Hub** :

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. Pomocí textového editoru vytvořte v pracovním adresáři soubor s názvem **dmpatterns_getstarted_service. py** .

3. Na `import` začátek souboru **dmpatterns_getstarted_service. py** přidejte následující příkazy.

    ```python
    import sys, time

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import CloudToDeviceMethod, CloudToDeviceMethodResult, Twin
    ```

4. Přidejte následující deklarace proměnných. Nahraďte `{IoTHubConnectionString}` hodnotu zástupného symbolu připojovacím řetězcem IoT Hub, který jste zkopírovali dříve v [části získání připojovacího řetězce centra IoT Hub](#get-the-iot-hub-connection-string). Nahraďte `{deviceId}` hodnotu zástupného symbolu ID zařízení, které jste zaregistrovali v [části registrace nového zařízení ve službě IoT Hub](#register-a-new-device-in-the-iot-hub).

    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

5. Přidejte následující funkci, která vyvolá metodu zařízení k restartování cílového zařízení, a pak dotaz na vlákna zařízení a získá čas posledního restartování.

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

6. Uložte a zavřete soubor **dmpatterns_getstarted_service. py** .

## <a name="run-the-apps"></a>Spouštění aplikací

Nyní jste připraveni spustit aplikace.

1. Na příkazovém řádku spusťte následující příkaz, který zahájí naslouchání metodě restartování přímo.

    ```cmd/sh
    python dmpatterns_getstarted_device.py
    ```

2. Na jiném příkazovém řádku spusťte následující příkaz, který aktivuje vzdálené restartování a dotaz na vlákna zařízení, aby se našel čas posledního restartování.

    ```cmd/sh
    python dmpatterns_getstarted_service.py
    ```

3. V konzole se zobrazí odpověď zařízení k přímé metodě.

   Následuje ukázka reakce zařízení přímo na metodu restart:

   ![Výstup aplikace simulovaného zařízení](./media/iot-hub-python-python-device-management-get-started/device.png)

   Následující příklad ukazuje službu, která volá metodu přímého restartování a cyklické dotazování na stav zařízení:

   ![Aktivovat výstup služby restart](./media/iot-hub-python-python-device-management-get-started/service.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
