---
title: Formát dat GeoJSON monitorové geografické zóny ve službě Azure Maps | Dokumentace Microsoftu
description: Další informace o formátu GeoJSON monitorové geografické zóny dat ve službě Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 02/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: aa39661c8ecc4b594478def7d0cc9be3006294c4
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56008248"
---
# <a name="geofencing-geojson-data"></a>Monitorování geografických zón GeoJSON dat

Azure Maps [získat monitorové geografické zóny](https://docs.microsoft.com/rest/api/map/spatial/getgeofencepreview) a [monitorové geografické zóny příspěvek](https://docs.microsoft.com/rest/api/map/spatial/postgeofencepreview) rozhraní API umožňují načíst blízkosti souřadnice vzhledem k zadané monitorové geografické zóny nebo sadu ohrazení. Tento článek podrobně popisuje, jak připravit data monitorové geografické zóny, který lze použít v Azure Maps GET a POST API.

Data pro monitorové geografické zóny nebo sadu monitorovaná geografická zóna je reprezentována `Feature` objektu a `FeatureCollection` objekt `GeoJSON` formátu, který je definován v [rfc7946](https://tools.ietf.org/html/rfc7946). Kromě:

* Typ objektu GeoJSON může být `Feature` objektu nebo `FeatureCollection` objektu.
* Může být typ objektu Geometry `Point`, `MultiPoint`, `LineString`, `MultiLineString`, `Polygon`, `MultiPolygon`, a `GeometryCollection`.
* By měl obsahovat všechny vlastnosti funkce `geometryId`, který se používá k identifikaci monitorové geografické zóny.
* Funkce s `Point`, `MultiPoint`, `LineString`, `MultiLineString` musí obsahovat `radius` ve vlastnostech. `radius` v metrech, se měří hodnota `radius` hodnotu od 1 do 10000.
* Funkce s `polygon` a `multipolygon` typ geometry nemá vlastnost radius.
* `validityTime` je volitelná vlastnost, která umožňuje uživateli nastavit čas vypršení platnosti a platnosti časové období pro data monitorové geografické zóny. Pokud není zadán, nikdy data vypršení platnosti a je vždy platný.
* `expiredTime` Je datum vypršení platnosti a čas dat monitorování geografických zón. Pokud hodnota `userTime` v požadavku je novější než tato hodnota, odpovídající monitorové geografické zóny data se považují za data s prošlou platností a není dotazovat. Na kterých geometryId tento monitorové geografické zóny se zahrnou data `expiredGeofenceGeometryId` pole v rámci odpovědi monitorové geografické zóny.
* `validityPeriod` Je seznam platnosti období monitorové geografické zóny. Pokud hodnota `userTime` v požadavku spadá mimo období platnosti, odpovídající data monitorové geografické zóny, je považován za jako neplatná a nebude možné dotazovat. Je součástí geometryId tato data monitorové geografické zóny `invalidPeriodGeofenceGeometryId` pole v rámci odpovědi monitorové geografické zóny. V následující tabulce jsou uvedeny vlastnosti prvku validityPeriod.

| Název | Type | Povinné  | Popis |
| :------------ |:------------: |:---------------:| :-----|
| startTime | Datum a čas  | true (pravda) | Počáteční datum a čas platnosti časového období. |
| endTime   | Datum a čas  | true (pravda) |  Koncové datum a čas platnosti časového období. |
| recurrenceType | řetězec | false (nepravda) |   Typ opakování období. Hodnota může být `Daily`, `Weekly`, `Monthly`, nebo `Yearly`. Výchozí hodnota je `Daily`.|
| businessDayOnly | Logická hodnota | false (nepravda) |  Označuje, zda data pouze platné během pracovních dnů. Výchozí hodnota je `false`.|


* Všechny hodnoty souřadnic jsou reprezentovány ve formě [latitude, longitude] definované v `WGS84`.
* Pro každou funkci, která obsahuje `MultiPoint`, `MultiLineString`, `MultiPolygon` , nebo `GeometryCollection`, vlastnosti, které se použijí na všechny prvky. Příklad: Všechny body v `MultiPoint` bude používat stejný radius k vytvoření více kruh monitorové geografické zóny.
* Ve scénáři kruh bod, kruh geometrie lze znázornit pomocí `Point` geometrický objekt s vlastnostmi rozpracovaného v [rozšíření GeoJSON geometrie](https://docs.microsoft.com/azure/azure-maps/extend-geojson).      

Tady je ukázkový text žádosti pro monitorové geografické zóny, vyjádřené geometrii kruh monitorové geografické zóny v `GeoJSON` pomocí středový bod a poloměr. Platná doba monitorové geografické zóny dat začíná od 2018-10-22, 9: 00 do 17: 00, opakuje každý den, s výjimkou víkendu. `expiredTime` označuje tato data monitorové geografické zóny se budou považovat za vypršela platnost, pokud `userTime` v požadavku je pozdější než `2019-01-01`.  

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