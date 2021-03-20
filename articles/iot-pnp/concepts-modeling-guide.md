---
title: Vysvětlení modelů zařízení IoT technologie Plug and Play | Microsoft Docs
description: Seznamte se s modulem DTDL (Digital neing Definition Language) pro zařízení IoT technologie Plug and Play. Článek popisuje primitivní a komplexní DataTypes, opakované použití vzorů, které používají komponenty a dědičnost a sémantické typy. Tento článek poskytuje pokyny k výběru identifikátoru zdvojeného modelu zařízení a podpora nástrojů pro vytváření modelů.
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2021
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: a3389408b0942875aa7d760f1c514b995e381f9c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104609063"
---
# <a name="iot-plug-and-play-modeling-guide"></a>Průvodce modelováním IoT technologie Plug and Play

V jádru IoT technologie Plug and Play je _model_ zařízení, který popisuje možnosti zařízení pro aplikaci s podporou technologie Plug and Play IoT. Tento model je strukturovaný jako sada rozhraní definujících:

- _Vlastnosti_ , které reprezentují stav jen pro čtení nebo zapisovatelného stavu zařízení nebo jiné entity. Například sériové číslo zařízení může být vlastnost jen pro čtení a cílová teplota na termostatovi může být vlastnost s možností zápisu.
- Pole _telemetrie_ , která definují data generovaná zařízením, ať už data jsou pravidelným proudem čtených senzorů, příležitostné chyby nebo informační zprávou.
- _Příkazy_ , které popisují funkci nebo operaci, které lze provést na zařízení. Například příkaz může restartovat bránu nebo pořídit obrázek pomocí vzdálené kamery.

Další informace o tom, jak IoT technologie Plug and Play používá modely zařízení, najdete v tématu [Příručka pro vývojáře pro zařízení iot technologie Plug and Play](concepts-developer-guide-device.md) a [Příručka pro vývojáře služby IoT technologie Plug and Play](concepts-developer-guide-service.md).

Chcete-li definovat model, použijte DTDL (Digital vlákna Definition Language). DTDL používá variantu JSON nazvanou [JSON-ld](https://json-ld.org/). Následující fragment kódu ukazuje model pro termostatické zařízení, které:

- Má jedinečné ID modelu: `dtmi:com:example:Thermostat;1` .
- Odesílá telemetrie na teplotu.
- Má vlastnost s možností zápisu, která nastaví cílovou teplotu.
- Má vlastnost jen pro čtení, která oznamuje maximální teplotu od posledního restartování.
- Odpoví na příkaz, který požaduje maximální, minimální a průměrné teploty v časovém intervalu.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "targetTemperature",
      "schema": "double",
      "displayName": "Target Temperature",
      "description": "Allows to remotely specify the desired target temperature.",
      "unit": "degreeCelsius",
      "writable": true
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "maxTempSinceLastReboot",
      "schema": "double",
      "unit": "degreeCelsius",
      "displayName": "Max temperature since last reboot.",
      "description": "Returns the max temperature since last device reboot."
    },
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
        "name": "tempReport",
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
              "name": "avgTemp",
              "displayName": "Average Temperature",
              "schema": "double"
            },
            {
              "name": "startTime",
              "displayName": "Start Time",
              "schema": "dateTime"
            },
            {
              "name": "endTime",
              "displayName": "End Time",
              "schema": "dateTime"
            }
          ]
        }
      }
    }
  ]
}
```

Termostatový model má jedno rozhraní. Pozdější příklady v tomto článku ukazují složitější modely, které používají komponenty a dědičnost.

Tento článek popisuje, jak navrhnout a vytvořit vlastní modely a popisuje témata, jako jsou datové typy, struktura modelů a nástroje.

Další informace najdete v článku specifikace [digitálního vlákna definice jazyka v2](https://github.com/Azure/opendigitaltwins-dtdl) .

## <a name="model-structure"></a>Struktura modelu

Vlastnosti, telemetrie a příkazy jsou seskupeny do rozhraní. Tato část popisuje, jak můžete pomocí rozhraní popsat jednoduché a komplexní modely pomocí komponent a dědičnosti.

### <a name="model-ids"></a>ID modelu

Každé rozhraní má jedinečný identifikátor digitálního vlákna (DTMI). Složité modely používají DTMIs k identifikaci komponent. Aplikace mohou používat DTMIs, která zařízení odesílají k vyhledání definicí modelů v úložišti.

DTMIs by mělo následovat po konvenci pojmenování vyžadované [úložištěm modelu IoT technologie Plug and Play](https://github.com/Azure/iot-plugandplay-models):

- Předpona DTMI je `dtmi:` .
- Přípona DTMI je číslo verze modelu, jako je například `;2` .
- Tělo DTMIu se mapuje na složku a soubor v úložišti modelu, kde je model uložený. Číslo verze je součástí názvu souboru.

Například model identifikovaný DTMI `dtmi:com:Example:Thermostat;2` je uložen v souboru *DTMI/com/example/thermostat-2.jsv* souboru.

Následující fragment kódu ukazuje obrys definice rozhraní s jeho jedinečným DTMI:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;2",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    ...
  ]
}
```

