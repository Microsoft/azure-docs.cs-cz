---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/04/2020
ms.author: dapine
ms.openlocfilehash: 33173390b9bdd874936ac6625e1755b96297cd7c
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2020
ms.locfileid: "80671700"
---
## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

> [!div class="checklist"]
> * [Vytvoření prostředku řeči Azure](../../../../get-started.md)
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?tabs=windows&pivots=programming-language-cpp)

[!INCLUDE [Audio input format](~/articles/cognitive-services/speech-service/includes/audio-input-format-chart.md)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete zdrojový soubor **helloworld.cpp**.

1. Nahraďte celý kód následujícím fragmentem:
   
   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-file/helloworld/helloworld.cpp#code)]

1. Ve stejném souboru nahraďte řetězec `YourSubscriptionKey` klíčem předplatného.

1. Nahraďte `YourServiceRegion` řetězec **identifikátorem oblasti** z [oblasti](https://aka.ms/speech/sdkregion) přidružené `westus` k vašemu předplatnému (například pro bezplatné zkušební předplatné).

1. Nahraďte `whatstheweatherlike.wav` řetězec vlastním názvem souboru.

1. Na řádku nabídek zvolte **Soubor** > **Uložit vše**.

> [!NOTE]
> Sada Speech SDK bude ve výchozím nastavení rozpoznána použití jazyka en-us, viz [Určení zdrojového jazyka pro řeč na text,](../../../../how-to-specify-source-language.md) kde naleznete informace o výběru zdrojového jazyka.

## <a name="build-and-run-the-application"></a>Sestavení a spuštění aplikace

1. Na řádku nabídek vyberte **sestavení** > **sestavení řešení** k sestavení aplikace. Kód by se teď měl zkompilovat bez chyb.

1. **Chcete-li** > spustit aplikaci **Helloworld,** zvolte Ladění**spouštění ladění** (nebo stiskněte **klávesu F5).**

1. Zvukový soubor je přenášen do služby Rozpoznávání řeči a první utterance v souboru je přepsán a text, který se zobrazí ve stejném okně.

   ```text
   Recognizing first result...
   We recognized: What's the weather like?
   ```

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]