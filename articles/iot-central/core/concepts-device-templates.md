---
title: Co jsou šablony zařízení v Azure IoT Central | Microsoft Docs
description: Šablony zařízení Azure IoT Central umožňují určit chování zařízení připojených k vaší aplikaci.
author: dominicbetts
ms.author: dobett
ms.date: 05/21/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: cdc85029ec004060abf69b111d8a0ebca42147a4
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90015088"
---
# <a name="what-are-device-templates"></a>Co jsou šablony zařízení?

_Tento článek se týká vývojářů zařízení a tvůrců řešení._

Šablona zařízení v Azure IoT Central je podrobný plán, který definuje charakteristiky a chování typu zařízení, které se připojuje k vaší aplikaci. Například šablona zařízení definuje telemetrii, kterou zařízení odesílá, aby IoT Central mohl vytvářet vizualizace, které používají správné jednotky a datové typy.

Tvůrce řešení přidá šablony zařízení do aplikace IoT Central. Vývojář zařízení zapíše kód zařízení, který implementuje chování definované v šabloně zařízení.

Šablona zařízení obsahuje následující oddíly:

- _Model schopností zařízení (DCM)_. Tato část šablony zařízení definuje, jak zařízení komunikuje s vaší aplikací. Vývojář zařízení implementuje chování definované v DCM.
- _Vlastnosti cloudu_. Tato část šablony zařízení umožňuje vývojáři řešení určit jakákoli metadata zařízení, která se mají uložit. Vlastnosti cloudu se nesynchronizují se zařízeními a v aplikaci existují jenom. Vlastnosti cloudu neovlivňují kód, který vývojář zařízení zapisuje k implementaci DCM.
- _Vlastní nastavení_. Tato část šablony zařízení umožňuje vývojáři řešení přepsat některé definice v DCM. Vlastní nastavení jsou užitečná, pokud vývojář řešení chce upřesnit způsob, jakým aplikace zpracovává hodnotu, jako je například Změna zobrazovaného názvu pro vlastnost nebo barva použitou k zobrazení hodnoty telemetrie. Přizpůsobení neovlivňují kód, který vývojář zařízení zapisuje k implementaci DCM.
- _Zobrazení_. Tato část šablony zařízení umožňuje vývojářům řešení definovat vizualizace pro zobrazení dat ze zařízení a formulářů pro správu a řízení zařízení. Zobrazení používají DCM, vlastnosti cloudu a přizpůsobení. Zobrazení neovlivňují kód, který vývojář zařízení zapisuje k implementaci DCM.

> [!NOTE]
> [Verze iot technologie Plug and Play Public Preview](../../iot-pnp/overview-iot-plug-and-play.md) slouží vývojářům zařízení a výrobcům OEM, aby mohli začít sestavovat zařízení, která můžou certifikovat pro IoT technologie Plug and Play před spuštěním GA.

## <a name="device-capability-models"></a>Modely funkcí zařízení

DCM definuje, jak zařízení komunikuje s vaší aplikací IoT Central. Vývojář zařízení musí zajistit, aby zařízení implementovalo chování definované v DCM, aby IoT Central mohl monitorovat a spravovat zařízení. DCM je tvořen jedním nebo více _rozhraními_a každé rozhraní může definovat kolekci typů _telemetrie_ , _vlastností zařízení_a _příkazů_. Vývojář řešení může importovat soubor JSON, který definuje DCM na šablonu zařízení, nebo použít webové uživatelské rozhraní v IoT Central k vytvoření nebo úpravě DCM. Změny v DCM provedené pomocí webového uživatelského rozhraní vyžadují, [aby byla šablona zařízení ve verzi](./howto-version-device-template.md).

Vývojář řešení může také exportovat soubor JSON, který obsahuje DCM. Vývojář zařízení může pomocí tohoto dokumentu JSON pochopit, jak má zařízení komunikovat s aplikací IoT Central.