### <a name="no-components"></a>Žádné součásti

Jednoduchý model, jako je například termostat uvedený výše, nepoužívá vložené nebo kaskádové komponenty. Telemetrie, vlastnosti a příkazy jsou definovány v `contents` uzlu rozhraní.

Následující příklad ukazuje část jednoduchého modelu, který nepoužívá komponenty:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
...
```

Nástroje jako Azure IoT Explorer a IoT Central Návrhář šablon zařízení označí samostatná rozhraní, jako je například termostat jako _výchozí komponenta_.

Následující snímek obrazovky ukazuje, jak se model zobrazuje v nástroji Azure IoT Explorer:

:::image type="content" source="media/concepts-modeling-guide/default-component.png" alt-text="Výchozí součást v Azure IoT Exploreru":::

Následující snímek obrazovky ukazuje, jak se model zobrazuje jako výchozí komponenta v Návrháři šablon zařízení IoT Central. Výběrem **Zobrazit identitu** Zobrazte DTMI modelu:

:::image type="content" source="media/concepts-modeling-guide/iot-central-model.png" alt-text="Snímek obrazovky znázorňující model Termostata v Návrháři IoT Central":::

ID modelu je uloženo ve vlastnosti vlákna zařízení, jak ukazuje následující snímek obrazovky:

:::image type="content" source="media/concepts-modeling-guide/twin-model-id.png" alt-text="ID modelu ve vlastnosti digitálního vlákna":::

Model DTDL bez komponent je užitečné zjednodušení pro zařízení nebo modul IoT Edge s jedinou sadou telemetrie, vlastností a příkazů. Model, který nepoužívá komponenty, usnadňuje migraci stávajícího zařízení nebo modulu technologie Plug and Play zařízení nebo modul IoT – vytvoříte model DTDL, který popisuje vaše skutečné zařízení nebo modul, aniž by bylo potřeba definovat nějaké součásti.

> [!TIP]
> Modulem může být [modul zařízení nebo modul](../iot-hub/iot-hub-devguide-module-twins.md) [IoT Edge](../iot-edge/about-iot-edge.md).

### <a name="reuse"></a>Opakované použití

Existují dva způsoby, jak znovu použít definice rozhraní. Použití více komponent v modelu pro odkazování na jiné definice rozhraní. Použijte dědičnost pro rozšiřování existujících definic rozhraní.

### <a name="multiple-components"></a>Více komponent

Komponenty umožňují vytvořit model rozhraní jako sestavení jiných rozhraní.

Například rozhraní [termostatu](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) je definováno jako model. Můžete začlenit toto rozhraní jako jednu nebo více součástí při definování [modelu teplotního adaptéru](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json). V následujícím příkladu jsou tyto komponenty volány `thermostat1` a `thermostat2` .

Pro model DTDL s více komponentami je k dispozici nejméně dva oddíly součásti. Každý oddíl má `@type` nastavenou hodnotu `Component` a explicitně odkazuje na schéma, jak je znázorněno v následujícím fragmentu kódu:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "description": "Device with two thermostats and remote reboot.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "DataSize"
      ],
      "name": "workingSet",
      "displayName": "Working Set",
      "description": "Current working set of the device memory in KiB.",
      "schema": "double",
      "unit": "kibibyte"
    },
    {
      "@type": "Property",
      "name": "serialNumber",
      "displayName": "Serial Number",
      "description": "Serial number of the device.",
      "schema": "string"
    },
    {
      "@type": "Command",
      "name": "reboot",
      "displayName": "Reboot",
      "description": "Reboots the device after waiting the number of seconds specified.",
      "request": {
        "name": "delay",
        "displayName": "Delay",
        "description": "Number of seconds to wait before rebooting the device.",
        "schema": "integer"
      }
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1",
      "displayName": "Thermostat One",
      "description": "Thermostat One of Two."
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat2",
      "displayName": "Thermostat Two",
      "description": "Thermostat Two of Two."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
  ]
}
```

