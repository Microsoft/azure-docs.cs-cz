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
ms.openlocfilehash: 48cd4c7996eabad7293aa2429c76b8943e0ab3da
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100368468"
---
# <a name="get-started-with-real-time-conversation-transcription"></a>Začínáme s přepisem konverzací v reálném čase

Rozhraní **ConversationTranscriber** API sady Speech SDK umožňuje přepisovat schůzky a další konverzace s možností přidávat, odebírat a identifikovat více účastníků streamování zvuku do služby Speech pomocí `PullStream` nebo `PushStream` . Nejdřív vytvoříte podpis hlasu pro každého účastníka pomocí REST API a pak pomocí hlasových signatur se sadou SDK přepisovat konverzace. Další informace najdete v tématu [Přehled](conversation-transcription.md) přepisu konverzace.

## <a name="limitations"></a>Omezení

* K dispozici pouze v následujících oblastech předplatného: `centralus` , `eastasia` , `eastus` , `westeurope`
* Vyžaduje, aby bylo pole s více mikrofony s více mikrofony v podobě mikrofonu. Pole mikrofonu by mělo splňovat [naše specifikace](./speech-devices-sdk-microphone.md).
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