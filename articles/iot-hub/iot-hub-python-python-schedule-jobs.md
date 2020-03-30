---
title: Plánování úloh s Azure IoT Hub (Python) | Dokumenty společnosti Microsoft
description: Jak naplánovat úlohu služby Azure IoT Hub k vyvolání přímé metody na více zařízeních. Pomocí sad Azure IoT SDK pro Python implementovat aplikace simulovaných zařízení a aplikace služby ke spuštění úlohy.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: robinsh
ms.openlocfilehash: 1d721e89534c09a5572e5674796f28355f652165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527397"
---
# <a name="schedule-and-broadcast-jobs-python"></a>Plánování a vysílání úloh (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub je plně spravovaná služba, která umožňuje back-endové aplikaci vytvářet a sledovat úlohy, které plánují a aktualizují miliony zařízení.  Úlohy lze použít pro následující akce:

* Aktualizace požadovaných vlastností
* Aktualizovat značky
* Vyvolat přímé metody

Koncepčně úloha zabalí jednu z těchto akcí a sleduje průběh provádění proti sadě zařízení, která je definována dotazem dvojčete zařízení.  Například back-endová aplikace může použít úlohu k vyvolání metody restartování na 10 000 zařízeních, určená dotazem dvojčete zařízení a naplánovaná v budoucnu.  Tato aplikace pak můžete sledovat průběh jako každé z těchto zařízení přijímat a spouštět metodu restartování.

Další informace o jednotlivých funkcích najdete v těchto článcích:

* Dvojče zařízení a vlastnosti: [Začínáme s dvojčaty zařízení](iot-hub-python-twin-getstarted.md) a [kurz: Jak používat vlastnosti dvojčete zařízení](tutorial-device-twins.md)

