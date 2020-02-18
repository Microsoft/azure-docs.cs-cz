---
title: Cloudy svrchované – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Naučte se používat cloudy v svrchovaném provozu.
services: cognitive-services
author: cbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 1/14/2020
ms.author: cbasoglu
ms.openlocfilehash: 00617fc09f471eaf3dc13a5aa691c4aab2e1e2ec
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2020
ms.locfileid: "77424911"
---
# <a name="speech-services-with-sovereign-clouds"></a>Hlasové služby s svrchovanými cloudy

## <a name="azure-government-united-states"></a>Azure Government (USA)

Přístup k této vyhrazené instanci mají jenom americké federální, státní, místní nebo samosprávnéové vlády a jejich partneři s činnostmi, které řídí naši občané USA.
- Oblasti: US Gov – Virginie
- SR in SpeechSDK:*config. FromHost ("WSS://Virginia.STT.Speech.Azure.us"; "\<Key\>");*
- TTS v SpeechSDK: *config. FromHost ("https[]()://Virginia.TTS.Speech.Azure.us"; "\<Key\>");*
- Ověřovací tokeny:[]()https://Virginia.API.Cognitive.Microsoft.us/STS/v1.0/issueToken
- Azure Portal: https://portal.azure.us  
- Portál Custom Speech: https://virginia.cris.azure.us/Home/CustomSpeech
- Dostupné SKU: S0
- Podporované funkce:
  - Řeč na text
  - Custom Speech (úprava akustického/jazyka)
  - Převod textu na řeč
  - Překladatel řeči
- Nepodporované funkce
  - Vlastní hlas
  - Neuronové hlasy pro převod textu na řeč
- Podporovaná národní prostředí: národní prostředí pro následující jazyky jsou podporovaná.
  - Arabština (ar-*)
  - Čínština (zh-*)
  - Angličtina (EN-*)
  - Francouzština (FR-*)
  - Němčina (de-*)
  - Hindština
  - Korejština
  - Ruština
  - Španělština (ES-*)

## <a name="microsoft-azure-china"></a>Microsoft Azure – Čína

V Číně je k dispozici datové centrum Azure s přímým přístupem k Číně Mobile, Čína Telecom, Čína UNICOM a další hlavní páteřní síť pro uživatele, kteří budou mít k dispozici vysokorychlostní a stabilní přístup k místní síti.
- Oblasti: Čína – východ 2 (Shanghai)
- SR in SpeechSDK: *config. FromHost ("WSS://chinaeast2.STT.Speech.Azure.cn"; "\<Key\>");*
- TTS v SpeechSDK: *config. FromHost ("https[]()://chinaeast2.TTS.Speech.Azure.cn"; "\<Key\>");*
- Ověřovací tokeny:[]()https://chinaeast2.API.Cognitive.Azure.cn/STS/v1.0/issueToken
- Azure Portal: https://portal.azure.cn
- Portál Custom Speech: https://chinaeast2.cris.azure.cn/Home/CustomSpeech
- Dostupné SKU: S0
- Podporované funkce:
  - Řeč na text
  - Custom Speech (úprava akustického/jazyka)
  - Převod textu na řeč
  - Překladatel řeči
- Nepodporované funkce
  - Vlastní hlas
  - Neuronové hlasy pro převod textu na řeč
- Podporovaná národní prostředí: národní prostředí pro následující jazyky jsou podporovaná.
  - Arabština (ar-*)
  - Čínština (zh-*)
  - Angličtina (EN-*)
  - Francouzština (FR-*)
  - Němčina (de-*)
  - Hindština
  - Korejština
  - Ruština
  - Španělština (ES-*)

