---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: erhopf
ms.openlocfilehash: 35133b64e43ca3814410d7bbf2ac1be9f760b369
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91654391"
---
Chcete-li vytvořit projekt sady Visual Studio pro vývoj mobilních aplikací pro různé platformy pomocí Xamarin, budete potřebovat:
- Nastavení možností vývoje sady Visual Studio.
- Vytvořte projekt a vyberte cílovou architekturu. 
- Nainstalujte sadu Speech SDK.

### <a name="set-up-visual-studio-development-options"></a>Nastavení možností vývoje pro Visual Studio

Pokud chcete začít, ujistěte se, že jste správně nastavili v sadě Visual Studio pro vývoj mobilních aplikací pro různé platformy pomocí .NET:

1. Otevřete Visual Studio 2019.

1. V řádku nabídek sady Visual Studio vyberte **nástroje**  >  **získat nástroje a funkce** . otevře se instalační program pro Visual Studio a zobrazí se dialogové okno **změny** .

   ![Snímek obrazovky zobrazující kartu úlohy, změny dialogového okna a Instalační program pro Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-workload.png)

1. Na kartě **úlohy** v části **Windows**Najděte **vývoj mobilních aplikací pomocí technologie .NET** . Pokud je zaškrtnuté políčko vedle této úlohy, zavřete dialogové okno **Úpravy** a pokračujte krokem 5.

1. Zaškrtněte políčko **vývoj pro mobilní zařízení pomocí .NET** a pak vyberte **Upravit**. V dialogovém okně **předtím, než** začnete, vyberte **pokračovat** a nainstalujte mobilní vývoj pomocí úlohy .NET. Instalace nové funkce může chvíli trvat.

1. Zavřete Instalační program pro Visual Studio.

### <a name="create-the-project"></a>Vytvoření projektu

1. Na panelu nabídek aplikace Visual Studio vyberte **soubor**  >  **Nový**  >  **projekt** , aby se zobrazilo okno **vytvořit nový projekt** .

   ![Snímek obrazovky, který ukazuje, jak vytvořit nový projekt v aplikaci Visual Studio.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-create-new-project.png)

1. Vyhledejte a vyberte **mobilní aplikace (Xamarin. Forms)**.

1. Kliknutím na tlačítko **Další** zobrazíte obrazovku **Konfigurovat nový projekt** .

   ![Snímek obrazovky, který ukazuje, jak nakonfigurovat nový projekt v aplikaci Visual Studio.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-configure-your-new-project.png)

1. Do **název projektu**zadejte *HelloWorld*.

1. V části **umístění**klikněte na a vyberte nebo vytvořte složku, do které chcete projekt uložit.

1. Výběrem možnosti **vytvořit** přejdete do okna **Nový projekt mobilní aplikace Xamarin Forms** .

   ![Snímek obrazovky, který zobrazuje dialogové okno Nový projekt Univerzální platforma Windows v aplikaci Visual Studio.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-new-xamarin-project.png)

1. Vyberte **prázdnou** šablonu.

1. V části **platforma**zaškrtněte políčka pro **Android**, **iOS**a **Windows (UWP)**.

1. Vyberte **OK**. Vrátíte se do integrovaného vývojového prostředí sady Visual Studio s novým projektem vytvořeným a zobrazeným v podokně **Průzkumník řešení** .

   ![Projekt HelloWorld – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-helloworld.png)

Nyní vyberte cílovou architekturu platformy a spouštěný projekt. Na panelu nástrojů sady Visual Studio Najděte rozevírací seznam **platformy řešení** . (Pokud ji nevidíte, vyberte **Zobrazit**  >  **Panely nástrojů**  >  **Standard** pro zobrazení panelu nástrojů, který obsahuje **platformy řešení**.) Pokud používáte 64-bit Windows, v rozevíracím seznamu vyberte **x64** . Pokud 64 chcete, aby 32bitové aplikace Windows mohly spouštět i 32 aplikace, můžete vybrat možnost **x86** . V rozevíracím seznamu **Spustit projekty** nastavte **HelloWorld. UWP (univerzální pro Windows)**

### <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Nainstalujte [balíček NuGet sady Speech SDK](https://aka.ms/csspeech/nuget)a odkažte na sadu Speech SDK v projektu.

1. V **Průzkumník řešení**klikněte pravým tlačítkem myši na vaše řešení. Výběrem **možnosti spravovat balíčky NuGet pro řešení** přejdete do okna **řešení NuGet** .

1. Vyberte **Procházet**.

   ![Snímek obrazovky dialogového okna spravovat balíčky pro řešení při instalaci sady Speech SDK](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. Ve **zdroji balíčku**vyberte NuGet.org.

1. Do **vyhledávacího** pole zadejte *Microsoft. cognitiveservices Account. Speech*. Pak vyberte tento balíček, jakmile se zobrazí ve výsledcích hledání.

   ![Snímek obrazovky, který zvýrazní balíček Microsoft. Cognitiveservices Account. Speech.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-nuget-install.png)

   > [!NOTE] 
   > Knihovna iOS uvnitř `Microsoft.CognitiveServices.Speech` NuGet nemá povolený bitcode. Pokud potřebujete pro svou aplikaci povolenou knihovnu bitcode, použijte `Microsoft.CognitiveServices.Speech.Xamarin.iOS` konkrétně NuGet pro projekt iOS.

1. V podokně stav balíčku vedle výsledků hledání vyberte všechny projekty: **HelloWorld**, **HelloWorld. Android**, **HelloWorld. iOS**a **HelloWorld. UWP**.

1. Vyberte **Nainstalovat**.

1. V dialogovém okně **Náhled změn** vyberte **OK**.

1. V dialogovém okně **přijetí licence** si prohlédněte licenci a pak vyberte **Souhlasím**. Nainstalujte odkaz na balíček sady Speech SDK do všech projektů. Po úspěšném dokončení instalace se může zobrazit následující upozornění pro HelloWorld. iOS. Jedná se o známý problém a neměl by mít vliv na funkčnost vaší aplikace.

   > Nepovedlo se přeložit referenci "C:\Users\Default \. nuget\packages\microsoft.cognitiveservices.speech\1.7.0\build\Xamarin.iOS\libMicrosoft.CognitiveServices.Speech.Core.a". Pokud je tento odkaz požadován vaším kódem, může dojít k chybám kompilace.
