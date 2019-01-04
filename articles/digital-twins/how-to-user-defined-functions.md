---
title: Vytvoření uživatelem definovaných funkcí v Azure digitální dvojče | Dokumentace Microsoftu
description: Jak vytvořit uživatelem definované funkce, procesy pro hledání shody a přiřazení rolí v digitální dvojče Azure.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: alinast
ms.custom: seodec18
ms.openlocfilehash: 06c6d2935358650eb9f7ef1cda55d5292e203daf
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54019924"
---
# <a name="how-to-create-user-defined-functions-in-azure-digital-twins"></a>Vytvoření uživatelem definovaných funkcí v Azure digitální dvojče

[Uživatelem definované funkce](./concepts-user-defined-functions.md) povolit uživatelům konfigurovat vlastní logiku, který se spustí z příchozí zprávy telemetrii a metadata prostorový graf. Uživatelé můžou také posílat události předdefinované [koncové body](./how-to-egress-endpoints.md).

Tento průvodce vás provede příkladem představením toho, jak detekovat a upozornění na jakékoli materiály, které překračuje teplota z teploty přijatých událostí.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>Klientská knihovna – referenční informace

Funkce k dispozici jako pomocné metody v modulu runtime uživatelem definovaných funkcích jsou uvedené v [Klientská knihovna – referenční informace](./reference-user-defined-functions-client-library.md) dokumentu.

## <a name="create-a-matcher"></a>Vytvoření předávaný

Procesy pro hledání shody jsou objektů grafu, které určují, uživatelem definované funkce spuštění pro danou telemetrické zprávy.

- Platný předávaný podmínka porovnání:

  - `Equals`
  - `NotEquals`
  - `Contains`

- Platný předávaný podmínku cíle:

  - `Sensor`
  - `SensorDevice`
  - `SensorSpace`

Následující příklad předávaný vyhodnotí jako true na libovolnou událost telemetrická data ze senzorů s `"Temperature"` jako hodnotu datového typu. Můžete vytvořit více procesy pro hledání shody na uživatelem definovanou funkci tak, že ověřeného požadavku HTTP POST do:

```plaintext
YOUR_MANAGEMENT_API_URL/matchers
```

S text JSON:

```JSON
{
  "Name": "Temperature Matcher",
  "Conditions": [
    {
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    }
  ],
  "SpaceId": "YOUR_SPACE_IDENTIFIER"
}
```

| Hodnota | Nahradit hodnotou |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | Jaké oblasti serveru vaší instance je hostován aplikací |

## <a name="create-a-user-defined-function"></a>Vytvoření uživatelem definované funkce

Po vytvoření procesy pro hledání shody nahrajte funkce fragment kódu s požadavkem HTTP POST s více částmi. následující ověření:

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

```plaintext
YOUR_MANAGEMENT_API_URL/userdefinedfunctions
```

Pomocí následujícího textu JSON:

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
| USER_DEFINED_BOUNDARY | Název obsahu hranic s více částmi. |
| YOUR_SPACE_IDENTIFIER | Identifikátor místa  |
| YOUR_MATCHER_IDENTIFIER | ID předávaný kterou chcete použít |

1. Ověřte, že hlavičky zahrnují: `Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`.
1. Ověřte, zda je textu vícedílné zprávy standardu:

   - První část obsahuje metadata požadované uživatelem definované funkce.
   - Druhá část obsahuje logiku výpočetní jazyka JavaScript.

1. V **USER_DEFINED_BOUNDARY** části, nahraďte **spaceId** (`YOUR_SPACE_IDENTIFIER`) a **procesy pro hledání shody** (`YOUR_MATCHER_IDENTIFIER`) hodnoty.
1. Ověřte, že je jako zadaný uživatelem definované funkce jazyka JavaScript `Content-Type: text/javascript`.

### <a name="example-functions"></a>Příklad funkce

Nastavte telemetrická data ze senzorů čtení přímo pro senzoru s datovým typem **teploty**, což je `sensor.DataType`:

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

**Telemetrie** zpřístupňuje parametr **SensorId** a **zpráva** atributy odpovídající zpráva odeslaná senzoru. **Kontextu executionContext** parametr zveřejňuje následující atributy:

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

