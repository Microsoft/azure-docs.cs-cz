---
title: Identita modulu Azure IoT Hub a modul s dvojitou ochranou (Python)
description: Přečtěte si, jak vytvořit identitu modulu a aktualizovat modul s dvojitou identitou pomocí sad IoT SDK pro Python.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: menchi
ms.custom: devx-track-python
ms.openlocfilehash: 665281adc892e6b3655c0b1d0533cb3148e62940
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92139393"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-python"></a>Začínáme s identitou modulu IoT Hub a modulem s dvojitou podobou (Python)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Identity modulů a](iot-hub-devguide-module-twins.md) nefungující moduly jsou podobné identitám zařízení v Azure IoT Hub a k nevlákenám zařízení, ale poskytují jemnější členitost. I když se v Azure IoT Hub identity zařízení a vlákna zařízení povolují aplikace back-end pro konfiguraci zařízení a zajištění viditelnosti podmínek zařízení, identit modulů a vláken v modulech poskytují tyto možnosti pro jednotlivé součásti zařízení. Na zařízeních podporujících více komponent, jako jsou například zařízení založená na operačním systému nebo zařízení firmwaru, umožňují izolovanou konfiguraci a podmínky pro jednotlivé komponenty.
>

Na konci tohoto kurzu máte tři aplikace v Pythonu:

* **CreateModule**, která vytvoří identitu zařízení, identitu modulu a přidružené bezpečnostní klíče pro připojení klientů zařízení a modulů.

* **UpdateModuleTwinDesiredProperties**, která odesílá aktualizovanému modulu požadované vlastnosti do IoT Hub.

* **ReceiveModuleTwinDesiredPropertiesPatch**, která obdrží na svém zařízení opravu s neočekávanými vlastnostmi požadovaného modulu.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT Hub

V tomto článku vytvoříte back-end službu, která přidá zařízení do registru identit a potom do tohoto zařízení přidá modul. Tato služba vyžaduje oprávnění k **zápisu do registru** (které zahrnuje i **čtení registru**). Také vytvoříte službu, která pro nově vytvořený modul doplní do modulu vlákna požadované vlastnosti. Tato služba potřebuje oprávnění **připojit ke službě** . I když existují výchozí zásady sdíleného přístupu, které udělují tato oprávnění individuálně, v této části vytvoříte vlastní zásadu sdíleného přístupu, která obsahuje obě tato oprávnění.

