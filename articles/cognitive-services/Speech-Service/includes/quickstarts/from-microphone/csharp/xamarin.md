---
title: 'Rychlý Start: rozpoznávání řeči z mikrofonu, C# (Xamarin) – Speech Service'
titleSuffix: Azure Cognitive Services
description: V tomto článku vytvoříte aplikaci Xamarin v C# pro různé platformy pro Univerzální platforma Windows (UWP), Android a iOS pomocí sady Cognitive Services Speech SDK. Přepisovat řeči na text v reálném čase pomocí mikrofonu vašeho zařízení nebo simulátoru. Aplikace je sestavená pomocí balíčku NuGet sady Speech SDK a Microsoft Visual Studio 2019.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/02/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: f89bdbfd144fb327c1daae020a1e371c00045859
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376386"
---
## <a name="prerequisites"></a>Požadavky

Než začnete:

> [!div class="checklist"]
> * [Vytvoření prostředku Azure Speech](../../../../overview.md#try-the-speech-service-for-free)
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?tabs=xamarin&pivots=programming-language-csharp)
> * Ujistěte se, že máte přístup k mikrofonu pro záznam zvuku

Pokud jste to již provedli, Skvělé. Pojďme pokračovat.

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Přidat vzorový kód pro běžný projekt HelloWorld

Běžný projekt HelloWorld obsahuje implementace nezávislé na platformě pro vaši aplikaci pro různé platformy. Nyní přidejte kód jazyka XAML, který definuje uživatelské rozhraní aplikace, a přidejte kód jazyka C# za implementaci.

1. V **Průzkumník řešení**v části běžný projekt HelloWorld otevřete `MainPage.xaml` .

1. V zobrazení jazyka XAML návrháře vložte následující fragment kódu XAML do značky **Grid** mezi `<StackLayout>` a `</StackLayout>` :

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml)]

1. V **Průzkumník řešení**otevřete zdrojový soubor kódu na pozadí `MainPage.xaml.cs` . Je seskupený pod `MainPage.xaml` .

1. Nahraďte veškerý kód v něm následujícím fragmentem kódu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. V `OnRecognitionButtonClicked` obslužné rutině zdrojového souboru vyhledejte řetězec `YourSubscriptionKey` a nahraďte ho klíčem předplatného.


