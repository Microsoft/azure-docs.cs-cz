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
ms.openlocfilehash: b41967033b00144ca5bd52ce23cf8aabcea6749e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "78228088"
---
# <a name="speech-services-with-sovereign-clouds"></a>Hlasové služby s svrchovanými cloudy

## <a name="azure-government-united-states"></a>Azure Government (USA)

Přístup k této vyhrazené instanci mají jenom americké federální, státní, místní nebo samosprávnéové vlády a jejich partneři s činnostmi, které řídí naši občané USA.
- Oblasti: US Gov – Virginie
- SR in SpeechSDK:*config. FromHost ("WSS://Virginia.STT.Speech.Azure.us"; " \<your-key\> ");*
- TTS v SpeechSDK: *config. FromHost ("https []() ://Virginia.TTS.Speech.Azure.us"; " \<your-key\> ");*
- Ověřovací tokeny: https []() ://Virginia.API.Cognitive.Microsoft.us/STS/v1.0/issueToken
- Azure Portal: https://portal.azure.us  
- Portál Custom Speech: https://virginia.cris.azure.us/Home/CustomSpeech
- Dostupné SKU: S0
- Podporované funkce:
  - Převod řeči na text
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

## <a name="microsoft-azure-china"></a>Microsoft Azure Čína

V Číně je k dispozici datové centrum Azure s přímým přístupem k Číně Mobile, Čína Telecom, Čína UNICOM a další hlavní páteřní síť pro uživatele, kteří budou mít k dispozici vysokorychlostní a stabilní přístup k místní síti.
- Oblasti: Čína – východ 2 (Shanghai)
- SR in SpeechSDK: *config. FromHost ("WSS://chinaeast2.STT.Speech.Azure.cn"; " \<your-key\> ");*
- TTS v SpeechSDK:  *config. FromHost ("https []() ://chinaeast2.TTS.Speech.Azure.cn"; " \<your-key\> ");*
- Ověřovací tokeny: https []() ://chinaeast2.API.Cognitive.Azure.cn/STS/v1.0/issueToken
- Azure Portal: https://portal.azure.cn
- Portál Custom Speech: https://speech.azure.cn/CustomSpeech
- Dostupné SKU: S0
- Podporované funkce:
  - Převod řeči na text
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

