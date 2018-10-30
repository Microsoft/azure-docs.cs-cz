---
title: 'Rychlý start: Rozpoznávání řeči v jazyce C# v aplikaci pro UPW s využitím sady Speech SDK'
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak rozpoznávat řeč v aplikaci pro UPW s využitím sady Speech SDK.
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: wolfma
ms.openlocfilehash: 1c6e0521f37e85bcb3395de0ad1e9d463125e17f
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49469794"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Rychlý start: Rozpoznávání řeči v aplikaci pro UPW s využitím sady Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku vytvoříte aplikaci pro Univerzální platformu Windows (UPW) v jazyce C# pomocí sady Cognitive Services [Speech SDK](speech-sdk.md). V reálném čase přepíšete řeč z mikrofonu zařízení na text. Tato aplikace je vytvořená pomocí [balíčku NuGet sady Speech SDK](https://aka.ms/csspeech/nuget) a sady Microsoft Visual Studio 2017 (libovolné edice).

> [!NOTE]
> Univerzální platforma Windows umožňuje vyvíjet aplikace, které běží na všech zařízeních podporujících Windows 10 včetně počítačů, Xboxu, Surface Hubu a dalších.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu potřebujete klíč předplatného služby Speech. Můžete ho získat zdarma. Podrobnosti najdete v článku [Vyzkoušejte si službu Speech zdarma](get-started.md).

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

1. Spusťte Visual Studio 2017.

1. Ověřte, že je k dispozici sada funkcí **Vývoj pro Univerzální platformu Windows**. Volbou příkazu **Nástroje** > **Získat nástroje a funkce** na řádku nabídek sady Visual Studio otevřete instalační program sady Visual Studio. Pokud je tato sada funkcí už povolená, zavřete dialogové okno. 

    ![Snímek obrazovky instalačního programu sady Visual Studio se zvýrazněnou kartou Sady funkcí](media/sdk/vs-enable-uwp-workload.png)

    V opačném případě zaškrtněte políčko **Vývoj multiplatformních aplikací pomocí rozhraní .NET** a vyberte **Změnit** v pravém dolním rohu dialogového okna. Instalace nové funkce chvíli trvá.

