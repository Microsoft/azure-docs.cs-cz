---
title: Použití uživatelem definovaných funkcí v Azure digitální dvojče | Dokumentace Microsoftu
description: Pokyny o tom, jak vytvořit uživatelem definované funkce a procesy pro hledání shody přiřazení rolí s Dvojčaty digitální Azure.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: 7fbaff5ed1b60a4434ba2eb0c78c6aa1f3fd6645
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324070"
---
# <a name="how-to-use-user-defined-functions-in-azure-digital-twins"></a>Použití uživatelem definovaných funkcí v Azure digitální dvojče

[Uživatelem definované funkce](./concepts-user-defined-functions.md) povolit uživatelům spustit vlastní logiku pro příchozí zprávy telemetrii a metadata prostorový graf, které uživateli umožňují odesílání událostí do předdefinovaných koncových bodů. V této příručce provedeme příklad funguje událostí teplotní ke zjištění a upozornění na jakékoli materiály, které překračuje určité teploty.

V příkladech níže `https://yourManagementApiUrl` odkazuje na identifikátor URI digitální dvojče rozhraní API:

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management
```

| Název vlastního atributu | Nahraďte |
| --- | --- |
| `yourInstanceName` | Název instance digitální dvojče Azure |
| `yourLocation` | Jaké oblasti serveru vaší instance je hostován aplikací |

## <a name="client-library-reference"></a>Klientská knihovna – referenční informace

Funkce, které jsou k dispozici jako pomocné metody v modulu runtime uživatelem definovaných funkcích jsou uvedené v následující [klienta odkaz](#Client-Reference).

## <a name="create-a-matcher"></a>Vytvoření předávaný

Procesy pro hledání shody se graf objektů, které určují, jaké uživatelem definované funkce se provede pro danou telemetrické zprávy.

Platný předávaný podmínka porovnání:

- `Equals`
- `NotEquals`
- `Contains`

Platný předávaný podmínku cíle:

- `Sensor`
- `SensorDevice`
- `SensorSpace`

Následující příklad předávaný vyhodnotí na hodnotu true na libovolnou událost telemetrická data ze senzorů s `Temperature` jako hodnotu datového typu. Můžete vytvořit více procesy pro hledání shody v uživatelsky definované funkce.

```text
POST https://yourManagementApiUrl/api/v1.0/matchers
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
  "SpaceId": "yourSpaceIdentifier"
}
```

| Název vlastního atributu | Nahraďte |
| --- | --- |
| `yourManagementApiUrl` | Úplná cesta adresy URL pro vaše rozhraní API pro správu  |
| `yourSpaceIdentifier` | Jaké oblasti serveru vaší instance je hostován aplikací |

## <a name="create-a-user-defined-function-udf"></a>Vytvoření uživatelem definované funkce (UDF)

Po procesy pro hledání shody byly vytvořeny, nahrajte fragment funkce se volání POST tak následující:

> [!IMPORTANT]
> - V záhlaví, nastavte následující `Content-Type: multipart/form-data; boundary="userDefinedBoundary"`.
> - Text je vícedílný:
>   - První část je o metadata potřebná pro UDF.
>   - Druhá část je výpočetní logiky javascript.
> - Nahradit v `userDefinedBoundary` části `SpaceId` a `Machers` identifikátory GUID.

```plaintext
POST https://yourManagementApiUrl/api/v1.0/userdefinedfunctions with Content-Type: multipart/form-data; boundary="userDefinedBoundary"
```

| Název vlastního atributu | Nahraďte |
| --- | --- |
| `yourManagementApiUrl` | Úplná cesta adresy URL pro vaše rozhraní API pro správu  |

Text zprávy:

```plaintext
--userDefinedBoundary
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "SpaceId": "yourSpaceIdentifier",
  "Name": "User Defined Function",
  "Description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "Matchers": ["yourMatcherIdentifier"]
}
--userDefinedBoundary
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--userDefinedBoundary--
```

| Název vlastního atributu | Nahraďte |
| --- | --- |
| `yourSpaceIdentifier` | Identifikátor místa  |
| `yourMatcherIdentifier` | Id předávaný kterou chcete použít |

### <a name="example-functions"></a>Příklad funkce

Nastavte telemetrická data ze senzorů čtení přímo pro senzoru s datovým typem `Temperature`, což je senzoru. Datový typ:

```javascript
function process(telemetry, executionContext) {

  // Get sensor metadata
  var sensor = getSensorMetadata(telemetry.SensorId);

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Set the sensor reading as the current value for the sensor.
  setSensorValue(telemetry.SensorId, sensor.DataType, parseReading.SensorValue);
}
```

Zaznamenat zprávu, pokud čtení telemetrických dat ze senzorů převyšuje předem definovanou prahovou hodnotu. Pokud v instanci digitální dvojče jsou povolené diagnostické nastavení, budou předány protokoly z uživatelem definovaných funkcí:

```javascript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Example sensor telemetry reading range is greater than 0.5
  if(parseFloat(parseReading.SensorValue) > 0.5) {
    log(`Alert: Sensor with ID: ${telemetry.SensorId} detected an anomaly!`);
  }
}
```

Následující kód se aktivuje oznámení, pokud úroveň teplota stoupne nad předem definované konstantě.

```javascript
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

