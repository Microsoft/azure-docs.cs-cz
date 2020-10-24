---
title: Rychlý Start přepisu konverzace v reálném čase – služba pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Naučte se používat přepis konverzací v reálném čase s využitím sady Speech SDK. Přepis konverzace umožňuje přepisovat schůzky a další konverzace s možností přidávat, odebírat a identifikovat více účastníků streamování zvuku do služby Speech.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: trbye
zone_pivot_groups: acs-js-csharp
ms.openlocfilehash: 6cb338e8b7baa45e1c84f59a5730a9a500e71a79
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92486773"
---
# <a name="get-started-with-real-time-conversation-transcription"></a>Začínáme s přepisem konverzací v reálném čase

Rozhraní **ConversationTranscriber** API sady Speech SDK umožňuje přepisovat schůzky a další konverzace s možností přidávat, odebírat a identifikovat více účastníků streamování zvuku do služby Speech pomocí `PullStream` nebo `PushStream` . Nejdřív vytvoříte podpis hlasu pro každého účastníka pomocí REST API a pak pomocí hlasových signatur se sadou SDK přepisovat konverzace. Další informace najdete v tématu [Přehled](conversation-transcription.md) přepisu konverzace.

## <a name="limitations"></a>Omezení

* K dispozici pouze v následujících oblastech předplatného: `centralus` , `eastasia` , `eastus` , `westeurope`
* Vyžaduje pro datový proud s odkazem na přehrávání více než 7 mikrofon. Pole mikrofonu by mělo splňovat [naše specifikace](https://aka.ms/sdsdk-microphone).
* [Sada Speech Devices SDK](speech-devices-sdk.md) nabízí vhodná zařízení a ukázkovou aplikaci, která demonstruje přepis konverzace.

## <a name="prerequisites"></a>Předpoklady

V tomto článku se předpokládá, že máte účet Azure a předplatné služby Speech. Pokud účet a předplatné nemáte, [Vyzkoušejte službu Speech Service zdarma](overview.md#try-the-speech-service-for-free).

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/conversation-transcription/real-time-javascript.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/conversation-transcription/real-time-csharp.md)]
::: zone-end

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přepis](how-to-async-conversation-transcription.md) 
>  asynchronní konverzace [Vzorový kód](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/ConversationTranscription.java) 
>  zařízení ROOBO [Vzorový kód pro Azure Kinect dev Kit](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)
