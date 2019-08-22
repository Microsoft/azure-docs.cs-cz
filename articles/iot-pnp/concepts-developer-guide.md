---
title: Příručka pro vývojáře – IoT technologie Plug and Play Preview | Microsoft Docs
description: Popis modelování zařízení pro vývojáře IoT technologie Plug and Play
author: dominicbetts
ms.author: dobett
ms.date: 07/05/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 6d5247454fe65e5539a2401330192f1db9a65114
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880563"
---
# <a name="iot-plug-and-play-preview-modeling-developer-guide"></a>Příručka pro vývojáře pro modelování IoT technologie Plug and Play Preview

IoT technologie Plug and Play Preview umožňuje sestavovat zařízení, která budou inzerovat své možnosti aplikacím Azure IoT. Zařízení IoT technologie Plug and Play nevyžadují ruční konfiguraci, když ji zákazník připojí k aplikacím s podporou IoT technologie Plug and Play. IoT Central je příklad aplikace s podporou technologie Plug and Play IoT.

Chcete-li vytvořit zařízení IoT technologie Plug and Play, je nutné vytvořit popis zařízení. Popis se provádí pomocí jednoduchého definičního jazyka s názvem Digital DTDL (digitální vlákna).

## <a name="device-capability-model"></a>Model schopností zařízení

Pomocí DTDL vytvoříte _model schopností zařízení_ , který popisuje části zařízení. Typické zařízení IoT se skládá z těchto součástí:

- Vlastní části, které tvoří jedinečné zařízení.
- Standardní části, které jsou společné pro všechna zařízení.

Tyto části se nazývají _rozhraní_ v modelu schopností zařízení. Rozhraní definují podrobnosti jednotlivých částí, které vaše zařízení implementuje.

Následující příklad ukazuje model schopností zařízení pro termostatické zařízení:

```json
{
  "@id": "urn:example:Thermostat_T_1000:1",
  "@type": "CapabilityModel",
  "implements": [
    {
      "name": "thermostat",
      "schema": "urn:example:Thermostat:1"
    },
    {
      "name": "urn_azureiot_deviceManagement_DeviceInformation",
      "schema": "urn:azureiot:deviceManagement:DeviceInformation:1"
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/CapabilityModel.json"
}
```

Model schopností má některá povinná pole:

- `@id`: jedinečné ID ve formě jednoduchého uniformního názvu prostředku.
- `@type`: deklaruje, že tento objekt je model schopností.
- `@context`: Určuje verzi DTDL, která se používá pro model schopností.
- `implements`: zobrazí seznam rozhraní, které vaše zařízení implementuje.

Každá položka v seznamu rozhraní v oddílu Implements má:

- `name`: název programování rozhraní.
- `schema`: rozhraní, které model schopností implementuje.

K dispozici jsou další volitelná pole, která můžete použít k přidání dalších podrobností do modelu schopností, jako je například zobrazovaný název a popis. Rozhraní, která jsou deklarována v modelu schopností, lze představit jako komponenty zařízení. V případě verze Public Preview může mít seznam rozhraní pouze jednu položku pro každé schéma.

## <a name="interface"></a>Rozhraní

Pomocí DTDL popíšete možnosti svého zařízení pomocí rozhraní. Rozhraní popisují _vlastnosti_, _telemetrie_a _příkazy_ , které součást vašeho zařízení implementuje:

- `Properties`. Vlastnosti jsou datová pole, která představují stav vašeho zařízení. Použijte vlastnosti, které reprezentují trvalý stav zařízení, jako je například stav Zapnuto pro čerpadlo chladicí kapaliny. Vlastnosti mohou také představovat základní vlastnosti zařízení, například verzi firmwaru zařízení. Vlastnosti můžete deklarovat jako jen pro čtení nebo zapisovatelné.
- `Telemetry`. Pole telemetrie reprezentují měření od senzorů. Pokaždé, když zařízení získá měření senzorů, mělo by se odeslat událost telemetrie obsahující data ze senzorů.
- `Commands`. Příkazy reprezentují metody, které můžou uživatelé zařízení na zařízení spouštět. Například příkaz pro obnovení nebo příkaz pro zapnutí nebo vypnutí ventilátoru.

Následující příklad ukazuje rozhraní pro zařízení termostata:

```json
{
  "@id": "urn:example:Thermostat:1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Telemetry",
      "name": "temperature",
      "schema": "double"
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/Interface.json"
}
```

