---
title: Uživatelem definované funkce odkaz na knihovnu klienta - Azure Digital Twins | Dokumenty společnosti Microsoft
description: Azure Digital Twins uživatelsky definované funkce klienta knihovny referenční dokumentace.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: article
ms.date: 01/17/2020
ms.custom: seodec18
ms.openlocfilehash: bd6095daca51ddca0cfb4b34ca86e763df9a3d02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276811"
---
# <a name="user-defined-functions-client-library-reference"></a>Uživatelem definované funkce odkaz na knihovnu klienta

Tento dokument obsahuje referenční informace pro klientskou knihovnu azure digital twins uživatelem definovaných funkcí.

## <a name="helper-methods"></a>Pomocné metody

Klientská knihovna definuje pomocné metody pro běžně používané operace.

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒`space`

Vzhledem k identifikátoru mezery tato funkce načte prostor z grafu.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ---------- | ------------------- | ------------ |
| *Id*  | `guid` | Identifikátor prostoru |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒`sensor`

Vzhledem k identifikátoru senzoru tato funkce načte senzor z grafu.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ---------- | ------------------- | ------------ |
| *Id*  | `guid` | Identifikátor senzoru |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒`device`

Vzhledem k identifikátoru zařízení tato funkce načte zařízení z grafu.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *Id* | `guid` | Identifikátor zařízení |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue (sensorId, dataType) ⇒`value`

Vzhledem k identifikátoru senzoru a jeho datovému typu tato funkce načte aktuální hodnotu tohoto senzoru.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *sensorId*  | `guid` | Identifikátor senzoru |
| *Datatype*  | `string` | Datový typ senzoru |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue(spaceId, valueName) ⇒`value`

Vzhledem k identifikátoru mezery a název hodnoty, tato funkce načte aktuální hodnotu pro tuto vlastnost prostoru.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *mezera*  | `guid` | Identifikátor prostoru |
| *Název hodnoty* | `string` | Název vlastnosti mezery |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues (sensorId, dataType) ⇒`value[]`

Vzhledem k identifikátoru senzoru a jeho datovému typu tato funkce načte historické hodnoty tohoto senzoru.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identifikátor senzoru |
| *Datatype* | `string` | Datový typ senzoru |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues (spaceId, dataType) ⇒ ⇒`value[]`

Vzhledem k identifikátor mezery a název hodnoty, tato funkce načte historické hodnoty pro tuto vlastnost v prostoru.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *mezera* | `guid` | Identifikátor prostoru |
| *Název hodnoty* | `string` | Název vlastnosti mezery |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces (spaceId) ⇒`space[]`

Vzhledem k identifikátoru prostoru tato funkce načte podřízené prostory pro tento nadřazený prostor.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *mezera* | `guid` | Identifikátor prostoru |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors (spaceId) ⇒`sensor[]`

Vzhledem k identifikátoru prostoru tato funkce načte podřízené senzory pro tento nadřazený prostor.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *mezera* | `guid` | Identifikátor prostoru |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒`device[]`

Vzhledem k identifikátoru prostoru tato funkce načte podřízená zařízení pro tento nadřazený prostor.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *mezera* | `guid` | Identifikátor prostoru |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ⇒`sensor[]`

Vzhledem k identifikátoru zařízení tato funkce načte podřízené senzory pro dané nadřazené zařízení.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Identifikátor zařízení |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace (childSpaceId) ⇒`space`

Tato funkce, která má identifikátor prostoru, načte nadřazený prostor.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *childSpaceId* | `guid` | Identifikátor prostoru |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace (childSensorId) ⇒`space`

Vzhledem k identifikátoru senzoru tato funkce načte svůj nadřazený prostor.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Identifikátor senzoru |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ⇒`space`

Vzhledem k identifikátoru zařízení tato funkce načte nadřazený prostor.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *childDeviceId* | `guid` | Identifikátor zařízení |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒`space`

Vzhledem k identifikátoru senzoru tato funkce načte své nadřazené zařízení.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Identifikátor senzoru |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty(spaceId, propertyName) ⇒`extendedProperty`

Vzhledem k identifikátoru prostoru tato funkce načte vlastnost a její hodnotu z prostoru.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *mezera* | `guid` | Identifikátor prostoru |
| *Propertyname* | `string` | Název vlastnosti mezery |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty(sensorId, propertyName) ⇒`extendedProperty`

Vzhledem k identifikátoru senzoru tato funkce načte vlastnost a její hodnotu ze senzoru.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identifikátor senzoru |
| *Propertyname* | `string` | Název vlastnosti senzoru |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty(deviceId, propertyName) ⇒`extendedProperty`