Soubor JSON, který definuje DCM, používá [digitální DTDL (digitald Definition Language) V1](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL). IoT Central očekává, že soubor JSON bude obsahovat DCM s rozhraními definovanými jako vložené místo v samostatných souborech.

Typické zařízení IoT se skládá z těchto součástí:

- Vlastní části, které tvoří jedinečné zařízení.
- Standardní části, které jsou společné pro všechna zařízení.

Tyto části se nazývají _rozhraní_ v DCM. Rozhraní definují podrobnosti jednotlivých částí, které vaše zařízení implementuje. Rozhraní jsou opakovaně použitelná napříč DCMs.

Následující příklad ukazuje osnovu modelu schopností zařízení pro zařízení snímače životního prostředí se dvěma rozhraními:

```json
{
  "@id": "urn:contoso:sensor_device:1",
  "@type": "CapabilityModel",
  "displayName": "Environment Sensor Capability Model",
  "implements": [
    {
      "@type": "InterfaceInstance",
      "name": "deviceinfo",
      "schema": {
        "@id": "urn:azureiot:DeviceManagement:DeviceInformation:1",
        "@type": "Interface",
        "displayName": "Device Information",
        "@context": "http://azureiot.com/v1/contexts/IoTModel.json",
        "contents": [
          ...
        ]
      }
    },
    {
      "@type": "InterfaceInstance",
      "name": "sensor",
      "schema": {
        "@id": "urn:contoso:EnvironmentalSensor:1",
        "@type": "Interface",
        "displayName": "Environmental Sensor",
        "@context": "http://azureiot.com/v1/contexts/IoTModel.json",
        "contents": [
          ...
        ]
      }
    }
  ],
  "@context": "http://azureiot.com/v1/contexts/IoTModel.json"
}
```

Model schopností má některá povinná pole:

- `@id`: jedinečné ID ve formě jednoduchého uniformního názvu prostředku.
- `@type`: deklaruje, že tento objekt je model schopností.
- `@context`: Určuje verzi DTDL, která se používá pro model schopností.
- `implements`: zobrazí seznam rozhraní, které vaše zařízení implementuje.

Každá položka v seznamu rozhraní v oddílu Implements má:

- `name`: název programování rozhraní.
- `schema`: rozhraní, které model schopností implementuje.

Rozhraní má některá povinná pole:

- `@id`: jedinečné ID ve formě jednoduchého uniformního názvu prostředku.
- `@type`: deklaruje, že tento objekt je rozhraní.
- `@context`: Určuje verzi DTDL, která se používá pro rozhraní.
- `contents`: zobrazí seznam vlastností, telemetrie a příkazů, které tvoří vaše zařízení.

K dispozici jsou některá volitelná pole, která můžete použít k přidání dalších podrobností do modelu schopností, jako je například zobrazovaný název a popis.

### <a name="interface"></a>Rozhraní

DTDL vám umožňuje popsat možnosti vašeho zařízení. Související možnosti jsou seskupené do rozhraní. Rozhraní popisují vlastnosti, telemetrie a příkazy, které součást vašeho zařízení implementuje:

- `Properties`. Vlastnosti jsou datová pole, která představují stav vašeho zařízení. Použijte vlastnosti, které reprezentují trvalý stav zařízení, jako je například stav Zapnuto pro čerpadlo chladicí kapaliny. Vlastnosti mohou také představovat základní vlastnosti zařízení, například verzi firmwaru zařízení. Vlastnosti můžete deklarovat jako jen pro čtení nebo zapisovatelné.
- `Telemetry`. Pole telemetrie reprezentují měření od senzorů. Pokaždé, když zařízení získá měření senzorů, mělo by se odeslat událost telemetrie obsahující data ze senzorů.
- `Commands`. Příkazy reprezentují metody, které můžou uživatelé zařízení na zařízení spouštět. Například příkaz pro obnovení nebo příkaz pro zapnutí nebo vypnutí ventilátoru.

