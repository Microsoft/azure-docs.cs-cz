---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 09/08/2020
ms.author: trbye
ms.openlocfilehash: 905eacc3751b3d5d6c66a2fdb0e1391a747ab895
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91327027"
---
Jednou z klíčových funkcí služby Speech je možnost rozpoznávat a přepisovat lidské řeči (často se označuje jako převod řeči na text). V tomto rychlém startu se dozvíte, jak používat rozhraní příkazového řádku pro rozpoznávání řeči v aplikacích a produktech k provádění vysoce kvalitních převodů řeči na text.

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