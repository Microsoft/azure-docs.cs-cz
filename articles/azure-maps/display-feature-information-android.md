---
title: Zobrazení informací o funkcích v sadě Azure Maps Android SDK | Mapy Microsoft Azure
description: V tomto článku se dozvíte, jak zobrazit informace o funkcích na mapě pomocí sady Microsoft Azure Maps Android SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 08/08/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 26f41a7fd88a3c2018592e89ae95e3b962c1a9e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75911697"
---
# <a name="display-feature-information"></a>Zobrazení informací o funkci

Prostorová data jsou často reprezentována pomocí bodů, čar a polygonů. Tato data mají často k tomu spojené informace o metadatech. Bod může například představovat umístění úložiště a metadata o této restauraci může být jeho název, adresa a typ jídla, které slouží. Tato metadata lze přidat jako vlastnosti `JsonObject`těchto funkcí pomocí . Následující kód vytvoří jednoduchý bod `title` funkce s vlastností, která má hodnotu "Hello World!"

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

Když uživatel interaguje s funkcí na mapě, události lze použít k reakci na tyto akce. Běžným scénářem je zobrazení zprávy z řad vlastností metadat funkce, se kterou uživatel interagoval. Událost `OnFeatureClick` je hlavní událostí používanou k detekci, kdy uživatel poklepal na funkci na mapě. Je tu také `OnLongFeatureClick` událost. Při přidávání `OnFeatureClick` události do mapy může být omezena na jednu vrstvu předáním ID vrstvy, na kterou se omezí. Pokud není předáno žádné ID vrstvy, klepnutím na libovolnou funkci na mapě bez ohledu na to, ve které vrstvě se nachází, by se tato událost stala. Následující kód vytvoří vrstvu symbolů pro vykreslení dat `OnFeatureClick` bodů na mapě, pak přidá událost a omezí ji na tuto vrstvu symbolů.

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

## <a name="display-a-toast-message"></a>Zobrazení informační zprávy

Informační zpráva je jedním z nejjednodušších způsobů zobrazení informací uživateli a je k dispozici ve všech verzích systému Android. Nepodporuje žádný typ uživatelského vstupu a zobrazuje se pouze na krátkou dobu. Pokud chcete uživateli rychle dát vědět něco o tom, na co poklepal, může být dobrou volbou informační zpráva. Následující kód ukazuje, jak lze informační `OnFeatureClick` zprávu použít s událostí.

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

![Animace funkce, která je poklepávána, a zobrazená informační zpráva](./media/display-feature-information-android/symbol-layer-click-toast-message.gif)</center>

Kromě informačních zpráv existuje mnoho dalších způsobů, jak prezentovat vlastnosti metadat funkce, například:

- [Widget Snakbar](https://developer.android.com/training/snackbar/showing.html) - Snackbars poskytují lehkou zpětnou vazbu o operaci. Zobrazují krátkou zprávu v dolní části obrazovky na mobilních zařízeních a vlevo dole na větších zařízeních. Snackbars se objeví nad všemi ostatními prvky na obrazovce a pouze jeden může být zobrazen najednou.
- [Dialogy](https://developer.android.com/guide/topics/ui/dialogs) – Dialogové okno je malé okno, které vyzve uživatele k rozhodnutí nebo k zadání dalších informací. Dialogové okno nevyplňuje obrazovku a obvykle se používá pro modální události, které vyžadují, aby uživatelé před pokračováním akce.
- Přidejte [fragment](https://developer.android.com/guide/components/fragments) k aktuální aktivitě.
- Přejděte na jinou aktivitu nebo zobrazení.

## <a name="next-steps"></a>Další kroky

Přidání dalších dat do mapy:

> [!div class="nextstepaction"]
> [Přidání vrstvy symbolů](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Přidání obrazců do mapy Androidu](how-to-add-shapes-to-android-map.md)
