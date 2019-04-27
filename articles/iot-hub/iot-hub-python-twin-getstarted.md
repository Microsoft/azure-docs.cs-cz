---
title: Začínáme s dvojčaty zařízení Azure IoT Hub (Python) | Dokumentace Microsoftu
description: Jak používat dvojče zařízení Azure IoT Hub pro přidání značek a následné použití k dotazu služby IoT Hub. Implementace aplikace simulovaného zařízení a app service, které přidá značky a spustí dotaz služby IoT Hub pomocí sad Azure IoT SDK pro Python.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: kgremban
ms.openlocfilehash: 3e703c999d57cf62064291cf91059a17a959a2c3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61442587"
---
# <a name="get-started-with-device-twins-python"></a>Začínáme s dvojčaty zařízení (Python)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Na konci tohoto kurzu budete mít dvě aplikace konzoly v Pythonu:

* **AddTagsAndQuery.py**, back endové aplikace Python, které přidá značky a dotazy dvojčata zařízení.

* **ReportConnectivity.py**, aplikace v Pythonu, která simuluje zařízení, která se připojuje ke službě IoT hub s dříve vytvořenou identitou zařízení a sestav stavu připojení.

> [!NOTE]
> Tento článek [sad SDK Azure IoT](iot-hub-devguide-sdks.md) poskytuje informace o Azure IoT SDK, že vám pomůže vytvářet aplikace pro zařízení i back-end.

K dokončení tohoto kurzu budete potřebovat následující:

