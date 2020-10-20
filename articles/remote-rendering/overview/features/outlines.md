---
title: Vykreslování obrysů
description: Vysvětluje, jak provést vykreslení osnovy výběru.
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: 55151ab965b13a944fee5517a2ca8b4f9eb64bb0
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92207236"
---
# <a name="outline-rendering"></a>Vykreslování obrysů

Vybrané objekty lze zvýraznit vizuálně přidáním vykreslování osnovy prostřednictvím [komponenty pro přepsání hierarchického stavu](../../overview/features/override-hierarchical-state.md). V této kapitole se dozvíte, jak se globální parametry pro vykreslování osnovy mění prostřednictvím rozhraní API klienta.

Vlastnosti obrysu jsou globální nastavení. Všechny objekty, které používají vykreslování osnovy, budou používat stejné nastavení – není možné použít barvu obrysu pro jednotlivé objekty.

## <a name="parameters-for-outlinesettings"></a>Parametry pro `OutlineSettings`

Třída `OutlineSettings` obsahuje nastavení týkající se globálních vlastností osnovy. Zpřístupňuje následující členy:

| Parametr      | Typ    | Popis                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | Color4Ub | Barva, která se používá k vykreslování obrysu. Část alfa je ignorována.         |
| `PulseRateHz`    | float   | Míra kolísání obrysu za sekundu|
| `PulseIntensity` | float   | Intenzita efektu obrysového impulsu Musí být v rozmezí od 0,0 do žádného pohonu a 1,0 pro plný pohon. Intenzita implicitně nastaví minimální neprůhlednost obrysu jako `MinOpacity = 1.0 - PulseIntensity` . |

![Objekt vykreslený třikrát pomocí různých parametrů obrysu ](./media/outlines.png) má vliv na změnu `color` parametru od žluté (vlevo) na purpurové (střed) a `pulseIntensity` od 0 do 0,8 (vpravo).

## <a name="example"></a>Příklad

Následující kód ukazuje příklad nastavení parametrů osnovy prostřednictvím rozhraní API:

```cs
void SetOutlineParameters(AzureSession session)
{
    OutlineSettings outlineSettings = session.Actions.OutlineSettings;
    outlineSettings.Color = new Color4Ub(255, 255, 0, 255);
    outlineSettings.PulseRateHz = 2.0f;
    outlineSettings.PulseIntensity = 0.5f;
}
```

```cpp
void SetOutlineParameters(ApiHandle<AzureSession> session)
{
    ApiHandle<OutlineSettings> outlineSettings = session->Actions()->GetOutlineSettings();
    Color4Ub outlineColor;
    outlineColor.channels = { 255, 255, 0, 255 };
    outlineSettings->SetColor(outlineColor);
    outlineSettings->SetPulseRateHz(2.0f);
    outlineSettings->SetPulseIntensity(0.5f);
}
```

## <a name="performance"></a>Výkon

Vykreslování osnovy může mít významný dopad na výkon vykreslování. Tento dopad se liší v závislosti na adresním vztahu na úrovni obrazovky mezi vybranými a nevybranými objekty pro daný snímek.

## <a name="api-documentation"></a>Dokumentace k rozhraní API

* [Vlastnost C# RemoteManager. OutlineSettings](/dotnet/api/microsoft.azure.remoterendering.remotemanager.outlinesettings)
* [C++ RemoteManager:: OutlineSettings ()](/cpp/api/remote-rendering/remotemanager#outlinesettings)

## <a name="next-steps"></a>Další kroky

* [Součást přepisu hierarchického stavu](../../overview/features/override-hierarchical-state.md)