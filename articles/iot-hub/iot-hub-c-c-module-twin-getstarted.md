---
title: Začínáme s Azure IoT Hub modulu identity a modul twin (C) | Microsoft Docs
description: Naučte se vytvořit modul identitu a aktualizovat twin modulu pomocí sady SDK služby IoT pro C.
author: chrissie926
manager: ''
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: conceptual
ms.date: 06/25/2018
ms.author: menchi
ms.openlocfilehash: 5a9e3fb741563d2804c916dc3db3e02a79147b6c
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37035807"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-c-back-end-and-c-device"></a>Začínáme s identity a modul twin serveru IoT Hub modulu pomocí C back-end a C zařízení

> [!NOTE]
> [Identity modulů a dvojčata modulů](iot-hub-devguide-module-twins.md) se podobají identitě zařízení a dvojčeti zařízení služby Azure IoT Hub, ale poskytují větší úroveň členitosti. Zatímco identita zařízení a dvojče zařízení služby Azure IoT Hub umožňují back-endové aplikaci konfigurovat zařízení a poskytují vhled do stavu zařízení, identita modulu a dvojče modulu poskytují tyto možnosti pro jednotlivé součásti zařízení. Na způsobilých zařízeních s několika součástmi, jako jsou zařízení s operačním systémem nebo zařízení s firmwarem, to umožňuje izolovanou konfiguraci a vhled do stavu jednotlivých součástí.

Na konci tohoto kurzu máte dvě C aplikace:

* Aplikaci **CreateIdentities**, která vytvoří identitu zařízení, identitu modulu a přidružený klíč zabezpečení pro připojení klientů zařízení a modulu.
* Aplikaci **UpdateModuleTwinReportedProperties**, která do služby IoT Hub odešle aktualizované hlášené vlastnosti dvojčete modulu.

> [!NOTE]
> Informace o sadách SDK služby Azure IoT Hub, s jejichž pomocí můžete sestavit aplikace, které poběží v zařízení, i back-end vašeho řešení, najdete v tématu [Sady SDK služby IoT Hub][lnk-hub-sdks].

Pro absolvování tohoto kurzu potřebujete:

