---
title: Konvence technologie Plug and Play IoT | Microsoft Docs
description: Popis konvencí IoT technologie Plug and Play očekává, že se zařízení použijí při odesílání telemetrie a vlastností a zpracování příkazů a aktualizací vlastností.
author: rido-min
ms.author: rmpablos
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 86c6ea9dded423e7bd513faf73adfd293f2bd38f
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2020
ms.locfileid: "96302609"
---
# <a name="iot-plug-and-play-conventions"></a>Konvence IoT Plug and Play

Zařízení technologie Plug and Play IoT by měla při výměně zpráv se službou IoT Hub postupovat podle sady konvencí. Zařízení IoT technologie Plug and Play používají ke komunikaci s IoT Hub protokol MQTT.

Zařízení mohou zahrnovat [moduly](../iot-hub/iot-hub-devguide-module-twins.md)nebo být implementovány v [modulu IoT Edge](../iot-edge/about-iot-edge.md) hostovaném IoT Edge modulem runtime.

Popíšete telemetrii, vlastnosti a příkazy, které zařízení technologie Plug and Play IoT implementuje, pomocí modelu [DTDL (Digital Reed Definition Language) verze V2](https://github.com/Azure/opendigitaltwins-dtdl) _model_. V tomto článku jsou uvedeny dva typy modelu:

- **Žádná součást** -model bez komponent. Model deklaruje telemetrii, vlastnosti a příkazy jako vlastnosti nejvyšší úrovně v části obsah hlavního rozhraní. V nástroji Azure IoT Explorer se tento model zobrazuje jako jediná _výchozí součást_.
- **Více komponent** – model složený ze dvou nebo více rozhraní. Hlavní rozhraní, které se zobrazí jako _výchozí komponenta_ s telemetrie, vlastnostmi a příkazy. Jedno nebo více rozhraní deklarované jako komponenty s další telemetrie, vlastnostmi a příkazy.

Další informace naleznete v tématu [komponenty IoT technologie Plug and Play v modelech](concepts-components.md).

## <a name="identify-the-model"></a>Identifikace modelu

Aby bylo možné ohlásit model, který implementuje, zařízení IoT technologie Plug and Play nebo modul zahrnuje ID modelu v paketu připojení MQTT přidáním `model-id` do `USERNAME` pole.

Pro identifikaci modelu, ze kterého zařízení nebo modul implementuje, může služba získat ID modelu z:

- Pole vlákna zařízení `modelId` .
- Pole digitálního vlákna `$metadata.$model` .
- Digitální dvojitá oznámení o změně.

## <a name="telemetry"></a>Telemetrie

Telemetrie odesílaná ze zařízení bez součásti nevyžaduje žádná další metadata. Systém přidá `dt-dataschema` vlastnost.

Telemetrii odeslaná ze zařízení s více součástmi musí být přidána `$.sub` jako vlastnost zprávy. Systém přidá `dt-subject` `dt-dataschema` vlastnosti a.

## <a name="read-only-properties"></a>Vlastnosti jen pro čtení

### <a name="sample-no-component-read-only-property"></a>Ukázková vlastnost bez součásti jen pro čtení

Zařízení nebo modul může poslat libovolný platný JSON, který následuje po pravidlech DTDL v2.

DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

Ukázka datové části hlášené vlastnosti:

```json
"reported" :
{
  "temperature" : 21.3
}
```

### <a name="sample-multiple-components-read-only-property"></a>Ukázková více komponent – vlastnost jen pro čtení

Zařízení nebo modul musí přidat `{"__t": "c"}` značku, aby označoval, že element odkazuje na komponentu.

DTDL, které odkazují na komponentu:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}
```

DTDL definující komponentu:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

Ukázka datové části hlášené vlastnosti:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "temperature": 21.3
  }
}
```

## <a name="writable-properties"></a>Vlastnosti s možností zápisu

Zařízení nebo modul by měl potvrdit, že obdržel vlastnost odesláním hlášené vlastnosti. Vykazovaná vlastnost by měla zahrnovat:

- `value` – Skutečná hodnota vlastnosti (obvykle přijatá hodnota, ale zařízení se může rozhodnout, že nahlásí jinou hodnotu).
- `ac` – potvrzovací kód, který používá stavový kód HTTP.
- `av` – verze potvrzení, která odkazuje na `$version` požadovanou vlastnost. Tuto hodnotu můžete najít v datové části JSON požadované vlastnosti.
- `ad` – volitelný popis potvrzení.

