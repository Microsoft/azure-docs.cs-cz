---
title: zahrnout soubor
description: zahrnout soubor
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 07/26/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: cb1392bd70c92ae4f6bfc6707198c3079ad7ecef
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39285717"
---
## <a name="state-behavior"></a>Chování stavu

[Simulace](../articles/iot-accelerators/iot-accelerators-device-simulation-device-schema.md#simulation) části schématu modelu zařízení definuje vnitřní stav Simulovaná zařízení:

- `InitialState` Definuje počáteční hodnoty pro všechny vlastnosti objektu stavu zařízení.
- `Script` identifikuje soubor jazyka JavaScript, která běží na plán, který chcete aktualizovat stav zařízení.

Následující příklad ukazuje definici objektu stavu zařízení pro zařízení s Simulovaná chladič:

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

Stav simulované zařízení, jak jsou definovány v `InitialState` části, je udržována v paměti službou simulace. Informace o stavu je předán jako vstup `main` funkci definovanou v **chladič-01-state.js**. V tomto příkladu simulace služba běží **chladič-01-state.js** souboru každých pět sekund. Skript můžete změnit stav simulované zařízení.

Následující příklad zobrazuje přehled typické `main` funkce:

```javascript
function main(context, previousState, previousProperties) {

  // Use the previous device state to
  // generate the new device state
  // returned by the function.

  return state;
}
```

`context` Parametr má následující vlastnosti:

- `currentTime` jako řetězec s formátem `yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, například `Simulated.Chiller.123`
- `deviceModel`, například `Chiller`

`state` Parametr obsahuje stav zařízení, která jsou spravovaná službou simulace zařízení. Tato hodnota je `state` objekt vrácený z předchozího volání `main`.

Následující příklad ukazuje obvyklá implementace metody `main` metodu ke zpracování stavu zařízení spravovaná službou simulace:

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

Následující příklad ukazuje způsob, jakým `main` metoda můžete simulovat hodnoty telemetrie, které se liší v čase:

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

Můžete zobrazit úplný [chladič-01-state.js](https://github.com/Azure/device-simulation-dotnet/blob/master/Services/data/devicemodels/scripts/chiller-01-state.js) na Githubu.

## <a name="method-behavior"></a>Chování metody

[CloudToDeviceMethods](../articles/iot-accelerators/iot-accelerators-device-simulation-device-schema.md#cloudtodevicemethods) části schématu modelu zařízení definuje metody Simulovaná zařízení jsou reaguje na.

Následující příklad zobrazuje seznam metod podporovaných chladič simulované zařízení:

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

Každá metoda má přidružený soubor jazyka JavaScript, který implementuje chování metody.

Stav simulované zařízení, jak jsou definovány v `InitialState` části schématu, je udržována v paměti službou simulace. Informace o stavu je předán jako vstup `main` funkci definovanou v souboru jazyka JavaScript, když je volána metoda. Skript můžete změnit stav simulované zařízení.

Následující příklad zobrazuje přehled typické `main` funkce:

```javascript
function main(context, previousState, previousProperties) {

}
```

`context` Parametr má následující vlastnosti:

- `currentTime` jako řetězec s formátem `yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, například `Simulated.Chiller.123`
- `deviceModel`, například `Chiller`

`state` Parametr obsahuje stav zařízení, která jsou spravovaná službou simulace zařízení.

`properties` Parametr obsahuje vlastnosti zařízení, které se zapisují jako ohlášené vlastnosti do dvojčete zařízení služby IoT Hub.

Existují tři globální funkce, které lze použít k implementaci chování metody:

- `updateState` k aktualizaci stavu uchovávaného touto službu simulace.
- `updateProperty` k aktualizaci jedné vlastnosti zařízení.
- `sleep` pozastavit provádění simulovat dlouhotrvající úlohy.

Následující příklad ukazuje zkrácenou verzi **IncreasePressure method.js** skript chladič s Simulovaná zařízení používají:

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

## <a name="debugging-script-files"></a>Ladění skriptů

Není možné připojit ladicí program k interpretu Javascript používá služba simulace zařízení spouštět skripty stavu a metody. Však můžete protokolovat informace v protokolu služby. Předdefinované `log()` funkce umožňuje uložit informace o sledování a ladění provádění funkce.

Pokud dochází k chybě syntaxe interpretu selže, a zapíše `Jint.Runtime.JavaScriptException` položka do protokolu služby.

[Místně spuštěná služba](https://github.com/Azure/device-simulation-dotnet#running-the-service-locally-eg-for-development-tasks) článku na Githubu se dozvíte, jak místně spustit službu pro simulaci zařízení. Místně spuštěná služba usnadňuje ladění simulovaných zařízení, před jejich nasazením do cloudu.