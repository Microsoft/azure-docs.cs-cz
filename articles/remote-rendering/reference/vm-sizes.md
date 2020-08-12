---
title: Velikost virtuálních počítačů
description: Popisuje různé velikosti virtuálních počítačů, které se dají přidělit.
author: florianborn71
ms.author: flborn
ms.date: 05/28/2020
ms.topic: reference
ms.openlocfilehash: e8e439a055b71ed291573965c561ee31610e3ed4
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121608"
---
# <a name="vm-sizes"></a>Velikost virtuálních počítačů

Služba vykreslování může pracovat na dvou různých typech počítačů v Azure, které se nazývají `Standard` a `Premium` .

## <a name="polygon-limits"></a>Omezení mnohoúhelníku

Pro velikost virtuálního počítače existuje omezení **20 000 000 pro mnohoúhelníky** `Standard` . Neexistují taková omezení `Premium` velikosti.

Když zobrazovací jednotka na standardním virtuálním počítači narazí na toto omezení, přepne vykreslování na šachovnicové pozadí:

![Šachovnicový](media/checkerboard.png)

## <a name="allocate-the-vm"></a>Přidělte virtuální počítač

Požadovaný typ virtuálního počítače je nutné zadat při inicializaci relace vykreslování. Nedá se změnit v běžící relaci. Následující příklady kódu ukazují místo, kde musí být zadaná velikost virtuálního počítače:

```cs
async void CreateRenderingSession(AzureFrontend frontend)
{
    RenderingSessionCreationParams sessionCreationParams = new RenderingSessionCreationParams();
    sessionCreationParams.Size = RenderingSessionVmSize.Standard; // or  RenderingSessionVmSize.Premium

    AzureSession session = await frontend.CreateNewRenderingSessionAsync(sessionCreationParams).AsTask();
}
```

```cpp
void CreateRenderingSession(ApiHandle<AzureFrontend> frontend)
{
    RenderingSessionCreationParams sessionCreationParams;
    sessionCreationParams.Size = RenderingSessionVmSize::Standard; // or  RenderingSessionVmSize::Premium

    if (auto createSessionAsync = frontend->CreateNewRenderingSessionAsync(sessionCreationParams))
    {
        // ...
    }
}
```

V případě [ukázkových skriptů PowerShellu](../samples/powershell-example-scripts.md)musí být v souboru zadaná velikost virtuálního počítače `arrconfig.json` :

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

Existují dva způsoby, jak určit počet mnohoúhelníků modelu nebo scény, které přispívají k limitu rozpočtu pro `standard` Velikost virtuálního počítače:
* Na straně převod modelu načtěte [výstupní soubor JSON převodu](../how-tos/conversion/get-information.md)a podívejte se na `numFaces` položku v [části *inputStatistics* ](../how-tos/conversion/get-information.md#the-inputstatistics-section) .
* Pokud vaše aplikace pracuje s dynamickým obsahem, Počet vykreslených mnohoúhelníků se dá dynamicky dotazovat za běhu. Použijte [dotaz vyhodnocování výkonu](../overview/features/performance-queries.md#performance-assessment-queries) a ověřte `polygonsRendered` člena ve `FrameStatistics` struktuře. `polygonsRendered`Pole bude nastaveno na hodnotu, `bad` Pokud zobrazovací jednotka narazí na omezení mnohoúhelníku. V případě prodlevy se u šachovnicového pozadí vždy zvolna zaručí, že akce uživatele může být provedena po tomto asynchronním dotazu. Akce uživatele může být například skrývání nebo odstraňování instancí modelu.

## <a name="pricing"></a>Ceny

Podrobný rozpis cen pro každý typ virtuálního počítače najdete na stránce s [cenami pro vzdálené vykreslování](https://azure.microsoft.com/pricing/details/remote-rendering) .

## <a name="next-steps"></a>Další kroky
* [Ukázkové skripty PowerShellu](../samples/powershell-example-scripts.md)
* [Převod modelu](../how-tos/conversion/model-conversion.md)

