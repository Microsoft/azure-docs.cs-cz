---
title: Začínáme se správou zařízení v Azure IoT Hub (Python) | Microsoft Docs
description: Použití správy zařízení IoT Hub k zahájení restartování vzdáleného zařízení. Pomocí sady Azure IoT SDK pro Python můžete implementovat aplikaci simulovaného zařízení, která zahrnuje přímou metodu a aplikaci služby, která volá přímou metodu.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: 287dbd3d6da4aa2bf5bd1da652cdeaeda3136321
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907751"
---
# <a name="get-started-with-device-management-python"></a>Začínáme se správou zařízení (Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

V tomto kurzu získáte informace o následujících postupech:

* Pomocí Azure Portal můžete vytvořit IoT Hub a vytvořit identitu zařízení ve službě IoT Hub.

* Vytvořte aplikaci simulovaného zařízení, která obsahuje přímou metodu, která toto zařízení restartuje. Přímé metody jsou vyvolány z cloudu.

* Vytvořte konzolovou aplikaci v Pythonu, která v aplikaci simulovaného zařízení zavolá přímou metodu restartu ve službě IoT Hub.

Na konci tohoto kurzu máte dvě konzolové aplikace v Pythonu:

* **dmpatterns_getstarted_device. py**, který se připojuje ke službě IoT Hub s dříve vytvořenou identitou zařízení, přijímá přímou metodu restartování, simuluje fyzické restartování a oznamuje čas posledního restartování.

* **dmpatterns_getstarted_service. py**, který volá přímou metodu v aplikaci simulovaného zařízení, zobrazuje odpověď a zobrazuje aktualizované hlášené vlastnosti.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

Níže jsou uvedené pokyny k instalaci požadovaných součástí.

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení ve službě IoT Hub

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity.md)]

## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení

V této části:

* Vytvoření konzolové aplikace v Pythonu, která reaguje na přímou metodu volanou cloudem

* Simulace restartování zařízení

* Pomocí hlášených vlastností povolit nefungující dotazy zařízení k identifikaci zařízení a jejich poslední restartování

1. Na příkazovém řádku spusťte následující příkaz k instalaci balíčku **Azure-IoT-Device-Client** :

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

   > [!NOTE]
   > Balíčky PIP pro Azure-iothub-Service-Client a Azure-iothub-Device-Client jsou momentálně dostupné jenom pro operační systém Windows. Informace pro Linux a Mac OS najdete v oddílech týkajících se Linux a Mac OS na stránce [Příprava vývojového prostředí pro Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) .
   >

2. Pomocí textového editoru vytvořte v pracovním adresáři soubor s názvem **dmpatterns_getstarted_device. py** .

3. Na začátek souboru `import` **dmpatterns_getstarted_device. py** přidejte následující příkazy.

    ```python
    import random
    import time, datetime
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError, DeviceMethodReturnValue
    ```

4. Přidejte proměnné včetně proměnné **CONNECTION_STRING** a inicializace klienta.  Nahraďte `{deviceConnectionString}` hodnotu zástupného symbolu připojovacím řetězcem zařízení. Tento připojovací řetězec jste dříve zkopírovali v [části registrace nového zařízení ve službě IoT Hub](#register-a-new-device-in-the-iot-hub).  

    ```python
    CONNECTION_STRING = "{deviceConnectionString}"
    PROTOCOL = IoTHubTransportProvider.MQTT

    CLIENT = IoTHubClient(CONNECTION_STRING, PROTOCOL)

    WAIT_COUNT = 5

    SEND_REPORTED_STATE_CONTEXT = 0
    METHOD_CONTEXT = 0

    SEND_REPORTED_STATE_CALLBACKS = 0
    METHOD_CALLBACKS = 0
    ```

5. Přidejte následující zpětná volání funkcí k implementaci přímé metody v zařízení.

    ```python
    def send_reported_state_callback(status_code, user_context):
        global SEND_REPORTED_STATE_CALLBACKS

        print ( "Device twins updated." )

    def device_method_callback(method_name, payload, user_context):
        global METHOD_CALLBACKS

        if method_name == "rebootDevice":
            print ( "Rebooting device..." )
            time.sleep(20)

            print ( "Device rebooted." )

            current_time = str(datetime.datetime.now())
            reported_state = "{\"rebootTime\":\"" + current_time + "\"}"
            CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

            print ( "Updating device twins: rebootTime" )

        device_method_return_value = DeviceMethodReturnValue()
        device_method_return_value.response = "{ \"Response\": \"This is the response from the device\" }"
        device_method_return_value.status = 200

        return device_method_return_value
    ```

6. Spusťte naslouchací proces přímé metody a počkejte.

    ```python
    def iothub_client_init():
        if CLIENT.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            CLIENT.set_device_method_callback(device_method_callback, METHOD_CONTEXT)

    def iothub_client_sample_run():
        try:
            iothub_client_init()

            while True:
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

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

1. Na příkazovém řádku spusťte následující příkaz k instalaci balíčku **Azure-IoT-Service-Client** :

    ```cmd/sh
    pip install azure-iothub-service-client
    ```

   > [!NOTE]
   > Balíčky PIP pro Azure-iothub-Service-Client a Azure-iothub-Device-Client jsou momentálně dostupné jenom pro operační systém Windows. Informace pro Linux a Mac OS najdete v oddílech týkajících se Linux a Mac OS na stránce [Příprava vývojového prostředí pro Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) .
   >

2. Pomocí textového editoru vytvořte v pracovním adresáři soubor s názvem **dmpatterns_getstarted_service. py** .

3. Na začátek souboru `import` **dmpatterns_getstarted_service. py** přidejte následující příkazy.

    ```python
    import sys, time
    import iothub_service_client

    from iothub_service_client import IoTHubDeviceMethod, IoTHubError, IoTHubDeviceTwin
    ```

4. Přidejte následující deklarace proměnných. Nahraďte hodnotu [](#get-the-iot-hub-connection-string) zástupnéhosymbolupřipojovacímřetězcemIoTHub,kterýjstezkopírovalidřívevčástizískánípřipojovacíhořetězcecentraIoT`{IoTHubConnectionString}` hub. Nahraďte hodnotu [](#register-a-new-device-in-the-iot-hub) zástupnéhosymboluIDzařízení,kteréjstezaregistrovalivčástiregistracenovéhozařízeníveslužběIoT`{deviceId}` hub.

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
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)

            print ( "" )
            print ( "Invoking device to reboot..." )

            response = iothub_device_method.invoke(DEVICE_ID, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)

            print ( "" )
            print ( "Successfully invoked the device to reboot." )

            print ( "" )
            print ( response.payload )

            while True:
                print ( "" )
                print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    twin_info = iothub_twin_method.get_twin(DEVICE_ID)

                    if twin_info.find("rebootTime") != -1:
                        print ( "Last reboot time: " + twin_info[twin_info.find("rebootTime")+11:twin_info.find("rebootTime")+37])
                    else:
                        print ("Waiting for device to report last reboot time...")

                    time.sleep(5)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}".format(iothub_error) )
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