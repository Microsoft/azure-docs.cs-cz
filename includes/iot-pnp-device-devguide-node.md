---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/19/2020
ms.openlocfilehash: c5846c42fe55dab7328e89a6d7bc5987b0937416
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2020
ms.locfileid: "95511418"
---
## <a name="model-id-announcement"></a>Oznámení ID modelu

Aby bylo možné oznámit ID modelu, musí ho zařízení zahrnout do informací o připojení:

```nodejs
const modelIdObject = { modelId: 'dtmi:com:example:Thermostat;1' };
const client = Client.fromConnectionString(deviceConnectionString, Protocol);
await client.setOptions(modelIdObject);
await client.open();
```

> [!TIP]
> Pro moduly a IoT Edge použijte `ModuleClient` místo `Client` .

## <a name="dps-payload"></a>Datová část DPS

Zařízení, která používají [službu Device Provisioning (DPS)](../articles/iot-dps/about-iot-dps.md) , můžou zahrnovat, `modelId` aby se během procesu zřizování používala tato datová část JSON.

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="implement-telemetry-properties-and-commands"></a>Implementace telemetrie, vlastností a příkazů

Jak je popsáno v tématu [pochopení komponent v modelech IoT technologie Plug and Play](../articles/iot-pnp/concepts-components.md), musí tvůrci zařízení rozhodnout, jestli chtějí používat komponenty k popisu jejich zařízení. Při používání komponent musí zařízení splňovat pravidla popsaná v této části.

### <a name="telemetry"></a>Telemetrie

Výchozí součást nevyžaduje žádnou zvláštní vlastnost.

Při použití vnořených komponent musí zařízení nastavit vlastnost zprávy s názvem komponenty:

```nodejs
async function sendTelemetry(deviceClient, data, index, componentName) {
  const msg = new Message(data);
  if (!!(componentName)) {
    msg.properties.add(messageSubjectProperty, componentName);
  }
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

### <a name="read-only-properties"></a>Vlastnosti jen pro čtení

Vytváření sestav vlastnosti z výchozí komponenty nevyžaduje žádnou speciální konstrukci:

```nodejs
const createReportPropPatch = (propertiesToReport) => {
  let patch;
  patch = { };
  patch = propertiesToReport;
  return patch;
};

deviceTwin = await client.getTwin();
patchThermostat = createReportPropPatch({
  maxTempSinceLastReboot: 38.7
});

deviceTwin.properties.reported.update(patchThermostat, function (err) {
  if (err) throw err;
});
```

Nevlákenná zpráva zařízení se aktualizuje o další hlášené vlastnosti:

```json
{
  "reported": {
      "maxTempSinceLastReboot" : 38.7
  }
}
```

Při použití vnořených komponent musí být v rámci názvu komponenty vytvořeny vlastnosti.:

```nodejs
helperCreateReportedPropertiesPatch = (propertiesToReport, componentName) => {
  let patch;
  if (!!(componentName)) {
    patch = { };
    propertiesToReport.__t = 'c';
    patch[componentName] = propertiesToReport;
  } else {
    patch = { };
    patch = propertiesToReport;
  }
  return patch;
};

deviceTwin = await client.getTwin();
patchThermostat1Info = helperCreateReportedPropertiesPatch({
  maxTempSinceLastReboot: 38.7,
}, 'thermostat1');

deviceTwin.properties.reported.update(patchThermostat1Info, function (err) {
  if (err) throw err;
});
```

Nevlákenná zpráva zařízení se aktualizuje o další hlášené vlastnosti:

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTempSinceLastReboot" : 38.7
     } 
  }
}
```

### <a name="writable-properties"></a>Vlastnosti s možností zápisu

Tyto vlastnosti může nastavit zařízení nebo aktualizovat řešení. Pokud řešení aktualizuje vlastnost, klient obdrží oznámení jako zpětné volání v `Client` nebo `ModuleClient` . Aby bylo možné postupovat podle konvencí technologie Plug and Play IoT, zařízení musí informovat službu o tom, že byla vlastnost úspěšně přijata.

#### <a name="report-a-writable-property"></a>Nahlásit vlastnost s možností zápisu

Když zařízení ohlásí vlastnost s možností zápisu, musí zahrnovat `ack` hodnoty definované v konvencích.

Chcete-li nahlásit vlastnost s možností zápisu z výchozí komponenty:

```nodejs
patch = {
  targetTemperature:
    {
      'value': 23.2,
      'ac': 200,  // using HTTP status codes
      'ad': 'reported default value',
      'av': 0  // not read from a desired property
    }
};
deviceTwin.properties.reported.update(patch, function (err) {
  if (err) throw err;
});
```

Nevlákenná zpráva zařízení se aktualizuje o další hlášené vlastnosti:

```json
{
  "reported": {
    "targetTemperature": {
      "value": 23.2,
      "ac": 200,
      "av": 0,
      "ad": "reported default value"
    }
  }
}
```

Chcete-li nahlásit vlastnost s možností zápisu z vnořené komponenty, musí mít vlákna značku:

