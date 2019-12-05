---
title: 'Rychlý Start: syntetizace řeči C++ , (Windows) – služba Speech'
titleSuffix: Azure Cognitive Services
description: Naučte se, jak na ploše C++ Windows syntetizovat řeč pomocí sady Speech SDK
services: cognitive-services
author: yinhew
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/24/2019
ms.author: yinhew
ms.openlocfilehash: 35c8eb640e3c8c9e33486d5126f83dd40b5b96e0
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818388"
---
## <a name="prerequisites"></a>Předpoklady

Než začnete, nezapomeňte:

> [!div class="checklist"]
> * [Vytvoření prostředku Azure Speech](../../../../get-started.md)
> * [Nastavení vývojového prostředí](../../../../quickstarts/setup-platform.md?tabs=windows)
> * [Vytvořit prázdný ukázkový projekt](../../../../quickstarts/create-project.md?tabs=windows)

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete zdrojový soubor **helloworld.cpp**.

1. Nahraďte celý kód následujícím fragmentem kódu:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/text-to-speech/helloworld/helloworld.cpp#code)]

1. Ve stejném souboru nahraďte řetězec `YourSubscriptionKey` klíčem předplatného.

1. Řetězec `YourServiceRegion` nahraďte [oblastí](~/articles/cognitive-services/Speech-Service/regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

1. V řádku nabídek vyberte možnost **soubor** > **Uložit vše**.

## <a name="build-and-run-the-application"></a>Sestavení a spuštění aplikace

1. V řádku nabídek vyberte **sestavit** > **Sestavit řešení** a sestavte aplikaci. Kód by se teď měl zkompilovat bez chyb.

1. Zvolte **ladění** > **Spustit ladění** (nebo stiskněte klávesu **F5**) a spusťte aplikaci **HelloWorld** .

1. Zadejte anglickou frázi nebo větu. Aplikace přenáší váš text do služby pro rozpoznávání řeči, která odešle do aplikace syntetizované rozpoznávání řeči, aby bylo možné na svém mluvčím hrát.

   ![Výstup na konzole po úspěšném Shrnutí řeči](~/articles/cognitive-services/Speech-Service/media/sdk/qs-tts-cpp-windows-console-output.png)

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Další informace najdete v tématech

- [Vytvoření vlastního hlasu](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Zaznamenat vlastní ukázky hlasu](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
