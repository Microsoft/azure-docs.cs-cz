---
title: Lights
description: Popis a vlastnosti zdroje světla
author: florianborn71
ms.author: flborn
ms.date: 02/10/2020
ms.topic: article
ms.openlocfilehash: 0a4a226af1347b5302b0c3964889fc072f89e7f8
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680944"
---
# <a name="lights"></a>Lights

Ve výchozím nastavení jsou dálkově vykreslené objekty osvětleny pomocí [nebeského světla](sky.md). Pro většinu aplikací je to již dostačující, ale můžete přidat další světelné zdroje do scény.

> [!IMPORTANT]
> Světelné zdroje ovlivňují pouze [materiály PBR.](pbr-materials.md) [Barevné materiály](color-materials.md) se vždy objevují zcela jasné.

> [!NOTE]
> Vrhat stíny není aktuálně podporováno. Vzdálené vykreslování Azure je optimalizované tak, aby vykreslovalo obrovské množství geometrie, a v případě potřeby využívá více gpu. Tradiční přístupy pro vrhání stínů nefungují dobře v těchto scénářích.

## <a name="common-light-component-properties"></a>Společné vlastnosti lehkých součástí

Všechny typy světel jsou odvozeny z abstraktní základní třídy `LightComponent` a sdílejí tyto vlastnosti:

* **Barva:** Barva světla v [gama prostoru](https://en.wikipedia.org/wiki/SRGB). Alfa je ignorována.

* **Intenzita:** Jas světla. U bodových a bodových světel intenzita také definuje, jak daleko světlo svítí.

## <a name="point-light"></a>Bodové světlo

V Azure Remote `PointLightComponent` Rendering může nejen vyzařovat světlo z jednoho bodu, ale také z malé koule nebo malé trubice, aby simuloval měkčí světelné zdroje.

### <a name="pointlightcomponent-properties"></a>Vlastnosti PointLightComponent

* **Poloměr:** Výchozí poloměr je nula, v takovém případě světlo působí jako bodové světlo. Pokud je poloměr větší než nula, působí jako sférický zdroj světla, který mění vzhled odlesků.

* **Délka:** Pokud `Length` jsou `Radius` obě a jsou nenulové, světlo funguje jako trubice světlo. To lze použít k simulaci neonových trubek.

* **ÚtlumCutoff:** Je-li ponecháno na (0,0) útlum světla `Intensity`závisí pouze na jeho . Můžete však zadat vlastní minimální/maximální vzdálenosti, u kterých je intenzita světla lineárně zmenšena na 0. Tuto funkci lze použít k vynucení menšího rozsahu vlivu určitého světla.

* **ProjectedCubemap:** Pokud je nastavena na platnou [mapu krychle](../../concepts/textures.md), textura se promítne na okolní geometrii světla. Barva krychle je modulována barvou světla.

## <a name="spot-light"></a>Bodové světlo

Je `SpotLightComponent` `PointLightComponent` podobný, ale světlo je omezen na tvar kužele. Orientace kužele je definována *zápornou osou z entity vlastníka*.

### <a name="spotlightcomponent-properties"></a>Vlastnosti komponenty SpotLightComponent

* **Poloměr:** Stejné jako `PointLightComponent`u .

* **SpotAngleDeg:** Tento interval definuje vnitřní a vnější úhel kužele měřený ve stupních. Vše ve vnitřním úhlu je osvětleno plným jasem. Pokles se aplikuje směrem k vnějšímu úhlu, který generuje efekt podobný polostínu.

* **FalloffExponent:** Definuje, jak ostře přechody poklesu mezi vnitřním a vnějším úhlem kužele. Vyšší hodnota má za následek ostřejší přechod. Výchozí hodnota 1.0 má za následek lineární přechod.

* **ÚtlumCutoff:** Stejné jako `PointLightComponent`u .

* **Promítnuté2dTextura:** Pokud je nastavena na platnou [2D texturu](../../concepts/textures.md), obraz se promítne na geometrii, na které světlo svítí. Barva textury je modulována barvou světla.

## <a name="directional-light"></a>Směrové světlo

Simuluje `DirectionalLightComponent` světelný zdroj, který je nekonečně daleko. Světlo svítí do směru *záporné osy z vlastníka .* Pozice entity je ignorována.

Neexistují žádné další vlastnosti.

## <a name="performance-considerations"></a>Otázky výkonu

Světelné zdroje mají významný vliv na výkon vykreslování. Používejte je opatrně a pouze v případě, že to aplikace vyžaduje. Jakékoli statické globální světelné podmínky, včetně statické směrové součásti, lze dosáhnout [pomocí vlastní textury oblohy](sky.md)bez dodatečných nákladů na vykreslování.

## <a name="next-steps"></a>Další kroky

* [Materials](../../concepts/materials.md)
* [Obloze](sky.md)
