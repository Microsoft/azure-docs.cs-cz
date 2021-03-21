---
title: Přidání ovládacích prvků do mapy pro Android | Mapy Microsoft Azure
description: Postup přidání ovládacího prvku Lupa, ovládacího prvku sklonu, otočení ovládacího prvku a výběru stylu na mapu v Microsoft Azure Maps Android SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 90d037fc02bdc1c4d6fe682386790561c890c1e6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102100215"
---
# <a name="add-controls-to-a-map-android-sdk"></a>Přidání ovládacích prvků do mapy (Android SDK)

V tomto článku se dozvíte, jak přidat ovládací prvky uživatelského rozhraní do mapy.

## <a name="add-zoom-control"></a>Přidat ovládací prvek Lupa

Ovládací prvek přiblížení přidá tlačítka pro přiblížení a oddálení mapy. Následující ukázka kódu vytvoří instanci `ZoomControl` třídy a přidá ji do mapy.

::: zone pivot="programming-language-java-android"

```java
//Construct a zoom control and add it to the map.
map.controls.add(new ZoomControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a zoom control and add it to the map.
map.controls.add(ZoomControl())
```

::: zone-end

Níže uvedený snímek obrazovky je ovládací prvek přiblížení nahraný na mapě.

![Ovládací prvek přiblížení přidaný do mapy](media/map-add-controls-android/android-zoom-control.jpg)

## <a name="add-pitch-control"></a>Přidat ovládací prvek sklonu

Ovládací prvek sklonu přidá tlačítka pro naklonění sklonu k mapě vzhledem k horizontu. Následující ukázka kódu vytvoří instanci `PitchControl` třídy a přidá ji do mapy.

::: zone pivot="programming-language-java-android"

```java
//Construct a pitch control and add it to the map.
map.controls.add(new PitchControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a pitch control and add it to the map.
map.controls.add(PitchControl())
```

::: zone-end

Níže uvedený snímek obrazovky je ovládací prvek sklonu, který je načten na mapě.

![Přidaný ovládací prvek sklonu k mapě](media/map-add-controls-android/android-pitch-control.jpg)

## <a name="add-compass-control"></a>Přidat ovládací prvek kompas

Ovládací prvek kompas přidá tlačítko pro otočení mapy. Následující ukázka kódu vytvoří instanci `CompassControl` třídy a přidá ji do mapy.

::: zone pivot="programming-language-java-android"

```java
//Construct a compass control and add it to the map.
map.controls.add(new CompassControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a compass control and add it to the map.
map.controls.add(CompassControl())
```

::: zone-end

Snímek obrazovky níže je ovládací prvek kompasu načtený na mapě.

![Ovládací prvek kompas přidaný do mapy](media/map-add-controls-android/android-compass-control.jpg)

## <a name="add-traffic-control"></a>Přidat řízení provozu

Řízení přenosů přidává tlačítko pro přepínání viditelnosti dat přenosů na mapě. Následující ukázka kódu vytvoří instanci `TrafficControl` třídy a přidá ji do mapy.

::: zone pivot="programming-language-java-android"

```java
//Construct a traffic control and add it to the map.
map.controls.add(new TrafficControl());
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Construct a traffic control and add it to the map.
map.controls.add(TrafficControl())
```

::: zone-end

Níže uvedený snímek obrazovky je ovládací prvek přenosu dat načtený na mapě.

![Řízení přenosů přidané do mapy](media/map-add-controls-android/android-traffic-control.jpg)

## <a name="a-map-with-all-controls"></a>Mapa se všemi ovládacími prvky

Do pole lze vložit více ovládacích prvků a přidat je do mapy všechny najednou a umístit ve stejné oblasti mapy pro zjednodušení vývoje. Následující postup přidá na mapu standardní navigační ovládací prvky pomocí tohoto přístupu.

::: zone pivot="programming-language-java-android"

```java
map.controls.add(
    new Control[]{
        new ZoomControl(),
        new CompassControl(),
        new PitchControl(),
        new TrafficControl()
    }
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
map.controls.add(
    arrayOf<Control>(
        ZoomControl(),
        CompassControl(),
        PitchControl(),
        TrafficControl()
    )
)
```

::: zone-end

Níže uvedený snímek obrazovky ukazuje všechny ovládací prvky načtené na mapě. Všimněte si, že pořadí, v jakém jsou přidány na mapu, je pořadí, ve kterém se budou zobrazovat.

![Všechny ovládací prvky přidané do mapy](media/map-add-controls-android/android-all-controls.jpg)

## <a name="next-steps"></a>Další kroky

Další ukázky kódu pro přidání do vašich map najdete v následujících článcích:

> [!div class="nextstepaction"]
> [Přidání vrstvy symbolů](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Přidání vrstvy bublin](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Přidání řádkové vrstvy](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Přidání mnohoúhelníkové vrstvy](how-to-add-shapes-to-android-map.md)