```nodejs
patch = {
  thermostat1: {
    '__t' : 'c',
    targetTemperature: {
      'value': 23.2,
      'ac': 200,  // using HTTP status codes
      'ad': 'reported default value',
      'av': 0  // not read from a desired property
    }
  }
};
deviceTwin.properties.reported.update(patch, function (err) {
  if (err) throw err;
});
```

Nevlákenná zpráva zařízení se aktualizuje o další hlášené vlastnosti:

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 0,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>Přihlášení k odběru požadovaných aktualizací vlastností

Služby mohou aktualizovat požadované vlastnosti, které aktivují oznámení na připojených zařízeních. Toto oznámení zahrnuje aktualizované požadované vlastnosti, včetně čísla verze identifikující aktualizaci. Zařízení musí odpovídat stejné `ack` zprávě jako hlášené vlastnosti.

Výchozí komponenta vidí jedinou vlastnost a vytvoří hlášené `ack` s obdrženou verzí:

```nodejs
const propertyUpdateHandler = (deviceTwin, propertyName, reportedValue, desiredValue, version) => {
  const patch = createReportPropPatch(
    { [propertyName]:
      {
        'value': desiredValue,
        'ac': 200,
        'ad': 'Successfully executed patch for ' + propertyName,
        'av': version
      }
    });
  updateComponentReportedProperties(deviceTwin, patch);
};

desiredPropertyPatchHandler = (deviceTwin) => {
  deviceTwin.on('properties.desired', (delta) => {
    const versionProperty = delta.$version;

    Object.entries(delta).forEach(([propertyName, propertyValue]) => {
      if (propertyName !== '$version') {
        propertyUpdateHandler(deviceTwin, propertyName, null, propertyValue, versionProperty);
      }
    });
  });
};
```

Doplňování zařízení zobrazuje vlastnost v požadovaných a nahlášených částech:

```json
{
  "desired" : {
    "targetTemperature": 23.2,
    "$version" : 3
  },
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

Vnořená komponenta přijímá požadované vlastnosti zabalené s názvem komponenty a měla by nahlásit zpět `ack` ohlášenou vlastnost:

```nodejs
const desiredPropertyPatchListener = (deviceTwin, componentNames) => {
  deviceTwin.on('properties.desired', (delta) => {
    Object.entries(delta).forEach(([key, values]) => {
      const version = delta.$version;
      if (!!(componentNames) && componentNames.includes(key)) { // then it is a component we are expecting
        const componentName = key;
        const patchForComponents = { [componentName]: {} };
        Object.entries(values).forEach(([propertyName, propertyValue]) => {
          if (propertyName !== '__t' && propertyName !== '$version') {
            const propertyContent = { value: propertyValue };
            propertyContent.ac = 200;
            propertyContent.ad = 'Successfully executed patch';
            propertyContent.av = version;
            patchForComponents[componentName][propertyName] = propertyContent;
          }
        });
        updateComponentReportedProperties(deviceTwin, patchForComponents, componentName);
      }
      else if  (key !== '$version') { // individual property for root
        const patchForRoot = { };
        const propertyContent = { value: values };
        propertyContent.ac = 200;
        propertyContent.ad = 'Successfully executed patch';
        propertyContent.av = version;
        patchForRoot[key] = propertyContent;
        updateComponentReportedProperties(deviceTwin, patchForRoot, null);
      }
    });
  });
};
```

Vyplňování zařízení pro součásti zobrazuje požadované a hlášené oddíly následujícím způsobem:

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

### <a name="commands"></a>Příkazy

Výchozí komponenta přijímá název příkazu, který byl vyvolán službou.

Vnořená komponenta přijímá název příkazu s předponou názvu komponenty a `*` oddělovače.

```nodejs
const commandHandler = async (request, response) => {
  switch (request.methodName) {
  
  // ...

  case 'thermostat1*reboot': {
    await response.send(200, 'reboot response');
    break;
  }
  default:
    await response.send(404, 'unknown method');
    break;
  }
};

client.onDeviceMethod('thermostat1*reboot', commandHandler);
```

#### <a name="request-and-response-payloads"></a>Datové části žádosti a odpovědi

Příkazy používají typy k definování svých datových částí požadavků a odpovědí. Zařízení musí deserializovat příchozí vstupní parametr a serializovat odpověď. Následující příklad ukazuje, jak implementovat příkaz se složitými typy definovanými v datových vytíženích:

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

Následující fragmenty kódu ukazují, jak zařízení implementuje tuto definici příkazu, včetně typů používaných pro povolení serializace a deserializace:

```nodejs
class TemperatureSensor {

  // ...

  getMaxMinReportObject() {
    return {
      maxTemp: this.maxTemp,
      minTemp: this.minTemp,
      avgTemp: this.cumulativeTemperature / this.numberOfTemperatureReadings,
      endTime: (new Date(Date.now())).toISOString(),
      startTime: this.startTime
    };
  }
}

// ...

const deviceTemperatureSensor = new TemperatureSensor();

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case commandMaxMinReport: {
    console.log('MaxMinReport ' + request.payload);
    await response.send(200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await response.send(404, 'unknown method');
    break;
  }
};
```

> [!Tip]
> Název žádosti a odpovědi nejsou k dispozici v serializovaných datových vytíženích přenášených přes tento kabel.