Tento model má tři komponenty, které jsou definovány v části Contents – dvě `Thermostat` komponenty a `DeviceInformation` komponenta. Oddíl Contents obsahuje také definice vlastností, telemetrie a příkazů.

Následující snímky obrazovky ukazují, jak se tento model zobrazuje v IoT Central. V rámci **výchozí součásti** nejvyšší úrovně se zobrazí definice vlastností, telemetrie a příkazů v řadiči teploty. V definicích součástí se zobrazí vlastnosti, telemetrie a definice příkazů pro každý termostat:

:::image type="content" source="media/concepts-modeling-guide/temperature-controller.png" alt-text="Snímek obrazovky zobrazující šablonu zařízení řadiče teploty v IoT Central.":::

:::image type="content" source="media/concepts-modeling-guide/temperature-controller-components.png" alt-text="Snímek obrazovky znázorňující termostatové komponenty v šabloně zařízení řadiče teploty v IoT Central.":::

Informace o tom, jak napsat kód zařízení, který komunikuje s komponentami, najdete v tématu [Příručka pro vývojáře pro zařízení IoT technologie Plug and Play](concepts-developer-guide-device.md).

Informace o tom, jak napsat kód služby, který se rozchází s komponentami na zařízení, najdete v tématu [Příručka pro vývojáře služby IoT technologie Plug and Play](concepts-developer-guide-service.md).

### <a name="inheritance"></a>Dědičnost

Dědičnost umožňuje znovu použít možnosti v základních rozhraních, aby se rozšířily možnosti rozhraní. Některé modely zařízení můžou například sdílet běžné možnosti, jako je sériové číslo:

:::image type="content" source="media/concepts-modeling-guide/inheritance.png" alt-text="Příklad dědičnosti v modelu zařízení ukazující Termostata a kontroler toku, který sdílí možnosti ze základního rozhraní." border="false":::

Následující fragment kódu ukazuje model DTML, který používá `extends` klíčové slovo k definování vztahu dědičnosti zobrazeného v předchozím diagramu:

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double",
        "unit": "degreeCelsius"
      },
      {
        "@type": "Property",
        "name": "targetTemperature",
        "schema": "double",
        "unit": "degreeCelsius",
        "writable": true
      }
    ],
    "extends": [
      "dtmi:com:example:baseDevice;1"
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:baseDevice;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "SerialNumber",
        "schema": "double",
        "writable": false
      }
    ]
  }
]
```

Následující snímek obrazovky ukazuje tento model v prostředí IoT Central šablon zařízení:

:::image type="content" source="media/concepts-modeling-guide/iot-central-inheritance.png" alt-text="Snímek obrazovky znázorňující dědičnost rozhraní v IoT Central":::

Při psaní kódu zařízení nebo na straně služby nemusí váš kód provádět žádné zvláštní kroky pro zpracování zděděných rozhraní. V příkladu zobrazeném v této části kód zařízení hlásí sériové číslo, jako by bylo součástí termostatu rozhraní.

### <a name="tips"></a>Tipy

Při vytváření modelu můžete kombinovat komponenty a dědičnost. Následující diagram znázorňuje `thermostat` model, který je děděn z `baseDevice` rozhraní. `baseDevice`Rozhraní má komponentu, kterou sám dědí z jiného rozhraní:

:::image type="content" source="media/concepts-modeling-guide/inheritance-components.png" alt-text="Diagram znázorňující model, který používá obě komponenty a dědičnost." border="false":::

Následující fragment kódu ukazuje model DTML, který používá `extends` `component` klíčová slova a k definování vztahu dědičnosti a použití komponenty zobrazené v předchozím diagramu:

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double",
        "unit": "degreeCelsius"
      },
      {
        "@type": "Property",
        "name": "targetTemperature",
        "schema": "double",
        "unit": "degreeCelsius",
        "writable": true
      }
    ],
    "extends": [
      "dtmi:com:example:baseDevice;1"
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:baseDevice;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "SerialNumber",
        "schema": "double",
        "writable": false
      },
      {
        "@type" : "Component",
        "schema": "dtmi:com:example:baseComponent;1",
        "name": "baseComponent"
      }
    ]
  }
]
```

