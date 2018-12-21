---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 12/12/2018
ms.author: erhopf
ms.openlocfilehash: 6e49db90aa9e9f933a190425afbafd15e0057fca
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53729488"
---
1. Spusťte Visual Studio 2017.

1. Ověřte, že je k dispozici sada funkcí **Vývoj multiplatformních aplikací pomocí rozhraní .NET Core**. Volbou příkazu **Nástroje** > **Získat nástroje a funkce** na řádku nabídek sady Visual Studio otevřete instalační program sady Visual Studio. Pokud je tato sada funkcí už povolená, zavřete dialogové okno.

   ![Snímek obrazovky instalačního programu sady Visual Studio se zvýrazněnou kartou Sady funkcí](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-net-core-workload.png)

   V opačném případě zaškrtněte políčko **Vývoj multiplatformních aplikací pomocí rozhraní .NET Core** a vyberte **Změnit** v pravém dolním rohu dialogového okna. Instalace nové funkce bude chvíli trvat.

1. Vytvořte novou konzolovou aplikaci v jazyce Visual C# pro rozhraní .NET Core. V dialogovém okně **Nový projekt** rozbalte **Nainstalováno** > **Visual C#** > **.NET Core** v levém podokně. Pak vyberte **Konzolová aplikace (.NET Core)**. Jako název projektu zadejte *helloworld*.

   ![Snímek obrazovky s dialogovým oknem Nový projekt](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Vytvoření konzolové aplikace Visual C# (.NET Core)")

1. Nainstalujte [balíček NuGet sady Speech SDK](https://aka.ms/csspeech/nuget) a vytvořte na něho odkaz. V Průzkumníku řešení klikněte pravým tlačítkem na řešení a vyberte **Spravovat balíčky NuGet pro řešení**.

   ![Snímek obrazovky Průzkumníka řešení se zvýrazněnou možností Spravovat balíčky NuGet pro řešení](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "Správa balíčků NuGet pro řešení")

1. V poli **Zdroj balíčku** v pravém horním rohu vyberte **nuget.org**. Vyhledejte balíček `Microsoft.CognitiveServices.Speech` a nainstalujte ho do projektu **helloworld**.

   ![Snímek obrazovky s dialogovým oknem Spravovat balíčky pro řešení](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png "Instalace balíčku NuGet")

1. Přijměte zobrazenou licenci a zahajte instalaci tohoto balíčku NuGet.

   ![Snímek obrazovky s dialogovým oknem Souhlas s podmínkami licence](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "Přijetí licence")

Až se balíček nainstaluje, v konzole Správce balíčků se zobrazí potvrzení.
