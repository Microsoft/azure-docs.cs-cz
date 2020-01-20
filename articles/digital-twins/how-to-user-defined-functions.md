---
title: Vytvoření uživatelsky definovaných funkcí – v Azure Digital provlákna | Microsoft Docs
description: Jak vytvořit uživatelsky definované funkce, párování a přiřazení rolí v Azure Digital autovlákna.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/17/2020
ms.custom: seodec18
ms.openlocfilehash: 232d85789c25e905873286eba6fda32c327a6e25
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276937"
---
# <a name="how-to-create-user-defined-functions-in-azure-digital-twins"></a>Jak vytvořit uživatelsky definované funkce v digitálních prostředníkech Azure

[Uživatelsky definované funkce](./concepts-user-defined-functions.md) umožňují uživatelům nakonfigurovat vlastní logiku, aby se spustila ze příchozích zpráv telemetrie a metadat prostorového grafu. Uživatelé mohou také odesílat události do předdefinovaných [koncových bodů](./how-to-egress-endpoints.md).

Tato příručka vás seznámí s příkladem, který demonstruje, jak detekovat a upozorňovat na všechny čtení, která překročí určitou teplotu od přijatých událostí teploty.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>Reference klientské knihovny

Funkce, které jsou k dispozici jako pomocné metody v modulu runtime uživatelsky definované funkce, jsou uvedeny v [referenčním dokumentu klientské knihovny](./reference-user-defined-functions-client-library.md) .

## <a name="create-a-matcher"></a>Vytvoření shody

Shody jsou objekty grafu, které určují, jaké uživatelsky definované funkce se spouštějí pro danou zprávu telemetrie.

- Platné porovnání podmínky shody:

  - `Equals`
  - `NotEquals`
  - `Contains`

- Platné cíle podmínky shody:

  - `Sensor`
  - `SensorDevice`
  - `SensorSpace`

Následující příklad porovnávání vyhodnotí hodnotu true u jakékoli události telemetrie snímače, která `"Temperature"` jako hodnotu datového typu. Pomocí ověřené žádosti HTTP POST můžete vytvořit více shod na uživatelsky definované funkci:

```URL
YOUR_MANAGEMENT_API_URL/matchers
```

S textem JSON:

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
| YOUR_SPACE_IDENTIFIER | Jaké oblasti serveru vaší instance je hostován aplikací |

## <a name="create-a-user-defined-function"></a>Vytvoření uživatelem definované funkce

Vytvoření uživatelsky definované funkce zahrnuje vytvoření požadavku HTTP s více částmi rozhraní API pro správu digitálních vláken Azure.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

Po vytvoření porovnávání nahrajte tento fragment funkce s následujícím ověřeným požadavkem HTTP POST po částech.

```URL
YOUR_MANAGEMENT_API_URL/userdefinedfunctions
```

Použijte následující text:

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
| USER_DEFINED_BOUNDARY | Název hranice obsahu s více částmi |
| YOUR_SPACE_IDENTIFIER | Identifikátor prostoru  |
| YOUR_MATCHER_IDENTIFIER | ID shody, kterou chcete použít |

1. Ověřte, že hlavičky zahrnují: `Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`.
1. Ověřte, zda se jedná o část těla:

   - První část obsahuje požadovaná uživatelsky definovaná metadata funkce.
   - Druhá část obsahuje výpočetní logiku JavaScriptu.

1. V části **USER_DEFINED_BOUNDARY** nahraďte hodnoty **spaceId** (`YOUR_SPACE_IDENTIFIER`) a **Matches** (`YOUR_MATCHER_IDENTIFIER`).
1. Ověřte, zda je uživatelem definovaná funkce jazyka JavaScript dodávána jako `Content-Type: text/javascript`.

### <a name="example-functions"></a>Ukázkové funkce

Nastavte telemetrii senzorů přímo pro senzor s použitím **teploty**datového typu, který je `sensor.DataType`:

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

Parametr **telemetrie** zpřístupňuje atributy **SensorId** a **Message** odpovídající zprávě odesílané senzorem. Parametr **ExecutionContext** zpřístupňuje následující atributy:

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

V dalším příkladu se zaznamená zpráva, pokud se při čtení telemetrie senzorů překročí předdefinovaná prahová hodnota. Pokud jsou v instanci digitálních vláken Azure povolené vaše nastavení diagnostiky, předají se také protokoly z uživatelsky definovaných funkcí:

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