Rozhraní má některá povinná pole:

- `@id`: jedinečné ID ve formě jednoduchého uniformního názvu prostředku.
- `@type`: deklaruje, že tento objekt je rozhraní.
- `@context`: Určuje verzi DTDL, která se používá pro rozhraní.
- `contents`: zobrazí seznam vlastností, telemetrie a příkazů, které tvoří vaše zařízení.

V tomto jednoduchém příkladu je k dispozici pouze jedno pole telemetrie. Minimální Popis pole má:

- `@type`: Určuje typ schopnosti: `Telemetry`, `Property`nebo `Command`.
- `name`: poskytuje název hodnoty telemetrie.
- `schema`: Určuje datový typ telemetrie. Tato hodnota může být primitivní typ, například Double, Integer, Boolean nebo String. Podporují se také komplexní typy objektů, pole a mapy.

Další volitelná pole, jako je například zobrazované jméno a popis, umožňují přidat další podrobnosti k rozhraní a funkcím.

### <a name="properties"></a>Vlastnosti

Ve výchozím nastavení jsou vlastnosti jen pro čtení. Vlastnosti jen pro čtení znamenají, že se do služby IoT Hub nahlásí aktualizace hodnoty vlastnosti zařízení. Vaše centrum IoT nemůže nastavit hodnotu vlastnosti jen pro čtení.

Můžete také označit vlastnost jako zapisovatelnou na rozhraní. Zařízení může obdržet aktualizaci zapisovatelné vlastnosti z vašeho centra IoT a také aktualizovat hodnoty vlastností pro vytváření sestav do vašeho centra.

Zařízení nemusí být připojená k nastavením hodnot vlastností. Aktualizované hodnoty se přenesou, když se zařízení příště připojí k rozbočovači. Toto chování platí pro vlastnosti jen pro čtení i pro zápis.

Nepoužívejte vlastnosti k odeslání telemetrie ze zařízení. Například vlastnost jen pro čtení, například `temperatureSetting=80` by měla znamenat, že se teplota zařízení nastavila na 80 a zařízení se snaží získat nebo zůstat v této teplotě.

U zapisovatelných vlastností aplikace zařízení vrátí stavový kód požadovaného stavu, verzi a popis, aby označoval, zda obdržel a používal hodnotu vlastnosti.

### <a name="telemetry"></a>Telemetrie

