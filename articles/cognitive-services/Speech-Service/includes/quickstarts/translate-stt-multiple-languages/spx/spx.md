---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: c73ee93d89a350763e8ced490187e1c47f8918de
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806564"
---
## <a name="run-the-speech-cli"></a>Spuštění funkce CLI pro rozpoznávání řeči

Teď jste připraveni spustit rozhraní příkazového řádku pro rozpoznávání řeči pro převod řeči na text ve dvou různých jazycích.

V příkazovém řádku přejděte do adresáře, který obsahuje binární soubor CLI rozpoznávání řeči, a zadejte:

```bash
spx translate --microphone --target de-DE --target es-MX
```

Rozhraní příkazového řádku pro rozpoznávání řeči převede přirozený jazyk na text vytištěný v němčině a (mexické) španělštině.
Stiskněte klávesu ENTER a nástroj se zastaví.

> [!NOTE]
> Rozpoznávání řeči je výchozím nastavením angličtiny. [V tabulce převod řeči na text](../../../../language-support.md)můžete zvolit jiný jazyk.
> Přidejte například `--source ja-JP` pro rozpoznávání řeči v japonštině.
