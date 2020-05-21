---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: 9f1da13efc9f75d14fdf2d158e8b7547d4a9fa94
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715293"
---
## <a name="enable-microphone"></a>Povolit mikrofon

Připojte se k počítači mikrofonu a zapněte ho a vypněte všechny aplikace, které můžou mikrofon používat. Některé počítače mají integrovaný mikrofon, zatímco jiné vyžadují konfiguraci zařízení Bluetooth.

## <a name="run-the-spx-tool"></a>Spusťte nástroj SPX.

Teď jste připraveni spustit nástroj SPX pro rozpoznávání řeči z mikrofonu.

1. **Spusťte aplikaci** – z příkazového řádku přejděte do adresáře, který obsahuje binární soubor nástroje SPX, a zadejte:
    ```bash
    spx recognize --microphone
    ```

    > [!NOTE]
    > Nástroj SPX je ve výchozím nastavení English. [V tabulce převod řeči na text](../../../../language-support.md)můžete zvolit jiný jazyk.
    > Přidejte například `--source de-DE` pro rozpoznávání rozpoznávání řeči v němčině.

2. **Spusťte rozpoznávání** – mluvte do mikrofonu. V reálném čase uvidíte přepis svých slov na text. Nástroj SPX se zastaví po určité době tichého běhu nebo po stisknutí kombinace kláves CTRL + C.
