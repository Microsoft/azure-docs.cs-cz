---
title: Odkaz na klientskou knihovnu uživatelem definovaných funkcí – digitální vlákna Azure | Microsoft Docs
description: Služba Azure Digital vypracuje s referenční dokumentskou knihovnou klientské funkce.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: article
ms.date: 01/17/2020
ms.custom: seodec18
ms.openlocfilehash: bd6095daca51ddca0cfb4b34ca86e763df9a3d02
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276811"
---
# <a name="user-defined-functions-client-library-reference"></a>Uživatelsky definované funkce – reference klientské knihovny

Tento dokument poskytuje referenční informace o klientské knihovně uživatelů uživatelem definovaných funkcí pro uživatele Azure.

## <a name="helper-methods"></a>Pomocné metody

Knihovna klienta definuje pomocné metody pro běžně používané operace.

### <a name="getspacemetadataid--space"></a>getSpaceMetadata (ID) ⇒ `space`

Pomocí identifikátoru prostoru načte tato funkce prostor z grafu.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | Identifikátor prostoru |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒ `sensor`

Pomocí identifikátoru senzoru Tato funkce načte senzor z grafu.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | Identifikátor senzoru |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata (ID) ⇒ `device`

Pomocí identifikátoru zařízení Tato funkce načte zařízení z grafu.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *id* | `guid` | Identifikátor zařízení |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue (sensorId, datový typ) ⇒ `value`

Pomocí identifikátoru senzoru a jeho datového typu načte tato funkce aktuální hodnotu pro daný senzor.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *sensorId*  | `guid` | Identifikátor senzoru |
| *dataType*  | `string` | Datový typ senzoru |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue (spaceId, Název_hodnoty) – ⇒ `value`

Při zadání identifikátoru mezery a názvu hodnoty Tato funkce načte aktuální hodnotu pro danou vlastnost Space.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *spaceId*  | `guid` | Identifikátor prostoru |
| *valueName* | `string` | Název vlastnosti prostoru |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues (sensorId, datový typ) ⇒ `value[]`

Pomocí identifikátoru senzoru a jeho datového typu načte tato funkce historické hodnoty pro tento senzor.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identifikátor senzoru |
| *dataType* | `string` | Datový typ senzoru |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues (spaceId, datový typ) ⇒ `value[]`

Při zadání identifikátoru mezery a názvu hodnoty Tato funkce načte historické hodnoty této vlastnosti v prostoru.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identifikátor prostoru |
| *valueName* | `string` | Název vlastnosti prostoru |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces (spaceId) ⇒ `space[]`

Pomocí identifikátoru prostoru načte tato funkce podřízené mezery pro daný nadřazený prostor.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identifikátor prostoru |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors (spaceId) ⇒ `sensor[]`

Při zadání identifikátoru mezery načte tato funkce podřízené senzory pro daný nadřazený prostor.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identifikátor prostoru |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices (spaceId) ⇒ `device[]`

Při zadání identifikátoru mezery Tato funkce načte podřízená zařízení pro tento nadřazený prostor.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identifikátor prostoru |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors (deviceId) ⇒ `sensor[]`

Pomocí identifikátoru zařízení načte tato funkce podřízené senzory pro toto nadřazené zařízení.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Identifikátor zařízení |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒ `space`

Pomocí identifikátoru prostoru načte tato funkce svůj nadřazený prostor.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *childSpaceId* | `guid` | Identifikátor prostoru |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒ `space`

Když tato funkce Získá identifikátor senzoru, načte jeho nadřazený prostor.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Identifikátor senzoru |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace (childDeviceId) ⇒ `space`

Když tato funkce Získá identifikátor zařízení, načte jeho nadřazený prostor.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *childDeviceId* | `guid` | Identifikátor zařízení |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒ `space`

Pomocí identifikátoru senzoru Tato funkce načte své nadřazené zařízení.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Identifikátor senzoru |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty (spaceId, propertyName) ⇒ `extendedProperty`

Pomocí identifikátoru prostoru Tato funkce načte vlastnost a její hodnotu z prostoru.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identifikátor prostoru |
| *propertyName* | `string` | Název vlastnosti prostoru |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty (sensorId, propertyName) ⇒ `extendedProperty`

Pomocí identifikátoru senzoru Tato funkce načte vlastnost a její hodnotu ze senzoru.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identifikátor senzoru |
| *propertyName* | `string` | Název vlastnosti senzoru |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty (deviceId, propertyName) ⇒ `extendedProperty`

Když tato funkce Získá identifikátor zařízení, načte vlastnost a její hodnotu ze zařízení.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Identifikátor zařízení |
| *propertyName* | `string` | Název vlastnosti zařízení |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue (sensorId, datový typ, hodnota)