Po spuštění zařízení by se mělo vyžádat, aby zařízení bylo zdvojené a kontrolovalo všechny zapisovatelné aktualizace vlastností. Pokud se v době, kdy bylo zařízení v režimu offline, zvýšila verze vlastnosti s možností zápisu, zařízení by mělo odeslat oznámenou odpověď na vlastnost, která potvrdí, že aktualizace přijala.

Když se zařízení poprvé spustí, může odeslat počáteční hodnotu pro hlášené vlastnosti, pokud neobdrží počáteční požadovanou vlastnost z rozbočovače. V takovém případě by mělo být zařízení nastavené `av` na `1` . Příklad:

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 1,
    "ad": "initialize"
  }
}
```

Zařízení může použít hlášenou vlastnost k poskytnutí dalších informací do centra. Zařízení může například reagovat pomocí série probíhajících zpráv, například:

```json
"reported": {
  "targetTemperature": {
    "value": 35.0,
    "ac": 202,
    "av": 3,
    "ad": "In-progress - reporting current temperature"
  }
}
```

Když zařízení dosáhne cílové teploty, pošle následující zprávu:

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 3,
    "ad": "Reached target temperature"
  }
}
```

Zařízení může nahlásit chybu, například:

```json
"reported": {
  "targetTemperature": {
    "value": 120.0,
    "ac": 500,
    "av": 3,
    "ad": "Target temperature out of range. Valid range is 10 to 99."
  }
}
```

### <a name="sample-no-component-writable-property"></a>Ukázka vlastnosti bez zapisovatelného prvku

Když zařízení obdrží více hlášených vlastností v jedné datové části, může odesílat hlášené odezvy vlastností napříč více datovými částmi.

Zařízení nebo modul může poslat libovolný platný kód JSON, který následuje po pravidlech DTDL v2:

DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    }
  ]
}
```

Ukázka datové části požadované vlastnosti:

```json
"desired" :
{
  "targetTemperature" : 21.3,
  "targetHumidity" : 80
},
"$version" : 3
```

Ukázka první datové části hlášené vlastnosti:

```json
"reported": {
  "targetTemperature": {
    "value": 21.3,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

Ukázka ohlásila druhou datovou část:

```json
"reported": {
  "targetHumidity": {
    "value": 80,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

### <a name="sample-multiple-components-writable-property"></a>Ukázka vlastnosti s možností zápisu na více komponent

Zařízení nebo modul musí přidat `{"__t": "c"}` značku, aby označoval, že element odkazuje na komponentu.

Značka je odeslána pouze pro aktualizace vlastností definovaných v součásti. Aktualizace vlastností definovaných ve výchozích součástech neobsahují značku, viz [Ukázka vlastnost bez zapisovatelné komponenty](#sample-no-component-writable-property) .

Když zařízení obdrží více hlášených vlastností v jedné datové části, může odesílat hlášené odezvy vlastností napříč více datovými částmi.

Zařízení nebo modul by měl potvrdit, že přijaly vlastnosti odesláním hlášených vlastností:

DTDL, které odkazují na komponentu:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}
```

DTDL definující komponentu:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    }
  ]
}
```

Ukázka datové části požadované vlastnosti:

```json
"desired": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": 21.3,
    "targetHumidity": 80
  }
},
"$version" : 3
```

Ukázka první datové části hlášené vlastnosti:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": {
      "value": 23,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

Ukázka ohlásila druhou datovou část:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetHumidity": {
      "value": 80,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

## <a name="commands"></a>Příkazy

Žádná rozhraní komponenty nepoužívají název příkazu bez předpony.

V zařízení nebo modulu více rozhraní komponent používá názvy příkazů v následujícím formátu: `componentName*commandName` .

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili o konvencích IoT technologie Plug and Play, je zde několik dalších prostředků:

- [Jazyk DTDL (Digital autodefinition Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Sada SDK pro zařízení jazyka C](/azure/iot-hub/iot-c-sdk-ref/)
- [REST API IoT](/rest/api/iothub/device)
- [Komponenty modelu](./concepts-components.md)