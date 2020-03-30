---
title: Začínáme s dvojčaty zařízení Azure IoT Hub (Python) | Dokumenty společnosti Microsoft
description: Jak použít dvojčata zařízení Azure IoT Hub k přidání značek a pak použít dotaz služby IoT Hub. Sady Azure IoT SDK pro Python slouží k implementaci aplikace simulovaných zařízení a aplikace služby, která přidá značky a spustí dotaz služby IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: robinsh
ms.openlocfilehash: c1db7f1a891646ad29f6cae95ddb7e2cf3a42bfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409702"
---
# <a name="get-started-with-device-twins-python"></a>Začínáme s dvojčaty zařízení (Python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Na konci tohoto kurzu budete mít dvě konzolové aplikace Pythonu:

* **AddTagsAndQuery.py**, back-endová aplikace pythonu, která přidává značky a dotazuje dvojčata zařízení.

* **ReportConnectivity.py**, aplikace Pythonu, která simuluje zařízení, které se připojuje k vašemu centru IoT s identitou zařízení vytvořenou dříve a hlásí jeho stav připojení.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

* Zkontrolujte, zda je v bráně firewall otevřený port 8883. Ukázka zařízení v tomto článku používá protokol MQTT, který komunikuje přes port 8883. Tento port může být blokován v některých prostředích podnikové a vzdělávací sítě. Další informace a způsoby, jak tento problém vyřešit, najdete [v tématu připojení k centru IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení v centru IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT hub

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Vytvoření aplikace služby

V této části vytvoříte konzolovou aplikaci Pythonu, která přidá metadata umístění do dvojčete zařízení přidruženého k **vašemu {ID zařízení}**. Potom se dotazuje dvojčata zařízení uložená v centru IoT výběru zařízení umístěných v Redmondu a potom ty, které hlásí mobilní připojení.

1. Ve svém pracovním adresáři otevřete příkazový řádek a nainstalujte **sadu Azure IoT Hub Service SDK pro Python**.

   ```cmd/sh
   pip install azure-iot-hub
   ```

2. Pomocí textového editoru vytvořte nový **soubor AddTagsAndQuery.py.**

3. Přidejte následující kód, který importuje požadované moduly ze sady SDK služby:

   ```python
   import sys
   from time import sleep
   from azure.iot.hub import IoTHubRegistryManager
   from azure.iot.hub.models import Twin, TwinProperties, QuerySpecification, QueryResult
   ```

4. Přidejte následující kód. Nahraďte `[IoTHub Connection String]` připojovacím řetězcem centra IoT, který jste zkopírovali v [části Získání připojovacího řetězce centra IoT hub](#get-the-iot-hub-connection-string). Nahraďte `[Device Id]` ID zařízení, které jste zaregistrovali [v registru nového zařízení v centru IoT](#register-a-new-device-in-the-iot-hub)hub .
  
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

    **Objekt IoTHubRegistryManager** zveřejňuje všechny metody potřebné k interakci s dvojčaty zařízení ze služby. Kód nejprve inicializuje objekt **IoTHubRegistryManager,** potom aktualizuje dvojče zařízení pro **DEVICE_ID**a nakonec spustí dva dotazy. První vybere pouze dvojčata zařízení umístěných v závodě **Redmond43** a druhá zpřesní dotaz a vybere pouze zařízení, která jsou také připojena prostřednictvím mobilní sítě.

6. Přidejte následující kód na konci **AddTagsAndQuery.py** k implementaci **funkce iothub_service_sample_run:**

    ```python
    if __name__ == '__main__':
        print("Starting the Python IoT Hub Device Twin service sample...")
        print()

        iothub_service_sample_run()
    ```

7. Spusťte aplikaci s:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Ve výsledcích dotazu byste měli vidět jedno zařízení s žádostí o všechna zařízení umístěná v **Redmond43** a žádné pro dotaz, který omezuje výsledky na zařízení, která používají mobilní síť.

    ![první dotaz zobrazující všechna zařízení v Redmondu](./media/iot-hub-python-twin-getstarted/service-1.png)

V další části vytvoříte aplikaci pro zařízení, která hlásí informace o připojení a změní výsledek dotazu v předchozí části.

## <a name="create-the-device-app"></a>Vytvoření aplikace pro zařízení

V této části vytvoříte konzolovou aplikaci Pythonu, která se připojí k vašemu rozbočovači jako **{ID zařízení}** a pak aktualizuje ohlášené vlastnosti dvojčete zařízení tak, aby obsahovaly informace, které jsou připojeny pomocí mobilní sítě.

1. Z příkazového řádku ve vašem pracovním adresáři nainstalujte **sadku Azure IoT Hub Device SDK pro Python**:

    ```cmd/sh
    pip install azure-iot-device
    ```

2. Pomocí textového editoru vytvořte nový **soubor ReportConnectivity.py.**

3. Přidejte následující kód pro import požadovaných modulů ze sady SDK zařízení:

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient
    ```

4. Přidejte následující kód. `[IoTHub Device Connection String]` Nahraďte zástupnou hodnotu připojovacím řetězcem zařízení, který jste zkopírovali v [části Registrovat nové zařízení v centru IoT](#register-a-new-device-in-the-iot-hub)hub .

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"
    ```

5. Přidejte do souboru **ReportConnectivity.py** následující kód k implementaci funkce dvojčat zařízení:

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

    Objekt **IoTHubModuleClient** zveřejňuje všechny metody, které potřebujete k interakci s dvojčaty zařízení ze zařízení. Předchozí kód po inicializaci objektu **IoTHubModuleClient** načte dvojče zařízení pro vaše zařízení a aktualizuje jeho ohlášenou vlastnost informacemi o připojení.

6. Přidejte následující kód na konci **ReportConnectivity.py** k implementaci **funkce iothub_client_sample_run:**

    ```python
    if __name__ == '__main__':
        print ( "Starting the Python IoT Hub Device Twin device sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Spuštění aplikace zařízení:

    ```cmd/sh
    python ReportConnectivity.py
    ```

    Měli byste vidět potvrzení dvojčete zařízení hlášené vlastnosti byly aktualizovány.

    ![aktualizace ohlášených vlastností z aplikace zařízení](./media/iot-hub-python-twin-getstarted/device-1.png)

8. Nyní, když zařízení ohlásilo informace o připojení, mělo by se zobrazit v obou dotazech. Vraťte se a znovu spusťte dotazy:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Tentokrát by se **vaše {ID zařízení}** mělo objevit v obou výsledcích dotazu.

    ![druhý dotaz v aplikaci služby](./media/iot-hub-python-twin-getstarted/service-2.png)

    V aplikaci pro zařízení se zobrazí potvrzení, že byla přijata požadovaná oprava dvojčete vlastností odeslaná servisní aplikací.

    ![příjem požadovaných vlastností v aplikaci zařízení](./media/iot-hub-python-twin-getstarted/device-2.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nakonfigurovali novou službu IoT Hub na webu Azure Portal a potom jste vytvořili identitu zařízení v registru identit ve službě IoT Hub. Přidali jste metadata zařízení jako značky z back-endové aplikace a napsali jste simulovanou aplikaci zařízení, která nahlásí informace o připojení zařízení v dvojčeti zařízení. Také jste se dozvěděli, jak dotaz na tyto informace pomocí registru.

Pomocí následujících zdrojů se dozvíte, jak:

* Odesílejte telemetrii ze zařízení pomocí výukového [programu Začínáme s ioT hubem.](quickstart-send-telemetry-python.md)

* Konfigurace zařízení pomocí požadovaných vlastností dvojčete zařízení s [účelem použití požadovaných vlastností ke konfiguraci](tutorial-device-twins.md) kurzu zařízení.

* Ovládejte zařízení interaktivně (například zapnutí ventilátoru z uživatelem řízené aplikace) pomocí kurzu [Použití přímých metod.](quickstart-control-device-python.md)
