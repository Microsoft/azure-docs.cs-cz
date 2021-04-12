---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 03/31/2021
ms.openlocfilehash: 097886e5e1942dd957b8fa3d9b8599177c229747
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106491062"
---
## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto článku potřebujete aplikaci Azure IoT Central vytvořenou pomocí šablony **vlastní aplikace** . Další informace najdete v [rychlém startu k vytvoření aplikace](../articles/iot-central/core/quick-deploy-iot-central.md). Aplikace musí být vytvořená na základě 14. července 2020 nebo po ní.

Tento kurz můžete spustit v systému Linux nebo Windows. Příkazy prostředí v tomto kurzu se řídí konvencí pro oddělovače cest pro Linux `/` , pokud sledujete v systému Windows, nezapomeňte tyto oddělovače pro použít `\` .

Požadavky se liší podle operačního systému:

### <a name="linux"></a>Linux

V tomto kurzu se předpokládá, že používáte Ubuntu Linux. Kroky v tomto kurzu byly testovány pomocí Ubuntu 18,04.

K dokončení tohoto kurzu v systému Linux nainstalujte do svého místního prostředí systému Linux následující software:

Pomocí příkazu nainstalujte **RSZ**, **Git**, **cmake** a všechny požadované závislosti `apt-get` :

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Ověřte, že verze nástroje `cmake` je nad **2.8.12** a verze **RSZ** je vyšší než **4.4.7**.

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows

Pro dokončení tohoto kurzu ve Windows nainstalujte do svého místního prostředí Windows následující software:

* [Visual Studio (komunita, Professional nebo Enterprise)](https://visualstudio.microsoft.com/downloads/) – nezapomeňte při [instalaci](/cpp/build/vscpp-step-0-installation?preserve-view=true&view=vs-2019) sady Visual Studio zahrnout **desktopový vývoj s** využitím úlohy C++.
* [Git](https://git-scm.com/download/).
* [Cmake](https://cmake.org/download/).

## <a name="download-the-code"></a>Stáhněte si kód

V tomto kurzu připravíte vývojové prostředí, které můžete použít k klonování a sestavení sady Azure IoT Hub zařízení C SDK.

Otevřete příkazový řádek v adresáři dle vašeho výběru. Spusťte následující příkaz, který naklonuje úložiště GitHub sady [Azure IoT C a knihovny](https://github.com/Azure/azure-iot-sdk-c) do tohoto umístění:

```cmd\bash
git clone https://github.com/Azure/azure-iot-sdk-c.git
cd azure-iot-sdk-c
git submodule update --init
```

Očekává se, že dokončení této operace trvá několik minut.

## <a name="review-the-code"></a>Kontrola kódu

V kopii Microsoft Azure IoT SDK pro jazyk C, kterou jste dříve stáhli, otevřete soubory *Azure-IoT-SDK-C/iothub_client/samples/pnp/pnp_temperature_controller/pnp_temperature_controller.* c a *Azure-IoT-SDK-C/iothub_client/samples/pnp/pnp_temperature_controller/pnp_thermostat_component. c* v textovém editoru.

Když spustíte ukázku pro připojení k IoT Central, používá službu Device Provisioning (DPS) k registraci zařízení a vygenerování připojovacího řetězce. Ukázka načte informace o připojení DPS, které potřebuje z prostředí příkazového řádku.

V *pnp_temperature_controller. c* `main` funkce nejprve volá `CreateDeviceClientAndAllocateComponents` :

* Nastavte `dtmi:com:example:Thermostat;1` ID modelu. IoT Central používá ID modelu k identifikaci nebo generování šablony zařízení pro toto zařízení. Další informace najdete v tématu [přidružení zařízení k šabloně zařízení](../articles/iot-central/core/concepts-get-connected.md#associate-a-device-with-a-device-template).
* K zřízení a registraci zařízení použijte DPS.
* Vytvořte popisovač klienta zařízení a připojte se k vaší IoT Central aplikaci.
* Vytvoří obslužnou rutinu pro příkazy v komponentě řadiče teploty.
* Vytvoří obslužnou rutinu pro aktualizace vlastností v součásti řadiče teploty.
* Vytvoří dvě komponenty termostatu.

`main`Funkce Next:

* Oznamuje některé hodnoty počátečních vlastností pro všechny komponenty.
* Spustí smyčku pro odeslání telemetrie ze všech součástí.

`main`Funkce potom spustí vlákno pro pravidelné zasílání telemetrie.

```c
int main(void)
{
  IOTHUB_DEVICE_CLIENT_LL_HANDLE deviceClient = NULL;

  if ((deviceClient = CreateDeviceClientAndAllocateComponents()) == NULL)
  {
    LogError("Failure creating IotHub device client");
  }
  else
  {
    LogInfo("Successfully created device client.  Hit Control-C to exit program\n");

    int numberOfIterations = 0;

    // During startup, send the non-"writeable" properties.
    PnP_TempControlComponent_ReportSerialNumber_Property(deviceClient);
    PnP_DeviceInfoComponent_Report_All_Properties(g_deviceInfoComponentName, deviceClient);
    PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle1, deviceClient);
    PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(g_thermostatHandle2, deviceClient);

    while (true)
    {
      // Wake up periodically to poll.  Even if we do not plan on sending telemetry, we still need to poll periodically in order to process
      // incoming requests from the server and to do connection keep alives.
      if ((numberOfIterations % g_sendTelemetryPollInterval) == 0)
      {
          PnP_TempControlComponent_SendWorkingSet(deviceClient);
          PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle1, deviceClient);
          PnP_ThermostatComponent_SendTelemetry(g_thermostatHandle2, deviceClient);
      }

      IoTHubDeviceClient_LL_DoWork(deviceClient);
      ThreadAPI_Sleep(g_sleepBetweenPollsMs);
      numberOfIterations++;
    }

    // Free the memory allocated to track simulated thermostat.
    PnP_ThermostatComponent_Destroy(g_thermostatHandle2);
    PnP_ThermostatComponent_Destroy(g_thermostatHandle1);

    // Clean up the iothub sdk handle
    IoTHubDeviceClient_LL_Destroy(deviceClient);
    // Free all the sdk subsystem
    IoTHub_Deinit();
  }

  return 0;
}
```

`pnp_thermostat_component.c`Funkce v nástroji `PnP_ThermostatComponent_SendTelemetry` ukazuje, jak zařízení odesílá telemetrii teploty ze součásti do IoT Central:

```c
void PnP_ThermostatComponent_SendTelemetry(PNP_THERMOSTAT_COMPONENT_HANDLE pnpThermostatComponentHandle, IOTHUB_DEVICE_CLIENT_LL_HANDLE deviceClientLL)
{
  PNP_THERMOSTAT_COMPONENT* pnpThermostatComponent = (PNP_THERMOSTAT_COMPONENT*)pnpThermostatComponentHandle;
  IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
  IOTHUB_CLIENT_RESULT iothubResult;

  char temperatureStringBuffer[32];

  if (snprintf(temperatureStringBuffer, sizeof(temperatureStringBuffer), g_temperatureTelemetryBodyFormat, pnpThermostatComponent->currentTemperature) < 0)
  {
    LogError("snprintf of current temperature telemetry failed");
  }
  else if ((messageHandle = PnP_CreateTelemetryMessageHandle(pnpThermostatComponent->componentName, temperatureStringBuffer)) == NULL)
  {
    LogError("Unable to create telemetry message");
  }
  else if ((iothubResult = IoTHubDeviceClient_LL_SendEventAsync(deviceClientLL, messageHandle, NULL, NULL)) != IOTHUB_CLIENT_OK)
  {
    LogError("Unable to send telemetry message, error=%d", iothubResult);
  }

  IoTHubMessage_Destroy(messageHandle);
}
```

V nástroji `pnp_thermostat_component.c` `PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property` pošle funkce `maxTempSinceLastReboot` aktualizaci vlastnosti ze součásti do IoT Central:

```c
void PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(PNP_THERMOSTAT_COMPONENT_HANDLE pnpThermostatComponentHandle, IOTHUB_DEVICE_CLIENT_LL_HANDLE deviceClientLL)
{
  PNP_THERMOSTAT_COMPONENT* pnpThermostatComponent = (PNP_THERMOSTAT_COMPONENT*)pnpThermostatComponentHandle;
  char maximumTemperatureAsString[32];
  IOTHUB_CLIENT_RESULT iothubClientResult;
  STRING_HANDLE jsonToSend = NULL;

  if (snprintf(maximumTemperatureAsString, sizeof(maximumTemperatureAsString), g_maxTempSinceLastRebootPropertyFormat, pnpThermostatComponent->maxTemperature) < 0)
  {
    LogError("Unable to create max temp since last reboot string for reporting result");
  }
  else if ((jsonToSend = PnP_CreateReportedProperty(pnpThermostatComponent->componentName, g_maxTempSinceLastRebootPropertyName, maximumTemperatureAsString)) == NULL)
  {
    LogError("Unable to build max temp since last reboot property");
  }
  else
  {
    const char* jsonToSendStr = STRING_c_str(jsonToSend);
    size_t jsonToSendStrLen = strlen(jsonToSendStr);

    if ((iothubClientResult = IoTHubDeviceClient_LL_SendReportedState(deviceClientLL, (const unsigned char*)jsonToSendStr, jsonToSendStrLen, NULL, NULL)) != IOTHUB_CLIENT_OK)
    {
      LogError("Unable to send reported state, error=%d", iothubClientResult);
    }
    else
    {
      LogInfo("Sending maximumTemperatureSinceLastReboot property to IoTHub for component=%s", pnpThermostatComponent->componentName);
    }
  }

  STRING_delete(jsonToSend);
}
```

V nástroji `pnp_thermostat_component.c` `PnP_ThermostatComponent_ProcessPropertyUpdate` zpracovává funkce zapisovatelné aktualizace vlastností z IoT Central:

```c
void PnP_ThermostatComponent_ProcessPropertyUpdate(PNP_THERMOSTAT_COMPONENT_HANDLE pnpThermostatComponentHandle, IOTHUB_DEVICE_CLIENT_LL_HANDLE deviceClientLL, const char* propertyName, JSON_Value* propertyValue, int version)
{
  PNP_THERMOSTAT_COMPONENT* pnpThermostatComponent = (PNP_THERMOSTAT_COMPONENT*)pnpThermostatComponentHandle;

  if (strcmp(propertyName, g_targetTemperaturePropertyName) != 0)
  {
    LogError("Property=%s was requested to be changed but is not part of the thermostat interface definition", propertyName);
  }
  else if (json_value_get_type(propertyValue) != JSONNumber)
  {
    LogError("JSON field %s is not a number", g_targetTemperaturePropertyName);
  }
  else
  {
    double targetTemperature = json_value_get_number(propertyValue);

    LogInfo("Received targetTemperature=%f for component=%s", targetTemperature, pnpThermostatComponent->componentName);
    
    bool maxTempUpdated = false;
    UpdateTemperatureAndStatistics(pnpThermostatComponent, targetTemperature, &maxTempUpdated);

    // The device needs to let the service know that it has received the targetTemperature desired property.
    SendTargetTemperatureResponse(pnpThermostatComponent, deviceClientLL, version);
    
    if (maxTempUpdated)
    {
      // If the Maximum temperature has been updated, we also report this as a property.
        PnP_TempControlComponent_Report_MaxTempSinceLastReboot_Property(pnpThermostatComponent, deviceClientLL);
    }
  }
}
```

V nástroji `pnp_thermostat_component.c` `PnP_ThermostatComponent_ProcessCommand` funkce zpracovává příkazy volané z IoT Central:

```c
int PnP_ThermostatComponent_ProcessCommand(PNP_THERMOSTAT_COMPONENT_HANDLE pnpThermostatComponentHandle, const char *pnpCommandName, JSON_Value* commandJsonValue, unsigned char** response, size_t* responseSize)
{
  PNP_THERMOSTAT_COMPONENT* pnpThermostatComponent = (PNP_THERMOSTAT_COMPONENT*)pnpThermostatComponentHandle;
  const char* sinceStr;
  int result;

  if (strcmp(pnpCommandName, g_getMaxMinReport) != 0)
  {
    LogError("PnP command=%s is not supported on thermostat component", pnpCommandName);
    result = PNP_STATUS_NOT_FOUND;
  }
  // See caveats section in ../readme.md; we don't actually respect this sinceStr to keep the sample simple,
  // but want to demonstrate how to parse out in any case.
  else if ((sinceStr = json_value_get_string(commandJsonValue)) == NULL)
  {
    LogError("Cannot retrieve JSON string for command");
    result = PNP_STATUS_BAD_FORMAT;
  }
  else if (BuildMaxMinCommandResponse(pnpThermostatComponent, response, responseSize) == false)
  {
    LogError("Unable to build response for component=%s", pnpThermostatComponent->componentName);
    result = PNP_STATUS_INTERNAL_ERROR;
  }
  else
  {
    LogInfo("Returning success from command request for component=%s", pnpThermostatComponent->componentName);
    result = PNP_STATUS_SUCCESS;
  }

  return result;
}
```

## <a name="build-the-code"></a>Sestavení kódu

Pomocí sady SDK pro zařízení sestavíte zahrnutý vzorový kód:

1. Vytvořte podadresář _cmake_ v kořenové složce sady SDK pro zařízení a přejděte do této složky:

    ```cmd\bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Spusťte následující příkazy a Sestavte sadu SDK a ukázky:

    ```cmd\bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key=ON -Drun_e2e_tests=OFF ..
    cmake --build .
    ```

