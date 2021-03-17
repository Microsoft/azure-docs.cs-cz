---
title: Formát dat geografického JSON pro geografickou plot | Mapy Microsoft Azure
description: Přečtěte si o Azure Maps dat o geograficky. Přečtěte si, jak používat rozhraní API pro monitorování geografických zón a při načítání umístění souřadnic vzhledem k geograficky.
author: anastasia-ms
ms.author: v-stharr
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: e880710b93a6764df50780e685c89b5f569b4ec0
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92897190"
---
# <a name="geofencing-geojson-data"></a>Geografická data geografických zón

Rozhraní API pro monitorování geografických [zón](/rest/api/maps/spatial/getgeofence) a Azure Maps jejich [vystavování](/rest/api/maps/spatial/postgeofence) vám umožní získat blízkost souřadnic vzhledem k poskytnuté geografické ohrazení nebo sadě plotů. Tento článek podrobně popisuje, jak připravit data o geografickosti, která se dají použít v Azure Maps GET a POST API.

Data pro geografickou nebo množinu geografických plotů jsou reprezentována `Feature` objektem a `FeatureCollection` objektem ve `GeoJSON` formátu, který je definován v [rfc7946](https://tools.ietf.org/html/rfc7946). Kromě toho:

* Typ objektu pro typ objektivu JSON může být `Feature` objekt nebo `FeatureCollection` objekt.
* Typ objektu geometrie může být `Point` ,,, `MultiPoint` , `LineString` `MultiLineString` `Polygon` , `MultiPolygon` a `GeometryCollection` .
* Všechny vlastnosti funkce by měly obsahovat a `geometryId` , který se používá k identifikaci geografického ohraničení.
* Funkce se systémem `Point` , `MultiPoint` , `LineString` , `MultiLineString` musí obsahovat `radius` Vlastnosti. `radius` hodnota se měří v metrech, `radius` hodnota rozsahu od 1 do 10000.
* Funkce s `polygon` `multipolygon` typem a geometrie nemá vlastnost poloměr.
* `validityTime` je volitelná vlastnost, která umožňuje uživateli nastavit dobu platnosti a dobu platnosti pro data geografické hodnoty. Pokud není zadán, data budou nikdy vypršet a jsou vždy platná.
* `expiredTime`Je datum a čas vypršení platnosti dat geografických zón. Pokud `userTime` je hodnota v žádosti pozdější než tato hodnota, považují se odpovídající data o geografickou část považována za data s vypršenou platností a nedotazují se na ně. Na základě toho se geometryId z těchto geografických dat do `expiredGeofenceGeometryId` pole v rámci reakce na geografické ploty.
* `validityPeriod`Je seznam časových období platnosti geografické zóny. Pokud hodnota `userTime` v žádosti spadá mimo období platnosti, považují se odpovídající data o geografickou oblast za neplatnou a nebudou se dotazovat. GeometryId těchto geografických dat je součástí `invalidPeriodGeofenceGeometryId` pole v rámci reakce na geografické ploty. V následující tabulce jsou uvedeny vlastnosti elementu validityPeriod.

| Název | Typ | Povinné  | Popis |
| :------------ |:------------: |:---------------:| :-----|
| startTime | Datum a čas  | true | Datum a čas zahájení období platnosti. |
| endTime   | Datum a čas  | true |  Datum a čas konce období platnosti. |
| recurrenceType | řetězec | false (nepravda) |   Typ opakování období. Hodnota může být `Daily` , `Weekly` , `Monthly` nebo `Yearly` . Výchozí hodnota je `Daily`.|
| businessDayOnly | Logická hodnota | false (nepravda) |  Určuje, jestli jsou data platná jenom během pracovních dnů. Výchozí hodnota je `false`.|


* Všechny hodnoty souřadnic jsou reprezentovány jako [Zeměpisná délka, zeměpisná šířka] definovaná v `WGS84` .
* Pro každou funkci, která obsahuje `MultiPoint` , `MultiLineString` , `MultiPolygon` nebo `GeometryCollection` , jsou vlastnosti aplikovány na všechny prvky. například: všechny body v nástroji `MultiPoint` budou používat stejný poloměr pro vytvoření více geografických paprsků.
* V bodovém scénáři je možné znázornit geometrii kruhu pomocí `Point` objektu geometrie s vlastnostmi, které jsou vypracované v rámci rozšíření geografického [geometriíového formátu JSON](./extend-geojson.md).      

Následuje ukázkový text žádosti o geografickou ochranou, která je vyjádřena jako geometrie geografického tónu v `GeoJSON` používání středového bodu a poloměru. Platné období dat o geografickosti začíná od 2018-10-22 9:00 do 17:00, opakuje se každý den s výjimkou víkendu. `expiredTime` označuje, že tato data geografické plotu budou považována za prošlá, pokud `userTime` je v žádosti později než `2019-01-01` .  

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