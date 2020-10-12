---
title: Zobrazit informace o funkcích v Android SDK Azure Maps | Mapy Microsoft Azure
description: Naučte se zobrazovat informace, když uživatelé komunikují s funkcemi mapy. Pomocí Android SDK Azure Maps můžete zobrazit informační zprávy a jiné typy zpráv.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 44c5f9f1a7c0d014d101ad45a80e1e53c42f69a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88009031"
---
# <a name="display-feature-information"></a>Zobrazení informací o funkci

Prostorová data jsou často reprezentovaná pomocí bodů, čar a mnohoúhelníků. Tato data často obsahují informace o metadatech, které jsou k ní přidružené. Například bod může představovat umístění úložiště a metadata o této restauraci může být jeho jméno, adresa a typ potravin, který IT oddělení obsluhuje. Tato metadata lze přidat jako vlastnosti těchto funkcí pomocí `JsonObject` . Následující kód vytvoří jednoduchou funkci Point s `title` vlastností, která má hodnotu "Hello World!".

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

Když uživatel komunikuje s funkcí na mapě, můžou se události použít k reakci na tyto akce. Běžným scénářem je zobrazení zprávy z vlastností metadat funkce, se kterou uživatel pracuje. `OnFeatureClick`Událost je hlavní událost, která se používá k detekci, kdy uživatel klepne na funkci na mapě. K dispozici je také `OnLongFeatureClick` událost. Při přidávání `OnFeatureClick` události do mapy může být omezena na jednu vrstvu předáním ID vrstvy, na kterou se omezuje. Pokud není předáno žádné ID vrstvy, klepněte na mapě na libovolné funkci, bez ohledu na to, ve které vrstvě se nachází, by tato událost mohla vyvolat. Následující kód vytvoří vrstvu symbolů pro vykreslení dat bodů na mapě, poté přidá `OnFeatureClick` událost a omezí ji na tuto vrstvu symbolů.

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

Zpráva informační zprávy je jedním z nejjednodušších způsobů zobrazení informací uživateli a je k dispozici ve všech verzích Androidu. Nepodporuje žádný typ vstupu uživatele a zobrazuje se jenom po krátkou dobu. Pokud chcete uživateli rychle informovat o tom, co se na něj klepnuli, může být informační zpráva vhodná. Následující kód ukazuje, jak lze v události použít informační zprávy `OnFeatureClick` .

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

![Animace používané funkce a zobrazená zpráva informační zprávy](./media/display-feature-information-android/symbol-layer-click-toast-message.gif)</center>

Kromě informačních zpráv existuje mnoho dalších způsobů, jak prezentovat vlastnosti metadat funkce, jako například:

- [Widget Snakbar](https://developer.android.com/training/snackbar/showing.html) – Snackbars poskytuje nezjednodušenou zpětnou vazbu k operaci. Zobrazují v dolní části obrazovky na mobilním zařízení stručnou zprávu a na větších zařízeních zbývá dole. Snackbars se zobrazí nad všemi ostatními prvky na obrazovce a v jednu chvíli může být zobrazen pouze jeden.
- [Dialogová](https://developer.android.com/guide/topics/ui/dialogs) okna – dialogové okno je malé okno s výzvou, aby uživatel zadal rozhodnutí nebo zavedl Další informace. Dialogové okno neplní obrazovku a obvykle se používá pro modální události, které vyžadují, aby uživatelé před pokračováním prohlédli akci.
- Přidá [fragment](https://developer.android.com/guide/components/fragments) do aktuální aktivity.
- Přejděte k jiné aktivitě nebo zobrazení.

## <a name="next-steps"></a>Další kroky

Přidání dalších dat do mapy:

> [!div class="nextstepaction"]
> [Přidání vrstvy symbolů](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Přidání obrazců na mapu pro Android](how-to-add-shapes-to-android-map.md)
