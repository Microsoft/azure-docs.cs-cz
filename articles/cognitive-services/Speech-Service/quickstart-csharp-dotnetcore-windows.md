---
title: 'Rychlý start: Rozpoznávání řeči, .NET Core (Windows) – hlasové služby'
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak rozpoznávat řeč v jazyce C# v rozhraní .NET Core ve Windows s využitím sady Speech SDK.
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 10/12/2018
ms.author: wolfma
ms.openlocfilehash: e82e39eb3fc6c7ebaf4798ad10038bfd2fa9a41b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53085480"
---
# <a name="quickstart-recognize-speech-in-c-under-net-core-on-windows-by-using-the-speech-sdk"></a>Rychlý start: Rozpoznávání řeči v C# v rozhraní .NET Core ve Windows s využitím sady Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku vytvoříte konzolovou aplikaci v jazyce C# pro .NET Core ve Windows s využitím sady Cognitive Services [Speech SDK](speech-sdk.md). V reálném čase přepíšete řeč z mikrofonu počítače na text. Tato aplikace je vytvořená pomocí [balíčku NuGet sady Speech SDK](https://aka.ms/csspeech/nuget) a sady Microsoft Visual Studio 2017 (libovolné edice).

> [!NOTE]
> .NET Core je Open Source multiplatformní rozhraní .NET, které implementuje specifikace rozhraní [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard).

K dokončení tohoto rychlého startu potřebujete klíč předplatného služby Speech. Můžete ho získat zdarma. Podrobnosti najdete v článku [Vyzkoušejte si službu Speech zdarma](get-started.md).


## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

1. Spusťte Visual Studio 2017.

1. Ověřte, že je k dispozici sada funkcí **Vývoj multiplatformních aplikací pomocí rozhraní .NET Core**. Volbou příkazu **Nástroje** > **Získat nástroje a funkce** na řádku nabídek sady Visual Studio otevřete instalační program sady Visual Studio. Pokud je tato sada funkcí už povolená, zavřete dialogové okno.

    ![Snímek obrazovky instalačního programu sady Visual Studio se zvýrazněnou kartou Sady funkcí](media/sdk/vs-enable-net-core-workload.png)

    V opačném případě zaškrtněte políčko **Vývoj multiplatformních aplikací pomocí rozhraní .NET Core** a vyberte **Změnit** v pravém dolním rohu dialogového okna. Instalace nové funkce bude chvíli trvat.

1. Vytvořte novou konzolovou aplikaci v jazyce Visual C# pro rozhraní .NET Core. V dialogovém okně **Nový projekt** rozbalte **Nainstalováno** > **Visual C#** > **.NET Core** v levém podokně. Pak vyberte **Konzolová aplikace (.NET Core)**. Jako název projektu zadejte *helloworld*.

    ![Snímek obrazovky s dialogovým oknem Nový projekt](media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Vytvoření konzolové aplikace Visual C# (.NET Core)")

1. Nainstalujte [balíček NuGet sady Speech SDK](https://aka.ms/csspeech/nuget) a vytvořte na něho odkaz. V Průzkumníku řešení klikněte pravým tlačítkem na řešení a vyberte **Spravovat balíčky NuGet pro řešení**.

    ![Snímek obrazovky Průzkumníka řešení se zvýrazněnou možností Spravovat balíčky NuGet pro řešení](media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "Správa balíčků NuGet pro řešení")

1. V poli **Zdroj balíčku** v pravém horním rohu vyberte **nuget.org**. Vyhledejte balíček `Microsoft.CognitiveServices.Speech` a nainstalujte ho do projektu **helloworld**.

    ![Snímek obrazovky s dialogovým oknem Spravovat balíčky pro řešení](media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png "Instalace balíčku NuGet")

1. Přijměte zobrazenou licenci a zahajte instalaci tohoto balíčku NuGet.

    ![Snímek obrazovky s dialogovým oknem Souhlas s podmínkami licence](media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "Přijetí licence")

Po instalaci balíčku se v konzole Správce balíčků zobrazí potvrzení.


## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Otevřete soubor `Program.cs` a veškerý kód nahraďte následujícím kódem.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnetcore/helloworld/Program.cs#code)]

1. Ve stejném souboru nahraďte řetězec `YourSubscriptionKey` klíčem předplatného.

1. Také řetězec `YourServiceRegion` nahraďte [oblastí](regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

1. Uložte změny do projektu.

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

1. Sestavte aplikaci. V řádku nabídek zvolte **Sestavení** > **Sestavit řešení**. Kód by se měl zkompilovat bez chyb.

    ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Sestavit řešení](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Úspěšné sestavení")

1. Spusťte aplikaci. V řádku nabídek zvolte **Ladit** > **Spustit ladění** nebo stiskněte klávesu **F5**.

    ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Spustit ladění](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Spuštění aplikace s laděním")

1. Zobrazí se okno konzoly a vyzve vás, abyste něco řekli. Vyslovte anglickou frázi nebo větu. Vaše řeč se přenese do služby Speech a přepíše na text, který se zobrazí ve stejném okně.

    ![Snímek obrazovky s výstupem konzoly po úspěšném rozpoznání](media/sdk/qs-csharp-dotnetcore-windows-07-console-output.png "Výstup konzoly po úspěšném rozpoznání")

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Vyhledejte tuto ukázku ve složce `quickstart/csharp-dotnetcore-windows`.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Rozpoznávání záměrů z řeči pomocí sady Speech SDK pro C#](how-to-recognize-intents-from-speech-csharp.md)

## <a name="see-also"></a>Další informace najdete v tématech

- [Překlad řeči](how-to-translate-speech-csharp.md)
- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)
