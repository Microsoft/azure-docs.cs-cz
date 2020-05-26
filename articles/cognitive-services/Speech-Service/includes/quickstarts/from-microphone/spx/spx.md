---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: a9beaea70dd3c8ce852344b11a50c5f20e5f8ca2
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806275"
---
## <a name="enable-microphone"></a>Povolit mikrofon

Připojte se k počítači mikrofonu a zapněte ho a vypněte všechny aplikace, které můžou mikrofon používat. Některé počítače mají integrovaný mikrofon, zatímco jiné vyžadují konfiguraci zařízení Bluetooth.

## <a name="run-the-speech-cli"></a>Spuštění funkce CLI pro rozpoznávání řeči

Teď jste připraveni spustit rozhraní příkazového řádku pro rozpoznávání řeči z mikrofonu.

1. **Spusťte aplikaci** – z příkazového řádku přejděte do adresáře, který obsahuje binární soubor CLI rozpoznávání řeči, a zadejte:
    ```bash
    spx recognize --microphone
    ```

    > [!NOTE]
    > Rozpoznávání řeči je výchozím nastavením angličtiny. [V tabulce převod řeči na text](../../../../language-support.md)můžete zvolit jiný jazyk.
    > Přidejte například `--source de-DE` pro rozpoznávání rozpoznávání řeči v němčině.

2. **Spusťte rozpoznávání** – mluvte do mikrofonu. V reálném čase uvidíte přepis svých slov na text. Rozpoznávání řeči se zastaví po určité době tichého běhu nebo po stisknutí kombinace kláves CTRL + C.