1. Vytvořte prázdnou univerzální aplikaci pro Windows v jazyce Visual C#. Nejdříve v nabídce vyberte **Soubor** > **Nový** > **Projekt**. V dialogovém okně **Nový projekt** rozbalte **Nainstalováno** > **Visual C#** > **Univerzální aplikace pro Windows**  v levém podokně. Pak vyberte **Prázdná aplikace (univerzální pro Windows)**. Jako název projektu zadejte *helloworld*.

    ![Snímek obrazovky s dialogovým oknem Nový projekt](media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. Sada Speech SDK vyžaduje, aby aplikace byla vytvořená pro Windows 10 Fall Creators Update nebo novější. V zobrazeném okně **Nový projekt Univerzální platformy Windows** zvolte v poli **Minimální verze** možnost **Windows 10 Fall Creators Update (10.0; build 16299)**. V poli **Cílová verze** vyberte tuto nebo novější verzi a pak klikněte na **OK**.

    ![Snímek obrazovky s oknem Nový projekt univerzální platformy Windows](media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. Pokud používáte 64bitovou verzi Windows, můžete pomocí rozevírací nabídky na panelu nástrojů sady Visual Studio přepnout platformu sestavení na `x64`. (64bitová verze Windows dokáže spouštět 32bitové aplikace, takže pokud chcete, můžete ponechat nastavení `x86`.)

   ![Snímek obrazovky panelu nástrojů sady Visual Studio se zvýrazněnou možností x64](media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > Sada Speech SDK podporuje jen procesory kompatibilní s Intelem. Procesory ARM nejsou momentálně podporované.

1. Nainstalujte [balíček NuGet sady Speech SDK](https://aka.ms/csspeech/nuget) a vytvořte na něho odkaz. V Průzkumníku řešení klikněte pravým tlačítkem na řešení a vyberte **Spravovat balíčky NuGet pro řešení**.

    ![Snímek obrazovky s Průzkumníkem řešení a zvýrazněnou možností Spravovat balíčky NuGet pro řešení](media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. V poli **Zdroj balíčku** v pravém horním rohu vyberte **nuget.org**. Vyhledejte balíček `Microsoft.CognitiveServices.Speech` a nainstalujte ho do projektu **helloworld**.

    ![Snímek obrazovky s dialogovým oknem Spravovat balíčky pro řešení](media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png "Instalace balíčku NuGet")

1. Přijměte zobrazenou licenci a zahajte instalaci tohoto balíčku NuGet.

    ![Snímek obrazovky s dialogovým oknem Souhlas s podmínkami licence](media/sdk/qs-csharp-uwp-06-nuget-license.png "Přijetí licence")

1. V konzole Správce balíčků se zobrazí následující řádek výstupu.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 1.0.1' to helloworld
   ```

1. Protože aplikace používá mikrofon pro hlasový vstup, přidejte do projektu schopnost **Mikrofon**. V Průzkumníku řešení dvakrát klikněte na **Package.appxmanifest** a upravte manifest aplikace. Přepněte na kartu **Schopnosti**, zaškrtněte políčko u schopnosti **Mikrofon** a uložte provedené změny.

   ![Snímek obrazovky s manifestem aplikace sady Visual Studio a zvýrazněnými volbami Schopnosti a Mikrofon](media/sdk/qs-csharp-uwp-07-capabilities.png)


## <a name="add-sample-code"></a>Přidání ukázkového kódu

1. Uživatelské rozhraní aplikace je definované pomocí XAML. Otevřete `MainPage.xaml` v Průzkumníku řešení. V zobrazení XAML návrháře vložte do značky Grid (mezi `<Grid>` a `</Grid>`) následující fragment kódu XAML.

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Otevřete zdrojový soubor s kódem `MainPage.xaml.cs` (je seskupený pod `MainPage.xaml`). Veškerý kód nahraďte následujícím kódem.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. V obslužné rutině `SpeechRecognitionFromMicrophone_ButtonClicked` v tomto souboru nahraďte řetězec `YourSubscriptionKey` klíčem předplatného.

1. V obslužné rutině `SpeechRecognitionFromMicrophone_ButtonClicked` nahraďte řetězec `YourServiceRegion` [oblastí](regions.md) přidruženou k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

1. Uložte všechny změny do projektu.

## <a name="build-and-run-the-app"></a>Sestavení a spuštění aplikace

1. Sestavte aplikaci. V řádku nabídek vyberte **Sestavení** > **Sestavit řešení**. Kód by se teď měl zkompilovat bez chyb.

    ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Sestavit řešení](media/sdk/qs-csharp-uwp-08-build.png "Úspěšné sestavení")

1. Spusťte aplikaci. V řádku nabídek vyberte **Ladit** > **Spustit ladění** nebo stiskněte klávesu **F5**.

    ![Snímek obrazovky s aplikací sady Visual Studio se zvýrazněnou možností Spustit ladění](media/sdk/qs-csharp-uwp-09-start-debugging.png "Spuštění aplikace s laděním")

1. Otevře se okno. Vyberte **Povolit mikrofon** a potvrďte žádost o oprávnění, která se otevře.

    ![Snímek obrazovky s žádostí o oprávnění](media/sdk/qs-csharp-uwp-10-access-prompt.png "Spuštění aplikace s laděním")

1. Vyberte **Rozpoznávání řeči se vstupem z mikrofonu** a vyslovte do mikrofonu zařízení anglickou frázi nebo větu. Vaše řeč se přenese do služby Speech a přepíše na text, který se zobrazí v okně.

    ![Snímek obrazovky uživatelského rozhraní pro rozpoznávání řeči](media/sdk/qs-csharp-uwp-11-ui-result.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Vyhledejte tuto ukázku ve složce `quickstart/csharp-uwp`.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Rozpoznávání záměrů z řeči pomocí sady Speech SDK pro C#](how-to-recognize-intents-from-speech-csharp.md)

## <a name="see-also"></a>Viz také

- [Překlad řeči](how-to-translate-speech-csharp.md)
- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)
