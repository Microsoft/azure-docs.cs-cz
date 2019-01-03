---
title: Azure digitální dvojče uživatelem definované funkce Klientská knihovna – referenční informace | Dokumentace Microsoftu
description: Azure digitální dvojče uživatelem definované funkce Klientská knihovna – referenční informace.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: article
ms.date: 12/27/2018
ms.author: alinast
ms.custom: seodec18
ms.openlocfilehash: d309765e6c4b0b0cc8a7e92977134d4135a69a13
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "53995481"
---
# <a name="user-defined-functions-client-library-reference"></a>Uživatelem definované funkce Klientská knihovna – referenční informace

Tento dokument obsahuje informace o klientské knihovně Azure digitální dvojče uživatelem definované funkce.

## <a name="helper-methods"></a>Pomocné metody

Klientská knihovna definuje pomocné metody pro běžně používané operace.

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒ `space`

Zadaný identifikátor místa, načte tuto funkci místo z grafu.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | identifikátor místa |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒ `sensor`

Zadaný identifikátor senzor, tato funkce načte senzor z grafu.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | identifikátor senzor |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒ `device`

Zadaný identifikátor zařízení, tato funkce načítá zařízení z grafu.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *id* | `guid` | Identifikátor zařízení: |

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

Modely odpovědi vrácené z klienta odkaz pomocné metody jsou popsané níže.

### <a name="space"></a>Kosmické aktivity

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "Space",
  "friendlyName": "Conference Room",
  "typeId": 0,
  "parentSpaceId": "00000000-0000-0000-0000-000000000001",
  "subtypeId": 0
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
  "id": "00000000-0000-0000-0000-000000000002",
  "name": "Device",
  "friendlyName": "Temperature Sensing Device",
  "description": "This device contains a sensor that captures temperature readings.",
  "type": "None",
  "subtype": "None",
  "typeId": 0,
  "subtypeId": 0,
  "hardwareId": "ABC123",
  "gatewayId": "ABC",
  "spaceId": "00000000-0000-0000-0000-000000000000"
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
  "id": "00000000-0000-0000-0000-000000000003",
  "port": "30",
  "pollRate": 3600,
  "dataType": "Temperature",
  "dataSubtype": "None",
  "type": "Classic",
  "portType": "None",
  "dataUnitType": "FahrenheitTemperature",
  "spaceId": "00000000-0000-0000-0000-000000000000",
  "deviceId": "00000000-0000-0000-0000-000000000001",
  "portTypeId": 0,
  "dataUnitTypeId": 0,
  "dataTypeId": 0,
  "dataSubtypeId": 0,
  "typeId": 0  
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
  "dataType": "Temperature",
  "value": "70",
  "createdTime": ""
}
```

### <a name="extended-property"></a>Rozšířené vlastnosti

```JSON
{
  "name": "OccupancyStatus",
  "value": "Occupied"
}
```

## <a name="next-steps"></a>Další postup

- Další informace o [uživatelsky definovaných funkcí Azure digitální dvojče](./concepts-user-defined-functions.md).

- Přečtěte si [vytvoření uživatelem definovaných funkcí](./how-to-user-defined-functions.md).

- Přečtěte si [ladění uživatelem definované funkce](./how-to-diagnose-user-defined-functions.md).
