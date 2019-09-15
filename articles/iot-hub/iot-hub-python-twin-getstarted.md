---
title: Začínáme s neznámkou zařízení s Azure IoT Hub (Python) | Microsoft Docs
description: Jak používat vlákna v zařízeních Azure IoT Hub k přidávání značek a k následnému použití dotazu IoT Hub. Sady SDK Azure IoT pro Python můžete použít k implementaci aplikace simulovaného zařízení a aplikace služby, která přidá značky a spustí dotaz IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: robinsh
ms.openlocfilehash: 565330528638bb6c8e0458a9761e2cf9fa4e3d2a
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001485"
---
# <a name="get-started-with-device-twins-python"></a>Začínáme se zdvojením zařízení (Python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Na konci tohoto kurzu budete mít dvě konzolové aplikace Python:

* **AddTagsAndQuery.py**– back-endové aplikace v Pythonu, které přidávají značky a dotaz na vlákna zařízení.

* **ReportConnectivity.py**, aplikace v Pythonu, která simuluje zařízení, které se připojuje ke službě IoT Hub s dříve vytvořenou identitou zařízení a oznamuje její podmínku připojení.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registrace nového zařízení ve službě IoT Hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT Hub

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>Vytvoření aplikace služby

V této části vytvoříte konzolovou aplikaci v Pythonu, která přidá metadata umístění do vlákna zařízení přidruženého k vašemu **{ID zařízení}** . Pak se dotazuje na vlákna uložená ve službě IoT Hub a vybere zařízení umístěná v Redmond a pak ty, které vytvářejí mobilní připojení.

1. V pracovním adresáři otevřete příkazový řádek a nainstalujte **sadu Azure IoT Hub Service SDK pro Python**.

   ```cmd/sh
   pip install azure-iothub-service-client
   ```

   > [!NOTE]
   > Balíček PIP pro Azure-iothub-Service-Client je momentálně dostupný jenom pro operační systém Windows. Informace pro Linux a Mac OS najdete v oddílech týkajících se Linux a Mac OS na stránce [Příprava vývojového prostředí pro Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) .
   >

2. Pomocí textového editoru vytvořte nový soubor **AddTagsAndQuery.py** .

3. Přidejte následující kód, který importuje požadované moduly ze sady SDK služby:

   ```python
   import sys
   import iothub_service_client
   from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
   from iothub_service_client import IoTHubDeviceTwin, IoTHubError
   ```

