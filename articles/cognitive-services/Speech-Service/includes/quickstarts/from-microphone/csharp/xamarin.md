---
title: 'Rychlý Start: rozpoznávání řeči z mikrofonu C# , (Xamarin) – služba Speech'
titleSuffix: Azure Cognitive Services
description: V tomto článku vytvoříte aplikaci Xamarin pro různé platformy C# pro Univerzální platforma Windows (UWP), Android a iOS pomocí sady Cognitive Services Speech SDK. Přepisovat řeči na text v reálném čase pomocí mikrofonu vašeho zařízení nebo simulátoru. Aplikace je sestavená pomocí balíčku NuGet sady Speech SDK a Microsoft Visual Studio 2019.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 3f315f29eab107c9e0e145bd25db71a8cb8b2ace
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77156730"
---
## <a name="prerequisites"></a>Předpoklady

Než začnete:

> [!div class="checklist"]
> * [Vytvoření prostředku Azure Speech](../../../../get-started.md)
> * [Nastavení vývojového prostředí](../../../../quickstarts/setup-platform.md?tabs=xamarin)
> * [Vytvořit prázdný ukázkový projekt](../../../../quickstarts/create-project.md?tabs=xamarin)
> * Ujistěte se, že máte přístup k mikrofonu pro záznam zvuku

Pokud jste to již provedli, Skvělé. Pojďme pokračovat.

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Přidat vzorový kód pro běžný projekt HelloWorld

Běžný projekt HelloWorld obsahuje implementace nezávislé na platformě pro vaši aplikaci pro různé platformy. Nyní přidejte kód jazyka XAML, který definuje uživatelské rozhraní aplikace, a přidejte C# kód za implementací.

1. V **Průzkumník řešení**v části běžný projekt helloworld otevřete `MainPage.xaml`.

1. V zobrazení jazyka XAML návrháře vložte následující fragment kódu XAML do značky **Grid** mezi `<StackLayout>` a `</StackLayout>`:

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml)]

1. V **Průzkumník řešení**otevřete zdrojový soubor kódu na pozadí `MainPage.xaml.cs`. Je seskupený pod `MainPage.xaml`.

1. Nahraďte veškerý kód v něm následujícím fragmentem kódu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. V obslužné rutině `OnRecognitionButtonClicked` zdrojového souboru Najděte `YourSubscriptionKey`řetězce a nahraďte ho klíčem předplatného.


