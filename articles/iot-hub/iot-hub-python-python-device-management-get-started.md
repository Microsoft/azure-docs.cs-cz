---
title: Začínáme se správou zařízení Azure IoT Hub (Python) | Microsoft Docs
description: Jak používat k zahájení restartu zařízení vzdálenou správou zařízení IoT Hub. Sada Azure IoT SDK pro Python kterou použijete k implementaci aplikaci simulovaného zařízení, která zahrnuje přímá metoda a aplikační služby, která volá metodu direct.
services: iot-hub
documentationcenter: .net
author: kgremban
manager: timlt
editor: ''
ms.assetid: e044006d-ffd6-469b-bc63-c182ad066e31
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/02/2018
ms.author: v-masebo;kgremban
ms.openlocfilehash: 001a5d180e2b97894174ba7c63ec3947c9968364
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="get-started-with-device-management-python"></a>Začínáme se správou zařízení (Python)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

V tomto kurzu získáte informace o následujících postupech:

* Použití portálu Azure k vytvoření služby IoT Hub a vytvoření identity zařízení ve službě IoT hub.
* Vytvoření aplikace simulovaného zařízení, která obsahuje přímý metodu, která restartování zařízení. Přímé metody jsou vyvolány z cloudu.
* Vytvořte konzolovou aplikaci Python, která volá metodu přímé restartování v aplikaci simulovaného zařízení prostřednictvím služby IoT hub.

Na konci tohoto kurzu máte dvě aplikace Python konzoly:

**dmpatterns_getstarted_device.PY**, který připojí ke službě IoT hub s identitou zařízení vytvořenou dříve, obdrží přímá metoda restartování, simuluje restartu fyzické a sestavy čas posledního restartování.

**dmpatterns_getstarted_service.PY**, která volá metodu přímé v aplikaci simulovaného zařízení, zobrazí odpověď a zobrazí aktualizovaná hlášené vlastnosti.

Pro absolvování tohoto kurzu potřebujete:

* [Python 2.x nebo 3.x][lnk-python-download]. Ujistěte se, že používáte 32bitovou, nebo 64bitovou instalaci podle požadavků vašeho nastavení. Po zobrazení výzvy v průběhu instalace nezapomeňte přidat Python do proměnné prostředí pro konkrétní platformu. Pokud používáte Python 2.x, možná bude nutné [nainstalovat nebo upgradovat *pip*, systém pro správu balíčků Pythonu][lnk-install-pip].
    * Nainstalujte [azure-iothub zařízení client](https://pypi.org/project/azure-iothub-device-client/) balíčku, pomocí příkazu   `pip install azure-iothub-device-client`
    * Nainstalujte [azure-iothub-service-client](https://pypi.org/project/azure-iothub-service-client/) balíčku, pomocí příkazu   `pip install azure-iothub-service-client`
* Pokud používáte operační systém Windows, je k povolení používání nativních knihoven DLL z Pythonu potřeba [balíček distribuovatelných součástí Visual C++][lnk-visual-c-redist].
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení
V této části provedete následující:

* Vytvořte konzolovou aplikaci Python, která reaguje na přímé metodu s názvem cloudem
* Simulovat restartování zařízení
* Pomocí hlášen vlastnostech povolit dotazy na twin zařízení k identifikaci zařízení, a když trvají restartovat

1. Pomocí textového editoru, vytvořte **dmpatterns_getstarted_device.py** souboru.

1. Přidejte následující `import` příkazy na začátku **dmpatterns_getstarted_device.py** souboru.
   
    ```python
    import random
    import time, datetime
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError, DeviceMethodReturnValue
    ```

1. Přidání proměnné, včetně **CONNECTION_STRING** proměnnou a inicializace klienta.  Nahraďte připojovacího řetězce připojovacím řetězcem zařízení.  
   
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

1. Přidejte následující zpětná funkce volání a implementovat metodu přímé na zařízení.
   
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

1. Spuštění nástroje pro sledování přímá metoda a počkejte.
   
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
> Za účelem zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu byte měli implementovat zásady opakování (například exponenciální opakování), jak je navrženo v článku [Řešení přechodných chyb][lnk-transient-faults] na webu MSDN.


## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Aktivační události restartu vzdálené v zařízení s přímá metoda
V této části vytvoříte konzolovou aplikaci Python, který iniciuje vzdálené restartování na zařízení pomocí přímého metody. Aplikace používá dotazy twin zařízení Pokud chcete zjistit, čas posledního restartování pro toto zařízení.

1. Pomocí textového editoru, vytvořte **dmpatterns_getstarted_service.py** souboru.

1. Přidejte následující `import` příkazy na začátku **dmpatterns_getstarted_service.py** souboru.
   
    ```python
    import sys, time
    import iothub_service_client

    from iothub_service_client import IoTHubDeviceMethod, IoTHubError, IoTHubDeviceTwin
    ```

1. Přidejte následující deklarace proměnných. Pouze nahraďte zástupný symbol hodnoty pro _IoTHubConnectionString_ a _deviceId_.
   
    ```python
    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "rebootDevice"
    METHOD_PAYLOAD = "{\"method_number\":\"42\"}"
    TIMEOUT = 60
    WAIT_COUNT = 10
    ```

1. Přidejte následující funkci k vyvolání metody zařízení k restartování cílového zařízení a potom dotaz pro dvojčata zařízení a získat poslední čas restartování.
   
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

1. Na příkazovém řádku spusťte následující příkaz, aby začal přijímat přímá metoda restartování.
   
    ```
    python dmpatterns_getstarted_device.py
    ```

1. Jiné příkazového řádku spusťte následující příkaz k aktivační události restartovat vzdálený počítač a dotaz pro dvojče zařízení najít poslední čas restartování počítače.
   
    ```
    python dmpatterns_getstarted_service.py
    ```

1. Zobrazí zařízení odpověď na přímá metoda v konzole.

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
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
