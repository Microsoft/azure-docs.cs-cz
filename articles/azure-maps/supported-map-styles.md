---
title: Podporován styly mapy ve službě Azure Maps | Dokumentace Microsoftu
description: Podporuje Azure Maps styly mapy
author: walsehgal
ms.author: v-musehg
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: db396ad06bf46cbbaf486696b68393a6a4214c2f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65230829"
---
# <a name="azure-maps-supported-map-styles"></a>Azure Maps podporován styly mapy
Azure Maps podporuje několik různých předdefinovaných Mapovat styly, jak je popsáno níže.

## <a name="road"></a>Silniční
A **silniční** mapa je standardní mapy, která zobrazuje silnicích zakázána, přirozené a umělý funkce spolu s popisky pro tyto funkce.

![Silniční](./media/supported-map-styles/road.png)

**Použít rozhraní API:**
* [Obrázek mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Mapovou dlaždici](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Mapový ovládací prvek JS
* Android mapový ovládací prvek

## <a name="satellite"></a>Satelit 
**Satelitní** style je kombinací satelitní a leteckých snímků.

![Satelit](./media/supported-map-styles/satellite.png)

**Použít rozhraní API:**
* [Satelitní dlaždice](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Mapový ovládací prvek JS
* Android mapový ovládací prvek

## <a name="satelliteroadlabels"></a>satellite_road_labels
Tento styl mapy je v hybridní kombinaci cest a popisky překrývající nad satelitní a leteckých snímků.

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**Použít rozhraní API:**
* Mapový ovládací prvek JS
* Android mapový ovládací prvek

## <a name="grayscaledark"></a>grayscale_dark
**Tmavý ve stupních šedi** tmavě verze styl silniční mapu.

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**Použít rozhraní API:**
* Mapový ovládací prvek JS 
* Android mapový ovládací prvek


## <a name="grayscalelight"></a>grayscale_light
**světle ve stupních šedi** je jednodušší verzi styl silniční mapu.

![světle ve stupních šedi](./media/supported-map-styles/grayscale_light.png)

**Použít rozhraní API:**
* Mapový ovládací prvek JS
* Android mapový ovládací prvek


## <a name="night"></a>noční
**noční** je verze Tmavý styl silniční mapu s barevné cest a symboly.

![noční](./media/supported-map-styles/night.png)

**Použít rozhraní API:**
* Mapový ovládací prvek JS
* Android mapový ovládací prvek

## <a name="roadshadedrelief"></a>road_shaded_relief
**silniční stín pomáhajících** je hlavní styl Azure Maps byla dokončena s obrysy všech koutech světa.

![označeno šedou barvou pomáhajících](./media/supported-map-styles/shaded-relief.png)

**Použít rozhraní API:**
* [Mapovou dlaždici](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Mapový ovládací prvek JS
* Android mapový ovládací prvek
