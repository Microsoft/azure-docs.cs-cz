---
title: 'Rychlý start: Syntetizace řeči, jazyka C++ (Windows) – hlasové služby'
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak tak, aby odpovídaly řeči v jazyce C++ na ploše Windows s použitím sadou SDK pro řeč
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 4/03/2019
ms.author: yinhew
ms.openlocfilehash: d818f8a8fc813d3da5eea4d8ef9cd2f4fd18bb53
ms.sourcegitcommit: 1aab8b764d767d86fbf262bda34996189d00c7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2019
ms.locfileid: "64402650"
---
# <a name="quickstart-synthesize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Rychlý start: Syntetizace řeči v C++ ve Windows s použitím sadou SDK pro řeč

V tomto článku vytvoříte konzolovou aplikaci pro Windows v jazyce C++. Použití služeb Cognitive Services [sadou SDK pro řeč](speech-sdk.md) syntetizace řeč z textu v reálném čase a přehrát řeč na váš počítač mluvčího. Tato aplikace je vytvořená pomocí [balíčku NuGet sady Speech SDK](https://aka.ms/csspeech/nuget) a sady Microsoft Visual Studio 2017 (libovolné edice).

Funkce popsané v tomto článku je k dispozici z [sadou SDK pro řeč 1.4.0](https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech/1.4.0).

Úplný seznam jazyků/hlasy dostupné pro syntézu řeči, naleznete v tématu [jazykovou podporu](language-support.md#text-to-speech).

## <a name="prerequisites"></a>Požadavky

Klíč předplatného hlasové služby k dokončení tohoto rychlého startu potřebujete. Můžete ho získat zdarma. Zobrazit [hlasové služby si můžete vyzkoušet zdarma](get-started.md) podrobnosti.

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

1. Zobrazí se okno konzoly vás vyzve k zadání nějaký text. Zadejte pár slov nebo věty. Text, který jste zadali se přenášejí do služeb řeči a syntetizovat do mluvené řeči, který přehraje na reproduktoru.

   ![Snímek obrazovky výstup na konzole po úspěšné syntézu](media/sdk/qs-tts-cpp-windows-console-output.png)

## <a name="next-steps"></a>Další postup

Další ukázky, jako je například ukládání řeči zvukový soubor, jsou k dispozici na Githubu.

> [!div class="nextstepaction"]
> [Prozkoumejte C++ – ukázky na Githubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Další informace najdete v tématech

- [Přizpůsobení hlasová písma](how-to-customize-voice-font.md)
- [Záznam hlasu ukázky](record-custom-voice-samples.md)
