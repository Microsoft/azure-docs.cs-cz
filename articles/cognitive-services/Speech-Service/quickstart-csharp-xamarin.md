---
title: 'Rychlý Start: rozpoznávání řeči C# , (Xamarin) – služba Speech'
titleSuffix: Azure Cognitive Services
description: V tomto článku vytvoříte aplikaci Xamarin pro různé platformy C# pro Windows UWP, Android a iOS pomocí sady Cognitive Services Speech SDK. Přepisovat řeči na text v reálném čase pomocí mikrofonu vašeho zařízení nebo simulátoru. Aplikace je sestavená pomocí balíčku NuGet sady Speech SDK a Microsoft Visual Studio 2019.
services: cognitive-services
author: jhakulin
manager: robch
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: jhakulin
ms.custom: ''
ms.openlocfilehash: c9467bac8b5998252c021faca4eb4177c42a1736
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387393"
---
# <a name="quickstart-recognize-speech-using-cross-platform-xamarin-app-by-using-the-speech-sdk"></a>Rychlý Start: rozpoznávání řeči pomocí aplikace Xamarin pro různé platformy pomocí sady Speech SDK

K dispozici jsou také rychlé starty pro [rozpoznávání řeči textu](quickstart-csharp-uwp.md), převod [textu na řeč](quickstart-text-to-speech-csharp-uwp.md) a [Překlad řeči](quickstart-translate-speech-uwp.md).

V tomto článku budete vyvíjet aplikaci pro víc platforem C# pomocí sady Xamarin for Univerzální platforma Windows (UWP), Androidu a iOS pomocí [sady Speech SDK](speech-sdk.md). Program transcribes hlas do textu v reálném čase z mikrofonu vašeho zařízení. Aplikace je sestavená pomocí [balíčku NuGet sady Speech SDK](https://aka.ms/csspeech/nuget) a Microsoft Visual Studio 2019 (libovolná edice).

## <a name="prerequisites"></a>Předpoklady

