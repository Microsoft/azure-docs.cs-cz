---
title: Simulované zařízení v řešení vzdáleného monitorování – Azure | Microsoft Docs
description: Tento článek popisuje, jak pomocí JavaScriptu definovat chování simulovaného zařízení v řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: 7f887aac91bdb1b8c752806c7c5076708a40bc10
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91276165"
---
# <a name="implement-the-device-model-behavior"></a>Implementace chování modelu zařízení

Článek [porozumění schématu modelu zařízení](iot-accelerators-remote-monitoring-device-schema.md) popisuje schéma definující model simulovaného zařízení. Tento článek odkazoval na dva typy souborů JavaScriptu, které implementují chování simulovaného zařízení:

- **Stav** Soubory JavaScriptu, které se spouští v pevných intervalech k aktualizaci vnitřního stavu zařízení.
- **Metoda** Soubory jazyka JavaScript, které jsou spouštěny, když řešení vyvolá metodu na zařízení.

> [!NOTE]
> Chování modelu zařízení je pouze pro simulovaná zařízení hostovaná ve službě simulace zařízení. Pokud chcete vytvořit reálné zařízení, přečtěte si téma [připojení zařízení k akcelerátoru řešení vzdáleného monitorování](iot-accelerators-connecting-devices.md).

V tomto článku získáte informace o těchto tématech:

>[!div class="checklist"]
> * Řízení stavu simulovaného zařízení
> * Definování způsobu, jakým simulované zařízení reaguje na volání metody z řešení vzdáleného monitorování
> * Ladění skriptů

## <a name="state-behavior"></a>Chování stavu

Oddíl [simulace](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#simulation) schématu modelu zařízení definuje vnitřní stav simulovaného zařízení:

- `InitialState` Definuje počáteční hodnoty pro všechny vlastnosti objektu stavu zařízení.
- `Script` Identifikuje soubor JavaScriptu, který se spouští podle plánu, aby se aktualizoval stav zařízení.

Následující příklad ukazuje definici objektu stavu zařízení pro simulované chladicí zařízení:

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

Stav simulovaného zařízení, jak je definováno v `InitialState` oddílu, je uchováván v paměti službou simulace. Informace o stavu jsou předány jako vstup do `main` funkce definované v **chiller-01-state.js**. V tomto příkladu služba simulace spouští soubor **chiller-01-state.js** každých pět sekund. Skript může změnit stav simulovaného zařízení.

Následující příklad ukazuje osnovu typické `main` funkce:

```javascript
function main(context, previousState, previousProperties) {

  // Use the previous device state to
  // generate the new device state
  // returned by the function.

  return state;
}
```

`context`Parametr má následující vlastnosti:

- `currentTime` jako řetězec ve formátu `yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`například `Simulated.Chiller.123`
- `deviceModel`například `Chiller`

`state`Parametr obsahuje stav zařízení, které udržuje služba pro simulaci zařízení. Tato hodnota je `state` objekt vrácený předchozím voláním metody `main` .

Následující příklad ukazuje typickou implementaci `main` metody pro zpracování stavu zařízení spravovaného službou simulace:

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

Následující příklad ukazuje, jak `main` metoda může simulovat hodnoty telemetrie, které se v průběhu času liší:

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

Kompletní [chiller-01-state.js](https://github.com/Azure/device-simulation-dotnet/blob/master/Services/data/devicemodels/scripts/chiller-01-state.js) můžete zobrazit na GitHubu.

## <a name="method-behavior"></a>Chování metody

Část [CloudToDeviceMethods](../../articles/iot-accelerators/iot-accelerators-remote-monitoring-device-schema.md#cloudtodevicemethods) schématu modelu zařízení definuje metody, na které simulované zařízení reaguje.

Následující příklad ukazuje seznam metod podporovaných simulovaným zařízením chladicího zařízení:

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

Každá metoda má přidružený soubor JavaScriptu, který implementuje chování metody.

Stav simulovaného zařízení, jak je definováno v `InitialState` části schématu, je držen v paměti službou simulace. Informace o stavu jsou předány jako vstup do `main` funkce definované v souboru JavaScriptu při volání metody. Skript může změnit stav simulovaného zařízení.

Následující příklad ukazuje osnovu typické `main` funkce:

```javascript
function main(context, previousState, previousProperties) {

}
```

`context`Parametr má následující vlastnosti:

- `currentTime` jako řetězec ve formátu `yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`například `Simulated.Chiller.123`
- `deviceModel`například `Chiller`

`state`Parametr obsahuje stav zařízení, které udržuje služba pro simulaci zařízení.

`properties`Parametr obsahuje vlastnosti zařízení, které jsou zapsány jako hlášené vlastnosti, do vlákna IoT Hub zařízení.

Existují tři globální funkce, které lze použít pro usnadnění implementace chování metody:

- `updateState` aktualizace stavu, který uchovává služba simulace.
- `updateProperty` Chcete-li aktualizovat jednu vlastnost zařízení.
- `sleep` pozastavení provádění za účelem simulace dlouhotrvající úlohy.

Následující příklad ukazuje zkrácenou verzi **IncreasePressure-method.js** skriptu používaném simulovanými chladicími zařízeními:

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

## <a name="debugging-script-files"></a>Ladění souborů skriptu

Není možné připojit ladicí program k překladači JavaScriptu, který používá služba pro simulaci zařízení ke spouštění skriptů stavu a metody. Můžete však protokolovat informace v protokolu služby. Integrovaná `log()` funkce umožňuje ukládat informace pro sledování a ladění provádění funkce.

Pokud dojde k chybě syntaxe, překladač se nezdařil a zapíše `Jint.Runtime.JavaScriptException` položku do protokolu služby.

V článku o [Spuštění místně spuštěné služby](https://github.com/Azure/device-simulation-dotnet#running-the-service-locally-eg-for-development-tasks) na GitHubu se dozvíte, jak místně spustit službu pro simulaci zařízení. Místní spuštění služby usnadňuje ladění simulovaných zařízení před jejich nasazením do cloudu.

## <a name="next-steps"></a>Další kroky

Tento článek popisuje, jak definovat chování vlastního modelu simulovaného zařízení. Tento článek vám ukázal, jak:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Řízení stavu simulovaného zařízení
> * Definování způsobu, jakým simulované zařízení reaguje na volání metody z řešení vzdáleného monitorování
> * Ladění skriptů

Teď, když jste se naučili, jak určit chování simulovaného zařízení, je navržený další krok, kde se dozvíte, jak [vytvořit simulované zařízení](iot-accelerators-remote-monitoring-create-simulated-device.md).

Další informace o vývojářích řešení vzdáleného monitorování najdete v těchto tématech:

* [Referenční příručka pro vývojáře](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Příručka pro řešení potíží pro vývojáře](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
