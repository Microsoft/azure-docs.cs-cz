---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 2/20/2019
ms.author: erhopf
ms.openlocfilehash: 27fcf32a9a268488da318567d3edc55d23bd8967
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66482411"
---
1. Start Visual Studio 2019.

1. Ověřte, že je k dispozici sada funkcí **Vývoj desktopových aplikací pomocí C++** . Volbou příkazu **Nástroje** > **Získat nástroje a funkce** na řádku nabídek sady Visual Studio otevřete instalační program sady Visual Studio. Pokud je tato sada funkcí už povolená, přeskočte na další krok.

    ![Snímek obrazovky s kartou Sady funkcí pro Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

    V opačném případě zaškrtněte políčko **Vývoj desktopových aplikací pomocí C++** .

1. Ověřte, že je dostupná komponenta **Správce balíčků NuGet**. Přepněte na kartu **Jednotlivé komponenty** dialogového okna instalačního programu sady Visual Studio a vyberte **Správce balíčků NuGet**, pokud ještě není povolený.

      ![Snímek obrazovky s kartou Jednotlivé komponenty sady Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-nuget-package-manager.png)

1. Pokud potřebujete povolit sadu funkcí C++ nebo NuGet, vyberte **Změnit** (v levém dolním rohu dialogového okna). Instalace nových funkcí chvíli trvá. Pokud už jsou obě funkce povolené, zavřete toto dialogové okno.

1. Vytvořte novou konzolovou aplikaci pro plochu Windows v jazyce Visual C++. Nejdříve v nabídce vyberte **Soubor** > **Nový** > **Projekt**. V dialogovém okně **Nový projekt** rozbalte **Nainstalováno** > **Visual C++**  > **Desktop Windows** v levém podokně. Pak vyberte **Konzolová aplikace pro Windows**. Jako název projektu zadejte *helloworld*.

    ![Snímek obrazovky s dialogovým oknem Nový projekt](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. Pokud používáte 64bitovou verzi Windows, můžete pomocí rozevírací nabídky na panelu nástrojů sady Visual Studio přepnout platformu sestavení na `x64`. (64bitové verze Windows dokážou spouštět 32bitové aplikace, takže to není povinné.)

    ![Snímek obrazovky panelu nástrojů sady Visual Studio se zvýrazněnou možností x64](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-02-switch-to-x64.png)

1. V Průzkumníku řešení klikněte pravým tlačítkem na řešení a zvolte **Spravovat balíčky NuGet pro řešení**.

    ![Snímek obrazovky s Průzkumníkem řešení a zvýrazněnou možností Spravovat balíčky NuGet pro řešení](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. V poli **Zdroj balíčku** v pravém horním rohu vyberte **nuget.org**. Vyhledejte balíček `Microsoft.CognitiveServices.Speech` a nainstalujte ho do projektu **helloworld**.

    ![Snímek obrazovky s dialogovým oknem Spravovat balíčky pro řešení](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

    > [!NOTE]
    > Aktuální verze sady Cognitive Services Speech SDK je `1.5.0`.

1. Přijměte zobrazenou licenci a zahajte instalaci tohoto balíčku NuGet.

    ![Snímek obrazovky s dialogovým oknem Přijetí licence](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-05-nuget-license.png)

Po instalaci balíčku se v konzole Správce balíčků zobrazí potvrzení.
