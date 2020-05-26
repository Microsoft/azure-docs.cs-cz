---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: 398dfe36314f86614515168986023146d695e69b
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806340"
---
## <a name="find-a-file-that-contains-speech"></a>Vyhledání souboru, který obsahuje řeč

Rozhraní příkazového řádku pro rozpoznávání řeči dokáže rozpoznávat řeč v mnoha formátech souborů a přirozených jazycích. V tomto rychlém startu můžete použít soubor WAV (16kHz nebo 8kHz, 16 bitů a mono PCM), který obsahuje anglický hlas.

1. Stáhněte si soubor <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike. <span class="docon docon-download x-hidden-focus"></span> WAV </a>.
2. Zkopírujte `whatstheweatherlike.wav` soubor do stejného adresáře jako binární soubor CLI rozpoznávání řeči.

## <a name="run-the-speech-cli"></a>Spuštění funkce CLI pro rozpoznávání řeči

Teď jste připraveni spustit rozhraní příkazového řádku pro rozpoznávání řeči nalezené ve zvukovém souboru.

V příkazovém řádku přejděte do adresáře, který obsahuje binární soubor CLI rozpoznávání řeči, a zadejte:

```bash
spx recognize --file whatstheweatherlike.wav
```

> [!NOTE]
> Rozpoznávání řeči je výchozím nastavením angličtiny. [V tabulce převod řeči na text](../../../../language-support.md)můžete zvolit jiný jazyk.
> Přidejte například `--source de-DE` pro rozpoznávání rozpoznávání řeči v němčině.

Rozhraní příkazového řádku pro rozpoznávání řeči zobrazí text na obrazovce. Pak bude rozhraní příkazového řádku pro rozpoznávání řeči zavřeno.
