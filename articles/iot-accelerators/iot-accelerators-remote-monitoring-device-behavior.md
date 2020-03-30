---
title: Simulované zařízení v řešení vzdáleného monitorování – Azure | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak pomocí JavaScriptu definovat chování simulovaného zařízení v řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: c39ca0a018bd22844cf7e5350e6d3586319aac16
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890850"
---
# <a name="implement-the-device-model-behavior"></a>Implementace chování modelu zařízení

Článek [Pochopit schéma modelu zařízení](iot-accelerators-remote-monitoring-device-schema.md) popsané schéma, které definuje model simulované zařízení. Tento článek odkazoval na dva typy souboru JavaScript, které implementují chování simulovaného zařízení:

- **Stát** JavaScript soubory, které běží v pevných intervalech aktualizovat vnitřní stav zařízení.
- **Metoda** Soubory JavaScriptu, které se spustí, když řešení vyvolá metodu v zařízení.

> [!NOTE]
> Chování modelu zařízení je pouze pro simulovaná zařízení hostovaná ve službě simulace zařízení. Pokud chcete vytvořit skutečné zařízení, přečtěte si informace [o připojení zařízení k akcelerátoru řešení vzdáleného monitorování](iot-accelerators-connecting-devices.md).

V tomto článku získáte informace o těchto tématech:

>[!div class="checklist"]
> * Řízení stavu simulovaného zařízení
> * Definujte, jak simulované zařízení reaguje na volání metody z řešení vzdáleného monitorování
> * Ladění skriptů

## <a name="state-behavior"></a>Chování stavu

