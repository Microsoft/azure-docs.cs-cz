---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: bee28c946242ecf227287fb36b5b03aa6defb1c2
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2020
ms.locfileid: "88173120"
---
[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="synthesize-speech-to-a-speaker"></a>Syntéza řeči do reproduktoru

Teď jste připraveni spustit rozhraní příkazového řádku pro rozpoznávání řeči od textu. Z příkazového řádku přejděte do adresáře, který obsahuje binární soubor funkce Speech CLI. Pak spusťte následující příkaz.

```bash
spx synthesize --text "The speech synthesizer greets you!"
```

Rozhraní příkazového řádku pro rozpoznávání řeči vytvoří přirozený jazyk v angličtině prostřednictvím mluvčího počítače.

## <a name="synthesize-speech-to-a-file"></a>Vysyntetizátorování řeči v souboru

Spuštěním následujícího příkazu změňte výstup z vašeho mluvčího na `.wav` soubor.

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

Rozhraní příkazového řádku pro rozpoznávání řeči vytvoří v angličtině přirozený jazyk `greetings.wav` .
V systému Windows můžete přehrát zvukový soubor zadáním `start greetings.wav` .