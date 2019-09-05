---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/21/2019
ms.author: erhopf
ms.openlocfilehash: 99a7dec6936b86af4ab9b80d266cd886dae66d12
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70381862"
---
Chcete-li vytvořit projekt sady Visual C++ Studio pro vývoj aplikací pro stolní počítače, je třeba nastavit možnosti vývoje sady Visual Studio, vytvořit projekt, vybrat cílovou architekturu a nainstalovat sadu Speech SDK. 

### <a name="set-up-visual-studio-development-options"></a>Nastavení možností vývoje pro Visual Studio

Pokud chcete začít, ujistěte se, že jste správně nastavili v C++ sadě Visual Studio pro vývoj desktopových aplikací:

1. Otevřete Visual Studio 2019 pro zobrazení okna **Start** .

   ![Spustit okno – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-start-window.png) 

1. Pokud chcete přejít na integrované vývojové prostředí (IDE) sady Visual Studio, vyberte **pokračovat bez kódu** .

1. V řádku nabídek sady Visual Studio vyberte **nástroje** > **získat nástroje a funkce** . otevře se instalační program pro Visual Studio a zobrazí se dialogové okno **změny** .

   ![Karta úlohy, změny dialogového okna, Instalační program pro Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

1. Na kartě **úlohy** v části **Windows**Najděte **desktopový vývoj s C++**  využitím úlohy. Pokud není zaškrtnuté políčko vedle této úlohy, vyberte ji.

1. Na kartě **jednotlivé součásti** vyhledejte zaškrtávací políčko **Správce balíčků NuGet** . Pokud políčko ještě není vybrané, vyberte ho.

1. Vyberte tlačítko v rohu označené buď **Zavřít** , nebo **Upravit**. (Název tlačítka se liší v závislosti na tom, zda jste vybrali některé funkce pro instalaci.) Pokud vyberete možnost **Upravit**, začne instalace, což může chvíli trvat.

1. Zavřete Instalační program pro Visual Studio.

### <a name="create-the-project-and-select-the-target-architecture"></a>Vytvořit projekt a vybrat cílovou architekturu

Potom vytvořte projekt:

1. V řádku nabídek sady Visual Studio vyberte **soubor** > **Nový** > **projekt** , aby se zobrazilo okno **vytvořit nový projekt** .

   ![Vytvoření nového projektu C++ – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. Najděte a vyberte **konzolovou aplikaci**. Ujistěte se, že jste vybrali C++ verzi tohoto typu projektu (na rozdíl od C# nebo Visual Basic).

1. Kliknutím na tlačítko **Další** zobrazíte obrazovku **Konfigurovat nový projekt** .

   ![Konfigurace nového projektu C++ – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-configure-your-new-project.png)

1. Do **název projektu**zadejte `helloworld`.

1. V části **umístění**přejděte na a vyberte nebo vytvořte složku, do které chcete projekt uložit.

Teď vyberte cílovou architekturu platformy. Na panelu nástrojů sady Visual Studio Najděte rozevírací seznam **platformy řešení** . (Pokud ji nevidíte, vyberte možnost **Zobrazit** > **panely nástrojů** > **standardní** a zobrazte panel nástrojů obsahující **platformy řešení**.) Pokud používáte 64-bit Windows, v rozevíracím seznamu vyberte **x64** . 64-bitová okna můžou spouštět i 32 aplikace, takže pokud chcete, můžete vybrat možnost **x86** .

### <a name="install-the-speech-sdk"></a>Instalace sady Speech SDK

Nakonec nainstalujte [balíček NuGet sady Speech SDK](https://aka.ms/csspeech/nuget)a odkažte na sadu Speech SDK v projektu:

1. V **Průzkumník řešení**klikněte pravým tlačítkem myši na vaše řešení a vyberte **Spravovat balíčky NuGet pro řešení** , abyste přešli do okna **NuGet – řešení** .

1. Vyberte **Procházet**.

   ![NuGet – karta řešení, Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. Ve **zdroji balíčku**vyberte možnost **NuGet.org**.

1. Do **vyhledávacího** pole zadejte `Microsoft.CognitiveServices.Speech`a po zobrazení ve výsledcích hledání zvolte tento balíček.

   ![Instalace balíčku Microsoft. Cognitiveservices Account C++ . Speech – Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

1. V podokně stav balíčku vedle výsledků hledání vyberte svůj projekt **HelloWorld** .

1. Vyberte **Install** (Nainstalovat).

1. V dialogovém okně **Náhled změn** vyberte **OK**.

1. V dialogovém okně **přijetí licence** si prohlédněte licenci a pak vyberte **Souhlasím**. Spustí se instalace balíčku. po dokončení instalace se v podokně **výstup** zobrazí zpráva podobná následujícímu textu: `Successfully installed 'Microsoft.CognitiveServices.Speech 1.6.0' to helloworld`. 