Část [Simulace](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#simulation) schématu modelu zařízení definuje vnitřní stav simulovaného zařízení:

- `InitialState`definuje počáteční hodnoty pro všechny vlastnosti objektu stavu zařízení.
- `Script`identifikuje soubor JavaScriptu, který běží podle plánu pro aktualizaci stavu zařízení.

Následující příklad ukazuje definici objektu stavu zařízení pro simulované zařízení chladiče:

```json
"Simulation": {
  "InitialState": {
    "online": true,
    "temperature": 75.0,
    "temperature_unit": "F",
    "humidity": 70.0,
    "humidity_unit": "%",
    "pressure": 150.0,
    "pressure_unit": "psig",
    "simulation_state": "normal_pressure"
  },
  "Interval": "00:00:05",
  "Scripts": {
    "Type": "javascript",
    "Path": "chiller-01-state.js"
  }
}
```

Stav simulovaného zařízení, jak je `InitialState` definován v části, je v paměti simulační službou. Informace o stavu jsou předávány jako vstup do `main` funkce definované v **souboru chiller-01-state.js**. V tomto příkladu spustí simulační služba každých pět sekund soubor **chiller-01-state.js.** Skript může upravit stav simulovaného zařízení.

Následující text ukazuje obrys `main` typické funkce:

```javascript
function main(context, previousState, previousProperties) {

  // Use the previous device state to
  // generate the new device state
  // returned by the function.

  return state;
}
```

Parametr `context` má následující vlastnosti:

- `currentTime`jako řetězec s formátem`yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, například`Simulated.Chiller.123`
- `deviceModel`, například`Chiller`

Parametr `state` obsahuje stav zařízení, který udržuje simulační služba zařízení. Tato hodnota `state` je objekt vrácený `main`předchozí volání .

Následující příklad ukazuje typickou `main` implementaci metody zpracování stavu zařízení spravovaného simulační službou:

```javascript
// Default state
var state = {
  online: true,
  temperature: 75.0,
  temperature_unit: "F",
  humidity: 70.0,
  humidity_unit: "%",
  pressure: 150.0,
  pressure_unit: "psig",
  simulation_state: "normal_pressure"
};

function restoreState(previousState) {
  // If the previous state is null, force a default state
  if (previousState !== undefined && previousState !== null) {
      state = previousState;
  } else {
      log("Using default state");
  }
}

function main(context, previousState, previousProperties) {

  restoreState(previousState);

  // Update state

  return state;
}
```

Následující příklad ukazuje, `main` jak může metoda simulovat hodnoty telemetrie, které se v průběhu času liší:

```javascript
/**
 * Simple formula generating a random value
 * around the average and between min and max
 */
function vary(avg, percentage, min, max) {
  var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
  value = Math.max(value, min);
  value = Math.min(value, max);
  return value;
}


function main(context, previousState, previousProperties) {

    restoreState(previousState);

    // 75F +/- 5%,  Min 25F, Max 100F
    state.temperature = vary(75, 5, 25, 100);

    // 70% +/- 5%,  Min 2%, Max 99%
    state.humidity = vary(70, 5, 2, 99);

    log("Simulation state: " + state.simulation_state);
    if (state.simulation_state === "high_pressure") {
        // 250 psig +/- 25%,  Min 50 psig, Max 300 psig
        state.pressure = vary(250, 25, 50, 300);
    } else {
        // 150 psig +/- 10%,  Min 50 psig, Max 300 psig
        state.pressure = vary(150, 10, 50, 300);
    }

    return state;
}
```

Můžete zobrazit kompletní [chiller-01-state.js](https://github.com/Azure/device-simulation-dotnet/blob/master/Services/data/devicemodels/scripts/chiller-01-state.js) na GitHubu.

## <a name="method-behavior"></a>Chování metody

[CloudToDeviceMethods](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#cloudtodevicemethods) části schématu modelu zařízení definuje metody simulované zařízení reaguje.

Následující příklad ukazuje seznam metod podporovaných simulovaným chladicím zařízením:

```json
"CloudToDeviceMethods": {
  "Reboot": {
    "Type": "javascript",
    "Path": "Reboot-method.js"
  },
  "FirmwareUpdate": {
    "Type": "javascript",
    "Path": "FirmwareUpdate-method.js"
  },
  "EmergencyValveRelease": {
    "Type": "javascript",
    "Path": "EmergencyValveRelease-method.js"
  },
  "IncreasePressure": {
    "Type": "javascript",
    "Path": "IncreasePressure-method.js"
  }
}
```

Každá metoda má přidružený soubor JavaScript, který implementuje chování metody.

Stav simulovaného zařízení, jak je `InitialState` definován v části schématu, je v paměti uložen simulační službou. Informace o stavu jsou předávány jako vstup do `main` funkce definované v souboru JavaScript, když je metoda volána. Skript může upravit stav simulovaného zařízení.

Následující text ukazuje obrys `main` typické funkce:

```javascript
function main(context, previousState, previousProperties) {

}
```

Parametr `context` má následující vlastnosti:

- `currentTime`jako řetězec s formátem`yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, například`Simulated.Chiller.123`
- `deviceModel`, například`Chiller`

Parametr `state` obsahuje stav zařízení, který udržuje simulační služba zařízení.

Parametr `properties` obsahuje vlastnosti zařízení, které jsou zapsány jako hlášené vlastnosti dvojčezařízení služby IoT Hub.

Existují tři globální funkce, které můžete použít k implementaci chování metody:

- `updateState`aktualizovat stav, který má simulační služba v držení.
- `updateProperty`aktualizace jedné vlastnosti zařízení.
- `sleep`pozastavit provádění simulovat dlouhotrvající úlohu.

Následující příklad ukazuje zkrácenou verzi skriptu **IncreasePressure-method.js** používaného simulovanými chladicími zařízeními:

```javascript
function main(context, previousState, previousProperties) {

    log("Starting 'Increase Pressure' method simulation (5 seconds)");

    // Pause the simulation and change the simulation mode so that the
    // temperature will fluctuate at ~250 when it resumes
    var state = {
        simulation_state: "high_pressure",
        CalculateRandomizedTelemetry: false
    };
    updateState(state);

    // Increase
    state.pressure = 210;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Increase
    state.pressure = 250;
    updateState(state);
    log("Pressure increased to " + state.pressure);
    sleep(1000);

    // Resume the simulation
    state.CalculateRandomizedTelemetry = true;
    updateState(state);

    log("'Increase Pressure' method simulation completed");
}
```

## <a name="debugging-script-files"></a>Ladění souborů skriptů

Ladicí program není možné připojit k interpretu Javascriptu používanému simulační službou zařízení ke spuštění skriptů stavu a metody. Informace však můžete protokolovat v protokolu služeb. Vestavěná `log()` funkce umožňuje ukládat informace ke sledování a ladění spuštění funkce.

Pokud dojde k chybě syntaxe, překladač selže a zapíše položku `Jint.Runtime.JavaScriptException` do protokolu služby.

[Spuštění služby místně](https://github.com/Azure/device-simulation-dotnet#running-the-service-locally-eg-for-development-tasks) článek na GitHubu ukazuje, jak spustit službu simulace zařízení místně. Spuštění služby místně usnadňuje ladění simulovaných zařízení před jejich nasazením do cloudu.

## <a name="next-steps"></a>Další kroky

Tento článek popisuje, jak definovat chování vlastního modelu simulovaného zařízení. Tento článek vám ukázal, jak:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Řízení stavu simulovaného zařízení
> * Definujte, jak simulované zařízení reaguje na volání metody z řešení vzdáleného monitorování
> * Ladění skriptů

Nyní, když jste se naučili, jak určit chování simulovaného zařízení, je navrhovaným dalším krokem naučit se [vytvořit simulované zařízení](iot-accelerators-remote-monitoring-create-simulated-device.md).

Další informace pro vývojáře o řešení vzdáleného monitorování naleznete v následujících tématech:

* [Referenční příručka pro vývojáře](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Průvodce odstraňováním potíží s vývojáři](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
