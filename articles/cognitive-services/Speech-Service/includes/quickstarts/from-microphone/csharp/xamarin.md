---
title: 'Úvodní příručka: Rozpoznávání řeči z mikrofonu, C# (Xamarin) - Služba řeči'
titleSuffix: Azure Cognitive Services
description: V tomto článku vytvoříte aplikace C# Xamarin pro univerzální platformu Windows (UPW), Android a iOS pomocí sady Cognitive Services Speech SDK. Přepisujete řeč na text v reálném čase z mikrofonu vašeho zařízení nebo simulátoru. Aplikace je vytvořena pomocí balíčku Speech SDK NuGet package a Microsoft Visual Studio 2019.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/02/2020
ms.author: erhopf
ms.openlocfilehash: c9bcd301b19252cedd9ac9a1867ccf132a537587
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274722"
---
## <a name="prerequisites"></a>Požadavky

Než začnete:

> [!div class="checklist"]
> * [Vytvoření řečového prostředku Azure](../../../../get-started.md)
> * [Nastavení vývojového prostředí a vytvoření prázdného projektu](../../../../quickstarts/setup-platform.md?tabs=xamarin&pivots=programming-language-csharp)
> * Ujistěte se, že máte přístup k mikrofonu pro snímání zvuku

Pokud jste to už udělali, skvělé. Pojďme dál.

## <a name="add-sample-code-for-the-common-helloworld-project"></a>Přidat ukázkový kód pro společný projekt Helloworld

Společný projekt Helloworld obsahuje implementace nezávislé na platformě pro vaši aplikaci napříč platformami. Nyní přidejte kód XAML, který definuje uživatelské rozhraní aplikace, a přidejte kód Jazyka C# za implementaci.

1. V **Průzkumníku řešení**otevřete v `MainPage.xaml`rámci společného projektu Helloworld .

1. V zobrazení XAML návrháře vložte do značky **Mřížka** mezi `<StackLayout>` a: `</StackLayout>`

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml)]

1. V **Průzkumníku řešení**otevřete zdrojový `MainPage.xaml.cs`soubor s kódem na pozadí . Je seskupena pod `MainPage.xaml`.

1. Nahraďte veškerý kód v něm následujícím fragmentem:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/MainPage.xaml.cs)]

1. Ve obslužné rutině `OnRecognitionButtonClicked` `YourSubscriptionKey`zdrojového souboru najděte řetězec a nahraďte ho klíčem předplatného.


1. V `OnRecognitionButtonClicked` obslužné `YourServiceregion`rutině vyhledejte řetězec a nahraďte jej **identifikátorem oblasti** z [oblasti](https://aka.ms/speech/sdkregion) přidružené k vašemu předplatnému. (Použijte například `westus` bezplatné zkušební předplatné.)

1. Dále je třeba vytvořit [službu Xamarin](https://docs.microsoft.com/xamarin/android/app-fundamentals/services/creating-a-service/), která se používá k dotazování oprávnění mikrofonu z různých projektů platformy, jako je NAPříklad UPW, Android a iOS. Chcete-li to provést, přidejte novou složku s názvem *Služby* v rámci projektu helloworld a vytvořte pod ním nový zdrojový soubor Jazyka C#. Můžete klepnout pravým tlačítkem myši na složku *Služby* a vybrat **možnost Přidat** > **soubor kódu**nové**položky** > . Přejmenujte `IMicrophoneService.cs`soubor a umístěte do tohoto souboru veškerý kód z následujícího fragmentu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld/Services/IMicrophoneService.cs)]

#### <a name="android"></a>[Android](#tab/x-android)
## <a name="add-sample-code-for-the-helloworldandroid-project"></a>Přidání ukázkového `helloworld.Android` kódu projektu

Nyní přidejte kód Jazyka C#, který definuje část aplikace specifickou pro Android.

1. V **Průzkumníku řešení**, pod helloworld. Android projekt, `MainActivity.cs`otevřít .

1. Nahraďte veškerý kód v něm následujícím fragmentem:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/MainActivity.cs)]

1. Dále přidejte implementaci specifickou pro `MicrophoneService` Android vytvořením nové složky *Služby* pod helloworld. Android projektu. Poté pod ním vytvořte nový zdrojový soubor Jazyka C#. Přejmenujte `MicrophoneService.cs`soubor . Zkopírujte a vložte do tohoto souboru následující fragment kódu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.Android/Services/MicrophoneService.cs)]

1. Poté otevřete `AndroidManifest.xml` ve složce *Vlastnosti.* Mezi `<manifest>` a `</manifest>`:

   ```xml
   <uses-permission android:name="android.permission.RECORD_AUDIO" />
   ```
   
#### <a name="ios"></a>[iOS](#tab/ios)
## <a name="add-sample-code-for-the-helloworldios-project"></a>Přidání ukázkového `helloworld.iOS` kódu projektu

Nyní přidejte kód C#, který definuje část aplikace specifickou pro iOS. Vytvořte také konfigurace specifické pro zařízení Apple pro projekt helloworld.iOS.

1. V **Průzkumníku řešení**otevřete v projektu helloworld.iOS . `AppDelegate.cs`

