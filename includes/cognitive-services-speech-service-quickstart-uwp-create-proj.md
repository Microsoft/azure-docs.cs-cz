---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 02/10/2020
ms.author: erhopf
ms.openlocfilehash: 6804834dab81b0774dd29b1ede836492d0dfea4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78383902"
---
Chcete-li vytvořit projekt sady Visual Studio pro vývoj platformy Universal Windows Platform (UPW), je třeba nastavit možnosti vývoje sady Visual Studio, vytvořit projekt, vybrat cílovou architekturu, nastavit sběr zvuku a nainstalovat sadu Speech SDK.

### <a name="set-up-visual-studio-development-options"></a>Nastavení možností vývoje sady Visual Studio

Chcete-li začít, ujistěte se, že jste správně nastaveni ve Visual Studiu pro vývoj UPW:

1. Otevřete Visual Studio 2019 pro zobrazení okna **Start.**

   ![Počáteční okno – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-start-window.png)

1. Chcete-li přejít na ide sady Visual Studio, vyberte **pokračovat bez kódu.**

1. Na řádku nabídek Sady Visual Studio vyberte **Nástroje** > **získat nástroje a funkce,** chcete-li otevřít Instalační službu sady Visual Studio a zobrazit dialogové okno **Úpravy.**

   ![Karta Úlohy, dialogové okno Úpravy, Instalační služba Sady Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

1. Na kartě **Úlohy** v části **Windows**najdete pracovní vytížení **vývoje univerzální platformy Windows.** Pokud je políčko vedle tohoto pracovního vytížení již zaškrtnuté, **zavřete** dialogové okno Změnit a přejděte ke kroku 6.

1. Zaškrtněte políčko **Univerzální vývoj platformy Windows,** vyberte **Změnit**a potom v **dialogovém okně Než začneme,** a vyberte **Pokračovat a** nainstalujte pracovní vytížení vývoje UPW. Instalace nové funkce může chvíli trvat.

1. Zavřete Instalační program sady Visual Studio.

### <a name="create-the-project-and-select-the-target-architecture"></a>Vytvoření projektu a výběr cílové architektury

Dále vytvořte projekt:

1. Na řádku nabídek Sady Visual Studio zvolte **Soubor** > **nového** > **projektu,** **chcete-li** zobrazit okno Vytvořit nový projekt.

   ![Vytvoření nového projektu – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-create-new-project.png)

1. Najít a vybrat **Prázdná aplikace (Univerzální Windows)**. Ujistěte se, že vyberete verzi jazyka C# tohoto typu projektu (na rozdíl od jazyka Visual Basic).

1. Výběrem **možnosti Další** zobrazíte obrazovku **Konfigurovat nový projekt.**

   ![Konfigurace nového projektu – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-configure-your-new-project.png)

1. Do pole Název `helloworld` **projektu**zadejte .

1. V **části Umístění**přejděte na složku, do které chcete projekt uložit, a vytvořte ji.

1. Vyberte **Vytvořit,** chcete-li přejít do okna **Nový projekt univerzální platformy Windows.**

   ![Dialogové okno Nový projekt univerzální platformy Windows – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. V **minimální verzi** (druhý rozevírací seznam) zvolte Windows **10 Fall Creators Update (10.0; Sestavení 16299)**, což je minimální požadavek pro sadu SDK řeči.

1. V **cílové verzi** (první rozevírací seznam) zvolte hodnotu shodnou s hodnotou nebo novější, než je hodnota v minimální **verzi**.

1. Vyberte **OK**. Vrátíte se do rozhraní IDE sady Visual Studio s novým projektem vytvořeným a viditelným v podokně **Průzkumník a řešení.**

   ![Projekt helloworld - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-helloworld.png)

Nyní vyberte architekturu cílové platformy. Na panelu nástrojů Sady Visual Studio vyhledejte rozevírací okno **Platformy řešení.** (Pokud ji nevidíte, zvolte **Zobrazit** > **panely nástrojů** > **Standard,** chcete-li zobrazit panel nástrojů obsahující **platformy řešení**.) Pokud používáte 64bitový Systém Windows, zvolte v rozevíracím poli **x64.** 64bitový systém Windows může také spouštět 32bitové aplikace, takže si můžete vybrat **x86,** pokud chcete.

> [!NOTE]
> Sada Speech SDK podporuje pouze procesory kompatibilní s procesory Intel. Procesory ARM nejsou aktuálně podporovány.

### <a name="set-up-audio-capture"></a>Nastavení snímání zvuku

Poté povolte projektu zachytit zvukový vstup:

1. V **Průzkumníku řešení**poklepejte na **package.appxmanifest** a otevřete manifest aplikace balíčku.

1. Vyberte kartu **Možnosti.**

   ![Karta Možnosti, manifest aplikace balíček – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)

1. Vyberte pole pro funkci **Mikrofon.**

1. Na řádku nabídek zvolte **Soubor** > **Uložit Package.appxmanifest,** chcete-li uložit změny.

### <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Nakonec nainstalujte [balíček Speech SDK NuGet](https://aka.ms/csspeech/nuget)a odkazujte na sadu Speech SDK v projektu:

1. V **Průzkumníku řešení**klikněte pravým tlačítkem myši na vaše řešení a zvolte **Spravovat balíčky NuGet pro řešení** a přejděte do okna **NuGet – řešení.**

1. Vyberte **Procházet**.

   ![Snímek obrazovky s dialogovým oknem Spravovat balíčky pro řešení](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. Ve **zdroji balíčků**zvolte **nuget.org**.

1. Do pole **Hledat** `Microsoft.CognitiveServices.Speech`zadejte a poté zvolte tento balíček, jakmile se zobrazí ve výsledcích hledání.

   ![Snímek obrazovky s dialogovým oknem Spravovat balíčky pro řešení](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png)

1. V podokně stavu balíčku vedle výsledků hledání vyberte projekt **Helloworld.**

1. Vyberte **Install** (Nainstalovat).

1. V dialogovém **okně Náhled změn** vyberte **OK**.

1. V dialogovém okně **Přijetí licence** zobrazte licenci a vyberte **Možnost Přijmout**. Instalace balíčku začíná a po dokončení instalace se v podokně **Výstup** zobrazí `Successfully installed 'Microsoft.CognitiveServices.Speech 1.10.0' to helloworld`zpráva podobná následujícímu textu: .
