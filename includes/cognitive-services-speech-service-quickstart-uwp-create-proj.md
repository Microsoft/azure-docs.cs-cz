---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: 134c9996690d629ad8acb348d1928904741b6b2d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91654388"
---
Chcete-li vytvořit projekt sady Visual Studio pro Univerzální platforma Windows (UWP), je nutné nastavit vývojové možnosti sady Visual Studio, vytvořit projekt, vybrat cílovou architekturu, nastavit záznam zvuku a nainstalovat sadu Speech SDK.

### <a name="set-up-visual-studio-development-options"></a>Nastavení možností vývoje pro Visual Studio

Pokud chcete začít, ujistěte se, že jste správně nastavili v sadě Visual Studio pro vývoj UWP:

1. Otevřete Visual Studio 2019 pro zobrazení okna **Start** .

   ![Snímek obrazovky, který zobrazuje okno "spustit" se zvýrazněnou akcí pokračovat bez kódu ".](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-start-window.png)

1. Pokud chcete přejít na integrované vývojové prostředí (IDE) sady Visual Studio, vyberte **pokračovat bez kódu** .

1. V řádku nabídek sady Visual Studio vyberte **nástroje**  >  **získat nástroje a funkce** . otevře se instalační program pro Visual Studio a zobrazí se dialogové okno **změny** .

   ![Snímek obrazovky zobrazující kartu úlohy v dialogovém okně úpravy s zvýrazněným Univerzální platforma Windowsm vývojem](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

1. Na kartě **úlohy** v části **Windows**Najděte **Univerzální platforma Windows vývojové** úlohy. Pokud je zaškrtnuté políčko vedle této úlohy, zavřete dialogové okno **Úpravy** a pokračujte na krok 6.

1. Zaškrtněte políčko **Univerzální platforma Windows vývoje** , vyberte **Upravit**a potom v dialogovém okně **před** spuštěním vyberte **pokračovat** a nainstalujte úlohu vývoje UWP. Instalace nové funkce může chvíli trvat.

1. Zavřete Instalační program pro Visual Studio.

### <a name="create-the-project-and-select-the-target-architecture"></a>Vytvořit projekt a vybrat cílovou architekturu

Potom vytvořte projekt:

1. V řádku nabídek sady Visual Studio vyberte **soubor**  >  **Nový**  >  **projekt** , aby se zobrazilo okno **vytvořit nový projekt** .

   ![Snímek obrazovky, který zobrazuje okno vytvořit nový projekt s vybranou možností prázdná aplikace (univerzální pro Windows) a zvýrazněným tlačítkem Další.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-create-new-project.png)

1. Najděte a vyberte **prázdná aplikace (univerzální pro Windows)**. Ujistěte se, že jste vybrali verzi jazyka C# tohoto typu projektu (na rozdíl od Visual Basic).

1. Kliknutím na tlačítko **Další** zobrazíte obrazovku **Konfigurovat nový projekt** .

   ![Snímek obrazovky zobrazující obrazovku konfigurovat nový projekt s poli název projektu a umístění a zvýrazněným tlačítkem vytvořit.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-configure-your-new-project.png)

1. Do **název projektu**zadejte `helloworld` .

1. V části **umístění**přejděte na a vyberte nebo vytvořte složku, do které chcete projekt uložit.

1. Výběrem možnosti **vytvořit** přejdete do okna **Nový Univerzální platforma Windows projektu** .

   ![Snímek obrazovky, který zobrazuje dialogové okno Nový projekt Univerzální platforma Windows.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. V **minimální verzi** (druhý rozevírací seznam) vyberte **Windows 10 poklesnout Creators Update (10,0; Sestavení 16299)**, což je minimální požadavek pro sadu Speech SDK.

1. V **cílové verzi** (první rozevírací seznam) vyberte hodnotu, která je stejná nebo vyšší než hodnota v **minimální verzi**.

1. Vyberte **OK**. Vrátíte se do integrovaného vývojového prostředí sady Visual Studio s novým projektem vytvořeným a zobrazeným v podokně **Průzkumník řešení** .

   ![projekt HelloWorld – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-helloworld.png)

Teď vyberte cílovou architekturu platformy. Na panelu nástrojů sady Visual Studio Najděte rozevírací seznam **platformy řešení** . (Pokud ji nevidíte, klikněte na tlačítko **Zobrazit**  >  **Panely nástrojů**  >  **Standard** pro zobrazení panelu nástrojů obsahujícího **platformy řešení**.) Pokud používáte 64-bit Windows, v rozevíracím seznamu vyberte **x64** . 64-bitová okna můžou spouštět i 32 aplikace, takže pokud chcete, můžete vybrat možnost **x86** .

> [!NOTE]
> Sada Speech SDK podporuje všechny procesory kompatibilní s platformou Intel, ale **pouze 64bitové** verze procesorů ARM.

### <a name="set-up-audio-capture"></a>Nastavení záznamu zvuku

Umožněte zachytávání zvukového vstupu v projektu:

1. V **Průzkumník řešení**dvakrát klikněte na **Package. appxmanifest** a otevřete manifest aplikace balíčku.

1. Vyberte kartu **Možnosti** .

   ![Karta Možnosti, manifest aplikace balíčku – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)

1. Zaškrtněte políčko pro schopnost **mikrofonu** .

1. V řádku nabídek vyberte **soubor**  >  **Uložit balíček. appxmanifest** a uložte provedené změny.

### <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Nakonec nainstalujte [balíček NuGet sady Speech SDK](https://aka.ms/csspeech/nuget)a odkažte na sadu Speech SDK v projektu:

1. V **Průzkumník řešení**klikněte pravým tlačítkem myši na vaše řešení a vyberte **Spravovat balíčky NuGet pro řešení** , abyste přešli do okna **NuGet – řešení** .

1. Vyberte **Procházet**.

   ![Snímek obrazovky, který zobrazuje dialogové okno Spravovat balíčky pro řešení pomocí karty Procházet, hledání a "zdroj balíčku" byl zvýrazněn.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. Ve **zdroji balíčku**vyberte možnost **NuGet.org**.

1. Do **vyhledávacího** pole zadejte `Microsoft.CognitiveServices.Speech` a po zobrazení ve výsledcích hledání zvolte tento balíček.

   ![Snímek obrazovky, na kterém je vybraný Microsoft. Cognitiveservices Account. Speech, se zvýrazněným tlačítkem pro projekt a instalaci.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png)

1. V podokně stav balíčku vedle výsledků hledání vyberte svůj projekt **HelloWorld** .

1. Vyberte **Nainstalovat**.

1. V dialogovém okně **Náhled změn** vyberte **OK**.

1. V dialogovém okně **přijetí licence** si prohlédněte licenci a pak vyberte **Souhlasím**. Spustí se instalace balíčku. po dokončení instalace se v podokně **výstup** zobrazí zpráva podobná následujícímu textu: `Successfully installed 'Microsoft.CognitiveServices.Speech 1.13.0' to helloworld` .
