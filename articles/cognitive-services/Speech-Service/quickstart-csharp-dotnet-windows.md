---
title: 'Rychlý start: Rozpoznávání a přepis řeči, .NET Framework (Windows) – Služba Speech'
titleSuffix: Azure Cognitive Services
description: Pomocí této příručky vytvoříte konzolovou aplikaci pro převod řeči na text s využitím rozhraní .NET Framework pro Windows a sady Speech SDK. Po dokončení můžete použít mikrofon vašeho počítače a v reálném čase přepisovat řeč na text.
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 11/05/2018
ms.author: wolfma
ms.openlocfilehash: 2dd001ebebd5cdf90d7d0b8163a85a5f83c855f6
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51281290"
---
# <a name="quickstart-recognize-and-transcribe-speech-using-the-speech-sdk-and-net-framework-windows"></a>Rychlý start: Rozpoznávání a přepis řeči s využitím sady Speech SDK a rozhraní .NET Framework (Windows)

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Pomocí této příručky vytvoříte konzolovou aplikaci pro převod řeči na text s využitím rozhraní .NET Framework pro Windows a sady Speech SDK. Po dokončení můžete použít mikrofon vašeho počítače a v reálném čase přepisovat řeč na text.

K tomuto rychlému startu potřebujete [účet služby Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s povolenou službou Microsoft Speech. Pokud účet nemáte, můžete k získání klíče předplatného použít [bezplatnou zkušební verzi](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto projektu budete potřebovat:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Klíč předplatného pro službu Speech
* Přístup k mikrofonu vašeho počítače

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

[!INCLUDE [Create project ](../../../includes/cognitive-services-speech-service-create-speech-project-vs-csharp.md)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete soubor `Program.cs` a nahraďte automaticky vygenerovaný kód tímto vzorovým kódem:

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Vyhledejte řetězec `YourSubscriptionKey` a nahraďte ho vaším klíčem předplatného služby Speech.

1. Vyhledejte řetězec `YourServiceRegion` a nahraďte ho [oblastí](regions.md) přidruženou k vašemu předplatnému. Pokud například používáte bezplatnou zkušební verzi, oblast je `westus`.

1. Uložte změny do projektu.

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

1. V řádku nabídek vyberte **Sestavení** > **Sestavit řešení**. Kód by se teď měl zkompilovat bez chyb.

    ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Sestavit řešení](media/sdk/qs-csharp-dotnet-windows-08-build.png "Úspěšné sestavení")

1. Aplikaci spustíte tak, že na řádku nabídek vyberete **Ladit** > **Spustit ladění** nebo stisknete klávesu **F5**.

    ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Spustit ladění](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Spuštění aplikace s laděním")

1. Zobrazí se okno konzoly s výzvou k mluvení. Teď řekněte něco v angličtině. Vaše řeč se v reálném čase přenese do služby Speech a přepíše na text. Výsledek se zobrazí v konzole.

    ![Snímek obrazovky s výstupem konzoly po úspěšném rozpoznání](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "Výstup konzoly po úspěšném rozpoznání")

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Kód je k dispozici ve složce `quickstart/csharp-dotnet-windows`.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Rozpoznávání záměrů z řeči pomocí sady Speech SDK pro C#](how-to-recognize-intents-from-speech-csharp.md)

## <a name="see-also"></a>Viz také

- [Překlad řeči](how-to-translate-speech-csharp.md)
- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)
