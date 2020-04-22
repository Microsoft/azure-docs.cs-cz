---
title: Vytvoření pokročilého modelu simulovaných zařízení – Azure| Dokumenty společnosti Microsoft
description: V tomto návodu se dozvíte, jak vytvořit pokročilý model zařízení pro použití s akcelerátorem řešení Simulace zařízení.
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
ms.openlocfilehash: c568dddcbbf57ebd6ed5906bb83af01a84dafa41
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683827"
---
# <a name="create-an-advanced-device-model"></a>Vytvoření pokročilého modelu zařízení

Tento návod popisuje soubory JSON a JavaScript, které definují vlastní model zařízení. Článek obsahuje některé ukázkové soubory definice modelu zařízení a ukazuje, jak je nahrát do instance Simulace zařízení. Můžete vytvořit pokročilé modely zařízení pro simulaci realističtější chování zařízení pro vaše testování.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Chcete-li postupovat podle pokynů v tomto návodu, potřebujete nasazenou instanci simulace zařízení ve vašem předplatném Azure.

Pokud jste Simulaci zařízení ještě nenasadili, měli byste dokončit rychlý start [Nasazení a spuštění simulace zařízení IoT v Azure](quickstart-device-simulation-deploy.md).

### <a name="open-device-simulation"></a>Otevření Simulace zařízení

