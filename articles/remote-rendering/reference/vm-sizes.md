---
title: Velikosti serverů
description: Popisuje různé velikosti serverů, které mohou být přiděleny
author: florianborn71
ms.author: flborn
ms.date: 05/28/2020
ms.topic: reference
ms.custom: devx-track-csharp
ms.openlocfilehash: 0e2687954fb05ce826e780ae0dbd3931d899885f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99594396"
---
# <a name="server-sizes"></a>Velikosti serverů

Vzdálené vykreslování Azure je k dispozici ve dvou konfiguracích serveru: `Standard` a `Premium` .

## <a name="polygon-limits"></a>Omezení mnohoúhelníku

Vzdálené vykreslování s `Standard` velikostí serveru má maximální velikost scény 20 000 000 mnohoúhelníků. Vzdálené vykreslování s `Premium` velikostí vynutilo vysoké maximum, ale výkon může být snížený, pokud váš obsah překročí možnosti vykreslování služby.

Když zobrazovací jednotka na standardní velikosti serveru narazí na toto omezení, přepne vykreslování na šachovnicové pozadí:

![Snímek obrazovky zobrazuje mřížku černého a bílého čtverečku pomocí nabídky nástroje.](media/checkerboard.png)

## <a name="specify-the-server-size"></a>Zadejte velikost serveru.

Požadovaný typ konfigurace serveru je nutné zadat při generování inicializace relace vykreslování. Nedá se změnit v běžící relaci. Následující příklady kódu ukazují místo, kde je nutné zadat velikost serveru:

```cs
async void CreateRenderingSession(RemoteRenderingClient client)
{
    RenderingSessionCreationOptions sessionCreationOptions = default;
    sessionCreationOptions.Size = RenderingSessionVmSize.Standard; // or  RenderingSessionVmSize.Premium

    CreateRenderingSessionResult result = await client.CreateNewRenderingSessionAsync(sessionCreationOptions);
    if (result.ErrorCode == Result.Success)
    {
        RenderingSession session = result.Session;
        // do something with the session
    }
}
```

```cpp
void CreateRenderingSession(ApiHandle<RemoteRenderingClient> client)
{
    RenderingSessionCreationOptions sessionCreationOptions;
    sessionCreationOptions.Size = RenderingSessionVmSize::Standard; // or  RenderingSessionVmSize::Premium

    client->CreateNewRenderingSessionAsync(sessionCreationOptions, [](Status status, ApiHandle<CreateRenderingSessionResult> result) {
        if (status == Status::OK && result->GetErrorCode() == Result::Success)
        {
            ApiHandle<RenderingSession> session = result->GetSession();
            // do something with the session
        }
    });
}

```

V případě [ukázkových skriptů PowerShellu](../samples/powershell-example-scripts.md)je nutné zadat požadovanou velikost serveru v rámci `arrconfig.json` souboru:

```json
{
  "accountSettings": {
    ...
  },
  "renderingSessionSettings": {
    "vmSize": "<standard or premium>",
    ...
  },
```

### <a name="how-the-renderer-evaluates-the-number-of-polygons"></a>Jak zobrazovací jednotka vyhodnocuje počet mnohoúhelníků

Počet mnohoúhelníků, které jsou považovány za test omezení, je počet mnohoúhelníků, které jsou skutečně předány do zobrazovací jednotky. Tato geometrie je obvykle součet všech vytvořených instancí modelů, ale existují i výjimky. Následující geometrie není k **dispozici**:
* Načetly se instance modelu, které jsou zcela mimo frustum zobrazení.
* Modely nebo části modelu, které jsou přepnuty na neviditelné, pomocí [komponenty pro přepsání hierarchického stavu](../overview/features/override-hierarchical-state.md).

To znamená, že je možné napsat aplikaci, která cílí na `standard` velikost, která načte více modelů s počtem mnohoúhelníků blízko do limitu pro každý model s jedním modelem. Když aplikace zobrazuje jenom jeden model najednou, šachovnice se neaktivuje.

### <a name="how-to-determine-the-number-of-polygons"></a>Jak určit počet mnohoúhelníků

Existují dva způsoby, jak určit počet mnohoúhelníků modelu nebo scény, které přispívají k omezením rozpočtu `standard` velikosti konfigurace:
* Na straně převod modelu načtěte [výstupní soubor JSON převodu](../how-tos/conversion/get-information.md)a podívejte se na `numFaces` položku v [části *inputStatistics*](../how-tos/conversion/get-information.md#the-inputstatistics-section) .
* Pokud vaše aplikace pracuje s dynamickým obsahem, Počet vykreslených mnohoúhelníků se dá dynamicky dotazovat za běhu. Použijte [dotaz vyhodnocování výkonu](../overview/features/performance-queries.md#performance-assessment-queries) a ověřte `polygonsRendered` člena ve `FrameStatistics` struktuře. `PolygonsRendered`Pole bude nastaveno na hodnotu, `bad` Pokud zobrazovací jednotka narazí na omezení mnohoúhelníku. V případě prodlevy se u šachovnicového pozadí vždy zvolna zaručí, že akce uživatele může být provedena po tomto asynchronním dotazu. Akce uživatele může být například skrývání nebo odstraňování instancí modelu.

## <a name="pricing"></a>Ceny

Podrobný rozpis cen pro jednotlivé typy konfigurací najdete na stránce s [cenami pro vzdálené vykreslování](https://azure.microsoft.com/pricing/details/remote-rendering) .

## <a name="next-steps"></a>Další kroky
* [Ukázkové skripty PowerShellu](../samples/powershell-example-scripts.md)
* [Převod modelu](../how-tos/conversion/model-conversion.md)

