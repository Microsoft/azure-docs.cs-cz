---
title: Začínáme s neznámkou zařízení s Azure IoT Hub (Python) | Microsoft Docs
description: Jak používat vlákna v zařízeních Azure IoT Hub k přidávání značek a k následnému použití dotazu IoT Hub. Sady SDK Azure IoT pro Python můžete použít k implementaci aplikace simulovaného zařízení a aplikace služby, která přidá značky a spustí dotaz IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python
ms.openlocfilehash: 12b1d083ae1481f7c8b5fe60cac9156a56aeaa0a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87875468"
---
# <a name="get-started-with-device-twins-python"></a>Začínáme se zdvojením zařízení (Python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Na konci tohoto kurzu budete mít dvě konzolové aplikace Python:

* **AddTagsAndQuery.py**– back-endové aplikace v Pythonu, které přidávají značky a dotaz na vlákna zařízení.

* **ReportConnectivity.py**, aplikace v Pythonu, která simuluje zařízení, které se připojuje ke službě IoT Hub s dříve vytvořenou identitou zařízení a oznamuje její podmínku připojení.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Ujistěte se, že je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokovaný v některých podnikových a vzdělávacích prostředích sítě. Další informace a způsoby, jak tento problém obejít, najdete v tématu [připojení k IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení ve službě IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT Hub

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Vytvoření aplikace služby

V této části vytvoříte konzolovou aplikaci v Pythonu, která přidá metadata umístění do vlákna zařízení přidruženého k vašemu **{ID zařízení}**. Pak se dotazuje na vlákna uložená ve službě IoT Hub a vybere zařízení umístěná v Redmond a pak ty, které vytvářejí mobilní připojení.

1. V pracovním adresáři otevřete příkazový řádek a nainstalujte **sadu Azure IoT Hub Service SDK pro Python**.

   ```cmd/sh
   pip install azure-iot-hub
   ```

2. Pomocí textového editoru vytvořte nový soubor **AddTagsAndQuery.py** .

3. Přidejte následující kód, který importuje požadované moduly ze sady SDK služby:

   ```python
   import sys
   from time import sleep
   from azure.iot.hub import IoTHubRegistryManager
   from azure.iot.hub.models import Twin, TwinProperties, QuerySpecification, QueryResult
   ```

4. Přidejte následující kód. Nahraďte `[IoTHub Connection String]` připojovacím řetězcem centra IoT, který jste zkopírovali v [části získání připojovacího řetězce centra IoT Hub](#get-the-iot-hub-connection-string). Nahraďte `[Device Id]` ID zařízení, které jste zaregistrovali v [části registrace nového zařízení ve službě IoT Hub](#register-a-new-device-in-the-iot-hub).
  
    ```python
    IOTHUB_CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"
    ```

5. Do souboru **AddTagsAndQuery.py** přidejte následující kód:

    ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(IOTHUB_CONNECTION_STRING)

            new_tags = {
                    'location' : {
                        'region' : 'US',
                        'plant' : 'Redmond43'
                    }
                }

            twin = iothub_registry_manager.get_twin(DEVICE_ID)
            twin_patch = Twin(tags=new_tags, properties= TwinProperties(desired={'power_level' : 1}))
            twin = iothub_registry_manager.update_twin(DEVICE_ID, twin_patch, twin.etag)

            # Add a delay to account for any latency before executing the query
            sleep(1)

            query_spec = QuerySpecification(query="SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'")
            query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 100)
            print("Devices in Redmond43 plant: {}".format(', '.join([twin.device_id for twin in query_result.items])))

            print()

            query_spec = QuerySpecification(query="SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity = 'cellular'")
            query_result = iothub_registry_manager.query_iot_hub(query_spec, None, 100)
            print("Devices in Redmond43 plant using cellular network: {}".format(', '.join([twin.device_id for twin in query_result.items])))

        except Exception as ex:
            print("Unexpected error {0}".format(ex))
            return
        except KeyboardInterrupt:
            print("IoT Hub Device Twin service sample stopped")
    ```

    Objekt **IoTHubRegistryManager** zpřístupňuje všechny metody potřebné pro interakci se zařízeními ze služby. Kód nejprve inicializuje objekt **IoTHubRegistryManager** , pak aktualizuje dvojitou dobu zařízení pro **DEVICE_ID** a nakonec spustí dva dotazy. Nejdřív se vybere jenom zařízení, která se nacházejí v **Redmond43** , a druhá ho podrobná a vybere jenom zařízení, která jsou taky připojená přes mobilní síť.

6. Přidejte následující kód na konec  **AddTagsAndQuery.py** k implementaci funkce **iothub_service_sample_run** :

    ```python
    if __name__ == '__main__':
        print("Starting the Python IoT Hub Device Twin service sample...")
        print()

        iothub_service_sample_run()
    ```

7. Spusťte aplikaci pomocí:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Ve výsledcích dotazu pro všechna zařízení umístěná v **Redmond43** byste měli vidět jedno zařízení, které pro dotaz neomezuje výsledky na zařízení, která používají mobilní síť.

    ![první dotaz zobrazující všechna zařízení v Redmond](./media/iot-hub-python-twin-getstarted/service-1.png)

V další části vytvoříte aplikaci pro zařízení, která oznamuje informace o připojení a mění výsledek dotazu v předchozí části.

## <a name="create-the-device-app"></a>Vytvoření aplikace zařízení

V této části vytvoříte konzolovou aplikaci v Pythonu, která se připojí k vašemu rozbočovači jako vaše **{ID zařízení}**, a pak aktualizuje nahlášené vlastnosti, které jsou v zařízení nahlášeny, aby obsahovaly informace, které jsou připojené pomocí mobilní sítě.

1. Z příkazového řádku v pracovním adresáři nainstalujte **sadu Azure IoT Hub Device SDK pro Python**:

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Pomocí textového editoru vytvořte nový soubor **ReportConnectivity.py** .

3. Přidejte následující kód, který importuje požadované moduly ze sady SDK pro zařízení:

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient
    ```

4. Přidejte následující kód. Nahraďte `[IoTHub Device Connection String]` hodnotu zástupného symbolu připojovacím řetězcem zařízení, který jste zkopírovali v [části registrace nového zařízení ve službě IoT Hub](#register-a-new-device-in-the-iot-hub).

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    ```

5. Do souboru **ReportConnectivity.py** přidejte následující kód, který implementuje funkci dopředné funkce zařízení:

    ```python
    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("Twin patch received:")
            print(patch)

    def iothub_client_init():
        client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)
        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            # Send reported 
            print ( "Sending data as reported property..." )
            reported_patch = {"connectivity": "cellular"}
            client.patch_twin_reported_properties(reported_patch)
            print ( "Reported properties updated" )

            while True:
                time.sleep(1000000)
        except KeyboardInterrupt:
            print ( "IoT Hub Device Twin device sample stopped" )
    ```

    Objekt **IoTHubModuleClient** zpřístupňuje všechny metody, které vyžadujete pro interakci se zařízeními ze zařízení. Předchozí kód po inicializaci objektu **IoTHubModuleClient** načte do zařízení pro vaše zařízení vlákna a aktualizuje jeho hlášenou vlastnost informacemi o připojení.

6. Přidejte následující kód na konec  **ReportConnectivity.py** k implementaci funkce **iothub_client_sample_run** :

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub Device Twin device sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Spusťte aplikaci zařízení:

    ```cmd/sh
    python ReportConnectivity.py
    ```

    Mělo by se zobrazit potvrzení, že nedokončené hlášené vlastnosti zařízení byly aktualizovány.

    ![aktualizace hlášených vlastností z aplikace zařízení](./media/iot-hub-python-twin-getstarted/device-1.png)

8. Teď, když zařízení oznámilo informace o připojení, by se mělo zobrazit v obou dotazech. Vraťte se zpět a znovu spusťte dotazy:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Tentokrát by **{ID zařízení}** mělo být ve výsledcích dotazu.

    ![druhý dotaz na aplikaci služby](./media/iot-hub-python-twin-getstarted/service-2.png)

    V aplikaci pro zařízení se zobrazí potvrzení, že byla přijata požadovaná vlastnost s dvojitou opravou odeslanou aplikací služby.

    ![přijímání požadovaných vlastností v aplikaci pro zařízení](./media/iot-hub-python-twin-getstarted/device-2.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali novou službu IoT Hub na webu Azure Portal a potom jste vytvořili identitu zařízení v registru identit ve službě IoT Hub. Metadata zařízení jste přidali jako značky z back-endové aplikace a zapsali jste aplikaci simulovaného zařízení, která oznamuje informace o připojení zařízení v zařízení. Zjistili jste také, jak zadat dotaz na tyto informace pomocí registru.

Pomocí následujících zdrojů se naučíte:

* Pomocí kurzu [Začínáme s IoT Hub](quickstart-send-telemetry-python.md) odešlete telemetrii ze zařízení.

* V kurzu [použití požadovaných vlastností ke konfiguraci zařízení](tutorial-device-twins.md) nakonfigurujte zařízení pomocí požadovaných vlastností pro vyplňování zařízení.

* Interaktivní řízení zařízení (například zapnutí ventilátoru z aplikace řízené uživatelem) pomocí kurzu [použití přímých metod](quickstart-control-device-python.md) .
