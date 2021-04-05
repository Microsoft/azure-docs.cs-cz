---
title: Vytvoření pokročilého modelu simulovaného zařízení – Azure | Microsoft Docs
description: V této příručce se dozvíte, jak vytvořit pokročilý model zařízení pro použití s akcelerátorem řešení pro simulaci zařízení.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: troyhop
ms.custom:
- mvc
- amqp
- mqtt
- devx-track-js
ms.openlocfilehash: 58891bdb4601744129c97454f43202d3ba07b709
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96852553"
---
# <a name="create-an-advanced-device-model"></a>Vytvoření pokročilého modelu zařízení

Tato příručka popisuje soubory JSON a JavaScript, které definují vlastní model zařízení. Článek obsahuje několik ukázkových definičních souborů modelů zařízení a ukazuje, jak je nahrát do instance simulace zařízení. Můžete vytvářet pokročilé modely zařízení a simulovat tak realističtější chování zařízení pro vaše testování.

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

Pokud chcete postupovat podle kroků v tomto průvodci, budete potřebovat nasazenou instanci simulace zařízení ve vašem předplatném Azure.

Pokud jste ještě nasadili simulaci zařízení, přečtěte si téma [nasazení simulace zařízení](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) na GitHubu.

### <a name="open-device-simulation"></a>Otevření Simulace zařízení

Pokud jste ještě nasadili simulaci zařízení, přečtěte si téma [nasazení simulace zařízení](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) na GitHubu.

## <a name="device-models"></a>Modely zařízení

Každé simulované zařízení patří ke konkrétnímu modelu zařízení, který definuje chování simulace. Toto chování zahrnuje jak často odesílat telemetrii, jaký druh zpráv odeslat a podporované metody.

Model zařízení definujete pomocí definičního souboru zařízení JSON a sady souborů JavaScriptu. Tyto soubory JavaScriptu definují chování simulace, jako je náhodná telemetrie a logika metody.

Typický model zařízení má:

* Jeden soubor JSON pro každý model zařízení (například elevator.jszapnutý).
* Jeden soubor skriptu chování JavaScriptu pro každý model zařízení (například elevator-state.js)
* Jeden soubor skriptu metody JavaScriptu pro každou metodu zařízení (například elevator-go-down.js)

> [!NOTE]
> Ne všechny modely zařízení definují metody. Proto model zařízení může nebo nemusí mít skripty metody. Všechny modely zařízení ale musí mít skript chování.

## <a name="device-definition-file"></a>Soubor definice zařízení

Každý definiční soubor zařízení obsahuje podrobnosti o simulovaném modelu zařízení, včetně následujících informací:

* Název modelu zařízení: řetězec.
* Protokol: AMQP | MQTT | HTTP.
* Počáteční stav zařízení.
* Jak často se má aktualizovat stav zařízení.
* Který soubor JavaScriptu se má použít k aktualizaci stavu zařízení.
* Seznam zpráv telemetrie, které se mají odeslat, z nichž každá má určitou frekvenci
* Schéma zpráv telemetrie, které aplikace využívají back-end k analýze přijatých telemetrie.
* Seznam podporovaných metod a souboru JavaScriptu, který se má použít k simulaci jednotlivých metod.

### <a name="file-schema"></a>Schéma souboru

Verze schématu je vždycky "1.0.0" a je specifická pro formát tohoto souboru:

```json
"SchemaVersion": "1.0.0"
```

### <a name="device-model-description"></a>Popis modelu zařízení

Následující vlastnosti popisují model zařízení. Každý typ má jedinečný identifikátor, sémantickou verzi, název a popis:

```json
"Id": "chiller-01",
"Version": "0.0.1",
"Name": "Chiller",
"Description": "Chiller with external temperature and humidity sensors."
```

### <a name="iot-protocol"></a>Protokol IoT

Zařízení IoT se můžou připojit pomocí různých protokolů. Simulace vám umožní použít buď **AMQP**, **MQTT**, nebo **http**:

```json
"Protocol": "AMQP"
```

### <a name="simulated-device-state"></a>Stav simulovaného zařízení

Každé simulované zařízení má vnitřní stav, který musí být definovaný. Stav také definuje vlastnosti, které mohou být hlášeny v telemetrie. Například chladicí událost může mít počáteční stav, například:

```json
"InitialState": {
    "temperature": 50,
    "humidity": 40
},
```

Přesunutí zařízení s několika senzory může mít více vlastností, například:

```json
"InitialState": {
    "latitude": 47.445301,
    "longitude": -122.296307,
    "speed": 30.0,
    "speed_unit": "mph",
    "cargotemperature": 38.0,
    "cargotemperature_unit": "F"
}
```

