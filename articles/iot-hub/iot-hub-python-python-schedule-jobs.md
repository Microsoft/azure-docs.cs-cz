---
title: Plánování úloh službou Azure IoT Hub (Python) | Microsoft Docs
description: Popisuje, jak naplánovat úlohu služby Azure IoT Hub pro vyvolání přímé metody na několika zařízeních. SDK služby Azure IoT pro jazyk Python použijete k implementaci aplikace simulovaného zařízení a aplikační služby, který chcete spustit úlohu.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/16/2018
ms.author: kgremban
ms.openlocfilehash: 7cbfe289f662987d85f0f2678e4971492ed8cd80
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635164"
---
# <a name="schedule-and-broadcast-jobs-python"></a>Úlohy plán a všesměrového vysílání (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub je plně spravovaná služba, která umožňuje back-end aplikačním k vytvoření a sledování úloh, které naplánovat a aktualizovat miliony zařízení.  Úlohy lze použít pro následující akce:

* Aktualizace požadovaných vlastností
* Aktualizace značky
* Vyvolání metody přímé

Úloha koncepčně, zabalí jednu z těchto akcí a sleduje průběh provádění na skupiny zařízení, který je definován v dotazu twin zařízení.  Back-end aplikace můžete například použít úlohu pro vyvolání metody restartu na 10 000 zařízení, určeného zařízení twin dotazu a naplánovány na datum v budoucnosti.  Aplikace pak můžete sledovat průběh každé z těchto zařízení přijímat a provést metodu restartování.

Další informace o každém z těchto funkcí v těchto článcích:

* Dvojče zařízení a vlastností: [začít pracovat s dvojčata zařízení] [ lnk-get-started-twin] a [kurz: použití dvojici vlastností zařízení][lnk-twin-props]
* Přímé metody: [Příručka vývojáře pro službu IoT Hub - přímé metody] [ lnk-dev-methods] a [kurz: přímé metody][lnk-c2d-methods]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

V tomto kurzu získáte informace o následujících postupech:

* Vytvoření aplikace simulovaného zařízení Python, která má přímý metodu, která umožňuje **lockDoor**, který je možné volat v back-end řešení.
* Vytvořte konzolovou aplikaci Python, která volá **lockDoor** přímá metoda v aplikaci simulovaného zařízení pomocí úlohy a aktualizace požadované vlastnosti pomocí úlohy zařízení.

Na konci tohoto kurzu máte dvě aplikace Python:

**simDevice.py**, který připojuje ke službě IoT hub s identitou zařízení a přijímá **lockDoor** přímá metoda.

**scheduleJobService.py**, která volá metodu přímé v aplikaci simulovaného zařízení a aktualizuje dvojče zařízení požadovaných vlastností pomocí úlohy.

Pro absolvování tohoto kurzu potřebujete:

* [Python 2.x nebo 3.x][lnk-python-download]. Ujistěte se, že používáte 32bitovou, nebo 64bitovou instalaci podle požadavků vašeho nastavení. Po zobrazení výzvy v průběhu instalace nezapomeňte přidat Python do proměnné prostředí pro konkrétní platformu. Pokud používáte Python 2.x, možná bude nutné [nainstalovat nebo upgradovat *pip*, systém pro správu balíčků Pythonu][lnk-install-pip].
* Pokud používáte operační systém Windows, je k povolení používání nativních knihoven DLL z Pythonu potřeba [balíček distribuovatelných součástí Visual C++][lnk-visual-c-redist].
* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)

> [!NOTE]
> **Azure IoT SDK pro jazyk Python** přímo nepodporuje **úlohy** funkce. V tomto kurzu místo toho poskytuje alternativní řešení využívá asynchronní vláken a časovače. Další aktualizace, najdete v článku **služby Client SDK** seznam funkcí na [Azure IoT SDK pro jazyk Python](https://github.com/Azure/azure-iot-sdk-python) stránky. 
> 
> 

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]


## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení
V této části vytvoříte konzolovou aplikaci Python, která reaguje na přímé metodu s názvem cloudem, který aktivuje simulované **lockDoor** metoda.

1. Na příkazovém řádku, spusťte následující příkaz k instalaci **azure-iot zařízení client** balíčku:
   
    ```cmd/sh
    pip install azure-iothub-device-client
    ```

1. Pomocí textového editoru, vytvořte novou **simDevice.py** souboru v pracovním adresáři.

1. Přidejte následující `import` příkazy a proměnné na začátku **simDevice.py** souboru. Nahraďte `deviceConnectionString` připojovacím řetězcem, zařízení, které jste vytvořili výše:
   
    ```python
    import time
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubError, DeviceMethodReturnValue

    METHOD_CONTEXT = 0
    TWIN_CONTEXT = 0
    WAIT_COUNT = 10

    PROTOCOL = IoTHubTransportProvider.MQTT
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

1. Přidejte následující funkce zpětného volání pro zpracování **lockDoor** metoda:
   
    ```python
    def device_method_callback(method_name, payload, user_context):
        if method_name == "lockDoor":
            print ( "Locking Door!" )

            device_method_return_value = DeviceMethodReturnValue()
            device_method_return_value.response = "{ \"Response\": \"lockDoor called successfully\" }"
            device_method_return_value.status = 200
            return device_method_return_value
    ```

1. Přidejte další funkce zpětného volání pro zpracování aktualizace dvojčata zařízení:

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "")
        print ( "Twin callback called with:")
        print ( "payload: %s" % payload )
    ```

1. Přidejte následující kód pro obslužnou rutinu pro registraci **lockDoor** metoda. Zahrnují taky `main` rutiny:
   
    ```python
    def iothub_jobs_sample_run():
        try:
            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)
            client.set_device_method_callback(device_method_callback, METHOD_CONTEXT)
            client.set_device_twin_callback(device_twin_callback, TWIN_CONTEXT)

            print ( "Direct method initialized." )
            print ( "Device twin callback initialized." )
            print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )
        
            while True:
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
        print ( "Starting the IoT Hub Python jobs sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_jobs_sample_run()
    ```

1. Uložte a zavřete **simDevice.py** souboru.

> [!NOTE]
> Za účelem zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu byte měli implementovat zásady opakování (například exponenciální opakování), jak je navrženo v článku [Řešení přechodných chyb][lnk-transient-faults] na webu MSDN.
> 
> 


## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Plán úlohy pro volání přímá metoda a aktualizace vlastností dvojče zařízení
V této části vytvoříte konzolovou aplikaci Python, který iniciuje vzdálené **lockDoor** na zařízení pomocí jiné metody přímé a aktualizovat vlastnosti dvojče zařízení.

1. Na příkazovém řádku, spusťte následující příkaz k instalaci **azure-iot-service-client** balíčku:
   
    ```cmd/sh
    pip install azure-iothub-service-client
    ```

1. Pomocí textového editoru, vytvořte novou **scheduleJobService.py** souboru v pracovním adresáři.

1. Přidejte následující `import` příkazy a proměnné na začátku **scheduleJobService.py** souboru:
   
    ```python
    import sys
    import time
    import threading
    import uuid

    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceTwin, IoTHubDeviceMethod, IoTHubError

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "lockDoor"
    METHOD_PAYLOAD = "{\"lockTime\":\"10m\"}"
    UPDATE_JSON = "{\"properties\":{\"desired\":{\"building\":43,\"floor\":3}}}"
    TIMEOUT = 60
    WAIT_COUNT = 5
    ```

1. Přidejte následující funkce, které se používá k dotazování pro zařízení:
   
    ```python
    def query_condition(device_id):
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)
    
        number_of_devices = 10
        dev_list = iothub_registry_manager.get_device_list(number_of_devices)
    
        for device in range(0, number_of_devices):
            if dev_list[device].deviceId == device_id:
                return 1

        print ( "Device not found" )
        return 0
    ```

