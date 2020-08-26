---
title: Konvence technologie Plug and Play IoT | Microsoft Docs
description: Popis konvencí IoT technologie Plug and Play očekává, že se zařízení použijí při odesílání telemetrie a vlastností a zpracování příkazů a aktualizací vlastností.
author: rido-min
ms.author: rmpablos
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ef29be53e776c4c185ac8430b3340c53ca85d855
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/25/2020
ms.locfileid: "88856043"
---
# <a name="iot-plug-and-play-conventions"></a>Konvence IoT Plug and Play

Zařízení IoT technologie Plug and Play ve verzi Preview by měla při výměně zpráv se službou IoT Hub dodržovat sadu konvencí. Zařízení IoT technologie Plug and Play ve verzi Preview používají ke komunikaci s IoT Hub protokol MQTT.

Popíšete telemetrii, vlastnosti a příkazy, které zařízení technologie Plug and Play IoT implementuje, pomocí modelu [DTDL (Digital Reed Definition Language) verze V2](https://github.com/Azure/opendigitaltwins-dtdl) _model_. V tomto článku jsou uvedeny dva typy modelu:

- **Žádná součást** -model bez komponent. Model deklaruje telemetrii, vlastnosti a příkazy jako vlastnosti nejvyšší úrovně v části obsah hlavního rozhraní.
- **Více komponent** – model složený ze dvou nebo více rozhraní. Hlavní rozhraní s telemetrie, vlastnostmi a příkazy. Jedno nebo více rozhraní deklarované jako komponenty s další telemetrie, vlastnostmi a příkazy.

Další informace naleznete v tématu [komponenty IoT technologie Plug and Play v modelech](concepts-components.md).

## <a name="identify-the-model"></a>Identifikace modelu

Aby bylo možné ohlásit model, který implementuje, zařízení IoT technologie Plug and Play zahrnuje ID modelu v paketu připojení MQTT přidáním `model-id` do `USERNAME` pole.

Pro identifikaci modelu, který zařízení implementuje, může služba získat ID modelu z:

- Pole vlákna zařízení `modelId` .
- Pole digitálního vlákna `$metadata.$model` .
- Digitální dvojitá oznámení o změně.

## <a name="telemetry"></a>Telemetrie

Telemetrie odesílaná ze zařízení bez součásti nevyžaduje žádná další metadata. Systém přidá `dt-dataschema` vlastnost.

Telemetrii odeslaná ze zařízení s více součástmi musí být přidána `$.sub` jako vlastnost zprávy. Systém přidá `dt-subject` `dt-dataschema` vlastnosti a.

## <a name="read-only-properties"></a>Vlastnosti jen pro čtení

### <a name="sample-no-component-read-only-property"></a>Ukázková vlastnost bez součásti jen pro čtení

Zařízení může poslat libovolný platný kód JSON, který následuje po pravidlech DTDL v2.

:::row:::
   :::column span="":::
      **DTDL**

      ```json
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
      ```
   :::column-end:::
   :::column span="":::
      **Ukázková datová část**

      ```json
      "reported" :
      {
        "temperature" : 21.3
      }
      ```
   :::column-end:::
:::row-end:::

### <a name="sample-multiple-components-read-only-property"></a>Ukázková více komponent – vlastnost jen pro čtení

Zařízení musí přidat `{"__t": "c"}` značku, aby označovala, že element odkazuje na komponentu.

:::row:::
   :::column span="":::
      **DTDL**

      ```json
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
      ```
   :::column-end:::
   :::column span="":::
      **Vykazovaná – vlastnost**

      ```json
      "reported": {
        "thermostat1": {
          "__t": "c",
          "temperature": 21.3
        }
      }
      ```
   :::column-end:::
:::row-end:::

## <a name="writable-properties"></a>Vlastnosti s možností zápisu

Zařízení by mělo potvrdit, že obdržel vlastnost odesláním hlášené vlastnosti. Vykazovaná vlastnost by měla zahrnovat:

- `value` – Skutečná hodnota vlastnosti (obvykle přijatá hodnota, ale zařízení se může rozhodnout, že nahlásí jinou hodnotu).
- `ac` – potvrzovací kód, který používá stavový kód HTTP.
- `av` – verze potvrzení, která odkazuje na `$version` požadovanou vlastnost.
- `ad` – volitelný popis potvrzení.

### <a name="sample-no-component-writable-property"></a>Ukázka vlastnosti bez zapisovatelného prvku

Zařízení může poslat libovolný platný kód JSON, který následuje po pravidlech DTDL v2:

:::row:::
   :::column span="":::
      **DTDL**

      ```json
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
      ```
   :::column-end:::
   :::column span="":::
      **Požadovaná vlastnost**

      ```json
      "desired" :
      {
        "targetTemperature" : 21.3
      },
      "$version" : 3
      ```
   :::column-end:::
   :::column span="":::
      **Vykazovaná – vlastnost**

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
   :::column-end:::
:::row-end:::

### <a name="sample-multiple-components-writable-property"></a>Ukázka vlastnosti s možností zápisu na více komponent

Zařízení musí přidat `{"__t": "c"}` značku, aby označovala, že element odkazuje na komponentu.

Tato značka se pošle jenom na aktualizace na úrovni součásti, takže zařízení nesmí kontrolu tohoto příznaku.

Zařízení by mělo potvrdit, že vlastnost obdržela odesláním hlášené vlastnosti:

:::row:::
   :::column span="":::
      **DTDL**

      ```json
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
      ```
   :::column-end:::
   :::column span="":::
      **Požadovaná vlastnost**

      ```json
      "desired": {
        "thermostat1": {
          "__t": "c",
          "targetTemperature": 21.3
        }
      },
      "$version" : 3
      ```
   :::column-end:::
   :::column span="":::
      **Vykazovaná – vlastnost**

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
   :::column-end:::
:::row-end:::

## <a name="commands"></a>Příkazy

Žádná rozhraní komponenty nepoužívají název příkazu bez předpony.

V zařízení více rozhraní komponent používá názvy příkazů v následujícím formátu: `componentName*commandName` .

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili o konvencích IoT technologie Plug and Play, je zde několik dalších prostředků:

- [Jazyk DTDL (Digital autodefinition Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Sada SDK pro zařízení jazyka C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [REST API IoT](https://docs.microsoft.com/rest/api/iothub/device)
- [Komponenty modelu](./concepts-components.md)