Pokud chcete spustit Simulaci zařízení v prohlížeči, nejprve přejděte na web [Akcelerátory řešení Microsoft Azure IoT](https://www.azureiotsolutions.com).

Může se zobrazit výzva k přihlášení pomocí vašich přihlašovacích údajů k předplatnému Azure.

Potom klikněte na **tlačítko Spustit** na dlaždici pro simulaci zařízení, kterou jste nasadili v [nasazení, a spusťte simulaci zařízení IoT v rychlém](quickstart-device-simulation-deploy.md) startu Azure.

## <a name="device-models"></a>Modely zařízení

Každé simulované zařízení patří k určitému modelu zařízení, který definuje chování simulace. Toto chování zahrnuje, jak často odesílat telemetrie, jaký druh zpráv k odeslání a podporované metody.

Model zařízení definujete pomocí definičního souboru zařízení JSON a sady souborů JavaScriptu. Tyto soubory JavaScriptu definují chování simulace, jako je náhodná telemetrie a logika metody.

Typický model zařízení má:

* Jeden soubor JSON pro každý model zařízení (například elevator.json).
* Jeden soubor skriptu chování JavaScriptu pro každý model zařízení (například elevator-state.js)
* Jeden soubor skriptu metody JavaScript pro každou metodu zařízení (například elevator-go-down.js)

> [!NOTE]
> Metody nedefinují všechny modely zařízení. Proto model zařízení může nebo nemusí mít skripty metody. Všechny modely zařízení však musí mít skript chování.

## <a name="device-definition-file"></a>Soubor definice zařízení

Každý soubor definice zařízení obsahuje podrobnosti o simulovaném modelu zařízení, včetně následujících informací:

* Název modelu zařízení: řetězec.
* Protokol: AMQP | MQTT | http.
* Počáteční stav zařízení.
* Jak často aktualizovat stav zařízení.
* Který soubor JavaScriptu použít k aktualizaci stavu zařízení.
* Seznam telemetrických zpráv k odeslání, každý s určitou frekvencí.
* Schéma telemetrických zpráv, které používá back-endaplikace k analýzě přijaté telemetrie.
* Seznam podporovaných metod a soubor javascriptu, který se má použít k simulaci jednotlivých metod.

### <a name="file-schema"></a>Schéma souborů

Verze schématu je vždy "1.0.0" a je specifická pro formát tohoto souboru:

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

Zařízení IoT se mohou připojovat pomocí různých protokolů. Simulace umožňuje použít buď **AMQP**, **MQTT**nebo **HTTP**:

```json
"Protocol": "AMQP"
```

### <a name="simulated-device-state"></a>Simulovaný stav zařízení

Každé simulované zařízení má vnitřní stav, který musí být definován. Stav také definuje vlastnosti, které mohou být hlášeny v telemetrii. Chladič může mít například počáteční stav, například:

```json
"InitialState": {
    "temperature": 50,
    "humidity": 40
},
```

Pohybující se zařízení s několika senzory může mít více vlastností, například:

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

Stav zařízení je udržován v paměti simulační službou a je poskytován jako vstup do funkce JavaScript. Funkce JavaScript uměl rozhodnout:

* Chcete-li ignorovat stav a generovat některá náhodná data.
* Chcete-li aktualizovat stav zařízení nějakým realistickým způsobem pro daný scénář.

Funkce, která generuje stav také přijímá jako vstup:

* ID zařízení.
* Model zařízení.
* Aktuální čas. Tato hodnota umožňuje generovat různá data podle zařízení a podle času.

### <a name="generating-telemetry-messages"></a>Generování telemetrických zpráv

Simulační služba může odeslat několik typů telemetrie pro každé zařízení. Telemetrie obvykle zahrnuje data ze stavu zařízení. Simulovaná místnost může například každých 10 sekund odesílat informace o teplotě a vlhkosti. Všimněte si zástupných symbolů v následujícím úryvku, které jsou automaticky nahrazeny hodnotami ze stavu zařízení:

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

Zástupné symboly používají speciální syntaxi **${NAME},** kde **NAME** je klíč z objektu stavu zařízení vráceného **hlavní** funkcí JavaScriptu. Řetězce by měly být citovány, zatímco čísla by neměla.

#### <a name="message-schema"></a>Schéma zprávy

Každý typ zprávy musí mít dobře definované schéma. Schéma zpráv je také publikováno do služby IoT Hub, takže back-endové aplikace mohou znovu použít informace k interpretaci příchozí telemetrie.

Schéma podporuje formát JSON, který umožňuje snadnou analýzu, transformaci a analýzu v několika systémech a službách.

Pole uvedená ve schématu mohou být následujících typů:

* Objekt - serializován pomocí JSON
* Binární - serializovánpomocí base64
* Text
* Logická hodnota
* Integer
* Double
* DateTime

### <a name="supported-methods"></a>Podporované metody

Simulovaná zařízení mohou také reagovat na volání metod, v takovém případě spustí určitou logiku a poskytnou určitou odpověď. Podobně jako simulace je logika metody uložena v souboru JavaScript a může pracovat se stavem zařízení. Příklad:

```json
"CloudToDeviceMethods": {
    "Start": {
        "Type": "JavaScript",
        "Path": "release-pressure.js"
    }
}
```

## <a name="create-a-device-definition-file"></a>Vytvoření definičního souboru zařízení

V tomto návodu uvidíte, jak vytvořit model zařízení pro drone. Dron bude náhodně létat kolem počáteční sady souřadnic, které mění polohu a nadmořskou výšku.

Zkopírujte následující JSON do textového editoru a uložte jej jako **drone.json**.

### <a name="device-definition-json-example"></a>Příklad json definice zařízení

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

## <a name="behavior-script-files"></a>Soubory skriptů chování

Kód v souboru skriptu chování přesune drone. Skript mění výšku a umístění drone manipulací zařízení v paměťovém stavu.

Soubory JavaScriptu musí mít **hlavní** funkci, která přijímá dva parametry:

* **Objekt kontextu,** který obsahuje tři vlastnosti:
    * **currentTime** jako řetězec s formátem **yyyy-MM-dd'T'HH:mm:sszzz**.
    * **deviceId**. Například **Simulated.Elevator.123**.
    * **deviceModel**. Například **Výtah**.
* Objekt **stavu,** který je hodnota vrácená funkcí v předchozím volání. Tento stav zařízení je udržován a slouží ke generování telemetrických zpráv.

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

Zkopírujte následující JavaScript do textového editoru a uložte jej jako **drone-state.js**.

### <a name="device-model-javascript-simulation-example"></a>Příklad simulace javascriptového modelu zařízení

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

Skripty metod jsou podobné skriptům chování. Definují chování zařízení při volání konkrétní metody cloud to device.

Skript pro stažení dronu nastavuje souřadnice dronu do pevného bodu, aby simuloval návrat dronu domů.

Zkopírujte následující JavaScript do textového editoru a uložte jej jako **droneRecall-method.js**.

### <a name="device-model-javascript-simulation-example"></a>Příklad simulace javascriptového modelu zařízení

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

## <a name="debugging-script-files"></a>Ladění souborů skriptů

I když nelze připojit ladicí program ke spuštěnému souboru chování, je možné zapisovat informace do protokolu služby pomocí funkce **protokolu.** V případě chyb syntaxe se překladač nezdaří a zapíše informace o výjimce protokolu.

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

## <a name="deploy-an-advanced-device-model"></a>Nasazení rozšířeného modelu zařízení

Chcete-li nasadit pokročilý model zařízení, nahrajte soubory, které instanci simulace zařízení:

Na řádku nabídek vyberte **Modely zařízení**. Stránka **Modely zařízení** obsahuje seznam modelů zařízení dostupných v této instanci simulace zařízení:

![Modely zařízení](media/iot-accelerators-device-simulation-advanced-device/devicemodelnav.png)

V pravém horním rohu stránky klikněte na **+ Přidat modely zařízení**:

![Přidání modelu zařízení](media/iot-accelerators-device-simulation-advanced-device/devicemodels.png)

Kliknutím na **Upřesnit** otevřete kartu rozšířeného modelu zařízení:

![Karta Upřesnit](media/iot-accelerators-device-simulation-advanced-device/advancedtab.png)

Klikněte na **Procházet** a vyberte soubory JSON a JavaScript, které jste vytvořili. Nezapomeňte vybrat všechny tři soubory. Pokud některý soubor chybí, ověření se nezdaří:

![Procházet soubory](media/iot-accelerators-device-simulation-advanced-device/browse.png)

Pokud soubory projdou ověřením, klikněte na **Uložit** a model zařízení je připraven k použití v simulaci. V opačném případě opravte všechny chyby a znovu nahrajte soubory:

![Uložit](media/iot-accelerators-device-simulation-advanced-device/validated.png)

## <a name="next-steps"></a>Další kroky

V tomto návodu jste se dozvěděli o souborech modelů zařízení používaných v simulaci zařízení a o tom, jak vytvořit pokročilý model zařízení. Dále můžete chtít prozkoumat, jak [použít Přehledy časových řad k vizualizaci telemetrie odeslané z akcelerátoru řešení Simulace zařízení](https://docs.microsoft.com/azure/iot-accelerators/iot-accelerators-device-simulation-time-series-insights).