Následující kód vyvolá oznámení, pokud se hladina teploty zvyšuje nad předdefinovanou konstantou:

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

Složitější ukázku kódu uživatelsky definované funkce najdete v [rychlém](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js)startu pro obsazení.

## <a name="create-a-role-assignment"></a>Vytvoření přiřazení role

Vytvořte přiřazení role pro funkci definovanou uživatelem, která se má spustit. Pokud pro uživatelsky definovanou funkci neexistuje žádné přiřazení role, nebude mít správná oprávnění k interakci s rozhraním API pro správu nebo musí mít přístup k provádění akcí v objektech grafu. Akce, které může uživatelsky definovaná funkce provádět, jsou určené a definované prostřednictvím řízení přístupu na základě role v rámci rozhraní API pro správu digitálních vláken Azure. Například uživatelsky definované funkce mohou být omezeny oborem zadáním určitých rolí nebo určitých cest řízení přístupu. Další informace najdete v dokumentaci pro [řízení přístupu na základě rolí](./security-role-based-access-control.md) .

1. [Dotaz na systémové rozhraní API](./security-create-manage-role-assignments.md#retrieve-all-roles) pro všechny role, abyste získali ID role, kterou chcete přiřadit k uživatelsky definované funkci. Provedete to tak, že vytvoříte ověřený požadavek HTTP GET na:

    ```URL
    YOUR_MANAGEMENT_API_URL/system/roles
    ```
   Ponechte ID požadované role. Bude předán jako atribut těla JSON **roleId** (`YOUR_DESIRED_ROLE_IDENTIFIER`) níže.

1. **objectID** (`YOUR_USER_DEFINED_FUNCTION_ID`) bude uživatelsky definované ID funkce, která byla vytvořena dříve.
1. Najděte hodnotu **path** (`YOUR_ACCESS_CONTROL_PATH`) dotazem na mezery pomocí `fullpath`.
1. Zkopírujte vrácenou hodnotu `spacePaths`. Použijete níže. Nastavte ověřený požadavek HTTP GET na:

    ```URL
    YOUR_MANAGEMENT_API_URL/spaces?name=YOUR_SPACE_NAME&includes=fullpath
    ```

    | Hodnota | Nahradit hodnotou |
    | --- | --- |
    | YOUR_SPACE_NAME | Název místa, které se má použít |

1. Vložte vrácenou `spacePaths`ovou hodnotu do **cesty** , abyste vytvořili uživatelem definovanou funkci přiřazení role, a to tak, že vytvoříte ověřený požadavek HTTP post:

    ```URL
    YOUR_MANAGEMENT_API_URL/roleassignments
    ```
    S textem JSON:

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
    | YOUR_DESIRED_ROLE_IDENTIFIER | Identifikátor požadované role |
    | YOUR_USER_DEFINED_FUNCTION_ID | ID uživatelsky definované funkce, kterou chcete použít |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | ID určující typ funkce definovaný uživatelem (`UserDefinedFunctionId`) |
    | YOUR_ACCESS_CONTROL_PATH | Cesta k řízení přístupu |

>[!TIP]
> Další informace o operacích a koncových bodech rozhraní API pro správu funkcí definovaných uživatelem najdete v článku [jak vytvořit a spravovat přiřazení rolí](./security-create-manage-role-assignments.md) .

## <a name="send-telemetry-to-be-processed"></a>Odeslat telemetrii, která se má zpracovat

Senzor definovaný v grafu prostorové Intelligence posílá telemetrii. Telemetrie pak aktivuje spuštění uživatelsky definované funkce, která se nahrála. Procesor dat vybere telemetrii. Pak se vytvoří plán spuštění pro vyvolání uživatelsky definované funkce.

1. Načíst shody pro senzor, ze kterého byl vygenerováno čtení.
1. V závislosti na tom, které shody byly úspěšně vyhodnoceny, načtěte přidružené uživatelsky definované funkce.
1. Spusťte každou uživatelsky definovanou funkci.

## <a name="next-steps"></a>Další kroky

- Naučte se [vytvářet koncové body digitálních vláken Azure](./how-to-egress-endpoints.md) pro odesílání událostí do.

- Další podrobnosti o směrování v digitálních událostech Azure najdete v tématu [Směrování událostí a zpráv](./concepts-events-routing.md).

- Projděte si [referenční dokumentaci ke klientské knihovně](./reference-user-defined-functions-client-library.md).