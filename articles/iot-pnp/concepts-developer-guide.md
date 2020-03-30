---
title: Průvodce pro vývojáře – IoT Plug and Play Preview | Dokumenty společnosti Microsoft
description: Popis modelování zařízení pro vývojáře IoT Plug and Play
author: dominicbetts
ms.author: dobett
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5fda51e6d2f62b9cbef0fcac22d5bb2ea0df905b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605217"
---
# <a name="iot-plug-and-play-preview-modeling-developer-guide"></a>Průvodce vývojářem pro modelování náhledu IoT Plug and Play

IoT Plug and Play Preview umožňuje vytvářet zařízení, která inzerují jejich možnosti pro aplikace Azure IoT. Zařízení IoT Plug and Play nevyžadují ruční konfiguraci, když je zákazník připojí k aplikacím podporujícím technologii IoT Plug a Play. IoT Central je příkladem aplikace s podporou IoT Plug and Play.

Chcete-li vytvořit zařízení IoT Plug and Play, musíte vytvořit popis zařízení. Popis se provádí pomocí jednoduchého definičního jazyka s názvem Digital Twins Definition Language (DTDL).

## <a name="device-capability-model"></a>Model schopností zařízení

S DTDL vytvoříte _model schopností zařízení_ k popisu částí zařízení. Typické zařízení IoT se skládá z:

- Vlastní díly, což jsou věci, které činí vaše zařízení jedinečným.
- Standardní díly, což jsou věci, které jsou společné pro všechna zařízení.

Tyto části se nazývají _rozhraní_ v modelu schopností zařízení. Rozhraní definují podrobnosti o každé části, kterou vaše zařízení implementuje.

Následující příklad ukazuje model schopností zařízení pro zařízení termostatu:

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
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Model schopností má některá povinná pole:

- `@id`: jedinečné ID ve formě jednoduchého jednotného názvu prostředku.
- `@type`: deklaruje, že tento objekt je model schopností.
- `@context`: Určuje verzi DTDL použitou pro model schopností.
- `implements`: uvádí rozhraní, která vaše zařízení implementuje.

Každá položka v seznamu rozhraní v sekci implements má:

- `name`: název programování rozhraní.
- `schema`: rozhraní, které model schopností implementuje.

Existují další volitelná pole, která můžete použít k přidání dalších podrobností do modelu schopností, například zobrazovaný název a popis. Rozhraní, které jsou deklarovány v rámci modelu schopností lze považovat za součásti zařízení. Pro verzi Public Preview může mít seznam rozhraní pouze jednu položku na schéma.

## <a name="interface"></a>Rozhraní

S DTDL, můžete popsat možnosti vašeho zařízení pomocí rozhraní. Rozhraní popisují _vlastnosti_, _telemetrii_a _příkazy, které_ část zařízení implementuje:

- `Properties`. Vlastnosti jsou datová pole, která představují stav vašeho zařízení. Vlastnosti slouží k reprezentaci trvalého stavu zařízení, například stavu zapnutí a vypnutí čerpadla chladicí kapaliny. Vlastnosti mohou také představovat základní vlastnosti zařízení, například verzi firmwaru zařízení. Vlastnosti můžete deklarovat jako jen pro čtení nebo zapisovatelné.
- `Telemetry`. Telemetrická pole představují měření ze senzorů. Kdykoli zařízení provede měření senzoru, mělo by odeslat telemetrickou událost obsahující data senzoru.
- `Commands`. Příkazy představují metody, které mohou uživatelé vašeho zařízení v zařízení provádět. Například příkaz reset nebo příkaz pro zapnutí nebo vypnutí ventilátoru.

Následující příklad ukazuje rozhraní pro termostatzařízení:

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
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Rozhraní má některá povinná pole:

- `@id`: jedinečné ID ve formě jednoduchého jednotného názvu prostředku.
- `@type`: deklaruje, že tento objekt je rozhraní.
- `@context`: určuje verzi DTDL použitou pro rozhraní.
- `contents`: uvádí vlastnosti, telemetrii a příkazy, které tvoří vaše zařízení.

