---
title: Vytvoření modelu pokročilé simulovaného zařízení – Azure | Dokumentace Microsoftu
description: V této příručce s postupy se dozvíte, jak vytvořit model pokročilé zařízení pro použití s akcelerátor řešení simulaci zařízení.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 10/25/2018
ms.author: troyhop
ms.openlocfilehash: 7e680e3cd8a3d7056141814c5e7d4539b72073b4
ms.sourcegitcommit: 3dcb1a3993e51963954194ba2a5e42260d0be258
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2018
ms.locfileid: "50757204"
---
# <a name="create-an-advanced-device-model"></a>Vytvoření modelu pokročilé zařízení

Tato příručka popisuje soubory JSON a JavaScript, které definují model vlastní zařízení. Tento článek obsahuje některé ukázkové soubory definice modelu zařízení a ukazuje, jak jejich nahrávání do vaší instance simulace zařízení. Můžete vytvářet modely pokročilé zařízení pro simulaci realističtější chování jednotlivých zařízení pro testování.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Postupujte podle kroků v této příručce s postupy, je třeba nasazené instance simulace zařízení ve vašem předplatném Azure.

Pokud jste nenasadili simulace zařízení ještě by se měl Dokončit [nasadit a spustit simulaci zařízení IoT v Azure](quickstart-device-simulation-deploy.md) rychlý start.

### <a name="open-device-simulation"></a>Simulace zařízení otevřít