Ukázku kódu složitější UDF najdete [zkontrolujte dostupné místo vzduchem nová UDF](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js)

## <a name="create-a-role-assignment"></a>Vytvořit přiřazení role

Potřebujeme vytvořit přiřazení role pro uživatelem definované funkce spouštět pod. Pokud jsme tak neučiníte, nebude mít správná oprávnění k interakci s rozhraním API pro správu k provádění akcí v grafu objektů. Akce, které provádí uživatelem definovanou funkci nejsou vyloučené z řízení přístupu na základě rolí v rámci rozhraní API pro správu digitálních Dvojčat. Může být omezená v oboru tak, že zadáte určité role nebo určité cesty řízení přístupu. Další informace najdete v tématu [řízení přístupu na základě rolí](./security-role-based-access-control.md) dokumentaci.

- Dotazování pro role a získat ID role, kterou chcete přiřadit k UDF; Předejte RoleId níže.

```plaintext
GET https://yourManagementApiUrl/api/v1.0/system/roles
```

| Název vlastního atributu | Nahraďte |
| --- | --- |
| `yourManagementApiUrl` | Úplná cesta adresy URL pro vaše rozhraní API pro správu  |

- ID objektu bude UDF ID, které jste vytvořili dříve
- Najít `Path` dotazováním tyto mezery za jejich úplnou cestu a zkopírujte `spacePaths` hodnotu. Vložit v cestě pod při vytváření přiřazení role UDF

```plaintext
GET https://yourManagementApiUrl/api/v1.0/spaces?name=yourSpaceName&includes=fullpath
```

| Název vlastního atributu | Nahraďte |
| --- | --- |
| `yourManagementApiUrl` | Úplná cesta adresy URL pro vaše rozhraní API pro správu  |
| `yourSpaceName` | Název pole, které chcete použít |

```plaintext
POST https://yourManagementApiUrl/api/v1.0/roleassignments
{
  "RoleId": "yourDesiredRoleIdentifier",
  "ObjectId": "yourUserDefinedFunctionId",
  "ObjectIdType": "UserDefinedFunctionId",
  "Path": "yourAccessControlPath"
}
```

| Název vlastního atributu | Nahraďte |
| --- | --- |
| `yourManagementApiUrl` | Úplná cesta adresy URL pro vaše rozhraní API pro správu  |
| `yourDesiredRoleIdentifier` | Identifikátor pro požadovanou roli |
| `yourUserDefinedFunctionId` | Id pro UDF, kterou chcete použít |
| `yourAccessControlPath` | Cesta správy přístupu |

## <a name="send-telemetry-to-be-processed"></a>Odesílání telemetrických dat ke zpracování

Spuštění uživatelem definované funkce, který byl nahrán by měly aktivovat telemetrii generovanou senzor je popsáno v grafu. Po telemetrická data převezme procesor dat, se vytvoří plán provádění volání uživatelem definované funkce.

1. Načte procesy pro hledání shody vygenerování čtení odhlásit z senzoru.
1. V závislosti na tom, co procesy pro hledání shody úspěšně vyhodnocena načtěte související uživatelem definované funkce.
1. Spuštění každý uživatelem definované funkce.

## <a name="client-reference"></a>Klientská – referenční informace

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒ `space`

Zadaný identifikátor místo načte místo z grafu.

**Druh**: globální funkce

| Param  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| id  | `guid` | identifikátor místa |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒ `sensor`

Zadaný identifikátor senzor načte senzor z grafu.