1. Nahraďte veškerý kód v něm následujícím fragmentem:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/AppDelegate.cs)]

1. Dále přidejte implementaci specifickou `MicrophoneService` pro iOS vytvořením nové složky *Služby* v rámci projektu helloworld.iO. Poté pod ním vytvořte nový zdrojový soubor Jazyka C#. Přejmenujte `MicrophoneService.cs`soubor . Zkopírujte a vložte do tohoto souboru následující fragment kódu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.iOS/Services/MicrophoneService.cs)]

1. Otevřete `Info.plist` pod projektem helloworld.iOS v textovém editoru. Pod oddíl dict přidejte následující dvojici hodnot klíče:

   <key>NSMicrophoneUsageDescription</key>
   <string>Tato ukázková aplikace vyžaduje přístup k mikrofonu</string>

   > [!NOTE]
   > Pokud vytváříte pro iPhone zařízení, `Bundle Identifier` ujistěte se, že odpovídá ID zřizovacího profilu vašeho zařízení. V opačném případě sestavení se nezdaří. S iPhoneSimulator, můžete nechat tak, jak je.

1. Pokud stavíte na počítači s Windows, navazte na připojení k zařízení Mac pro vytváření pomocí nástrojů > **iOS** > **Pair to Mac**. **Tools** Chcete-li povolit připojení k zařízení Mac, postupujte podle pokynů poskytnutých souborem Visual Studio.

#### <a name="uwp"></a>[UWP](#tab/helloworlduwp)
## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Přidání ukázkového `helloworld.UWP` kódu projektu

## <a name="add-sample-code-for-the-helloworlduwp-project"></a>Přidejte ukázkový kód pro helloworld. Projekt UPW

Nyní přidejte kód Jazyka C#, který definuje část aplikace specifickou pro UPW.

1. V **Průzkumníku řešení**, pod helloworld. Projekt UPW, `MainPage.xaml.cs`otevřít .

1. Nahraďte veškerý kód v něm následujícím fragmentem:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/MainPage.xaml.cs)]

1. Dále přidejte implementaci specifickou `MicrophoneService` pro UWP vytvořením nové složky *Služby* pod helloworld. Projekt UPW. Poté pod ním vytvořte nový zdrojový soubor Jazyka C#. Přejmenujte `MicrophoneService.cs`soubor . Zkopírujte a vložte do tohoto souboru následující fragment kódu:

   [!code-csharp[Quickstart code](~/samples-cognitive-services-speech-sdk/quickstart/csharp/xamarin/helloworld/helloworld.UWP/Services/MicrophoneService.cs)]

1. Dále poklepejte `Package.appxmanifest` na soubor pod helloworld. UWP projektu uvnitř Sady Visual. V části **Capabilities**zkontrolujte, zda je vybraná volba **Mikrofon,** a uložte soubor.

1. Další poklepání `Package.appxmanifest` na `helloworld.UWP` soubor v rámci projektu uvnitř sady Visual Studio a v části **Možnosti** > **mikrofon** je zaškrtnuto a uložte soubor.
   > Poznámka: V případě, že vidíte varování : Soubor certifikátu neexistuje: helloworld. UWP_TemporaryKey.pfx, zkontrolujte, zda [řeč na vzorek textu](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=uwp) pro více informací.

1. Na řádku nabídek vyberte **Uložit** > **vše,** chcete-li uložit změny.

## <a name="build-and-run-the-uwp-application"></a>Sestavení a spuštění aplikace UPW

1. Nastavte helloworld. UWP jako spouštěcí projekt. Klikněte pravým tlačítkem myši na svět vítání. UWP projektu a vyberte **sestavení** k sestavení aplikace.

1. Chcete-li spustit aplikaci, vyberte **možnost Ladění** > **zahájit ladění** (nebo vyberte možnost **F5).** Zobrazí se okno **Helloworld.**

   ![Ukázka aplikace rozpoznávání řeči UpW v c# - rychlý start](../../../../media/sdk/qs-csharp-xamarin-helloworld-uwp-window.png)

1. Vyberte **povolit mikrofon**. Po zobrazíní žádost o přístupová oprávnění vyberte **Ano**.

   ![Žádost o oprávnění k přístupu k mikrofonu](../../../../media/sdk/qs-csharp-xamarin-uwp-access-prompt.png)

1. Vyberte **Spustit rozpoznávání řeči**a do mikrofonu zařízení vyslovte anglickou frázi nebo větu. Vaše řeč se přenese do služby Speech a přepíše na text, který se zobrazí v okně.

   ![Uživatelské rozhraní pro rozpoznávání řeči](../../../../media/sdk/qs-csharp-xamarin-uwp-ui-result.png)
* * *

## <a name="build-and-run-the-android-and-ios-applications"></a>Vytváření a spouštění aplikací pro Android a iOS

Vytváření a spouštění aplikací pro Android a iOS v zařízení nebo simulátoru se děje podobným způsobem jako UPW. Ujistěte se, že všechny sady SDK jsou nainstalovány správně podle potřeby v části "Požadavky" tohoto článku.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