V následujícím příkladu jsme zaznamenat zprávu, pokud čtení telemetrických dat ze senzorů převyšuje předdefinované prahovou hodnotu. Pokud nastavení diagnostiky se na instanci Azure digitální dvojče povolí, předávají také protokoly z uživatelem definovaných funkcí:

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

Následující kód spustí oznámení, pokud úroveň teplota překročí předdefinovanou konstantu:

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

Složitější ukázku kódu uživatelem definované funkce, najdete v článku [rychlý start obsazení](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js).

## <a name="create-a-role-assignment"></a>Vytvořit přiřazení role

Vytvořte přiřazení role pro uživatelem definované funkce ke spuštění v rámci. Pokud pro uživatelem definované funkce neexistuje žádná přiřazení role, nemá potřebná oprávnění k interakci s rozhraním API pro správu nebo nebude mít přístup k provádění akcí v grafu objektů. Akce, které může provádět uživatelem definované funkce jsou zadány a prostřednictvím řízení přístupu na základě rolí v rámci Azure digitální dvojče Management API. Uživatelem definované funkce může omezit v oboru, například tak, že zadáte určité role nebo určité cesty řízení přístupu. Další informace najdete v tématu [řízení přístupu na základě rolí](./security-role-based-access-control.md) dokumentaci.

1. [Dotazování do rozhraní API systému](./security-create-manage-role-assignments.md#all) u všech rolí k získání ID role, kterou chcete přiřadit do uživatelem definované funkce. Udělat tak, že ověřeného požadavku HTTP GET na:

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```
   Zachovat ID požadované role. Se předá jako atribut textu JSON **roleId** (`YOUR_DESIRED_ROLE_IDENTIFIER`) níže.

1. **ID objektu** (`YOUR_USER_DEFINED_FUNCTION_ID`) bude uživatelem definovanou funkci ID, které jste vytvořili dříve.
1. Vrátí hodnotu **cesta** (`YOUR_ACCESS_CONTROL_PATH`) dotazováním prostory vaší s `fullpath`.
1. Zkopírujte vráceného `spacePaths` hodnotu. Který budete používat níže. Proveďte ověřené požadavek HTTP GET na:

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=YOUR_SPACE_NAME&includes=fullpath
    ```

    | Hodnota | Nahradit hodnotou |
    | --- | --- |
    | YOUR_SPACE_NAME | Název pole, které chcete použít |

1. Vložte vrácený `spacePaths` hodnoty do **cesta** k vytvoření přiřazení role uživatelem definovanou funkci tak, že ověřeného požadavku HTTP POST do:

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments
    ```
    S text JSON:

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
    | YOUR_USER_DEFINED_FUNCTION_ID | ID pro uživatelem definované funkce, kterou chcete použít |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | ID určení typu uživatelem definované funkce |
    | YOUR_ACCESS_CONTROL_PATH | Cesta správy přístupu |

>[!TIP]
> Přečtěte si článek [jak vytvořit a spravovat přiřazení rolí](./security-create-manage-role-assignments.md) Další informace o operacích rozhraní API pro správu uživatelem definované funkce a koncových bodů.

## <a name="send-telemetry-to-be-processed"></a>Odesílání telemetrických dat ke zpracování

Senzor definované v grafu prostorových intelligence odesílá telemetrická data. Telemetrická data zase aktivuje spuštění uživatelem definované funkce, který byl nahrán. Procesor dat převezme telemetrická data. Pak je vytvořen plán provádění volání uživatelem definované funkce.

1. Načte procesy pro hledání shody vygenerování čtení ze senzoru.
1. V závislosti na tom, jaké procesy pro hledání shody byla úspěšně vyhodnocena načtěte související uživatelem definované funkce.
1. Spuštění každý uživatelem definované funkce.

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [vytvoření koncových bodů Azure digitální dvojče](./how-to-egress-endpoints.md) k odesílání událostí do.

- Další podrobnosti o směrování v Azure digitální dvojče [směrování události a zprávy](./concepts-events-routing.md).

- Zkontrolujte [klientské knihovny referenční dokumentaci](./reference-user-defined-functions-client-library.md).
