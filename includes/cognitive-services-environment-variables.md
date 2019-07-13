---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 6d6451d50a00569eb1da8f5b0a0dc10d3c6b1115
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67841391"
---
## <a name="configure-an-environment-variable-for-authentication"></a>Konfigurace proměnné prostředí pro ověřování

Aplikace potřebují k ověření přístupu k jejich použití služeb Cognitive Services. Pokud chcete ověřit, doporučujeme vytvořit proměnnou prostředí pro ukládání klíčů pro vaše prostředky Azure. 

Až budete mít klíč zapisovat do nové proměnné prostředí v místním počítači spustíte aplikaci. Proměnnou prostředí nastavíte tak, že otevřete okno konzoly a budete postupovat podle pokynů pro váš operační systém. Nahraďte `your-key` s jedním z klíčů pro váš prostředek.

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