## <a name="data-types"></a>Typy dat

K definování telemetrie, vlastností a parametrů příkazu použijte datové typy. Datové typy mohou být primitivní nebo komplexní. Komplexní DataTypes používají primitivní nebo jiné komplexní typy. Maximální hloubka složitých typů je pět úrovní.

### <a name="primitive-types"></a>Primitivní typy

Následující tabulka ukazuje sadu primitivních typů, které můžete použít:

| Primitivní typ | Description |
| --- | --- |
| `boolean` | Logická hodnota |
| `date` | Úplné datum, jak je definováno v [části 5,6 RFC 3339](https://tools.ietf.org/html/rfc3339#section-5.6) |
| `dateTime` | Datum a čas definované v [dokumentu RFC 3339](https://tools.ietf.org/html/rfc3339) |
| `double` | Plovoucí desetinná čárka IEEE 8 bajtů |
| `duration` | Doba ve formátu ISO 8601 |
| `float` | Plovoucí desetinná čárka IEEE 4 bajty |
| `integer` | Celé číslo se znaménkem a 4 bajty |
| `long` | Celé 8bitové číslo se znaménkem |
| `string` | Řetězec UTF8 |
| `time` | V plném čase, jak je definováno v [části 5,6 dokumentu RFC 3339](https://tools.ietf.org/html/rfc3339#section-5.6) |

Následující fragment kódu ukazuje ukázkovou definici telemetrie, která používá `double` typ v `schema` poli:

```json
{
  "@type": "Telemetry",
  "name": "temperature",
  "displayName": "Temperature",
  "schema": "double"
}
```

### <a name="complex-datatypes"></a>Komplexní DataTypes

Komplexní DataTypes jsou jedna z *polí*, *výčtů*, *map*, *objektů* nebo jednoho ze geoprostorového typu.

#### <a name="arrays"></a>Pole

Pole je indexovaný datový typ, kde jsou všechny prvky stejného typu. Typ prvku může být primitivní nebo komplexní typ.

Následující fragment kódu ukazuje ukázkovou definici telemetrie, která používá `Array` typ v `schema` poli. Prvky pole jsou logické hodnoty:

```json
{
  "@type": "Telemetry",
  "name": "ledState",
  "schema": {
    "@type": "Array",
    "elementSchema": "boolean"
  }
}
```

#### <a name="enumerations"></a>Výčty

Výčet popisuje typ se sadou pojmenovaných popisků, které jsou mapovány na hodnoty. Hodnoty mohou být celá čísla nebo řetězce, ale popisky jsou vždy řetězce.

Následující fragment kódu ukazuje ukázkovou definici telemetrie, která používá `Enum` typ v `schema` poli. Hodnoty ve výčtu jsou celá čísla:

```json
{
  "@type": "Telemetry",
  "name": "state",
  "schema": {
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "name": "offline",
        "displayName": "Offline",
        "enumValue": 1
      },
      {
        "name": "online",
        "displayName": "Online",
        "enumValue": 2
      }
    ]
  }
}
```

#### <a name="maps"></a>Maps

Mapa je typ s páry klíč-hodnota, kde všechny hodnoty mají stejný typ. Klíč v mapě musí být řetězec. Hodnoty v mapě můžou být libovolného typu, včetně jiného komplexního typu.

Následující fragment kódu ukazuje příklad definice vlastnosti, která používá `Map` typ v `schema` poli. Hodnoty v mapě jsou řetězce:

```json
{
  "@type": "Property",
  "name": "modules",
  "writable": true,
  "schema": {
    "@type": "Map",
    "mapKey": {
      "name": "moduleName",
      "schema": "string"
    },
    "mapValue": {
      "name": "moduleState",
      "schema": "string"
    }
  }
}
```

### <a name="objects"></a>Objekty

Typ objektu se skládá z pojmenovaných polí. Typy polí v mapě objektů mohou být primitivní nebo komplexní typy.

Následující fragment kódu ukazuje ukázkovou definici telemetrie, která používá `Object` typ v `schema` poli. Pole v objektu jsou `dateTime` `duration` typy, a `string` :

```json
{
  "@type": "Telemetry",
  "name": "monitor",
  "schema": {
    "@type": "Object",
    "fields": [
      {
        "name": "start",
        "schema": "dateTime"
      },
      {
        "name": "interval",
        "schema": "duration"
      },
      {
        "name": "status",
        "schema": "string"
      }
    ]
  }
}
```

#### <a name="geospatial-types"></a>Geoprostorové typy

DTDL poskytuje sadu geoprostorové typy založené na geografickém formátu [JSON](https://geojson.org/)pro modelování geografických datových struktur: `point` , `multiPoint` , `lineString` ,, a `multiLineString` `polygon` `multiPolygon` . Tyto typy jsou předdefinované vnořené struktury polí, objektů a výčtů.

Následující fragment kódu ukazuje ukázkovou definici telemetrie, která používá `point` typ v `schema` poli:

```json
{
  "@type": "Telemetry",
  "name": "location",
  "schema": "point"
}
```

Vzhledem k tomu, že geoprostorové typy jsou založené na poli, nelze je aktuálně použít v definicích vlastností.

## <a name="semantic-types"></a>Sémantické typy

Typ dat definice vlastnosti nebo telemetrie určuje formát dat, která zařízení vyměňuje se službou. Sémantický typ poskytuje informace o telemetrie a vlastnostech, které může aplikace použít k určení způsobu zpracování nebo zobrazení hodnoty. Každý sémantický typ má jednu nebo více přidružených jednotek. Například ve stupních Celsia a Fahrenheita jsou jednotky pro sémantický typ teploty. IoT Central řídicí panely a analýzy mohou pomocí informací sémantického typu určit, jak se mají vykreslovat hodnoty telemetrie nebo vlastností a zobrazované jednotky. Informace o tom, jak můžete použít analyzátor modelu ke čtení sémantických typů, naleznete v tématu [pochopení modelu digitálního nevlákenných modelů](concepts-model-parser.md).

Následující fragment kódu ukazuje ukázkovou definici telemetrie, která obsahuje informace o sémantickém typu. Sémantický typ `Temperature` se přidá do `@type` pole a `unit` hodnota `degreeCelsius` je jedna z platných jednotek pro sémantický typ:

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

## <a name="localization"></a>Lokalizace

Aplikace, jako je například IoT Central, používají informace v modelu k dynamickému sestavení uživatelského rozhraní kolem dat vyměňovaných pomocí zařízení IoT technologie Plug and Play. Například dlaždice na řídicím panelu mohou zobrazovat názvy a popisy pro telemetrii, vlastnosti a příkazy.

Nepovinná `description` `displayName` pole a v modelu uchovávají řetězce určené pro použití v uživatelském rozhraní. Tato pole mohou obsahovat lokalizované řetězce, které aplikace může použít k vykreslení lokalizovaného uživatelského rozhraní.

Následující fragment kódu ukazuje příklad definice telemetrie s teplotou, která obsahuje lokalizované řetězce:

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "description": {
    "en": "Temperature in degrees Celsius.",
    "it": "Temperatura in gradi Celsius."
  },
  "displayName": {
    "en": "Temperature",
    "it": "Temperatura"
  },
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

Přidávání lokalizovaných řetězců je volitelné. Následující příklad má pouze jeden výchozí jazyk:

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "description": "Temperature in degrees Celsius.",
  "displayName": "Temperature",
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

## <a name="lifecycle-and-tools"></a>Životní cyklus a nástroje

Mezi čtyři fáze životního cyklu pro model zařízení patří vytváření, publikování, používání a správa verzí:

### <a name="author"></a>Autor

Modely zařízení DTML jsou dokumenty JSON, které můžete vytvořit v textovém editoru. V IoT Central ale můžete k vytvoření modelu DTML použít prostředí GUI pro šablonu zařízení. V IoT Central můžete:

- Vytvořte rozhraní, která definují vlastnosti, telemetrie a příkazy.
- Použijte komponenty k sestavení více rozhraní dohromady.
- Definujte vztahy dědičnosti mezi rozhraními.
- Importujte a exportujte soubory modelu DTML.

Další informace najdete v tématu [definování nového typu zařízení IoT v aplikaci Azure IoT Central](../iot-central/core/howto-set-up-template.md).

[Editor DTDL pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) poskytuje textové prostředí pro úpravy pomocí ověřování syntaxe a automatické dokončování pro lepší kontrolu nad prostředím pro vytváření modelů.

### <a name="publish"></a>Publikovat

Pokud chcete, aby se vaše modely DTML sdíletelné a zjistitelné, publikujete je v úložišti modelů zařízení.

Před publikováním modelu ve veřejném úložišti můžete použít `dmr-client` nástroje k ověření modelu.

Další informace najdete v tématu [úložiště modelů zařízení](concepts-model-repository.md).

### <a name="use"></a>Použití

Aplikace, jako je IoT Central, používají modely zařízení. V IoT Central je model součástí šablony zařízení, která popisuje možnosti daného zařízení. IoT Central používá šablonu zařízení k dynamickému sestavení uživatelského rozhraní pro zařízení, včetně řídicích panelů a analýz.

Vlastní řešení může použít [analyzátor modelu digitálních vláken](concepts-model-parser.md) a pochopit možnosti zařízení, které model implementuje. Další informace najdete v tématu [použití modelů iot technologie Plug and Play v řešení IoT](concepts-model-discovery.md).

### <a name="version"></a>Verze

Aby se zajistilo, že zařízení a řešení na straně serveru, která používají modely, fungují i nadále, publikované modely jsou neměnné.

DTMI zahrnuje číslo verze, které můžete použít k vytvoření více verzí modelu. Zařízení a řešení na straně serveru můžou používat konkrétní verzi, kterou byly navrženy pro použití.

IoT Central implementuje více pravidel správy verzí pro modely zařízení. Pokud v IoT Central zadáte verzi šablony zařízení a její model, můžete migrovat zařízení z předchozích verzí na novější verze. Migrované zařízení ale nemůžou používat nové funkce bez upgradu firmwaru. Další informace najdete v tématu [Vytvoření nové verze šablony zařízení](../iot-central/core/howto-version-device-template.md).

## <a name="limits-and-constraints"></a>Omezení a omezení

Následující seznam shrnuje některá klíčová omezení a omezení pro modely:

- V současné době je maximální hloubka polí, map a objektů pět úrovní hloubky.
- V definicích vlastností nelze použít pole.
- Rozhraní můžete roztáhnout na hloubku 10 úrovní.
- Rozhraní může být rozšířeno na nejvíce dvou dalších rozhraní.
- Součást nemůže obsahovat jinou komponentu.

## <a name="next-steps"></a>Další kroky

Teď, když jste se dozvěděli o modelování zařízení, tady je několik dalších prostředků:

- [Instalace a použití nástrojů pro tvorbu DTDL](howto-use-dtdl-authoring-tools.md)
- [Digital neDTDLs – definice – jazyk v2 ()](https://github.com/Azure/opendigitaltwins-dtdl)
- [Úložiště modelů](./concepts-model-repository.md)
