---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/19/2019
ms.author: erhopf
ms.openlocfilehash: 0140981a694a7a7cd8556f7139a90d0656679d7d
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70381978"
---
Chcete-li vytvořit projekt sady Visual Studio pro Univerzální platforma Windows (UWP), je nutné nastavit vývojové možnosti sady Visual Studio, vytvořit projekt, vybrat cílovou architekturu, nastavit záznam zvuku a nainstalovat sadu Speech SDK.

### <a name="set-up-visual-studio-development-options"></a>Nastavení možností vývoje pro Visual Studio

Pokud chcete začít, ujistěte se, že jste správně nastavili v sadě Visual Studio pro vývoj UWP:

1. Otevřete Visual Studio 2019 pro zobrazení okna **Start** .

   ![Spustit okno – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-start-window.png)

1. Pokud chcete přejít na integrované vývojové prostředí (IDE) sady Visual Studio, vyberte **pokračovat bez kódu** .

1. V řádku nabídek sady Visual Studio vyberte **nástroje** > **získat nástroje a funkce** . otevře se instalační program pro Visual Studio a zobrazí se dialogové okno **změny** .

   ![Karta úlohy, změny dialogového okna, Instalační program pro Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

1. Na kartě **úlohy** v části **Windows**Najděte **Univerzální platforma Windows vývojové** úlohy. Pokud je zaškrtnuté políčko vedle této úlohy, zavřete dialogové okno **Úpravy** a pokračujte na krok 6.

1. Zaškrtněte políčko **Univerzální platforma Windows vývoje** , vyberte **Upravit**a potom v dialogovém okně **před** spuštěním vyberte **pokračovat** a nainstalujte úlohu vývoje UWP. Instalace nové funkce může chvíli trvat.

1. Zavřete Instalační program pro Visual Studio.

### <a name="create-the-project-and-select-the-target-architecture"></a>Vytvořit projekt a vybrat cílovou architekturu

Potom vytvořte projekt:

1. V řádku nabídek sady Visual Studio vyberte **soubor** > **Nový** > **projekt** , aby se zobrazilo okno **vytvořit nový projekt** .

   ![Vytvořit nový projekt – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-create-new-project.png)

1. Najděte a vyberte **prázdná aplikace (univerzální pro Windows)** . Ujistěte se, že jste vybrali C# verzi tohoto typu projektu (na rozdíl od Visual Basic).

1. Kliknutím na tlačítko **Další** zobrazíte obrazovku **Konfigurovat nový projekt** . 

   ![Konfigurace nového projektu – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-configure-your-new-project.png)

1. Do **název projektu**zadejte `helloworld`.

1. V části **umístění**přejděte na a vyberte nebo vytvořte složku, do které chcete projekt uložit.

1. Výběrem možnosti **vytvořit** přejdete do okna **Nový Univerzální platforma Windows projektu** .

   ![Dialogové okno Nový projekt Univerzální platforma Windows – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. V **minimální verzi** (druhý rozevírací seznam) vyberte **Windows 10 poklesnout Creators Update (10,0; Sestavení 16299)** , což je minimální požadavek pro sadu Speech SDK.

1. V **cílové verzi** (první rozevírací seznam) vyberte hodnotu, která je stejná nebo vyšší než hodnota v **minimální verzi**.

1. Vyberte **OK**. Vrátíte se do integrovaného vývojového prostředí sady Visual Studio s novým projektem vytvořeným a zobrazeným v podokně **Průzkumník řešení** .

   ![projekt HelloWorld – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-helloworld.png)

Teď vyberte cílovou architekturu platformy. Na panelu nástrojů sady Visual Studio Najděte rozevírací seznam **platformy řešení** . (Pokud ji nevidíte, vyberte možnost **Zobrazit** > **panely nástrojů** > **standardní** a zobrazte panel nástrojů obsahující **platformy řešení**.) Pokud používáte 64-bit Windows, v rozevíracím seznamu vyberte **x64** . 64-bitová okna můžou spouštět i 32 aplikace, takže pokud chcete, můžete vybrat možnost **x86** .

> [!NOTE]
> Sada Speech SDK podporuje pouze procesory kompatibilní s procesorem Intel. Procesory ARM se aktuálně nepodporují.

### <a name="set-up-audio-capture"></a>Nastavení záznamu zvuku

Pak umožněte zachytávání zvukového vstupu v projektu:

1. V **Průzkumník řešení**dvakrát klikněte na **Package. appxmanifest** a otevřete manifest aplikace balíčku.

1. Vyberte kartu **Možnosti** .

   ![Karta Možnosti, manifest aplikace balíčku – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)

1. Zaškrtněte políčko pro schopnost **mikrofonu** .

1. V řádku nabídek vyberte **soubor** > **Uložit balíček. appxmanifest** a uložte provedené změny.

### <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Nakonec nainstalujte [balíček NuGet sady Speech SDK](https://aka.ms/csspeech/nuget)a odkažte na sadu Speech SDK v projektu:

1. V **Průzkumník řešení**klikněte pravým tlačítkem myši na vaše řešení a vyberte **Spravovat balíčky NuGet pro řešení** , abyste přešli do okna **NuGet – řešení** .

1. Vyberte **Procházet**.

   ![Snímek obrazovky s dialogovým oknem Spravovat balíčky pro řešení](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. Ve **zdroji balíčku**vyberte možnost **NuGet.org**.

1. Do **vyhledávacího** pole zadejte `Microsoft.CognitiveServices.Speech`a po zobrazení ve výsledcích hledání zvolte tento balíček.

   ![Snímek obrazovky s dialogovým oknem Spravovat balíčky pro řešení](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png)

1. V podokně stav balíčku vedle výsledků hledání vyberte svůj projekt **HelloWorld** .

1. Vyberte **Install** (Nainstalovat).

1. V dialogovém okně **Náhled změn** vyberte **OK**.

1. V dialogovém okně **přijetí licence** si prohlédněte licenci a pak vyberte **Souhlasím**. Spustí se instalace balíčku. po dokončení instalace se v podokně **výstup** zobrazí zpráva podobná následujícímu textu: `Successfully installed 'Microsoft.CognitiveServices.Speech 1.6.0' to helloworld`.