[!INCLUDE [iot-hub-include-find-service-regrw-connection-string](../../includes/iot-hub-include-find-service-regrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Vytvoření identity zařízení a identity modulu v IoT Hub

V této části vytvoříte aplikaci služby Python, která v registru identit ve službě IoT Hub vytvoří identitu zařízení a identitu modulu. Zařízení nebo modul se nemůže připojit ke službě IoT Hub, pokud neobsahuje záznam v registru identit. Další informace najdete v tématu [vysvětlení registru identit ve službě IoT Hub](iot-hub-devguide-identity-registry.md). Když spustíte tuto konzolovou aplikaci, vygeneruje jedinečné ID a klíč zařízení i modulu. Vaše zařízení a modul použijí tyto hodnoty k vlastní identifikaci při odesílání zpráv typu zařízení-cloud do služby IoT Hub. V ID se rozlišují malá a velká písmena.

1. Na příkazovém řádku spusťte následující příkaz pro instalaci balíčku **Azure-IoT-Hub** :

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. Na příkazovém řádku spusťte následující příkaz, který nainstaluje balíček **msrest** . Tento balíček potřebujete k zachycení výjimek **HTTPOperationError** .

    ```cmd/sh
    pip install msrest
    ```

1. Pomocí textového editoru vytvořte v pracovním adresáři soubor s názvem **CreateModule.py** .

1. Do souboru Pythonu přidejte následující kód. Nahraďte *YourIoTHubConnectionString* připojovacím řetězcem, který jste zkopírovali v [části získání připojovacího řetězce centra IoT Hub](#get-the-iot-hub-connection-string).

    ```python
    import sys
    from msrest.exceptions import HttpOperationError
    from azure.iot.hub import IoTHubRegistryManager

    CONNECTION_STRING = "YourIotHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        try:
            # CreateDevice - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            device_state = "enabled"
            new_device = iothub_registry_manager.create_device_with_sas(
                DEVICE_ID, primary_key, secondary_key, device_state
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the device already exists.
                new_device = iothub_registry_manager.get_device(DEVICE_ID)
            else:
                raise

        print("device <" + DEVICE_ID +
              "> has primary key = " + new_device.authentication.symmetric_key.primary_key)

        try:
            # CreateModule - let IoT Hub assign keys
            primary_key = ""
            secondary_key = ""
            managed_by = ""
            new_module = iothub_registry_manager.create_module_with_sas(
                DEVICE_ID, MODULE_ID, managed_by, primary_key, secondary_key
            )
        except HttpOperationError as ex:
            if ex.response.status_code == 409:
                # 409 indicates a conflict. This happens because the module already exists.
                new_module = iothub_registry_manager.get_module(DEVICE_ID, MODULE_ID)
            else:
                raise

        print("device/module <" + DEVICE_ID + "/" + MODULE_ID +
              "> has primary key = " + new_module.authentication.symmetric_key.primary_key)

    except Exception as ex:
        print("Unexpected error {0}".format(ex))
    except KeyboardInterrupt:
        print("IoTHubRegistryManager sample stopped")
    ```

1. Na příkazovém řádku spusťte následující příkaz:

    ```cmd/sh
    python CreateModule.py
    ```

Tato aplikace vytvoří identitu zařízení s ID **myFirstDevice** a identitou modulu s ID **MyFirstModule** v části **myFirstDevice**zařízení. (Pokud zařízení nebo ID modulu již v registru identit existuje, kód jednoduše načte existující informace o zařízení nebo modulu.) Aplikace zobrazí ID a primární klíč pro každou identitu.

> [!NOTE]
> V registru identit služby IoT Hub se uchovávají identity zařízení a modulů pouze za účelem bezpečného přístupu k centru IoT. Registr identit ukládá ID zařízení a klíče pro použití jako bezpečnostních pověření. Registr identit také ukládá povolené a zakázané příznaky pro jednotlivá zařízení, pomocí kterých můžete zakázat přístup pro dané zařízení. Pokud aplikace potřebuje pro zařízení ukládat další metadata, měla by používat úložiště pro konkrétní aplikaci. Pro identity modulů neexistuje žádný příznak povoleno/zakázáno. Další informace najdete v tématu [vysvětlení registru identit ve službě IoT Hub](iot-hub-devguide-identity-registry.md).
>

## <a name="update-the-module-twin-using-python-service-sdk"></a>Aktualizujte modul s dvojím použitím sady SDK služby Python.

V této části vytvoříte aplikaci služby Python, která aktualizuje modul s požadovanými vlastnostmi.

1. Na příkazovém řádku spusťte následující příkaz pro instalaci balíčku **Azure-IoT-Hub** . Tento krok můžete přeskočit, pokud jste nainstalovali balíček **Azure-IoT-Hub** v předchozí části.

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. Pomocí textového editoru vytvořte v pracovním adresáři soubor s názvem **UpdateModuleTwinDesiredProperties.py** .

1. Do souboru Pythonu přidejte následující kód. Nahraďte *YourIoTHubConnectionString* připojovacím řetězcem, který jste zkopírovali v [části získání připojovacího řetězce centra IoT Hub](#get-the-iot-hub-connection-string).

    ```python
    import sys
    from azure.iot.hub import IoTHubRegistryManager
    from azure.iot.hub.models import Twin, TwinProperties

    CONNECTION_STRING = "YourIoTHubConnectionString"
    DEVICE_ID = "myFirstDevice"
    MODULE_ID = "myFirstModule"

    try:
        # RegistryManager
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        module_twin = iothub_registry_manager.get_module_twin(DEVICE_ID, MODULE_ID)
        print ( "" )
        print ( "Module twin properties before update    :" )
        print ( "{0}".format(module_twin.properties) )

        # Update twin
        twin_patch = Twin()
        twin_patch.properties = TwinProperties(desired={"telemetryInterval": 122})
        updated_module_twin = iothub_registry_manager.update_module_twin(
            DEVICE_ID, MODULE_ID, twin_patch, module_twin.etag
        )
        print ( "" )
        print ( "Module twin properties after update     :" )
        print ( "{0}".format(updated_module_twin.properties) )

    except Exception as ex:
        print ( "Unexpected error {0}".format(ex) )
    except KeyboardInterrupt:
        print ( "IoTHubRegistryManager sample stopped" )
    ```

## <a name="get-updates-on-the-device-side"></a>Získat aktualizace na straně zařízení

V této části vytvoříte aplikaci v Pythonu, která v zařízení získá aktualizaci vlastností s nechtěným požadováním.

1. Získejte připojovací řetězec modulu. V [Azure Portal](https://portal.azure.com/)přejděte do IoT Hub a v levém podokně vyberte **zařízení IoT** . V seznamu zařízení vyberte **myFirstDevice** a otevřete ji. V části **identity modulu**vyberte **myFirstModule**. Zkopírujte připojovací řetězec modulu. Budete ho potřebovat v následujícím kroku.

   ![Podrobnosti o modulu na webu Azure Portal](./media/iot-hub-python-python-module-twin-getstarted/module-detail.png)

1. Na příkazovém řádku spusťte následující příkaz pro instalaci balíčku **Azure-IoT-Device** :

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Pomocí textového editoru vytvořte v pracovním adresáři soubor s názvem **ReceiveModuleTwinDesiredPropertiesPatch.py** .

1. Do souboru Pythonu přidejte následující kód. Nahraďte *YourModuleConnectionString* připojovacím řetězcem modulu, který jste zkopírovali v kroku 1.

    ```python
    import time
    import threading
    from azure.iot.device import IoTHubModuleClient

    CONNECTION_STRING = "YourModuleConnectionString"


    def twin_update_listener(client):
        while True:
            patch = client.receive_twin_desired_properties_patch()  # blocking call
            print("")
            print("Twin desired properties patch received:")
            print(patch)

    def iothub_client_sample_run():
        try:
            module_client = IoTHubModuleClient.create_from_connection_string(CONNECTION_STRING)

            twin_update_listener_thread = threading.Thread(target=twin_update_listener, args=(module_client,))
            twin_update_listener_thread.daemon = True
            twin_update_listener_thread.start()

            while True:
                time.sleep(1000000)

        except KeyboardInterrupt:
            print("IoTHubModuleClient sample stopped")


    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python sample..." )
        print ( "IoTHubModuleClient waiting for commands, press Ctrl-C to exit" )

        iothub_client_sample_run()
    ```

## <a name="run-the-apps"></a>Spouštění aplikací

V této části spustíte aplikaci zařízení **ReceiveModuleTwinDesiredPropertiesPatch** a potom spustíte aplikaci služby **UpdateModuleTwinDesiredProperties** , abyste aktualizovali požadované vlastnosti vašeho modulu.

1. Otevřete příkazový řádek a spusťte aplikaci zařízení:

    ```cmd/sh
    python ReceiveModuleTwinDesiredPropertiesPatch.py
    ```

   ![Počáteční výstup aplikace zařízení](./media/iot-hub-python-python-module-twin-getstarted/device-1.png)

1. Otevřete samostatný příkazový řádek a spusťte aplikaci služby:

    ```cmd/sh
    python UpdateModuleTwinDesiredProperties.py
    ```

    Všimněte si, že požadovaná vlastnost **TelemetryInterval** se zobrazí ve výstupu aktualizovaného modulu ve výstupu aplikace služby:

   ![Výstup aplikace služby](./media/iot-hub-python-python-module-twin-getstarted/service.png)

    Stejná vlastnost se zobrazí v opravě požadované vlastnosti přijaté ve výstupu aplikace vašeho zařízení:

   ![Výstup aplikace zařízení zobrazuje opravu požadovaných vlastností.](./media/iot-hub-python-python-module-twin-getstarted/device-2.png)

## <a name="next-steps"></a>Další kroky

Chcete-li pokračovat v seznamování se službou IoT Hub a prozkoumat další scénáře IoT, podívejte se na tato témata:

* [Začínáme se správou zařízení](iot-hub-node-node-device-management-get-started.md)

* [Začínáme se službou IoT Edge](../iot-edge/quickstart-linux.md)