Ve výchozím nastavení IoT Hub směruje všechny zprávy telemetrie ze zařízení na [integrovaný koncový bod (**zprávy/události**)](../iot-hub/iot-hub-devguide-messages-read-builtin.md) , který je kompatibilní se službou [Event Hubs](https://azure.microsoft.com/documentation/services/event-hubs/).

Můžete použít [vlastní koncové body a pravidla směrování IoT Hub](../iot-hub/iot-hub-devguide-messages-d2c.md) k odeslání telemetrie do jiných cílů, jako je úložiště objektů BLOB nebo jiná centra událostí. Pravidla směrování používají vlastnosti zprávy k výběru zpráv.

### <a name="commands"></a>Příkazy

Příkazy jsou buď synchronní, nebo asynchronní. Synchronní příkaz se musí provést během 30 sekund ve výchozím nastavení a zařízení musí být připojené, až se dorazí na příkaz. Pokud zařízení reaguje v čase nebo pokud zařízení není připojené, příkaz se nezdařil.

Pro dlouhotrvající operace použijte asynchronní příkazy. Zařízení odesílá informace o průběhu pomocí zpráv telemetrie. Tyto zprávy o průběhu mají následující vlastnosti záhlaví:

- `iothub-command-name`: název příkazu, například `UpdateFirmware`.
- `iothub-command-request-id`: ID žádosti vygenerované na straně serveru, které se odešle do zařízení při počátečním volání.
- `iothub-interface-id`:  ID rozhraní, ve kterém je tento příkaz definován, `urn:example:AssetTracker:1`například.
 `iothub-interface-name`: název instance tohoto rozhraní, například `myAssetTracker`.
- `iothub-command-statuscode`: stavový kód vrácený ze zařízení, například `202`.

## <a name="register-a-device"></a>Registrování zařízení

IoT technologie Plug and Play usnadňuje inzerci možností vašeho zařízení. Když technologie Plug and Play IoT se vaše zařízení připojí k IoT Hub, musíte zaregistrovat svůj model schopností zařízení. Registrace umožňuje zákazníkům používat možnosti IoT technologie Plug and Play vašeho zařízení.

V této příručce se dozvíte, jak zaregistrovat zařízení pomocí sady SDK pro zařízení Azure IoT pro jazyk C.

Pro každé rozhraní, které vaše zařízení implementuje, je nutné vytvořit rozhraní a připojit ho ke své implementaci.

Pro rozhraní termostatu zobrazené výše pomocí sady C SDK vytvoříte a připojíte své rozhraní termostata k jeho implementaci:

```c
DIGITALTWIN_INTERFACE_HANDLE thermostatInterfaceHandle;

DIGITALTWIN_CLIENT_RESULT result = DigitalTwin_InterfaceClient_Create(
    "thermostat",
    "urn:example:Thermostat:1",
    null, null,
    &thermostatInterfaceHandle);

result = DigitalTwin_Interface_SetCommandsCallbacks(
    thermostatInterfaceHandle,
    commandsCallbackTable);

result = DigitalTwin_Interface_SetPropertiesUpdatedCallbacks(
    thermostatInterfaceHandle,
    propertiesCallbackTable);

```

Tento kód opakujte pro každé rozhraní, které vaše zařízení implementuje.

Po vytvoření rozhraní zaregistrujte model a rozhraní schopností zařízení ve službě IoT Hub:

```c
DIGITALTWIN_INTERFACE_CLIENT_HANDLE interfaces[2];
interfaces[0] = thermostatInterfaceHandle;
interfaces[1] = deviceInfoInterfaceHandle;

result = DigitalTwin_DeviceClient_RegisterInterfacesAsync(
    digitalTwinClientHandle, // The handle for the connection to Azure IoT
    "urn:example:Thermostat_T_1000:1",
    interfaceHandleList, 2,
    null, null);
```

## <a name="use-a-device"></a>Použití zařízení

IoT technologie Plug and Play umožňuje používat zařízení, která zaregistrovala své možnosti ve službě IoT Hub. Můžete například získat přímý přístup k vlastnostem a příkazům zařízení.

Pokud chcete používat technologie Plug and Play IoT, která je připojená ke službě IoT Hub, použijte buď IoT Hub REST API nebo jednu z jazykových sad IoT. V následujících příkladech se používá REST API IoT Hub.

Chcete-li získat hodnotu vlastnosti zařízení, jako je například firmware verze (`fwVersion`) `DeviceInformation` v rozhraní ve termostatu, použijte digitální vlákna REST API.

Pokud se zavolá `t-123`zařízení termostata, zobrazí se všechny vlastnosti implementované vaším zařízením a volání REST API Get:

```REST
GET /digitalTwins/t-123/interfaces
```

Obecněji jsou k dispozici všechny vlastnosti s touto šablonou REST API `{device-id}` , kde je identifikátor zařízení:

```REST
GET /digitalTwins/{device-id}/interfaces
```

Pokud znáte název rozhraní a chcete získat vlastnosti tohoto konkrétního rozhraní, oborujte požadavek na konkrétní rozhraní podle názvu:

```REST
GET /digitalTwins/t-123/interfaces/info
```

Obecně platí, že vlastnosti pro konkrétní rozhraní jsou dostupné prostřednictvím této šablony REST API, kde `device-id` je identifikátorem zařízení a `{interface-name}` je název rozhraní:

```REST
GET /digitalTwins/{device-id}/interfaces/{interface-name}
```

Můžete volat přímo technologie Plug and Play příkazy pro zařízení IoT. Pokud rozhraní v zařízení obsahuje `restart` příkaz, můžete ho zavolat voláním REST API post: `t-123` `Thermostat`

```REST
POST /digitalTwins/t-123/interfaces/thermostat/commands/restart
```

Obecně lze příkazy volat pomocí této šablony REST API:

- `device-id`: identifikátor zařízení.
- `interface-name`: název rozhraní z oddílu Implements v modelu schopností zařízení.
- `command-name`: název příkazu.

```REST
/digitalTwins/{device-id}/interfaces/{interface-name}/commands/{command-name}
```

## <a name="next-steps"></a>Další postup

Teď, když jste se dozvěděli o modelování zařízení, tady je několik dalších prostředků:

- [DTDL (Digital redefinition Language) – jazyk](https://aka.ms/DTDL)
- [Sady SDK pro zařízení jazyka C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [REST API IoT](https://docs.microsoft.com/rest/api/iothub/device)