K tomuto rychlému startu potřebujete:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/).
* Klíč předplatného Azure pro službu Speech Service. [Získejte je zdarma](get-started.md).
* POČÍTAČ s Windows s Windows 10 je aktualizací Creators Update (10,0; Sestavte 16299) nebo novější a pomocí pracovního mikrofonu.
* [Instalace Xamarin do sady Visual Studio](https://docs.microsoft.com/xamarin/get-started/installation/?pivots=windows)
* [Instalace Xamarin Androidu ve Windows](https://docs.microsoft.com/xamarin/android/get-started/installation/windows)
* [Instalace Xamarin iOS ve Windows](https://docs.microsoft.com/xamarin/ios/get-started/installation/windows/?pivots=windows)
* Cílová platforma pro Android: 
   * Zařízení s Androidem (ARM32/64, x86; Rozhraní API 23: Android 6,0 Marshmallow nebo vyšší) je [povolené pro vývoj](https://developer.android.com/studio/debug/dev-options) pomocí pracovního mikrofonu.
* Cílení na iOS: zařízení s iOS (ARM64) nebo simulátor iOS (x64) je [povolené pro vývoj](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/) pomocí pracovního mikrofonu.
* Pro podporu sestavení Windows ARM64 nainstalujte [volitelné nástroje sestavení a sadu Windows 10 SDK pro ARM/ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-xamarin-create-proj.md)]

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Přidat vzorový kód pro běžný projekt `helloworld`

Běžný projekt `helloworld` obsahuje implementace nezávislé na platformě pro vaši aplikaci pro různé platformy.
Nyní přidejte kód jazyka XAML, který definuje uživatelské rozhraní aplikace, a přidejte C# kód za implementaci.

1. V **Průzkumník řešení**v části běžný projekt `helloworld` Otevřete `MainPage.xaml`.

1. V zobrazení jazyka XAML návrháře vložte následující fragment kódu XAML do značky **Grid** (mezi `<StackLayout>` a `</StackLayout>`):

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml)]

1. V **Průzkumník řešení**otevřete zdrojový soubor kódu na pozadí `MainPage.xaml.cs`. (Je seskupena pod `MainPage.xaml`.)

1. Nahraďte veškerý kód v něm následujícím fragmentem kódu:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. V obslužné rutině `OnRecognitionButtonClicked` zdrojového souboru Najděte řetězec `YourSubscriptionKey` a nahraďte ho klíčem předplatného.

1. V obslužné rutině `OnRecognitionButtonClicked` Najděte řetězec `YourServiceRegion` a nahraďte ho [oblastí](regions.md) přidruženou k vašemu předplatnému. (Například pro bezplatné zkušební předplatné použijte `westus`.)

1. Dále potřebujete vytvořit [službu Xamarin](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/), která se používá k dotazování na oprávnění mikrofonu z různých projektů platforem (UWP, Android a iOS). Pokud chcete přidat novou složku `Services` pod projekt `helloworld` a vytvořit v něm nový C# zdrojový soubor (klikněte pravým tlačítkem na složku `Services` a **přidejte** **soubor s kódem** > **nové položky** > ) a přejmenujte ho na `IMicrophoneService.cs` a vložte veškerý kód z Následující fragment kódu v tomto souboru:

[!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Přidat vzorový kód pro projekt `helloworld.Android`

Nyní přidejte C# kód, který definuje část aplikace specifickou pro Android.

1. V **Průzkumník řešení**otevřete v projektu `helloworld.Android` `MainActivity.cs`.

1. Nahraďte veškerý kód v něm následujícím fragmentem kódu:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. Dále přidejte implementaci specifickou pro Android pro `MicrophoneService` vytvořením nové složky `Services` v projektu `helloworld.Android`. Poté vytvořte nový C# zdrojový soubor a přejmenujte jej na `MicrophoneService.cs` a zkopírujte do tohoto souboru následující fragment kódu.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. Po otevření `AndroidManifest.xml` pod složkou `Properties` a přidejte následující nastavení oprávnění použití pro mikrofon mezi `<manifest>` a `</manifest>`.
```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
```

## <a name="add-sample-code-for-the-helloworldios-project"></a>Přidat vzorový kód pro projekt `helloworld.iOS`

Nyní přidejte C# kód, který definuje část aplikace specifickou pro iOS, a také v projektu HelloWorld. iOS vytvořte konfigurace specifické pro zařízení Apple.

1. V **Průzkumník řešení**otevřete v projektu `helloworld.iOS` `AppDelegate.cs`.

1. Nahraďte veškerý kód v něm následujícím fragmentem kódu:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. Dále přidejte implementaci specifickou pro iOS pro `MicrophoneService` vytvořením nové složky `Services` v projektu `helloworld.iOS`. Poté vytvořte nový C# zdrojový soubor a přejmenujte jej na `MicrophoneService.cs` a zkopírujte do tohoto souboru následující fragment kódu.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. Otevřete info. plist v rámci projektu `helloworld.iOS` do textového editoru a v části Dict – <key>NSMicrophoneUsageDescription</key>přidejte následující pár klíč-hodnota 
   .<string>Tato ukázková aplikace vyžaduje přístup přes mikrofon</string> .
   > Poznámka: v případě, že cílíte na sestavení pro zařízení iPhone, zajistěte, aby `Bundle Identifier` odpovídaly ID aplikace profilu zřizování vašeho zařízení, jinak se sestavení nezdaří. Pomocí iPhoneSimulator můžete ponechat tak, jak je.

1. V případě, že vytváříte počítač s Windows, musíte navázat připojení k zařízení Mac pro vytváření prostřednictvím **nástrojů** > **iOS** > **páru do Mac**. Postupujte podle pokynů průvodce, který poskytuje Visual Studio, a povolte tak připojení k zařízení Mac.

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Přidat vzorový kód pro projekt `helloworld.UWP`

Nyní přidejte C# kód, který definuje část aplikace specifickou pro UWP.

1. V **Průzkumník řešení**otevřete v projektu `helloworld.UWP` `MainPage.xaml.cs`.

1. Nahraďte veškerý kód v něm následujícím fragmentem kódu:

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Dále přidejte implementaci specifickou pro UWP pro `MicrophoneService` vytvořením nové složky `Services` v projektu `helloworld.UWP`. Poté vytvořte nový C# zdrojový soubor a přejmenujte jej na `MicrophoneService.cs` a zkopírujte do tohoto souboru následující fragment kódu.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Dále dvakrát klikněte na `Package.appxmanifest` v projektu `helloworld.UWP` v rámci sady Visual Studio a v části **možnosti**@no__t-**3 je zaškrtnuto** a uložte soubor.
   > Poznámka: pro případ, že se zobrazí upozornění: soubor certifikátu neexistuje: HelloWorld. UWP_TemporaryKey. pfx. Další informace najdete v ukázce [řeči na text](quickstart-csharp-uwp.md) .

1. V řádku nabídek vyberte **soubor** > **Uložit vše** a uložte provedené změny.

## <a name="build-and-run-the-uwp-application"></a>Sestavení a spuštění aplikace UWP

1. Nastavte `helloworld.UWP` jako spouštěcí projekt a klikněte pravým tlačítkem myši na projekt `helloworld.UWP` a výběrem možnosti **sestavit** sestavte aplikaci. 

1. Zvolte možnost **ladění** > **Spustit ladění** (nebo stiskněte klávesu **F5**) a spusťte aplikaci. Zobrazí se okno **HelloWorld** .

   ![Ukázková aplikace pro rozpoznávání řeči C# UWP v rychlém startu](media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Vyberte možnost **Povolit mikrofon**a když se zobrazí žádost o přístupové oprávnění, vyberte **Ano**.

   ![Žádost o oprávnění k přístupu k mikrofonu](media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Vyberte možnost **spustit rozpoznávání řeči**a mluvte do mikrofonu zařízení anglickou frázi nebo větu. Váš hlas se přenáší do služby Speech Services a přepisu na text, který se zobrazí v okně.

   ![Uživatelské rozhraní rozpoznávání řeči](media/sdk/qs-csharp-xamarin-uwp-ui-result.png)

## <a name="build-and-run-the-android-and-ios-applications"></a>Sestavování a spouštění aplikací pro Android a iOS

Sestavování a spouštění aplikací pro Android a iOS v zařízení nebo simulátoru dochází podobně jako u UWP. Důležité je zajistit, aby všechny sady SDK byly správně nainstalovány v tomto dokumentu v části `Prerequisites`.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte C# ukázky na GitHubu](https://aka.ms/csspeech/samples)
