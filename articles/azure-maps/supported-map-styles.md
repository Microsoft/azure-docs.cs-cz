---
title: Podporované styly mapy | Mapy Microsoft Azure
description: V tomto článku se dozvíte o různých stylech vykreslování map podporovaných Microsoft Azure Maps.
author: farah-alyasari
ms.author: v-faalya
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: a65f20e04603403a1d3a180e364626d5b4440661
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77664483"
---
# <a name="azure-maps-supported-map-styles"></a>Azure Maps podporované styly mapy
Azure Maps podporuje několik různých vestavěných stylů mapy, jak je popsáno níže.

## <a name="road"></a>silniční
Mapa **cest** je standardní mapa, která zobrazuje cesty, přirozené a umělé funkce spolu s popisky pro tyto funkce.

![typ mapy cest](./media/supported-map-styles/road.png)

**Platná rozhraní API:**
* [Obrázek mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Dlaždice mapy](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Mapový ovládací prvek sady web SDK
* Ovládací prvek mapy pro Android

## <a name="blank-and-blank_accessible"></a>prázdné a blank_accessible

**Prázdné** a **blank_accessible** styly mapy poskytují prázdné plátno, na kterém se data vizualizují. Styl **blank_accessible** bude dál poskytovat aktualizace čtečky obrazovky s podrobnostmi o umístění mapy, i když základní mapa není zobrazená.

> [!Note]
> V sadě web SDK můžete změnit barvu pozadí mapy nastavením stylu CSS `background-color` stylu elementu DIV mapy.

**Platná rozhraní API:**
* Mapový ovládací prvek sady web SDK

## <a name="satellite"></a>satelitní 
**Satelitní** styl je kombinací satelitních a leteckých snímků.

![styl mapy dlaždice satelitu](./media/supported-map-styles/satellite.png)

**Platná rozhraní API:**
* [Satelitní dlaždice](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Mapový ovládací prvek sady web SDK
* Ovládací prvek mapy pro Android

## <a name="satellite_road_labels"></a>satellite_road_labels
Tento styl mapy je hybridem cest a popisků, které se překrývají na družicovém a leteckém mikrosnímků.

![satellite_road_labels styl mapy](./media/supported-map-styles/satellite-road-labels.png)

**Platná rozhraní API:**
* Mapový ovládací prvek sady web SDK
* Ovládací prvek mapy pro Android

## <a name="grayscale_dark"></a>grayscale_dark
**tmavě šedá** je tmavá verze stylu mapy cest.

![gray_scale styl mapy](./media/supported-map-styles/grayscale-dark.png)

**Platná rozhraní API:**
* [Obrázek mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Dlaždice mapy](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Mapový ovládací prvek sady web SDK 
* Ovládací prvek mapy pro Android


## <a name="grayscale_light"></a>grayscale_light
**šedé světlo** je světlá verze stylu mapy cest.

![světlý styl mapy ve stupních šedi](./media/supported-map-styles/grayscale-light.png)

**Platná rozhraní API:**
* Mapový ovládací prvek sady web SDK
* Ovládací prvek mapy pro Android


## <a name="night"></a>Veletrh
**noční** je tmavá verze stylu mapy cest s barevnými cestami a symboly.

![styl noční mapy](./media/supported-map-styles/night.png)

**Platná rozhraní API:**
* Mapový ovládací prvek sady web SDK
* Ovládací prvek mapy pro Android

## <a name="road_shaded_relief"></a>road_shaded_relief
**odlehčené vybarvené z provozu** je Azure Maps hlavní styl dokončený pomocí kokolace země.

![styl mapy reliéfu ve stínovém tvaru](./media/supported-map-styles/shaded-relief.png)

**Platná rozhraní API:**
* [Dlaždice mapy](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Mapový ovládací prvek sady web SDK
* Ovládací prvek mapy pro Android

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark** je tmavý styl mapy s vyšším kontrastem než ostatní styly.

![styl tmavé mapy s vysokým kontrastem](./media/supported-map-styles/high-contrast-dark.png)

**Platná rozhraní API:**
* Mapový ovládací prvek sady web SDK

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak nastavit styl mapy v Azure Maps:

> [!div class="nextstepaction"]
> [Zvolit styl mapy](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
