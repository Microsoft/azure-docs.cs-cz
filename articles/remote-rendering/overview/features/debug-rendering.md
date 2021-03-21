---
title: Ladění vykreslování
description: Přehled efektů vykreslování ladění na straně serveru
author: jumeder
ms.author: jumeder
ms.date: 06/15/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: f6d79f41843069fe6cafe1fa1358ac6c1aab12e6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99591731"
---
# <a name="debug-rendering"></a>Ladění vykreslování

Rozhraní API pro vykreslování ladění poskytuje řadu globálních možností pro změnu vykreslování na straně serveru s různými efekty ladění.

## <a name="available-debug-rendering-effects"></a>Dostupné efekty vykreslování ladění

|Nastavení                          | Účinek                               |
|---------------------------------|:-------------------------------------|
|Čítač rámců                    | Vykreslí překryv textu do levého horního rohu rámečku. Text zobrazuje aktuální ID rámce na straně serveru, které se průběžně zvětšuje podle toho, jak vykreslování pokračuje. |
|Počet mnohoúhelníků                    | Vykreslí překryv textu do levého horního rohu rámečku. Text zobrazuje aktuálně vykreslené množství mnohoúhelníků, stejnou hodnotu jako dotazování dotazů na výkon na [straně serveru](performance-queries.md) .| 
|Drátový                        | Pokud je povoleno, všechny geometrie objektů načtené na serveru budou vykresleny v režimu drátěného modelu. V tomto režimu budou rastry pouze hrany mnohoúhelníků. |

Následující kód povoluje tyto efekty ladění:

```cs
void EnableDebugRenderingEffects(RenderingSession session, bool highlight)
{
    DebugRenderingSettings settings = session.Connection.DebugRenderingSettings;

    // Enable frame counter text overlay on the server side rendering
    settings.RenderFrameCount = true;

    // Enable polygon count text overlay on the server side rendering
    settings.RenderPolygonCount = true;

    // Enable wireframe rendering of object geometry on the server
    settings.RenderWireframe = true;
}
```

```cpp
void EnableDebugRenderingEffects(ApiHandle<RenderingSession> session, bool highlight)
{
    ApiHandle<DebugRenderingSettings> settings = session->Connection()->GetDebugRenderingSettings();

    // Enable frame counter text overlay on the server side rendering
    settings->SetRenderFrameCount(true);

    // Enable polygon count text overlay on the server side rendering
    settings->SetRenderPolygonCount(true);

    // Enable wireframe rendering of object geometry on the server
    settings->SetRenderWireframe(true);
}
```

![Ladění vykreslování](./media/debug-rendering.png)

> [!NOTE]
> Všechny efekty vykreslování ladění jsou globální nastavení, která mají vliv na celý rámec.

## <a name="use-cases"></a>Případy použití

Rozhraní API pro vykreslování ladění je určené pro jednoduché úlohy ladění, jako je ověření, že připojení služby je skutečně spuštěné a funguje správně. Možnosti vykreslování textu mají přímý vliv na video snímky v datovém proudu. Když je povolíte, ověří se, jestli se přijímají nové rámce a správně dekóduje video.

Poskytnuté účinky však neposkytují žádné podrobné introspekce stavu služby. Pro tento případ použití se doporučují [dotazy na výkon na straně serveru](performance-queries.md) .

## <a name="performance-considerations"></a>Otázky výkonu

* Povolení překryvných textů se mírně nezvyšuje bez režie výkonu.
* Povolení režimu drátěného modelu má za následek netriviální výkon, i když se může lišit v závislosti na scéně. U složitých scén může tento režim způsobit pokles frekvence snímků pod cílovou 60 až Hz.

## <a name="api-documentation"></a>Dokumentace k rozhraní API

* [C++ RenderingConnection::D ebugRenderingSettings ()](/cpp/api/remote-rendering/renderingconnection#debugrenderingsettings)

## <a name="next-steps"></a>Další kroky

* [Režimy vykreslování](../../concepts/rendering-modes.md)
* [Dotazy na výkon na straně serveru](performance-queries.md)