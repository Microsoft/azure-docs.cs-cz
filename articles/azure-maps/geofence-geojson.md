---
title: Formát dat GeoJSON pro geofence | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak připravit data geografické zóny, která lze použít v rozhraní chod Microsoft Azure Maps GET a POST Geofence API.
author: philmea
ms.author: philmea
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 7b9860908dd3bdf3dcda727f350578a97b890cac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335622"
---
# <a name="geofencing-geojson-data"></a>Geofencing GeoJSON data

Azure Maps [GET Geofence](/rest/api/maps/spatial/getgeofence) a [POST Geofence](/rest/api/maps/spatial/postgeofence) rozhraní umožňují načíst blízkost souřadnice vzhledem k poskytované geofence nebo sadu plotů. Tento článek podrobně popisuje, jak připravit data geofence, které lze použít v Azure Maps GET a POST API.

Data pro geofence nebo sadu geofences `Feature` jsou `FeatureCollection` reprezentována objektem a objektem ve `GeoJSON` formátu, který je definován v [rfc7946](https://tools.ietf.org/html/rfc7946). Kromě toho:

* Typ objektu GeoJSON `Feature` může být `FeatureCollection` Object nebo Object.
* Typ Objekt geometrie `Point`může `MultiPoint` `LineString`být `MultiLineString` `Polygon`a `MultiPolygon`, `GeometryCollection`, , , , , a .
* Všechny vlastnosti prvku `geometryId`by měly obsahovat , který se používá k identifikaci geofence.
* Funkce `Point`s `MultiPoint` `LineString`, `MultiLineString` , `radius` musí obsahovat vlastnosti. `radius`měřena v metrech, hodnota se `radius` pohybuje od 1 do 10000.
* Prvek `polygon` s `multipolygon` typem geometrie a typ geometrie nemá vlastnost poloměru.
* `validityTime`je volitelná vlastnost, která uživateli umožňuje nastavit čas a dobu platnosti pro data geografické zóny. Pokud není zadán, data nikdy vyprší a je vždy platný.
* Je `expiredTime` datum vypršení platnosti a čas geofencing dat. Pokud je `userTime` hodnota v požadavku pozdější než tato hodnota, odpovídající data geografické zóny se považují za data s prošlou platností a není dotazován. Na kterém geometrieId těchto dat geofence `expiredGeofenceGeometryId` budou zahrnuty v matici v rámci geofence odpověď.
* Jedná `validityPeriod` se o seznam doby platnosti geofence. Pokud hodnota `userTime` v požadavku spadá mimo dobu platnosti, odpovídající geofence data je považována za neplatnou a nebude dotazován. GeometryId těchto dat geofence je `invalidPeriodGeofenceGeometryId` součástí pole v rámci geofence odpověď. V následující tabulce jsou uvedeny vlastnosti elementu validityPeriod.

| Name (Název) | Typ | Požaduje se  | Popis |
| :------------ |:------------: |:---------------:| :-----|
| startTime | Datum a čas  | true | Čas počátečního data časového období platnosti. |
| endTime   | Datum a čas  | true |  Čas koncového data časového období platnosti. |
| recurrenceType | řetězec | false (nepravda) |   Typ opakování období. Hodnota může `Daily`být `Weekly` `Monthly`, `Yearly`, , nebo . Výchozí hodnota `Daily`je .|
| businessDayOnly | Logická hodnota | false (nepravda) |  Určete, zda jsou data platná pouze během pracovních dnů. Výchozí hodnota `false`je .|


* Všechny hodnoty souřadnic jsou reprezentovány jako `WGS84`[zeměpisná délka, zeměpisná šířka] definovaná v .
* Pro každý prvek, `MultiPoint` `MultiLineString`který `MultiPolygon` obsahuje `GeometryCollection`, , nebo , vlastnosti jsou použity na všechny prvky. příklad: Všechny body `MultiPoint` v písmenu a) budou používat stejný poloměr k vytvoření vícekruhové geografické zóny.
* Ve scénáři bodového kružnice může být `Point` geometrie kružnice reprezentována pomocí geometrie s vlastnostmi vypracovanými v [geometriích Rozšíření GeoJSON](https://docs.microsoft.com/azure/azure-maps/extend-geojson).      

Následuje tělo ukázkového požadavku pro geofence reprezentovanou jako geometrie geofence kruhu při `GeoJSON` použití středového bodu a poloměru. Platné období dat geofence začíná od 2018-10-22, 9AM do 5PM, opakované každý den kromě víkendu. `expiredTime`označuje, že tato data geofence `userTime` budou považována za `2019-01-01`prošlá, pokud je v požadavku pozdější než .  

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "geometryId" : "1",
        "subType": "Circle",
        "radius": 500,
        "validityTime": 
        {
            "expiredTime": "2019-01-01T00:00:00",
            "validityPeriod": [
                {
                    "startTime": "2018-10-22T09:00:00",
                    "endTime": "2018-10-22T17:00:00",
                    "recurrenceType": "Daily",
                    "recurrenceFrequency": 1,
                    "businessDayOnly": true
                }
            ]
        }
    }
}
```