Stav zařízení je udržován v paměti službou simulace a poskytuje se jako vstup do funkce JavaScriptu. Funkce JavaScriptu by mohla rozhodnout:

* Pro ignorování stavu a generování náhodných dat.
* Postup aktualizace stavu zařízení v některých reálných způsobech pro daný scénář.

Funkce, která generuje stav, obdrží také jako vstup:

* ID zařízení.
* Model zařízení.
* Aktuální čas. Tato hodnota umožňuje generovat různá data podle zařízení a podle času.

### <a name="generating-telemetry-messages"></a>Generování zpráv telemetrie

Služba simulace může pro každé zařízení odeslat několik typů telemetrie. Telemetrie obvykle zahrnuje data ze stavu zařízení. Například simulovaná místnost může odesílat informace o teplotě a vlhkosti každých 10 sekund. Poznamenejte si zástupné symboly v následujícím fragmentu kódu, které se automaticky nahradí hodnotami ze stavu zařízení:

```json
"Telemetry": [
    {
        "Interval": "00:00:10",
        "MessageTemplate":
            "{\"temperature\":${temperature},\"temperature_unit\":\"${temperature_unit}\",\"humidity\":\"${humidity}\"}",
        "MessageSchema": {
            "Name": "RoomComfort;v1",
            "Format": "JSON",
            "Fields": {
                "temperature": "double",
                "temperature_unit": "text",
                "humidity": "integer"
            }
        }
    }
],
```

Zástupné symboly používají speciální syntaxi **$ {Name}** , kde **Name** je klíč z objektu stavu zařízení vráceného **hlavní** funkcí jazyka JavaScript. Řetězce by měly být v uvozovkách, zatímco číslice by neměly.

#### <a name="message-schema"></a>Schéma zprávy

Každý typ zprávy musí mít dobře definované schéma. Schéma zprávy je také Publikováno do IoT Hub, takže aplikace back-end mohou znovu použít informace k interpretaci příchozí telemetrie.

Schéma podporuje formát JSON, který umožňuje snadnou analýzu, transformaci a analýzu v různých systémech a službách.

Pole uvedená ve schématu mohou mít následující typy:

* Objekt – serializovaný pomocí JSON
* Binární – serializovaný pomocí Base64
* Text
* Logická hodnota
* Integer
* dvojité
* DateTime

### <a name="supported-methods"></a>Podporované metody

Simulovaná zařízení mohou také reagovat na volání metody. v takovém případě spouštějí určitou logiku a poskytnou určitou odpověď. Podobně jako simulace je logika metody uložena v souboru JavaScriptu a může komunikovat se stavem zařízení. Například:

```json
"CloudToDeviceMethods": {
    "Start": {
        "Type": "JavaScript",
        "Path": "release-pressure.js"
    }
}
```

## <a name="create-a-device-definition-file"></a>Vytvoření souboru definice zařízení

V tomto průvodci uvidíte, jak vytvořit model zařízení pro pomocí dronů. Pomocí dronů náhodně dokončí počáteční sadu souřadnic měnících polohu a nadmořskou výšku.

Zkopírujte následující JSON do textového editoru a uložte ho jako **drone.jsna**.

### <a name="device-definition-json-example"></a>Ukázka JSON definice zařízení

```json
{
  "SchemaVersion": "1.0.0",
  "Id": "drone",
  "Version": "0.0.1",
  "Name": "Drone",
  "Description": "Simple drone.",
  "Protocol": "AMQP",
  "Simulation": {
    "InitialState": {
      "velocity": 0.0,
      "velocity_unit": "mm/sec",
      "acceleration": 0.0,
      "acceleration_unit": "mm/sec^2",
      "latitude": 47.476075,
      "longitude": -122.192026,
      "altitude": 0.0
    },
    "Interval": "00:00:05",
    "Scripts": [{
      "Type": "JavaScript",
      "Path": "drone-state.js"
    }]
  },
  "Properties": {
    "Type": "Drone",
    "Firmware": "1.0",
    "Model": "P-96"
  },
  "Tags": {
    "Owner": "Contoso"
  },
  "Telemetry": [{
      "Interval": "00:00:05",
      "MessageTemplate": "{\"velocity\":\"${velocity}\",\"acceleration\":\"${acceleration}\",\"position\":\"${latitude}|${longitude}|${altitude}\"}",
      "MessageSchema": {
        "Name": "drone-event-sensor;v1",
        "Format": "JSON",
        "Fields": {
          "velocity": "double",
          "velocity_unit": "text",
          "acceleration": "double",
          "acceleration_unit": "text",
          "latitude": "double",
          "longitude": "double",
          "altitude": "double"
        }
      }
    }
  ],
    "CloudToDeviceMethods": {
        "RecallDrone": {
            "Type": "JavaScript",
            "Path": "droneRecall-method.js"
        }
    }
}
```

