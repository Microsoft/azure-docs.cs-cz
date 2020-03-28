---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: erhopf
ms.openlocfilehash: 0bcdd315fe11b7472166a5a9ad4f7395e22d2126
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72675607"
---
Chcete-li vytvořit projekt sady Visual Studio pro vývoj mobilní aplikace .NET s Xamarinem pro různé platformy, musíte:
- Nastavte možnosti vývoje sady Visual Studio.
- Vytvořte projekt a vyberte cílovou architekturu. 
- Nainstalujte sadu Speech SDK.

### <a name="set-up-visual-studio-development-options"></a>Nastavení možností vývoje sady Visual Studio

Chcete-li začít, ujistěte se, že jste správně nastaveni ve Visual Studiu pro vývoj mobilních zařízení napříč platformami s rozhraním .NET:

1. Otevřete Visual Studio 2019.

1. Na řádku nabídek Sady Visual Studio vyberte **Nástroje** > **získat nástroje a funkce,** chcete-li otevřít Instalační službu sady Visual Studio a zobrazit dialogové okno **Úpravy.**

   ![Karta Úlohy, dialogové okno Úpravy, Instalační služba Sady Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-workload.png)

1. Na **kartě Úlohy** v části **Windows**vyhledejte vývoj Mobile s zatížením **.NET.** Pokud je políčko vedle tohoto pracovního vytížení již zaškrtnuté, **zavřete** dialogové okno Změnit a přejděte ke kroku 5.

1. Zaškrtněte políčko **Vývoj v mobilních zařízeních pomocí rozhraní .NET** a vyberte **Změnit**. V dialogovém okně **Než začneme,** vyberte **Pokračovat a** nainstalujte vývoj mobilních zařízení s zatížením rozhraní .NET. Instalace nové funkce může chvíli trvat.

1. Zavřete Instalační program sady Visual Studio.

### <a name="create-the-project"></a>Vytvoření projektu

1. Na řádku nabídek Sady Visual Studio vyberte **Soubor** > **nového** > **projektu,** **chcete-li** zobrazit okno Vytvořit nový projekt.

   ![Vytvoření nového projektu – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-create-new-project.png)

1. Najít a vybrat **mobilní aplikace (Xamarin.Forms)**.

1. Výběrem **možnosti Další** zobrazíte obrazovku **Konfigurovat nový projekt.**

   ![Konfigurace nového projektu – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-configure-your-new-project.png)

1. Do **názvu projektu**zadejte *helloworld*.

1. V **části Umístění**přejděte na složku, do které chcete projekt uložit, a vytvořte ji.

1. Vyberte **Vytvořit,** chcete-li přejít do okna **Projektu aplikace Xamarin Forms Nové mobilní aplikace.**

   ![Dialogové okno Nový projekt univerzální platformy Windows – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-new-xamarin-project.png)

1. Vyberte **šablonu Prázdné.**

1. V **platformě**vyberte pole pro **Android**, **iOS**a **Windows (UPW).**

1. Vyberte **OK**. Vrátíte se do rozhraní IDE sady Visual Studio s novým projektem vytvořeným a viditelným v podokně **Průzkumník a řešení.**

   ![Projekt Helloworld - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-helloworld.png)

Nyní vyberte architekturu cílové platformy a projekt spuštění. Na panelu nástrojů Visual Studio najděte rozevírací okno **Platformy řešení.** (Pokud ji nevidíte, vyberte **Zobrazit** > **panely nástrojů** > **Standard,** chcete-li zobrazit panel nástrojů, který obsahuje **platformy řešení**.) Pokud používáte 64bitový systém Windows, vyberte v rozevíracím poli **x64.** Pokud chcete, můžete vybrat **x86,** protože 64bitový systém Windows může také spouštět 32bitové aplikace. V rozevíracím poli **Start-up Projects** nastavte **helloworld. UPW (Univerzální Windows)**.

### <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Nainstalujte [balíček Speech SDK NuGet](https://aka.ms/csspeech/nuget)a odkazujte na sadu Speech SDK v projektu.

1. V **Průzkumníku řešení**klikněte pravým tlačítkem myši na vaše řešení. Vyberte **Spravovat balíčky NuGet pro řešení** pro přejděte do okna **NuGet - Solution.**

1. Vyberte **Procházet**.

   ![Snímek obrazovky s dialogovým oknem Spravovat balíčky pro řešení](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. Ve **zdroji balíčku**vyberte nuget.org.

1. Do pole **Hledat** zadejte *Microsoft.CognitiveServices.Speech*. Poté, co se zobrazí ve výsledcích hledání, vyberte tento balíček.

   ![Snímek obrazovky s dialogovým oknem Spravovat balíčky pro řešení](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-nuget-install.png)

   > [!NOTE] 
   > Knihovna iOS `Microsoft.CognitiveServices.Speech` uvnitř NuGet nemá bitový kód povolen. Pokud potřebujete povolenou knihovnu bitových `Microsoft.CognitiveServices.Speech.Xamarin.iOS` kódů pro vaši aplikaci, použijte NuGet pro projekt iOS konkrétně.

1. V podokně stavu balíčku vedle výsledků hledání vyberte všechny projekty: **helloworld**, **helloworld. Android**, **helloworld.iOS**, a **helloworld. UWP**.

1. Vyberte **Install** (Nainstalovat).

1. V dialogovém **okně Náhled změn** vyberte **OK**.

1. V dialogovém okně **Přijetí licence** zobrazte licenci a vyberte **Možnost Přijmout**. Nainstalujte odkaz na balíček sady Speech SDK na všechny projekty. Po úspěšném dokončení instalace se může zobrazit následující upozornění pro helloworld.iOS. Jedná se o známý problém, který by neměl mít vliv na funkčnost aplikace.

   > Nelze vyřešit odkaz "C:\Users\Default\.nuget\packages\microsoft.cognitiveservices.speech\1.7.0\build\Xamarin.iOS\libMicrosoft.CognitiveServices.Speech.core.a". Pokud tento odkaz je vyžadován kódem, může dojít k chybám kompilace.
