---
title: Vykreslení osnovy
description: Vysvětluje, jak provést vykreslování osnovy výběru
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 8b52dbe8cd12e51c42677ce37acbd57ad551ec50
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680827"
---
# <a name="outline-rendering"></a>Vykreslení osnovy

Vybrané objekty lze vizuálně zvýraznit přidáním vykreslování osnovy prostřednictvím [komponenty Přepsání hierarchického stavu](../../overview/features/override-hierarchical-state.md). Tato kapitola vysvětluje, jak se mění globální parametry pro vykreslování osnovy prostřednictvím klientského rozhraní API.

Vlastnosti osnovy jsou globální nastavení. Všechny objekty, které používají vykreslování osnovy, budou používat stejné nastavení – není možné použít barvu obrysu pro každý objekt.

## <a name="parameters-for-outlinesettings"></a>Parametry pro`OutlineSettings`

Třída `OutlineSettings` obsahuje nastavení související s vlastnostmi globálního obrysu. Poskytuje následující členy:

| Parametr      | Typ    | Popis                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | Barva4Ub | Barva, která se používá pro kreslení obrysu. Alfa část je ignorována.         |
| `PulseRateHz`    | float   | Rychlost, s jakou obrys osciluje za sekundu|
| `PulseIntensity` | float   | Intenzita efektu obrysového pulzu. Musí být mezi 0,0 pro žádné pulzování a 1,0 pro plné pulzování. Intenzita implicitně nastaví minimální krytí `MinOpacity = 1.0 - PulseIntensity`osnovy jako . |

![Obrysy](./media/outlines.png) Efekt změny `color` parametru ze žluté (vlevo) na purpurovou (uprostřed) a `pulseIntensity` z 0 na 0,8 (vpravo).

## <a name="example"></a>Příklad

Následující kód ukazuje příklad pro nastavení parametrů osnovy prostřednictvím rozhraní API:

``` cs
void SetOutlineParameters(AzureSession session)
{
    OutlineSettings outlineSettings = session.Actions.OutlineSettings;
    outlineSettings.Color = new Color4Ub(255, 255, 0, 255);
    outlineSettings.PulseRateHz = 2.0f;
    outlineSettings.PulseIntensity = 0.5f;
}
```

## <a name="performance"></a>Výkon

Vykreslování osnovy může mít významný vliv na výkon vykreslování. Tento dopad se liší v závislosti na prostorovém vztahu prostoru obrazovky mezi vybranými a nevybranými objekty pro daný snímek.

## <a name="next-steps"></a>Další kroky

* [Součást přepsání hierarchického stavu](../../overview/features/override-hierarchical-state.md)
