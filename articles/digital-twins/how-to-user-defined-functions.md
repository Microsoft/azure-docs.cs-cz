---
title: Použití uživatelem definovaných funkcí v Azure digitální dvojče | Dokumentace Microsoftu
description: Pokyny o tom, jak vytvořit přiřazení role, uživatelem definované funkce a procesy pro hledání shody s Dvojčaty digitální Azure.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: alinast
ms.openlocfilehash: 33190472215e7a02b94951a73054ebe3e1994e54
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51623906"
---
# <a name="how-to-use-user-defined-functions-in-azure-digital-twins"></a>Použití uživatelem definovaných funkcí v Azure digitální dvojče

[Uživatelem definované funkce](./concepts-user-defined-functions.md) povolit uživatelům spustit vlastní logiku pro příchozí zprávy telemetrii a metadata prostorový graf (UDF). Uživatel pak může odesílání událostí do předdefinovaných koncových bodů. Tento průvodce vás provede příkladem acting událostí teplotní ke zjištění a upozornění na jakékoli materiály, které překračuje určité teploty.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>Klientská knihovna – referenční informace

Funkce, které jsou k dispozici jako pomocné metody v modulu runtime uživatelem definovaných funkcích jsou uvedené v [odkaz klienta](#Client-Reference) oddílu.

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

Následující příklad předávaný vyhodnotí jako true na libovolnou událost telemetrická data ze senzorů s `"Temperature"` jako hodnotu datového typu. Můžete vytvořit více procesy pro hledání shody v uživatelsky definované funkce:

```plaintext
POST yourManagementApiUrl/matchers
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

## <a name="create-a-user-defined-function-udf"></a>Vytvoření uživatelem definované funkce (UDF)

Po vytvoření procesy pro hledání shody nahrát fragment funkce s následujícími **příspěvek** volání:

> [!IMPORTANT]
> - V záhlaví, nastavte následující `Content-Type: multipart/form-data; boundary="userDefinedBoundary"`.
> - Text je vícedílné zprávy standardu:
>   - První část je o metadata potřebná pro UDF.
>   - Druhá část je výpočetní logiky JavaScript.
> - V **userDefinedBoundary** části, nahraďte **SpaceId** a **Machers** hodnoty.

```plaintext
POST yourManagementApiUrl/userdefinedfunctions with Content-Type: multipart/form-data; boundary="userDefinedBoundary"
```

| Hodnota parametru | Nahradit hodnotou |
| --- | --- |
| *userDefinedBoundary* | Název vícedílný obsah hranice |

### <a name="body"></a>Tělo

```plaintext
--userDefinedBoundary
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "SpaceId": "YOUR_SPACE_IDENTIFIER",
  "Name": "User Defined Function",
  "Description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "Matchers": ["YOUR_MATCHER_IDENTIFIER"]
}
--userDefinedBoundary
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--userDefinedBoundary--
```

| Hodnota | Nahradit hodnotou |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | Identifikátor místa  |
| YOUR_MATCHER_IDENTIFIER | ID předávaný kterou chcete použít |

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

Pro složitější ukázku kódu UDF [zkontrolujte dostupné místo čerstvé vzduchem UDF](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js).

## <a name="create-a-role-assignment"></a>Vytvořit přiřazení role

Potřebujeme vytvořit přiřazení role pro uživatelem definované funkce ke spuštění v rámci. Pokud není, nebude mít správná oprávnění k interakci s rozhraním API pro správu k provádění akcí v grafu objektů. Akce, které provádí uživatelem definovanou funkci nejsou vyloučené z řízení přístupu na základě rolí v rámci Azure digitální dvojče Management API. Může být omezená v oboru tak, že zadáte určité role nebo určité cesty řízení přístupu. Další informace najdete v tématu [řízení přístupu na základě rolí](./security-role-based-access-control.md) dokumentaci.

1. Dotazování pro role a získat ID role, kterou chcete přiřadit k UDF. Předejte ji do **RoleId**:

    ```plaintext
    GET yourManagementApiUrl/system/roles
    ```

1. **ID objektu** bude UDF ID, které jste vytvořili dříve.
1. Vrátí hodnotu **cesta** dotazováním prostory vaší s `fullpath`.
1. Zkopírujte vráceného `spacePaths` hodnotu. Který použijete v následujícím kódu:

    ```plaintext
    GET yourManagementApiUrl/spaces?name=yourSpaceName&includes=fullpath
    ```

    | Hodnota parametru | Nahradit hodnotou |
    | --- | --- |
    | *yourSpaceName* | Název pole, které chcete použít |

1. Vložte vrácený `spacePaths` hodnoty do **cesta** k vytvoření přiřazení role UDF:

    ```plaintext
    POST yourManagementApiUrl/roleassignments
    {
      "RoleId": "YOUR_DESIRED_ROLE_IDENTIFIER",
      "ObjectId": "YOUR_USER_DEFINED_FUNCTION_ID",
      "ObjectIdType": "YOUR_USER_DEFINED_FUNCTION_TYPE_ID",
      "Path": "YOUR_ACCESS_CONTROL_PATH"
    }
    ```

    | Hodnota | Nahradit hodnotou |
    | --- | --- |
    | YOUR_DESIRED_ROLE_IDENTIFIER | Identifikátor pro požadovanou roli |
    | YOUR_USER_DEFINED_FUNCTION_ID | ID pro UDF, kterou chcete použít |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | ID určení typu UDF |
    | YOUR_ACCESS_CONTROL_PATH | Cesta správy přístupu |

## <a name="send-telemetry-to-be-processed"></a>Odesílání telemetrických dat ke zpracování

Telemetrii generovanou senzor je popsáno v grafu se aktivuje spuštění uživatelem definované funkce, který byl nahrán. Procesor dat převezme telemetrická data. Plán spuštění vytvoří se pro volání uživatelem definované funkce.

1. Načte procesy pro hledání shody vygenerování čtení odhlásit z senzoru.
1. V závislosti na tom, co procesy pro hledání shody úspěšně vyhodnocena načtěte související uživatelem definované funkce.
1. Spusťte každý uživatelem definované funkce.

## <a name="client-reference"></a>Klientská – referenční informace

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒ `space`

Zadaný identifikátor místa, načte tuto funkci místo z grafu.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ---------- | ------------------- | ------------ |
| *ID*  | `guid` | identifikátor místa |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒ `sensor`

Zadaný identifikátor senzor, tato funkce načte senzor z grafu.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ---------- | ------------------- | ------------ |
| *ID*  | `guid` | identifikátor senzor |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒ `device`

Zadaný identifikátor zařízení, tato funkce načítá zařízení z grafu.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *ID* | `guid` | Identifikátor zařízení: |

### <a name="getsensorvaluesensorid-datatype--value"></a>⇒ getSensorValue (sensorId, datový typ) `value`

Zadaný identifikátor ze senzorů a jeho datového typu, tato funkce načte aktuální hodnota pro tento senzoru.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *sensorId*  | `guid` | identifikátor senzor |
| *Datový typ*  | `string` | Typ dat ze senzorů |

### <a name="getspacevaluespaceid-valuename--value"></a>⇒ getSpaceValue (spaceId, valueName) `value`

Zadaný identifikátor místa a název hodnoty, tato funkce načte aktuální hodnotou pro tuto vlastnost místa.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *spaceId*  | `guid` | identifikátor místa |
| *Název hodnoty* | `string` | Název vlastnosti místa |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>⇒ getSensorHistoryValues (sensorId, datový typ) `value[]`

Zadaný identifikátor ze senzorů a jeho datového typu, tato funkce načte historickými hodnotami pro tento senzoru.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | identifikátor senzor |
| *Datový typ* | `string` | Typ dat ze senzorů |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>⇒ getSpaceHistoryValues (spaceId, datový typ) `value[]`

Zadaný identifikátor místa a název hodnoty, tato funkce načte historickými hodnotami pro tuto vlastnost v prostoru.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | identifikátor místa |
| *Název hodnoty* | `string` | Název vlastnosti místa |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒ `space[]`

Zadaný identifikátor místo, tato funkce načte podřízený prostory pro toto nadřazené místo.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | identifikátor místa |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors(spaceId) ⇒ `sensor[]`

Zadaný identifikátor místo, tato funkce načte podřízený senzory pro toto nadřazené místo.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | identifikátor místa |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒ `device[]`

Zadaný identifikátor místa, tato funkce načte podřízený zařízení pro toto nadřazené místo.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | identifikátor místa |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ⇒ `sensor[]`

Zadaný identifikátor zařízení, tato funkce načítá podřízené senzory pro funkce zařízení nadřazené.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *ID zařízení* | `guid` | Identifikátor zařízení: |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒ `space`

Zadaný identifikátor místo, tato funkce načítá prostor jeho nadřazené.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *childSpaceId* | `guid` | identifikátor místa |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒ `space`

Zadaný identifikátor senzor, tato funkce načítá prostor jeho nadřazené.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | identifikátor senzor |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ⇒ `space`

Zadaný identifikátor zařízení, tato funkce načítá prostor jeho nadřazené.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *childDeviceId* | `guid` | Identifikátor zařízení: |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒ `space`

Zadaný identifikátor senzor, tato funkce načítá nadřazeného zařízení.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | identifikátor senzor |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>⇒ getSpaceExtendedProperty (spaceId, propertyName) `extendedProperty`

Zadaný identifikátor místa, tato funkce načte vlastnosti a její hodnotu z prostoru.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | identifikátor místa |
| *propertyName* | `string` | Název vlastnosti místa |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>⇒ getSensorExtendedProperty (sensorId, propertyName) `extendedProperty`

Zadaný identifikátor senzor, tato funkce načte vlastnosti a její hodnotu z senzoru.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | identifikátor senzor |
| *propertyName* | `string` | Název vlastnosti senzor |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>⇒ getDeviceExtendedProperty (deviceId, propertyName) `extendedProperty`

Zadaný identifikátor zařízení, tato funkce použije vlastnost a její hodnotu ze zařízení.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *ID zařízení* | `guid` | Identifikátor zařízení: |
| *propertyName* | `string` | Název vlastnosti zařízení |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue (sensorId, datový typ, hodnoty)

Tato funkce nastaví hodnotu v objektu ze senzorů pomocí daného datového typu.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | identifikátor senzor |
| *Datový typ*  | `string` | Typ dat ze senzorů |
| *value*  | `string` | Hodnota |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue (spaceId, datový typ, hodnoty)

Tato funkce nastaví hodnotu na objekt prostoru s danou datovým typem.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | identifikátor místa |
| *Datový typ* | `string` | Typ dat |
| *value* | `string` | Hodnota |

### <a name="logmessage"></a>log(Message)

Tato funkce protokolů následující zprávu do uživatelem definované funkce.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *Zpráva* | `string` | zaznamenávané zprávy |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification (topologyObjectId, topologyObjectType, datová část)

Tato funkce odesílá vlastní oznámení k odeslání.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *topologyObjectId*  | `guid` | Identifikátor objektu grafu. Příklady jsou místa, ze senzorů a ID zařízení.|
| *topologyObjectType*  | `string` | Příklady jsou ze senzorů a zařízení.|
| *datová část*  | `string` | Datová část JSON k odeslání oznámení. |

## <a name="return-types"></a>Návratové typy

Následující modely popisují návratových objektů z předchozí odkaz klienta.

### <a name="space"></a>Kosmické aktivity

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000000",
  "Name": "Space",
  "FriendlyName": "Conference Room",
  "TypeId": 0,
  "ParentSpaceId": "00000000-0000-0000-0000-000000000001",
  "SubtypeId": 0
}
```