Simulace zařízení spustit v prohlížeči, nejprve přejděte do [akcelerátory řešení IoT Microsoft Azure](https://www.azureiotsolutions.com).

Může se zobrazit výzva k přihlášení pomocí vašich přihlašovacích údajů k předplatnému Azure.

Pak klikněte na tlačítko **spuštění** na dlaždici pro simulaci zařízení, které jste nasadili v [nasadit a spustit simulaci zařízení IoT v Azure](quickstart-device-simulation-deploy.md) rychlý start.

## <a name="device-models"></a>Modely zařízení

Každé simulované zařízení patří do konkrétního zařízení model, který definuje chování simulace. Toto chování zahrnuje jak často k odesílání telemetrických dat, jaký druh zprávy k odeslání a podporovaných metod.

Můžete definovat model zařízení pomocí souboru definice JSON zařízení a sadu souborů JavaScriptu. Tyto soubory jazyka JavaScript definuje chování simulace například náhodné telemetrie a logiku metody.

Model zařízení typické obsahuje:

* Jeden soubor JSON pro každý model zařízení (například elevator.json).
* Jeden soubor skriptu chování jazyka JavaScript pro každý model zařízení (například hodnocení state.js)
* Jeden soubor skriptu metoda jazyka JavaScript pro každou metodu zařízení (například hodnocení go-down.js)

> [!NOTE]
> Ne všechny modely zařízení definovat metody. Model zařízení proto může nebo nemusí mít metoda skripty. Všechny modely zařízení ale musí mít skript chování.

## <a name="device-definition-file"></a>Soubor definice zařízení

Každý soubor s definicí zařízení obsahuje podrobné informace o modelu simulované zařízení, včetně následujících informací:

* Název modelu zařízení: řetězec.
* Protokol: AMQP | MQTT | HTTP.
* Počáteční stav zařízení.
* Jak často se má aktualizovat stav zařízení.
* Jaký soubor JavaScript slouží k aktualizaci stavu zařízení.
* Seznam telemetrické zprávy k odeslání, každý s konkrétní frekvencí.
* Schéma telemetrických zpráv, použitá k analýze telemetrických dat přijatých back endové aplikace.
* Seznam podporovaných metod a soubor jazyka JavaScript určený pro simulaci každé metody.

### <a name="file-schema"></a>Schéma souboru reklamy

Verze schématu je vždy "1.0.0" a je specifické pro formát tohoto souboru:

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

Zařízení IoT můžete připojit pomocí různých protokolů. Simulace vám umožní používat buď **AMQP**, **MQTT**, nebo **HTTP**:

```json
"Protocol": "AMQP"
```

### <a name="simulated-device-state"></a>Stav simulovaného zařízení

Každé simulované zařízení má vnitřní stav, který musí být definován. Stav také definuje vlastnosti, které mohou být zaznamenány v telemetrii. Chladič například může mít počáteční stav jako:

```json
"InitialState": {
    "temperature": 50,
    "humidity": 40
},
```

Přesunutí zařízení s několika senzorů může mít více vlastností, například:

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

Stav zařízení je uložený v paměti službou simulace a doplníte jako vstup do funkce jazyka JavaScript. Funkce JavaScript, která může rozhodnout:

* Chcete-li ignorovat stav a generovat náhodná data.
* Chcete-li aktualizovat stav zařízení nějakým způsobem realistické v daném scénáři.

Funkce, která generuje stav také přijímá jako vstup:

* ID zařízení.
* Model zařízení.
* Aktuální čas. Tato hodnota umožňuje generovat různé datové podle zařízení a podle času.

### <a name="generating-telemetry-messages"></a>Generování telemetrických zpráv

Simulace service můžete odeslat několik typů telemetrie pro každé zařízení. Telemetrie obvykle zahrnuje data ze stavu zařízení. Například Simulovaná místnosti může odesílat informace o teplotě a vlhkosti každých 10 sekund. Poznámka: zástupné symboly v následujícím fragmentu kódu, které jsou automaticky nahradit hodnotami ze stavu zařízení:

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

Zástupné symboly zvláštní syntaxí **${NAME}** kde **název** klíčem z objektu stavu zařízení, vrácený kód jazyka JavaScript je **hlavní** funkce. Řetězce by měla používat uvozovky, zatímco čísla by neměl.

#### <a name="message-schema"></a>Schéma zprávy

Každý typ zprávy musí být dobře definované schéma. Schéma zprávy, jsou publikované do služby IoT Hub, back endovým aplikacím můžete použít informace pro interpretaci příchozí telemetrická data.

Schéma podporuje formát JSON, který umožňuje snadno analýzu, transformaci a analýzy, napříč několika služeb a systémů.

Polí uvedených ve schématu může být z následujících typů:

* Objekt - serializovat pomocí JSON
* Binární – serializovanou ve formátu base64
* Text
* Logická hodnota
* Integer
* Double
* DateTime

### <a name="supported-methods"></a>Podporované metody

Simulovaná zařízení mohou také reagovat na volání metody, v takovém případě provést nějaké logiky a poskytují některé odpovědi. Podobně jako na simulaci metoda logiky je uložen v souboru jazyka JavaScript a může komunikovat s stavu zařízení. Příklad:

```json
"CloudToDeviceMethods": {
    "Start": {
        "Type": "JavaScript",
        "Path": "release-pressure.js"
    }
}
```

## <a name="create-a-device-definition-file"></a>Vytvořit soubor definice zařízení

V tomto postupy-k-průvodci zjistit, jak vytvořit model zařízení pro dron. Dronu bude náhodně plout po počáteční Sada souřadnic Změna umístění a výšku.

Zkopírujte následující kód JSON do textového editoru a uložte ho jako **drone.json**.

### <a name="device-definition-json-example"></a>Příklad JSON definice zařízení

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

Kód v souboru skriptu chování přesune dronu. Skript mění zvýšení úrovně oprávnění a umístění dronu manipulací ve stavu paměti zařízení.

Musí mít soubory jazyka JavaScript **hlavní** funkce, která přijímá dva parametry:

* A **kontextu** objekt, který obsahuje tři vlastnosti:
    * **aktualnicas** jako řetězec s formátem **rrrr MM-dd'T'HH:mm:sszzz**.
    * **ID zařízení**. Například **Simulated.Elevator.123**.
    * **deviceModel**. Například **Elevator**.
* A **stavu** objekt, který je hodnota vrácená funkcí v předchozí volání. Tento stav zařízení je spravovaná službou simulace a sloužící ke generování telemetrie zprávy.

**Hlavní** funkce vrátí nový stav zařízení. Příklad:

```JavaScript
function main(context, state) {

    // Use context if the simulation depends on
    // time or device details.
    // Execute some logic, updating 'state'

    return state;
}
```

## <a name="create-a-behavior-script-file"></a>Vytvoření souboru skriptu chování

Zkopírujte následující jazyka JavaScript do textového editoru a uložte ho jako **dronu state.js**.

### <a name="device-model-javascript-simulation-example"></a>Příklad v jazyce JavaScript simulace zařízení modelu

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

## <a name="create-a-method-script-file"></a>Vytvoření souboru skriptu – metoda

Metoda skripty jsou podobné chování skripty. Definují chování zařízení při volání metody zařízení na konkrétní cloud.

Skript pomocí dronů spojené s vracením nastaví souřadnice dronu pevnému bodu pro simulaci dronu vrácení Domů.

Zkopírujte následující jazyka JavaScript do textového editoru a uložte ho jako **droneRecall method.js**.

### <a name="device-model-javascript-simulation-example"></a>Příklad v jazyce JavaScript simulace zařízení modelu

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

## <a name="debugging-script-files"></a>Ladění skriptů

Při spouštění souboru chování nelze připojit ladicí program, je možné zapsat informace o službu pomocí protokolu **protokolu** funkce. Chyby syntaxe interpretu selže a zapíše do protokolu informace o výjimce.

Příklad protokolování:

```JavaScript
function main(context, state) {

    log("This message will appear in the service logs.");

    log(context.deviceId);

    if (typeof(state) !== "undefined" && state !== null) {
        log("Previous value: " + state.temperature);
    }

    // ...

    return state;
}
```

## <a name="deploy-an-advanced-device-model"></a>Nasazení modelu pokročilé zařízení

Nasazení modelu pokročilé zařízení, můžete odeslat soubory instanci simulace zařízení:

Vyberte **modely zařízení** v panelu nabídek. **Modely zařízení** stránce uvedeny modely zařízení, která je k dispozici v tomto případě simulace zařízení:

![Modely zařízení](media/iot-accelerators-device-simulation-advanced-device/devicemodelnav.png)

Klikněte na tlačítko **+ přidat zařízení modely** v pravém horním rohu stránky:

![Přidat model zařízení](media/iot-accelerators-device-simulation-advanced-device/devicemodels.png)

Klikněte na tlačítko **Upřesnit** otevřete kartu Upřesnit zařízení modelu:

![Karta Upřesnit](media/iot-accelerators-device-simulation-advanced-device/advancedtab.png)

Klikněte na tlačítko **Procházet** a vyberte jste vytvořili soubory JSON a JavaScript. Nezapomeňte vybrat všechny tři soubory. Pokud jakýkoli jeden soubor chybí, ověření se nezdaří:

![Procházet soubory](media/iot-accelerators-device-simulation-advanced-device/browse.png)

Pokud vaše soubory projít ověřením, klikněte na tlačítko **Uložit** a modelu zařízení je připravená k použití v simulaci. V opačném případě opravte všechny chyby a znovu nahrávat soubory:

![Uložení](media/iot-accelerators-device-simulation-advanced-device/validated.png)

## <a name="next-steps"></a>Další postup

V této příručce s postupy jste se dozvěděli o soubory modelu zařízení používaných pro simulaci zařízení a jak vytvořit model pokročilé zařízení. V dalším kroku můžete chtít prozkoumat jak [použití Time Series Insights k vizualizaci telemetrická data odesílaná ze zařízení simulace akcelerátor řešení](about-iot-accelerators.md).