1. V obslužné rutině `OnRecognitionButtonClicked` Najděte `YourServiceregion`řetězce a nahraďte ho parametrem "Speech SDK" z [oblasti](https://aka.ms/speech/sdkregion) přidružené k vašemu předplatnému. (Například pro předplatné bezplatné zkušební verze použijte `westus`.)

1. Dál je potřeba vytvořit [službu Xamarin](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/), která se používá k dotazování na oprávnění mikrofonu z různých projektů platforem, jako je UWP, Android nebo iOS. Chcete-li to provést, přidejte do projektu HelloWorld novou složku s názvem *Services* a vytvořte pod ní C# nový zdrojový soubor. Můžete kliknout pravým tlačítkem na složku *služby* a vybrat **Přidat** > **novou položku** > souboru s **kódem**. Přejmenujte soubor `IMicrophoneService.cs`a do tohoto souboru umístěte veškerý kód z následujícího fragmentu kódu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

#### <a name="androidtabx-android"></a>[Android](#tab/x-android)
## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Přidat vzorový kód pro `helloworld.Android` projekt

Nyní přidejte C# kód, který definuje část aplikace specifickou pro Android.

1. V **Průzkumník řešení**v rámci HelloWorld. Projekt pro Android otevřete `MainActivity.cs`.

1. Nahraďte veškerý kód v něm následujícím fragmentem kódu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. V dalším kroku přidejte implementaci pro `MicrophoneService` specifickou pro Android vytvořením nové *služby* složky v rámci HelloWorld. Projekt pro Android. Potom vytvořte nový C# zdrojový soubor. Přejmenujte soubor `MicrophoneService.cs`. Zkopírujte a vložte následující fragment kódu do tohoto souboru:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. Potom otevřete `AndroidManifest.xml` ve složce Properties ( *vlastnosti* ). Přidejte následující nastavení použití-oprávnění pro mikrofon mezi `<manifest>` a `</manifest>`:

   ```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
   ```
   
#### <a name="iostabios"></a>[iOS](#tab/ios)
## <a name="add-sample-code-for-the-helloworldios-project"></a>Přidat vzorový kód pro `helloworld.iOS` projekt

Nyní přidejte C# kód, který definuje část aplikace specifickou pro iOS. V projektu HelloWorld. iOS taky vytvořte konfigurace specifické pro zařízení Apple.

1. V **Průzkumník řešení**v projektu HelloWorld. iOS otevřete `AppDelegate.cs`.

1. Nahraďte veškerý kód v něm následujícím fragmentem kódu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. V dalším kroku přidejte implementaci pro `MicrophoneService` specifickou pro iOS vytvořením nové *služby* složky v projektu HelloWorld.IO. Potom vytvořte nový C# zdrojový soubor. Přejmenujte soubor `MicrophoneService.cs`. Zkopírujte a vložte následující fragment kódu do tohoto souboru:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. V textovém editoru otevřete `Info.plist` v rámci projektu HelloWorld. iOS. Do části dict – přidejte následující dvojici klíčových hodnot:

   <key>NSMicrophoneUsageDescription</key>
   <string>Tato ukázková aplikace vyžaduje přístup přes mikrofon</string> .

   > [!NOTE]
   > Pokud vytváříte zařízení pro iPhone, ujistěte se, že `Bundle Identifier` odpovídat ID aplikace profilu zřizování vašeho zařízení. V opačném případě se sestavení nezdaří. Pomocí iPhoneSimulator můžete ponechat tak, jak je.

1. Pokud vytváříte počítač s Windows, navažte připojení k zařízení Mac pro sestavování prostřednictvím **nástrojů** > dvojice > iOS **do počítače Mac**. Postupujte podle pokynů průvodce, který poskytuje Visual Studio, a povolte tak připojení k zařízení Mac.

#### <a name="uwptabhelloworlduwp"></a>[PODPORUJÍ](#tab/helloworlduwp)
## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Přidat vzorový kód pro `helloworld.UWP` projekt

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Přidejte vzorový kód pro HelloWorld. Projekt UWP

Nyní přidejte C# kód, který definuje část aplikace specifickou pro UWP.

1. V **Průzkumník řešení**v rámci HelloWorld. Projekt UWP otevřete `MainPage.xaml.cs`.

1. Nahraďte veškerý kód v něm následujícím fragmentem kódu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Dále přidejte implementaci pro `MicrophoneService` specifickou pro UWP tím, že vytvoříte nové *služby* složky v rámci HelloWorld. Projekt UWP. Potom vytvořte nový C# zdrojový soubor. Přejmenujte soubor `MicrophoneService.cs`. Zkopírujte a vložte následující fragment kódu do tohoto souboru:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Potom poklikejte na soubor `Package.appxmanifest` pod HelloWorld. Projekt UWP v aplikaci Visual Studio. V části **Možnosti**se ujistěte, že je vybraná možnost **mikrofon** , a uložte soubor.

1. Dále dvakrát klikněte na `Package.appxmanifest` soubor pod projektem `helloworld.UWP` v aplikaci Visual Studio a v části **možnosti** > je zaškrtnuto políčko **mikrofon** a soubor uložte.
   > Poznámka: pro případ, že se zobrazí upozornění: soubor certifikátu neexistuje: HelloWorld. UWP_TemporaryKey. pfx, další informace najdete v ukázce [řeči na text](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp) .

1. V řádku nabídek vyberte **soubor** > **Uložit vše** a uložte provedené změny.

## <a name="build-and-run-the-uwp-application"></a>Sestavení a spuštění aplikace UWP

1. Nastavte HelloWorld. UWP jako spouštěný projekt. Klikněte pravým tlačítkem myši na HelloWorld. Pro projekt UWP a vyberte **sestavení** pro sestavení aplikace.

1. Vyberte **ladění** > **Spustit ladění** (nebo vyberte **F5**) a spusťte tak aplikaci. Zobrazí se okno **HelloWorld** .

   ![Ukázková aplikace pro rozpoznávání řeči C# UWP v rychlém startu](../../../../media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Vyberte **Povolit mikrofon**. Po zobrazení žádosti o oprávnění k přístupu vyberte **Ano**.

   ![Žádost o oprávnění k přístupu k mikrofonu](../../../../media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Vyberte možnost **spustit rozpoznávání řeči**a mluvte do mikrofonu zařízení anglickou frázi nebo větu. Vaše řeč se přenese do služby Speech a přepíše na text, který se zobrazí v okně.

   ![Uživatelské rozhraní rozpoznávání řeči](../../../../media/sdk/qs-csharp-xamarin-uwp-ui-result.png)
* * *

## <a name="build-and-run-the-android-and-ios-applications"></a>Sestavování a spouštění aplikací pro Android a iOS

Sestavování a spouštění aplikací pro Android a iOS v zařízení nebo simulátoru dochází podobně jako u UWP. Ujistěte se, že všechny sady SDK jsou správně nainstalované podle požadavků v tomto článku v části požadavky.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [footer](./footer.md)]
