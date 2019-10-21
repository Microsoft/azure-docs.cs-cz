---
title: 'Rychlý Start: rozpoznávání řeči C# , (Xamarin) – služba Speech'
titleSuffix: Azure Cognitive Services
description: V tomto článku vytvoříte aplikaci Xamarin pro různé platformy C# pro Univerzální platforma Windows (UWP), Android a iOS pomocí sady Cognitive Services Speech SDK. Přepisovat řeči na text v reálném čase pomocí mikrofonu vašeho zařízení nebo simulátoru. Aplikace je sestavená pomocí balíčku NuGet sady Speech SDK a Microsoft Visual Studio 2019.
services: cognitive-services
author: jhakulin
manager: robch
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/16/2019
ms.author: jhakulin
ms.custom: ''
ms.openlocfilehash: ad1c6f7d17d1e04fcfa5b187a29dadd2fa61edd8
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2019
ms.locfileid: "72675598"
---
# <a name="quickstart-recognize-speech-by-using-a-cross-platform-xamarin-app-that-uses-the-speech-sdk"></a>Rychlý Start: rozpoznávání řeči pomocí aplikace Xamarin pro různé platformy, která používá sadu Speech SDK

K dispozici jsou také rychlé starty pro [rozpoznávání textu](quickstart-csharp-uwp.md), převodu [textu na řeč](quickstart-text-to-speech-csharp-uwp.md)a [Převod řeči](quickstart-translate-speech-uwp.md).

V tomto článku vyvíjíte aplikace pro různé platformy C# pomocí Xamarin pro Univerzální platforma Windows (UWP), Android a iOS, které používají sadu Azure Cognitive Services [Speech SDK](speech-sdk.md). Program transcribes hlas do textu v reálném čase z mikrofonu vašeho zařízení. Aplikace je sestavená pomocí [balíčku NuGet sady Speech SDK](https://aka.ms/csspeech/nuget) a libovolné edice Microsoft Visual Studio 2019.

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
* Cílení na iOS: 
    * Zařízení s iOS (ARM64) nebo simulátor iOS (x64) je [povolené pro vývoj](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/) pomocí pracovního mikrofonu.
* Pro podporu sestavení Windows ARM64 nainstalujte [volitelné nástroje sestavení a sadu Windows 10 SDK pro ARM/ARM64](https://blogs.windows.com/buildingapps/2018/11/15/official-support-for-windows-10-on-arm-development/).

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

[!INCLUDE [](../../../includes/cognitive-services-speech-service-quickstart-xamarin-create-proj.md)]

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Přidat vzorový kód pro běžný projekt HelloWorld

Běžný projekt HelloWorld obsahuje implementace nezávislé na platformě pro vaši aplikaci pro různé platformy. Nyní přidejte kód jazyka XAML, který definuje uživatelské rozhraní aplikace, a přidejte C# kód za implementací.

1. V **Průzkumník řešení**v části běžný projekt helloworld otevřete `MainPage.xaml`.

1. V zobrazení jazyka XAML návrháře vložte následující fragment kódu XAML do značky **Grid** mezi `<StackLayout>` a `</StackLayout>`:

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml)]

1. V **Průzkumník řešení**otevřete zdrojový soubor kódu na pozadí `MainPage.xaml.cs`. Je seskupený pod `MainPage.xaml`.

1. Nahraďte veškerý kód v něm následujícím fragmentem kódu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. V obslužné rutině `OnRecognitionButtonClicked` zdrojového souboru Najděte řetězec `YourSubscriptionKey` a nahraďte ho klíčem předplatného.

1. V obslužné rutině `OnRecognitionButtonClicked` Najděte řetězec `YourServiceRegion` a nahraďte ho [oblastí](regions.md) přidruženou k vašemu předplatnému. Například pro předplatné bezplatné zkušební verze použijte `westus`.

1. Dál je potřeba vytvořit [službu Xamarin](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/), která se používá k dotazování na oprávnění mikrofonu z různých projektů platforem, jako je UWP, Android nebo iOS. Chcete-li to provést, přidejte do projektu HelloWorld novou složku s názvem *Services* a vytvořte pod ní C# nový zdrojový soubor. Můžete kliknout pravým tlačítkem na složku *služby* a vybrat **Přidat**  > **novou položku**  >  souboru s**kódem**. Přejmenujte soubor `IMicrophoneService.cs` a do tohoto souboru umístěte veškerý kód z následujícího fragmentu kódu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Přidejte vzorový kód pro HelloWorld. Projekt pro Android

Nyní přidejte C# kód, který definuje část aplikace specifickou pro Android.

1. V **Průzkumník řešení**v rámci HelloWorld. Projekt pro Android otevřete `MainActivity.cs`.

