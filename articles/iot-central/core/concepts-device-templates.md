---
title: Co jsou šablony zařízení v Azure IoT Central | Microsoft Docs
description: Šablony zařízení Azure IoT Central umožňují určit chování zařízení připojených k vaší aplikaci. Šablona zařízení určuje telemetrii, vlastnosti a příkazy, které zařízení musí implementovat. Šablona zařízení také definuje uživatelské rozhraní pro zařízení v IoT Central, jako jsou například formuláře a řídicí panely, které používá operátor.
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 04c2330ffee396f5fc30b85640e992df77c08263
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97795424"
---
# <a name="what-are-device-templates"></a>Co jsou šablony zařízení?

_Tento článek se týká vývojářů zařízení a tvůrců řešení._

Šablona zařízení v Azure IoT Central je podrobný plán, který definuje charakteristiky a chování typu zařízení, které se připojuje k vaší aplikaci. Například šablona zařízení definuje telemetrii, kterou zařízení odesílá, aby IoT Central mohl vytvářet vizualizace, které používají správné jednotky a datové typy.

Tvůrce řešení přidá šablony zařízení do aplikace IoT Central. Vývojář zařízení zapíše kód zařízení, který implementuje chování definované v šabloně zařízení.

Šablona zařízení obsahuje následující oddíly:

- _Model zařízení_. Tato část šablony zařízení definuje, jak zařízení komunikuje s vaší aplikací. Vývojář zařízení implementuje chování definované v modelu.
    - _Výchozí součást_. Každý model zařízení má výchozí komponentu. Výchozí rozhraní komponenty popisuje funkce, které jsou specifické pro model zařízení.
    - _Součásti_. Model zařízení může kromě výchozího komponentu zahrnovat i komponenty, které popisují možnosti zařízení. Každá součást obsahuje rozhraní, které popisuje funkce součásti. Rozhraní komponent se můžou znovu použít v jiných modelech zařízení. Například několik modelů telefonních zařízení může používat stejné rozhraní kamery.
    - _Zděděná rozhraní_. Model zařízení obsahuje jedno nebo více rozhraní, která šíří možnosti výchozí součásti.
- _Vlastnosti cloudu_. Tato část šablony zařízení umožňuje vývojáři řešení určit jakákoli metadata zařízení, která se mají uložit. Vlastnosti cloudu se nesynchronizují se zařízeními a v aplikaci existují jenom. Vlastnosti cloudu neovlivňují kód, který vývojář zařízení zapisuje k implementaci modelu zařízení.
- _Vlastní nastavení_. Tato část šablony zařízení umožňuje vývojáři řešení přepsat některé definice v modelu zařízení. Vlastní nastavení jsou užitečná, pokud vývojář řešení chce upřesnit způsob, jakým aplikace zpracovává hodnotu, jako je například Změna zobrazovaného názvu pro vlastnost nebo barva použitou k zobrazení hodnoty telemetrie. Přizpůsobení neovlivňují kód, který vývojář zařízení zapisuje k implementaci modelu zařízení.
- _Zobrazení_. Tato část šablony zařízení umožňuje vývojářům řešení definovat vizualizace pro zobrazení dat ze zařízení a formulářů pro správu a řízení zařízení. Zobrazení používají model zařízení, vlastnosti cloudu a přizpůsobení. Zobrazení neovlivňují kód, který vývojář zařízení zapisuje k implementaci modelu zařízení.

## <a name="device-models"></a>Modely zařízení

Model zařízení definuje, jak zařízení komunikuje s vaší aplikací IoT Central. Vývojář zařízení musí zajistit, aby zařízení implementovalo chování definované v modelu zařízení, aby IoT Central mohl monitorovat a spravovat zařízení. Model zařízení je tvořen jedním nebo více _rozhraními_ a každé rozhraní může definovat kolekci typů _telemetrie_ , _vlastností zařízení_ a _příkazů_. Vývojář řešení může importovat soubor JSON, který definuje model zařízení do šablony zařízení, nebo použít webové uživatelské rozhraní v IoT Central k vytvoření nebo úpravě modelu zařízení. Změny modelu zařízení provedené pomocí webového uživatelského rozhraní vyžadují, [aby se šablona zařízení](./howto-version-device-template.md)používala ve verzi.

Vývojář řešení může také exportovat soubor JSON, který obsahuje model zařízení. Vývojář zařízení může pomocí tohoto dokumentu JSON pochopit, jak má zařízení komunikovat s aplikací IoT Central.

Soubor JSON, který definuje model zařízení, používá program [Digital DTDL Definition Language () v2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). IoT Central očekává, že soubor JSON bude obsahovat model zařízení s rozhraními definovanými jako vložené místo v samostatných souborech.

Typické zařízení IoT se skládá z těchto součástí:

- Vlastní části, které tvoří jedinečné zařízení.
- Standardní části, které jsou společné pro všechna zařízení.

