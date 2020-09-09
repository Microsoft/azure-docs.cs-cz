---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 09/08/2020
ms.author: trbye
ms.openlocfilehash: 9c2c46040dd741253e9a68855c4dca89c1dc9a9a
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2020
ms.locfileid: "89570598"
---
[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="speech-to-text-from-microphone"></a>Převod řeči na text z mikrofonu

Připojte se k počítači mikrofonu a zapněte ho a vypněte všechny aplikace, které můžou mikrofon používat. Některé počítače mají integrovaný mikrofon, zatímco jiné vyžadují konfiguraci zařízení Bluetooth.

Teď jste připraveni spustit rozhraní příkazového řádku pro rozpoznávání řeči z mikrofonu. Z příkazového řádku přejděte do adresáře, který obsahuje binární soubor CLI rozpoznávání řeči, a spusťte následující příkaz.

```bash
spx recognize --microphone
```

> [!NOTE]
> Rozpoznávání řeči je výchozím nastavením angličtiny. [V tabulce převod řeči na text](../../../../language-support.md)můžete zvolit jiný jazyk.
> Přidejte například `--source de-DE` pro rozpoznávání rozpoznávání řeči v němčině.

Mluvte do mikrofonu a uvidíte přepis svých slov na text v reálném čase. Rozpoznávání řeči se zastaví po určité době tichého běhu nebo po stisknutí kombinace kláves CTRL + C.

## <a name="speech-to-text-from-audio-file"></a>Převod řeči na text ze zvukového souboru

Rozhraní příkazového řádku pro rozpoznávání řeči dokáže rozpoznávat řeč v mnoha formátech souborů a přirozených jazycích. V tomto příkladu můžete použít libovolný soubor WAV (16kHz nebo 8kHz, 16 bitů a mono PCM), který obsahuje anglický hlas. Nebo pokud chcete rychle vzorkovat, Stáhněte si soubor <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike. wav <span class="docon docon-download x-hidden-focus"></span> </a> a zkopírujte ho do stejného adresáře jako binární soubor CLI rozpoznávání řeči.

Nyní jste připraveni spustit rozhraní příkazového řádku pro rozpoznávání řeči nalezené ve zvukovém souboru spuštěním následujícího příkazu.

```bash
spx recognize --file whatstheweatherlike.wav
```

> [!NOTE]
> Rozpoznávání řeči je výchozím nastavením angličtiny. [V tabulce převod řeči na text](../../../../language-support.md)můžete zvolit jiný jazyk.
> Přidejte například `--source de-DE` pro rozpoznávání rozpoznávání řeči v němčině.

Rozhraní příkazového řádku pro rozpoznávání řeči zobrazí text na obrazovce.