## <a name="behavior-script-files"></a>Soubory skriptu chování

Kód v souboru skriptu chování přesune pomocí dronů. Skript změní zvýšení úrovně a umístění pomocí dronů tím, že bude manipulovat se stavem paměti zařízení.

Soubory JavaScriptu musí mít funkci **Main** , která přijímá dva parametry:

* Objekt **kontextu** , který obsahuje tři vlastnosti:
    * **currentTime** jako řetězec ve formátu **rrrr-mm-dd'T'HH: mm: sszzz**.
    * **deviceId**. Například **Simulovaná. výtah. 123**.
    * **deviceModel**. Například **výtah**.
* Objekt **stavu** , který je hodnotou vrácenou funkcí v předchozím volání. Tento stav zařízení je udržován službou simulace a používá se ke generování zpráv telemetrie.

Funkce **Main** vrátí nový stav zařízení. Například:

```JavaScript
function main(context, state) {

    // Use context if the simulation depends on
    // time or device details.
    // Execute some logic, updating 'state'

    return state;
}
```

## <a name="create-a-behavior-script-file"></a>Vytvoření souboru skriptu chování

Zkopírujte následující JavaScript do textového editoru a uložte ho jako **drone-state.js**.

### <a name="device-model-javascript-simulation-example"></a>Příklad simulace kódu JavaScriptu pro model zařízení

```JavaScript
"use strict";

// Position control
const DefaultLatitude = 47.476075;
const DefaultLongitude = -122.192026;
const DistanceVariation = 10;

// Altitude control
const DefaultAltitude = 0.0;
const AverageAltitude = 499.99;
const AltitudeVariation = 5;

// Velocity control
const AverageVelocity = 60.00;
const MinVelocity = 20.00;
const MaxVelocity = 120.00;
const VelocityVariation = 5;

// Acceleration control
const AverageAcceleration = 2.50;
const MinAcceleration = 0.01;
const MaxAcceleration = 9.99;
const AccelerationVariation = 1;

// Display control for position and other attributes
const GeoSpatialPrecision = 6;
const DecimalPrecision = 2;

// Default state
var state = {
    velocity: 0.0,
    velocity_unit: "mm/sec",
    acceleration: 0.0,
    acceleration_unit: "mm/sec^2",
    latitude: DefaultLatitude,
    longitude: DefaultLongitude,
    altitude: DefaultAltitude
};

// Default device properties
var properties = {};

/**
 * Restore the global state using data from the previous iteration.
 *
 * @param previousState device state from the previous iteration
 * @param previousProperties device properties from the previous iteration
 */
function restoreSimulation(previousState, previousProperties) {
    // If the previous state is null, force a default state
    if (previousState) {
        state = previousState;
    } else {
        log("Using default state");
    }

    if (previousProperties) {
        properties = previousProperties;
    } else {
        log("Using default properties");
    }
}

/**
 * Simple formula generating a random value around the average
 * in between min and max
 */
function vary(avg, percentage, min, max) {
    var value = avg * (1 + ((percentage / 100) * (2 * Math.random() - 1)));
    value = Math.max(value, min);
    value = Math.min(value, max);
    return value;
}

/**
 * Entry point function called by the simulation engine.
 * Returns updated simulation state.
 * Device property updates must call updateProperties() to persist.
 *
 * @param context             The context contains current time, device model and id
 * @param previousState       The device state since the last iteration
 * @param previousProperties  The device properties since the last iteration
 */
/*jslint unparam: true*/
function main(context, previousState, previousProperties) {

    // Restore the global state before generating the new telemetry, so that
    // the telemetry can apply changes using the previous function state.
    restoreSimulation(previousState, previousProperties);

    state.acceleration = vary(AverageAcceleration, AccelerationVariation, MinAcceleration, MaxAcceleration).toFixed(DecimalPrecision);
    state.velocity = vary(AverageVelocity, VelocityVariation, MinVelocity, MaxVelocity).toFixed(DecimalPrecision);

    // Use the last coordinates to calculate the next set with a given variation
    var coords = varylocation(Number(state.latitude), Number(state.longitude), DistanceVariation);
    state.latitude = Number(coords.latitude).toFixed(GeoSpatialPrecision);
    state.longitude = Number(coords.longitude).toFixed(GeoSpatialPrecision);

    // Fluctuate altitude between given variation constant by more or less
    state.altitude = vary(AverageAltitude, AltitudeVariation, AverageAltitude - AltitudeVariation, AverageAltitude + AltitudeVariation).toFixed(DecimalPrecision);

    return state;
}

/**
 * Generate a random geolocation at some distance (in miles)
 * from a given location
 */
function varylocation(latitude, longitude, distance) {
    // Convert to meters, use Earth radius, convert to radians
    var radians = (distance * 1609.344 / 6378137) * (180 / Math.PI);
    return {
        latitude: latitude + radians,
        longitude: longitude + radians / Math.cos(latitude * Math.PI / 180)
    };
}
```