4. Přidejte následující kód. Nahraďte `[IoTHub Connection String]` připojovacím řetězcem centra IoT, který jste zkopírovali v [části získání připojovacího řetězce centra IoT Hub](#get-the-iot-hub-connection-string). Nahraďte `[Device Id]` ID zařízení, které jste zaregistrovali v [části registrace nového zařízení ve službě IoT Hub](#register-a-new-device-in-the-iot-hub).
  
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"location\":\"Redmond\"}}}"

    UPDATE_JSON_SEARCH = "\"location\":\"Redmond\""
    UPDATE_JSON_CLIENT_SEARCH = "\"connectivity\":\"cellular\""
    ```

5. Do souboru **AddTagsAndQuery.py** přidejte následující kód:

    ```python
    def iothub_service_sample_run():
        try:
            iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

            iothub_registry_statistics = iothub_registry_manager.get_statistics()
            print ( "Total device count                       : {0}".format(iothub_registry_statistics.totalDeviceCount) )
            print ( "Enabled device count                     : {0}".format(iothub_registry_statistics.enabledDeviceCount) )
            print ( "Disabled device count                    : {0}".format(iothub_registry_statistics.disabledDeviceCount) )
            print ( "" )

            number_of_devices = iothub_registry_statistics.totalDeviceCount
            dev_list = iothub_registry_manager.get_device_list(number_of_devices)

            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            for device in range(0, number_of_devices):
                if dev_list[device].deviceId == DEVICE_ID:
                    twin_info = iothub_twin_method.update_twin(dev_list[device].deviceId, UPDATE_JSON)

            print ( "Devices in Redmond: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)

                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    print ( dev_list[device].deviceId )

            print ( "" )

            print ( "Devices in Redmond using cellular network: " )
            for device in range(0, number_of_devices):
                twin_info = iothub_twin_method.get_twin(dev_list[device].deviceId)

                if twin_info.find(UPDATE_JSON_SEARCH) > -1:
                    if twin_info.find(UPDATE_JSON_CLIENT_SEARCH) > -1:
                        print ( dev_list[device].deviceId )

        except IoTHubError as iothub_error:
            print ( "Unexpected error {0}".format(iothub_error) )
            return
        except KeyboardInterrupt:
            print ( "IoTHub sample stopped" )
    ```

    Objekt **registru** zveřejňuje všechny metody, které jsou potřeba pro interakci se zařízeními ze služby. Kód nejprve inicializuje objekt **registru** , potom aktualizuje pole pro zařízení v poli pro **deviceId**a nakonec spustí dva dotazy. Nejdřív se vybere jenom zařízení, která se nacházejí v **Redmond43** , a druhá ho podrobná a vybere jenom zařízení, která jsou taky připojená přes mobilní síť.

6. Přidejte následující kód na konec **AddTagsAndQuery.py** k implementaci funkce **iothub_service_sample_run** :

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python service sample..." )

        iothub_service_sample_run()
    ```

7. Spusťte aplikaci pomocí:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Ve výsledcích dotazu pro všechna zařízení umístěná v **Redmond43** byste měli vidět jedno zařízení, které pro dotaz neomezuje výsledky na zařízení, která používají mobilní síť.

    ![první dotaz zobrazující všechna zařízení v Redmond](./media/iot-hub-python-twin-getstarted/service-1.png)

V další části vytvoříte aplikaci pro zařízení, která oznamuje informace o připojení a mění výsledek dotazu v předchozí části.

## <a name="create-the-device-app"></a>Vytvoření aplikace pro zařízení

V této části vytvoříte konzolovou aplikaci v Pythonu, která se připojí k vašemu rozbočovači jako vaše **{ID zařízení}** , a pak aktualizuje nahlášené vlastnosti, které jsou v zařízení nahlášeny, aby obsahovaly informace, které jsou připojené pomocí mobilní sítě.

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

4. Přidejte následující kód. Nahraďte hodnotu [](#register-a-new-device-in-the-iot-hub) zástupnéhosymbolupřipojovacímřetězcemzařízení,kterýjstezkopírovalivčástiregistracenovéhozařízeníveslužběIoT`[IoTHub Device Connection String]` hub.

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
        client = IoTHubDeviceClient.create_from_connection_string(CONNECTION_STRING)
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
            print ( "IoTHubClient sample stopped" )
    ```

    Objekt **klienta** zveřejňuje všechny metody, které vyžadujete pro interakci se zařízeními ze zařízení. Předchozí kód: po inicializaci objektu **klienta** načte objekt pro vaše zařízení vlákna a aktualizuje jeho hlášenou vlastnost informacemi o připojení.

6. Přidejte následující kód na konec **ReportConnectivity.py** k implementaci funkce **iothub_client_sample_run** :

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python client sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

7. Spusťte aplikaci zařízení:

    ```cmd/sh
    python ReportConnectivity.py
    ```

    Mělo by se zobrazit potvrzení, že se vlákna zařízení aktualizovala.

    ![aktualizovat vlákna](./media/iot-hub-python-twin-getstarted/device-1.png)

8. Teď, když zařízení oznámilo informace o připojení, by se mělo zobrazit v obou dotazech. Vraťte se zpět a znovu spusťte dotazy:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Tentokrát by **{ID zařízení}** mělo být ve výsledcích dotazu.

    ![druhý dotaz](./media/iot-hub-python-twin-getstarted/service-2.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste nakonfigurovali novou službu IoT Hub na webu Azure Portal a potom jste vytvořili identitu zařízení v registru identit ve službě IoT Hub. Metadata zařízení jste přidali jako značky z back-endové aplikace a zapsali jste aplikaci simulovaného zařízení, která oznamuje informace o připojení zařízení v zařízení. Zjistili jste také, jak zadat dotaz na tyto informace pomocí registru.

Pomocí následujících zdrojů se naučíte:

* Pomocí kurzu [Začínáme s IoT Hub](quickstart-send-telemetry-python.md) odešlete telemetrii ze zařízení.

* V kurzu [použití požadovaných vlastností ke konfiguraci zařízení](tutorial-device-twins.md) nakonfigurujte zařízení pomocí požadovaných vlastností pro vyplňování zařízení.

* interaktivní řízení zařízení (například zapnutí ventilátoru z aplikace řízené uživatelem) pomocí kurzu [použití přímých metod](quickstart-control-device-python.md) .
