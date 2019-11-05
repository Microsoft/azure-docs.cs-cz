---
title: Zadat dotaz na koncový bod kontejneru textu na řeč
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 8460ddca5cff2b3da540b5fa8cf66e0687892789
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491103"
---
Kontejner poskytuje [rozhraní API koncového bodu na bázi REST](../rest-text-to-speech.md). K dispozici je mnoho [ukázkových projektů zdrojového kódu](https://azure.microsoft.com/resources/samples/cognitive-speech-tts/) pro platformu, architekturu a jazykové variace.

Se standardním kontejnerem *pro převod textu na řeč* byste měli spoléhat na národní prostředí a hlas značky obrázku, kterou jste stáhli. Pokud jste například stáhli značku `latest`, je výchozím národním prostředím `en-US` a `JessaRUS` hlas. Argument `{VOICE_NAME}` by pak byl [`en-US-JessaRUS`](../language-support.md#standard-voices). Podívejte se na příklad SSML níže:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Pro *vlastní převod textu na řeč* ale budete muset získat **hlas nebo model** z [vlastního hlasového portálu](https://aka.ms/custom-voice-portal). Název vlastního modelu je synonymum s názvem hlasu. Přejděte na stránku **školení** a zkopírujte **hlasový/model** , který chcete použít jako argument `{VOICE_NAME}`.
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

Pojďme sestavit požadavek HTTP POST, který poskytuje pár hlaviček a datovou část dat. Zástupný text `{VOICE_NAME}` nahraďte vlastní hodnotou.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

Tento příkaz:

* Vytvoří požadavek HTTP POST pro `speech/synthesize/cognitiveservices/v1` koncový bod.
* Určuje `Accept` záhlaví `audio/*`
* Určuje `Content-Type` záhlaví `application/ssml+xml`. Další informace najdete v části [Text žádosti](../rest-text-to-speech.md#request-body).
* Určuje `X-Microsoft-OutputFormat` záhlaví `riff-16khz-16bit-mono-pcm`, pro další možnosti se zobrazí [zvukový výstup](../rest-text-to-speech.md#audio-outputs).
* Odešle žádost [SSML (Speech syntézy Markup Language)](../speech-synthesis-markup.md) dané `{VOICE_NAME}` koncovému bodu.