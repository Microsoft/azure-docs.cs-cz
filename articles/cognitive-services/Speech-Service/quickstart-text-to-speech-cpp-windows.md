---
title: 'Rychlý start: Syntetizace řeči, C++ (Windows) – služba Speech'
titleSuffix: Azure Cognitive Services
description: Naučte se, jak na ploše C++ Windows syntetizovat řeč pomocí sady Speech SDK
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: yinhew
ms.openlocfilehash: 6936d8f93221402fca9b250d09a842ce753e0e12
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559308"
---
# <a name="quickstart-synthesize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Rychlý start: Používání sady Speech SDK C++ v systému Windows syntetiz řeči

K dispozici jsou také rychlé starty pro [rozpoznávání řeči](quickstart-cpp-windows.md) a [Překlad řeči](quickstart-translate-speech-cpp-windows.md).

V tomto článku vytvoříte konzolovou aplikaci pro Windows v jazyce C++. [Sada Cognitive Services Speech SDK](speech-sdk.md) slouží k syntetizování řeči z textu v reálném čase a přehrávání řeči v mluvčím počítače. Tato aplikace je vytvořená pomocí [balíčku NuGet sady Speech SDK](https://aka.ms/csspeech/nuget) a sady Microsoft Visual Studio 2017 (libovolné edice).

Funkce popsaná v tomto článku je k dispozici v [sadě Speech SDK 1.5.0](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/1.5.0).

Úplný seznam jazyků a hlasů dostupných pro syntézu řeči najdete v tématu [Podpora jazyků](language-support.md#text-to-speech).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu budete potřebovat klíč předplatného služby Speech Services. Můžete ho získat zdarma. Další informace najdete v tématu o bezplatném pokusu o [hlasové služby](get-started.md) .

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

[!INCLUDE [Quickstart C++ project](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete zdrojový soubor *helloworld.cpp*. Nahraďte všechen kód pod úvodním příkazem include (`#include "stdafx.h"` nebo `#include "pch.h"`) následujícím kódem:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/cpp-windows/helloworld/helloworld.cpp#code)]

1. Ve stejném souboru nahraďte řetězec `YourSubscriptionKey` klíčem předplatného.

1. Řetězec `YourServiceRegion` nahraďte [oblastí](regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

1. Uložte změny do projektu.

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

1. Sestavte aplikaci. V řádku nabídek zvolte **Sestavení** > **Sestavit řešení**. Kód by se měl zkompilovat bez chyb.

   ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Sestavit řešení](media/sdk/qs-cpp-windows-06-build.png)

1. Spusťte aplikaci. V řádku nabídek zvolte **Ladit** > **Spustit ladění** nebo stiskněte klávesu **F5**.

   ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Spustit ladění](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. Zobrazí se okno konzoly, které vás vyzve k zadání textu. Zadejte několik slov nebo větu. Text, který jste zadali, se přenáší do služeb řeči a syntetizuje se na řeč, který hraje na reproduktoru.

   ![Snímek obrazovky s výstupem konzoly po úspěšném Shrnutí](media/sdk/qs-tts-cpp-windows-console-output.png)

## <a name="next-steps"></a>Další postup

Další ukázky, jako je například ukládání řeči do zvukového souboru, jsou k dispozici na GitHubu.

> [!div class="nextstepaction"]
> [Prozkoumejte C++ ukázky na GitHubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Viz také:

- [Přizpůsobení hlasových písem](how-to-customize-voice-font.md)
- [Záznam ukázek hlasu](record-custom-voice-samples.md)
