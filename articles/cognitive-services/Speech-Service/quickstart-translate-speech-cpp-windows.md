---
title: 'Rychlý start: Překlad řeči, jazyka C++ (Windows) – hlasové služby'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu vytvoříte jednoduchou aplikaci C++ zaznamenat uživatelské řeči, přeloží ji do jiného jazyka a panelem text do příkazového řádku. Tato příručka je určená pro uživatele Windows.
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: erhopf
ms.openlocfilehash: 8d6f7c36745f8c1a6fdbf8d4a0058e297fef0409
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56110587"
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

    [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/speech-translation/cpp-windows/helloworld/helloworld.cpp#code)]

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