V tomto jednoduchém příkladu existuje pouze jedno pole telemetrie. Minimální popis pole má:

- `@type`: určuje typ `Telemetry`schopnosti: `Property`, `Command`, nebo .
- `name`: poskytuje název hodnoty telemetrie.
- `schema`: určuje datový typ pro telemetrii. Tato hodnota může být primitivní typ, například dvojité, celé číslo, logická hodnota nebo řetězec. Podporovány jsou také komplexní typy objektů, pole a mapy.

Další volitelná pole, například zobrazovaný název a popis, umožňují přidat další podrobnosti do rozhraní a možností.

### <a name="properties"></a>Vlastnosti

Ve výchozím nastavení jsou vlastnosti jen pro čtení. Vlastnosti jen pro čtení znamenají, že zařízení hlásí aktualizace hodnoty vlastnosti do centra IoT. Vaše centrum IoT nemůže nastavit hodnotu vlastnosti jen pro čtení.

Můžete také označit vlastnost jako zapisovatelné na rozhraní. Zařízení může přijímat aktualizaci zapisovatelné vlastnosti z vašeho centra IoT hub, stejně jako vykazování aktualizace hodnoty vlastností do vašeho centra.

Zařízení nemusí být připojena k nastavení hodnot vlastností. Aktualizované hodnoty jsou přeneseny, když se zařízení dále připojí k rozbočovači. Toto chování platí pro vlastnosti jen pro čtení a zápis.

Nepoužívejte vlastnosti k odesílání telemetrie ze zařízení. Například vlastnost jen pro `temperatureSetting=80` čtení, jako je například by měla znamenat, že teplota zařízení byla nastavena na 80 a zařízení se snaží získat nebo zůstat na této teplotě.

Pro zapisovatelné vlastnosti aplikace zařízení vrátí požadovaný stavový kód, verzi a popis, který označuje, zda přijala a použila hodnotu vlastnosti.

### <a name="telemetry"></a>Telemetrie