Vzhledem k identifikátoru zařízení, tato funkce načte vlastnost a jeho hodnotu ze zařízení.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Identifikátor zařízení |
| *Propertyname* | `string` | Název vlastnosti zařízení |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue (sensorId, dataType, hodnota)

Tato funkce nastaví hodnotu objektu senzoru s daným datovým typem.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Identifikátor senzoru |
| *Datatype*  | `string` | Datový typ senzoru |
| *value*  | `string` | Hodnota |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue(spaceId, dataType, hodnota)

Tato funkce nastaví hodnotu objektu mezery s daným datovým typem.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *mezera* | `guid` | Identifikátor prostoru |
| *Datatype* | `string` | Datový typ |
| *value* | `string` | Hodnota |

### <a name="logmessage"></a>log(zpráva)

Tato funkce protokoluje následující zprávu v rámci uživatelem definované funkce.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *Zprávu* | `string` | Zpráva, která má být zaznamenána |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification(topologyObjectId, topologyObjectType, datová část)

Tato funkce odešle vlastní oznámení k odeslání.

**Druh**: globální funkce

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *topologyObjectId*  | `guid` | Identifikátor objektu grafu. Příklady jsou prostor, senzor a ID zařízení.|
| *topologieObjectType*  | `string` | Příklady jsou senzor a zařízení.|
| *payload*  | `string` | Datová část JSON, která má být odeslána s oznámením. |

## <a name="return-types"></a>Návratové typy

Modely odpovědí vrácené z metod nápovědy pro referenci klienta jsou popsány níže.

### <a name="space"></a>Místo

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

### <a name="space-methods"></a>Vesmírné metody

#### <a name="parent--space"></a>Rodič() ⇒`space`

Tato funkce vrátí nadřazený prostor aktuálního prostoru.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒`sensor[]`

Tato funkce vrátí podřízené senzory aktuálního prostoru.

#### <a name="childdevices--device"></a>ChildDevices() ⇒`device[]`

Tato funkce vrátí podřízená zařízení aktuálního prostoru.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒`extendedProperty`

Tato funkce vrátí rozšířenou vlastnost a její hodnotu pro aktuální prostor.

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *Propertyname* | `string` | Název rozšířené nemovitosti |

#### <a name="valuevaluename--value"></a>Hodnota(název_hodnoty) ⇒`value`

Tato funkce vrátí hodnotu aktuálního prostoru.

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *Název hodnoty* | `string` | Název hodnoty |

#### <a name="historyvaluename--value"></a>Historie (valueName) ⇒`value[]`

Tato funkce vrátí historické hodnoty aktuálního prostoru.

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *Název hodnoty* | `string` | Název hodnoty |

#### <a name="notifypayload"></a>Upozornit (datová část)

Tato funkce odešle oznámení se zadanou datovou část.

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | Datová část JSON, která má být zahrnuta do oznámení |

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

#### <a name="parent--space"></a>Rodič() ⇒`space`

Tato funkce vrátí nadřazený prostor aktuálního zařízení.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒`sensor[]`

Tato funkce vrátí podřízené senzory aktuálního zařízení.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒`extendedProperty`

Tato funkce vrátí rozšířené vlastnost i její hodnotu pro aktuální zařízení.

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *Propertyname* | `string` | Název rozšířené nemovitosti |

#### <a name="notifypayload"></a>Upozornit (datová část)

Tato funkce odešle oznámení se zadanou datovou část.

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | Datová část JSON, která má být zahrnuta do oznámení |

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

### <a name="sensor-methods"></a>Metody senzorů

#### <a name="space--space"></a>Prostor() ⇒`space`

Tato funkce vrátí nadřazený prostor aktuálního senzoru.

#### <a name="device--device"></a>Zařízení() ⇒`device`

Tato funkce vrátí nadřazené zařízení aktuálního senzoru.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒`extendedProperty`

Tato funkce vrátí rozšířenou vlastnost a její hodnotu pro aktuální senzor.

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *Propertyname* | `string` | Název rozšířené nemovitosti |

#### <a name="value--value"></a>Hodnota() ⇒`value`

Tato funkce vrátí hodnotu aktuálního senzoru.

#### <a name="history--value"></a>Historie() ⇒`value[]`

Tato funkce vrátí historické hodnoty aktuálního senzoru.

#### <a name="notifypayload"></a>Upozornit (datová část)

Tato funkce odešle oznámení se zadanou datovou část.

| Parametr  | Typ                | Popis  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | Datová část JSON, která má být zahrnuta do oznámení |

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

- Informace o [uživatelem definovaných funkcích Azure Digital Twins](./concepts-user-defined-functions.md).

- Přečtěte [si, jak vytvářet uživatelem definované funkce](./how-to-user-defined-functions.md).

- Přečtěte [si, jak ladit uživatelem definované funkce](./how-to-diagnose-user-defined-functions.md).