* Aktivní účet Azure. (Pokud účet nemáte, můžete si během několika minut vytvořit [bezplatný účet][lnk-free-trial].)
* Centrum IoT.
* Nainstalujte si nejnovější verzi [C SDK](https://github.com/Azure/azure-iot-sdk-c).


Nyní jste vytvořili službu IoT Hub a máte název hostitele a připojovací řetězec služby IoT Hub, které potřebujete k dokončení kurzu.

<a id="DeviceIdentity_csharp"></a>
## <a name="create-a-device-identity-and-a-module-identity-in-iot-hub"></a>Vytvoření identity zařízení a identitu modulu ve IoT Hub.

V této části vytvoříte C aplikaci, která v registru identit ve službě IoT hub vytvoří identitu zařízení a modul identity. Zařízení nebo modul je možné připojit k centru IoT, pouze pokud má záznam v registru identit. Další informace najdete v části Registr identit v [Příručce pro vývojáře pro službu IoT Hub][lnk-devguide-identity]. Když spustíte tuto konzolovou aplikaci, vygeneruje jedinečné ID a klíč zařízení i modulu. Vaše zařízení a modul použijí tyto hodnoty k vlastní identifikaci při odesílání zpráv typu zařízení-cloud do služby IoT Hub. V ID se rozlišují malá a velká písmena.

Přidejte následující kód do souboru C:

```C
#include <stdio.h>
#include <stdlib.h>

#include "azure_c_shared_utility/crt_abstractions.h"
#include "azure_c_shared_utility/threadapi.h"
#include "azure_c_shared_utility/platform.h"

#include "iothub_service_client_auth.h"
#include "iothub_registrymanager.h"

static const char* hubConnectionString ="[your hub's connection string]"; // modify

static void createDevice(IOTHUB_REGISTRYMANAGER_HANDLE iotHubRegistryManagerHandle, const char* deviceId)
{
    IOTHUB_REGISTRY_DEVICE_CREATE_EX deviceCreateInfo;
    IOTHUB_REGISTRYMANAGER_RESULT result;
    
    (void)memset(&deviceCreateInfo, 0, sizeof(deviceCreateInfo));
    deviceCreateInfo.version = 1;
    deviceCreateInfo.deviceId = deviceId;
    deviceCreateInfo.primaryKey = "";
    deviceCreateInfo.secondaryKey = "";
    deviceCreateInfo.authMethod = IOTHUB_REGISTRYMANAGER_AUTH_SPK;
    
    IOTHUB_DEVICE_EX deviceInfoEx;
    memset(&deviceInfoEx, 0, sizeof(deviceInfoEx));
    deviceInfoEx.version = 1;
    
    // Create device
    result = IoTHubRegistryManager_CreateDevice_Ex(iotHubRegistryManagerHandle, &deviceCreateInfo, &deviceInfoEx);
    if (result == IOTHUB_REGISTRYMANAGER_OK)
    {
        (void)printf("IoTHubRegistryManager_CreateDevice: Device has been created successfully: deviceId=%s, primaryKey=%s\n", deviceInfoEx.deviceId, deviceInfoEx.primaryKey);
    }
    else if (result == IOTHUB_REGISTRYMANAGER_DEVICE_EXIST)
    {
        (void)printf("IoTHubRegistryManager_CreateDevice: Device already exists\n");
    }
    else if (result == IOTHUB_REGISTRYMANAGER_ERROR)
    {
        (void)printf("IoTHubRegistryManager_CreateDevice failed\n");
    }
    // You will need to Free the returned device information after it was created
    IoTHubRegistryManager_FreeDeviceExMembers(&deviceInfoEx);
}

static void createModule(IOTHUB_REGISTRYMANAGER_HANDLE iotHubRegistryManagerHandle, const char* deviceId, const char* moduleId)
{
    IOTHUB_REGISTRY_MODULE_CREATE moduleCreateInfo;
    IOTHUB_REGISTRYMANAGER_RESULT result;    
    
    (void)memset(&moduleCreateInfo, 0, sizeof(moduleCreateInfo));
    moduleCreateInfo.version = 1;
    moduleCreateInfo.deviceId = deviceId;
    moduleCreateInfo.moduleId = moduleId;
    moduleCreateInfo.primaryKey = "";
    moduleCreateInfo.secondaryKey = "";
    moduleCreateInfo.authMethod = IOTHUB_REGISTRYMANAGER_AUTH_SPK;
    
    IOTHUB_MODULE moduleInfo;
    memset(&moduleInfo, 0, sizeof(moduleInfo));
    moduleInfo.version = 1;
    
    // Create module
    result = IoTHubRegistryManager_CreateModule(iotHubRegistryManagerHandle, &moduleCreateInfo, &moduleInfo);
    if (result == IOTHUB_REGISTRYMANAGER_OK)
    {
        (void)printf("IoTHubRegistryManager_CreateModule: Module has been created successfully: deviceId=%s, moduleId=%s, primaryKey=%s\n", moduleInfo.deviceId, moduleInfo.moduleId, moduleInfo.primaryKey);
    }
    else if (result == IOTHUB_REGISTRYMANAGER_DEVICE_EXIST)
    {
        (void)printf("IoTHubRegistryManager_CreateModule: Module already exists\n");
    }
    else if (result == IOTHUB_REGISTRYMANAGER_ERROR)
    {
        (void)printf("IoTHubRegistryManager_CreateModule failed\n");
    }
    // You will need to Free the returned module information after it was created
    IoTHubRegistryManager_FreeModuleMembers(&moduleInfo);
}

int main(void)
{
    (void)platform_init();

    const char* deviceId = "myFirstDevice";
    const char* moduleId = "myFirstModule";
    IOTHUB_SERVICE_CLIENT_AUTH_HANDLE iotHubServiceClientHandle = NULL;
    IOTHUB_REGISTRYMANAGER_HANDLE iotHubRegistryManagerHandle = NULL;

    if ((iotHubServiceClientHandle = IoTHubServiceClientAuth_CreateFromConnectionString(hubConnectionString)) == NULL)
    {
        (void)printf("IoTHubServiceClientAuth_CreateFromConnectionString failed\n");
    }
    else if ((iotHubRegistryManagerHandle = IoTHubRegistryManager_Create(iotHubServiceClientHandle)) == NULL)
    {
        (void)printf("IoTHubServiceClientAuth_CreateFromConnectionString failed\n");
    }
    else
    {
        createDevice(iotHubRegistryManagerHandle, deviceId);
        createModule(iotHubRegistryManagerHandle, deviceId, moduleId);
    }

    if (iotHubRegistryManagerHandle != NULL)
    {
        (void)printf("Calling IoTHubRegistryManager_Destroy...\n");
        IoTHubRegistryManager_Destroy(iotHubRegistryManagerHandle);
    }

    if (iotHubServiceClientHandle != NULL)
    {
        (void)printf("Calling IoTHubServiceClientAuth_Destroy...\n");
        IoTHubServiceClientAuth_Destroy(iotHubServiceClientHandle);
    }
    
    platform_deinit();
    return 0;
}
```

Tato aplikace vytvoří identitu zařízení s ID **myFirstDevice** a identita modul s ID **myFirstModule** pod zařízení **myFirstDevice**. (Pokud toto ID modulu již v registru identit existuje, kód jednoduše načte informace o stávajícím modulu.) Aplikace pak zobrazí primární klíč pro danou identitu. Tento klíč v aplikaci simulovaného modulu slouží k připojení k centru IoT.

> [!NOTE]
> V registru identit služby IoT Hub se uchovávají identity zařízení a modulů pouze za účelem bezpečného přístupu k centru IoT. Registr identit ukládá ID zařízení a klíče pro použití jako bezpečnostních pověření. Registr identit také ukládá povolené a zakázané příznaky pro jednotlivá zařízení, pomocí kterých můžete zakázat přístup pro dané zařízení. Pokud aplikace potřebuje pro zařízení ukládat další metadata, měla by používat úložiště pro konkrétní aplikaci. Pro identity modulů neexistuje žádný příznak povoleno/zakázáno. Další informace najdete v [Příručce pro vývojáře pro službu IoT Hub][lnk-devguide-identity].


<a id="D2C_csharp"></a>
## <a name="update-the-module-twin-using-c-device-sdk"></a>Aktualizovat twin modulu pomocí zařízení C sady SDK

V této části vytvoříte aplikaci C na vaše simulované zařízení, která aktualizuje modul twin hlásí vlastnosti.

1. **Získání připojovacího řetězce modulu** – Teď se přihlaste k webu [Azure Portal][lnk-portal]. Přejděte do vaší služby IoT Hub a klikněte na Zařízení IoT. Vyhledejte identitu zařízení myFirstDevice a otevřete ji. Zobrazí se úspěšně vytvořená identita modulu myFirstModule. Zkopírujte připojovací řetězec modulu. Budete ho potřebovat v dalším kroku.

    ![Podrobnosti o modulu na webu Azure Portal][15]

2. **Vytvoření aplikace UpdateModuleTwinReportedProperties** přidejte následující `using` příkazy v horní části **Program.cs** souboru:

    ```C
    #include <stdio.h>
    #include <stdlib.h>

    #include "azure_c_shared_utility/crt_abstractions.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"

    #include "iothub_service_client_auth.h"
    #include "iothub_devicetwin.h"

    const char* deviceId = "bugbash-test-2";
    const char* moduleId = "module-id-1";
    static const char* hubConnectionString ="[your hub's connection string]"; // modify
    const char* testJson = "{\"properties\":{\"desired\":{\"integer_property\": b-1234, \"string_property\": \"abcd\"}}}";

    int main(void)
    {
        (void)platform_init();

        IOTHUB_SERVICE_CLIENT_AUTH_HANDLE iotHubServiceClientHandle = NULL;
        IOTHUB_SERVICE_CLIENT_DEVICE_TWIN_HANDLE iothubDeviceTwinHandle = NULL;

        if ((iotHubServiceClientHandle = IoTHubServiceClientAuth_CreateFromConnectionString(moduleConnectionString)) == NULL)
        {
            (void)printf("IoTHubServiceClientAuth_CreateFromConnectionString failed\n");
        }
        else if ((iothubDeviceTwinHandle = IoTHubDeviceTwin_Create(iotHubServiceClientHandle)) == NULL)
        {
            (void)printf("IoTHubServiceClientAuth_CreateFromConnectionString failed\n");
        }
        else
        {
            char *result = IoTHubDeviceTwin_UpdateModuleTwin(iothubDeviceTwinHandle, deviceId, moduleId, testJson);
            printf("IoTHubDeviceTwin_UpdateModuleTwin returned %s\n", result);
        }

        if (iothubDeviceTwinHandle != NULL)
        {
            (void)printf("Calling IoTHubDeviceTwin_Destroy...\n");
            IoTHubDeviceTwin_Destroy(iothubDeviceTwinHandle);
        }

        if (iotHubServiceClientHandle != NULL)
        {
            (void)printf("Calling IoTHubServiceClientAuth_Destroy...\n");
            IoTHubServiceClientAuth_Destroy(iotHubServiceClientHandle);
        }
        
        platform_deinit();
        return 0;
    }
    ```

Tento příklad ukazuje, jak načtení modulu twin a hlášen vlastnostech aktualizace. 

## <a name="get-updates-on-the-device-side"></a>Získat aktualizace na straně zařízení
Kromě výše uvedeného kódu můžete přidat následující blok kódu získat aktualizace twin zprávu na vaše zařízení.

```C
#include <stdio.h>
#include <stdlib.h>

#include "azure_c_shared_utility/crt_abstractions.h"
#include "azure_c_shared_utility/macro_utils.h"
#include "azure_c_shared_utility/threadapi.h"
#include "azure_c_shared_utility/platform.h"
#include "iothub_module_client_ll.h"
#include "iothub_client_options.h"
#include "iothub_message.h"

// The protocol you wish to use should be uncommented
//
//#define SAMPLE_MQTT
//#define SAMPLE_MQTT_OVER_WEBSOCKETS
#define SAMPLE_AMQP
//#define SAMPLE_AMQP_OVER_WEBSOCKETS
//#define SAMPLE_HTTP

#ifdef SAMPLE_MQTT
    #include "iothubtransportmqtt.h"
#endif // SAMPLE_MQTT
#ifdef SAMPLE_MQTT_OVER_WEBSOCKETS
    #include "iothubtransportmqtt_websockets.h"
#endif // SAMPLE_MQTT_OVER_WEBSOCKETS
#ifdef SAMPLE_AMQP
    #include "iothubtransportamqp.h"
#endif // SAMPLE_AMQP
#ifdef SAMPLE_AMQP_OVER_WEBSOCKETS
    #include "iothubtransportamqp_websockets.h"
#endif // SAMPLE_AMQP_OVER_WEBSOCKETS
#ifdef SAMPLE_HTTP
    #include "iothubtransporthttp.h"
#endif // SAMPLE_HTTP

/* Paste in the your iothub connection string  */
static const char* connectionString = "[Fill in connection string]";

static bool g_continueRunning;
#define DOWORK_LOOP_NUM     3

static void deviceTwinCallback(DEVICE_TWIN_UPDATE_STATE update_state, const unsigned char* payLoad, size_t size, void* userContextCallback)
{
    (void)userContextCallback;

    printf("Device Twin update received (state=%s, size=%zu): %s\r\n", 
        ENUM_TO_STRING(DEVICE_TWIN_UPDATE_STATE, update_state), size, payLoad);
}

static void reportedStateCallback(int status_code, void* userContextCallback)
{
    (void)userContextCallback;
    printf("Device Twin reported properties update completed with result: %d\r\n", status_code);

    g_continueRunning = false;
}


void iothub_module_client_sample_device_twin_run(void)
{
    IOTHUB_CLIENT_TRANSPORT_PROVIDER protocol;
    IOTHUB_MODULE_CLIENT_LL_HANDLE iotHubModuleClientHandle;
    g_continueRunning = true;

    // Select the Protocol to use with the connection
#ifdef SAMPLE_MQTT
    protocol = MQTT_Protocol;
#endif // SAMPLE_MQTT
#ifdef SAMPLE_MQTT_OVER_WEBSOCKETS
    protocol = MQTT_WebSocket_Protocol;
#endif // SAMPLE_MQTT_OVER_WEBSOCKETS
#ifdef SAMPLE_AMQP
    protocol = AMQP_Protocol;
#endif // SAMPLE_AMQP
#ifdef SAMPLE_AMQP_OVER_WEBSOCKETS
    protocol = AMQP_Protocol_over_WebSocketsTls;
#endif // SAMPLE_AMQP_OVER_WEBSOCKETS
#ifdef SAMPLE_HTTP
    protocol = HTTP_Protocol;
#endif // SAMPLE_HTTP

    if (platform_init() != 0)
    {
        (void)printf("Failed to initialize the platform.\r\n");
    }
    else
    {
        if ((iotHubModuleClientHandle = IoTHubModuleClient_LL_CreateFromConnectionString(connectionString, protocol)) == NULL)
        {
            (void)printf("ERROR: iotHubModuleClientHandle is NULL!\r\n");
        }
        else
        {
            bool traceOn = true;
            const char* reportedState = "{ 'device_property': 'new_value'}";
            size_t reportedStateSize = strlen(reportedState);

            (void)IoTHubModuleClient_LL_SetOption(iotHubModuleClientHandle, OPTION_LOG_TRACE, &traceOn);

            // Check the return of all API calls when developing your solution. Return checks ommited for sample simplification.

            (void)IoTHubModuleClient_LL_SetModuleTwinCallback(iotHubModuleClientHandle, deviceTwinCallback, iotHubModuleClientHandle);
            (void)IoTHubModuleClient_LL_SendReportedState(iotHubModuleClientHandle, (const unsigned char*)reportedState, reportedStateSize, reportedStateCallback, iotHubModuleClientHandle);

            do
            {
                IoTHubModuleClient_LL_DoWork(iotHubModuleClientHandle);
                ThreadAPI_Sleep(1);
            } while (g_continueRunning);

            for (size_t index = 0; index < DOWORK_LOOP_NUM; index++)
            {
                IoTHubModuleClient_LL_DoWork(iotHubModuleClientHandle);
                ThreadAPI_Sleep(1);
            }

            IoTHubModuleClient_LL_Destroy(iotHubModuleClientHandle);
        }
        platform_deinit();
    }
}

int main(void)
{
    iothub_module_client_sample_device_twin_run();
    return 0;
}
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