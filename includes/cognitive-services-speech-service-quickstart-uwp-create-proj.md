---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 12/13/2018
ms.author: erhopf
ms.openlocfilehash: 79fbe2db1ec9758d1e15ba7d89363429415918c7
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53729471"
---
1. Spusťte Visual Studio 2017.

1. Ověřte, že je k dispozici sada funkcí **Vývoj pro Univerzální platformu Windows**. Volbou příkazu **Nástroje** > **Získat nástroje a funkce** na řádku nabídek sady Visual Studio otevřete instalační program sady Visual Studio. Pokud je tato sada funkcí už povolená, zavřete dialogové okno.

    ![Snímek obrazovky instalačního programu sady Visual Studio se zvýrazněnou kartou Sady funkcí](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

    V opačném případě zaškrtněte políčko **Vývoj multiplatformních aplikací pomocí rozhraní .NET** a vyberte **Změnit** v pravém dolním rohu dialogového okna. Instalace nové funkce chvíli trvá.

1. Vytvořte prázdnou univerzální aplikaci pro Windows v jazyce Visual C#. Nejdříve v nabídce vyberte **Soubor** > **Nový** > **Projekt**. V dialogovém okně **Nový projekt** rozbalte **Nainstalováno** > **Visual C#** > **Univerzální aplikace pro Windows**  v levém podokně. Pak vyberte **Prázdná aplikace (univerzální pro Windows)**. Jako název projektu zadejte *helloworld*.

    ![Snímek obrazovky s dialogovým oknem Nový projekt](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. Sada Speech SDK vyžaduje, aby aplikace byla vytvořená pro Windows 10 Fall Creators Update nebo novější. V zobrazeném okně **Nový projekt Univerzální platformy Windows** zvolte v poli **Minimální verze** možnost **Windows 10 Fall Creators Update (10.0; build 16299)**. V poli **Cílová verze** vyberte tuto nebo novější verzi a pak klikněte na **OK**.

    ![Snímek obrazovky s oknem Nový projekt univerzální platformy Windows](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. Pokud používáte 64bitovou verzi Windows, můžete pomocí rozevírací nabídky na panelu nástrojů sady Visual Studio přepnout platformu sestavení na `x64`. (64bitová verze Windows dokáže spouštět 32bitové aplikace, takže pokud chcete, můžete ponechat nastavení `x86`.)

   ![Snímek obrazovky panelu nástrojů sady Visual Studio se zvýrazněnou možností x64](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > Sadou SDK pro řeč podporuje pouze procesory kompatibilní s verzí Intel. Procesory ARM nejsou momentálně podporované.

1. Nainstalujte [balíček NuGet sady Speech SDK](https://aka.ms/csspeech/nuget) a vytvořte na něho odkaz. V Průzkumníku řešení klikněte pravým tlačítkem na řešení a vyberte **Spravovat balíčky NuGet pro řešení**.

    ![Snímek obrazovky s Průzkumníkem řešení a zvýrazněnou možností Spravovat balíčky NuGet pro řešení](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. V poli **Zdroj balíčku** v pravém horním rohu vyberte **nuget.org**. Vyhledejte balíček `Microsoft.CognitiveServices.Speech` a nainstalujte ho do projektu **helloworld**.

    ![Snímek obrazovky s dialogovým oknem Spravovat balíčky pro řešení](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png "Instalace balíčku NuGet")

1. Přijměte zobrazenou licenci a zahajte instalaci tohoto balíčku NuGet.

    ![Snímek obrazovky s dialogovým oknem Souhlas s podmínkami licence](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-06-nuget-license.png "Přijetí licence")

1. V konzole Správce balíčků se zobrazí následující řádek výstupu.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 1.2.0' to helloworld
   ```

1. Protože aplikace používá mikrofon pro hlasový vstup, přidejte do projektu schopnost **Mikrofon**. V Průzkumníku řešení dvakrát klikněte na **Package.appxmanifest** a upravte manifest aplikace. Přepněte na kartu **Schopnosti**, zaškrtněte políčko u schopnosti **Mikrofon** a uložte provedené změny.

   ![Snímek obrazovky s manifestem aplikace sady Visual Studio a zvýrazněnými volbami Schopnosti a Mikrofon](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)
