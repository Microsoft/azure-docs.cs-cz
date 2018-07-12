---
title: Aktualizace firmwaru zařízení pomocí služby Azure IoT Hub (Python) | Dokumentace Microsoftu
description: Jak zahájit aktualizaci firmwaru zařízení pomocí správy zařízení ve službě Azure IoT Hub. Pomocí sady Azure IoT SDK pro Python k implementaci aplikace simulovaného zařízení a app service, která spustí aktualizaci firmwaru.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: kgremban
ms.openlocfilehash: d2ebdf54e595c2f02464c0c2446a6e5f5feefb9c
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38482016"
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-pythonpython"></a>Aktualizovat pomocí správy zařízení za účelem zahájení firmwaru zařízení (Python/Python)
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

V [Začínáme se správou zařízení] [ lnk-dm-getstarted] výukový program, jste viděli, jak používat [dvojče zařízení] [ lnk-devtwin] a [přímé metody ] [ lnk-c2dmethod] primitiv vzdálené restartování zařízení. Tento kurz používá stejný základní služby IoT Hub a obsahuje pokyny a ukazuje, jak provádět aktualizace firmwaru simulované začátku do konce.  Tento model se používá k provedení aktualizace firmwaru Intel Edison ukázkou zařízení.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

V tomto kurzu získáte informace o následujících postupech:

* Vytvoření konzolové aplikace v Pythonu, která volá metodu firmwareUpdate s přímým přístupem v aplikaci simulovaného zařízení prostřednictvím služby IoT hub.
* Vytvoření aplikace simulovaného zařízení, která implementuje **firmwareUpdate** přímá metoda. Tato metoda zahájí vícefázový proces, který čeká na stažením image firmwaru, stáhne image firmwaru a nakonec použije image firmwaru. Během každé fáze aktualizace zařízení využívá ohlášené vlastnosti k podávat zprávy o pokroku.

Na konci tohoto kurzu budete mít dvě aplikace konzoly v Pythonu:

**dmpatterns_fwupdate_service.PY**, která volá metodu s přímým přístupem v aplikaci simulovaného zařízení, zobrazí odpovědi a pravidelně (každých 500ms) zobrazí aktualizovaný ohlášené vlastnosti.

**dmpatterns_fwupdate_device.PY**, propojuje službu IoT hub s identitou zařízení vytvořenou dříve, obdrží firmwareUpdate přímé metody, prochází přes více stavu procesu pro simulaci, včetně aktualizace firmwaru: čeká se na obrázku stáhnete, stahuje nové image a nakonec použitím bitové kopie.

Pro absolvování tohoto kurzu potřebujete:

* [Python 2.x nebo 3.x][lnk-python-download]. Ujistěte se, že používáte 32bitovou, nebo 64bitovou instalaci podle požadavků vašeho nastavení. Po zobrazení výzvy v průběhu instalace nezapomeňte přidat Python do proměnné prostředí pro konkrétní platformu. Pokud používáte Python 2.x, možná bude nutné [nainstalovat nebo upgradovat *pip*, systém pro správu balíčků Pythonu][lnk-install-pip].
* Pokud používáte operační systém Windows, je k povolení používání nativních knihoven DLL z Pythonu potřeba [balíček distribuovatelných součástí Visual C++][lnk-visual-c-redist].
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Aktivace firmwaru vzdálené aktualizace na zařízení s využitím přímé metody
V této části vytvoříte konzolovou aplikaci v Pythonu, které vyvolává vzdálené firmwaru v zařízení. Aplikace používá přímé metody k zahájení aktualizace a používá dotazů na dvojčata zařízení a pravidelně získat stav aktualizace firmwaru aktivní.

1. Na příkazovém řádku spusťte následující příkaz k instalaci **azure-iothub-service-client** balíčku:
   
    ```cmd/sh
    pip install azure-iothub-service-client
    ```

1. Pomocí textového editoru, v pracovním adresáři, vytvořit **dmpatterns_getstarted_service.py** souboru.

1. Přidejte následující "import" příkazy a proměnné na začátku **dmpatterns_getstarted_service.py** souboru. Nahraďte `IoTHubConnectionString` a `deviceId` s hodnotami, které jste si poznamenali dříve:
   
    ```python
    import sys
    import time

    import iothub_service_client
    from iothub_service_client import IoTHubDeviceTwin, IoTHubDeviceMethod, IoTHubError

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "firmwareUpdate"
    METHOD_PAYLOAD = "{\"fwPackageUri\":\"test.com\"}"
    TIMEOUT = 60
    MESSAGE_COUNT = 5
    ```

1. Přidejte následující funkce volání přímé metody a zobrazení hodnoty firmwareUpdate hlášené vlastnost. Také přidat `main` rutinu:
   
    ```python
    def iothub_firmware_sample_run():
        try:
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            print ( "" )
            print ( "Direct Method called." )
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)
        
            response = iothub_device_method.invoke(DEVICE_ID, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)
            print ( response.payload )
        
            print ( "" )
            print ( "Device Twin queried, press Ctrl-C to exit" )
            while True:
                twin_info = iothub_twin_method.get_twin(DEVICE_ID)

                if "\"firmwareStatus\":\"standBy\"" in twin_info:
                    print ( "Waiting on device..." )
                elif "\"firmwareStatus\":\"downloading\"" in twin_info:
                    print ( "Downloading firmware..." )
                elif "\"firmwareStatus\":\"applying\"" in twin_info:
                    print ( "Download complete, applying firmware..." )
                elif "\"firmwareStatus\":\"completed\"" in twin_info:
                    print ( "Firmware applied" )
                    print ( "" )
                    print ( "Get reported properties from device twin:" )
                    print ( twin_info )
                    break
                else:
                    print ( "Unknown status" )

                status_counter = 0
                while status_counter <= MESSAGE_COUNT:
                    time.sleep(1)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubService sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub firmware update Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_firmware_sample_run()
    ```

