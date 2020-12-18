---
title: Začínáme s ovládacím prvkem pro mapování Androidu | Mapy Microsoft Azure
description: Seznámení se Azure Maps Android SDK. Podívejte se, jak vytvořit projekt v Android Studio, jak nainstalovat sadu SDK a vytvořit interaktivní mapu.
author: rbrundritt
ms.author: richbrun
ms.date: 12/10/2020
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: mvc
ms.openlocfilehash: a7533e079ca13f8ac891fa96f11f740a21c1a3dc
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680358"
---
# <a name="getting-started-with-azure-maps-android-sdk"></a>Začínáme s Azure Maps Android SDK

Azure Maps Android SDK je knihovna vektorových map pro Android. Tento článek vás provede procesy instalace Azure Maps Android SDK a načtení mapy.

## <a name="prerequisites"></a>Požadavky

Ujistěte se, že jste dokončili kroky v [rychlém startu: vytvoření dokumentu aplikace pro Android](quick-android-map.md) .

## <a name="localizing-the-map"></a>Lokalizace mapy

Azure Maps Android SDK poskytuje tři různé způsoby nastavení jazyka a regionálního zobrazení mapy. Následující kód ukazuje, jak nastavit jazyk na francouzštinu ("fr-FR") a místní zobrazení na "auto".

První možností je předat jazyk a zobrazit regionální informace do `AzureMaps` třídy pomocí globálně statických `setLanguage` a `setView` metod. Tím se nastaví výchozí jazyk a místní zobrazení ve všech Azure Mapsch ovládacích prvcích načtených ve vaší aplikaci.

```java
static {
    //Set your Azure Maps Key.
    AzureMaps.setSubscriptionKey("<Your Azure Maps Key>");

    //Set the language to be used by Azure Maps.
    AzureMaps.setLanguage("fr-FR");

    //Set the regional view to be used by Azure Maps.
    AzureMaps.setView("Auto");
}
```

Druhou možností je předat jazyk a zobrazit informace do XML mapového ovládacího prvku.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/myMap"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_language="fr-FR"
    app:mapcontrol_view="Auto"
    />
```

Třetí možností je programově nastavit jazyk a regionální zobrazení mapy pomocí `setStyle` metody map. To lze provést kdykoli, chcete-li změnit jazyk a regionální zobrazení mapy.

```java
mapControl.onReady(map -> {
    map.setStyle(
        language("fr-FR"),
        view("Auto")
    );
});
```

Tady je příklad Azure Maps s jazykem, který je nastaven na "fr-FR" a místní zobrazení nastavené na "auto".

![Azure Maps, obrázek mapy znázorňující popisky ve francouzštině](media/how-to-use-android-map-control-library/android-localization.png)

Úplný seznam podporovaných jazyků a regionálních zobrazení je popsán [zde](supported-languages.md).

## <a name="navigating-the-map"></a>Navigace v mapě

Existuje několik různých způsobů, jak lze mapu zvětšit, vytočit, otočit a rozteč. Následující podrobnosti jsou všechny různými způsoby navigace v mapě.

**Zvětšit mapu**

* Připojte se k mapě pomocí dvou prsty a gesto roztažení prstů společně, abyste se přiblížili nebo rozdělili prsty.
* Poklepáním na mapu přiblížíte jednu úroveň.
* Poklikáním na dva prsty přiblížíte rozvržení na jednu úroveň.
* Klepněte dvakrát; při druhém klepněte na mapu na mapě a přetažením nahoru nebo dolů zmenšete zobrazení.

**Posouvání mapy**

* Najeďte na mapu a táhněte v libovolném směru.

**Otočit mapu**

* Dotkněte se mapy pomocí dvou prsty a otočení.

**Rozteč mapy**

* Dotkněte se mapy dvěma prsty a přetáhněte je dohromady nahoru nebo dolů.

## <a name="azure-government-cloud-support"></a>Podpora cloudu Azure Government

Azure Maps Android SDK podporuje cloud Azure Government. K Android SDK Azure Maps se dostanete ze stejného úložiště Maven. Aby bylo možné se připojit ke cloudové verzi Azure Government Azure Maps platformy, bude nutné provést následující úlohy.

Na stejném místě, kde jsou zadány podrobnosti ověření Azure Maps, přidejte následující řádek kódu, který oznámí mapě použití cloudové domény Azure Maps státní správy.

```java
AzureMaps.setDomain("atlas.azure.us");
```

Při ověřování map a služeb nezapomeňte použít podrobnosti ověřování Azure Maps z Azure Government cloudové platformy.

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak přidat překryvná data na mapu:

> [!div class="nextstepaction"]
> [Správa ověřování v Azure Maps](how-to-manage-authentication.md)

> [!div class="nextstepaction"]
> [Změna stylů mapy v doplňkech Android Maps](set-android-map-styles.md)

> [!div class="nextstepaction"]
> [Přidání vrstvy symbolů](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Přidání řádkové vrstvy](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Přidání mnohoúhelníkové vrstvy](how-to-add-shapes-to-android-map.md)
