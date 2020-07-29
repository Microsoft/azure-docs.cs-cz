---
title: Podporované integrované styly Azure Maps mapy
description: V tomto článku se seznámíte s různými typy integrovaných map, které podporuje Microsoft Azure Maps.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/24/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: b9641fe647eebae020ee67ee19a9cc131a487a7c
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87286879"
---
# <a name="azure-maps-supported-built-in-map-styles"></a>Azure Maps podporují předdefinované styly map.

Azure Maps podporuje několik různých vestavěných stylů mapy, jak je popsáno níže.

## <a name="road"></a>silniční

Mapa **cest** je standardní mapa, která zobrazuje cesty. Zobrazuje také přirozené a umělé funkce a štítky pro tyto funkce.

![typ mapy cest](./media/supported-map-styles/road.png)

**Platná rozhraní API:**

* [Obrázek mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Dlaždice mapy](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Mapový ovládací prvek sady web SDK
* Ovládací prvek mapy pro Android
* Vizuál Power BI

## <a name="blank-and-blank_accessible"></a>prázdné a blank_accessible

**Prázdné** a **blank_accessible** styly mapy poskytují prázdné plátno pro vizualizaci dat. Styl **blank_accessible** bude dál poskytovat aktualizace čtečky obrazovky s podrobnostmi o umístění mapy, i když základní mapa není zobrazená.

> [!Note]
> V sadě web SDK můžete změnit barvu pozadí mapy nastavením `background-color` stylu CSS prvku div mapy.

**Platná rozhraní API:**

* Mapový ovládací prvek sady web SDK

## <a name="satellite"></a>satellite

**Satelitní** styl je kombinací satelitních a leteckých snímků.

![styl mapy dlaždice satelitu](./media/supported-map-styles/satellite.png)

**Platná rozhraní API:**

* [Satelitní dlaždice](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytilepreview)
* Mapový ovládací prvek sady web SDK
* Ovládací prvek mapy pro Android
* Vizuál Power BI

## <a name="satellite_road_labels"></a>satellite_road_labels

Tento styl mapy je hybridem cest a popisků, které se překrývají na družicovém a leteckém mikrosnímků.

![satellite_road_labels styl mapy](./media/supported-map-styles/satellite-road-labels.png)

**Platná rozhraní API:**

* Mapový ovládací prvek sady web SDK
* Ovládací prvek mapy pro Android
* Vizuál Power BI

## <a name="grayscale_dark"></a>grayscale_dark

**tmavě šedá** je tmavá verze stylu mapy cest.

![gray_scale styl mapy](./media/supported-map-styles/grayscale-dark.png)

**Platná rozhraní API:**

* [Obrázek mapy](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Dlaždice mapy](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Mapový ovládací prvek sady web SDK
* Ovládací prvek mapy pro Android
* Vizuál Power BI

## <a name="grayscale_light"></a>grayscale_light

**šedé světlo** je světlá verze stylu mapy cest.

![světlý styl mapy ve stupních šedi](./media/supported-map-styles/grayscale-light.png)

**Platná rozhraní API:**
* Mapový ovládací prvek sady web SDK
* Ovládací prvek mapy pro Android
* Vizuál Power BI

## <a name="night"></a>veletrh

**noční** je tmavá verze stylu mapy cest s barevnými cestami a symboly.

![styl noční mapy](./media/supported-map-styles/night.png)

**Platná rozhraní API:**

* Mapový ovládací prvek sady web SDK
* Ovládací prvek mapy pro Android
* Vizuál Power BI

## <a name="road_shaded_relief"></a>road_shaded_relief

**odlehčené vybarvené z provozu** je Azure Maps hlavní styl dokončený pomocí kokolace země.

![styl mapy reliéfu ve stínovém tvaru](./media/supported-map-styles/shaded-relief.png)

**Platná rozhraní API:**

* [Dlaždice mapy](https://docs.microsoft.com/rest/api/maps/render/getmaptile)
* Mapový ovládací prvek sady web SDK
* Ovládací prvek mapy pro Android
* Vizuál Power BI

## <a name="high_contrast_dark"></a>high_contrast_dark

**high_contrast_dark** je tmavý styl mapy s vyšším kontrastem než ostatní styly.

![styl tmavé mapy s vysokým kontrastem](./media/supported-map-styles/high-contrast-dark.png)

**Platná rozhraní API:**

* Mapový ovládací prvek sady web SDK
* Vizuál Power BI

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak nastavit styl mapy v Azure Maps:

> [!div class="nextstepaction"]
> [Výběr stylu mapy](https://docs.microsoft.com/azure/azure-maps/choose-map-style)
