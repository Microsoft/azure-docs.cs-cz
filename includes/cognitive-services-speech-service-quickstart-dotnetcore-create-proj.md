---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 12/12/2018
ms.author: erhopf
ms.openlocfilehash: 777b2608cf5f326556dfaea307f4f3e9346213f8
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "66482270"
---
1. Spusťte Visual Studio 2019.

1. Ověřte, že je k dispozici sada funkcí **Vývoj multiplatformních aplikací pomocí rozhraní .NET Core**. Výběrem **Možnosti** > **získat nástroje a funkce** na řádku nabídek sady Visual Studio otevřete instalační program sady Visual Studio. Pokud je tato sada funkcí už povolená, zavřete dialogové okno.

   ![Snímek obrazovky instalačního programu sady Visual Studio se zvýrazněnou kartou Sady funkcí](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-net-core-workload.png)

   V opačném případě zaškrtněte políčko **Vývoj multiplatformních aplikací pomocí rozhraní .NET Core** a vyberte **Změnit** v pravém dolním rohu dialogového okna. Instalace nové funkce bude chvíli trvat.

1. Vytvořte novou konzolovou aplikaci v jazyce Visual C# pro rozhraní .NET Core. V dialogovém okně **Nový projekt** rozbalte **Nainstalováno** > **Visual C#** > **.NET Core** v levém podokně. Pak vyberte **Konzolová aplikace (.NET Core)**. Jako název projektu zadejte *helloworld*.

   ![Snímek obrazovky s dialogovým oknem Nový projekt](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Vytvoření konzolové aplikace Visual C# (.NET Core)")

1. Nainstalujte [Balíček NuGet sady Speech SDK](https://aka.ms/csspeech/nuget) a vytvořte na něho odkaz. V Průzkumník řešení klikněte pravým tlačítkem na řešení a vyberte **Spravovat balíčky NuGet pro řešení**.

   ![Snímek obrazovky Průzkumník řešení s zvýrazněnou možností spravovat balíčky NuGet pro řešení](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "Spravovat balíčky NuGet pro řešení")

1. V pravém horním rohu v poli **zdroj balíčku** vyberte **NuGet.org**. Vyhledejte `Microsoft.CognitiveServices.Speech` balíček a nainstalujte ho do projektu **HelloWorld** .

   ![Snímek obrazovky s dialogovým oknem Spravovat balíčky pro řešení](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png "Nainstalovat balíček NuGet")

1. Přijměte zobrazenou licenci, aby se zahájila instalace balíčku NuGet.

   ![Snímek obrazovky s dialogovým oknem Přijetí licence](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "Přijmout licenci")

Až se balíček nainstaluje, v konzole Správce balíčků se zobrazí potvrzení.
