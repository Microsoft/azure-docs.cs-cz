---
title: 'Rychlý start: Rozpoznávání řeči, .NET Framework (Windows) – služba Speech'
titleSuffix: Azure Cognitive Services
description: Pomocí této příručky vytvoříte konzolovou aplikaci pro převod řeči na text s využitím rozhraní .NET Framework pro Windows a sady Speech SDK. Po dokončení můžete použít mikrofon vašeho počítače a v reálném čase přepisovat řeč na text.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: wolfma
ms.openlocfilehash: cb140647394858fbc0a9a00ea125365d5b7a08d5
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327058"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Rychlý start: Rozpoznávání řeči pomocí sady Speech SDK pro .NET Framework (Windows)

K dispozici jsou také rychlé starty pro [syntézu řeči](quickstart-text-to-speech-dotnet-windows.md) a [Překlad řeči](quickstart-translate-speech-dotnetframework-windows.md).

Pokud chcete, vyberte jiný programovací jazyk nebo prostředí:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Pomocí této příručky vytvoříte konzolovou aplikaci pro převod řeči na text s využitím rozhraní .NET Framework pro Windows a sady Speech SDK. Po dokončení můžete použít mikrofon vašeho počítače a v reálném čase přepisovat řeč na text.

Pro rychlou ukázku (bez sestavování projektu sady Visual Studio, jak je popsáno v tomto článku), získáte nejnovější [ukázky sady Cognitive Services Speech SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk) z GitHubu.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto projektu budete potřebovat:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Klíč předplatného pro službu rozpoznávání řeči [Získejte je zdarma](get-started.md).
* Přístup k mikrofonu vašeho počítače.

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete **program.cs** a nahraďte automaticky generovaný kód touto ukázkou:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Vyhledejte řetězec `YourSubscriptionKey` a nahraďte ho klíčem předplatného služby Speech Services.

1. Vyhledejte řetězec `YourServiceRegion` a nahraďte ho [oblastí](regions.md) , která je přidružená k vašemu předplatnému. Pokud například používáte bezplatné zkušební předplatné, oblast je `westus`.

1. V řádku nabídek vyberte **soubor** > **Uložit vše**.

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

1. V řádku nabídek vyberte **sestavení** **řešení**  >  pro sestavení aplikace. Kód by se teď měl zkompilovat bez chyb.

1. Zvolte možnost **ladění** > **Spustit ladění** (nebo vyberte **F5**) a spusťte aplikaci **HelloWorld** .

1. Přemluvte si anglickou frázi nebo větu do mikrofonu vašeho zařízení. Aplikace přenáší váš hlas do služeb Speech, které pošle text přepisu zpět do aplikace k zobrazení.

   ![Uživatelské rozhraní rozpoznávání řeči](media/sdk/qs-csharp-dotnet-windows-10-console-output.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte C# ukázky na GitHubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Viz také:

- [Výuka modelu pro Custom Speech](how-to-custom-speech-train-model.md)
