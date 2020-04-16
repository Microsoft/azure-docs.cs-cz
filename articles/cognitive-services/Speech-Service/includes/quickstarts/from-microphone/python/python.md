---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: 3613d190ef079d0e477d42b426a224d8e4dda7e6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400693"
---
## <a name="prerequisites"></a>Požadavky

Než začnete:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Vytvoření prostředku řeči Azure<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)
> * Ujistěte se, že máte přístup k mikrofonu pro snímání zvuku

## <a name="source-code"></a>Zdrojový kód

Vytvořte soubor s názvem *quickstart.py* a vložte do něj následující kód Pythonu.

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/from-microphone/quickstart.py#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Vysvětlení kódu

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>Vytváření a spouštění aplikací

Teď jste připraveni otestovat aplikaci a ověřit funkci rozpoznávání řeči pomocí služby Řeč.

1. **Spuštění aplikace** – Z příkazového řádku zadejte:
    ```bash
    python quickstart.py
    ```
2. **Start uznání** - Vyzve vás mluvit frázi v angličtině. Vaše řeč je odeslána do služby Řeč, přepsána jako text a vykreslena v konzole.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