Následující příklad ukazuje definici rozhraní senzoru prostředí:

```json
{
  "@type": "Property",
  "displayName": "Device State",
  "description": "The state of the device. Two states online/offline are available.",
  "name": "state",
  "schema": "boolean"
},
{
  "@type": "Property",
  "displayName": "Customer Name",
  "description": "The name of the customer currently operating the device.",
  "name": "name",
  "schema": "string",
  "writable": true
},
{
  "@type": [
    "Telemetry",
    "SemanticType/Temperature"
  ],
  "description": "Current temperature on the device",
  "displayName": "Temperature",
  "name": "temp",
  "schema": "double",
  "unit": "Units/Temperature/fahrenheit"
},
{
  "@type": "Command",
  "name": "turnon",
  "comment": "This Commands will turn-on the LED light on the device.",
  "commandType": "synchronous"
},
{
  "@type": "Command",
  "name": "turnoff",
  "comment": "This Commands will turn-off the LED light on the device.",
  "commandType": "synchronous"
}
```

Tento příklad ukazuje dvě vlastnosti, typ telemetrie a dva příkazy. Minimální Popis pole má:

- `@type` Chcete-li určit typ schopnosti: `Telemetry` , `Property` nebo `Command` .  V některých případech typ obsahuje sémantický typ, který umožňuje IoT Central provést některé předpoklady o tom, jak tuto hodnotu zpracovat.
- `name` pro hodnotu telemetrie.
- `schema` pro určení datového typu telemetrie nebo vlastnosti. Tato hodnota může být primitivní typ, například Double, Integer, Boolean nebo String. Podporují se také komplexní typy objektů, pole a mapy.
- `commandType` Určete, jak má být příkaz zpracován.

Volitelná pole, jako je zobrazované jméno a popis, umožňují přidat další podrobnosti k rozhraní a funkcím.

### <a name="properties"></a>Vlastnosti

Ve výchozím nastavení jsou vlastnosti jen pro čtení. Vlastnosti jen pro čtení znamenají, že se hodnota vlastnosti zařízení hlásí jako aktualizace vaší aplikace IoT Central. Vaše aplikace IoT Central nemůže nastavit hodnotu vlastnosti jen pro čtení.

Můžete také označit vlastnost jako zapisovatelnou na rozhraní. Zařízení může obdržet aktualizaci zapisovatelné vlastnosti z vaší aplikace IoT Central a také aktualizace hodnot vlastností sestav aplikace.

Zařízení není nutné připojit k nastavením hodnot vlastností. Aktualizované hodnoty se přenesou, když se zařízení příště připojí k aplikaci. Toto chování platí pro vlastnosti jen pro čtení i pro zápis.

Nepoužívejte vlastnosti k odeslání telemetrie ze zařízení. Například vlastnost jen pro čtení, například `temperatureSetting=80` by měla znamenat, že se teplota zařízení nastavila na 80 a zařízení se snaží získat nebo zůstat v této teplotě.

U zapisovatelných vlastností aplikace zařízení vrátí stavový kód požadovaného stavu, verzi a popis, aby označoval, zda obdržel a používal hodnotu vlastnosti.

### <a name="telemetry"></a>Telemetrie

IoT Central umožňuje zobrazit telemetrii na řídicích panelech a grafech a používat pravidla pro aktivaci akcí po dosažení mezních hodnot. IoT Central používá informace v DCM, jako jsou datové typy, jednotky a zobrazované názvy, k určení toho, jak se mají zobrazovat hodnoty telemetrie.

Můžete použít funkci IoT Central data export k streamování telemetrie do jiných míst určení, jako je například úložiště nebo Event Hubs.

### <a name="commands"></a>Příkazy

Příkazy jsou buď synchronní, nebo asynchronní. Synchronní příkaz se musí provést během 30 sekund ve výchozím nastavení a zařízení musí být připojené, až se dorazí na příkaz. Pokud zařízení reaguje v čase nebo pokud zařízení není připojené, příkaz se nezdařil.