**Druh**: globální funkce

| Param  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| id  | `guid` | identifikátor senzor |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒ `device`

Zadaný identifikátor zařízení načte zařízení z grafu.

**Druh**: globální funkce

| Param  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| id  | `guid` | Identifikátor zařízení: |

### <a name="getsensorvaluesensorid-datatype--value"></a>⇒ getSensorValue (sensorId, datový typ) `value`

Zadaný identifikátor ze senzorů a jeho datového typu, načte aktuální hodnota pro tento senzoru.

**Druh**: globální funkce

| Param  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| sensorId  | `guid` | identifikátor senzor |
| Datový typ  | `string` | Typ dat ze senzorů |

### <a name="getspacevaluespaceid-valuename--value"></a>⇒ getSpaceValue (spaceId, valueName) `value`

Zadaný identifikátor místa a název hodnoty, načte aktuální hodnotou pro tuto vlastnost místa.

**Druh**: globální funkce

| Param  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | identifikátor místa |
| Název hodnoty  | `string` | Název vlastnosti místa |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>⇒ getSensorHistoryValues (sensorId, datový typ) `value[]`

Zadaný identifikátor ze senzorů a jeho datového typu, načítání historických hodnot pro tento senzoru.

**Druh**: globální funkce

| Param  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| sensorId  | `guid` | identifikátor senzor |
| Datový typ  | `string` | Typ dat ze senzorů |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>⇒ getSpaceHistoryValues (spaceId, datový typ) `value[]`

Zadaný identifikátor místa a název hodnoty, načtení historických hodnot pro tuto vlastnost na místo.

**Druh**: globální funkce

| Param  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | identifikátor místa |
| Název hodnoty  | `string` | Název vlastnosti místa |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒ `space[]`

Zadaný identifikátor místa, načte podřízené prostory pro toto nadřazené místo.

**Druh**: globální funkce

| Param  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | identifikátor místa |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors(spaceId) ⇒ `sensor[]`

Zadaný identifikátor místa, načte podřízené senzory pro toto nadřazené místo.

**Druh**: globální funkce

| Param  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | identifikátor místa |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒ `device[]`

Zadaný identifikátor místa, načtěte podřízené zařízení pro toto nadřazené místo.

**Druh**: globální funkce

| Param  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | identifikátor místa |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ⇒ `sensor[]`

Zadaný identifikátor zařízení, načte podřízené senzory pro funkce zařízení nadřazené.

**Druh**: globální funkce

| Param  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| deviceId  | `guid` | Identifikátor zařízení: |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒ `space`

Zadaný identifikátor místa, načtení prostor jeho nadřazené.

**Druh**: globální funkce

| Param  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| childSpaceId  | `guid` | identifikátor místa |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒ `space`

Zadaný identifikátor senzor, načtení prostor jeho nadřazené.

**Druh**: globální funkce

| Param  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| childSensorId  | `guid` | identifikátor senzor |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ⇒ `space`

Zadaný identifikátor zařízení, načtěte prostor jeho nadřazené.

**Druh**: globální funkce

| Param  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| childDeviceId  | `guid` | Identifikátor zařízení: |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒ `space`

Zadaný identifikátor senzor, načtení nadřazeného zařízení.

**Druh**: globální funkce

| Param  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| childSensorId  | `guid` | identifikátor senzor |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>⇒ getSpaceExtendedProperty (spaceId, propertyName) `extendedProperty`

Zadaný identifikátor místa, načíst vlastnost a její hodnotu z prostoru.

**Druh**: globální funkce

| Param  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | identifikátor místa |
| Vlastnost PropertyName  | `string` | Název vlastnosti místa |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>⇒ getSensorExtendedProperty (sensorId, propertyName) `extendedProperty`

Zadaný identifikátor senzor, načíst vlastnost a její hodnotu ze senzoru.

**Druh**: globální funkce

| Param  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| sensorId  | `guid` | identifikátor senzor |
| Vlastnost PropertyName  | `string` | Název vlastnosti senzor |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>⇒ getDeviceExtendedProperty (deviceId, propertyName) `extendedProperty`

Zadaný identifikátor zařízení, načíst vlastnost a její hodnotu ze zařízení.

**Druh**: globální funkce

| Param  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| deviceId  | `guid` | Identifikátor zařízení: |
| Vlastnost PropertyName  | `string` | Název vlastnosti zařízení |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue (sensorId, datový typ, hodnoty)

