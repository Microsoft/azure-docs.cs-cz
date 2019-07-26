---
title: Plánování úloh pomocí Azure IoT Hub (Python) | Microsoft Docs
description: Jak naplánovat úlohu Azure IoT Hub k vyvolání přímé metody na více zařízeních. Sady SDK Azure IoT pro Python slouží k implementaci aplikací simulovaného zařízení a aplikace služby ke spuštění úlohy.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/16/2019
ms.author: kgremban
ms.openlocfilehash: f4a7cbb5c4f8f4a019cbf5d63a6f2ffe8092546e
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405893"
---
# <a name="schedule-and-broadcast-jobs-python"></a>Úlohy plánování a vysílání (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub je plně spravovaná služba, která umožňuje aplikacím back-end vytvářet a sledovat úlohy, které naplánují a aktualizují miliony zařízení.  Úlohy lze použít pro následující akce:

* Aktualizace požadovaných vlastností
* Aktualizovat značky
* Vyvolání přímých metod

V koncepčním případě úloha obaluje jednu z těchto akcí a sleduje průběh provádění na základě sady zařízení, která je definována dotazem se zdvojeným zařízením.  Například aplikace back-end může použít úlohu k vyvolání metody restart na zařízeních 10 000, která je určená dotazem typu v budoucnosti a naplánována v budoucím čase.  Tato aplikace pak může sledovat průběh, protože každé zařízení obdrží a spustí metodu restartování.

Další informace o každé z těchto možností najdete v těchto článcích:

* Vlastnosti a vlákna zařízení: [Začínáme s dvojitými zprávami](iot-hub-python-twin-getstarted.md) a [kurzem zařízení: Jak používat zdvojené vlastnosti zařízení](tutorial-device-twins.md)

