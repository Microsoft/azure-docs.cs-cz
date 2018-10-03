---
title: Podporován styly mapy ve službě Azure Maps | Dokumentace Microsoftu
description: Podporuje Azure Maps styly mapy
author: walsehgal
ms.author: v-musehg
ms.date: 10/02/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: c8edaba8de597e3e76e760e1f5109006338a663c
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238816"
---
# <a name="azure-maps-supported-map-styles"></a>Azure Maps podporován styly mapy
Azure maps podporují čtyři integrované mapovat různé styly. Styly s jejich popisy jsou uvedeny níže.

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