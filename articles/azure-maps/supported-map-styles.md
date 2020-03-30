---
title: Podporované mapové styly | Mapy Microsoft Azure
description: V tomto článku se dozvíte o různých stylech vykreslování map podporovaných Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 2eafe3c16a89723d55ec52fde785e9ec69e45e0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334039"
---
# <a name="azure-maps-supported-map-styles"></a>Podporované styly map Azure Maps
Azure Maps podporuje několik různých předdefinovaných stylů map, jak je popsáno níže.

## <a name="road"></a>Silnici
**Cestovní** mapa je standardní mapa, která zobrazuje silnice, přírodní a umělé prvky spolu s popisky těchto funkcí.

![styl silniční mapy](./media/supported-map-styles/road.png)

**Příslušná přístupová pravidla:**
* [Obrázek mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Dlaždice mapy](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Ovládací prvek mapy sady Web SDK
* Ovládání mapy Android

## <a name="blank-and-blank_accessible"></a>prázdné a blank_accessible

**Prázdné** a **blank_accessible** styly mapy poskytují prázdné plátno, na kterém můžete vizualizovat data. Styl **blank_accessible** bude i nadále poskytovat aktualizace čtečky obrazovky s podrobnostmi o poloze mapy, i když se základní mapa nezobrazí.

> [!Note]
> Ve webové sdk můžete změnit barvu pozadí mapy nastavením `background-color` CSS styl mapy DIV element.

**Příslušná přístupová pravidla:**
* Ovládací prvek mapy sady Web SDK

## <a name="satellite"></a>satellite 
**Satelitní** styl je kombinací satelitních a leteckých snímků.

![styl mapy satelitních dlaždic](./media/supported-map-styles/satellite.png)

**Příslušná přístupová pravidla:**
* [Satelitní dlaždice](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Ovládací prvek mapy sady Web SDK
* Ovládání mapy Android

## <a name="satellite_road_labels"></a>satellite_road_labels
Tento styl mapy je hybridem silnic a štítků překrytých na satelitních a leteckých snímcích.

![satellite_road_labels styl mapy](./media/supported-map-styles/satellite-road-labels.png)

**Příslušná přístupová pravidla:**
* Ovládací prvek mapy sady Web SDK
* Ovládání mapy Android

## <a name="grayscale_dark"></a>grayscale_dark
**odstíny šedi tmavé** je tmavá verze stylu silniční mapy.

![gray_scale styl mapy](./media/supported-map-styles/grayscale-dark.png)

**Příslušná přístupová pravidla:**
* [Obrázek mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Dlaždice mapy](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Ovládací prvek mapy sady Web SDK 
* Ovládání mapy Android


## <a name="grayscale_light"></a>grayscale_light
**stupně šedi je** světlá verze stylu silniční mapy.

![styl světelné mapy ve stupních šedi](./media/supported-map-styles/grayscale-light.png)

**Příslušná přístupová pravidla:**
* Ovládací prvek mapy sady Web SDK
* Ovládání mapy Android


## <a name="night"></a>Noc
**noc** je tmavá verze stylu silniční mapy s barevnými silnicemi a symboly.

![styl noční mapy](./media/supported-map-styles/night.png)

**Příslušná přístupová pravidla:**
* Ovládací prvek mapy sady Web SDK
* Ovládání mapy Android

## <a name="road_shaded_relief"></a>road_shaded_relief
**silniční stínovaný reliéf** je hlavní styl Azure Maps doplněný o kontury Země.

![stínovaný reliéfní styl mapy](./media/supported-map-styles/shaded-relief.png)

**Příslušná přístupová pravidla:**
* [Dlaždice mapy](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Ovládací prvek mapy sady Web SDK
* Ovládání mapy Android

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark** je tmavý styl mapy s vyšším kontrastem než ostatní styly.

![styl tmavé mapy s vysokým kontrastem](./media/supported-map-styles/high-contrast-dark.png)

**Příslušná přístupová pravidla:**
* Ovládací prvek mapy sady Web SDK

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak nastavit styl mapy v Azure Maps:

> [!div class="nextstepaction"]
> [Výběr stylu mapy](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
