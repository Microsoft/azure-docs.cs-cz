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
ms.openlocfilehash: 3b96ded52aa2148df9e96d6f1e878d50b821abab
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2020
ms.locfileid: "76170465"
---
# <a name="speech-services-with-sovereign-clouds"></a>Hlasové služby s svrchovanými cloudy

## <a name="azure-government-united-states"></a>Azure Government (USA)

Přístup k této vyhrazené instanci obsluhované prověřenými občany USA mají pouze americké federální, státní, místní nebo samosprávné instituce a jejich partneři.
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
  - Text na řeč
  - Překladatel řeči
- Nepodporované funkce
  - Custom Voice
  - Neuronové hlasy pro převod textu na řeč
- Podporovaná národní prostředí: národní prostředí pro následující jazyky jsou podporovaná.
  - Arabština (ar-*)
  - Čínština (zh-*)
  - Angličtina (EN-*)
  - Francouzština (FR-*)
  - Němčina (de-*)
  - Hindština
  - Korejština
  - ruština
  - Španělština (ES-*)

## <a name="microsoft-azure-china"></a>Microsoft Azure (Čína)

V Číně je k dispozici datové centrum Azure s přímým přístupem k Číně Mobile, Čína Telecom, Čína UNICOM a další hlavní páteřní síť pro uživatele, kteří budou mít k dispozici vysokorychlostní a stabilní přístup k místní síti.
- Oblasti: Čína – východ 2 (Shanghai)
- SR in SpeechSDK: *config. FromHost ("WSS://chinaeast2.STT.Speech.Azure.cn"; "\<Key\>");*
- TTS v SpeechSDK: *config. FromHost ("https[]()://chinaeast2.TTS.Speech.Azure.cn"; "\<Key\>");*
- Ověřovací tokeny:[]()https://chinaeast2.API.Cognitive.Microsoft.cn/STS/v1.0/issueToken
- Azure Portal: https://portal.azure.cn
- Portál Custom Speech: https://chinaeast2.cris.azure.cn/Home/CustomSpeech
- Dostupné SKU: S0
- Podporované funkce:
  - Řeč na text
  - Custom Speech (úprava akustického/jazyka)
  - Text na řeč
  - Překladatel řeči
- Nepodporované funkce
  - Custom Voice
  - Neuronové hlasy pro převod textu na řeč
- Podporovaná národní prostředí: národní prostředí pro následující jazyky jsou podporovaná.
  - Arabština (ar-*)
  - Čínština (zh-*)
  - Angličtina (EN-*)
  - Francouzština (FR-*)
  - Němčina (de-*)
  - Hindština
  - Korejština
  - ruština
  - Španělština (ES-*)

