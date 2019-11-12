---
title: Zobrazit informace o funkcích v Android SDK Azure Maps | Microsoft Docs
description: Naučte se zobrazovat informace o funkcích v Android SDK Azure Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 0dcabb0e5141a92394f2be38cbe7e71fa6e03d58
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73928541"
---
# <a name="display-feature-information"></a>Zobrazení informací o funkci

Prostorová data jsou často reprezentovaná pomocí bodů, čar a mnohoúhelníků. Tato data často obsahují informace o metadatech, které jsou k ní přidružené. Například bod může představovat umístění úložiště a metadata o této restauraci může být jeho jméno, adresa a typ potravin, který IT oddělení obsluhuje. Tato metadata lze přidat jako vlastnosti těchto funkcí pomocí `JsonObject`. Následující kód vytvoří jednoduchou funkci Point s vlastností `title`, která má hodnotu "Hello World!".

```java
//Create a data source and add it to the map.
DataSource dataSource = new DataSource();
map.sources.add(dataSource);

//Create a point feature with some properties and add it to the data source.
JsonObject properties = new JsonObject();
properties.addProperty("title", "Hello World!");

//Create a point feature, pass in the metadata properties, and add it to the data source.
dataSource.add(Feature.fromGeometry(Point.fromLngLat(-122.33, 47.64), properties));
```

Když uživatel komunikuje s funkcí na mapě, můžou se události použít k reakci na tyto akce. Běžným scénářem je zobrazení zprávy z vlastností metadat funkce, se kterou uživatel pracuje. Událost `OnFeatureClick` je hlavní událost, která se používá k detekci, kdy uživatel klepne na funkci na mapě. K dispozici je také událost `OnLongFeatureClick`. Při přidávání události `OnFeatureClick` do mapy může být omezena na jednu vrstvu předáním ID vrstvy, aby byla omezena. Pokud není předáno žádné ID vrstvy, klepněte na mapě na libovolné funkci, bez ohledu na to, ve které vrstvě se nachází, by tato událost mohla vyvolat. Následující kód vytvoří vrstvu symbolů pro vykreslení dat bodu na mapě, poté přidá událost `OnFeatureClick` a omezí ji na tuto vrstvu symbolů.

```java
//Create a symbol and add it to the map.
SymbolLayer symbolLayer = new SymbolLayer(dataSource);
map.layers.add(symbolLayer);

//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).properties().get("title").getAsString();

    //Do something with the message.
}, symbolLayer.getId());    //Limit this event to the symbol layer.
```

## <a name="display-a-toast-message"></a>Zobrazit informační zprávy

Zpráva informační zprávy je jedním z nejjednodušších způsobů zobrazení informací uživateli a je k dispozici ve všech verzích Androidu. Nepodporuje žádný typ vstupu uživatele a zobrazuje se jenom po krátkou dobu. Pokud chcete uživateli rychle informovat o tom, co se na něj klepnuli, může být informační zpráva vhodná. Následující kód ukazuje, jak lze v události `OnFeatureClick` použít informační zprávu.

```java
//Add a feature click event to the map.
map.events.add((OnFeatureClick) (features) -> {
    //Retrieve the title property of the feature as a string.
    String msg = features.get(0).getStringProperty("title");

    //Display a toast message with the title information.
    Toast.makeText(this, msg, Toast.LENGTH_SHORT).show();
}, symbolLayer.getId());    //Limit this event to the symbol layer.
```

<center>

![animace používané funkce a zobrazená zpráva informační zprávy](./media/display-feature-information-android/symbol-layer-click-toast-message.gif)</center>

Kromě informačních zpráv existuje mnoho dalších způsobů, jak prezentovat vlastnosti metadat funkce, jako například:

- [Widget Snakbar](https://developer.android.com/training/snackbar/showing.html) – Snackbars poskytuje nezjednodušenou zpětnou vazbu k operaci. Zobrazují v dolní části obrazovky na mobilním zařízení stručnou zprávu a na větších zařízeních zbývá dole. Snackbars se zobrazí nad všemi ostatními prvky na obrazovce a v jednu chvíli může být zobrazen pouze jeden.
- [Dialogová](https://developer.android.com/guide/topics/ui/dialogs) okna – dialogové okno je malé okno s výzvou, aby uživatel zadal rozhodnutí nebo zavedl Další informace. Dialogové okno neplní obrazovku a obvykle se používá pro modální události, které vyžadují, aby uživatelé před pokračováním prohlédli akci.
- Přidá [fragment](https://developer.android.com/guide/components/fragments) do aktuální aktivity.
- Přejděte k jiné aktivitě nebo zobrazení.

## <a name="next-steps"></a>Další kroky

Přidání dalších dat do mapy:

> [!div class="nextstepaction"]
> [Přidat vrstvu symbolů](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Přidání obrazců na mapu pro Android](how-to-add-shapes-to-android-map.md)
