---
title: Podporován styly mapy ve službě Azure Maps | Dokumentace Microsoftu
description: Podporuje Azure Maps styly mapy
author: walsehgal
ms.author: v-musehg
ms.date: 02/12/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 923b9fef75fa610ee817a86ce0b4d6b322153f01
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56113427"
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

## <a name="satellite"></a>Satelit 
**Satelitní** style je kombinací satelitní a leteckých snímků.

![Satelit](./media/supported-map-styles/satellite.png)

**Použít rozhraní API:**
* [Satelitní dlaždice](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Mapový ovládací prvek JS

## <a name="satelliteroadlabels"></a>satellite_road_labels
Tento styl mapy je v hybridní kombinaci cest a popisky překrývající nad satelitní a leteckých snímků.

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**Použít rozhraní API:**
* Mapový ovládací prvek JS

## <a name="grayscaledark"></a>grayscale_dark
**Tmavý ve stupních šedi** tmavě verze styl silniční mapu.

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**Použít rozhraní API:**
* Mapový ovládací prvek JS 

## <a name="night"></a>noční
**noční** je verze Tmavý styl silniční mapu s barevné cest a symboly.

![noční](./media/supported-map-styles/night.png)

**Použít rozhraní API:**
* Mapový ovládací prvek JS

## <a name="roadshadedrelief"></a>road_shaded_relief
**silniční stín pomáhajících** je hlavní styl Azure Maps byla dokončena s obrysy všech koutech světa.

![označeno šedou barvou pomáhajících](./media/supported-map-styles/shaded-relief.png)

**Použít rozhraní API:**
* Mapový ovládací prvek JS
* [Mapovou dlaždici](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