1. Přidejte následující metody ke spuštění úloh, které volají přímé twin metoda a zařízení:
   
    ```python
    def device_method_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job: " + str(job_id) )
        time.sleep(wait_time)
    
        if query_condition(device_id):
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)
    
            response = iothub_device_method.invoke(device_id, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)
        
            print ( "" )
            print ( "Direct method " + METHOD_NAME + " called." )
        
    def device_twin_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job " + str(job_id) )
        time.sleep(wait_time)
    
        if query_condition(device_id):
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)
    
            twin_info = iothub_twin_method.update_twin(DEVICE_ID, UPDATE_JSON)
        
            print ( "" )
            print ( "Device twin updated." )
    ```

1. Přidejte následující kód k plánování úloh a aktualizovat stav úlohy. Zahrnují taky `main` rutiny:
   
    ```python
    def iothub_jobs_sample_run():
        try:
            method_thr_id = uuid.uuid4()
            method_thr = threading.Thread(target=device_method_job, args=(method_thr_id, DEVICE_ID, 20, TIMEOUT), kwargs={})
            method_thr.start()
        
            print ( "" )
            print ( "Direct method called with Job Id: " + str(method_thr_id) )
        
            twin_thr_id = uuid.uuid4()
            twin_thr = threading.Thread(target=device_twin_job, args=(twin_thr_id, DEVICE_ID, 10, TIMEOUT), kwargs={})
            twin_thr.start()
        
            print ( "" )
            print ( "Device twin called with Job Id: " + str(twin_thr_id) )
        
            while True:
                print ( "" )
            
                if method_thr.is_alive():
                    print ( "...job " + str(method_thr_id) + " still running." )
                else:
                    print ( "...job " + str(method_thr_id) + " complete." )
            
                if twin_thr.is_alive():
                    print ( "...job " + str(twin_thr_id) + " still running." )
                else:
                    print ( "...job " + str(twin_thr_id) + " complete." )
                
                print ( "Job status posted, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
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
        print ( "Starting the IoT Hub jobs Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_jobs_sample_run()
    ```

1. Uložte a zavřete **scheduleJobService.py** souboru.


## <a name="run-the-applications"></a>Spuštění aplikací
Nyní můžete spustit aplikace.

1. Na příkazovém řádku v pracovním adresáři, spusťte následující příkaz, aby začal přijímat přímá metoda restartování:
   
    ```cmd/sh
    python simDevice.py
    ```

1. Jiné příkazového řádku v pracovním adresáři, spusťte následující příkaz k aktivaci úlohy, které mají zamknout dveře a aktualizovat twin:
   
    ```cmd/sh
    python scheduleJobService.py
    ```

1. Zobrazí zařízení odpovědí na přímá metoda a dvojčata zařízení aktualizaci v konzole.

    ![výstupní zařízení][1]

    ![výstup služby][2]


## <a name="next-steps"></a>Další postup
V tomto kurzu jste použili úlohu při plánování přímá metoda zařízení a aktualizaci vlastností dvojče zařízení.

Chcete-li pokračovat, Začínáme se službou IoT Hub a vzory správy zařízení, jako je vzdálené přes aktualizaci firmwaru letecké, najdete v části:

[Kurz: Jak to provést aktualizaci firmwaru][lnk-fwupdate]

Chcete-li pokračovat, Začínáme se službou IoT Hub, najdete v části [Začínáme se službou Azure IoT Edge][lnk-iot-edge].

[lnk-get-started-twin]: iot-hub-python-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-python-python-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-node-node-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/tree/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-python-download]: https://www.python.org/downloads/
[lnk-visual-c-redist]: http://www.microsoft.com/download/confirmation.aspx?id=48145
[lnk-install-pip]: https://pip.pypa.io/en/stable/installing/
[lnk-transient-faults]: https://docs.microsoft.com/azure/architecture/best-practices/transient-faults

[1]: ./media/iot-hub-python-python-schedule-jobs/1.png
[2]: ./media/iot-hub-python-python-schedule-jobs/2.png