### <a name="space-methods"></a>Místo metody

#### <a name="parent--space"></a>Parent() ⇒ `space`

Tato funkce vrací nadřazené místo aktuálního místa.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Tato funkce vrací podřízeného senzorů aktuálního místa.

#### <a name="childdevices--device"></a>ChildDevices() ⇒ `device[]`

Tato funkce vrací podřízeného zařízení aktuálního místa.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Tato funkce vrací rozšířené vlastnosti a její hodnotu aktuálního místa.

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Název rozšířené vlastnosti |

#### <a name="valuevaluename--value"></a>Value(VALUENAME) ⇒ `value`

Tato funkce vrací hodnotu aktuálního místa.

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *Název hodnoty* | `string` | Název hodnoty |

#### <a name="historyvaluename--value"></a>History(VALUENAME) ⇒ `value[]`

Tato funkce vrací historickými hodnotami aktuálního místa.

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *Název hodnoty* | `string` | Název hodnoty |

#### <a name="notifypayload"></a>Notify(Payload)

Tato funkce odesílá oznámení se zadanou datovou část.

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *datová část* | `string` | Datová část JSON zahrnout oznámení |

### <a name="device"></a>Zařízení

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000002",
  "Name": "Device",
  "FriendlyName": "Temperature Sensing Device",
  "Description": "This device contains a sensor that captures temperature readings.",
  "Type": "None",
  "Subtype": "None",
  "TypeId": 0,
  "SubtypeId": 0,
  "HardwareId": "ABC123",
  "GatewayId": "ABC",
  "SpaceId": "00000000-0000-0000-0000-000000000000"
}
```

### <a name="device-methods"></a>Metody zařízení

#### <a name="parent--space"></a>Parent() ⇒ `space`

Tato funkce vrací nadřazené prostoru aktuální zařízení.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Tato funkce vrací podřízeného senzorů aktuální zařízení.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Tato funkce vrací rozšířené vlastnosti a její hodnotu pro aktuální zařízení.

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Název rozšířené vlastnosti |

#### <a name="notifypayload"></a>Notify(Payload)

Tato funkce odesílá oznámení se zadanou datovou část.

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *datová část* | `string` | Datová část JSON zahrnout oznámení |

### <a name="sensor"></a>Senzor

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000003",
  "Port": "30",
  "PollRate": 3600,
  "DataType": "Temperature",
  "DataSubtype": "None",
  "Type": "Classic",
  "PortType": "None",
  "DataUnitType": "FahrenheitTemperature",
  "SpaceId": "00000000-0000-0000-0000-000000000000",
  "DeviceId": "00000000-0000-0000-0000-000000000001",
  "PortTypeId": 0,
  "DataUnitTypeId": 0,
  "DataTypeId": 0,
  "DataSubtypeId": 0,
  "TypeId": 0  
}
```