Pro dlouhotrvající operace použijte asynchronní příkazy. Zařízení odesílá informace o průběhu pomocí zpráv telemetrie. Tyto zprávy o průběhu mají následující vlastnosti záhlaví:

- `iothub-command-name`: název příkazu, například `UpdateFirmware` .
- `iothub-command-request-id`: ID žádosti vygenerované na straně serveru, které se odešle do zařízení při počátečním volání.
- `iothub-interface-id`: ID rozhraní, ve kterém je tento příkaz definován, například `urn:example:AssetTracker:1` .
 `iothub-interface-name`: název instance tohoto rozhraní, například `myAssetTracker` .
- `iothub-command-statuscode`: stavový kód vrácený ze zařízení, například `202` .

## <a name="cloud-properties"></a>Vlastnosti cloudu

Vlastnosti cloudu jsou součástí šablony zařízení, ale nejsou součástí DCM. Cloudové vlastnosti umožňují vývojáři řešení zadat libovolná metadata zařízení, která se mají uložit v aplikaci IoT Central. Vlastnosti cloudu neovlivňují kód, který vývojář zařízení zapisuje k implementaci DCM.

Vývojář řešení může přidat vlastnosti cloudu do řídicích panelů a zobrazení spolu s vlastnostmi zařízení a umožnit tak operátorovi spravovat zařízení připojená k aplikaci. Vývojář řešení může také v rámci definice pravidla použít vlastnosti cloudu, aby mohla být prahová hodnota upravitelná pomocí operátoru.

## <a name="customizations"></a>Vlastní nastavení

Vlastní nastavení jsou součástí šablony zařízení, ale nejsou součástí DCM. Přizpůsobení umožní vývojářům řešení vylepšit nebo potlačit některé definice v DCM. Například vývojář řešení může změnit zobrazované jméno pro typ nebo vlastnost telemetrie. Vývojář řešení může také pomocí přizpůsobení přidat ověřování, jako je například minimální nebo maximální délka pro vlastnost zařízení typu řetězec.

Přizpůsobení může mít vliv na kód, který vývojář zařízení zapisuje k implementaci DCM. Přizpůsobení může například nastavit minimální a maximální délku řetězce nebo minimální a maximální číselné hodnoty pro telemetrii.

## <a name="views"></a>Zobrazení

Vývojář řešení vytvoří zobrazení, která operátorům umožní monitorovat a spravovat připojená zařízení. Zobrazení jsou součástí šablony zařízení, takže zobrazení je přidruženo k určitému typu zařízení. Zobrazení může zahrnovat:

- Grafy pro vykreslení telemetrie
- Dlaždice pro zobrazení vlastností zařízení jen pro čtení.
- Dlaždice, které umožní operátorovi upravit vlastnosti zařízení s možností zápisu.
- Dlaždice, které umožní operátorovi upravit vlastnosti cloudu.
- Dlaždice, které umožní operátorovi volat příkazy, včetně příkazů, které očekávají datovou část.
- Dlaždice pro zobrazení popisků, obrázků nebo Markdownu textu.

Telemetrii, vlastnosti a příkazy, které můžete přidat do zobrazení, jsou určeny pomocí DCM, vlastností cloudu a přizpůsobení v šabloně zařízení.

## <a name="next-steps"></a>Další kroky

Jako vývojář zařízení teď, když jste se dozvěděli o šablonách zařízení, je doporučený další postup čtení [telemetrie, vlastností a datových částí příkazů](./concepts-telemetry-properties-commands.md) , kde se dozvíte víc o datech, která zařízení vyměňuje pomocí IoT Central.

Jako vývojář řešení je doporučeným dalším krokem čtení [definice nového typu zařízení IoT v aplikaci Azure IoT Central](./howto-set-up-template.md) , kde najdete další informace o tom, jak vytvořit šablonu zařízení.
