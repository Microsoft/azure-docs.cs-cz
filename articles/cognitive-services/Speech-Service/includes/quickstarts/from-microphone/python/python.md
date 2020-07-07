---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: f26f0ab6da398dcdee307f89b27cca780d08af85
ms.sourcegitcommit: 374d1533ea2f2d9d3f8b6e6a8e65c6a5cd4aea47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2020
ms.locfileid: "85838924"
---
## <a name="prerequisites"></a>Požadavky

Než začnete:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Vytvoření prostředku Azure Speech<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)
> * Ujistěte se, že máte přístup k mikrofonu pro záznam zvuku

## <a name="source-code"></a>Zdrojový kód

Vytvořte soubor s názvem *quickstart.py* a vložte do něj následující kód Pythonu.

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/from-microphone/quickstart.py#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Vysvětlení kódu

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>Sestavení a spuštění aplikace

Nyní jste připraveni testovat rozpoznávání řeči pomocí služby Speech. 

Pokud tuto službu používáte v macOS a jedná se o první aplikaci v Pythonu, kterou jste sestavili tak, aby používala mikrofon, budete pravděpodobně muset dát terminálovému přístupu k mikrofonu. Otevřete **nastavení systému** a vyberte **zabezpečení & ochrana osobních údajů**. V dalším kroku vyberte možnost **soukromí** a v seznamu vyhledejte možnost **mikrofon** . Nakonec vyberte **terminály** a uložte. 

1. **Spusťte aplikaci** – z příkazového řádku zadejte:
    ```bash
    python quickstart.py
    ```
2. **Spustit rozpoznávání** – zobrazí výzvu k vymluvenému vynechání fráze v angličtině. Váš hlas se odešle službě Speech, přepisu jako text a vykreslí se v konzole nástroje.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
