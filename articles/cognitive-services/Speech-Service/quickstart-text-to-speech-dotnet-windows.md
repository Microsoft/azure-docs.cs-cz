---
title: 'Rychlý start: Rozpoznávání řeči, .NET Framework (Windows) – služba Speech'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto průvodce můžete vytvořit konzolovou aplikaci pro převod textu na řeč pomocí rozhraní .NET Framework pro Windows a sady Speech SDK. Až budete hotovi, můžete řeč z textu a slyšet řeč na mluvčím v reálném čase.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: yinhew
ms.openlocfilehash: 3c925e0160a4c57f7e43c3463fc1db7098a6e606
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68553592"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Rychlý start: Vysyntetizujte řeč pomocí sady Speech SDK pro .NET Framework (Windows)

K dispozici jsou také rychlé starty pro [rozpoznávání řeči](quickstart-csharp-dotnet-windows.md) a [Překlad řeči](quickstart-translate-speech-dotnetframework-windows.md).

Pomocí tohoto průvodce můžete vytvořit konzolovou aplikaci pro převod textu na řeč pomocí rozhraní .NET Framework pro Windows a sady Speech SDK. Až budete hotovi, můžete řeč z textu a slyšet řeč na mluvčím v reálném čase.

Pro rychlou ukázku (bez sestavování projektu sady Visual Studio, jak je vidět níže):

Získejte nejnovější [Cognitive Services ukázky sady Speech SDK](https://github.com/Azure-Samples/cognitive-services-speech-sdk) z GitHubu.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto projektu budete potřebovat:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Klíč předplatného pro službu rozpoznávání řeči [Získejte je zdarma](get-started.md).
* K dispozici je mluvčí (nebo náhlavní souprava).

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete soubor `Program.cs` a nahraďte automaticky vygenerovaný kód tímto vzorovým kódem:

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Vyhledejte řetězec `YourSubscriptionKey` a nahraďte ho klíčovým předplatným služby Speech Services.

1. Vyhledejte řetězec `YourServiceRegion` a nahraďte ho [oblastí](regions.md) přidruženou k vašemu předplatnému. Pokud například používáte bezplatnou zkušební verzi, oblast je `westus`.

1. Uložte změny do projektu.

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

1. V řádku nabídek vyberte **Sestavení** > **Sestavit řešení**. Kód by se teď měl zkompilovat bez chyb.

    ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Sestavit řešení](media/sdk/qs-csharp-dotnet-windows-08-build.png "Úspěšné sestavení")

1. Aplikaci spustíte tak, že na řádku nabídek vyberete **Ladit** > **Spustit ladění** nebo stisknete klávesu **F5**.

    ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Spustit ladění](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Spuštění aplikace s laděním")

1. Zobrazí se okno konzoly, které vás vyzve k zadání textu. Zadejte několik slov nebo větu. Text, který jste zadali, se přenáší do služeb řeči a syntetizuje se na řeč, který hraje na reproduktoru.

    ![Snímek obrazovky s výstupem konzoly po úspěšném rozpoznání](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "Výstup konzoly po úspěšném rozpoznání")

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Prozkoumejte C# ukázky na GitHubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Viz také:

- [Přizpůsobení hlasových písem](how-to-customize-voice-font.md)
- [Záznam ukázek hlasu](record-custom-voice-samples.md)
