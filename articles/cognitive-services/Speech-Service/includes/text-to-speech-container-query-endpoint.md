---
title: Zadat dotaz na koncový bod kontejneru textu na řeč
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/31/2020
ms.author: aahi
ms.openlocfilehash: 9a29244745b154aa81997813fcf4e1457f599270
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103622010"
---
Kontejner poskytuje [rozhraní API koncového bodu na bázi REST](../rest-text-to-speech.md). K dispozici je mnoho [ukázkových projektů zdrojového kódu](https://github.com/Azure-Samples/Cognitive-Speech-TTS) pro platformu, architekturu a jazykové variace.

Pomocí standardních nebo neuronové kontejnerů pro převod textu na řeč byste měli spoléhat na národní prostředí a hlas značky obrázku, kterou jste stáhli. Například pokud jste stáhli `latest` značku výchozí národní prostředí `en-US` a `AriaNeural` hlas. `{VOICE_NAME}`Argument by pak byl [`en-US-AriaNeural`](../language-support.md#neural-voices) . Podívejte se na příklad SSML níže:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Pro *vlastní převod textu na řeč* ale budete muset získat **hlas nebo model** z [vlastního hlasového portálu](https://aka.ms/custom-voice-portal). Název vlastního modelu je synonymum s názvem hlasu. Přejděte na stránku **školení** a zkopírujte **hlasový/model** , který chcete použít jako `{VOICE_NAME}` argument.
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="Vlastní hlasový model – hlasový název":::

Podívejte se na příklad SSML níže:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Pojďme sestavit požadavek HTTP POST, který poskytuje pár hlaviček a datovou část dat. `{VOICE_NAME}`Zástupný text nahraďte vlastní hodnotou.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-24khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>' > output.wav
```


Tento příkaz:

* Vytvoří požadavek HTTP POST pro `speech/synthesize/cognitiveservices/v1` koncový bod.
* Určuje `Accept` hlavičku `audio/*`
* Určuje `Content-Type` záhlaví `application/ssml+xml` . Další informace naleznete v části [Text žádosti](../rest-text-to-speech.md#request-body).
* Určuje `X-Microsoft-OutputFormat` hlavičku `riff-16khz-16bit-mono-pcm` , pro další možnosti se zobrazí [zvukový výstup](../rest-text-to-speech.md#audio-outputs).
* Odešle žádost [SSML (Speech syntézy Language)](../speech-synthesis-markup.md) danému `{VOICE_NAME}` koncovému bodu.
