---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: bd48618a520f547c72bfba7ff04ae6a249d0a673
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806430"
---
## <a name="run-the-speech-cli"></a>Spuštění funkce CLI pro rozpoznávání řeči

Teď jste připraveni spustit rozhraní příkazového řádku pro rozpoznávání řeči z textu do nového zvukového souboru.

V příkazovém řádku přejděte do adresáře, který obsahuje binární soubor CLI rozpoznávání řeči, a zadejte:

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

Rozhraní příkazového řádku pro rozpoznávání řeči vytvoří v angličtině přirozený jazyk `greetings.wav` .
V systému Windows můžete přehrát zvukový soubor zadáním `start greetings.wav` .