1. Nahraďte veškerý kód v něm následujícím fragmentem kódu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. V dalším kroku přidejte implementaci pro `MicrophoneService` specifickou pro Android vytvořením nové *služby* složky v rámci HelloWorld. Projekt pro Android. Potom vytvořte nový C# zdrojový soubor. Přejmenujte soubor `MicrophoneService.cs`. Zkopírujte a vložte následující fragment kódu do tohoto souboru:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. Potom otevřete `AndroidManifest.xml` ve složce Properties ( *vlastnosti* ). Přidejte následující nastavení použití-oprávnění pro mikrofon mezi `<manifest>` a `</manifest>`:

   ```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
   ```

## <a name="add-sample-code-for-the-helloworldios-project"></a>Přidat vzorový kód pro projekt HelloWorld. iOS

Nyní přidejte C# kód, který definuje část aplikace specifickou pro iOS. V projektu HelloWorld. iOS taky vytvořte konfigurace specifické pro zařízení Apple.

1. V **Průzkumník řešení**v projektu HelloWorld. iOS otevřete `AppDelegate.cs`.

1. Nahraďte veškerý kód v něm následujícím fragmentem kódu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. V dalším kroku přidejte implementaci pro `MicrophoneService` specifickou pro iOS vytvořením nové *služby* složky v projektu HelloWorld.IO. Potom vytvořte nový C# zdrojový soubor. Přejmenujte soubor `MicrophoneService.cs`. Zkopírujte a vložte následující fragment kódu do tohoto souboru:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. V textovém editoru otevřete `Info.plist` v rámci projektu HelloWorld. iOS. Do části dict – přidejte následující dvojici klíčových hodnot:

   <key>NSMicrophoneUsageDescription</key> 
   <string>Tato ukázková aplikace vyžaduje přístup přes mikrofon</string> .

   > [!NOTE] 
   > Pokud vytváříte zařízení pro iPhone, ujistěte se, že `Bundle Identifier` odpovídat ID aplikace profilu zřizování vašeho zařízení. V opačném případě se sestavení nezdaří. Pomocí iPhoneSimulator můžete ponechat tak, jak je.

1. Pokud vytváříte počítač s Windows, navažte připojení k zařízení Mac pro sestavování prostřednictvím **nástrojů**  >  dvojice  >  iOS**do počítače Mac**. Postupujte podle pokynů průvodce, který poskytuje Visual Studio, a povolte tak připojení k zařízení Mac.

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Přidejte vzorový kód pro HelloWorld. Projekt UWP

Nyní přidejte C# kód, který definuje část aplikace specifickou pro UWP.

1. V **Průzkumník řešení**v rámci HelloWorld. Projekt UWP otevřete `MainPage.xaml.cs`.

1. Nahraďte veškerý kód v něm následujícím fragmentem kódu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Dále přidejte implementaci pro `MicrophoneService` specifickou pro UWP tím, že vytvoříte nové *služby* složky v rámci HelloWorld. Projekt UWP. Potom vytvořte nový C# zdrojový soubor. Přejmenujte soubor `MicrophoneService.cs`. Zkopírujte a vložte následující fragment kódu do tohoto souboru:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Potom poklikejte na soubor `Package.appxmanifest` pod HelloWorld. Projekt UWP v aplikaci Visual Studio. V části **Možnosti**se ujistěte, že je vybraná možnost **mikrofon** , a uložte soubor.

   > [!NOTE] 
   > Pokud se zobrazí zpráva s upozorněním, že soubor certifikátu neexistuje: Hello. UWP_TemporaryKey. pfx, kde najdete další informace v ukázce [Převod řeči na text](quickstart-csharp-uwp.md) .

1. V řádku nabídek vyberte **soubor**  > **Uložit vše** a uložte provedené změny.

## <a name="build-and-run-the-uwp-application"></a>Sestavení a spuštění aplikace UWP

1. Nastavte HelloWorld. UWP jako spouštěný projekt. Klikněte pravým tlačítkem myši na HelloWorld. Pro projekt UWP a vyberte **sestavení** pro sestavení aplikace. 

1. Vyberte **ladění**  > **Spustit ladění** (nebo vyberte **F5**) a spusťte tak aplikaci. Zobrazí se okno **HelloWorld** .

   ![Ukázková aplikace pro rozpoznávání řeči C# UWP v rychlém startu](media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Vyberte **Povolit mikrofon**. Po zobrazení žádosti o oprávnění k přístupu vyberte **Ano**.

   ![Žádost o oprávnění k přístupu k mikrofonu](media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Vyberte možnost **spustit rozpoznávání řeči**a mluvte do mikrofonu zařízení anglickou frázi nebo větu. Váš hlas se přenáší do služeb Speech Services a přepisu text, který se zobrazí v okně.

   ![Uživatelské rozhraní rozpoznávání řeči](media/sdk/qs-csharp-xamarin-uwp-ui-result.png)

## <a name="build-and-run-the-android-and-ios-applications"></a>Sestavování a spouštění aplikací pro Android a iOS

Sestavování a spouštění aplikací pro Android a iOS v zařízení nebo simulátoru dochází podobně jako u UWP. Ujistěte se, že všechny sady SDK jsou správně nainstalované podle požadavků v tomto článku v části požadavky.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Prozkoumejte C# ukázky na GitHubu](https://aka.ms/csspeech/samples)
