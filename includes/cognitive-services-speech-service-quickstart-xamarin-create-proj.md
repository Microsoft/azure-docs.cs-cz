---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: erhopf
ms.openlocfilehash: 0e4e67710c98b80dce2b0d55a86869625f3942d2
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71837458"
---
Chcete-li vytvořit projekt sady Visual Studio pro vývoj mobilních aplikací pro různé platformy pomocí Xamarin, je nutné nastavit možnosti vývoje sady Visual Studio, vytvořit projekt, vybrat cílovou architekturu a nainstalovat sadu Speech SDK.

### <a name="set-up-visual-studio-development-options"></a>Nastavení možností vývoje pro Visual Studio

Pokud chcete začít, ujistěte se, že jste správně nastavili v sadě Visual Studio pro vývoj mobilních aplikací pro různé platformy pomocí .NET:

1. Otevřete Visual Studio 2019.

1. V řádku nabídek sady Visual Studio vyberte **nástroje** > **získat nástroje a funkce** pro otevření instalační program pro Visual Studio a zobrazení dialogového okna **změny** .

   ![Karta úlohy, změny dialogového okna, Instalační program pro Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-workload.png)

1. Na kartě **úlohy** v části **Windows**Najděte **vývoj mobilních aplikací pomocí technologie .NET** . Pokud je zaškrtnuté políčko vedle této úlohy, zavřete dialogové okno **Úpravy** a pokračujte krokem 5.

1. Zaškrtněte políčko **vývoj pro mobilní zařízení pomocí .NET** , vyberte **Upravit**a potom v dialogovém okně **před** spuštěním vyberte **pokračovat** a nainstalujte mobilní vývoj s využitím úlohy .NET. Instalace nové funkce může chvíli trvat.

1. Zavřete Instalační program pro Visual Studio.

### <a name="create-the-project"></a>Vytvoření projektu

1. V řádku nabídek sady Visual Studio vyberte **soubor** > **Nový** **projekt**  >  pro zobrazení okna **vytvořit nový projekt** .

   ![Vytvořit nový projekt – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-create-new-project.png)

1. Vyhledejte a vyberte **mobilní aplikace (Xamarin Forms)** .

1. Kliknutím na tlačítko **Další** zobrazíte obrazovku **Konfigurovat nový projekt** . 

   ![Konfigurace nového projektu – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-configure-your-new-project.png)

1. Do **název projektu**zadejte `helloworld`.

1. V části **umístění**přejděte na a vyberte nebo vytvořte složku, do které chcete projekt uložit.

1. Výběrem možnosti **vytvořit** přejdete do okna **Nový projekt mobilní aplikace Xamarin Forms** .

   ![Dialogové okno Nový projekt Univerzální platforma Windows – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-new-xamarin-project.png)

1. Vybrat **prázdnou** šablonu

1. V poli **platforma**zaškrtněte políčka pro **Android**, **iOS** a **Windows (UWP)** .

1. Vyberte **OK**. Vrátíte se do integrovaného vývojového prostředí sady Visual Studio s novým projektem vytvořeným a zobrazeným v podokně **Průzkumník řešení** .

   ![projekt HelloWorld – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-helloworld.png)

Nyní vyberte svou cílovou architekturu platformy a spusťte projekt. Na panelu nástrojů sady Visual Studio Najděte rozevírací seznam **platformy řešení** . (Pokud ji nevidíte, vyberte možnost **zobrazit** > **panely nástrojů**@no__t **-3. zobrazí se panel** nástrojů obsahující **platformy řešení**.) Pokud používáte 64-bit Windows, v rozevíracím seznamu vyberte **x64** . 64-bitová okna můžou spouštět i 32 aplikace, takže pokud chcete, můžete vybrat možnost **x86** . V rozevíracím seznamu pro **spouštěcí projekty** nastavte HelloWorld. UWP (univerzální pro Windows)

### <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Nainstalujte [balíček NuGet sady Speech SDK](https://aka.ms/csspeech/nuget)a odkažte na sadu Speech SDK v projektu:

1. V **Průzkumník řešení**klikněte pravým tlačítkem myši na vaše řešení a vyberte **Spravovat balíčky NuGet pro řešení** , abyste přešli do okna **NuGet – řešení** .

1. Vyberte **Procházet**.

   ![Snímek obrazovky s dialogem spravovat balíčky pro řešení](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. Ve **zdroji balíčku**vyberte možnost **NuGet.org**.

1. Do **vyhledávacího** pole zadejte `Microsoft.CognitiveServices.Speech` a po zobrazení ve výsledcích hledání zvolte tento balíček.

   ![Snímek obrazovky s dialogem spravovat balíčky pro řešení](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-nuget-install.png)
   > Poznámka: knihovna iOS v Microsoft. Cognitiveservices Account. Speech NuGet nemá povolený bitcode. V případě, že potřebujete pro svou aplikaci bitcode povolenou knihovnu, použijte konkrétně Microsoft. Cognitiveservices Account. Speech. Xamarin. iOS NuGet pro projekt iOS.

1. V podokně stav balíčku vedle výsledků hledání vyberte všechny projekty. **HelloWorld**, **HelloWorld. Android**, **HelloWorld. iOS** a **HelloWorld. UWP**.

1. Vyberte **nainstalovat**.

1. V dialogovém okně **Náhled změn** vyberte **OK**.

1. V dialogovém okně **přijetí licence** si zobrazte licenci a pak vyberte možnost **Přijímám** a instalovat odkaz na balíček sady Speech SDK do všech projektů. Po úspěšném dokončení instalace se může zobrazit následující upozornění pro HelloWorld. iOS. Jedná se o známý problém a neměl by mít vliv na funkčnost vaší aplikace.

> Nepovedlo se přeložit referenci "C:\Users\Default @ no__t-0nuget\packages\microsoft.cognitiveservices.speech\1.7.0\build\Xamarin.iOS\libMicrosoft.CognitiveServices.Speech.core.a". Pokud je tento odkaz požadován vaším kódem, může dojít k chybám kompilace.
