---
title: Informace
description: Úvod do vzdáleného vykreslování Azure
author: florianborn71
ms.author: flborn
ms.date: 02/05/2020
ms.topic: overview
ms.openlocfilehash: 14ebe9dfd11bed2dfaded9f18e23f73d33a708ae
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91650455"
---
# <a name="about-azure-remote-rendering"></a>Informace o službě Azure Remote Rendering

> [!IMPORTANT]
> **Vzdálené vykreslování Azure** je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

*Vzdálená vykreslování Azure* (ARR) je služba, která umožňuje vykreslovat vysoce kvalitní interaktivní 3D obsah v cloudu a streamovat ho v reálném čase do zařízení, jako je například HoloLens 2.

![Diagram, který zobrazuje příklad vykresleného vysoce kvalitního a interaktivního modulu 3D automobilu.](../media/arr-engine.png)

Nepřipojená zařízení mají omezený výpočetní výkon pro vykreslování složitých modelů. U mnoha aplikací by bylo možné nepřijmout, ale omezit vizuální věrnost v libovolném způsobu. Následující snímek obrazovky porovnává model s úplnými podrobnostmi s modelem, který byl Decimated pomocí společného nástroje pro vytváření obsahu:

![Ukázkový model](./media/engine-model-decimated.png)

Redukovaný model se skládá přibližně z 200 000 trojúhelníků (včetně podrobných vnitřních částí), v porovnání s více než 18 000 000 trojúhelníky v původním modelu.

*Vzdálené vykreslování* řeší tento problém přesunutím úlohy vykreslování na špičkové GPU v cloudu. Grafický modul hostovaný v cloudu vykresluje obrázek, zakóduje ho jako datový proud videa a streamy, které jsou na cílovém zařízení.

## <a name="hybrid-rendering"></a>Hybridní vykreslování

Ve většině aplikací není dostačující pouze vykreslení komplexního modelu. K poskytování funkcí uživateli potřebujete také vlastní uživatelské rozhraní. Vzdálené vykreslování Azure nenutí použití vyhrazeného rozhraní .NET Framework, ale podporuje *hybridní vykreslování*. To znamená, že můžete vykreslit prvky na zařízení pomocí preferované metody, například [MRTK](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/GettingStartedWithTheMRTK.html).

Na konci snímku Azure Remote rendering automaticky kombinuje místně vykreslený obsah se vzdálenou imagí. To lze provést i pomocí správného překrytí.

## <a name="multi-gpu-rendering"></a>Vykreslování s více PROCESORy

Některé modely jsou příliš složité pro vykreslování v rámci interaktivních snímkových frekvencí, a to i pro procesory s vysokým konečným procesorem. Hlavně v průmyslové vizualizaci je to běžný problém. Aby bylo možné doručovat omezení dál, vzdálené vykreslování Azure může distribuovat úlohy na více GPU. Výsledky jsou sloučeny do jediného obrázku, což umožňuje, aby byl proces zcela transparentní pro uživatele.

## <a name="high-level-architecture"></a>Architektura z širší perspektivy

Tento diagram znázorňuje architekturu vzdáleného vykreslování:

![Architektura](./media/arr-high-level-architecture.png)

Úplný cyklus pro generování imagí zahrnuje následující kroky:

1. Klientská strana: nastavení snímků
    1. Váš kód: zpracovává se uživatelský vstup, graf scény se aktualizuje.
    1. Kód ARR: aktualizace grafu scény a předpokládaná pozice hlav se odeslaly na server.
1. Na straně serveru: vzdálené vykreslování
    1. Vykreslovací modul distribuuje vykreslování přes dostupné GPU.
    1. Výstup z více GPU se skládá do jednoho obrázku.
    1. Obrázek je kódovaný jako video stream, který se pošle zpátky klientovi.
1. Klientská strana: finalizace
    1. Váš kód: vykreslí se volitelný místní obsah (uživatelské rozhraní, značky,...).
    1. Kód ARR: on ' prezentovat ', místně vykreslený obsah se automaticky sloučí se streamem videa

Latence sítě je hlavním problémem. Doba vypínání mezi odesláním požadavku a přijetí výsledku je obvykle příliš dlouhá pro míry interaktivního snímku. Proto může být v každém okamžiku v letu více než jeden rámec.

## <a name="next-steps"></a>Další kroky

* [Požadavky na systém](system-requirements.md)
* [Rychlý Start: vykreslení modelu pomocí Unity](../quickstarts/render-model.md)