* Přímé metody: [IoT Hub příručka pro vývojáře – přímé metody](iot-hub-devguide-direct-methods.md) a [kurz: přímé metody](quickstart-control-device-python.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

V tomto kurzu získáte informace o následujících postupech:

* Vytvořte aplikaci simulovaného zařízení v Pythonu, která má přímou metodu, která umožňuje **lockDoor**, kterou může volat back-end řešení.

* Vytvořte konzolovou aplikaci v Pythonu, která zavolá metodu **lockDoor** Direct v aplikaci simulovaného zařízení pomocí úlohy a aktualizuje požadované vlastnosti pomocí úlohy zařízení.

Na konci tohoto kurzu máte dvě aplikace v Pythonu:

**simDevice.py**, který se připojí ke službě IoT Hub s identitou zařízení a přijímá metodu **lockDoor** Direct.

**scheduleJobService.py**, která volá přímou metodu v aplikaci simulovaného zařízení a aktualizuje požadované vlastnosti pro vyplňování zařízení pomocí úlohy.

Pro absolvování tohoto kurzu potřebujete:

* [Python 2. x nebo 3. x](https://www.python.org/downloads/). Ujistěte se, že používáte 32bitovou, nebo 64bitovou instalaci podle požadavků vašeho nastavení. Po zobrazení výzvy v průběhu instalace nezapomeňte přidat Python do proměnné prostředí pro konkrétní platformu. Pokud používáte Python 2.x, možná bude nutné [nainstalovat nebo upgradovat *pip*, systém správy balíčků Pythonu](https://pip.pypa.io/en/stable/installing/).

* Pokud používáte operační systém Windows, je k povolení používání nativních knihoven DLL z Pythonu potřeba [balíček distribuovatelných součástí Visual C++](https://www.microsoft.com/download/confirmation.aspx?id=48145).

* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) .)

> [!NOTE]
> **Sada Azure IoT SDK pro Python** nepodporuje přímo funkce **úloh** . Místo toho tento kurz nabízí alternativní řešení, které využívá asynchronní vlákna a časovače. Další aktualizace najdete na stránce funkce **sady SDK klienta služby** na stránce [Azure IoT SDK pro Python](https://github.com/Azure/azure-iot-sdk-python) . 
>

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení ve službě IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení

V této části vytvoříte konzolovou aplikaci v Pythonu, která reaguje na přímou metodu volanou cloudem, která spustí simulovanou **lockDoor** metodu.

1. Na příkazovém řádku spusťte následující příkaz k instalaci balíčku **Azure-IoT-Device-Client** :

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

2. Pomocí textového editoru vytvořte nový soubor **simDevice.py** v pracovním adresáři.

3. Na začátek souboru `import` **simDevice.py** přidejte následující příkazy a proměnné. Nahraďte `deviceConnectionString` připojovacím řetězcem zařízení, které jste vytvořili výše:

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

4. Přidejte následující zpětné volání funkce pro zpracování metody **lockDoor** :

    ```python
    def device_method_callback(method_name, payload, user_context):
        if method_name == "lockDoor":
            print ( "Locking Door!" )

            device_method_return_value = DeviceMethodReturnValue()
            device_method_return_value.response = "{ \"Response\": \"lockDoor called successfully\" }"
            device_method_return_value.status = 200
            return device_method_return_value
    ```

5. Přidejte další zpětné volání funkce pro zpracování aktualizací s dvojitým voláním zařízení:

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "")
        print ( "Twin callback called with:")
        print ( "payload: %s" % payload )
    ```

6. Přidejte následující kód pro registraci obslužné rutiny pro metodu **lockDoor** . Zahrňte `main` také rutinu:

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

7. Uložte a zavřete soubor **simDevice.py** .

> [!NOTE]
> Za účelem zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu byste měli implementovat zásady opakování (například exponenciální omezení rychlosti), jak je navrženo v článku, [zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT Hub

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Plánování úloh pro volání přímé metody a aktualizace vlastností vlákna zařízení

V této části vytvoříte konzolovou aplikaci v Pythonu, která inicializuje vzdálenou **lockDoor** na zařízení pomocí přímé metody a aktualizuje vlastnosti vlákna zařízení.

1. Na příkazovém řádku spusťte následující příkaz k instalaci balíčku **Azure-IoT-Service-Client** :

    ```cmd/sh
    pip install azure-iothub-service-client
    ```

2. Pomocí textového editoru vytvořte nový soubor **scheduleJobService.py** v pracovním adresáři.

3. Na začátek souboru `import` **scheduleJobService.py** přidejte následující příkazy a proměnné:

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

4. Přidejte následující funkci, která se používá k dotazování na zařízení:

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

5. Přidejte následující metody pro spuštění úloh volajících přímo metodu a zařízení s dvojitým voláním:

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

6. Přidejte následující kód pro naplánování úloh a aktualizaci stavu úlohy. Zahrňte `main` také rutinu:

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

7. Uložte a zavřete soubor **scheduleJobService.py** .

## <a name="run-the-applications"></a>Spuštění aplikací

Nyní můžete spustit aplikace.

1. Na příkazovém řádku v pracovním adresáři spusťte následující příkaz, který zahájí naslouchání přímé metody restartování:

    ```cmd/sh
    python simDevice.py
    ```

2. Na jiném příkazovém řádku ve vašem pracovním adresáři spusťte následující příkaz, který aktivuje úlohy pro uzamknutí dveří a aktualizaci vlákna:
  
    ```cmd/sh
    python scheduleJobService.py
    ```

3. V konzole se zobrazí odpovědi zařízení na přímou metodu a v případě aktualizace s dvojitým zařízením.

    ![Ukázka úlohy IoT Hub 1 – výstup zařízení](./media/iot-hub-python-python-schedule-jobs/sample1-deviceoutput.png)

    ![Ukázka úlohy IoT Hub 2 – výstup zařízení](./media/iot-hub-python-python-schedule-jobs/sample2-deviceoutput.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste použili úlohu k naplánování přímé metody na zařízení a aktualizaci vlastností vlákna zařízení.

Pokud chcete pokračovat v seznámení se IoT Hub a způsoby správy zařízení, jako je například vzdálené prostřednictvím aktualizace firmwaru Air, přečtěte si téma [Jak provést aktualizaci firmwaru](tutorial-firmware-update.md).