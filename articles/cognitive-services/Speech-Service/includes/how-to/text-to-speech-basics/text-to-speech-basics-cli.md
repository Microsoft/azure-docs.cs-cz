---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: b68025c1b33a94f03e2d84693a7d6407a18abd88
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91332424"
---
V tomto rychlém startu se naučíte běžné vzory návrhu pro provádění syntézy textu na řeč pomocí sady Speech SDK. Začnete tím, že provádíte základní konfiguraci a shrnutí a přejdete k pokročilejším příkladům pro vývoj vlastních aplikací, včetně:

* Získávání odpovědí jako proudů v paměti
* Přizpůsobení výstupní vzorkovací frekvence a přenosové rychlosti
* Odesílání požadavků na syntézu pomocí SSML (Speech syntézy Markup Language)
* Používání hlasů neuronové

## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že máte účet Azure a předplatné služby Speech. Pokud účet a předplatné nemáte, [Vyzkoušejte službu Speech Service zdarma](../../../overview.md#try-the-speech-service-for-free).

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