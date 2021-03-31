---
title: Plánování úloh pomocí Azure IoT Hub (Python) | Microsoft Docs
description: Jak naplánovat úlohu Azure IoT Hub k vyvolání přímé metody na více zařízeních. Sady SDK Azure IoT pro Python slouží k implementaci aplikací simulovaného zařízení a aplikace služby ke spuštění úlohy.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: robinsh
ms.custom: devx-track-python
ms.openlocfilehash: 733e3be21a1a1305b5c7947de1ae54ddce5e0d2f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87876678"
---
# <a name="schedule-and-broadcast-jobs-python"></a>Úlohy plánování a vysílání (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub je plně spravovaná služba, která umožňuje aplikacím back-end vytvářet a sledovat úlohy, které naplánují a aktualizují miliony zařízení.  Úlohy lze použít pro následující akce:

* Aktualizace požadovaných vlastností
* Aktualizovat značky
* Vyvolání přímých metod

V koncepčním případě úloha obaluje jednu z těchto akcí a sleduje průběh provádění na základě sady zařízení, která je definována dotazem se zdvojeným zařízením.  Například aplikace back-end může použít úlohu k vyvolání metody restart na zařízeních 10 000, která je určená dotazem typu v budoucnosti a naplánována v budoucím čase.  Tato aplikace pak může sledovat průběh, protože každé zařízení obdrží a spustí metodu restartování.

Další informace o každé z těchto možností najdete v těchto článcích:

* Vlákna a vlastnosti zařízení: [Začínáme s dvojitými zprávami](iot-hub-python-twin-getstarted.md) a [kurzem zařízení: jak používat vlastnosti se zdvojeným zařízením](tutorial-device-twins.md)