1. Uložte a zavřete **dmpatterns_fwupdate_service.py** souboru.


## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení
V této části:

* Vytvoření konzolové aplikace v Pythonu, která bude reagovat na přímou metodu volanou cloudem.
* Aktivujete simulovanou aktualizaci firmwaru.
* Pomocí ohlášených vlastností umožníte dotazům na dvojčata zařízení identifikovat zařízení a čas jejich poslední dokončené aktualizace firmwaru.

1. Na příkazovém řádku spusťte následující příkaz k instalaci **azure-iothub-device-client** balíčku:
   
    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Pomocí textového editoru, vytvořte **dmpatterns_fwupdate_device.py** souboru.

1. Přidejte následující "import" příkazy a proměnné na začátku **dmpatterns_fwupdate_device.py** souboru. Nahraďte `deviceConnectionString` připojovacím řetězcem zařízení ze služby IoT hub:
   
    ```python
    import time, datetime
    import sys
    import threading

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubError, DeviceMethodReturnValue

    SEND_REPORTED_STATE_CONTEXT = 0
    METHOD_CONTEXT = 0

    MESSAGE_COUNT = 10

    PROTOCOL = IoTHubTransportProvider.MQTT
    CONNECTION_STRING = "{deviceConnectionString}"
    CLIENT = IoTHubClient(CONNECTION_STRING, PROTOCOL)
    ```

1. Přidejte následující funkce, které se používají k zajištění hlášené vlastností se aktualizuje a implementovat metodu s přímým přístupem:
   
    ```python
    def send_reported_state_callback(status_code, user_context):
        print ( "Reported state updated." )

    def device_method_callback(method_name, payload, user_context):
        if method_name == "firmwareUpdate":
            print ( "Starting firmware update." )
            image_url = payload
            thr = threading.Thread(target=simulate_download_image, args=([payload]), kwargs={})
            thr.start()
    
            device_method_return_value = DeviceMethodReturnValue()
            device_method_return_value.response = "{ \"Response\": \"Firmware update started\" }"
            device_method_return_value.status = 200
            return device_method_return_value
    ```

1. Přidejte následující funkce, které simulují stahování a použití image firmwaru:
   
    ```python
    def simulate_download_image(image_url):
        time.sleep(15)
        print ( "Downloading image from: " + image_url )

        reported_state = "{\"firmwareStatus\":\"downloading\", \"downloadComplete\":\"" + str(datetime.datetime.now()) + "\"}"
        CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        time.sleep(15)
    
        simulate_apply_image(image_url)
    
    def simulate_apply_image(image_url):
        print ( "Applying image from: " + image_url )

        reported_state = "{\"firmwareStatus\":\"applying\", \"startedApplyingImage\":\"" + str(datetime.datetime.now()) + "\"}"
        CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
        time.sleep(15)
    
        simulate_complete_image()

    def simulate_complete_image():
        print ( "Image applied." )

        reported_state = "{\"firmwareStatus\":\"completed\", \"lastFirmwareUpdate\":\"" + str(datetime.datetime.now()) + "\"}"
        CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
    ```

8. Přidejte následující funkci, která inicializuje dvojče zařízení ohlášené vlastnosti a počkejte přímé metody, která se má volat. Také přidat `main` rutinu:
   
    ```python
    def iothub_firmware_sample_run():
        try:
            CLIENT.set_device_method_callback(device_method_callback, METHOD_CONTEXT)

            reported_state = "{\"firmwareStatus\":\"standBy\", \"logTime\":\"" + str(datetime.datetime.now()) + "\"}"
            CLIENT.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)
            print ( "Device twins initialized." )
            print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )
        
            while True:
                status_counter = 0
                while status_counter <= MESSAGE_COUNT:
                    time.sleep(10)
                    status_counter += 1
            
        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python firmware update sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_firmware_sample_run()
    ```

> [!NOTE]
> Za účelem zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu by měly implementovat zásady opakování (například exponenciální regresí), jak je navrženo v článku na webu MSDN [zpracování přechodných chyb](https://msdn.microsoft.com/library/hh675232.aspx).
> 


## <a name="run-the-apps"></a>Spouštění aplikací
Nyní jste připraveni aplikaci spustit.

1. Na příkazovém řádku spusťte následující příkaz, který zahájit naslouchání pro přímé metody restartování.
   
    ```cmd/sh
    python dmpatterns_fwupdate_device.py
    ```

1. Jiné příkazového řádku spusťte následující příkaz spustí vzdálené restartování a dotazů pro dvojče zařízení najít poslední čas restartování.
   
    ```cmd/sh
    python dmpatterns_fwupdate_service.py
    ```

1. Zobrazí se zařízení odpověď na přímé metody v konzole. Poznamenejte si změnu v hodnotě ohlášené vlastnosti v průběhu aktualizace firmwaru.

    ![výstup programu][1]


## <a name="next-steps"></a>Další postup
V tomto kurzu se používá přímé metody k aktivaci vzdáleného firmwaru v zařízení a umožňuje sledovat průběh aktualizace firmwaru ohlášené vlastnosti.

Zjistěte, jak rozšířit vaše IoT řešení a plán metoda volá do různých zařízení, najdete v článku [plánování a vysílání úloh] [ lnk-tutorial-jobs] kurzu.

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-node-node-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-node-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[1]: ./media/iot-hub-python-python-firmware-update/1.png
