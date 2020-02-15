---
title: Formát dat geografického JSON pro geografickou plot | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak připravit data o geografickosti, která je možné použít ve službě Microsoft Azure Maps GET a POST API pro geografické rozvržení.
author: farah-alyasari
ms.author: v-faalya
ms.date: 02/14/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 7d1c9a1587771a020f5c9f89e2497a25eb1bba70
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210017"
---
# <a name="geofencing-geojson-data"></a>Geografická data geografických zón

Rozhraní API pro monitorování geografických [zón](/rest/api/maps/spatial/getgeofence) a Azure Maps jejich [vystavování](/rest/api/maps/spatial/postgeofence) vám umožní získat blízkost souřadnic vzhledem k poskytnuté geografické ohrazení nebo sadě plotů. Tento článek podrobně popisuje, jak připravit data o geografickosti, která se dají použít v Azure Maps GET a POST API.

Data pro geografickou nebo množinu geografických plotů jsou reprezentována objektem `Feature` objekt a `FeatureCollection` ve formátu `GeoJSON`, který je definován v [rfc7946](https://tools.ietf.org/html/rfc7946). Kromě toho:

* Typ objektu pro typ "podtyp JSON" může být objekt `Feature` nebo objekt `FeatureCollection`.
* Typ objektu geometrie může být `Point`, `MultiPoint`, `LineString`, `MultiLineString`, `Polygon`, `MultiPolygon`a `GeometryCollection`.
* Všechny vlastnosti funkce by měly obsahovat `geometryId`, která se používá k identifikaci geografického ohraničení.
* Funkce s `Point`, `MultiPoint`, `LineString``MultiLineString` musí obsahovat `radius` ve vlastnostech. hodnota `radius` je měřena v metrech, hodnota `radius` rozsahu od 1 do 10000.
* Funkce s typem geometrie `polygon` a `multipolygon` neobsahuje vlastnost poloměr.
* `validityTime` je volitelná vlastnost, která umožňuje uživateli nastavit dobu platnosti a dobu platnosti pro data geografické služby. Pokud není zadán, data budou nikdy vypršet a jsou vždy platná.
* `expiredTime` je datum a čas vypršení platnosti dat geografických zón. Pokud je hodnota `userTime` v žádosti pozdější než tato hodnota, příslušná data o geografickou ochraně se považují za data, která vypršela, a nedotazují se na ně. V takovém případě se geometryId těchto dat o geografickém prostředí zahrne do pole `expiredGeofenceGeometryId` v rámci odpovědi na základě geografické ploty.
* `validityPeriod` je seznam časových období platnosti geografické zóny. Pokud hodnota `userTime` v žádosti spadá mimo období platnosti, považují se odpovídající data o geografickou oblast jako neplatná a nebudou se dotazovat. GeometryId těchto geografických dat je zahrnutých do pole `invalidPeriodGeofenceGeometryId` v rámci odpovědi geografického ohraničení. V následující tabulce jsou uvedeny vlastnosti elementu validityPeriod.

| Název | Typ | Požadováno  | Popis |
| :------------ |:------------: |:---------------:| :-----|
| startTime | Datum a čas  | true | Datum a čas zahájení období platnosti. |
| endTime   | Datum a čas  | true |  Datum a čas konce období platnosti. |
| recurrenceType | řetězec | false |   Typ opakování období. Hodnota může být `Daily`, `Weekly`, `Monthly`nebo `Yearly`. Výchozí hodnota je `Daily`.|
| businessDayOnly | Logická hodnota | false |  Určuje, jestli jsou data platná jenom během pracovních dnů. Výchozí hodnota je `false`.|


* Všechny hodnoty souřadnic jsou reprezentovány jako [Zeměpisná délka, zeměpisná šířka] definované v `WGS84`.
* Pro každou funkci, která obsahuje `MultiPoint`, `MultiLineString`, `MultiPolygon` nebo `GeometryCollection`, jsou vlastnosti aplikovány na všechny prvky. například: všechny body v `MultiPoint` použijí stejný poloměr pro vytvoření více geografických paprsků.
* V bodovém scénáři je možné znázornit kruhovou geometrii pomocí objektu geometrie `Point` s vlastnostmi, které jsou vypracované v rozšíření geografického formátu [JSON geometrií](https://docs.microsoft.com/azure/azure-maps/extend-geojson).      

Následuje ukázkový text žádosti o geografickou ochranou, která je vyjádřena jako geometrie geografického tónu v `GeoJSON` pomocí středového bodu a poloměru. Platné období dat o geografickosti začíná od 2018-10-22 9:00 do 17:00, opakuje se každý den s výjimkou víkendu. `expiredTime` označuje, že tato data geografické plotu budou považována za prošlá, pokud je `userTime` v žádosti pozdější než `2019-01-01`.  

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
