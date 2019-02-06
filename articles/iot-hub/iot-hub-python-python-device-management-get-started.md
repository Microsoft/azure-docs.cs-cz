---
title: Začínáme se správou zařízení Azure IoT Hub (Python) | Dokumentace Microsoftu
description: Jak zahajte restartování vzdálené zařízení pomocí správy zařízení služby IoT Hub. Implementace aplikace simulovaného zařízení, která obsahuje metodu s přímým přístupem a app service, která vyvolá přímou metodu pomocí sady Azure IoT SDK pro Python.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 01/02/2018
ms.author: kgremban
ms.openlocfilehash: 36514fe321fa352c2526db5a18ac8618d7af713e
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55756763"
---
# <a name="get-started-with-device-management-python"></a>Začínáme se správou zařízení (Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

V tomto kurzu získáte informace o následujících postupech:

* Pomocí webu Azure portal k vytvoření služby IoT Hub a vytvoření identity zařízení ve službě IoT hub.
* Vytvoření aplikace simulovaného zařízení, která obsahuje přímé metody, která toto zařízení restartuje. Přímé metody jsou vyvolány z cloudu.
* Vytvoření konzolové aplikace v Pythonu, která volá metodu restartování s přímým přístupem v aplikaci simulovaného zařízení prostřednictvím služby IoT hub.

Na konci tohoto kurzu budete mít dvě aplikace konzoly v Pythonu:

**dmpatterns_getstarted_device.PY**, propojuje službu IoT hub s identitou zařízení vytvořenou dříve, obdrží restartování přímé metody, simuluje fyzické restartování a ohlásí, čas poslední restartování.

**dmpatterns_getstarted_service.PY**, která volá metodu s přímým přístupem v aplikaci simulovaného zařízení, zobrazí odpověď a zobrazí aktualizovaný ohlášené vlastnosti.

Pro absolvování tohoto kurzu potřebujete:

* [Python 2.x nebo 3.x][lnk-python-download]. Ujistěte se, že používáte 32bitovou, nebo 64bitovou instalaci podle požadavků vašeho nastavení. Po zobrazení výzvy v průběhu instalace nezapomeňte přidat Python do proměnné prostředí pro konkrétní platformu. Pokud používáte Python 2.x, možná bude nutné [nainstalovat nebo upgradovat *pip*, systém pro správu balíčků Pythonu][lnk-install-pip].
    * Nainstalujte [azure-iothub-device-client](https://pypi.org/project/azure-iothub-device-client/) balíček, pomocí příkazu   `pip install azure-iothub-device-client`
    * Nainstalujte [azure-iothub-service-client](https://pypi.org/project/azure-iothub-service-client/) balíček, pomocí příkazu   `pip install azure-iothub-service-client`
* Pokud používáte operační systém Windows, je k povolení používání nativních knihoven DLL z Pythonu potřeba [balíček distribuovatelných součástí Visual C++][lnk-visual-c-redist].
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Načtení připojovacího řetězce pro službu IoT hub

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení
V této části provedete následující:

* Vytvoření konzolové aplikace v Pythonu, která bude reagovat na přímou metodu volanou cloudem.
* Simulaci restartování zařízení
* Pomocí ohlášených vlastností umožníte dotazů na dvojčata zařízení identifikovat zařízení a kdy se naposledy restartování

1. Pomocí textového editoru, vytvořte **dmpatterns_getstarted_device.py** souboru.

1. Přidejte následující `import` příkazy na začátku **dmpatterns_getstarted_device.py** souboru.
   
    ```python
    import random
    import time, datetime
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError, DeviceMethodReturnValue
    ```

1. Přidání proměnné, včetně **CONNECTION_STRING** proměnné a inicializace klienta.  Nahraďte připojovací řetězec připojovacím řetězcem zařízení.  
   
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

1. Přidejte následující zpětná volání funkce pro implementaci přímé metody v zařízení.
   
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

1. Spuštění naslouchacího procesu přímé metody a počkat.
   
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

1. Uložte a zavřete **dmpatterns_getstarted_device.py** souboru.

> [!NOTE]
> Za účelem zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu by měly implementovat zásady opakování (například exponenciální regresí), jak je navrženo v článku [zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults).


## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Aktivační událost vzdálené restartování zařízení pomocí přímé metody
V této části vytvoříte konzolovou aplikaci v Pythonu, které vyvolává vzdálené restartování zařízení pomocí přímé metody. Aplikace používá dotazů na dvojčata zařízení ke zjištění poslední čas restartování pro dané zařízení.

1. Pomocí textového editoru, vytvořte **dmpatterns_getstarted_service.py** souboru.

1. Přidejte následující `import` příkazy na začátku **dmpatterns_getstarted_service.py** souboru.
   
    ```python
    import sys, time
    import iothub_service_client

    from iothub_service_client import IoTHubDeviceMethod, IoTHubError, IoTHubDeviceTwin
    ```

1. Přidejte následující deklarace proměnných. Jenom nahradit zástupné hodnoty _IoTHubConnectionString_ a _deviceId_.
   
    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

1. Přidejte následující funkci, která vyvolat metodu zařízení restartovat cílové zařízení a pak dotaz na dvojčata zařízení a získat poslední čas restartování.
   
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

1. Uložte a zavřete **dmpatterns_getstarted_service.py** souboru.


## <a name="run-the-apps"></a>Spouštění aplikací
Nyní jste připraveni aplikaci spustit.

1. Na příkazovém řádku spusťte následující příkaz, který zahájit naslouchání pro přímé metody restartování.
   
    ```
    python dmpatterns_getstarted_device.py
    ```

1. Jiné příkazového řádku spusťte následující příkaz spustí vzdálené restartování a dotazů pro dvojče zařízení najít poslední čas restartování.
   
    ```
    python dmpatterns_getstarted_service.py
    ```

1. Zobrazí se zařízení odpověď na přímé metody v konzole.

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

<!-- images and links -->
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/

[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
