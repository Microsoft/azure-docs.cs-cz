---
title: Simulované zařízení chování v řešení vzdáleného monitorování - Azure | Microsoft Docs
description: Tento článek popisuje, jak definovat chování simulované zařízení v řešení vzdáleného monitorování pomocí jazyka JavaScript.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/29/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 2a2cbe5379adbd2c4ad6534b621871ecc30bfc81
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="implement-the-device-model-behavior"></a>Implementace chování modelu zařízení

Článek [pochopit schéma modelu zařízení](iot-suite-remote-monitoring-device-schema.md) popisuje schéma, které definuje model simulované zařízení. Tento článek uvedené dva typy soubor JavaScript, které implementují chování simulované zařízení:

- **Stav** JavaScript soubory, které spustit v pevných intervalech aktualizovat vnitřní stav zařízení.
- **Metoda** JavaScript soubory, které jsou spuštěny při řešení volá metodu na zařízení.

V tomto článku získáte informace o těchto tématech:

>[!div class="checklist"]
> * Řízení stavu simulovaného zařízení
> * Definujte, jak reponds simulované zařízení na metodu volat z řešení vzdáleného monitorování
> * Ladění skriptů

## <a name="state-behavior"></a>Chování stavu

[Simulace](iot-suite-remote-monitoring-device-schema.md#simulation) části schéma modelu zařízení definuje vnitřní stav simulované zařízení:

- `InitialState` Definuje počáteční hodnoty pro všechny vlastnosti objektu stavu zařízení.
- `Script` identifikuje soubor JavaScript, která běží na plán, který chcete aktualizovat stav zařízení.

Následující příklad ukazuje definici objektu stavu zařízení pro chladič simulované zařízení:

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

Stav simulované zařízení, jak je definována v `InitialState` části, je udržována v paměti službou simulace. Informace o stavu se předá jako vstup `main` funkci definovanou v **chladič-01-state.js**. V tomto příkladu simulace služba bude spuštěna **chladič-01-state.js** souboru každých pět sekund. Skript můžete změnit stav simulované zařízení.

Následující příklad zobrazuje obrys typické `main` funkce:

```javascript
function main(context, previousState, previousProperties) {

  // Use the previous device state to
  // generate the new device state
  // returned by the function.

  return state;
}
```

`context` Parametr má následující vlastnosti:

- `currentTime` jako řetězec ve formátu `yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, například `Simulated.Chiller.123`
- `deviceModel`, například `Chiller`

`state` Parametr obsahuje stav zařízení, která jsou spravovaná službou simulace zařízení. Tato hodnota je `state` vrácený z předchozího volání `main`.

Následující příklad ukazuje Typická implementace `main` metoda pro zpracování stavu zařízení spravovaná službou simulace:

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

Následující příklad ukazuje jak `main` metoda můžete simulovat hodnot telemetrie v průběhu času:

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

Můžete zobrazit kompletní [chladič-01-state.js](https://github.com/Azure/device-simulation-dotnet/blob/master/Services/data/devicemodels/scripts/chiller-01-state.js) na Githubu.

## <a name="method-behavior"></a>Chování – metoda

[CloudToDeviceMethods](iot-suite-remote-monitoring-device-schema.md#cloudtodevicemethods) části schéma modelu zařízení definuje metody, simulované zařízení reaguje na.

Následující příklad ukazuje seznam metod podporovaných chladič simulované zařízení:

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

Každá z metod má přidružený soubor JavaScript, který implementuje chování metody.

Stav simulované zařízení, jak je definována v `InitialState` části schématu, je udržována v paměti službou simulace. Informace o stavu se předá jako vstup `main` funkci definovanou v souboru JavaScript při volání metody. Skript můžete změnit stav simulované zařízení.

Následující příklad zobrazuje obrys typické `main` funkce:

```javascript
function main(context, previousState, previousProperties) {

}
```

`context` Parametr má následující vlastnosti:

- `currentTime` jako řetězec ve formátu `yyyy-MM-dd'T'HH:mm:sszzz`
- `deviceId`, například `Simulated.Chiller.123`
- `deviceModel`, například `Chiller`

`state` Parametr obsahuje stav zařízení, která jsou spravovaná službou simulace zařízení.

`properties` Parametr obsahuje vlastnosti zařízení, které je zapsáno v jako hlášen vlastnostech dvojče zařízení IoT Hub.

Existují tři globální funkce, které můžete použít při provádění chování metody:

- `updateState` Aktualizovat stav uchovávat službou simulace.
- `updateProperty` Chcete-li aktualizovat jedné vlastnosti zařízení.
- `sleep` Chcete-li pozastavit provádění simulovat dlouhotrvající úlohy.

Následující příklad ukazuje, zkrácený verzi **IncreasePressure method.js** skript používá chladič simulované zařízení:

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

## <a name="debugging-script-files"></a>Soubory ladění skriptu

Není možné připojit ladicí program k překladač Javascript, který používá služba simulace zařízení ke spouštění skriptů stavu a metoda. Můžete však protokolovat informace v protokolu služby. Integrované `log()` funkce umožňuje uložit informace o sledování a ladění spuštění funkce.

Pokud je chyba syntaxe překladač selže, a zapisuje `Jint.Runtime.JavaScriptException` položka do protokolu služby.

[Vytvoření simulovaného zařízení](iot-suite-remote-monitoring-test.md) článek ukazuje, jak spustit službu simulace zařízení místně. Spuštěná služba místně usnadňuje ladění Simulovaná zařízení před jejich nasazením do cloudu.

## <a name="next-steps"></a>Další postup

Tento článek popisuje jak definovat chování modelu vlastní simulované zařízení. Tento článek ukázal, jak na:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Řízení stavu simulovaného zařízení
> * Definujte, jak reponds simulované zařízení na metodu volat z řešení vzdáleného monitorování
> * Ladění skriptů

Nyní jste se naučili určení chování simulované zařízení, navrhované dalším krokem je další postup [vytvoření simulovaného zařízení](iot-suite-remote-monitoring-test.md).

Další informace pro vývojáře o řešení vzdáleného monitorování najdete v části:

* [Referenční příručka pro vývojáře](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Průvodce řešením potíží pro vývojáře](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->