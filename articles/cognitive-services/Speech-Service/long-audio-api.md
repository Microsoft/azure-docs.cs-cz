---
title: Dlouhé zvukové rozhraní API (Preview) – služba Speech
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/26/2019
ms.author: erhopf
ms.openlocfilehash: b17b7088276aca6d7d8ed8f2d1cb554479b4de2b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507635"
---
# <a name="long-audio-api-preview"></a>Dlouhé zvukové rozhraní API (Preview)

Rozhraní API pro dlouhé zvukové služby je navržené pro asynchronní syntézu dlouhého textu na řeč (například: zvukové knihy). Toto rozhraní API nevrátí syntetizované zvuky v reálném čase, místo toho, abyste se mohli dotazovat na odpovědi a využívat výstupy, když jsou zpřístupněné ze služby. Na rozdíl od rozhraní API pro rozpoznávání řeči používaného sadou Speech SDK může dlouhé zvukové rozhraní API vytvářet syntetizované zvuky delší než 10 minut, což je ideální pro vydavatele a platformy zvukového obsahu.

Další výhody pro dlouhé zvukové rozhraní API:

* Syntetizované rozpoznávání řeči vrácené službou používá hlasy neuronové, které zajišťují zvukové výstupy s vysokou přesností.
* Vzhledem k tomu, že se odpovědi v reálném čase nepodporují, není nutné nasazovat hlasový koncový bod.

## <a name="workflow"></a>Pracovní postupy

Při použití rozhraní API dlouhého zvuku se obvykle odesílá textový soubor nebo soubory, které se mají syntetizovat, dotaz na jeho stav, a pokud je stav úspěšný, můžete si stáhnout zvukový výstup.

Tento diagram poskytuje přehled o pracovním postupu na nejvyšší úrovni.

![Diagram pracovního postupu dlouhého zvukového rozhraní API](media/long-audio-api/long-audio-api-workflow.png)

## <a name="prepare-content-for-synthesis"></a>Příprava obsahu pro syntézu

Při přípravě textového souboru se ujistěte, že:

* Je buď prostý text (. txt), nebo SSML text (. txt)
  * U prostého textu je každý odstavec oddělený příkladem **zadání a návratového** [vstupu v prostém textu](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/en-US.txt) .
  * V případě textu SSML se každý SSMLový kus považuje za odstavec. SSML kusy musí být oddělené různými odstavci – [Příklad textového vstupu SSML textu](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/SSMLTextInputSample.txt). Kód jazyka najdete v tématu [SSML (Speech syntéz Markup Language)](speech-synthesis-markup.md) .
* Je kódovaný jako [UTF-8 s označením pořadí bajtů (BOM)](https://www.w3.org/International/questions/qa-utf8-bom.en#bom) .
* Obsahuje více než 10 000 znaků nebo více než 50 odstavců.
* Je jeden soubor, ne zip.

## <a name="audio-output-formats"></a>Formáty zvukového výstupu

Rozhraní API pro dlouhé zvukové rozhraní podporuje následující formáty zvukového výstupu:

> [!NOTE]
> Výchozí formát zvuku je RIFF-16khz-16bitový-mono-PCM.

* RIFF-8KHz-16bitový-mono-PCM
* RIFF-16khz-16bitový-mono-PCM
* RIFF-24khz-16bitový-mono-PCM
* RIFF-48kHz-16bitový-mono-PCM
* Audio-16khz-32kbitrate-mono-MP3
* Audio-16khz-64kbitrate-mono-MP3
* Audio-16khz-128kbitrate-mono-MP3
* Audio-24khz-48kbitrate-mono-MP3
* Audio-24khz-96kbitrate-mono-MP3
* Audio-24khz-160kbitrate-mono-MP3

## <a name="quickstarts"></a>Rychlé starty

Nabízíme rychlé starty, které vám pomůžou úspěšně spouštět rozhraní API pro dlouhé zvukové zařízení. Tato tabulka obsahuje seznam dlouhých rychlých startů rozhraní API pro přenos v jazyce.

* [Rychlý Start: Python](https://aka.ms/long-audio-python)

## <a name="sample-code"></a>Ukázka kódu
Vzorový kód pro dlouhé zvukové rozhraní API je k dispozici na GitHubu.

* [Vzorový kód: Python](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/Python)
* [Vzorový kód:C#](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/CustomVoice-API-Samples/CSharp)
* [Vzorový kód: Java](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Java/)

## <a name="see-also"></a>Další informace najdete v tématech

* [Reference k rozhraní API pro dlouhé zvukové rozhraní](https://aka.ms/long-audio-ref)
