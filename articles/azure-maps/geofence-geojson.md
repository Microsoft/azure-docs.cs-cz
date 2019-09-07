---
title: Formát geografických dat geografického formátu JSON v Azure Maps | Microsoft Docs
description: Seznamte se s geografickými datovými formáty geografického formátu JSON v Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 5946180c161a38a30f44e235ce0b626fd70a5400
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735141"
---
# <a name="geofencing-geojson-data"></a>Geografická data geografických zón

Rozhraní API pro monitorování geografických [zón](/rest/api/maps/spatial/getgeofence) a Azure Maps jejich [vystavování](/rest/api/maps/spatial/postgeofence) vám umožní získat blízkost souřadnic vzhledem k poskytnuté geografické ohrazení nebo sadě plotů. Tento článek podrobně popisuje, jak připravit data o geografickosti, která se dají použít v Azure Maps GET a POST API.

Data pro geografickou nebo množinu geografických plotů jsou reprezentována `Feature` objektem a `GeoJSON` `FeatureCollection` objektem ve formátu, který je definován v [rfc7946](https://tools.ietf.org/html/rfc7946). Kromě toho:

* Typ objektu pro typ objektivu JSON může `Feature` být objekt `FeatureCollection` nebo objekt.
* Typ objektu `Point`geometrie může být, `MultiPoint`, `LineString` `MultiLineString` ,,`MultiPolygon`, a .`GeometryCollection` `Polygon`
* Všechny vlastnosti funkce by měly obsahovat `geometryId`a, který se používá k identifikaci geografického ohraničení.
* Funkce se `Point`systémem `MultiPoint`, `LineString`, ,`MultiLineString` musí obsahovat`radius` vlastnosti. `radius`hodnota se měří v metrech, `radius` hodnota rozsahu od 1 do 10000.
* Funkce s `polygon` typem `multipolygon` a geometrie nemá vlastnost poloměr.
* `validityTime`je volitelná vlastnost, která umožňuje uživateli nastavit dobu platnosti a dobu platnosti pro data geografické hodnoty. Pokud není zadán, data budou nikdy vypršet a jsou vždy platná.
* `expiredTime` Je datum a čas vypršení platnosti dat geografických zón. Pokud je hodnota `userTime` v žádosti pozdější než tato hodnota, považují se odpovídající data o geografickou část považována za data s vypršenou platností a nedotazují se na ně. Na základě toho se geometryId z těchto geografických dat `expiredGeofenceGeometryId` do pole v rámci reakce na geografické ploty.
* `validityPeriod` Je seznam časových období platnosti geografické zóny. Pokud hodnota `userTime` v žádosti spadá mimo období platnosti, považují se odpovídající data o geografickou oblast za neplatnou a nebudou se dotazovat. GeometryId těchto geografických dat je součástí `invalidPeriodGeofenceGeometryId` pole v rámci reakce na geografické ploty. V následující tabulce jsou uvedeny vlastnosti elementu validityPeriod.

| Name | type | Povinné  | Popis |
| :------------ |:------------: |:---------------:| :-----|
| startTime | Datetime  | true | Datum a čas zahájení období platnosti. |
| endTime   | Datetime  | true |  Datum a čas konce období platnosti. |
| recurrenceType | řetězec | false |   Typ opakování období. Hodnota může být `Daily`, `Weekly`, `Monthly`nebo. `Yearly` Výchozí hodnota je `Daily`.|
| businessDayOnly | Logická hodnota | false |  Určuje, jestli jsou data platná jenom během pracovních dnů. Výchozí hodnota je `false`.|


* Všechny hodnoty souřadnic jsou reprezentovány jako [Zeměpisná délka, `WGS84`Zeměpisná šířka] definovaná v.
* Pro každou funkci, která obsahuje `MultiPoint`, `MultiLineString`, `MultiPolygon` nebo `GeometryCollection`, jsou vlastnosti aplikovány na všechny prvky. například: Všechny body v `MultiPoint` nástroji budou používat stejný poloměr pro vytvoření více geografických paprsků.
* V bodovém scénáři je možné znázornit geometrii kruhu pomocí `Point` objektu geometrie s vlastnostmi, které jsou vypracované v rámci rozšíření geografického [geometriíového formátu JSON](https://docs.microsoft.com/azure/azure-maps/extend-geojson).      

Následuje ukázkový text žádosti o geografickou ochranou, která je vyjádřena jako geometrie geografického `GeoJSON` tónu v používání středového bodu a poloměru. Platné období dat o geografickosti začíná od 2018-10-22 9:00 do 17:00, opakuje se každý den s výjimkou víkendu. `expiredTime`označuje, že tato data geografické plotu budou považována `userTime` za prošlá, pokud je `2019-01-01`v žádosti později než.  

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
