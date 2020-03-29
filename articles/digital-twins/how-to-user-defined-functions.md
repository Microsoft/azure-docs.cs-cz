---
title: Jak vytvořit uživatelem definované funkce – v Azure Digital Twins | Dokumenty společnosti Microsoft
description: Jak vytvořit uživatelem definované funkce, matchery a přiřazení rolí v Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/17/2020
ms.custom: seodec18
ms.openlocfilehash: 232d85789c25e905873286eba6fda32c327a6e25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276937"
---
# <a name="how-to-create-user-defined-functions-in-azure-digital-twins"></a>Jak vytvořit uživatelem definované funkce v Azure Digital Twins

[Uživatelem definované funkce](./concepts-user-defined-functions.md) umožňují uživatelům konfigurovat vlastní logiku, která má být spuštěna z příchozích telemetrických zpráv a metadat prostorového grafu. Uživatelé mohou také odesílat události do předdefinovaných [koncových bodů](./how-to-egress-endpoints.md).

Tento průvodce provede příklad, který ukazuje, jak zjistit a upozornit na jakékoli čtení, které překračuje určitou teplotu z přijatých teplotních událostí.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>Odkaz na klientskou knihovnu

Funkce dostupné jako pomocné metody v době runtime definované uživatelem jsou uvedeny v referenčním dokumentu [klientské knihovny.](./reference-user-defined-functions-client-library.md)

## <a name="create-a-matcher"></a>Vytvoření matcheru

Matchers jsou objekty grafu, které určují, jaké uživatelem definované funkce běží pro danou telemetrickou zprávu.

- Platné porovnání podmínek matcheru:

  - `Equals`
  - `NotEquals`
  - `Contains`

- Platné cíle podmínky matcheru:

  - `Sensor`
  - `SensorDevice`
  - `SensorSpace`

Následující příklad matcher vyhodnotí true na všechny `"Temperature"` události telemetrie senzoru s jako jeho hodnota datového typu. Můžete vytvořit více matchers na uživatelem definované funkce tím, že ověřený požadavek HTTP POST na:

```URL
YOUR_MANAGEMENT_API_URL/matchers
```

S tělem JSON:

```JSON
{
  "id": "3626464-f39b-46c0-d9b0c-436aysj55",
  "name": "Temperature Matcher",
  "spaceId": "YOUR_SPACE_IDENTIFIER",
  "conditions": [
    {
      "id": "ag7gq35cfu3-e15a-4e9c-6437-sj6w68sy44s",
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    }
  ]
}
```

| Hodnota | Nahradit hodnotou |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | Ve které oblasti serveru je instance hostována |

## <a name="create-a-user-defined-function"></a>Vytvoření uživatelem definované funkce

Vytvoření uživatelem definované funkce zahrnuje vytvoření vícedílného požadavku HTTP pro rozhraní API pro správu digitálních dvojčat Azure.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

Po vytvoření matcherů nahrajte fragment funkce s následujícím ověřeným vícedílným požadavkem HTTP POST na:

```URL
YOUR_MANAGEMENT_API_URL/userdefinedfunctions
```

Použijte následující tělo:

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "spaceId": "YOUR_SPACE_IDENTIFIER",
  "name": "User Defined Function",
  "description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "matchers": ["YOUR_MATCHER_IDENTIFIER"]
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--USER_DEFINED_BOUNDARY--
```

| Hodnota | Nahradit hodnotou |
| --- | --- |
| USER_DEFINED_BOUNDARY | Název hranice vícedílného obsahu |
| YOUR_SPACE_IDENTIFIER | Identifikátor prostoru  |
| YOUR_MATCHER_IDENTIFIER | ID matcheru, který chcete použít |

1. Ověřte, zda `Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`záhlaví obsahují: .
1. Ověřte, zda je tělo vícedílné:

   - První část obsahuje požadovaná metadata uživatelem definované funkce.
   - Druhá část obsahuje výpočetní logiku JavaScriptu.

1. V **části USER_DEFINED_BOUNDARY** nahraďte hodnoty`YOUR_SPACE_IDENTIFIER` **spaceId** `YOUR_MATCHER_IDENTIFIER`( ) a **matchers** ( ).
1. Ověřte, zda je součástí uživatelem definované funkce JavaScriptu jako `Content-Type: text/javascript`.

### <a name="example-functions"></a>Příklady funkcí

Nastavte telemetrické údaje senzoru přímo pro **Temperature**snímač s `sensor.DataType`datovým typem Teplota , což je:

```JavaScript
function process(telemetry, executionContext) {

  // Get sensor metadata
  var sensor = getSensorMetadata(telemetry.SensorId);

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Set the sensor reading as the current value for the sensor.
  setSensorValue(telemetry.SensorId, sensor.DataType, parseReading.SensorValue);
}
```

**Parametr telemetrie** zveřejňuje **SensorId** a **Message** atributy, odpovídající zprávy odeslané senzorem. Parametr **executionContext** zveřejňuje následující atributy:

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

V dalším příkladu zaznamenáme zprávu, pokud telemetrické čtení senzoru překročí předdefinovanou prahovou hodnotu. Pokud jsou vaše diagnostická nastavení povolená v instanci Azure Digital Twins, protokoly z uživatelem definovaných funkcí se také předávají:

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Example sensor telemetry reading range is greater than 0.5
  if(parseFloat(parseReading.SensorValue) > 0.5) {
    log(`Alert: Sensor with ID: ${telemetry.SensorId} detected an anomaly!`);
  }
}
```

