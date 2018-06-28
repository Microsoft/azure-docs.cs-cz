---
title: Začínáme s Azure IoT Hub modulu identity a modul twin (Python) | Microsoft Docs
description: Naučte se vytvořit modul identity a aktualizovat twin modulu pomocí sady SDK služby IoT pro jazyk Python.
author: chrissie926
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: menchi
ms.openlocfilehash: 7ef4d00f34cdf35c670099baa6c3bc655d94afb4
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035749"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-python-back-end-and-python-device"></a>Začínáme s identity a modul twin serveru IoT Hub modulu pomocí back-end Python a Python zařízení

> [!NOTE]
> [Identity modulů a dvojčata modulů](iot-hub-devguide-module-twins.md) se podobají identitě zařízení a dvojčeti zařízení služby Azure IoT Hub, ale poskytují větší úroveň členitosti. Zatímco identita zařízení a dvojče zařízení služby Azure IoT Hub umožňují back-endové aplikaci konfigurovat zařízení a poskytují vhled do stavu zařízení, identita modulu a dvojče modulu poskytují tyto možnosti pro jednotlivé součásti zařízení. Na způsobilých zařízeních s několika součástmi, jako jsou zařízení s operačním systémem nebo zařízení s firmwarem, to umožňuje izolovanou konfiguraci a vhled do stavu jednotlivých součástí.

Na konci tohoto kurzu máte dvě aplikace Python:

* Aplikaci **CreateIdentities**, která vytvoří identitu zařízení, identitu modulu a přidružený klíč zabezpečení pro připojení klientů zařízení a modulu.
* Aplikaci **UpdateModuleTwinReportedProperties**, která do služby IoT Hub odešle aktualizované hlášené vlastnosti dvojčete modulu.

> [!NOTE]
> Informace o sadách SDK služby Azure IoT Hub, s jejichž pomocí můžete sestavit aplikace, které poběží v zařízení, i back-end vašeho řešení, najdete v tématu [Sady SDK služby IoT Hub][lnk-hub-sdks].

Pro absolvování tohoto kurzu potřebujete:

* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)
* Centrum IoT.
* Nainstalujte si nejnovější verzi [Python SDK](https://github.com/Azure/azure-iot-sdk-python).


Nyní jste vytvořili službu IoT Hub a máte název hostitele a připojovací řetězec služby IoT Hub, které potřebujete k dokončení kurzu.

<a id="DeviceIdentity_csharp"></a>
## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Vytvoření identity zařízení a identitu modulu ve IoT Hub.

V této části vytvoříte aplikaci Python, která v registru identit ve službě IoT hub vytvoří identitu zařízení a modul identity. Zařízení nebo modul je možné připojit k centru IoT, pouze pokud má záznam v registru identit. Další informace najdete v části Registr identit v [Příručce pro vývojáře pro službu IoT Hub][lnk-devguide-identity]. Když spustíte tuto konzolovou aplikaci, vygeneruje jedinečné ID a klíč zařízení i modulu. Vaše zařízení a modul použijí tyto hodnoty k vlastní identifikaci při odesílání zpráv typu zařízení-cloud do služby IoT Hub. V ID se rozlišují malá a velká písmena.

Přidejte následující kód do souboru Python:

```Python
import sys
import iothub_service_client
from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod, IoTHubError

CONNECTION_STRING = "YourConnString"
DEVICE_ID = "myFirstDevice"
MODULE_ID = "myFirstModule"

try:
    # RegistryManager
    iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

    # CreateDevice
    primary_key = ""
    secondary_key = ""
    auth_method = IoTHubRegistryManagerAuthMethod.SHARED_PRIVATE_KEY
    new_device = iothub_registry_manager.create_device(DEVICE_ID, primary_key, secondary_key, auth_method)
    print("new_device <" + DEVICE_ID + "> has primary key = " + new_device.primaryKey)

    # CreateModule
    new_module = iothub_registry_manager.create_module(DEVICE_ID, primary_key, secondary_key, MODULE_ID, auth_method)
    print("device/new_module <" + DEVICE_ID + "/" + MODULE_ID + "> has primary key = " + new_module.primaryKey)

except IoTHubError as iothub_error:
    print ( "Unexpected error {0}".format(iothub_error) )
except KeyboardInterrupt:
    print ( "IoTHubRegistryManager sample stopped" )
```

Tato aplikace vytvoří identitu zařízení s ID **myFirstDevice** a identita modul s ID **myFirstModule** pod zařízení **myFirstDevice**. (Pokud toto ID modulu již v registru identit existuje, kód jednoduše načte informace o stávajícím modulu.) Aplikace pak zobrazí primární klíč pro danou identitu. Tento klíč v aplikaci simulovaného modulu slouží k připojení k centru IoT.

> [!NOTE]
> V registru identit služby IoT Hub se uchovávají identity zařízení a modulů pouze za účelem bezpečného přístupu k centru IoT. Registr identit ukládá ID zařízení a klíče pro použití jako bezpečnostních pověření. Registr identit také ukládá povolené a zakázané příznaky pro jednotlivá zařízení, pomocí kterých můžete zakázat přístup pro dané zařízení. Pokud aplikace potřebuje pro zařízení ukládat další metadata, měla by používat úložiště pro konkrétní aplikaci. Pro identity modulů neexistuje žádný příznak povoleno/zakázáno. Další informace najdete v [Příručce pro vývojáře pro službu IoT Hub][lnk-devguide-identity].


<a id="D2C_csharp"></a>
## <a name="update-the-module-twin-using-python-device-sdk"></a>Aktualizovat twin modulu pomocí zařízení Python SDK

V této části vytvoříte Python aplikace na vašem simulované zařízení, která aktualizuje twin modul oznámil vlastnosti.

1. **Získání připojovacího řetězce modulu** – Teď se přihlaste k webu [Azure Portal][lnk-portal]. Přejděte do vaší služby IoT Hub a klikněte na Zařízení IoT. Vyhledejte identitu zařízení myFirstDevice a otevřete ji. Zobrazí se úspěšně vytvořená identita modulu myFirstModule. Zkopírujte připojovací řetězec modulu. Budete ho potřebovat v dalším kroku.

    ![Podrobnosti o modulu na webu Azure Portal][15]

2. **Vytvoření aplikace UpdateModuleTwinReportedProperties** přidejte následující `using` příkazy v horní části **Program.cs** souboru:

    ```Python
    import sys
    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod, IoTHubDeviceTwin, IoTHubError

    CONNECTION_STRING = "FILL IN CONNECTION STRING"
    DEVICE_ID = "MyFirstDevice"
    MODULE_ID = "MyFirstModule"

    UPDATE_JSON = "{\"properties\":{\"desired\":{\"telemetryInterval\":122}}}"

    try:
        iothub_twin = IoTHubDeviceTwin(CONNECTION_STRING)

        twin_info = iothub_twin.get_twin(DEVICE_ID, MODULE_ID)
        print ( "" )
        print ( "Twin before update    :" )
        print ( "{0}".format(twin_info) )

        twin_info_updated = iothub_twin.update_twin(DEVICE_ID, MODULE_ID, UPDATE_JSON)
        print ( "" )
        print ( "Twin after update     :" )
        print ( "{0}".format(twin_info_updated) )

    except IoTHubError as iothub_error:
        print ( "Unexpected error {0}".format(iothub_error) )
    except KeyboardInterrupt:
        print ( "IoTHubRegistryManager sample stopped" )
    ```

Tento vzorový kód ukazuje, jak načíst dvojče modulu a aktualizovat hlášené vlastnosti pomocí protokolu AMQP. 

## <a name="get-updates-on-the-device-side"></a>Získat aktualizace na straně zařízení
Kromě výše uvedeného kódu můžete přidat následující blok kódu získat aktualizace twin zprávu na vaše zařízení.

```Python
import random
import time
import sys
import iothub_client
from iothub_client import IoTHubModuleClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult

PROTOCOL = IoTHubTransportProvider.AMQP
CONNECTION_STRING = ""

def module_twin_callback(update_state, payload, user_context):
    print ("")
    print ("Twin callback called with:")
    print ("updateStatus: %s" % update_state )
    print ("context: %s" % user_context )
    print ("payload: %s" % payload )

try:
    module_client = IoTHubModuleClient(CONNECTION_STRING, PROTOCOL)
    module_client.set_module_twin_callback(module_twin_callback, 1234)

    print ("Waiting for incoming twin messages.  Hit Control-C to exit.")
    while True:

        time.sleep(1000000)

except IoTHubError as iothub_error:
    print ( "Unexpected error {0}".format(iothub_error) )
except KeyboardInterrupt:
    print ( "module client sample stopped" )
```


## <a name="next-steps"></a>Další postup

Chcete-li pokračovat v seznamování se službou IoT Hub a prozkoumat další scénáře IoT, podívejte se na tato témata:

* [Začínáme se správou zařízení][lnk-device-management]
* [Začínáme se službou IoT Edge][lnk-iot-edge]


<!-- Images. -->
[15]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/module-detail.JPG
<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-node-node-device-management-get-started.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/