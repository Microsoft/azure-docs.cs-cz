---
title: Ladění vykreslování
description: Přehled efektů vykreslování ladění na straně serveru
author: jumeder
ms.author: jumeder
ms.date: 06/15/2020
ms.topic: article
ms.custom: devx-track-csharp
ms.openlocfilehash: ebde1be459078f4f3473ebc3042d8acebcce910a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89613736"
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
void EnableDebugRenderingEffects(AzureSession session, bool highlight)
{
    DebugRenderingSettings settings = session.Actions.DebugRenderingSettings;

    // Enable frame counter text overlay on the server side rendering
    settings.RenderFrameCount = true;

    // Enable polygon count text overlay on the server side rendering
    settings.RenderPolygonCount = true;

    // Enable wireframe rendering of object geometry on the server
    settings.RenderWireframe = true;
}
```

```cpp
void EnableDebugRenderingEffects(ApiHandle<AzureSession> session, bool highlight)
{
    ApiHandle<DebugRenderingSettings> settings = session->Actions()->GetDebugRenderingSettings();

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

* [C++ RemoteManager::D ebugRenderingSettings ()](https://docs.microsoft.com/cpp/api/remote-rendering/remotemanager#debugrenderingsettings)

## <a name="next-steps"></a>Další kroky

* [Režimy vykreslování](../../concepts/rendering-modes.md)
* [Dotazy na výkon na straně serveru](performance-queries.md)