Tyto části se nazývají _rozhraní_ v modelu zařízení. Rozhraní definují podrobnosti jednotlivých částí, které vaše zařízení implementuje. Rozhraní jsou opakovaně použitelná napříč modely zařízení. V rozhraní DTDL komponenta odkazuje na rozhraní definované v samostatném souboru DTDL.

Následující příklad ukazuje osnovu modelu zařízení pro zařízení řadiče pro teploty. Výchozí komponenta obsahuje definice pro `workingSet` , `serialNumber` a `reboot` . Model zařízení zahrnuje také `thermostat` `deviceInformation` rozhraní a:

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
        "Telemetry", "DataSize"
      ],
      "name": "workingSet",
      "displayName": "Working Set",
      "description": "Current working set of the device memory in KiB.",
      "schema": "double",
      "unit" : "kibibyte"
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
      "name": "thermostat",
      "displayName": "Thermostat",
      "description": "Thermostat One."
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

Rozhraní má některá povinná pole:

- `@id`: jedinečné ID ve formě jednoduchého uniformního názvu prostředku.
- `@type`: deklaruje, že tento objekt je rozhraní.
- `@context`: Určuje verzi DTDL, která se používá pro rozhraní.
- `contents`: zobrazí seznam vlastností, telemetrie a příkazů, které tvoří vaše zařízení. Možnosti mohou být definovány ve více rozhraních.

K dispozici jsou některá volitelná pole, která můžete použít k přidání dalších podrobností do modelu schopností, jako je například zobrazovaný název a popis.

Každá položka v seznamu rozhraní v oddílu Implements má:

- `name`: název programování rozhraní.
- `schema`: rozhraní, které model schopností implementuje.

## <a name="interfaces"></a>Rozhraní

DTDL vám umožňuje popsat možnosti vašeho zařízení. Související možnosti jsou seskupené do rozhraní. Rozhraní popisují vlastnosti, telemetrie a příkazy, které součást vašeho zařízení implementuje:

- `Properties`. Vlastnosti jsou datová pole, která představují stav vašeho zařízení. Použijte vlastnosti, které reprezentují trvalý stav zařízení, jako je například stav Zapnuto pro čerpadlo chladicí kapaliny. Vlastnosti mohou také představovat základní vlastnosti zařízení, například verzi firmwaru zařízení. Vlastnosti můžete deklarovat jako jen pro čtení nebo zapisovatelné. Pouze zařízení mohou aktualizovat hodnotu vlastnosti jen pro čtení. Operátor může nastavit hodnotu vlastnosti s možností zápisu, která se má odeslat do zařízení.
- `Telemetry`. Pole telemetrie reprezentují měření od senzorů. Pokaždé, když zařízení získá měření senzorů, mělo by se odeslat událost telemetrie obsahující data ze senzorů.
- `Commands`. Příkazy reprezentují metody, které můžou uživatelé zařízení na zařízení spouštět. Například příkaz pro obnovení nebo příkaz pro zapnutí nebo vypnutí ventilátoru.

