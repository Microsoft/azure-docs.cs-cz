---
title: Podporován styly mapy ve službě Azure Maps | Dokumentace Microsoftu
description: Podporuje Azure Maps styly mapy
author: walsehgal
ms.author: v-musehg
ms.date: 08/28/2018
ms.topic: concepts
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 04c8f43e3b484ceeb942ae13ea95baf7f0215b53
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344831"
---
# <a name="azure-maps-supported-map-styles"></a>Azure Maps podporován styly mapy
Azure maps podporují čtyři integrované mapovat různé styly. Styly spolu s jejich popisy jsou uvedeny níže.

## <a name="road"></a>Silniční
A **silniční** mapa je standardní mapy, která zobrazuje silnicích zakázána, přirozené a lidmi vybudovaných funkce spolu s popisky pro tyto funkce.

![Silniční](./media/supported-map-styles/road.png)

**Použít rozhraní API:**
* [Obrázek mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Mapovou dlaždici](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Mapový ovládací prvek JS

## <a name="satellite"></a>Satelit 
**Satelitní** style je kombinací satelitní a leteckých snímků.

![Satelit](./media/supported-map-styles/satellite.png)

**Použít rozhraní API:**
* [Satelitní dlaždice](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Mapový ovládací prvek JS

## <a name="satelliteroadlabels"></a>Satellite_Road_Labels
Tento styl mapy je v hybridní kombinaci cest a popisky překrývající nad satelitní a leteckých snímků.

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**Použít rozhraní API:**
* Mapový ovládací prvek JS

## <a name="grayscaledark"></a>Grayscale_Dark
**Tmavý ve stupních šedi** tmavě verze styl silniční mapu.

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**Použít rozhraní API:**
* Mapový ovládací prvek JS 