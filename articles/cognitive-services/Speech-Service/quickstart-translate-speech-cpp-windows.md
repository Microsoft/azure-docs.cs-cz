---
title: 'Rychlý start: Překlad řeči, jazyka C++ (Windows) – hlasové služby'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu vytvoříte jednoduchou aplikaci C++ zaznamenat uživatelské řeči, přeloží ji do jiného jazyka a panelem text do příkazového řádku. Tato příručka je určená pro uživatele Windows.
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: erhopf
ms.openlocfilehash: 1531ad879b3345cfa089792220e7a2044570a6f8
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53730868"
---
# <a name="quickstart-translate-speech-with-the-speech-sdk-for-c"></a>Rychlý start: Překlad řeči se sadou SDK pro řeč pro C++

V tomto rychlém startu vytvoříte jednoduchou aplikaci C++, který zachycuje uživatele řeči z vašeho počítače mikrofonu, přeloží řeč a transcribes přeložený text do příkazového řádku v reálném čase. Tato aplikace je navržen pro spouštění na Windows 64-bit a využívá rozhraní [balíček NuGet sady SDK pro řeč](https://aka.ms/csspeech/nuget) a Microsoft Visual Studio 2017.

Úplný seznam jazyků, které jsou k dispozici pro překlad řeči, naleznete v tématu [jazykovou podporu](language-support.md).

## <a name="prerequisites"></a>Požadavky

K tomuto rychlému startu potřebujete:

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
* Klíč předplatného pro službu rozpoznávání řeči. [Získat zdarma](get-started.md).

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete zdrojový soubor *helloworld.cpp*. Nahraďte všechen kód pod úvodním příkazem include (`#include "stdafx.h"` nebo `#include "pch.h"`) následujícím kódem:

    ```cpp
    #include "pch.h"
    #include <iostream>
    #include <vector>
    #include <speechapi_cxx.h>

    using namespace std;
    using namespace Microsoft::CognitiveServices::Speech;
    using namespace Microsoft::CognitiveServices::Speech::Translation;

    // Translation with microphone input.
    void TranslationWithMicrophone()
    {
        // Creates an instance of a speech translation config with specified subscription key and service region.
        // Replace with your own subscription key and service region (e.g., "westus").
        auto config = SpeechTranslationConfig::FromSubscription("YourSubscriptionKey", "YourServiceRegion");

        // Sets source and target languages
        // Replace with the languages of your choice.
        auto fromLanguage = "en-US";
        config->SetSpeechRecognitionLanguage(fromLanguage);
        config->AddTargetLanguage("de");
        config->AddTargetLanguage("fr");

        // Creates a translation recognizer using microphone as audio input.
        auto recognizer = TranslationRecognizer::FromConfig(config);
        cout << "Say something...\n";

        // Starts translation. RecognizeOnceAsync() returns when the first utterance has been recognized,
        // so it is suitable only for single shot recognition like command or query. For long-running
        // recognition, use StartContinuousRecognitionAsync() instead.
        auto result = recognizer->RecognizeOnceAsync().get();

        // Checks result.
        if (result->Reason == ResultReason::TranslatedSpeech)
        {
            cout << "RECOGNIZED: Text=" << result->Text << std::endl
                << "  Language=" << fromLanguage << std::endl;

            for (const auto& it : result->Translations)
            {
                cout << "TRANSLATED into '" << it.first.c_str() << "': " << it.second.c_str() << std::endl;
            }
        }
        else if (result->Reason == ResultReason::RecognizedSpeech)
        {
            cout << "RECOGNIZED: Text=" << result->Text << " (text could not be translated)" << std::endl;
        }
        else if (result->Reason == ResultReason::NoMatch)
        {
            cout << "NOMATCH: Speech could not be recognized." << std::endl;
        }
        else if (result->Reason == ResultReason::Canceled)
        {
            auto cancellation = CancellationDetails::FromResult(result);
            cout << "CANCELED: Reason=" << (int)cancellation->Reason << std::endl;

            if (cancellation->Reason == CancellationReason::Error)
            {
                cout << "CANCELED: ErrorCode=" << (int)cancellation->ErrorCode << std::endl;
                cout << "CANCELED: ErrorDetails=" << cancellation->ErrorDetails << std::endl;
                cout << "CANCELED: Did you update the subscription info?" << std::endl;
            }
        }
    }

    int wmain()
    {
        TranslationWithMicrophone();
        cout << "Please press a key to continue.\n";
        cin.get();
        return 0;
    }
    ```

1. Ve stejném souboru nahraďte řetězec `YourSubscriptionKey` klíčem předplatného.

1. Řetězec `YourServiceRegion` nahraďte [oblastí](regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

1. Uložte změny do projektu.

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

1. Sestavte aplikaci. V řádku nabídek zvolte **Sestavení** > **Sestavit řešení**. Kód by se měl zkompilovat bez chyb.

   ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Sestavit řešení](media/sdk/qs-cpp-windows-06-build.png)

1. Spusťte aplikaci. V řádku nabídek zvolte **Ladit** > **Spustit ladění** nebo stiskněte klávesu **F5**.

   ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Spustit ladění](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. Zobrazí se okno konzoly a vyzve vás, abyste něco řekli. Vyslovte anglickou frázi nebo větu. Hlasu je do služby řeči, přeložit a převede na text, který se zobrazí ve stejném okně.

   ![Snímek obrazovky výstup na konzole po úspěšné překladu](media/sdk/qs-translate-cpp-windows-output.png)

## <a name="next-steps"></a>Další postup

Další ukázky, jako je čtení řeči ze zvukových souboru a výstup přeložený text jako řečového, jsou k dispozici na Githubu.

> [!div class="nextstepaction"]
> [Prozkoumejte C++ – ukázky na Githubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Další informace najdete v tématech

- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)
