---
title: Co je rozhraní API pro detekci anomálií lineární?
titleSuffix: Azure Cognitive Services
description: Přehled nových rozhraní API pro lineární pro detekci anomálií ve verzi Public Preview
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: detekce anomálií, strojové učení, algoritmy
ms.openlocfilehash: 63ad63cd57dce5f503e317e8c6330dca0325ba05
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315459"
---
# <a name="multivariate-time-series-anomaly-detection-public-preview"></a>Detekce anomálií lineární časové řady (Public Preview)

První verze detektoru anomálií služby Azure Cognitive Services umožňuje sestavovat řešení monitorování metrik pomocí snadno použitelných rozhraní API pro detekci [anomálií univariate Time Series](overview.md). Díky tomu, že umožňují analýzu časových řad jednotlivě, nabízí detektor anomálií univariate jednoduchost a škálovatelnost.

Nová rozhraní API pro **detekci anomálií lineární** ještě vývojářům umožňují snadnou integraci pokročilého AI pro detekci anomálií ze skupin metrik, aniž by to mělo znalosti strojového učení ani data s popisky. Závislosti a vzájemné korelace mezi až 300 různými signály se teď automaticky počítají jako klíčové faktory. Tato nová funkce vám pomůže aktivně chránit komplexní systémy, jako jsou softwarové aplikace, servery, výrobní počítače, kosmické lodi nebo dokonce i vaše podnikání, a to z chyb.

Představte si 20 senzorů z automatického motoru generujících 20 různých signálů, jako jsou vibrace, teplota, tlak na palivo atd. Čtení těchto signálů jednotlivě nemusí informovat o problémech na úrovni systému, ale společně mohou představovat stav motoru. Pokud se interakce těchto signálů odchyluje mimo obvyklý rozsah, funkce detekce anomálií lineární může vyhodnotit anomálii, jako je například zkušený odborník. Základní modely AI jsou vyškolené a přizpůsobené pomocí vašich dat tak, aby rozuměly jedinečným potřebám vaší firmy. Díky novým rozhraním API v detektoru anomálií teď můžou vývojáři snadno integrovat možnosti detekce anomálií lineární Time Series do řešení prediktivní údržby, řešení AIOps Monitoring pro komplexní podnikový software nebo business intelligence nástroje.

## <a name="when-to-use-multivariate-versus-univariate"></a>Kdy použít **lineární** versus **univariate**

Použijte rozhraní API pro detekci anomálií univariate, pokud vaším cílem je detekovat anomálie z normálního vzoru pro každou jednotlivou časovou řadu čistě na základě jejich vlastních historických dat. Příklady: Chcete zjistit anomálie denních výnosů na základě dat o výnosech nebo chcete zjistit špičku procesoru čistě na základě dat procesoru.
- `POST /anomalydetector/v1.0/timeseries/last/detect`
- `POST /anomalydetector/v1.0/timeseries/batch/detect`
- `POST /anomalydetector/v1.0/timeseries/changepoint/detect`

![Graf řady časových řad s hodnotami fluktuace jedné proměnné zachycené modrou čárou se anomáliemi, které identifikovaly oranžové kroužky](./media/anomaly_detection2.png)

Použijte rozhraní API pro detekci anomálií lineární níže, pokud je vaším cílem zjistit anomálie na úrovni systému ze skupiny dat časových řad. Obzvláště pokud se vám v každé z jednotlivých časových řad neřekne hodně a Vy se budete muset podívat na všechny signály (skupiny časových řad) komplexní a určit problém na úrovni systému. Příklad: máte nákladný fyzický prostředek jako letadla, vybavení v olejovém nebo satelitu. Každý z těchto prostředků má desítky nebo stovky různých typů senzorů. Museli byste se podívat na všechny signály časových řad od těchto senzorů a rozhodnout se, jestli dochází k problémům na úrovni systému.

- `POST /anomalydetector/v1.1-preview/multivariate/models`
- `GET /anomalydetector/v1.1-preview/multivariate/models[?$skip][&$top]`
- `GET /anomalydetector/v1.1-preview/multivariate/models/{modelId}`
- `POST/anomalydetector/v1.1-preview/multivariate/models/{modelId}/detect`
- `GET /anomalydetector/v1.1-preview/multivariate/results/{resultId}`
- `DELETE /anomalydetector/v1.1-preview/multivariate/models/{modelId}`
- `GET /anomalydetector/v1.1-preview/multivariate/models/{modelId}/export`

![Více grafů pro spojnici časových řad pro proměnné: vibrace, teplota, tlak, rychlost, rychlost otáčení se anomáliemi zvýrazněnými oranžově](./media/multivariate-graph.png)

## <a name="region-support"></a>Podpora oblastí

Verze Public Preview lineární detektoru anomálií je aktuálně dostupná ve třech oblastech: Západ USA 2, východní USA 2 a Západní Evropa.

## <a name="algorithms"></a>Algoritmy

- [Detekce anomálií lineární časové řady prostřednictvím sítě s upozorněním na graf](https://arxiv.org/abs/2009.02040)

## <a name="join-the-anomaly-detector-community"></a>Zapojení do komunity Detektoru anomálií

- Připojit se ke [skupině poradců pro detekci anomálií v Microsoft Teams](https://aka.ms/AdAdvisorsJoin)

## <a name="next-steps"></a>Další kroky

- [Rychlé starty](./quickstarts/client-libraries-multivariate.md).
- [Osvědčené postupy](./concepts/best-practices-multivariate.md): Tento článek se týká doporučených vzorů pro použití s rozhraními API lineární.