### <a name="sensor-methods"></a>Senzor metody

#### <a name="space--space"></a>Space() ⇒ `space`

Tato funkce vrací nadřazené místo aktuální ze souboru.

#### <a name="device--device"></a>Device() ⇒ `device`

Tato funkce vrací nadřazené zařízení aktuální ze souboru.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Tato funkce vrací rozšířené vlastnosti a její hodnotu aktuální senzoru.

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Název rozšířené vlastnosti |

#### <a name="value--value"></a>Value() ⇒ `value`

Tato funkce vrací hodnotu aktuální senzoru.

#### <a name="history--value"></a>History() ⇒ `value[]`

Tato funkce vrací historickými hodnotami aktuální senzoru.

#### <a name="notifypayload"></a>Notify(Payload)

Tato funkce odesílá oznámení se zadanou datovou část.

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *datová část* | `string` | Datová část JSON zahrnout oznámení |

### <a name="value"></a>Hodnota

```JSON
{
  "DataType": "Temperature",
  "Value": "70",
  "CreatedTime": ""
}
```

### <a name="extended-property"></a>Rozšířené vlastnosti

```JSON
{
  "Name": "OccupancyStatus",
  "Value": "Occupied"
}
```

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [vytvoření koncových bodů Azure digitální dvojče](how-to-egress-endpoints.md) k odesílání událostí do.

- Další podrobné informace o koncových bodů Azure digitální dvojče [Další informace o koncových bodech](concepts-events-routing.md).
