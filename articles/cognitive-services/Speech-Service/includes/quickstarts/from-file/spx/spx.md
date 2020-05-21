---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: 9ff21105e61be59b0000d86455ee19ba6f0d1aeb
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83714653"
---
## <a name="find-a-file-that-contains-speech"></a>Vyhledání souboru, který obsahuje řeč

Nástroj SPX dokáže rozpoznávat řeč v mnoha formátech souborů a přirozených jazycích. V tomto rychlém startu můžete použít soubor WAV (16kHz nebo 8kHz, 16 bitů a mono PCM), který obsahuje anglický hlas.

1. Stáhněte si soubor <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike. <span class="docon docon-download x-hidden-focus"></span> WAV </a>.
2. Zkopírujte `whatstheweatherlike.wav` soubor do stejného adresáře jako binární soubor nástroje SPX.

## <a name="run-the-spx-tool"></a>Spusťte nástroj SPX.

Teď jste připraveni spustit nástroj SPX pro rozpoznávání řeči nalezené ve zvukovém souboru.

V příkazovém řádku přejděte do adresáře, který obsahuje binární soubor nástroje SPX, a zadejte:

```bash
spx recognize --file whatstheweatherlike.wav
```

> [!NOTE]
> Nástroj SPX je ve výchozím nastavení English. [V tabulce převod řeči na text](../../../../language-support.md)můžete zvolit jiný jazyk.
> Přidejte například `--source de-DE` pro rozpoznávání rozpoznávání řeči v němčině.

Nástroj SPX zobrazí na obrazovce text přepisu řeči. Pak se nástroj SPX zavře.
