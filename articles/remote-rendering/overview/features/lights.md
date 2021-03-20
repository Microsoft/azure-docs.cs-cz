---
title: Osvětlení scény
description: Popis a vlastnosti zdroje světla
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 49027899d66a2192cc311fb4dba66e441155b527
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92206845"
---
# <a name="scene-lighting"></a>Osvětlení scény

Ve výchozím nastavení jsou vzdáleně vykreslované objekty osvětleny pomocí [nebe Light](sky.md). Pro většinu aplikací to již stačí, ale do scény můžete přidat další zdroje světla.

> [!IMPORTANT]
> Zdroje světla mají vliv pouze na [PBR materiály](pbr-materials.md) . [Barevné materiály](color-materials.md) se vždy zobrazují úplně jasně.

> [!NOTE]
> Stíny přetypování se v tuto chvíli nepodporují. Vzdálené vykreslování Azure je optimalizované pro vykreslování obrovských objemů geometrie a v případě potřeby využívá více GPU. Tradiční přístupy k přetypování stínů v takových scénářích nefungují dobře.

## <a name="common-light-component-properties"></a>Obecné vlastnosti komponenty Light

Všechny typy světla jsou odvozeny od abstraktní základní třídy `LightComponent` a sdílejí tyto vlastnosti:

* **Barva:** Barva světla v [prostoru hodnoty gamma](https://en.wikipedia.org/wiki/SRGB) Hodnota alfa je ignorována.

* **Intenzita:** Jas světla. V případě bodových a bodových světel intenzita také definuje, jak daleko světla vzzáří.

## <a name="point-light"></a>Bodová světla

Ve vzdáleném vykreslování Azure `PointLightComponent` nedokáže jenom vysílat světlo z jediného bodu, ale také z malé koule nebo malé trubice pro simulaci měkkých zdrojů světla.

### <a name="pointlightcomponent-properties"></a>Vlastnosti PointLightComponent

* **Poloměr:** Výchozí poloměr je nula. v takovém případě se světlo chová jako bodová svítilna. Pokud je poloměr větší než nula, funguje jako kulové zdroj světla, který mění vzhled odlesků.

* **Délka:** Pokud `Length` jsou a `Radius` jsou nenulové, světlo funguje jako kapilární světlo. Dá se použít k simulaci Neon zkumavek.

* **AttenuationCutoff:** Pokud zbývá (0, 0), oddálení závisí jenom na jeho `Intensity` . Můžete ale zadat vlastní minimální a maximální vzdálenosti, po které se intenzita světla škáluje lineárně dolů na 0. Tato funkce se dá použít k vykonání menšího rozsahu vlivu na konkrétní světlo.

* **ProjectedCubemap:** Pokud je nastavena na platný [cubemap](../../concepts/textures.md), textura je promítnuta do okolní geometrie světla. Barva cubemap je upravována barvou světla.

## <a name="spot-light"></a>Bodový světlo

`SpotLightComponent`Je podobná, `PointLightComponent` ale světlo se omezuje na tvar kužele. Orientace kuželu je definována na základě *záporné osy z entity vlastníka*.

### <a name="spotlightcomponent-properties"></a>Vlastnosti SpotLightComponent

* **Poloměr:** Stejné jako pro `PointLightComponent` .

* **SpotAngleDeg:** Tento interval definuje vnitřní a vnější úhel kužele (měřeno ve stupních). Vše v rámci vnitřního úhlu je osvětlené s úplným jasem. U vnějšího úhlu, který generuje Penumbra podobný efekt, se intenzita.

* **FalloffExponent:** Definuje, jak prudce prochází intenzita dolů mezi vnitřním a vnějším kuželovým úhlem. Vyšší hodnota má za následek ostřejší přechod. Výchozí hodnota 1,0 má za následek lineární přechod.

* **AttenuationCutoff:** Stejné jako pro `PointLightComponent` .

* **Projected2dTexture:** Pokud je nastavena na platnou [2D texturu](../../concepts/textures.md), je obrázek promítnut na geometrii, na které světlo světla pracuje. Barva textury je upravována barvou světla.

## <a name="directional-light"></a>Směrové světlo

`DirectionalLightComponent`Simuluje nekonečně daleko nekonečný zdroj světla. Světlo se rozsvítí do směru *záporné osy z entity Owner*. Pozice entity je ignorována.

Neexistují žádné další vlastnosti.

## <a name="performance-considerations"></a>Otázky výkonu

Zdroje světla mají významný dopad na výkon vykreslování. Používejte je pečlivě a jenom v případě, že to vyžaduje aplikace. Jakákoli statická globální podmínka osvětlení, včetně statické směrové komponenty, se dá dosáhnout s [vlastní texturou nebe](sky.md)bez dalších nákladů na vykreslování.

## <a name="api-documentation"></a>Dokumentace k rozhraní API

* [Třída C# LightComponentBase](/dotnet/api/microsoft.azure.remoterendering.lightcomponentbase)
* [Třída C# PointLightComponent](/dotnet/api/microsoft.azure.remoterendering.pointlightcomponent)
* [Třída C# SpotLightComponent](/dotnet/api/microsoft.azure.remoterendering.spotlightcomponent)
* [Třída C# DirectionalLightComponent](/dotnet/api/microsoft.azure.remoterendering.directionallightcomponent)
* [Třída C++ LightComponentBase](/cpp/api/remote-rendering/lightcomponentbase)
* [Třída C++ PointLightComponent](/cpp/api/remote-rendering/pointlightcomponent)
* [Třída C++ SpotLightComponent](/cpp/api/remote-rendering/spotlightcomponent)
* [Třída C++ DirectionalLightComponent](/cpp/api/remote-rendering/directionallightcomponent)

## <a name="next-steps"></a>Další kroky

* [Materiály](../../concepts/materials.md)
* [Cestovní](sky.md)