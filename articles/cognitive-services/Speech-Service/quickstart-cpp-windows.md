---
title: 'Rychlý start: Rozpoznávání řeči, jazyka C++ (Windows) – hlasové služby'
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak rozpoznávat řeč v C++ na ploše Windows s využitím sady Speech SDK.
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: wolfma
ms.openlocfilehash: 454b84f4ba5e492f61daf67c980b204c82a2090d
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53075183"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Rychlý start: Rozpoznávání řeči v C++ ve Windows s využitím sady Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku vytvoříte konzolovou aplikaci pro Windows v jazyce C++. Použijete sadu Cognitive Services [Speech SDK](speech-sdk.md) k přepisu řeči na text v reálném čase z mikrofonu počítače. Tato aplikace je vytvořená pomocí [balíčku NuGet sady Speech SDK](https://aka.ms/csspeech/nuget) a sady Microsoft Visual Studio 2017 (libovolné edice).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu potřebujete klíč předplatného služby Speech. Můžete ho získat zdarma. Podrobnosti najdete v článku [Vyzkoušejte si službu Speech zdarma](get-started.md).

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

1. Spusťte Visual Studio 2017.

1. Ověřte, že je k dispozici sada funkcí **Vývoj desktopových aplikací pomocí C++**. Volbou příkazu **Nástroje** > **Získat nástroje a funkce** na řádku nabídek sady Visual Studio otevřete instalační program sady Visual Studio. Pokud je tato sada funkcí už povolená, přeskočte na další krok.

    ![Snímek obrazovky s kartou Sady funkcí pro Visual Studio](media/sdk/vs-enable-cpp-workload.png)

    V opačném případě zaškrtněte políčko **Vývoj desktopových aplikací pomocí C++**.

1. Ověřte, že je dostupná komponenta **Správce balíčků NuGet**. Přepněte na kartu **Jednotlivé komponenty** dialogového okna instalačního programu sady Visual Studio a vyberte **Správce balíčků NuGet**, pokud ještě není povolený.

      ![Snímek obrazovky s kartou Jednotlivé komponenty sady Visual Studio](media/sdk/vs-enable-nuget-package-manager.png)

1. Pokud potřebujete povolit sadu funkcí C++ nebo NuGet, vyberte **Změnit** (v levém dolním rohu dialogového okna). Instalace nových funkcí chvíli trvá. Pokud už jsou obě funkce povolené, zavřete toto dialogové okno.

1. Vytvořte novou konzolovou aplikaci pro plochu Windows v jazyce Visual C++. Nejdříve v nabídce vyberte **Soubor** > **Nový** > **Projekt**. V dialogovém okně **Nový projekt** rozbalte **Nainstalováno** > **Visual C++** > **Desktop Windows** v levém podokně. Pak vyberte **Konzolová aplikace pro Windows**. Jako název projektu zadejte *helloworld*.

    ![Snímek obrazovky s dialogovým oknem Nový projekt](media/sdk/qs-cpp-windows-01-new-console-app.png)

1. Pokud používáte 64bitovou verzi Windows, můžete pomocí rozevírací nabídky na panelu nástrojů sady Visual Studio přepnout platformu sestavení na `x64`. (64bitové verze Windows dokážou spouštět 32bitové aplikace, takže to není povinné.)

    ![Snímek obrazovky panelu nástrojů sady Visual Studio se zvýrazněnou možností x64](media/sdk/qs-cpp-windows-02-switch-to-x64.png)

1. V Průzkumníku řešení klikněte pravým tlačítkem na řešení a zvolte **Spravovat balíčky NuGet pro řešení**.

    ![Snímek obrazovky s Průzkumníkem řešení a zvýrazněnou možností Spravovat balíčky NuGet pro řešení](media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. V poli **Zdroj balíčku** v pravém horním rohu vyberte **nuget.org**. Vyhledejte balíček `Microsoft.CognitiveServices.Speech` a nainstalujte ho do projektu **helloworld**.

    ![Snímek obrazovky s dialogovým oknem Spravovat balíčky pro řešení](media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

    > [!NOTE]
    > Aktuální verze sady Cognitive Services Speech SDK je `1.1.0`.

1. Přijměte zobrazenou licenci a zahajte instalaci tohoto balíčku NuGet.

    ![Snímek obrazovky s dialogovým oknem Přijetí licence](media/sdk/qs-cpp-windows-05-nuget-license.png)

Po instalaci balíčku se v konzole Správce balíčků zobrazí potvrzení.

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

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Vyhledejte tuto ukázku ve složce `quickstart/cpp-windows`.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Rozpoznávání záměrů z řeči pomocí sady Speech SDK pro C++](how-to-recognize-intents-from-speech-cpp.md)

## <a name="see-also"></a>Další informace najdete v tématech

- [Překlad řeči](how-to-translate-speech-csharp.md)
- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)
