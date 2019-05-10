---
title: 'Rychlý start: Rozpoznávání řeči, rozhraní .NET Framework (Windows) – hlasové služby'
titleSuffix: Azure Cognitive Services
description: Pomocí této příručky vytvoříte konzolovou aplikaci pro převod řeči na text s využitím rozhraní .NET Framework pro Windows a sady Speech SDK. Po dokončení můžete použít mikrofon vašeho počítače a v reálném čase přepisovat řeč na text.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: wolfma
ms.openlocfilehash: d297addbeffcc530ba676e4132f9f0ebdceac3c9
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65466999"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-net-framework-windows"></a>Rychlý start: Rozpoznávání řeči se sadou SDK pro řeč pro rozhraní .NET Framework (Windows)

Rychlí průvodci jsou také k dispozici pro [převod textu na řeč](quickstart-text-to-speech-dotnet-windows.md) a [překlad řeči](quickstart-translate-speech-dotnetframework-windows.md).

V případě potřeby vyberte jiný programovací jazyk a/nebo prostředí:<br/>
[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Pomocí této příručky vytvoříte konzolovou aplikaci pro převod řeči na text s využitím rozhraní .NET Framework pro Windows a sady Speech SDK. Po dokončení můžete použít mikrofon vašeho počítače a v reálném čase přepisovat řeč na text.

Rychlé ukázku (bez sestavování projektu sady Visual Studio sami, jak je uvedeno níže):

Získat všechny nejnovější [Cognitive Services řeči SDK ukázky](https://github.com/Azure-Samples/cognitive-services-speech-sdk) z Githubu.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto projektu budete potřebovat:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Klíč předplatného pro službu rozpoznávání řeči. [Získat zdarma](get-started.md).
* Přístup k mikrofonu vašeho počítače

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

[!INCLUDE [Create project](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete soubor `Program.cs` a nahraďte automaticky vygenerovaný kód tímto vzorovým kódem:

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Najít a nahradit řetězec `YourSubscriptionKey` s klíči předplatného hlasové služby.

1. Vyhledejte řetězec `YourServiceRegion` a nahraďte ho [oblastí](regions.md) přidruženou k vašemu předplatnému. Pokud například používáte bezplatnou zkušební verzi, oblast je `westus`.

1. Uložte změny do projektu.

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

1. V řádku nabídek vyberte **Sestavení** > **Sestavit řešení**. Kód by se teď měl zkompilovat bez chyb.

    ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Sestavit řešení](media/sdk/qs-csharp-dotnet-windows-08-build.png "Úspěšné sestavení")

1. Aplikaci spustíte tak, že na řádku nabídek vyberete **Ladit** > **Spustit ladění** nebo stisknete klávesu **F5**.

    ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Spustit ladění](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Spuštění aplikace s laděním")

1. Zobrazí se okno konzoly s výzvou k mluvení. Teď řekněte něco v angličtině. Vaše řeči se přenášejí do služeb řeči a převede na text v reálném čase. Výsledek se zobrazí v konzole.

    ![Snímek obrazovky s výstupem konzoly po úspěšném rozpoznání](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "Výstup konzoly po úspěšném rozpoznání")

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Prozkoumejte C# ukázky na Githubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Další informace najdete v tématech

- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)