1. V `OnRecognitionButtonClicked` obslužné rutině Najděte řetězec `YourServiceregion` a nahraďte ho **identifikátorem oblasti** z [oblasti](https://aka.ms/speech/sdkregion) , která je přidružená k vašemu předplatnému. 

1. Dál je potřeba vytvořit [službu Xamarin](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/), která se používá k dotazování na oprávnění mikrofonu z různých projektů platforem, jako je UWP, Android nebo iOS. Chcete-li to provést, přidejte do projektu HelloWorld novou složku s názvem *Services* a vytvořte pod ní nový zdrojový soubor C#. Můžete kliknout pravým tlačítkem na složku *služby* a vybrat **Přidat**  >  **Nový**  >  **soubor kódu**položky. Přejmenujte soubor a do tohoto `IMicrophoneService.cs` souboru umístěte veškerý kód z následujícího fragmentu kódu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

#### <a name="android"></a>[Android](#tab/x-android)
## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Přidat vzorový kód pro `helloworld.Android` projekt

Nyní přidejte kód jazyka C#, který definuje část aplikace specifickou pro Android.

1. V **Průzkumník řešení**v rámci HelloWorld. Otevřete projekt pro Android `MainActivity.cs` .

1. Nahraďte veškerý kód v něm následujícím fragmentem kódu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. V dalším kroku přidejte implementaci služby, která je specifická pro Android, tak, že `MicrophoneService` vytvoříte nové *služby* složky v rámci HelloWorld. Projekt pro Android. Potom vytvořte nový zdrojový soubor C#. Přejmenujte soubor `MicrophoneService.cs` . Zkopírujte a vložte následující fragment kódu do tohoto souboru:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. Potom otevřete `AndroidManifest.xml` ve složce Properties ( *vlastnosti* ). Přidejte následující nastavení použití-oprávnění pro mikrofon mezi `<manifest>` a `</manifest>` :

   ```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
   ```
   
#### <a name="ios"></a>[iOS](#tab/ios)
## <a name="add-sample-code-for-the-helloworldios-project"></a>Přidat vzorový kód pro `helloworld.iOS` projekt

Nyní přidejte kód jazyka C#, který definuje část aplikace specifickou pro iOS. V projektu HelloWorld. iOS taky vytvořte konfigurace specifické pro zařízení Apple.

1. V **Průzkumník řešení**v projektu HelloWorld. iOS otevřete `AppDelegate.cs` .

1. Nahraďte veškerý kód v něm následujícím fragmentem kódu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. Potom do `MicrophoneService` projektu HelloWorld.IO přidejte implementaci nové složky, která je specifická pro *Services* iOS. Potom vytvořte nový zdrojový soubor C#. Přejmenujte soubor `MicrophoneService.cs` . Zkopírujte a vložte následující fragment kódu do tohoto souboru:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. `Info.plist`V textovém editoru otevřete v projektu HelloWorld. iOS. Do části dict – přidejte následující dvojici klíčových hodnot:

   <key>NSMicrophoneUsageDescription</key> 
    <string>Tato ukázková aplikace vyžaduje přístup přes mikrofon</string> .

   > [!NOTE]
   > Pokud vytváříte zařízení pro iPhone, ujistěte se, že `Bundle Identifier` odpovídá ID aplikace zřizovacího profilu vašeho zařízení. V opačném případě se sestavení nezdaří. Pomocí iPhoneSimulator můžete ponechat tak, jak je.

1. Pokud vytváříte počítač s Windows, navažte připojení k zařízení Mac pro vytváření přes dvojice **nástrojů**  >  **iOS**  >  **na Mac**. Postupujte podle pokynů průvodce, který poskytuje Visual Studio, a povolte tak připojení k zařízení Mac.

#### <a name="uwp"></a>[UWP](#tab/helloworlduwp)
## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Přidat vzorový kód pro `helloworld.UWP` projekt

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Přidejte vzorový kód pro HelloWorld. Projekt UWP

Nyní přidejte kód jazyka C#, který definuje část aplikace specifickou pro UWP.

1. V **Průzkumník řešení**v rámci HelloWorld. Projekt UWP, otevřete `MainPage.xaml.cs` .

1. Nahraďte veškerý kód v něm následujícím fragmentem kódu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Dále přidejte implementaci specifickou pro UWP pro `MicrophoneService` vytvořením nové *služby* složky v rámci HelloWorld. Projekt UWP. Potom vytvořte nový zdrojový soubor C#. Přejmenujte soubor `MicrophoneService.cs` . Zkopírujte a vložte následující fragment kódu do tohoto souboru:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Potom poklikejte na `Package.appxmanifest` soubor v rámci HelloWorld. Projekt UWP v aplikaci Visual Studio. V části **Možnosti**se ujistěte, že je vybraná možnost **mikrofon** , a uložte soubor.

1. Další poklikejte na `Package.appxmanifest` soubor v rámci `helloworld.UWP` projektu v aplikaci Visual Studio a v části **Možnosti**  >  **mikrofonu** je zaškrtnuté a uložte soubor.
   > Poznámka: pro případ, že se zobrazí upozornění: soubor certifikátu neexistuje: HelloWorld. UWP_TemporaryKey. pfx, další informace najdete v ukázce [řeči na text](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp) .

1. V řádku nabídek vyberte **soubor**  >  **Uložit vše** a uložte provedené změny.

## <a name="build-and-run-the-uwp-application"></a>Sestavení a spuštění aplikace UWP

1. Nastavte HelloWorld. UWP jako spouštěný projekt. Klikněte pravým tlačítkem myši na HelloWorld. Pro projekt UWP a vyberte **sestavení** pro sestavení aplikace.

1. Vyberte **ladit**  >  **Spustit ladění** (nebo vyberte **F5**) a spusťte tak aplikaci. Zobrazí se okno **HelloWorld** .

   ![Ukázková aplikace pro rozpoznávání řeči UWP v C# – rychlý Start](../../../../media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Vyberte **Povolit mikrofon**. Po zobrazení žádosti o oprávnění k přístupu vyberte **Ano**.

   ![Žádost o oprávnění k přístupu k mikrofonu](../../../../media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Vyberte možnost **spustit rozpoznávání řeči**a mluvte do mikrofonu zařízení anglickou frázi nebo větu. Vaše řeč se přenese do služby Speech a přepíše na text, který se zobrazí v okně.

   ![Uživatelské rozhraní rozpoznávání řeči](../../../../media/sdk/qs-csharp-xamarin-uwp-ui-result.png)
* * *

## <a name="build-and-run-the-android-and-ios-applications"></a>Sestavování a spouštění aplikací pro Android a iOS

Sestavování a spouštění aplikací pro Android a iOS v zařízení nebo simulátoru dochází podobně jako u UWP. Ujistěte se, že všechny sady SDK jsou správně nainstalované podle požadavků v tomto článku v části požadavky.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
