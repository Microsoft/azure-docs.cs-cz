---
title: Identita modulu Azure IoT Hub a dvojče modulu (Python)
description: Naučte se vytvářet identitu modulu a aktualizovat dvojče modulu pomocí sad IoT SDK pro Python.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: menchi
ms.openlocfilehash: f846af548913e0cb3e872560e4b8438da306a255
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756920"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-python"></a>Začínáme s identitou modulu IoT Hub a dvojčeem modulu (Python)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [Identity modulů a dvojčata modulů](iot-hub-devguide-module-twins.md) jsou podobné identitám zařízení Azure IoT Hub a dvojčatům zařízení, ale poskytují jemnější rozlišovací schopnost. Zatímco identity zařízení Azure IoT Hub a dvojčata zařízení umožňují back-endaplikace ke konfiguraci zařízení a poskytují viditelnost na podmínky zařízení, identity modulu a dvojčata modulu poskytují tyto funkce pro jednotlivé součásti zařízení. Na schopných zařízeních s více součástmi, jako jsou zařízení založená na operačním systému nebo zařízení firmwaru, umožňují izolovanou konfiguraci a podmínky pro každou součást.
>

Na konci tohoto kurzu máte tři aplikace Pythonu:

* **CreateModule**, který vytvoří identitu zařízení, identitu modulu a přidružené klíče zabezpečení pro připojení klientů zařízení a modulu.

* **UpdateModuleTwinDesiredProperties**, který odešle aktualizované dvojče modulu požadované vlastnosti do služby IoT Hub.

