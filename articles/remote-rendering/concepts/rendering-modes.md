---
title: Režimy vykresl
description: Popisuje různé režimy vykreslování na straně serveru.
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 7f2b1031659864ae338bb0aa320c048ea23c21f3
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681698"
---
# <a name="rendering-modes"></a>Režimy vykresl

Vzdálené vykreslování nabízí dva hlavní provozní režimy, režim **TileBasedComposition** a **DepthBasedComposition.** Tyto režimy určují, jak je pracovní vytížení distribuováno mezi více gpu na serveru. Režim musí být zadán v době připojení a nelze jej změnit za běhu.

Oba režimy přicházejí s výhodami, ale také s vlastními omezeními funkcí, takže výběr nejvhodnějšího režimu je specifický pro případ použití.

## <a name="modes"></a>Režimy

Oba režimy jsou nyní podrobněji popsány.

### <a name="tilebasedcomposition-mode"></a>Režim "TileBasedComposition"

V režimu **TileBasedComposition** každý zapojený grafický procesor vykreslí konkrétní dílčí obdélníky (dlaždice) na obrazovce. Hlavní GPU vytvoří konečný obraz z dlaždic před odesláním jako video snímek klientovi. Proto všechny gpu vyžadují mít stejnou sadu prostředků pro vykreslování, takže načtené prostředky musí být vejde do paměti jednoho GPU.

Kvalita vykreslování v tomto režimu je o něco lepší než v režimu **DepthBasedComposition,** protože MSAA může pracovat na úplné sadě geometrie pro každý GRAFICKÝ procesor. Následující snímek obrazovky ukazuje, že vyhlazení funguje správně i pro oba okraje:

![MSAA v TileBasedComposition](./media/service-render-mode-quality.png)

Kromě toho v tomto režimu může být každá část přepnuta na průhledný materiál nebo přepnuta do režimu **průhlednosti** prostřednictvím [komponenty HierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md)

### <a name="depthbasedcomposition-mode"></a>Režim "DepthBasedComposition"

V režimu **DepthBasedComposition** se každý zapojený grafický procesor vykresluje v rozlišení celé obrazovky, ale pouze v podmnožině sítí. Konečná kompozice obrazu na hlavním GPU se stará o to, aby byly díly správně sloučeny podle informací o hloubce. Datová část paměti je přirozeně distribuována mezi gpu, což umožňuje vykreslovat modely, které by se nevešly do paměti jednoho GPU.

Každý grafický procesor používá MSAA k antialiasu místního obsahu. Může však existovat vlastní aliasing mezi hranami z různých gpu. Tento efekt je zmírněn postprocessing konečný obraz, ale Kvalita MSAA je ještě horší než v **režimu TileBasedComposition.**

Artefakty MSAA jsou znázorněny na ![následujícím obrázku: MSAA v DepthBasedComposition](./media/service-render-mode-balanced.png)

Antialiasing funguje správně mezi sochou a oponou, protože obě části jsou vykresleny na stejném GPU. Na druhou stranu, okraj mezi oponou a stěnou ukazuje některé aliasing, protože tyto dvě části jsou složeny z různých GPU.

Největší omezení tohoto režimu je, že geometrické části nelze dynamicky přepínat na průhledné materiály, ani **režim průhlednosti** pro [hierarchicalStateOverrideComponent](../overview/features/override-hierarchical-state.md). Ostatní funkce přepsání stavu (obrys, barevný barevný tón, ...) však fungují. Také materiály, které byly v době převodu označeny jako průhledné, fungují v tomto režimu správně.

### <a name="performance"></a>Výkon

Charakteristiky výkonu pro oba režimy se liší v závislosti na případu použití a je obtížné důvod nebo poskytnout obecná doporučení. Pokud nejste omezeni výše uvedenými omezeními (paměť nebo průhlednost/průhlednost), doporučujeme vyzkoušet oba režimy a sledovat výkon pomocí různých poloh kamery.

## <a name="setting-the-render-mode"></a>Nastavení režimu vykreslení

Režim vykreslení používaný na virtuálním počítači vzdáleného vykreslování je určen během `AzureSession.ConnectToRuntime` použití `ConnectToRuntimeParams`.

```cs
async void ExampleConnect(AzureSession session)
{
    ConnectToRuntimeParams parameters = new ConnectToRuntimeParams();

    // Connect with one rendering mode
    parameters.mode = ServiceRenderMode.TileBasedComposition;
    await session.ConnectToRuntime(parameters).AsTask();

    session.DisconnectFromRuntime();

    // Wait until session.IsConnected == false

    // Reconnect with a different rendering mode
    parameters.mode = ServiceRenderMode.DepthBasedComposition;
    await session.ConnectToRuntime(parameters).AsTask();
}
```

## <a name="next-steps"></a>Další kroky

* [Relace](../concepts/sessions.md)
* [Součást přepsání hierarchického stavu](../overview/features/override-hierarchical-state.md)