* Přímé metody: [IoT Hub příručka pro vývojáře – přímé metody](iot-hub-devguide-direct-methods.md) a [kurz: přímé metody](quickstart-control-device-python.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

V tomto kurzu získáte informace o následujících postupech:

* Vytvořte aplikaci simulovaného zařízení v Pythonu, která má přímou metodu, která umožňuje **lockDoor**, kterou může volat back-end řešení.

* Vytvořte konzolovou aplikaci v Pythonu, která zavolá metodu **lockDoor** Direct v aplikaci simulovaného zařízení pomocí úlohy a aktualizuje požadované vlastnosti pomocí úlohy zařízení.

Na konci tohoto kurzu máte dvě aplikace v Pythonu:

**simDevice.py**, který se připojí ke službě IoT Hub s identitou zařízení a přijímá metodu **lockDoor** Direct.

**scheduleJobService.py**, která volá přímou metodu v aplikaci simulovaného zařízení a aktualizuje požadované vlastnosti pro vyplňování zařízení pomocí úlohy.

> [!NOTE]
> **Sada Azure IoT SDK pro Python** nepodporuje přímo funkce **úloh** . Místo toho tento kurz nabízí alternativní řešení, které využívá asynchronní vlákna a časovače. Další aktualizace najdete na stránce funkce **sady SDK klienta služby** na stránce [Azure IoT SDK pro Python](https://github.com/Azure/azure-iot-sdk-python) .
>

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení ve službě IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení

V této části vytvoříte konzolovou aplikaci v Pythonu, která reaguje na přímou metodu volanou cloudem, která spustí simulovanou **lockDoor** metodu.

1. Na příkazovém řádku spusťte následující příkaz pro instalaci balíčku **Azure-IoT-Device** :

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Pomocí textového editoru vytvořte nový soubor **simDevice.py** v pracovním adresáři.

3. Na `import` začátek souboru **simDevice.py** přidejte následující příkazy a proměnné. Nahraďte `deviceConnectionString` připojovacím řetězcem zařízení, které jste vytvořili výše:

    ```python
    import threading
    import time
    from azure.iot.device import IoTHubDeviceClient, MethodResponse

    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Přidejte následující zpětné volání funkce pro zpracování metody **lockDoor** :

    ```python
    def lockdoor_listener(client):
        while True:
            # Receive the direct method request
            method_request = client.receive_method_request("lockDoor")  # blocking call
            print( "Locking Door!" )

            resp_status = 200
            resp_payload = {"Response": "lockDoor called successfully"}
            method_response = MethodResponse(method_request.request_id, resp_status, resp_payload)
            client.send_method_response(method_response)
    ```

5. Přidejte další zpětné volání funkce pro zpracování aktualizací s dvojitým voláním zařízení:

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print ("")
            print ("Twin desired properties patch received:")
            print (patch)
    ```

6. Přidejte následující kód pro registraci obslužné rutiny pro metodu **lockDoor** . Zahrňte také `main` rutinu:

    ```python
    def iothub_jobs_sample_run():
        try:
            client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)

            print( "Beginning to listen for 'lockDoor' direct method invocations...")
            lockdoor_listener_thread = threading.Thread(target=lockdoor_listener, args=(client,))
            lockdoor_listener_thread.daemon = True
            lockdoor_listener_thread.start()

            # Begin listening for updates to the Twin desired properties
            print ( "Beginning to listen for updates to Twin desired properties...")
            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()
            
            while True:
                time.sleep(1000)

        except KeyboardInterrupt:
            print ( "IoTHubDeviceClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python jobs sample..." )
        print ( "IoTHubDeviceClient waiting for commands, press Ctrl-C to exit" )

        iothub_jobs_sample_run()
    ```

7. Uložte a zavřete soubor **simDevice.py** .

> [!NOTE]
> Za účelem zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu byste měli implementovat zásady opakování (například exponenciální omezení rychlosti), jak je navrženo v článku, [zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT Hub

V tomto článku vytvoříte back-end službu, která vyvolá přímou metodu na zařízení a aktualizuje její dvojitou hodnotu. Služba potřebuje ke volání přímé metody na zařízení oprávnění **služby Connect** . Služba také potřebuje oprávnění **pro čtení** a **zápis** do registru ke čtení a zápisu registru identit. Nejsou k dispozici žádné výchozí zásady sdíleného přístupu, které obsahují pouze tato oprávnění, takže je třeba ji vytvořit.

Chcete-li vytvořit zásadu sdíleného přístupu, která uděluje oprávnění **k zápisu služby**, **čtení registru** a **zápisu do registru** , a k získání připojovacího řetězce pro tuto zásadu, postupujte podle následujících kroků:

1. Otevřete Centrum IoT v [Azure Portal](https://portal.azure.com). Nejjednodušší způsob, jak se dostat do služby IoT Hub, je vybrat **skupiny prostředků**, vybrat skupinu prostředků, ve které se nachází vaše centrum IoT, a pak ze seznamu prostředků vybrat centrum IoT.

2. V levém podokně Centra IoT vyberte **zásady sdíleného přístupu**.

3. V horní nabídce nad seznamem zásad vyberte **Přidat**.

4. V podokně **Přidat zásady sdíleného přístupu** zadejte popisný název zásady. například: *serviceAndRegistryReadWrite*. V části **oprávnění** vyberte **Služba připojení** a **zápis do registru** (při výběru **zápisu do registru** se automaticky vybere možnost **čtení** z registru). Potom vyberte **Vytvořit**.

    ![Ukázat, jak přidat nové zásady sdíleného přístupu](./media/iot-hub-python-python-schedule-jobs/add-policy.png)

5. Zpátky v podokně **zásady sdíleného přístupu** vyberte ze seznamu zásad novou zásadu.

6. V části **sdílené přístupové klíče** vyberte ikonu kopírování pro **připojovací řetězec – primární klíč** a uložte hodnotu.

    ![Zobrazit způsob načtení připojovacího řetězce](./media/iot-hub-python-python-schedule-jobs/get-connection-string.png)

Další informace o zásadách a oprávněních sdíleného přístupu IoT Hub najdete v tématu [řízení přístupu a oprávnění](./iot-hub-devguide-security.md#access-control-and-permissions).

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Plánování úloh pro volání přímé metody a aktualizace vlastností vlákna zařízení

V této části vytvoříte konzolovou aplikaci v Pythonu, která iniciuje vzdálenou **lockDoor** na zařízení pomocí přímé metody a také aktualizuje požadované vlastnosti pro vynechání zařízení.

1. Na příkazovém řádku spusťte následující příkaz pro instalaci balíčku **Azure-IoT-Hub** :

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. Pomocí textového editoru vytvořte nový soubor **scheduleJobService.py** v pracovním adresáři.

3. Na `import` začátek souboru **scheduleJobService.py** přidejte následující příkazy a proměnné. `{IoTHubConnectionString}`Zástupný symbol nahraďte připojovacím řetězcem IoT Hub, který jste zkopírovali dříve v [části získání připojovacího řetězce centra IoT Hub](#get-the-iot-hub-connection-string). `{deviceId}`Zástupný symbol nahraďte ID zařízení, které jste zaregistrovali v [části registrace nového zařízení ve službě IoT Hub](#register-a-new-device-in-the-iot-hub):

    ```python
    import sys
    import time
    import threading
    import uuid

    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties, CloudToDeviceMethod, CloudToDeviceMethodResult, QuerySpecification, QueryResult

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "lockDoor"
    METHOD_PAYLOAD = "{\"lockTime\":\"10m\"}"
    UPDATE_PATCH = {"building":43,"floor":3}
    TIMEOUT = 60
    WAIT_COUNT = 5
    ```

4. Přidejte následující funkci, která se používá k dotazování na zařízení:

    ```python
    def query_condition(iothub_registry_manager, device_id):

        query_spec = QuerySpecification(query="SELECT * FROM devices WHERE deviceId = '{}'".format(device_id))
        query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 1)

        return len(query_result.items)
    ```

5. Přidejte následující metody pro spuštění úloh volajících přímo metodu a zařízení s dvojitým voláním:

    ```python
    def device_method_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job: " + str(job_id) )
        time.sleep(wait_time)

        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)


        if query_condition(iothub_registry_manager, device_id):
            deviceMethod = CloudToDeviceMethod(method_name=METHOD_NAME, payload=METHOD_PAYLOAD)

            response = iothub_registry_manager.invoke_device_method(DEVICE_ID, deviceMethod)

            print ( "" )
            print ( "Direct method " + METHOD_NAME + " called." )

    def device_twin_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job " + str(job_id) )
        time.sleep(wait_time)

        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        if query_condition(iothub_registry_manager, device_id):

            twin = iothub_registry_manager.get_twin(DEVICE_ID)
            twin_patch = Twin(properties= TwinProperties(desired=UPDATE_PATCH))
            twin = iothub_registry_manager.update_twin(DEVICE_ID, twin_patch, twin.etag)

            print ( "" )
            print ( "Device twin updated." )
    ```

6. Přidejte následující kód pro naplánování úloh a aktualizaci stavu úlohy. Zahrňte také `main` rutinu:

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

        except Exception as ex:
            print ( "" )
            print ( "Unexpected error {0}" % ex )
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
