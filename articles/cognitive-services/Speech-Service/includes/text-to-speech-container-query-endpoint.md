---
title: Koncový bod kontejneru pro převod textu na řeč dotazu
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: b2a621a23a81e4fb4f47e7c99d780211973e30a0
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81275377"
---
Kontejner poskytuje [řešení API koncového bodu založené na rest](../rest-text-to-speech.md). Existuje mnoho [ukázkový zdrojový kód projekty](https://github.com/Azure-Samples/Cognitive-Speech-TTS) pro platformu, framework a jazykové varianty k dispozici.

U *standardního kontejneru převodu textu na řeč* byste se měli spoléhat na národní prostředí a hlas stažené značky obrázku. Pokud jste například stáhli `latest` značku, výchozí `en-US` národní `JessaRUS` prostředí je a hlas. Argument `{VOICE_NAME}` by pak [`en-US-JessaRUS`](../language-support.md#standard-voices)byl . Viz příklad SSML níže:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-JessaRUS">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Pro *vlastní převod textu na řeč* však budete muset získat hlas / **model** z [vlastního hlasového portálu](https://aka.ms/custom-voice-portal). Název vlastního modelu je synonymem pro název hlasu. Přejděte na stránku **Školení** a zkopírujte voice `{VOICE_NAME}` / **model,** který chcete použít jako argument.
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="Vlastní hlasový model - hlasové jméno":::

Viz příklad SSML níže:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Pojďme vytvořit požadavek HTTP POST, poskytuje několik záhlaví a datové části dat. Nahraďte `{VOICE_NAME}` zástupný symbol vlastní hodnotou.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-16khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>'
```

Tento příkaz:

* Vytvoří požadavek HTTP POST `speech/synthesize/cognitiveservices/v1` pro koncový bod.
* Určuje `Accept` záhlaví`audio/*`
* Určuje záhlaví `Content-Type` , `application/ssml+xml`další informace naleznete v tématu [tělo požadavku](../rest-text-to-speech.md#request-body).
* Určuje `X-Microsoft-OutputFormat` záhlaví aplikace `riff-16khz-16bit-mono-pcm`, viz [zvukový výstup](../rest-text-to-speech.md#audio-outputs).
* Odešle požadavek [jazyka s poznámkami pro syntézu řeči (SSML)](../speech-synthesis-markup.md) přidělený `{VOICE_NAME}` koncovému bodu.