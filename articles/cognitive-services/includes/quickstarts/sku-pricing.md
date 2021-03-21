---
title: Vyvnímání skladových položek a cen
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: 8cc4bc6907f83ce062fed82dde17815fc4debd67
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104719732"
---
Podívejte se na seznam SKU a informace o cenách níže. 

#### <a name="multi-service"></a>Více služeb

| Služba                    | Druh                      |
|----------------------------|---------------------------|
| Několik služeb. Další informace najdete na stránce s [cenami](https://azure.microsoft.com/pricing/details/cognitive-services/) .            | `CognitiveServices`     |


#### <a name="vision"></a>Obraz

| Služba                    | Druh                      |
|----------------------------|---------------------------|
| Počítačové zpracování obrazu            | `ComputerVision`          |
| Custom Vision – předpověď | `CustomVision.Prediction` |
| Custom Vision – školení   | `CustomVision.Training`   |
| Tvář                       | `Face`                    |
| Rozpoznávání formulářů            | `FormRecognizer`          |

#### <a name="search"></a>Hledat

| Služba            | Druh                  |
|--------------------|-----------------------|
| Automatické návrhy Bingu   | `Bing.Autosuggest.v7` |
| Vlastní vyhledávání Bingu | `Bing.CustomSearch`   |
| Vyhledávání entit Bingu | `Bing.EntitySearch`   |
| Vyhledávání Bingu        | `Bing.Search.v7`      |
| Kontrola pravopisu Bingu   | `Bing.SpellCheck.v7`  |

#### <a name="speech"></a>Řeč

| Služba            | Druh                 |
|--------------------|----------------------|
| Služby Speech    | `SpeechServices`     |
| Rozpoznávání řeči | `SpeakerRecognition` |

#### <a name="language"></a>Jazyk

| Služba            | Druh                |
|--------------------|---------------------|
| LUIS               | `LUIS`              |
| QnA Maker          | `QnAMaker`          |
| Analýza textu     | `TextAnalytics`     |
| Překlad textu   | `TextTranslation`   |

#### <a name="decision"></a>Rozhodnutí

| Služba           | Druh               |
|-------------------|--------------------|
| Detektor anomálií  | `AnomalyDetector`  |
| Content Moderator | `ContentModerator` |
| Personalizace      | `Personalizer`     |


#### <a name="pricing-tiers-and-billing"></a>Cenové úrovně a fakturace

Cenové úrovně (a množství, které se vám bude účtovat), vycházejí z počtu transakcí, které odešlete pomocí ověřovacích informací. Každá cenová úroveň určuje:
* maximální počet povolených transakcí za sekundu (TPS).
* funkce služby povolené v rámci cenové úrovně.
* náklady na předdefinovaný počet transakcí. Po překročení tohoto čísla dojde k zadání dalších poplatků uvedených v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) vaší služby.

> [!NOTE]
> Mnohé z Cognitive Services mají bezplatnou úroveň, kterou můžete použít k vyzkoušení služby. Pokud chcete používat úroveň Free, použijte `F0` jako SKU pro váš prostředek.