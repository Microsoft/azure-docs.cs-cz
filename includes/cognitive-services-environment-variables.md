---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 734fce2c01614d5dca73f171fb59f25f39d13705
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461493"
---
## <a name="configure-an-environment-variable-for-authentication"></a>Konfigurace proměnné prostředí pro ověřování

Aplikace potřebují k ověření přístupu k jejich použití služeb Cognitive Services. Pokud chcete ověřit, doporučujeme vytvořit proměnnou prostředí k uložení klíče ve vašich prostředcích Azure. 

Až budete mít klíč zapisovat do nové proměnné prostředí v místním počítači spustíte aplikaci. Proměnnou prostředí nastavíte tak, že otevřete okno konzoly a budete postupovat podle pokynů pro váš operační systém. Nahraďte `your-key` přístupovým klíčem vašeho detektor anomálií:

* Windows

    ```console
    setx COGNITIVE_SERVICE_KEY "your-key"
    ```

    Po přidání proměnné prostředí možná bude nutné restartovat všechny spuštěné programy, které budou potřebovat číst tuto proměnnou prostředí, a to včetně okna konzoly. Například pokud jako editor používáte Visual Studio, restartujte aplikaci Visual Studio před spuštěním v příkladu.

* Linux
    
    ```bash
    export COGNITIVE_SERVICE_KEY=your-key
    ```
    
    Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source ~/.bashrc`, aby se změny projevily.
    
* macOS
    
    Upravte svůj soubor .bash_profile a přidejte do něj proměnnou prostředí:
    
    ```bash
    export COGNITIVE_SERVICE_KEY=your-key
    ```
    
    Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source .bash_profile`, aby se změny projevily.
