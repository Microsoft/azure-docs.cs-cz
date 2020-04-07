---
title: Informace
description: Úvod do vzdáleného vykreslování Azure
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: overview
ms.openlocfilehash: a06c63152cb56be6d94cccc472d2e1d65651d6ce
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679951"
---
# <a name="about-azure-remote-rendering"></a>O vzdáleném vykreslování Azure

> [!IMPORTANT]
> **Vzdálené vykreslování Azure** je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

*Azure Remote Rendering* (ARR) je služba, která umožňuje vykreslovat vysoce kvalitní interaktivní 3D obsah v cloudu a streamovat ho v reálném čase do zařízení, jako je HoloLens 2.

![Vzorový model](../media/arr-engine.png)

Netetovaná zařízení mají omezený výpočetní výkon pro vykreslování složitých modelů. Pro mnoho aplikací by však bylo nepřijatelné, aby se vizuální věrnost jakýmkoli způsobem snížila.

*Vzdálené vykreslování* řeší tento problém přesunutím úlohy vykreslování na špičkové gpu v cloudu. Grafický engine hostovaný v cloudu vykreslí obraz, zakóduje jej jako datový proud videa a streamuje jej do cílového zařízení.

## <a name="hybrid-rendering"></a>Hybridní vykreslování

Ve většině aplikací nestačí pouze vykreslit složitý model. K poskytování funkcí uživateli potřebujete také vlastní uživatelské rozhraní. Azure Remote Rendering nenutí použití vyhrazené rozhraní uživatelského rozhraní, místo toho podporuje *hybridní vykreslování*. To znamená, že můžete vykreslit prvky na zařízení pomocí upřednostňované metody, například [MRTK](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/GettingStartedWithTheMRTK.html).

Na konci rámce Azure Remote Rendering pak automaticky kombinuje místně vykreslený obsah se vzdálenou bitovou kopii. Je dokonce schopen tak učinit se správnou okluzí.

## <a name="multi-gpu-rendering"></a>Vykreslování s více GPU

Některé modely jsou příliš složité na to, aby se vykreslovaly při interaktivních snímkových frekvencích, a to i pro špičkový grafický procesor. Zejména v průmyslové vizualizaci je to běžný problém. Chcete-li posunout limity dále, Azure Remote Rendering můžete distribuovat úlohy do více gpu. Výsledky jsou sloučeny do jednoho obrázku, takže proces zcela transparentní pro uživatele.

## <a name="high-level-architecture"></a>Architektura z širší perspektivy

Tento diagram znázorňuje architekturu vzdáleného vykreslování:

![Architektura](./media/arr-high-level-architecture.png)

Celý cyklus pro generování obrazu zahrnuje následující kroky:

1. Na straně klienta: Nastavení rámce
    1. Váš kód: Uživatelský vstup je zpracován, graf scény se aktualizuje
    1. ARR kód: Aktualizace grafu scény a předpokládané hlavní póza jsou odeslány na server
1. Na straně serveru: Vzdálené vykreslování
    1. Vykreslovací modul distribuuje vykreslování mezi dostupnými grafickými procesory
    1. Výstup z více gpu se skládá do jednoho obrazu
    1. Obrázek je kódován jako video stream, odeslán zpět klientovi
1. Na straně klienta: Finalizace
    1. Váš kód: Vykreslí se volitelný místní obsah (UI, značky, ...)
    1. Kód ARR: Při současném obsahu se místně vykreslený obsah automaticky sloučí s datovým proudem videa

Hlavním problémem je latence sítě. Doba odezvy mezi odesláním požadavku a přijetím výsledku je obvykle příliš dlouhá pro interaktivní kmitočet snímků. Proto může být v každém okamžiku více než jeden snímek v letu.

## <a name="next-steps"></a>Další kroky

* [Požadavky na systém](system-requirements.md)
* [Úvodní příručka: Vykreslení modelu pomocí Unity](../quickstarts/render-model.md)
