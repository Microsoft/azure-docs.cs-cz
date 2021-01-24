---
title: Jak vytvořit adaptér pro IoT technologie Plug and Play most | Microsoft Docs
description: Identifikujte součásti adaptéru IoT technologie Plug and Play mostu. Přečtěte si, jak tento most rozšíříte tak, že napíšete vlastní adaptér.
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 9a7028dfaeb94e87366de7acfa8cebc4c2f4c767
ms.sourcegitcommit: 4d48a54d0a3f772c01171719a9b80ee9c41c0c5d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2021
ms.locfileid: "98746822"
---
# <a name="extend-the-iot-plug-and-play-bridge"></a>Rozšiřování technologie Plug and Play mostu IoT
[Most iot technologie Plug and Play](concepts-iot-pnp-bridge.md#iot-plug-and-play-bridge-architecture) umožňuje připojit stávající zařízení připojená k bráně ke službě IoT Hub. Pomocí tohoto mostu namapujete rozhraní IoT technologie Plug and Play na připojená zařízení. Rozhraní IoT technologie Plug and Play definuje telemetrii, kterou zařízení odesílá, vlastnosti synchronizované mezi zařízením a cloudem a příkazy, na které zařízení reaguje. Můžete nainstalovat a nakonfigurovat aplikaci Open-Source mostu v bránách systému Windows nebo Linux. Most se navíc dá spustit jako modul modulu runtime Azure IoT Edge.

Tento článek podrobně vysvětluje, jak:

- Rozšíříte technologie Plug and Play IoT most s adaptérem.
- Implementujte běžná zpětná volání pro adaptér mostu.

Jednoduchý příklad, který ukazuje, jak používat most, najdete v tématu [Postup připojení ukázky služby IoT technologie Plug and Play Bridge, která je spuštěna v systému Linux nebo Windows a IoT Hub](howto-use-iot-pnp-bridge.md).

Pokyny a ukázky v tomto článku předpokládají základní znalost pomocí [digitálních vláken Azure](../digital-twins/overview.md) a [technologie Plug and Play IoT](overview-iot-plug-and-play.md). Tento článek navíc předpokládá znalost [vytváření a nasazení mostu IoT technologie Plug and Play](howto-build-deploy-extend-pnp-bridge.md).

## <a name="design-guide-to-extend-the-iot-plug-and-play-bridge-with-an-adapter"></a>Průvodce návrhem pro rozšiřování služby IoT technologie Plug and Play mostu pomocí adaptéru

Pro rozšiřování schopností mostu můžete vytvářet vlastní adaptéry mostu.

Most používá adaptéry k těmto akcím:

- Navažte spojení mezi zařízením a cloudem.
- Povolí tok dat mezi zařízením a cloudem.
- Povolte správu zařízení z cloudu.

Každý adaptér mostu musí:

- Vytvořte rozhraní Digital vláknas.
- Pomocí rozhraní můžete navazovat funkce na straně zařízení na cloudové možnosti, jako jsou telemetrie, vlastnosti a příkazy.
- Navažte řízení a datovou komunikaci s hardwarem nebo firmwarem zařízení.

Každý adaptér mostu komunikuje s konkrétním typem zařízení podle toho, jak se adaptér připojuje a komunikuje se zařízením. I v případě, že komunikace se zařízením používá protokol handshake, může mít adaptér mostu několik způsobů, jak data ze zařízení interpretovat. V tomto scénáři adaptér mostu používá informace pro adaptér v konfiguračním souboru k určení *Konfigurace rozhraní* , které má adaptér použít k analýze dat.

Aby bylo možné komunikovat se zařízením, adaptér mostu používá komunikační protokol podporovaný zařízením a rozhraními API poskytovanými buď základním operačním systémem, nebo dodavatelem zařízení.

Pro interakci s cloudem adaptér mostu používá rozhraní API poskytovaná sadou SDK pro zařízení Azure IoT, která slouží k odesílání telemetrie, vytváření digitálních vlákenných rozhraní, odesílání aktualizací vlastností a vytváření funkcí zpětného volání pro aktualizace vlastností a příkazy.

### <a name="create-a-bridge-adapter"></a>Vytvoření adaptéru mostu

Most očekává, že adaptér mostu implementuje rozhraní API definovaná v rozhraní [_PNP_ADAPTER](https://github.com/Azure/iot-plug-and-play-bridge/blob/9964f7f9f77ecbf4db3b60960b69af57fd83a871/pnpbridge/src/pnpbridge/inc/pnpadapter_api.h#L296) :

```c
typedef struct _PNP_ADAPTER {
  // Identity of the IoT Plug and Play adapter that is retrieved from the config
  const char* identity;

  PNPBRIDGE_ADAPTER_CREATE createAdapter;
  PNPBRIDGE_COMPONENT_CREATE createPnpComponent;
  PNPBRIDGE_COMPONENT_START startPnpComponent;
  PNPBRIDGE_COMPONENT_STOP stopPnpComponent;
  PNPBRIDGE_COMPONENT_DESTROY destroyPnpComponent;
  PNPBRIDGE_ADAPTER_DESTOY destroyAdapter;
} PNP_ADAPTER, * PPNP_ADAPTER;
```

V tomto rozhraní:

- `PNPBRIDGE_ADAPTER_CREATE` Vytvoří adaptér a nastaví prostředky správy rozhraní. Adaptér může také při vytváření adaptéru spoléhat na globální parametry adaptéru. Tato funkce se volá jednou pro jeden adaptér.
- `PNPBRIDGE_COMPONENT_CREATE` Vytvoří digitální dvojitá klientská rozhraní a naváže funkce zpětného volání. Adaptér inicializuje komunikační kanál do zařízení. Adaptér může nastavit prostředky, aby umožnily tok telemetrie, ale nespustil telemetrie pro vytváření sestav `PNPBRIDGE_COMPONENT_START` , dokud se nevolá. Tato funkce se volá jednou pro každou součást rozhraní v konfiguračním souboru.
- `PNPBRIDGE_COMPONENT_START` je volána, aby adaptér mostu mohl začít předávat telemetrii ze zařízení do digitálního vlákna klienta. Tato funkce se volá jednou pro každou součást rozhraní v konfiguračním souboru.
- `PNPBRIDGE_COMPONENT_STOP` zastaví tok telemetrie.
- `PNPBRIDGE_COMPONENT_DESTROY` zničí z digitálního vlákna klienta a přidružených prostředků rozhraní. Tato funkce se volá jednou pro každou součást rozhraní v konfiguračním souboru, když se přestanou zpomalit most nebo když dojde k závažné chybě.
- `PNPBRIDGE_ADAPTER_DESTROY` vyčistí prostředky můstkového adaptéru.

### <a name="bridge-core-interaction-with-bridge-adapters"></a>Přemostění interakce jádra s adaptéry mostu

Následující seznam popisuje, co se stane, když se most spustí:

1. Když se most spustí, správce adaptéru mostu prohledá jednotlivé komponenty rozhraní definované v konfiguračním souboru a zavolá `PNPBRIDGE_ADAPTER_CREATE` příslušný adaptér. Adaptér může použít parametry konfigurace globálního adaptéru k nastavení prostředků pro podporu různých *konfigurací rozhraní*.
1. Pro každé zařízení v konfiguračním souboru inicializuje správce mostu vytvoření rozhraní voláním `PNPBRIDGE_COMPONENT_CREATE` příslušného můstkového adaptéru.
1. Adaptér obdrží všechna volitelná nastavení konfigurace adaptéru pro komponentu rozhraní a použije tyto informace k nastavení připojení k zařízení.
1. Adaptér vytvoří digitální dvojitá klientská rozhraní a naváže funkce zpětného volání pro aktualizace vlastností a příkazy. Navázání připojení zařízení by nemělo po úspěšném vytvoření digitálního vlákna na konci zablokovat návrat zpětných volání. Aktivní připojení zařízení nezávisí na aktivním klientovi rozhraní, který most vytvoří. Pokud dojde k chybě připojení, adaptér předpokládá, že zařízení není aktivní. Adaptér mostu se může rozhodnout pro vytvoření tohoto připojení znovu.
1. Až správce adaptéru mostu vytvoří všechny součásti rozhraní zadané v konfiguračním souboru, zaregistruje všechna rozhraní s Azure IoT Hub. Registrace je blokující asynchronní volání. Po dokončení volání aktivuje zpětné volání v adaptéru mostu, které umožňuje spustit zpracování vlastností a zpětných volání příkazů z cloudu.
1. Správce mostu pak zavolá `PNPBRIDGE_INTERFACE_START` na každou součást a adaptér mostu zahájí oznamování telemetrie do digitálního nevlákenního klienta.

### <a name="design-guidelines"></a>Pokyny pro návrh

Při vývoji nového adaptéru mostu postupujte podle těchto pokynů:

- Určete, které možnosti zařízení jsou podporované a jaká definice rozhraní komponent pomocí tohoto adaptéru vypadá jako.
- Určete, jaké rozhraní a globální parametry je potřeba, aby váš adaptér byl definovaný v konfiguračním souboru.
- Identifikujte komunikaci zařízení nižší úrovně, která je nutná pro podporu vlastností a příkazů komponenty.
- Určete, jak má adaptér analyzovat nezpracovaná data ze zařízení a převést je na typy telemetrie, které definuje definice rozhraní IoT technologie Plug and Play.
- Implementujte rozhraní můstk Adapter popsané výše.
- Přidejte nový adaptér k manifestu adaptéru a sestavte most.

### <a name="enable-a-new-bridge-adapter"></a>Povolit nový adaptér mostu

Adaptéry v mostu povolíte přidáním odkazu do [adapter_manifest. c](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/shared/adapter_manifest.c):

```c
  extern PNP_ADAPTER MyPnpAdapter;
  PPNP_ADAPTER PNP_ADAPTER_MANIFEST[] = {
    .
    .
    &MyPnpAdapter
  }
```

> [!IMPORTANT]
> Zpětná volání adaptérů mostu se volají postupně. Adaptér by neměl zablokovat zpětné volání, protože to brání v jádru mostu v průběhu provádění.

### <a name="sample-camera-adapter"></a>Ukázkový adaptér kamery

[Soubor Readme adaptéru kamery](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/Camera/readme.md) popisuje ukázkový adaptér kamery, který můžete povolit.

## <a name="code-examples-for-common-adapter-scenarioscallbacks"></a>Příklady kódu pro běžné scénáře a zpětná volání adaptérů

V následující části najdete podrobné informace o tom, jak adaptér pro most by implementoval zpětná volání pro řadu běžných scénářů a použití v této části se zabývá následujícími zpětnými voláními:
- [Přijmout aktualizaci vlastnosti (Cloud do zařízení)](#receive-property-update-cloud-to-device)
- [Nahlášení aktualizace vlastnosti (zařízení do cloudu)](#report-a-property-update-device-to-cloud)
- [Poslat telemetrii (zařízení do cloudu)](#send-telemetry-device-to-cloud)
- [Příjem zpětného volání aktualizace příkazů z cloudu a jeho zpracování na straně zařízení (Cloud do zařízení)](#receive-command-update-callback-from-the-cloud-and-process-it-on-the-device-side-cloud-to-device)
- [Reakce na aktualizaci příkazu na straně zařízení (zařízení do cloudu)](#respond-to-command-update-on-the-device-side-device-to-cloud)

Níže uvedené příklady jsou založené na [ukázkovém adaptéru pro ekologický senzor](https://github.com/Azure/iot-plug-and-play-bridge/tree/master/pnpbridge/src/adapters/samples/environmental_sensor).

### <a name="receive-property-update-cloud-to-device"></a>Přijmout aktualizaci vlastnosti (Cloud do zařízení)
Prvním krokem je registrace funkce zpětného volání:

```c
PnpComponentHandleSetPropertyUpdateCallback(BridgeComponentHandle, EnvironmentSensor_ProcessPropertyUpdate);
```
Dalším krokem je implementace funkce zpětného volání pro čtení aktualizace vlastností na zařízení:

```c
void EnvironmentSensor_ProcessPropertyUpdate(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    void* userContextCallback
)
{
  // User context for the callback is set to the IoT Hub client handle, and therefore can be type-cast to the client handle type
    SampleEnvironmentalSensor_ProcessPropertyUpdate(userContextCallback, PropertyName, PropertyValue, version, PnpComponentHandle);
}

// SampleEnvironmentalSensor_ProcessPropertyUpdate receives updated properties from the server.  This implementation
// acts as a simple dispatcher to the functions to perform the actual processing.
void SampleEnvironmentalSensor_ProcessPropertyUpdate(
    void * ClientHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
  if (strcmp(PropertyName, sampleEnvironmentalSensorPropertyBrightness) == 0)
    {
        SampleEnvironmentalSensor_BrightnessCallback(ClientHandle, PropertyName, PropertyValue, version, PnpComponentHandle);
    }
    else
    {
        // If the property is not implemented by this interface, presently we only record a log message but do not have a mechanism to report back to the service
        LogError("Environmental Sensor Adapter:: Property name <%s> is not associated with this interface", PropertyName);
    }
}

// Process a property update for bright level.
static void SampleEnvironmentalSensor_BrightnessCallback(
    void * ClientHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
    IOTHUB_CLIENT_RESULT iothubClientResult;
    STRING_HANDLE jsonToSend = NULL;
    char targetBrightnessString[32];

    LogInfo("Environmental Sensor Adapter:: Brightness property invoked...");

    PENVIRONMENT_SENSOR EnvironmentalSensor = PnpComponentHandleGetContext(PnpComponentHandle);

    if (json_value_get_type(PropertyValue) != JSONNumber)
    {
        LogError("JSON field %s is not a number", PropertyName);
    }
    else if(EnvironmentalSensor == NULL || EnvironmentalSensor->SensorState == NULL)
    {
        LogError("Environmental sensor device context not initialized correctly.");
    }
    else if (SampleEnvironmentalSensor_ValidateBrightness(json_value_get_number(PropertyValue)))
    {
        EnvironmentalSensor->SensorState->brightness = (int) json_value_get_number(PropertyValue);
        if (snprintf(targetBrightnessString, sizeof(targetBrightnessString), 
            g_environmentalSensorBrightnessResponseFormat, EnvironmentalSensor->SensorState->brightness) < 0)
        {
            LogError("Unable to create target brightness string for reporting result");
        }
        else if ((jsonToSend = PnP_CreateReportedPropertyWithStatus(EnvironmentalSensor->SensorState->componentName,
                    PropertyName, targetBrightnessString, PNP_STATUS_SUCCESS, g_environmentalSensorPropertyResponseDescription,
                    version)) == NULL)
        {
            LogError("Unable to build reported property response");
        }
        else
        {
            const char* jsonToSendStr = STRING_c_str(jsonToSend);
            size_t jsonToSendStrLen = strlen(jsonToSendStr);

            if ((iothubClientResult = SampleEnvironmentalSensor_RouteReportedState(ClientHandle, PnpComponentHandle, (const unsigned char*)jsonToSendStr, jsonToSendStrLen,
                                        SampleEnvironmentalSensor_PropertyCallback,
                                        (void*) &EnvironmentalSensor->SensorState->brightness)) != IOTHUB_CLIENT_OK)
            {
                LogError("Environmental Sensor Adapter:: SampleEnvironmentalSensor_RouteReportedState for brightness failed, error=%d", iothubClientResult);
            }
            else
            {
                LogInfo("Environmental Sensor Adapter:: Successfully queued Property update for Brightness for component=%s", EnvironmentalSensor->SensorState->componentName);
            }

            STRING_delete(jsonToSend);
        }
    }
}

```

### <a name="report-a-property-update-device-to-cloud"></a>Nahlášení aktualizace vlastnosti (zařízení do cloudu)
V jakémkoli okamžiku, kdy je vaše komponenta vytvořená, může vaše zařízení nahlásit vlastnosti do cloudu se stavem: 
```c
// Environmental sensor's read-only property, device state indiciating whether its online or not
//
static const char sampleDeviceStateProperty[] = "state";
static const unsigned char sampleDeviceStateData[] = "true";
static const int sampleDeviceStateDataLen = sizeof(sampleDeviceStateData) - 1;

// Sends a reported property for device state of this simulated device.
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_ReportDeviceStateAsync(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const char * ComponentName)
{

    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;
    STRING_HANDLE jsonToSend = NULL;

    if ((jsonToSend = PnP_CreateReportedProperty(ComponentName, sampleDeviceStateProperty, (const char*) sampleDeviceStateData)) == NULL)
    {
        LogError("Unable to build reported property response for propertyName=%s, propertyValue=%s", sampleDeviceStateProperty, sampleDeviceStateData);
    }
    else
    {
        const char* jsonToSendStr = STRING_c_str(jsonToSend);
        size_t jsonToSendStrLen = strlen(jsonToSendStr);

        if ((iothubClientResult = SampleEnvironmentalSensor_RouteReportedState(NULL, PnpComponentHandle, (const unsigned char*)jsonToSendStr, jsonToSendStrLen,
            SampleEnvironmentalSensor_PropertyCallback, (void*)sampleDeviceStateProperty)) != IOTHUB_CLIENT_OK)
        {
            LogError("Environmental Sensor Adapter:: Unable to send reported state for property=%s, error=%d",
                                sampleDeviceStateProperty, iothubClientResult);
        }
        else
        {
            LogInfo("Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=%s, propertyValue=%s",
                        sampleDeviceStateProperty, sampleDeviceStateData);
        }

        STRING_delete(jsonToSend);
    }

    return iothubClientResult;
}


// Routes the reported property for device or module client. This function can be called either by passing a valid client handle or by passing
// a NULL client handle after components have been started such that the client handle can be extracted from the PnpComponentHandle
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_RouteReportedState(
    void * ClientHandle,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const unsigned char * ReportedState,
    size_t Size,
    IOTHUB_CLIENT_REPORTED_STATE_CALLBACK ReportedStateCallback,
    void * UserContextCallback)
{
    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;

    PNP_BRIDGE_CLIENT_HANDLE clientHandle = (ClientHandle != NULL) ?
            (PNP_BRIDGE_CLIENT_HANDLE) ClientHandle : PnpComponentHandleGetClientHandle(PnpComponentHandle);

    if ((iothubClientResult = PnpBridgeClient_SendReportedState(clientHandle, ReportedState, Size,
            ReportedStateCallback, UserContextCallback)) != IOTHUB_CLIENT_OK)
    {
        LogError("IoTHub client call to _SendReportedState failed with error code %d", iothubClientResult);
        goto exit;
    }
    else
    {
        LogInfo("IoTHub client call to _SendReportedState succeeded");
    }

exit:
    return iothubClientResult;
}

```

### <a name="send-telemetry-device-to-cloud"></a>Poslat telemetrii (zařízení do cloudu)
```c
//
// SampleEnvironmentalSensor_SendTelemetryMessagesAsync is periodically invoked by the caller to
// send telemetry containing the current temperature and humidity (in both cases random numbers
// so this sample will work on platforms without these sensors).
//
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_SendTelemetryMessagesAsync(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
    IOTHUB_CLIENT_RESULT result = IOTHUB_CLIENT_OK;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
    PENVIRONMENT_SENSOR device = PnpComponentHandleGetContext(PnpComponentHandle);

    float currentTemperature = 20.0f + ((float)rand() / RAND_MAX) * 15.0f;
    float currentHumidity = 60.0f + ((float)rand() / RAND_MAX) * 20.0f;

    char currentMessage[128];
    sprintf(currentMessage, "{\"%s\":%.3f, \"%s\":%.3f}", SampleEnvironmentalSensor_TemperatureTelemetry, 
            currentTemperature, SampleEnvironmentalSensor_HumidityTelemetry, currentHumidity);


    if ((messageHandle = PnP_CreateTelemetryMessageHandle(device->SensorState->componentName, currentMessage)) == NULL)
    {
        LogError("Environmental Sensor Adapter:: PnP_CreateTelemetryMessageHandle failed.");
    }
    else if ((result = SampleEnvironmentalSensor_RouteSendEventAsync(PnpComponentHandle, messageHandle,
            SampleEnvironmentalSensor_TelemetryCallback, device)) != IOTHUB_CLIENT_OK)
    {
        LogError("Environmental Sensor Adapter:: SampleEnvironmentalSensor_RouteSendEventAsync failed, error=%d", result);
    }

    IoTHubMessage_Destroy(messageHandle);

    return result;
}

// Routes the sending asynchronous events for device or module client
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_RouteSendEventAsync(
        PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
        IOTHUB_MESSAGE_HANDLE EventMessageHandle,
        IOTHUB_CLIENT_EVENT_CONFIRMATION_CALLBACK EventConfirmationCallback,
        void * UserContextCallback)
{
    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;
    PNP_BRIDGE_CLIENT_HANDLE clientHandle = PnpComponentHandleGetClientHandle(PnpComponentHandle);
    if ((iothubClientResult = PnpBridgeClient_SendEventAsync(clientHandle, EventMessageHandle,
            EventConfirmationCallback, UserContextCallback)) != IOTHUB_CLIENT_OK)
    {
        LogError("IoTHub client call to _SendEventAsync failed with error code %d", iothubClientResult);
        goto exit;
    }
    else
    {
        LogInfo("IoTHub client call to _SendEventAsync succeeded");
    }

exit:
    return iothubClientResult;
}

```
### <a name="receive-command-update-callback-from-the-cloud-and-process-it-on-the-device-side-cloud-to-device"></a>Příjem zpětného volání aktualizace příkazů z cloudu a jeho zpracování na straně zařízení (Cloud do zařízení)
```c
// SampleEnvironmentalSensor_ProcessCommandUpdate receives commands from the server.  This implementation acts as a simple dispatcher
// to the functions to perform the actual processing.
int SampleEnvironmentalSensor_ProcessCommandUpdate(
    PENVIRONMENT_SENSOR EnvironmentalSensor,
    const char* CommandName,
    JSON_Value* CommandValue,
    unsigned char** CommandResponse,
    size_t* CommandResponseSize)
{
    if (strcmp(CommandName, sampleEnvironmentalSensorCommandBlink) == 0)
    {
        return SampleEnvironmentalSensor_BlinkCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else if (strcmp(CommandName, sampleEnvironmentalSensorCommandTurnOn) == 0)
    {
        return SampleEnvironmentalSensor_TurnOnLightCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else if (strcmp(CommandName, sampleEnvironmentalSensorCommandTurnOff) == 0)
    {
        return SampleEnvironmentalSensor_TurnOffLightCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else
    {
        // If the command is not implemented by this interface, by convention we return a 404 error to server.
        LogError("Environmental Sensor Adapter:: Command name <%s> is not associated with this interface", CommandName);
        return SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_NotImplemented);
    }
}

// Implement the callback to process the command "blink". Information pertaining to the request is
// specified in the CommandValue parameter, and the callback fills out data it wishes to
// return to the caller on the service in CommandResponse.

static int SampleEnvironmentalSensor_BlinkCallback(
    PENVIRONMENT_SENSOR EnvironmentalSensor,
    JSON_Value* CommandValue,
    unsigned char** CommandResponse,
    size_t* CommandResponseSize)
{
    int result = PNP_STATUS_SUCCESS;
    int BlinkInterval = 0;

    LogInfo("Environmental Sensor Adapter:: Blink command invoked. It has been invoked %d times previously", EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled);

    if (json_value_get_type(CommandValue) != JSONNumber)
    {
        LogError("Cannot retrieve blink interval for blink command");
        result = PNP_STATUS_BAD_FORMAT;
    }
    else
    {
        BlinkInterval = (int)json_value_get_number(CommandValue);
        LogInfo("Environmental Sensor Adapter:: Blinking with interval=%d second(s)", BlinkInterval);
        EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled++;
        EnvironmentalSensor->SensorState->blinkInterval = BlinkInterval;

        result = SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_BlinkResponse);
    }

    return result;
}

```
### <a name="respond-to-command-update-on-the-device-side-device-to-cloud"></a>Reakce na aktualizaci příkazu na straně zařízení (zařízení do cloudu)

```c
    static int SampleEnvironmentalSensor_BlinkCallback(
        PENVIRONMENT_SENSOR EnvironmentalSensor,
        JSON_Value* CommandValue,
        unsigned char** CommandResponse,
        size_t* CommandResponseSize)
    {
        int result = PNP_STATUS_SUCCESS;
        int BlinkInterval = 0;
    
        LogInfo("Environmental Sensor Adapter:: Blink command invoked. It has been invoked %d times previously", EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled);
    
        if (json_value_get_type(CommandValue) != JSONNumber)
        {
            LogError("Cannot retrieve blink interval for blink command");
            result = PNP_STATUS_BAD_FORMAT;
        }
        else
        {
            BlinkInterval = (int)json_value_get_number(CommandValue);
            LogInfo("Environmental Sensor Adapter:: Blinking with interval=%d second(s)", BlinkInterval);
            EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled++;
            EnvironmentalSensor->SensorState->blinkInterval = BlinkInterval;
    
            result = SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_BlinkResponse);
        }
    
        return result;
    }
    
    // SampleEnvironmentalSensor_SetCommandResponse is a helper that fills out a command response
    static int SampleEnvironmentalSensor_SetCommandResponse(
        unsigned char** CommandResponse,
        size_t* CommandResponseSize,
        const unsigned char* ResponseData)
    {
        int result = PNP_STATUS_SUCCESS;
        if (ResponseData == NULL)
        {
            LogError("Environmental Sensor Adapter:: Response Data is empty");
            *CommandResponseSize = 0;
            return PNP_STATUS_INTERNAL_ERROR;
        }
    
        *CommandResponseSize = strlen((char*)ResponseData);
        memset(CommandResponse, 0, sizeof(*CommandResponse));
    
        // Allocate a copy of the response data to return to the invoker. Caller will free this.
        if (mallocAndStrcpy_s((char**)CommandResponse, (char*)ResponseData) != 0)
        {
            LogError("Environmental Sensor Adapter:: Unable to allocate response data");
            result = PNP_STATUS_INTERNAL_ERROR;
        }
    
        return result;
}
```

## <a name="next-steps"></a>Další kroky

Další informace o službě IoT technologie Plug and Play Bridge najdete v úložišti GitHub [technologie Plug and Play mostu IoT](https://github.com/Azure/iot-plug-and-play-bridge) .