* Přímé metody: [Průvodce vývojářem ioT Hubu - přímé metody](iot-hub-devguide-direct-methods.md) a [výuka: přímé metody](quickstart-control-device-python.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

V tomto kurzu získáte informace o následujících postupech:

* Vytvořte aplikaci simulovaného zařízení Pythonu, která má přímou metodu, která umožňuje **lockDoor**, který může být volán back-endem řešení.

* Vytvořte konzolovou aplikaci Pythonu, která volá přímou metodu **lockDoor** v aplikaci simulovaného zařízení pomocí úlohy a aktualizuje požadované vlastnosti pomocí úlohy zařízení.

Na konci tohoto kurzu máte dvě aplikace Pythonu:

**simDevice.py**, který se připojuje k centru IoT s identitou zařízení a přijímá **lockDoor** přímou metodu.

**scheduleJobService.py**, který volá přímou metodu v aplikaci simulovaného zařízení a aktualizuje požadované vlastnosti dvojčete zařízení pomocí úlohy.

> [!NOTE]
> Sada **Azure IoT SDK pro Python** přímo nepodporuje funkce **úloh.** Místo toho tento kurz nabízí alternativní řešení využívající asynchronní vlákna a časovače. Další aktualizace najdete v seznamu funkcí **sady Service Client SDK** na stránce [Azure IoT SDK for Python.](https://github.com/Azure/azure-iot-sdk-python)
>

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení v centru IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Vytvoření aplikace simulovaného zařízení

V této části vytvoříte konzolovou aplikaci Pythonu, která reaguje na přímou metodu volanou cloudem, která aktivuje simulovanou metodu **lockDoor.**

1. Na příkazovém řádku spusťte následující příkaz k instalaci balíčku **azure-iot-device:**

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Pomocí textového editoru vytvořte nový **soubor simDevice.py** v pracovním adresáři.

3. Na začátek `import` **souboru simDevice.py** přidejte následující příkazy a proměnné. Nahraďte `deviceConnectionString` připojovacím řetězcem zařízení, které jste vytvořili výše:

    ```python
    import threading
    import time
    from azure.iot.device import IoTHubDeviceClient, MethodResponse

    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Přidejte následující funkci zpětného volání pro zpracování **lockDoor** metoda:

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

5. Přidejte další funkce zpětnévolání pro zpracování aktualizace dvojčat zařízení:

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print ("")
            print ("Twin desired properties patch received:")
            print (patch)
    ```

6. Přidejte následující kód pro registraci obslužné rutiny pro metodu **lockDoor.** Také zahrnují `main` rutinní:

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

7. Uložte a zavřete **soubor simDevice.py.**

> [!NOTE]
> Za účelem zjednodušení tento kurz neimplementuje žádné zásady opakování. V produkčním kódu byste měli implementovat zásady opakování (například exponenciální backoff), jak je navrženo v článku [Přechodné zpracování chyb](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT hub

V tomto článku vytvoříte back-endovou službu, která vyvolá přímou metodu na zařízení a aktualizuje dvojče zařízení. Služba potřebuje oprávnění **připojení služby** k volání přímé metody na zařízení. Služba také potřebuje oprávnění **ke čtení a** **zápisu registru** k čtení a zápisu registru identity. Neexistuje žádná výchozí zásada sdíleného přístupu, která obsahuje pouze tato oprávnění, takže je třeba ji vytvořit.

Chcete-li vytvořit zásadu sdíleného přístupu, která uděluje oprávnění **služby connect**, **číst v registru**a získat připojovací řetězec pro tuto zásadu, postupujte takto: **registry write**

1. Otevřete své centrum IoT hub na [webu Azure Portal](https://portal.azure.com). Nejjednodušší způsob, jak se dostat do centra IoT, je vybrat **skupiny prostředků**, vybrat skupinu prostředků, kde se nachází vaše centrum IoT, a pak vybrat centrum IoT hub ze seznamu prostředků.

2. V levém podokně centra IoT hub vyberte **Zásady sdíleného přístupu**.

3. V horní nabídce nad seznamem zásad vyberte **Přidat**.

4. V podokně **Přidat zásady sdíleného přístupu** zadejte popisný název zásady. například: *serviceAndRegistryReadWrite*. V části **Oprávnění**vyberte **možnost Připojení služby** a **zápis registru** **(čtení registru** je automaticky vybráno, když vyberete zápis **registru).** Pak vyberte **Vytvořit**.

    ![Ukázka přidání nové zásady sdíleného přístupu](./media/iot-hub-python-python-schedule-jobs/add-policy.png)

5. V podokně **Zásady sdíleného přístupu** vyberte novou zásadu ze seznamu zásad.

6. V části **Sdílené přístupové klávesy**vyberte ikonu kopírování **pro připojovací řetězec – primární klíč** a uložte hodnotu.

    ![Zobrazit, jak načíst připojovací řetězec](./media/iot-hub-python-python-schedule-jobs/get-connection-string.png)

Další informace o zásadách a oprávněních sdíleného přístupu centra IoT Hub najdete [v tématu Řízení přístupu a oprávnění](./iot-hub-devguide-security.md#access-control-and-permissions).

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Naplánujte úlohy pro volání přímé metody a aktualizaci vlastností dvojčete zařízení

V této části vytvoříte konzolovou aplikaci Pythonu, která iniciuje vzdálené **lockDoor** na zařízení pomocí přímé metody a také aktualizuje požadované vlastnosti dvojčete zařízení.

1. Na příkazovém řádku spusťte následující příkaz pro instalaci balíčku **azure-iot-hub:**

    ```cmd/sh
    pip install azure-iot-hub
    ```

2. Pomocí textového editoru vytvořte nový **soubor scheduleJobService.py** v pracovním adresáři.

3. Na začátek `import` **souboru scheduleJobService.py** přidejte následující příkazy a proměnné. Nahraďte `{IoTHubConnectionString}` zástupný symbol připojovacím řetězcem centra IoT, který jste dříve zkopírovali v [připojovacím řetězci Centra IoT](#get-the-iot-hub-connection-string). Nahraďte `{deviceId}` zástupný symbol ID zařízení, které jste [zaregistrovali, zaregistrujte nové zařízení v centru IoT](#register-a-new-device-in-the-iot-hub):

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

5. Přidejte následující metody pro spuštění úloh, které volají přímou metodu a dvojče zařízení:

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

6. Přidejte následující kód pro naplánování úloh a aktualizaci stavu úlohy. Také zahrnují `main` rutinní:

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

7. Uložte a zavřete **soubor scheduleJobService.py.**

## <a name="run-the-applications"></a>Spuštění aplikací

Nyní můžete spustit aplikace.

1. Na příkazovém řádku v pracovním adresáři spusťte následující příkaz a začněte naslouchat metodě přímého restartování:

    ```cmd/sh
    python simDevice.py
    ```

2. Na jiném příkazovém řádku v pracovním adresáři spusťte následující příkaz, který spustí úlohy, které zamknou dveře a aktualizují dvojče:
  
    ```cmd/sh
    python scheduleJobService.py
    ```

3. Zobrazí se odpovědi zařízení na přímou metodu a dvojčata zařízení aktualizovat v konzole.

    ![Ukázka úlohy ioT hubu 1 -- výstup zařízení](./media/iot-hub-python-python-schedule-jobs/sample1-deviceoutput.png)

    ![Ukázka úlohy ioT Hub 2-- výstup zařízení](./media/iot-hub-python-python-schedule-jobs/sample2-deviceoutput.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste použili úlohu k naplánování přímé metody do zařízení a aktualizace vlastností dvojčete zařízení.

Chcete-li pokračovat v začínáme s IoT Hub a vzory správy zařízení, jako je například vzdálené přes aktualizaci firmwaru vzduchu, přečtěte si informace [o tom, jak provést aktualizaci firmwaru](tutorial-firmware-update.md).