Následující příklad ukazuje definici rozhraní termostata:

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
      "displayName" : "Temperature",
      "description" : "Temperature in degrees Celsius.",
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
      "unit" : "degreeCelsius",
      "writable": true
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "maxTempSinceLastReboot",
      "schema": "double",
      "unit" : "degreeCelsius",
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
  ]
}
```

Tento příklad ukazuje dvě vlastnosti (jeden jen pro čtení a jeden zapisovatelný), typ telemetrie a příkaz. Minimální Popis pole má:

- `@type` Chcete-li určit typ schopnosti: `Telemetry` , `Property` nebo `Command` .  V některých případech typ obsahuje sémantický typ, který umožňuje IoT Central provést některé předpoklady o tom, jak tuto hodnotu zpracovat.
- `name` pro hodnotu telemetrie.
- `schema` pro určení datového typu telemetrie nebo vlastnosti. Tato hodnota může být primitivní typ, například Double, Integer, Boolean nebo String. Podporují se také komplexní typy objektů a mapy.

Volitelná pole, jako je zobrazované jméno a popis, umožňují přidat další podrobnosti k rozhraní a funkcím.

## <a name="properties"></a>Vlastnosti

Ve výchozím nastavení jsou vlastnosti jen pro čtení. Vlastnosti jen pro čtení znamenají, že se hodnota vlastnosti zařízení hlásí jako aktualizace vaší aplikace IoT Central. Vaše aplikace IoT Central nemůže nastavit hodnotu vlastnosti jen pro čtení.

Můžete také označit vlastnost jako zapisovatelnou na rozhraní. Zařízení může obdržet aktualizaci zapisovatelné vlastnosti z vaší aplikace IoT Central a také aktualizace hodnot vlastností sestav aplikace.

Zařízení není nutné připojit k nastavením hodnot vlastností. Aktualizované hodnoty se přenesou, když se zařízení příště připojí k aplikaci. Toto chování platí pro vlastnosti jen pro čtení i pro zápis.

Nepoužívejte vlastnosti k odeslání telemetrie ze zařízení. Například vlastnost jen pro čtení, například `temperatureSetting=80` by měla znamenat, že se teplota zařízení nastavila na 80 a zařízení se snaží získat nebo zůstat v této teplotě.

U zapisovatelných vlastností aplikace zařízení vrátí stavový kód požadovaného stavu, verzi a popis, aby označoval, zda obdržel a používal hodnotu vlastnosti.

## <a name="telemetry"></a>Telemetrie

IoT Central umožňuje zobrazit telemetrii na řídicích panelech a grafech a používat pravidla pro aktivaci akcí po dosažení mezních hodnot. IoT Central používá informace v modelu zařízení, jako jsou datové typy, jednotky a zobrazované názvy, k určení, jak zobrazit hodnoty telemetrie.

Můžete použít funkci IoT Central data export k streamování telemetrie do jiných míst určení, jako je například úložiště nebo Event Hubs.

## <a name="commands"></a>Příkazy

Příkaz se musí provést během 30 sekund ve výchozím nastavení a po přijetí příkazu se musí zařízení připojit. Pokud zařízení reaguje v čase nebo pokud zařízení není připojené, příkaz se nezdařil.

Příkazy mohou mít parametry žádosti a vracet odpověď.

### <a name="offline-commands"></a>Offline příkazy

Příkazy Queue můžete zvolit, pokud je zařízení aktuálně offline. Pokud je v šabloně zařízení možnost offline pro příkaz, povolte **frontu** .

Offline příkazy jsou jednosměrná oznámení pro zařízení z vašeho řešení. Příkazy offline můžou mít parametry žádosti, ale nevrátí odpověď.

> [!NOTE]
> Tato možnost je k dispozici pouze ve webovém uživatelském rozhraní IoT Central. Toto nastavení není zahrnuté, pokud model nebo rozhraní exportujete ze šablony zařízení.

## <a name="cloud-properties"></a>Vlastnosti cloudu

Vlastnosti cloudu jsou součástí šablony zařízení, ale nejsou součástí modelu zařízení. Cloudové vlastnosti umožňují vývojáři řešení zadat libovolná metadata zařízení, která se mají uložit v aplikaci IoT Central. Vlastnosti cloudu neovlivňují kód, který vývojář zařízení zapisuje k implementaci modelu zařízení.

Vývojář řešení může přidat vlastnosti cloudu do řídicích panelů a zobrazení spolu s vlastnostmi zařízení a umožnit tak operátorovi spravovat zařízení připojená k aplikaci. Vývojář řešení může také v rámci definice pravidla použít vlastnosti cloudu, aby mohla být prahová hodnota upravitelná pomocí operátoru.

## <a name="customizations"></a>Vlastní nastavení

Vlastní nastavení jsou součástí šablony zařízení, ale nejsou součástí modelu zařízení. Přizpůsobení umožní vývojářům řešení vylepšit nebo potlačit některé definice v modelu zařízení. Například vývojář řešení může změnit zobrazované jméno pro typ nebo vlastnost telemetrie. Vývojář řešení může také pomocí přizpůsobení přidat ověřování, jako je například minimální nebo maximální délka pro vlastnost zařízení typu řetězec.

Přizpůsobení může mít vliv na kód, který vývojář zařízení zapisuje k implementaci modelu zařízení. Přizpůsobení může například nastavit minimální a maximální délku řetězce nebo minimální a maximální číselné hodnoty pro telemetrii.

## <a name="views"></a>Zobrazení

Vývojář řešení vytvoří zobrazení, která operátorům umožní monitorovat a spravovat připojená zařízení. Zobrazení jsou součástí šablony zařízení, takže zobrazení je přidruženo k určitému typu zařízení. Zobrazení může zahrnovat:

- Grafy pro vykreslení telemetrie
- Dlaždice pro zobrazení vlastností zařízení jen pro čtení.
- Dlaždice, které umožní operátorovi upravit vlastnosti zařízení s možností zápisu.
- Dlaždice, které umožní operátorovi upravit vlastnosti cloudu.
- Dlaždice, které umožní operátorovi volat příkazy, včetně příkazů, které očekávají datovou část.
- Dlaždice pro zobrazení popisků, obrázků nebo Markdownu textu.

Telemetrie, vlastnosti a příkazy, které můžete přidat do zobrazení, se určují podle modelu zařízení, vlastností cloudu a přizpůsobení v šabloně zařízení.

## <a name="next-steps"></a>Další kroky

Jako vývojář zařízení teď, když jste se dozvěděli o šablonách zařízení, je doporučený další postup čtení [telemetrie, vlastností a datových částí příkazů](./concepts-telemetry-properties-commands.md) , kde se dozvíte víc o datech, která zařízení vyměňuje pomocí IoT Central.

Jako vývojář řešení je doporučeným dalším krokem čtení [definice nového typu zařízení IoT v aplikaci Azure IoT Central](./howto-set-up-template.md) , kde najdete další informace o tom, jak vytvořit šablonu zařízení.