Následující kód spustí oznámení, pokud teplota stoupne nad předdefinovanou konstantu:

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Define threshold
  var threshold = 70;

  // Trigger notification 
  if(parseInt(parseReading) > threshold) {
    var alert = {
      message: 'Temperature reading has surpassed threshold',
      sensorId: telemetry.SensorId,
      reading: parseReading
    };

    sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(alert));
  }
}
```

Složitější ukázku kódu funkce definované uživatelem naleznete v [rychlém startu obsazenosti](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js).

## <a name="create-a-role-assignment"></a>Vytvoření přiřazení role

Vytvořte přiřazení role pro uživatelem definovanou funkci, pod kterou chcete spustit. Pokud pro uživatelem definovanou funkci neexistuje žádné přiřazení role, nebude mít správná oprávnění k interakci s rozhraním API pro správu ani k provádění akcí s objekty grafu. Akce, které může provádět uživatelem definovaná funkce, jsou určeny a definovány pomocí řízení přístupu na základě rolí v rámci rozhraní API pro správu digitálních dvojčat Azure. Například uživatelem definované funkce mohou být omezeny v oboru zadáním určitých rolí nebo určitých cest řízení přístupu. Další informace naleznete v dokumentaci k [řízení přístupu na základě rolí.](./security-role-based-access-control.md)

1. [Dotaz rozhraní API systému](./security-create-manage-role-assignments.md#retrieve-all-roles) pro všechny role získat ID role, které chcete přiřadit k vaší uživatelem definované funkce. Proveďte tak, že ověřte požadavek HTTP GET na:

    ```URL
    YOUR_MANAGEMENT_API_URL/system/roles
    ```
   Zachovat požadované ID role. Bude předán jako atribut těla JSON`YOUR_DESIRED_ROLE_IDENTIFIER` **roleId** ( ) níže.

1. **objectId** `YOUR_USER_DEFINED_FUNCTION_ID`( ) bude uživatelem definované ID funkce, které bylo vytvořeno dříve.
1. Najděte hodnotu`YOUR_ACCESS_CONTROL_PATH` **cesty** ( ) dotazem na mezery pomocí aplikace `fullpath`.
1. Zkopírujte `spacePaths` vrácenou hodnotu. Budete používat níže. Vytvořte ověřený požadavek HTTP GET na:

    ```URL
    YOUR_MANAGEMENT_API_URL/spaces?name=YOUR_SPACE_NAME&includes=fullpath
    ```

    | Hodnota | Nahradit hodnotou |
    | --- | --- |
    | YOUR_SPACE_NAME | Název prostoru, který chcete použít |

1. Vložte `spacePaths` vrácenou hodnotu do **cesty** a vytvořte uživatelem definované přiřazení role funkce vytvořením ověřeného požadavku HTTP POST na:

    ```URL
    YOUR_MANAGEMENT_API_URL/roleassignments
    ```
    S tělem JSON:

    ```JSON
    {
      "roleId": "YOUR_DESIRED_ROLE_IDENTIFIER",
      "objectId": "YOUR_USER_DEFINED_FUNCTION_ID",
      "objectIdType": "YOUR_USER_DEFINED_FUNCTION_TYPE_ID",
      "path": "YOUR_ACCESS_CONTROL_PATH"
    }
    ```

    | Hodnota | Nahradit hodnotou |
    | --- | --- |
    | YOUR_DESIRED_ROLE_IDENTIFIER | Identifikátor pro požadovanou roli |
    | YOUR_USER_DEFINED_FUNCTION_ID | ID uživatelem definované funkce, kterou chcete použít |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | ID určující uživatelem definovaný typ`UserDefinedFunctionId`funkce ( ) |
    | YOUR_ACCESS_CONTROL_PATH | Cesta řízení přístupu |

>[!TIP]
> Přečtěte si článek Jak vytvořit a spravovat přiřazení rolí pro další informace o uživatelem [definovaných](./security-create-manage-role-assignments.md) operacích rozhraní API pro správu funkcí a koncových bodech.

## <a name="send-telemetry-to-be-processed"></a>Odeslání telemetrie ke zpracování

Senzor definovaný v grafu prostorové inteligence vysílá telemetrii. Telemetrie zase aktivuje spuštění uživatelem definované funkce, která byla odeslána. Procesor dat vyzvedne telemetrii. Potom je vytvořen plán spuštění pro vyvolání uživatelem definované funkce.

1. Načtěte matchery pro senzor, ze kterého byl generován naměřený údaj.
1. V závislosti na tom, jaké matchers byly vyhodnoceny úspěšně, načíst přidružené uživatelem definované funkce.
1. Spusťte každou uživatelem definovanou funkci.

## <a name="next-steps"></a>Další kroky

- Zjistěte, jak [vytvořit koncové body Azure Digital Twins](./how-to-egress-endpoints.md) pro odesílání událostí.

- Další podrobnosti o směrování v Azure Digital Twins načtete-li se na [článek Události směrování a zprávy](./concepts-events-routing.md).

- Projděte si [referenční dokumentaci ke knihovně klienta](./reference-user-defined-functions-client-library.md).