Ve výchozím nastavení služba IoT Hub směruje všechny telemetrické zprávy ze zařízení do [integrovaného koncového bodu směřujícího ke službě **(zprávy nebo události),**](../iot-hub/iot-hub-devguide-messages-read-builtin.md) který je kompatibilní s [event huby](https://azure.microsoft.com/documentation/services/event-hubs/).

[Vlastní koncové body a pravidla směrování služby IoT Hub](../iot-hub/iot-hub-devguide-messages-d2c.md) můžete použít k odesílání telemetrie do jiných cílů, jako je úložiště objektů blob nebo jiná centra událostí. Pravidla směrování používají vlastnosti zprávy k výběru zpráv.

### <a name="commands"></a>Příkazy

Příkazy jsou synchronní nebo asynchronní. Synchronní příkaz musí být ve výchozím nastavení proveden do 30 sekund a zařízení musí být připojeno při doručení příkazu. Pokud zařízení reaguje včas nebo zařízení není připojeno, příkaz se nezdaří.

Pro dlouhotrvající operace používejte asynchronní příkazy. Zařízení odesílá informace o průběhu pomocí telemetrických zpráv. Tyto zprávy o průběhu mají následující vlastnosti záhlaví:

- `iothub-command-name`: název příkazu, `UpdateFirmware`například .
- `iothub-command-request-id`: ID požadavku generované na straně serveru a odeslané do zařízení v počátečním volání.
- `iothub-interface-id`: ID rozhraní, na které je tento `urn:example:AssetTracker:1`příkaz definován, například .
 `iothub-interface-name`: název instance tohoto rozhraní, `myAssetTracker`například .
- `iothub-command-statuscode`: stavový kód vrácený ze `202`zařízení, například .

## <a name="register-a-device"></a>Registrování zařízení

IoT Plug and Play usnadňuje inzerci možností vašeho zařízení. S IoT Plug and Play, po připojení zařízení k IoT Hub, musíte zaregistrovat model funkce zařízení. Registrace umožňuje zákazníkům používat funkce IoT Plug and Play vašeho zařízení.

Tato příručka ukazuje, jak zaregistrovat zařízení pomocí sady Azure IoT Device SDK pro C.

Pro každé rozhraní, které vaše zařízení implementuje, je nutné vytvořit rozhraní a připojit jej k jeho implementaci.

Pro rozhraní termostatu zobrazené dříve pomocí sady C SDK vytvoříte a připojíte rozhraní termostatu k jeho implementaci:

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

Tento kód opakujte pro každé rozhraní, které zařízení implementuje.

Po vytvoření rozhraní zaregistrujte model schopností zařízení a rozhraní s centrem IoT:

```c
DIGITALTWIN_INTERFACE_CLIENT_HANDLE interfaces[2];
interfaces[0] = thermostatInterfaceHandle;
interfaces[1] = deviceInfoInterfaceHandle;

result = DigitalTwin_DeviceClient_RegisterInterfacesAsync(
    digitalTwinClientHandle, // The handle for the connection to Azure IoT
    "urn:example:Thermostat_T_1000:1",
    interfaces, 2,
    null, null);
```

## <a name="use-a-device"></a>Použití zařízení

IoT Plug and Play umožňuje používat zařízení, která zaregistrovala své možnosti ve vašem centru IoT Hub. Můžete například přistupovat k vlastnostem a příkazům zařízení přímo.

Pokud chcete použít zařízení IoT Plug and Play, které je připojené k vašemu centru IoT hub, použijte rozhraní REST API služby IoT Hub nebo některé z sad SDK v jazyce IoT. Následující příklady používají rozhraní REST rozhraní IoT Hub. Aktuální verze rozhraní API `2019-07-01-preview`je . Připojte `?api-version=2019-07-01-preview` k volání REST PI.

Chcete-li získat hodnotu vlastnosti zařízení,`fwVersion`jako je `DeviceInformation` například verze firmwaru ( ) v rozhraní v termostatu, použijte digitální dvojčata REST API.

Pokud se vaše termostatové zařízení nazývá `t-123`, získáte všechny vlastnosti na všech rozhraních implementovaných zařízením s voláním GET rozhraní REST API:

```REST
GET /digitalTwins/t-123/interfaces
```

Obecněji řečeno, všechny vlastnosti na všech rozhraních `{device-id}` jsou přístupné pomocí této šablony rozhraní REST API, kde je identifikátor pro zařízení:

```REST
GET /digitalTwins/{device-id}/interfaces
```

Pokud znáte název rozhraní, například `deviceInformation`, a chcete získat vlastnosti pro toto konkrétní rozhraní, rozsah požadavku na konkrétní rozhraní podle názvu:

```REST
GET /digitalTwins/t-123/interfaces/deviceInformation
```

Obecněji lze přistupovat k vlastnostem pro konkrétní rozhraní `device-id` prostřednictvím této šablony `{interface-name}` rozhraní REST API, kde je identifikátor pro zařízení a je název rozhraní:

```REST
GET /digitalTwins/{device-id}/interfaces/{interface-name}
```

Příkazy zařízení IoT Plug and Play můžete volat přímo. Pokud `Thermostat` rozhraní v `t-123` zařízení `restart` obsahuje příkaz, můžete jej volat pomocí volání REST API POST:

```REST
POST /digitalTwins/t-123/interfaces/thermostat/commands/restart
```

Obecněji lze příkazy volat prostřednictvím této šablony rozhraní REST API:

- `device-id`: identifikátor zařízení.
- `interface-name`: název rozhraní z oddílu implements v modelu schopností zařízení.
- `command-name`: název příkazu.

```REST
/digitalTwins/{device-id}/interfaces/{interface-name}/commands/{command-name}
```

## <a name="next-steps"></a>Další kroky

Teď, když jste se dozvěděli o modelování zařízení, zde jsou některé další zdroje:

- [Definiční jazyk digitálního dvojčete (DTDL)](https://aka.ms/DTDL)
- [Sady SDK pro zařízení jazyka C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
