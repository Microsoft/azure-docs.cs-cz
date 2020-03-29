---
title: Suverénní mraky - služba řeči
titleSuffix: Azure Cognitive Services
description: Naučte se používat suverénní cloudy
services: cognitive-services
author: cbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 1/14/2020
ms.author: cbasoglu
ms.openlocfilehash: b41967033b00144ca5bd52ce23cf8aabcea6749e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78228088"
---
# <a name="speech-services-with-sovereign-clouds"></a>Hlasové služby se suverénními mraky

## <a name="azure-government-united-states"></a>Azure Government (Spojené státy)

Pouze americké federální, státní, místní a kmenové vlády a jejich partneři mají přístup k této specializované instanci s operacemi kontrolovanými prověřenými občany USA.
- Regiony: US Gov Virginia
- SR v SpeechSDK:*config. FromHost("wss://virginia.stt.speech.azure.us",\<" váš\>klíč ");*
- TTS v SpeechSDK: *config. FromHost("https://virginia.tts.speech.azure.us",[]()\<" váš\>klíč ");*
- Ověřovací tokeny:[]()https ://virginia.api.cognitive.microsoft.us/sts/v1.0/issueToken
- Portál Azure:https://portal.azure.us  
- Vlastní řečový portál:https://virginia.cris.azure.us/Home/CustomSpeech
- K dispozici SKUs: S0
- Podporované funkce:
  - Řeč na text
  - Vlastní řeč (akustická/jazyková adaptace)
  - Převod textu na řeč
  - Překladač řeči
- Nepodporované funkce
  - Vlastní hlas
  - Neurální hlasy pro převod textu na řeč
- Podporovaná národní prostředí: Národní prostředí pro následující jazyky jsou podporovány.
  - Arabština (ar-*)
  - Čínština (zh-*)
  - Angličtina (en-*)
  - Francouzština (fr-*)
  - Němčina (de-*)
  - Hindština
  - Korejština
  - Ruština
  - Španělština (es-*)

## <a name="microsoft-azure-china"></a>Microsoft Azure V Číně

Datové centrum Azure se nachází v Číně, které má přímý přístup k zařízením China Mobile, China Telecom, China Unicom a dalším významným páteřním sítím operátorů, a čínským uživatelům poskytuje vysokorychlostní a stabilní přístup k místní síti.
- Regiony: Čína východ 2 (Šanghaj)
- SR v SpeechSDK: *config. FromHost("wss://chinaeast2.stt.speech.azure.cn",\<" your-key\>");*
- TTS v SpeechSDK: *config. FromHost("https://chinaeast2.tts.speech.azure.cn",[]()\<" váš\>klíč ");*
- Ověřovací tokeny:[]()https://chinaeast2.api.cognitive.azure.cn/sts/v1.0/issueToken
- Portál Azure:https://portal.azure.cn
- Vlastní řečový portál:https://speech.azure.cn/CustomSpeech
- K dispozici SKUs: S0
- Podporované funkce:
  - Řeč na text
  - Vlastní řeč (akustická/jazyková adaptace)
  - Převod textu na řeč
  - Překladač řeči
- Nepodporované funkce
  - Vlastní hlas
  - Neurální hlasy pro převod textu na řeč
- Podporovaná národní prostředí: Národní prostředí pro následující jazyky jsou podporovány.
  - Arabština (ar-*)
  - Čínština (zh-*)
  - Angličtina (en-*)
  - Francouzština (fr-*)
  - Němčina (de-*)
  - Hindština
  - Korejština
  - Ruština
  - Španělština (es-*)

