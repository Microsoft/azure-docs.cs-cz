---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2018
ms.author: wolfma
ms.openlocfilehash: 8ff8e8341b6f39f66c2cc8014d41d3d3a2918d2b
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47454392"
---
1. Spusťte Visual Studio 2017.
 
1. Ujistěte se, že je k dispozici úloha **Desktopové prostředí .NET**. Zvolte **Nástroje** \> **Získat nástroje a funkce** z nabídky sady Visual Studio, aby se otevřel Instalační program pro Visual Studio. Pokud už je tato úloha povolená, zavřete dialog. 

    Jinak zaškrtněte políčko vedle položky **Vývoj desktopových aplikací pomocí .NET** a pak klikněte na tlačítko **Změnit** v pravém dolním rohu dialogu. Instalace nové funkce bude chvíli trvat.

    ![Povolení Vývoje desktopových aplikací pomocí .NET](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Vytvořte novou konzolovou aplikaci v jazyce Visual C#. V dialogovém okně **Nový projekt** v levém podokně rozbalte **Nainstalováno** \> **Visual C#** \> **Windows Desktop** a pak zvolte **Konzolová aplikace (.NET Framework)**. Jako název projektu zadejte *helloworld*.

    ![Vytvoření konzolové aplikace v jazyce Visual C# (.NET Framework)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "Vytvoření konzolové aplikace v jazyce Visual C# (.NET Framework)")

1. Nainstalujte [Balíček NuGet sady Speech SDK](https://aka.ms/csspeech/nuget) a vytvořte na něho odkaz. V Průzkumníku řešení klikněte pravým tlačítkem na řešení a vyberte **Spravovat balíčky NuGet pro řešení**.

    ![Kliknutí pravým tlačítkem na Spravovat balíčky NuGet pro řešení](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "Spravovat balíčky NuGet pro řešení")

1. V poli **Zdroj balíčku** v pravém horním rohu vyberte **nuget.org**. Vyhledejte balíček `Microsoft.CognitiveServices.Speech` a nainstalujte ho do projektu **helloworld**.

    ![Instalace balíčku NuGet Microsoft.CognitiveServices.Speech](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png "Instalace balíčku NuGet")

1. Přijměte zobrazenou licenci, aby se zahájila instalace balíčku NuGet.

    ![Přijetí licence](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "Přijetí licence")

    Až se balíček nainstaluje, v konzole Správce balíčků se zobrazí potvrzení.

1. Pomocí Správce konfigurace vytvořte konfiguraci platformy, která odpovídá architektuře vašeho počítače. Vyberte **Sestavení** > **Správce konfigurace**.

    ![Spuštění Správce konfigurace](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "Spuštění Správce konfigurace")

1. V dialogovém okně **Správce konfigurace** přidejte novou platformu. Z rozevíracího seznamu **Aktivní platforma řešení** vyberte **Nová**.

    ![Přidání nové platformy pod oknem Správce konfigurace](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "Přidání nové platformy pod oknem Správce konfigurace")

1. Pokud používáte 64bitový systém Windows, vytvořte novou konfiguraci platformy s názvem `x64`. Pokud používáte 32bitový systém Windows, vytvořte novou konfiguraci platformy s názvem `x86`.

    ![Přidání nové platformy s názvem x64 na 64bitovém systému Windows](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "Přidání platformy x64")