* **ReceiveModuleTwinDesiredPropertiesPatch**, který obdrží modul twin požadované vlastnosti patch na vašem zařízení.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [iot-hub-include-python-v2-installation-notes](../../includes/iot-hub-include-python-v2-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Vytvoření centra IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Získání připojovacího řetězce centra IoT hub

V tomto článku vytvoříte back-endovou službu, která přidá zařízení do registru identit a potom přidá modul do tohoto zařízení. Tato služba vyžaduje oprávnění **k zápisu do registru** (které zahrnuje také čtení **registru).** Můžete také vytvořit službu, která přidá požadované vlastnosti dvojčete modulu pro nově vytvořený modul. Tato služba potřebuje oprávnění **připojení služby.** Přestože existují výchozí zásady sdíleného přístupu, které udělují tato oprávnění jednotlivě, v této části vytvoříte vlastní zásady sdíleného přístupu, které obsahují obě tato oprávnění.

[!INCLUDE [iot-hub-include-find-service-regrw-connection-string](../../includes/iot-hub-include-find-service-regrw-connection-string.md)]

## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Vytvoření identity zařízení a identity modulu v centru IoT Hub

V této části vytvoříte aplikaci služby Pythonu, která vytvoří identitu zařízení a identitu modulu v registru identit v centru IoT. Zařízení nebo modul se nemůže připojit k centru IoT, pokud nemá položku v registru identit. Další informace naleznete [v tématu Principy registru identit ve vašem centru IoT Hub](iot-hub-devguide-identity-registry.md). Když spustíte tuto konzolovou aplikaci, vygeneruje jedinečné ID a klíč zařízení i modulu. Vaše zařízení a modul použijí tyto hodnoty k vlastní identifikaci při odesílání zpráv typu zařízení-cloud do služby IoT Hub. V ID se rozlišují malá a velká písmena.

1. Na příkazovém řádku spusťte následující příkaz pro instalaci balíčku **azure-iot-hub:**

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. Na příkazovém řádku spusťte následující příkaz a nainstalujte balíček **msrest.** Tento balíček potřebujete k zachycení výjimek **HTTPOperationError.**

    ```cmd/sh
    pip install msrest
    ```

1. Pomocí textového editoru vytvořte soubor s názvem **CreateModule.py** v pracovním adresáři.

1. Přidejte do souboru Pythonu následující kód. Nahraďte *yourIoTHubConnectionString* připojovacím řetězcem, který jste zkopírovali v [možnosti Získat připojovací řetězec centra IoT](#get-the-iot-hub-connection-string).

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

Tato aplikace vytvoří identitu zařízení s ID **myFirstDevice** a identitu modulu s ID **myFirstModule** pod zařízením **myFirstDevice**. (Pokud ID zařízení nebo modulu již v registru identit existuje, kód jednoduše načte existující informace o zařízení nebo modulu.) Aplikace zobrazí ID a primární klíč pro každou identitu.

> [!NOTE]
> V registru identit služby IoT Hub se uchovávají identity zařízení a modulů pouze za účelem bezpečného přístupu k centru IoT. Registr identit ukládá ID zařízení a klíče pro použití jako bezpečnostních pověření. Registr identit také ukládá povolené a zakázané příznaky pro jednotlivá zařízení, pomocí kterých můžete zakázat přístup pro dané zařízení. Pokud aplikace potřebuje pro zařízení ukládat další metadata, měla by používat úložiště pro konkrétní aplikaci. Pro identity modulů neexistuje žádný příznak povoleno/zakázáno. Další informace naleznete [v tématu Principy registru identit ve vašem centru IoT Hub](iot-hub-devguide-identity-registry.md).
>

## <a name="update-the-module-twin-using-python-service-sdk"></a>Aktualizace dvojčete modulu pomocí sady SDK služby Python

V této části vytvoříte aplikaci služby Pythonu, která aktualizuje požadované vlastnosti dvojčete modulu.

1. Na příkazovém řádku spusťte následující příkaz k instalaci balíčku **azure-iot-hub.** Tento krok můžete přeskočit, pokud jste nainstalovali balíček **azure-iot-hub** v předchozí části.

    ```cmd/sh
    pip install azure-iot-hub
    ```

1. Pomocí textového editoru vytvořte soubor s názvem **UpdateModuleTwinDesiredProperties.py** v pracovním adresáři.

1. Přidejte do souboru Pythonu následující kód. Nahraďte *yourIoTHubConnectionString* připojovacím řetězcem, který jste zkopírovali v [možnosti Získat připojovací řetězec centra IoT](#get-the-iot-hub-connection-string).

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

## <a name="get-updates-on-the-device-side"></a>Získání aktualizací na straně zařízení

V této části vytvoříte aplikaci Pythonu, abyste získali aktualizaci požadovaných vlastností modulu na vašem zařízení.

1. Získejte připojovací řetězec modulu. Na [webu Azure Portal](https://portal.azure.com/)přejděte do služby IoT Hub a v levém podokně vyberte zařízení **IoT.** Vyberte **myFirstDevice** ze seznamu zařízení a otevřete jej. V části **Identity modulu**vyberte **myFirstModule**. Zkopírujte připojovací řetězec modulu. Potřebujete ji v následujícím kroku.

   ![Podrobnosti o modulu na webu Azure Portal](./media/iot-hub-python-python-module-twin-getstarted/module-detail.png)

1. Na příkazovém řádku spusťte následující příkaz k instalaci balíčku **azure-iot-device:**

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Pomocí textového editoru vytvořte soubor s názvem **ReceiveModuleTwinDesiredPropertiesPatch.py** v pracovním adresáři.

1. Přidejte do souboru Pythonu následující kód. Nahraďte *modul YourModuleConnectionString* připojovacím řetězcem modulu, který jste zkopírovali v kroku 1.

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

V této části spustíte aplikaci **zařízení ReceiveModuleTwinDesiredPropertiesPatch** a potom spusťte aplikaci služby **UpdateModuleTwinDesiredProperties** a aktualizujte požadované vlastnosti modulu.

1. Otevřete příkazový řádek a spusťte aplikaci pro zařízení:

    ```cmd/sh
    python ReceiveModuleTwinDesiredPropertiesPatch.py
    ```

   ![Počáteční výstup aplikace zařízení](./media/iot-hub-python-python-module-twin-getstarted/device-1.png)

1. Otevřete samostatný příkazový řádek a spusťte aplikaci služby:

    ```cmd/sh
    python UpdateModuleTwinDesiredProperties.py
    ```

    Všimněte si, že požadovaná vlastnost **TelemetryInterval** se zobrazí v aktualizovanou dvojče modulu ve výstupu aplikace služby:

   ![Výstup aplikace služby](./media/iot-hub-python-python-module-twin-getstarted/service.png)

    Stejná vlastnost se zobrazí v požadované vlastnosti opravy přijaté ve výstupu aplikace zařízení:

   ![Výstup aplikace zařízení zobrazuje opravu požadovaných vlastností](./media/iot-hub-python-python-module-twin-getstarted/device-2.png)

## <a name="next-steps"></a>Další kroky

Chcete-li pokračovat v seznamování se službou IoT Hub a prozkoumat další scénáře IoT, podívejte se na tato témata:

* [Začínáme se správou zařízení](iot-hub-node-node-device-management-get-started.md)

* [Začínáme se službou IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)