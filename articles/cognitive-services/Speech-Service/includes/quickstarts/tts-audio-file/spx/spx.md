---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: 161138ba09baacdd2a3d18d35c930c645ee78103
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715157"
---
## <a name="run-the-spx-tool"></a>Spusťte nástroj SPX.

Nyní jste připraveni spustit nástroj SPX pro syntetizaci řeči z textu do nového zvukového souboru.

Z příkazového řádku přejděte do adresáře, který obsahuje binární soubor nástroje SPX, a zadejte:

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

Nástroj SPX vygeneruje do zvukového souboru přirozený jazyk v angličtině `greetings.wav` .
V systému Windows můžete přehrát zvukový soubor zadáním `start greetings.wav` .