* [Python 2.x nebo 3.x](https://www.python.org/downloads/). Ujistěte se, že používáte 32bitovou, nebo 64bitovou instalaci podle požadavků vašeho nastavení. Po zobrazení výzvy v průběhu instalace nezapomeňte přidat Python do proměnné prostředí pro konkrétní platformu. Pokud používáte Python 2.x, možná bude nutné [nainstalovat nebo upgradovat *pip*, systém správy balíčků Pythonu](https://pip.pypa.io/en/stable/installing/).

* Pokud používáte operační systém Windows, je k povolení používání nativních knihoven DLL z Pythonu potřeba [balíček distribuovatelných součástí Visual C++](https://www.microsoft.com/download/confirmation.aspx?id=48145).

* Aktivní účet Azure. (Pokud účet nemáte, můžete vytvořit [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) během několika minut.)

> [!NOTE]
> Balíčky *pip* pro `azure-iothub-service-client` a `azure-iothub-device-client` jsou v současné době dostupné jenom pro operační systém Windows. Linux/Mac OS, naleznete v části operačních systémů Linux a Mac OS konkrétní na [Příprava vývojového prostředí pro jazyk Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) příspěvku.
>

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Načtení připojovacího řetězce pro službu IoT hub

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Zaregistrujte nové zařízení ve službě IoT hub

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-the-service-app"></a>Vytvoření aplikace služby

V této části vytvoříte konzolovou aplikaci v Pythonu, který přidá umístění metadat do dvojčete zařízení spojené s vaší **{ID zařízení}**. Následně se dotazuje dvojčata zařízení, které jsou uložené ve službě IoT hub, výběrem zařízení umístěných v Redmondu a ty, které vykazují mobilní připojení.

1. Otevřete příkazový řádek a nainstalujte **sadu SDK služby Azure IoT Hub pro Python**. Po dokončení instalace sady SDK zavřete příkazový řádek.

   ```
   pip install azure-iothub-service-client
   ```

2. Pomocí textového editoru vytvořte nový **AddTagsAndQuery.py** souboru.

3. Přidejte následující kód, který importuje požadované moduly ze sady SDK služby:

   ```python
   import sys
   import iothub_service_client
   from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
   from iothub_service_client import IoTHubDeviceTwin, IoTHubError
   ```

4. Přidejte následující kód a nahraďte zástupnou hodnotu `[IoTHub Connection String]` a `[Device Id]` připojovacím řetězcem pro službu IoT hub a ID zařízení, kterou jste vytvořili v předchozích částech.
  
    ```python
    CONNECTION_STRING = "[IoTHub Connection String]"
    DEVICE_ID = "[Device Id]"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"location\":\"Redmond\"}}}"

    UPDATE_JSON_SEARCH = "\"location\":\"Redmond\""
    UPDATE_JSON_CLIENT_SEARCH = "\"connectivity\":\"cellular\""
    ```

5. Přidejte následující kód, který **AddTagsAndQuery.py** souboru:

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

    **Registru** objekt poskytuje všechny metody, které jsou nutné k interakci s dvojčaty zařízení ze služby. Kód nejprve inicializuje **registru** objekt a potom aktualizace dvojčete zařízení pro **deviceId**, a nakonec je spuštěn dva dotazy. První vybere pouze dvojčata zařízení nachází v zařízení **Redmond43** kategorie plant a druhá zpřesnění dotaz pro výběr pouze do zařízení, které jsou také připojené přes mobilní síť.

6. Přidejte následující kód na konci **AddTagsAndQuery.py** k implementaci **iothub_service_sample_run** funkce:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python service sample..." )

        iothub_service_sample_run()
    ```

7. Spusťte aplikaci:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Měli byste vidět jedno zařízení ve výsledcích pro kladení dotazů pro všechna zařízení nachází v **Redmond43** a žádné dotaz, který omezuje výsledky na zařízení, která používají mobilní síti.

    ![první dotaz v Redmondu nezobrazuje všechna](./media/iot-hub-python-twin-getstarted/1-device-twins-python-service-sample.png)

V další části můžete vytvořit aplikace pro zařízení, která hlásí informace o připojení a změní výsledek dotazu v předchozí části.

## <a name="create-the-device-app"></a>Vytvoření aplikace pro zařízení

V této části vytvoříte konzolovou aplikaci v Pythonu, který se připojuje k vašemu centru jako vaše **{ID zařízení}** a pak aktualizuje jeho dvojče zařízení uživatele ohlášené vlastnosti k obsahují informace, že je připojený pomocí mobilní síti.

1. Otevřete příkazový řádek a nainstalujte **sadu SDK služby Azure IoT Hub pro Python**. Po dokončení instalace sady SDK zavřete příkazový řádek.

    ```
    pip install azure-iothub-device-client
    ```

2. Pomocí textového editoru vytvořte nový **ReportConnectivity.py** souboru.

3. Přidejte následující kód, který importuje požadované moduly ze sady SDK služby:

    ```python
    import time
    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult, IoTHubError
    ```

4. Přidejte následující kód a nahraďte zástupnou hodnotu `[IoTHub Device Connection String]` připojovacím řetězcem pro zařízení IoT hub, jste vytvořili v předchozích částech.

    ```python
    CONNECTION_STRING = "[IoTHub Device Connection String]"

    # choose HTTP, AMQP, AMQP_WS or MQTT as transport protocol
    PROTOCOL = IoTHubTransportProvider.MQTT

    TIMER_COUNT = 5
    TWIN_CONTEXT = 0
    SEND_REPORTED_STATE_CONTEXT = 0
    ```

5. Přidejte následující kód, který **ReportConnectivity.py** soubor k implementaci zařízení dvojčat funkce:

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "" )
        print ( "Twin callback called with:" )
        print ( "    updateStatus: %s" % update_state )
        print ( "    payload: %s" % payload )

    def send_reported_state_callback(status_code, user_context):
        print ( "" )
        print ( "Confirmation for reported state called with:" )
        print ( "    status_code: %d" % status_code )

    def iothub_client_init():
        client = IoTHubClient(CONNECTION_STRING, PROTOCOL)

        if client.protocol == IoTHubTransportProvider.MQTT or client.protocol == IoTHubTransportProvider.MQTT_WS:
            client.set_device_twin_callback(
                device_twin_callback, TWIN_CONTEXT)

        return client

    def iothub_client_sample_run():
        try:
            client = iothub_client_init()

            if client.protocol == IoTHubTransportProvider.MQTT:
                print ( "Sending data as reported property..." )

                reported_state = "{\"connectivity\":\"cellular\"}"

                client.send_reported_state(reported_state, len(reported_state), send_reported_state_callback, SEND_REPORTED_STATE_CONTEXT)

            while True:
                print ( "Press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= TIMER_COUNT:
                    status = client.get_send_status()
                    time.sleep(10)
                    status_counter += 1 
        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )
     ```

    **Klienta** objekt poskytuje všechny metody, které budete potřebovat pro interakci s dvojčaty zařízení ze zařízení. Předchozí kód, až se inicializuje **klienta** objekt, načte dvojče zařízení pro vaše zařízení a jeho ohlášených vlastností aktualizuje informace o připojení.

6. Přidejte následující kód na konci **ReportConnectivity.py** k implementaci **iothub_client_sample_run** funkce:

    ```python
    if __name__ == '__main__':
        print ( "Starting the IoT Hub Device Twins Python client sample..." )

        iothub_client_sample_run()
    ```

7. Spuštění aplikace pro zařízení:

    ```cmd/sh
    python ReportConnectivity.py
    ```

    Měli byste vidět potvrzení, které byly aktualizovány dvojčata zařízení.

    ![aktualizace dvojčat](./media/iot-hub-python-twin-getstarted/2-python-client-sample.png)

8. Teď, když zařízení uvádět jeho informace o připojení, měl by se zobrazit v obou dotazech. Vraťte se a znovu spusťte dotazy:

    ```cmd/sh
    python AddTagsAndQuery.py
    ```

    Tentokrát vaše **{ID zařízení}** by se měla objevit v obou výsledky dotazu.

    ![druhý dotaz](./media/iot-hub-python-twin-getstarted/3-device-twins-python-service-sample.png)

## <a name="next-steps"></a>Další postup

V tomto kurzu jste nakonfigurovali novou službu IoT Hub na webu Azure Portal a potom jste vytvořili identitu zařízení v registru identit ve službě IoT Hub. Přidá metadata zařízení jako značky z back endové aplikace a aplikace s Simulovaná zařízení zapsáno do sestavy informací o připojení k zařízení ve dvojčeti zařízení. Také jste zjistili, jak zadávat dotazy na tyto informace pomocí registru.

Pomocí následujících zdrojích se dozvíte jak:

* Odesílání telemetrie ze zařízení s [Začínáme se službou IoT Hub](quickstart-send-telemetry-python.md) kurzu.

* Konfigurace zařízení pomocí požadované vlastnosti dvojčete zařízení s [použití požadované vlastnosti ke konfiguraci zařízení](tutorial-device-twins.md) kurzu.

* Kontroly nad zařízeními interaktivně (například zapnutí ventilátor z aplikace řízené uživatelem), se [použití přímých metod](quickstart-control-device-python.md) kurzu.