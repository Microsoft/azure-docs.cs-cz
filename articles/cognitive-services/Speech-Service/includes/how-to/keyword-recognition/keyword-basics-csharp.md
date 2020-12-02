---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 11/03/2020
ms.author: trbye
ms.openlocfilehash: d71a7f6451cda5e2e50b5410140ac88361bf1735
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96509506"
---
Nejprve načtěte soubor modelu klíčových slov pomocí `FromFile()` statické funkce, která vrátí `KeywordRecognitionModel` . Použijte cestu k `.table` souboru, který jste stáhli ze sady Speech Studio. Kromě toho se vytvoří `AudioConfig` pomocí výchozího mikrofonu a pak se vytvoří instance nového `KeywordRecognizer` pomocí konfigurace zvuku.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

V dalším kroku je spuštění rozpoznávání klíčového slova provedeno s jedním voláním metody `RecognizeOnceAsync()` předáním objektu modelu. Tím spustíte relaci rozpoznávání klíčových slov, která trvá, dokud nebude klíčové slovo rozpoznáno. Tento vzor návrhu se proto obecně používá v aplikacích s více vlákny nebo v případech použití, kde můžete počkat na neomezenou dobu probuzení slova.

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> Příklad, který je zde znázorněn, používá místní rozpoznávání klíčového slova, protože nevyžaduje `SpeechConfig` objekt pro kontext ověřování a nekontaktuje back-end. Rozpoznávání klíčových slov a ověřování však můžete spustit [pomocí nepřetržitého back-endu připojení](../../../tutorial-voice-enable-your-bot-speech-sdk.md#view-the-source-code-that-enables-keyword).