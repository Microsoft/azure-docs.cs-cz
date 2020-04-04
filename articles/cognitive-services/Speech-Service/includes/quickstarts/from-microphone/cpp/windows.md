---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: 9e7716acfe95371c9e9734b6962b66c405bd47e4
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80659364"
---
## <a name="prerequisites"></a>Požadavky

Než začnete:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Vytvoření prostředku řeči Azure<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?tabs=windows)
> * Ujistěte se, že máte přístup k mikrofonu pro snímání zvuku

## <a name="source-code"></a>Zdrojový kód

Vytvořte zdrojový soubor Jazyka C++ s názvem *helloworld.cpp*a vložte do něj následující kód.

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-microphone/helloworld/helloworld.cpp#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Vysvětlení kódu

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>Vytváření a spouštění aplikací

1. Na řádku nabídek vyberte **sestavení** > **sestavení řešení** k sestavení aplikace. Kód by se teď měl zkompilovat bez chyb.

1. **Chcete-li** > spustit aplikaci **Helloworld,** zvolte Ladění**spouštění ladění** (nebo stiskněte <kbd>klávesu F5).</kbd>

1. Vyslovte anglickou frázi nebo větu. Aplikace přenáší váš projev do služby Speech, která přepisuje na text a odešle jej zpět do aplikace pro zobrazení.

   ![Výstup konzoly po úspěšném rozpoznání](~/articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](../footer.md)]