Nastaví hodnotu v objektu ze senzorů pomocí daného datového typu.

**Druh**: globální funkce

| Param  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| sensorId  | `guid` | identifikátor senzor |
| Datový typ  | `string` | Typ dat ze senzorů |
| hodnota  | `string` | hodnota |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue (spaceId, datový typ, hodnoty)

Nastaví hodnotu na objekt prostoru s danou datovým typem.

**Druh**: globální funkce

| Param  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| spaceId  | `guid` | identifikátor místa |
| Datový typ  | `string` | Datový typ |
| hodnota  | `string` | hodnota |

### <a name="logmessage"></a>log(Message)

Zaznamená následující zprávu do uživatelem definované funkce.

**Druh**: globální funkce

| Param  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| zpráva  | `string` | zaznamenávané zprávy |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification (topologyObjectId, topologyObjectType, datová část)

Odešle vlastní oznámení k odeslání.

**Druh**: globální funkce

| Param  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| topologyObjectId  | `Guid` | Graf identifikátor objektu (např.) prostoru / id /device senzor)|
| topologyObjectType  | `string` | (např.) místo / ze senzorů a zařízení)|
| datová část  | `string` | datová část json k odeslání oznámení |

## <a name="return-types"></a>Návratové typy

Následují modelů popisem návratových objektů z výše uvedeného odkazu klienta.

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

Vrací nadřazené místo aktuálního místa.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Vrací podřízeného senzorů aktuálního místa.

#### <a name="childdevices--device"></a>ChildDevices() ⇒ `device[]`

Vrací podřízeného zařízení aktuálního místa.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Vrátí rozšířené vlastnosti a její hodnotu aktuálního místa.

| Param  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| Vlastnost PropertyName | `string` | Název rozšířené vlastnosti |

#### <a name="valuevaluename--value"></a>Value(VALUENAME) ⇒ `value`

Vrátí hodnotu aktuálního místa.

| Param  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| Název hodnoty | `string` | Název hodnoty |

#### <a name="historyvaluename--value"></a>History(VALUENAME) ⇒ `value[]`

Vrátí historickými hodnotami aktuálního místa.

| Param  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| Název hodnoty | `string` | Název hodnoty |

#### <a name="notifypayload"></a>Notify(Payload)

Odešle oznámení se zadanou datovou část.

| Param  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| datová část | `string` | datová část JSON zahrnout oznámení |

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

Vrátí nadřazenou prostoru aktuální zařízení.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Vrací podřízeného senzorů aktuální zařízení.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Vrátí rozšířené vlastnosti a její hodnotu pro aktuální zařízení.

| Param  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| Vlastnost PropertyName | `string` | Název rozšířené vlastnosti |

#### <a name="notifypayload"></a>Notify(Payload)

Odešle oznámení se zadanou datovou část.

| Param  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| datová část | `string` | datová část JSON zahrnout oznámení |

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

Vrací nadřazené místo aktuální ze souboru.

#### <a name="device--device"></a>Device() ⇒ `device`

Vrací nadřazené zařízení aktuální ze souboru.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Vrátí rozšířené vlastnosti a její hodnotu aktuální senzoru.

| Param  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| Vlastnost PropertyName | `string` | Název rozšířené vlastnosti |

#### <a name="value--value"></a>Value() ⇒ `value`

Vrátí hodnotu aktuálního senzoru.

#### <a name="history--value"></a>History() ⇒ `value[]`

Vrátí historickými hodnotami aktuální senzoru.

#### <a name="notifypayload"></a>Notify(Payload)

Odešle oznámení se zadanou datovou část.

| Param  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| datová část | `string` | datová část JSON zahrnout oznámení |

### <a name="value"></a>Hodnota

```JSON
{
  "DataType": "Temperature",
  "Value": "70",
  "CreatedTime": ""
}
```

### <a name="extended-property"></a>Rozšířená vlastnost

```JSON
{
  "Name": "OccupancyStatus",
  "Value": "Occupied"
}
```

## <a name="next-steps"></a>Další postup

Zjistěte, jak vytvořit digitální dvojče koncové body pro odesílání událostí do, přečtěte si téma [koncové body vytvořit digitální dvojče](how-to-egress-endpoints.md).

Další podrobnosti o digitální dvojče koncové body, přečtěte si [Další informace o koncových bodech](concepts-events-routing.md).