## <a name="create-a-method-script-file"></a>Vytvoření souboru skriptu metody

Skripty metody jsou podobné skriptům chování. Definují chování zařízení při volání konkrétní metody cloudu na zařízení.

Skript pro odvolání pomocí dronů nastaví souřadnice pomocí dronů na pevný bod pro simulaci pomocí dronů vracející domovskou stránku.

Zkopírujte následující JavaScript do textového editoru a uložte ho jako **droneRecall-method.js**.

### <a name="device-model-javascript-simulation-example"></a>Příklad simulace kódu JavaScriptu pro model zařízení

```JavaScript
"use strict";

// Default state
var state = {
    velocity: 0.0,
    velocity_unit: "mm/sec",
    acceleration: 0.0,
    acceleration_unit: "mm/sec^2",
    latitude: 0.0,
    longitude: 0.0,
    altitude: 0.0
};

// Default device properties
var properties = {};

/**
 * Restore the global state using data from the previous iteration.
 *
 * @param previousState device state from the previous iteration
 * @param previousProperties device properties from the previous iteration
 */
function restoreSimulation(previousState, previousProperties) {
    // If the previous state is null, force a default state
    if (previousState) {
        state = previousState;
    } else {
        log("Using default state");
    }

    if (previousProperties) {
        properties = previousProperties;
    } else {
        log("Using default properties");
    }
}

/**
 * Entry point function called by the simulation engine.
 *
 * @param context        The context contains current time, device model and id, not used
 * @param previousState  The device state since the last iteration, not used
 * @param previousProperties  The device properties since the last iteration
 */
/*jslint unparam: true*/
function main(context, previousState, previousProperties) {

    // Restore the global device properties and the global state before
    // generating the new telemetry, so that the telemetry can apply changes
    // using the previous function state.
    restoreSimulation(previousState, previousProperties);

    //simulate the behavior of a drone when recalled
  state.latitude = 47.476075;
  state.longitude = -122.192026;
  return state;
}
```

## <a name="debugging-script-files"></a>Ladění souborů skriptu

I když nemůžete připojit ladicí program k souboru běžícího chování, je možné zapsat informace do protokolu služby pomocí funkce **log** . V případě chyb syntaxe překladač neuspěje a zapisuje informace o výjimce do protokolu.

Příklad protokolování:

```JavaScript
function main(context, state) {

    log("This message will appear in the service logs.");

    log(context.deviceId);

    if (typeof(state) !== "undefined" && state !== null) {
        log("Previous value: " + state.temperature);
    }

    // ...

    return updateState;
}
```

## <a name="deploy-an-advanced-device-model"></a>Nasazení pokročilého modelu zařízení

K nasazení pokročilého modelu zařízení nahrajete soubory instance simulace zařízení:

Na řádku nabídek vyberte **Modely zařízení**. Stránka **modely zařízení** obsahuje seznam modelů zařízení dostupných v této instanci simulace zařízení:

![Modely zařízení](media/iot-accelerators-device-simulation-advanced-device/devicemodelnav.png)

V pravém horním rohu stránky klikněte na **+ Přidat modely zařízení**:

![Přidání modelu zařízení](media/iot-accelerators-device-simulation-advanced-device/devicemodels.png)

Kliknutím na **Upřesnit** otevřete kartu Pokročilý model zařízení:

![Karta Upřesnit](media/iot-accelerators-device-simulation-advanced-device/advancedtab.png)

Klikněte na **Procházet** a vyberte soubory JSON a JavaScript, které jste vytvořili. Nezapomeňte vybrat všechny tři soubory. Pokud chybí nějaký soubor, ověření se nepovede:

![Procházet soubory](media/iot-accelerators-device-simulation-advanced-device/browse.png)

Pokud vaše soubory prošly ověřením, klikněte na **Uložit** a model zařízení je připravený k použití v simulaci. V opačném případě opravte všechny chyby a znovu odešlete soubory:

![Uložit](media/iot-accelerators-device-simulation-advanced-device/validated.png)

## <a name="next-steps"></a>Další kroky

V této příručce se dozvíte o souborech modelů zařízení používaných v simulaci zařízení a o tom, jak vytvořit pokročilý model zařízení. Dále můžete chtít prozkoumat, jak [použít Time Series Insights k vizualizaci telemetrie odeslané z akcelerátoru řešení pro simulaci zařízení](./iot-accelerators-device-simulation-time-series-insights.md).