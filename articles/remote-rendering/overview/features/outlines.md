---
title: Vykreslování obrysů
description: Vysvětluje, jak provést vykreslení osnovy výběru.
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: c04f2312926d3b6d668dff712eedb57d816c8bf3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99592003"
---
# <a name="outline-rendering"></a>Vykreslování obrysů

Vybrané objekty lze zvýraznit vizuálně přidáním vykreslování osnovy prostřednictvím [komponenty pro přepsání hierarchického stavu](../../overview/features/override-hierarchical-state.md). V této kapitole se dozvíte, jak se globální parametry pro vykreslování osnovy mění prostřednictvím rozhraní API klienta.

Vlastnosti obrysu jsou globální nastavení. Všechny objekty, které používají vykreslování osnovy, budou používat stejné nastavení – není možné použít barvu obrysu pro jednotlivé objekty.

## <a name="parameters-for-outlinesettings"></a>Parametry pro `OutlineSettings`

Třída `OutlineSettings` obsahuje nastavení týkající se globálních vlastností osnovy. Zpřístupňuje následující členy:

| Parametr      | Typ    | Description                                             |
|----------------|---------|---------------------------------------------------------|
| `Color`          | Color4Ub | Barva, která se používá k vykreslování obrysu. Část alfa je ignorována.         |
| `PulseRateHz`    | float   | Míra kolísání obrysu za sekundu|
| `PulseIntensity` | float   | Intenzita efektu obrysového impulsu Musí být v rozmezí od 0,0 do žádného pohonu a 1,0 pro plný pohon. Intenzita implicitně nastaví minimální neprůhlednost obrysu jako `MinOpacity = 1.0 - PulseIntensity` . |

![Objekt vykreslený třikrát pomocí různých parametrů obrysu ](./media/outlines.png) má vliv na změnu `color` parametru od žluté (vlevo) na purpurové (střed) a `pulseIntensity` od 0 do 0,8 (vpravo).

## <a name="example"></a>Příklad

Následující kód ukazuje příklad nastavení parametrů osnovy prostřednictvím rozhraní API:

```cs
void SetOutlineParameters(RenderingSession session)
{
    OutlineSettings outlineSettings = session.Connection.OutlineSettings;
    outlineSettings.Color = new Color4Ub(255, 255, 0, 255);
    outlineSettings.PulseRateHz = 2.0f;
    outlineSettings.PulseIntensity = 0.5f;
}
```

```cpp
void SetOutlineParameters(ApiHandle<RenderingSession> session)
{
    ApiHandle<OutlineSettings> outlineSettings = session->Connection()->GetOutlineSettings();
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

* [Vlastnost C# RenderingConnection. OutlineSettings](/dotnet/api/microsoft.azure.remoterendering.renderingconnection.outlinesettings)
* [C++ RenderingConnection:: OutlineSettings ()](/cpp/api/remote-rendering/renderingconnection#outlinesettings)

## <a name="next-steps"></a>Další kroky

* [Součást přepisu hierarchického stavu](../../overview/features/override-hierarchical-state.md)