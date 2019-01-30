---
title: 'Rychlý start: Rozpoznávání řeči, jazyka C++ (Windows) – hlasové služby'
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak rozpoznávat řeč v C++ na ploše Windows s využitím sady Speech SDK.
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 12/13/2018
ms.author: wolfma
ms.openlocfilehash: 17bc5fb4ed059c0922e85e467cbafbd5a90299b0
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55221636"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Rychlý start: Rozpoznávání řeči v C++ ve Windows s použitím sadou SDK pro řeč

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku vytvoříte konzolovou aplikaci pro Windows v jazyce C++. Použijete sadu Cognitive Services [Speech SDK](speech-sdk.md) k přepisu řeči na text v reálném čase z mikrofonu počítače. Tato aplikace je vytvořená pomocí [balíčku NuGet sady Speech SDK](https://aka.ms/csspeech/nuget) a sady Microsoft Visual Studio 2017 (libovolné edice).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu potřebujete klíč předplatného služby Speech. Můžete ho získat zdarma. Podrobnosti najdete v článku [Vyzkoušejte si službu Speech zdarma](get-started.md).

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete zdrojový soubor *helloworld.cpp*. Nahraďte všechen kód pod úvodním příkazem include (`#include "stdafx.h"` nebo `#include "pch.h"`) následujícím kódem:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. Ve stejném souboru nahraďte řetězec `YourSubscriptionKey` klíčem předplatného.

1. Řetězec `YourServiceRegion` nahraďte [oblastí](regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

1. Uložte změny do projektu.

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

1. Sestavte aplikaci. V řádku nabídek zvolte **Sestavení** > **Sestavit řešení**. Kód by se měl zkompilovat bez chyb.

   ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Sestavit řešení](media/sdk/qs-cpp-windows-06-build.png)

1. Spusťte aplikaci. V řádku nabídek zvolte **Ladit** > **Spustit ladění** nebo stiskněte klávesu **F5**.

   ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Spustit ladění](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. Zobrazí se okno konzoly a vyzve vás, abyste něco řekli. Vyslovte anglickou frázi nebo větu. Vaše řeč se přenese do služby Speech a přepíše na text, který se zobrazí ve stejném okně.

   ![Snímek obrazovky s výstupem konzoly po úspěšném rozpoznání](media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>Další postup

Další ukázky, jako je čtení řeči z zvukový soubor, jsou k dispozici na Githubu.

> [!div class="nextstepaction"]
> [Prozkoumejte C++ – ukázky na Githubu](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Další informace najdete v tématech

- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)
