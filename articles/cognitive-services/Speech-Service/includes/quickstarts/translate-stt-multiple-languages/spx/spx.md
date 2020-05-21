---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: 94d50a15b0d5d7bed2bd43516952036044d7f0c0
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715088"
---
## <a name="run-the-spx-tool"></a>Spusťte nástroj SPX.

Teď jste připraveni spustit nástroj SPX pro převod řeči na text ve dvou různých jazycích.

V příkazovém řádku přejděte do adresáře, který obsahuje binární soubor nástroje SPX, a zadejte:

```bash
spx translate --microphone --target de-DE --target es-MX
```

Nástroj SPX převede přirozený jazyk mluveného slova na text vytištěný v němčině a (mexické) španělštině.
Stiskněte klávesu ENTER a nástroj se zastaví.

> [!NOTE]
> Nástroj SPX je ve výchozím nastavení English. [V tabulce převod řeči na text](../../../../language-support.md)můžete zvolit jiný jazyk.
> Přidejte například `--source ja-JP` pro rozpoznávání řeči v japonštině.