## <a name="get-connection-information"></a>Získání informací o připojení

[!INCLUDE [iot-central-connection-configuration](iot-central-connection-configuration.md)]

## <a name="run-the-code"></a>Spuštění kódu

Pokud chcete spustit ukázkovou aplikaci, otevřete prostředí příkazového řádku a přejděte do složky *Azure-IoT-SDK-c\cmake*.

[!INCLUDE [iot-central-connection-environment](iot-central-connection-environment.md)]

Spuštění ukázky:

```bash
# Bash
cd iothub_client/samples/pnp/pnp_temperature_controller/
./pnp_temperature_controller
```

```cmd
REM Windows
cd iothub_client\samples\pnp\pnp_temperature_controller\Debug
.\pnp_temperature_controller.exe
```

Následující výstup zobrazuje registraci zařízení a připojení k IoT Central. Ukázka spustí odesílání telemetrie:

```output
Info: Initiating DPS client to retrieve IoT Hub connection information
-> 09:43:27 CONNECT | VER: 4 | KEEPALIVE: 0 | FLAGS: 194 | USERNAME: 0ne0026656D/registrations/sample-device-01/api-version=2019-03-31&ClientVersion=1.6.0 | PWD: XXXX | CLEAN: 1
<- 09:43:28 CONNACK | SESSION_PRESENT: false | RETURN_CODE: 0x0
-> 09:43:29 SUBSCRIBE | PACKET_ID: 1 | TOPIC_NAME: $dps/registrations/res/# | QOS: 1
<- 09:43:30 SUBACK | PACKET_ID: 1 | RETURN_CODE: 1
-> 09:43:30 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_MOST_ONCE | TOPIC_NAME: $dps/registrations/PUT/iotdps-register/?$rid=1 | PAYLOAD_LEN: 102
<- 09:43:31 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_LEAST_ONCE | TOPIC_NAME: $dps/registrations/res/202/?$rid=1&retry-after=3 | PACKET_ID: 2 | PAYLOAD_LEN: 94
-> 09:43:31 PUBACK | PACKET_ID: 2
-> 09:43:33 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_MOST_ONCE | TOPIC_NAME: $dps/registrations/GET/iotdps-get-operationstatus/?$rid=2&operationId=4.2f792ade0a5c3e68.baf0e879-d88a-4153-afef-71aff51fd847 | PAYLOAD_LEN: 102
<- 09:43:34 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_LEAST_ONCE | TOPIC_NAME: $dps/registrations/res/202/?$rid=2&retry-after=3 | PACKET_ID: 2 | PAYLOAD_LEN: 173
-> 09:43:34 PUBACK | PACKET_ID: 2
-> 09:43:36 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_MOST_ONCE | TOPIC_NAME: $dps/registrations/GET/iotdps-get-operationstatus/?$rid=3&operationId=4.2f792ade0a5c3e68.baf0e879-d88a-4153-afef-71aff51fd847 | PAYLOAD_LEN: 102
<- 09:43:37 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_LEAST_ONCE | TOPIC_NAME: $dps/registrations/res/200/?$rid=3 | PACKET_ID: 2 | PAYLOAD_LEN: 478
-> 09:43:37 PUBACK | PACKET_ID: 2
Info: Provisioning callback indicates success.  iothubUri=iotc-60a....azure-devices.net, deviceId=sample-device-01
-> 09:43:37 DISCONNECT
Info: DPS successfully registered.  Continuing on to creation of IoTHub device client handle.
Info: Successfully created device client.  Hit Control-C to exit program

Info: Sending serialNumber property to IoTHub
Info: Sending device information property to IoTHub.  propertyName=swVersion, propertyValue="1.0.0.0"
Info: Sending device information property to IoTHub.  propertyName=manufacturer, propertyValue="Sample-Manufacturer"
Info: Sending device information property to IoTHub.  propertyName=model, propertyValue="sample-Model-123"
Info: Sending device information property to IoTHub.  propertyName=osName, propertyValue="sample-OperatingSystem-name"
Info: Sending device information property to IoTHub.  propertyName=processorArchitecture, propertyValue="Contoso-Arch-64bit"
Info: Sending device information property to IoTHub.  propertyName=processorManufacturer, propertyValue="Processor Manufacturer(TM)"
Info: Sending device information property to IoTHub.  propertyName=totalStorage, propertyValue=10000
Info: Sending device information property to IoTHub.  propertyName=totalMemory, propertyValue=200
Info: Sending maximumTemperatureSinceLastReboot property to IoTHub for component=thermostat1
Info: Sending maximumTemperatureSinceLastReboot property to IoTHub for component=thermostat2
-> 09:43:44 CONNECT | VER: 4 | KEEPALIVE: 240 | FLAGS: 192 | USERNAME: iotc-60a576a2-eec7-48e2-9306-9e7089a79995.azure-devices.net/sample-device-01/?api-version=2020-09-30&DeviceClientType=iothubclient%2f1.6.0%20(native%3b%20Linux%3b%20x86_64)&model-id=dtmi%3acom%3aexample%3aTemperatureController%3b1 | PWD: XXXX | CLEAN: 0
<- 09:43:44 CONNACK | SESSION_PRESENT: false | RETURN_CODE: 0x0
-> 09:43:44 SUBSCRIBE | PACKET_ID: 2 | TOPIC_NAME: $iothub/twin/res/# | QOS: 0 | TOPIC_NAME: $iothub/methods/POST/# | QOS: 0
-> 09:43:44 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_LEAST_ONCE | TOPIC_NAME: devices/sample-device-01/messages/events/ | PACKET_ID: 3 | PAYLOAD_LEN: 19
-> 09:43:44 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_LEAST_ONCE | TOPIC_NAME: devices/sample-device-01/messages/events/%24.sub=thermostat1 | PACKET_ID: 4 | PAYLOAD_LEN: 21
-> 09:43:44 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_LEAST_ONCE | TOPIC_NAME: devices/sample-device-01/messages/events/%24.sub=thermostat2 | PACKET_ID: 5 | PAYLOAD_LEN: 21
```

[!INCLUDE [iot-central-monitor-thermostat](iot-central-monitor-thermostat.md)]

Můžete zjistit, jak zařízení reaguje na příkazy a aktualizace vlastností:

```output
<- 09:49:03 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_MOST_ONCE | TOPIC_NAME: $iothub/methods/POST/thermostat1*getMaxMinReport/?$rid=1 | PAYLOAD_LEN: 26
Info: Received PnP command for component=thermostat1, command=getMaxMinReport
Info: Returning success from command request for component=thermostat1
-> 09:49:03 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_MOST_ONCE | TOPIC_NAME: $iothub/methods/res/200/?$rid=1 | PAYLOAD_LEN: 117

...

<- 09:50:04 PUBLISH | IS_DUP: false | RETAIN: 0 | QOS: DELIVER_AT_MOST_ONCE | TOPIC_NAME: $iothub/twin/PATCH/properties/desired/?$version=2 | PAYLOAD_LEN: 63
Info: Received targetTemperature=67.000000 for component=thermostat2
Info: Sending acknowledgement of property to IoTHub for component=thermostat2
```