Tato funkce nastaví hodnotu objektu senzoru s daným datovým typem.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identifikátor senzoru |
| *dataType*  | `string` | Datový typ senzoru |
| *value*  | `string` | Hodnota |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue (spaceId, datový typ, hodnota)

Tato funkce nastaví hodnotu objektu Space s daným datovým typem.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Identifikátor prostoru |
| *dataType* | `string` | Data type |
| *value* | `string` | Hodnota |

### <a name="logmessage"></a>protokol (zpráva)

Tato funkce zaznamená následující zprávu v rámci uživatelsky definované funkce.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *message* | `string` | Zpráva, která má být zaznamenána |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification (topologyObjectId, topologyObjectType, datová část)

Tato funkce pošle vlastní oznámení odesílané do odesílání.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *topologyObjectId*  | `guid` | Identifikátor objektu grafu Příklady jsou mezery, senzory a ID zařízení.|
| *topologyObjectType*  | `string` | Příklady jsou senzory a zařízení.|
| *Délka*  | `string` | Datová část JSON, která se má odeslat s oznámením. |

## <a name="return-types"></a>Návratové typy

Modely odpovědí vrácené pomocí pomocných metod klientských referencí jsou popsány níže.

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

### <a name="space-methods"></a>Metody prostoru

#### <a name="parent--space"></a>Parent() ⇒ `space`

Tato funkce vrací nadřazený prostor aktuálního prostoru.

#### <a name="childsensors--sensor"></a>ChildSensors () ⇒ `sensor[]`

Tato funkce vrací podřízené senzory aktuálního prostoru.

#### <a name="childdevices--device"></a>ChildDevices () ⇒ `device[]`

Tato funkce vrací podřízená zařízení aktuálního prostoru.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty (propertyName) ⇒ `extendedProperty`

Tato funkce vrací rozšířenou vlastnost a její hodnotu pro aktuální prostor.

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Název rozšířené vlastnosti |

#### <a name="valuevaluename--value"></a>Value (Název_hodnoty) ⇒ `value`

Tato funkce vrací hodnotu aktuálního prostoru.

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Název hodnoty |

#### <a name="historyvaluename--value"></a>History (valueName) ⇒ `value[]`

Tato funkce vrací historické hodnoty aktuálního místa.

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Název hodnoty |

#### <a name="notifypayload"></a>Oznamovat (datová část)

Tato funkce pošle oznámení se zadanou datovou částí.

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *Délka* | `string` | Datová část JSON, která se má zahrnout do oznámení |

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

Tato funkce vrací nadřazený prostor aktuálního zařízení.

#### <a name="childsensors--sensor"></a>ChildSensors () ⇒ `sensor[]`

Tato funkce vrací podřízené senzory aktuálního zařízení.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty (propertyName) ⇒ `extendedProperty`

Tato funkce vrací rozšířenou vlastnost a její hodnotu pro aktuální zařízení.

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Název rozšířené vlastnosti |

#### <a name="notifypayload"></a>Oznamovat (datová část)

Tato funkce pošle oznámení se zadanou datovou částí.

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *Délka* | `string` | Datová část JSON, která se má zahrnout do oznámení |

### <a name="sensor"></a>Elektrické

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

### <a name="sensor-methods"></a>Metody snímače

#### <a name="space--space"></a>Space() ⇒ `space`

Tato funkce vrací nadřazený prostor aktuálního senzoru.

#### <a name="device--device"></a>Zařízení () ⇒ `device`

Tato funkce vrátí nadřazené zařízení aktuálního senzoru.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty (propertyName) ⇒ `extendedProperty`

Tato funkce vrací rozšířenou vlastnost a její hodnotu pro aktuální senzor.

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | Název rozšířené vlastnosti |

#### <a name="value--value"></a>Value () ⇒ `value`

Tato funkce vrací hodnotu aktuálního senzoru.

#### <a name="history--value"></a>History () `value[]` ⇒

Tato funkce vrací historické hodnoty aktuálního senzoru.

#### <a name="notifypayload"></a>Oznamovat (datová část)

Tato funkce pošle oznámení se zadanou datovou částí.

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *Délka* | `string` | Datová část JSON, která se má zahrnout do oznámení |

### <a name="value"></a>Hodnota

```JSON
{
  "dataType": "Temperature",
  "value": "70",
  "createdTime": ""
}
```

### <a name="extended-property"></a>Rozšířená vlastnost

```JSON
{
  "name": "OccupancyStatus",
  "value": "Occupied"
}
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [digitálních funkcích Azure, které jsou uživatelsky definované](./concepts-user-defined-functions.md).

- Naučte [se vytvářet uživatelsky definované funkce](./how-to-user-defined-functions.md).

- Naučte [se ladit uživatelsky definované funkce](./how-to-diagnose-user-defined-functions.md).
