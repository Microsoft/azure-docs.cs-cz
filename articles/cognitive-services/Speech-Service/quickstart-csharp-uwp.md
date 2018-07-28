---
title: 'Rychlý start: Rozpoznávat řeč v jazyce C# v aplikaci UWP pomocí Cognitive Services SDK řeči'
titleSuffix: Microsoft Cognitive Services
description: Zjistěte, jak rozpoznávat řeč v aplikace pro UPW pomocí Cognitive Services SDK řeči
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 549a8fd29cf1f986da0af1ae49dbf0e5335603bd
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325696"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-using-the-speech-sdk"></a>Rychlý start: Rozpoznávat řeč v aplikace pro UPW pomocí sady SDK pro řeč

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

V tomto článku se dozvíte, jak vytvořit aplikaci univerzální platformy Windows (UPW) přepisy převod řeči na text pomocí Cognitive Services SDK řeči.
Aplikace je sestavená s [Microsoft Cognitive Services řeči balíčku sady SDK NuGet](https://aka.ms/csspeech/nuget) a Microsoft Visual Studio 2017.

> [!NOTE]
> Univerzální platforma Windows umožňuje vyvíjet aplikace, které běží na všech zařízeních, která podporuje Windows 10, včetně počítačů, Xbox, Surface Hubu a dalších zařízení. Aplikace s využitím sadou SDK pro řeč ještě nepředávejte Kit certifikaci aplikace Windows (souboru). Je možné nainstalovat ji bokem, které vaše aplikace, ale nemusí být odeslány aktuálně pro Windows Store.

## <a name="prerequisites"></a>Požadavky

* Klíč předplatného pro službu rozpoznávání řeči. Zobrazit [službu řeči si můžete vyzkoušet zdarma](get-started.md).
* Počítače s Windows (Windows 10 Fall Creators Update nebo novější) s pracovní mikrofon.
* [Microsoft Visual Studio 2017](https://www.visualstudio.com/), edice Community nebo vyšší.
* **Vývoj pro univerzální platformu Windows** úlohy v aplikaci Visual Studio.You můžete povolit v **nástroje** \> **stažení nástrojů a funkcí**.

  ![Povolit vývoj pro univerzální platformu Windows](media/sdk/vs-enable-uwp-workload.png)

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

1. V sadě Visual Studio 2017 vytvořte nový Visual C# Windows Universal prázdnou aplikaci. V **nový projekt** dialogové okno, v levém podokně rozbalte **nainstalováno** \> **Visual C#** \> **Windows Universal** a pak vyberte **prázdná aplikace (Universal Windows)**. Název projektu zadejte *helloworld*.

    ![](media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. V **nový projekt univerzální platformy Windows** okno, které bodů POP, zvolte **Windows 10 Fall Creators Update (10.0; Sestavení 16299)** jako **minimální verze**a to nebo novější verze, jako **cílovou verzi**, pak klikněte na tlačítko **OK**.

    ![](media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. Pokud máte spuštěný na instalaci Windows 64-bit, přepnete platforma sestavení k `x64`.

   ![Přepnout na x64 platforma sestavení](media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > V současné době podporuje sadou SDK pro řeč procesory kompatibilní s verzí Intel, ale ne ARM.

1. Instalaci a odkazují [balíček NuGet sady SDK pro řeč](https://aka.ms/csspeech/nuget). V Průzkumníku řešení klikněte pravým tlačítkem myši na řešení a vyberte **spravovat balíčky NuGet pro řešení**.

    ![Klikněte pravým tlačítkem na spravovat balíčky NuGet pro řešení](media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. V pravém horním rohu v **zdroj balíčku** pole, vyberte **Nuget.org**. Vyhledejte a nainstalujte `Microsoft.CognitiveServices.Speech` balíček a nainstalujte ho do **helloworld** projektu.

    ![Nainstalujte balíček NuGet Microsoft.CognitiveServices.Speech](media/sdk/qs-csharp-uwp-05-nuget-install-0.5.0.png "balíček Nuget nainstalovat")

1. Přijměte licenci v zobrazeném dialogu.

    ![Přijměte licenci](media/sdk/qs-csharp-uwp-06-nuget-license.png "přijměte licenci")

1. Následující řádek výstupu se zobrazí v konzole Správce balíčků.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 0.5.0' to helloworld
   ```

## <a name="add-the-sample-code"></a>Přidejte ukázkový kód

1. V Průzkumníku řešení poklikejte na **Package.appxmanifest** upravit manifest aplikace.
   Vyberte **možnosti** kartu, zaškrtněte políčko **mikrofon** funkce, které se a uložte provedené změny.

   ![](media/sdk/qs-csharp-uwp-07-capabilities.png)

1. Upravit uživatelské rozhraní vaší aplikace na něj poklikejte `MainPage.xaml` v Průzkumníku řešení. 

    V zobrazení návrháři XAML, vložte následující fragment kódu XAML do mřížky značky (mezi `<Grid>` a `</Grid>`).

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Úprava kódu XAML dvojitým kliknutím `MainPage.xaml.cs` v Průzkumníku řešení (je seskupený pod `MainPage.xaml` položky).
   Veškerý kód v tomto souboru nahraďte následujícím kódem.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. V `SpeechRecognitionFromMicrophone_ButtonClicked` obslužnou rutinu, nahraďte řetězec `YourSubscriptionKey` s klíči předplatného.

1. V `SpeechRecognitionFromMicrophone_ButtonClicked` obslužnou rutinu, nahraďte řetězec `YourServiceRegion` s [oblasti](regions.md) přidružených k vašemu předplatnému (například `westus` pro bezplatnou zkušební verzi předplatného).

1. Uložte všechny změny do projektu.

## <a name="build-and-run-the-sample"></a>Sestavení a spuštění ukázky

1. Sestavení aplikace. V panelu nabídky vyberte **sestavení** > **sestavit řešení**. Kód by měl nyní kompilovat bez chyb.

    ![Úspěšné sestavení](media/sdk/qs-csharp-uwp-08-build.png "úspěšné sestavení")

1. Spusťte aplikaci. V panelu nabídky vyberte **ladění** > **spustit ladění**, nebo stiskněte klávesu **F5**.

    ![Spusťte aplikaci do ladění](media/sdk/qs-csharp-uwp-09-start-debugging.png "spusťte aplikaci do ladění")

1. Zobrazí okno grafického uživatelského rozhraní. Nejprve klikněte na tlačítko **povolit mikrofon** tlačítko a berou na vědomí žádost o oprávnění, která se otevře.

    ![Spusťte aplikaci do ladění](media/sdk/qs-csharp-uwp-10-access-prompt.png "spusťte aplikaci do ladění")

1. Klikněte na tlačítko **rozpoznávání řeči se vstupem mikrofon** a Mluvte krátkých frází do vašeho zařízení mikrofon. Rozpoznaný text se zobrazí v okně.

    ![](media/sdk/qs-csharp-uwp-11-ui-result.png)

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Hledat v této ukázce `quickstart/csharp-uwp` složky.

## <a name="next-steps"></a>Další postup

- [Překlad řeči](how-to-translate-speech-csharp.md)
- [Přizpůsobení akustických modelů](how-to-customize-acoustic-models.md)
- [Přizpůsobení jazykových modelů](how-to-customize-language-model.md)
