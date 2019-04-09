---
title: 'Rychlý start: Syntetizace řeči, rozhraní .NET Framework (Windows) – hlasové služby'
titleSuffix: Azure Cognitive Services
description: Použijte tuto příručku k vytvoření převod textu na řeč konzolové aplikace pro Windows a sadou SDK pro řeč pomocí rozhraní .NET framework. Až budete hotovi, můžete syntetizace řeč z textu a poslechněte si řeč na reproduktoru v reálném čase.
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 4/03/2019
ms.author: yinhew
ms.openlocfilehash: a013189e45b1c1c8eeb88d62a718d495c0c415a2
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/04/2019
ms.locfileid: "59012245"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Rychlý start: Syntetizace řeči se sadou SDK pro řeč pro rozhraní .NET Framework (Windows)

Použijte tuto příručku k vytvoření převod textu na řeč konzolové aplikace pro Windows a sadou SDK pro řeč pomocí rozhraní .NET framework. Až budete hotovi, můžete syntetizace řeč z textu a poslechněte si řeč na reproduktoru v reálném čase.

Rychlé ukázku (bez sestavování projektu sady Visual Studio sami, jak je uvedeno níže):

Získat všechny nejnovější [Cognitive Services řeči SDK ukázky](https://github.com/Azure-Samples/cognitive-services-speech-sdk) z Githubu.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto projektu budete potřebovat:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Klíč předplatného pro službu rozpoznávání řeči. [Získat zdarma](get-started.md).
* Mluvčího (nebo sluchátka s mikrofonem) k dispozici.

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete soubor `Program.cs` a nahraďte automaticky vygenerovaný kód tímto vzorovým kódem:

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Najít a nahradit řetězec `YourSubscriptionKey` s klíči předplatného hlasové služby.

1. Vyhledejte řetězec `YourServiceRegion` a nahraďte ho [oblastí](regions.md) přidruženou k vašemu předplatnému. Pokud například používáte bezplatnou zkušební verzi, oblast je `westus`.

1. Uložte změny do projektu.

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

1. V řádku nabídek vyberte **Sestavení** > **Sestavit řešení**. Kód by se teď měl zkompilovat bez chyb.

    ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Sestavit řešení](media/sdk/qs-csharp-dotnet-windows-08-build.png "Úspěšné sestavení")

1. Aplikaci spustíte tak, že na řádku nabídek vyberete **Ladit** > **Spustit ladění** nebo stisknete klávesu **F5**.

    ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Spustit ladění](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Spuštění aplikace s laděním")

1. Zobrazí se okno konzoly, vás vyzve k zadání nějaký text. Zadejte pár slov nebo věty. Text, který jste zadali se přenášejí do služeb řeči a syntetizovat do mluvené řeči, který přehraje na reproduktoru.

    ![Snímek obrazovky s výstupem konzoly po úspěšném rozpoznání](media/sdk/qs-tts-csharp-dotnet-windows-console-output.png "Výstup konzoly po úspěšném rozpoznání")

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Prozkoumejte C# ukázky na Githubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Další informace najdete v tématech

- [Přizpůsobení hlasových písem](how-to-customize-voice-font.md)
- [Záznam hlasových ukázek](record-custom-voice-samples.md)
