---
title: Pochopení komponent v modelech IoT technologie Plug and Play | Microsoft Docs
description: Pochopení rozdílu mezi modely IoT technologie Plug and Play DTDL, které používají komponenty a modely, které nepoužívají komponenty.
author: ericmitt
ms.author: ericmitt
ms.date: 07/07/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 7d33811dc056baa3d205d7d86c1bdd7bff74ae54
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92329998"
---
# <a name="iot-plug-and-play-components-in-models"></a>Komponenty technologie IoT Plug and Play v modelech

V konvencích technologie IoT Plug and Play je zařízení zařízením IoT Plug and Play, pokud při připojení k centru IoT Hub reprezentuje své ID modelu jazyka DTDL (Digital Twin Definition Language).

Následující fragment kódu ukazuje několik příkladů ID modelu:

```json
 "@id": "dtmi:com:example:TemperatureController;1"
 "@id": "dtmi:com:example:Thermostat;1",
```

## <a name="no-components"></a>Žádné součásti

Jednoduchý model nepoužívá vložené nebo kaskádové komponenty. Obsahuje informace záhlaví a obsah k definování telemetrie, vlastností a příkazů.

Následující příklad ukazuje část jednoduchého modelu, který nepoužívá komponenty:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
...
```

I když model explicitně nedefinuje komponentu, chová se jako v případě, že existuje jediná _výchozí komponenta_se všemi definicemi telemetrie, vlastností a příkazů.

Následující snímek obrazovky ukazuje, jak se model zobrazuje v nástroji Azure IoT Explorer:

:::image type="content" source="media/concepts-components/default-component.png" alt-text="Výchozí součást v Azure IoT Exploreru":::

ID modelu je uloženo ve vlastnosti vlákna zařízení, jak ukazuje následující snímek obrazovky:

:::image type="content" source="media/concepts-components/twin-model-id.png" alt-text="Výchozí součást v Azure IoT Exploreru":::

Model DTDL bez komponent je užitečné zjednodušení pro zařízení nebo IoT Edge modul s jedinou sadou telemetrie, vlastností a příkazů. Model, který nepoužívá komponenty, usnadňuje migraci stávajícího zařízení nebo modulu technologie Plug and Play zařízení nebo modul IoT – vytvoříte model DTDL, který popisuje vaše skutečné zařízení nebo modul, aniž by bylo potřeba definovat nějaké součásti.

> [!TIP]
> Modulem může být [modul zařízení nebo modul](../iot-hub/iot-hub-devguide-module-twins.md) [IoT Edge](../iot-edge/about-iot-edge.md).

## <a name="multiple-components"></a>Více komponent

Komponenty umožňují vytvořit model rozhraní jako sestavení jiných rozhraní.

Například rozhraní [termostatu](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) je definováno jako model. Toto rozhraní můžete začlenit jako jednu nebo více součástí při definování [modelu teplotního adaptéru](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json). V následujícím příkladu jsou tyto komponenty volány `thermostat1` a `thermostat2` .

Pro model DTDL s více komponentami je k dispozici nejméně dva oddíly součásti. Každý oddíl má `@type` nastavenou hodnotu `Component` a explicitně odkazuje na schéma, jak je znázorněno v následujícím fragmentu kódu:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "description": "Device with two thermostats and remote reboot.",
  "contents": [
...
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1",
      "displayName": "Thermostat One",
      "description": "Thermostat One of Two."
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat2",
      "displayName": "Thermostat Two",
      "description": "Thermostat Two of Two."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
...
```

Tento model má tři komponenty, které jsou definovány v části Contents – dvě `Thermostat` komponenty a `DeviceInformation` komponenta. K dispozici je také výchozí komponenta.

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s komponentami modelu, tady je několik dalších prostředků:

- [Instalace a použití nástrojů pro tvorbu DTDL](howto-use-dtdl-authoring-tools.md)
- [Digital neDTDLs – definice – jazyk v2 ()](https://github.com/Azure/opendigitaltwins-dtdl)
- [Úložiště modelů](./concepts-model-repository.md)
