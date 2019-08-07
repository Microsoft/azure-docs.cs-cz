---
title: Podporované styly mapy v Azure Maps | Microsoft Docs
description: Styly mapy podporované Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 05/06/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 1aad2284c0f64c92efaefe3f9145d95c4aabec67
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839448"
---
# <a name="azure-maps-supported-map-styles"></a>Azure Maps podporované styly mapy
Azure Maps podporuje několik různých vestavěných stylů mapy, jak je popsáno níže.

## <a name="road"></a>silniční
Mapa **cest** je standardní mapa, která zobrazuje cesty, přirozené a umělé funkce spolu s popisky pro tyto funkce.

![silniční](./media/supported-map-styles/road.png)

**Platná rozhraní API:**
* [Obrázek mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Dlaždice mapy](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Mapový ovládací prvek sady web SDK
* Ovládací prvek mapy pro Android

## <a name="blank-and-blank_accessible"></a>prázdné a blank_accessible

**Prázdné** a **blank_accessible** styly map poskytují prázdné plátno, na kterém se data vizualizují. Styl **blank_accessible** bude dál poskytovat aktualizace čtečky obrazovky s podrobnostmi o umístění, kde se mapa nachází, i když základní mapa není zobrazená.

> [!Note]
> V sadě web SDK můžete změnit barvu pozadí mapy nastavením stylu CSS `background-color` prvku div mapy.

**Platná rozhraní API:**
* Mapový ovládací prvek sady web SDK

## <a name="satellite"></a>satelitní 
**Satelitní** styl je kombinací satelitních a leteckých snímků.

![satelitní](./media/supported-map-styles/satellite.png)

**Platná rozhraní API:**
* [Satelitní dlaždice](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Mapový ovládací prvek sady web SDK
* Ovládací prvek mapy pro Android

## <a name="satellite_road_labels"></a>satellite_road_labels
Tento styl mapy je hybridem cest a popisků, které se překrývají na družicovém a leteckém mikrosnímků.

![satellite_road_labels](./media/supported-map-styles/satellite_road_labels.png)

**Platná rozhraní API:**
* Mapový ovládací prvek sady web SDK
* Ovládací prvek mapy pro Android

## <a name="grayscale_dark"></a>grayscale_dark
**tmavě šedá** je tmavá verze stylu mapy cest.

![gray_scale](./media/supported-map-styles/grayscale_dark.png)

**Platná rozhraní API:**
* Mapový ovládací prvek sady web SDK 
* Ovládací prvek mapy pro Android


## <a name="grayscale_light"></a>grayscale_light
**šedé světlo** je světlá verze stylu mapy cest.

![světlo stupňů šedé](./media/supported-map-styles/grayscale_light.png)

**Platná rozhraní API:**
* Mapový ovládací prvek sady web SDK
* Ovládací prvek mapy pro Android


## <a name="night"></a>veletrh
**noční** je tmavá verze stylu mapy cest s barevnými cestami a symboly.

![veletrh](./media/supported-map-styles/night.png)

**Platná rozhraní API:**
* Mapový ovládací prvek sady web SDK
* Ovládací prvek mapy pro Android

## <a name="road_shaded_relief"></a>road_shaded_relief
**odlehčené vybarvené z provozu** je Azure Maps hlavní styl dokončený pomocí kokolace země.

![vybarvený reliéf](./media/supported-map-styles/shaded-relief.png)

**Platná rozhraní API:**
* [Dlaždice mapy](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Mapový ovládací prvek sady web SDK
* Ovládací prvek mapy pro Android
