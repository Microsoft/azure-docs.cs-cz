---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2018
ms.author: wolfma
ms.openlocfilehash: 7a028f65467c517be59be6f0c6d2c45ef9a7685e
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285727"
---
1. Spusťte Visual Studio 2017.

1. V panelu nabídek v sadě Visual Studio vyberte **nástroje > získat nástroje** a ujistěte se, že **vývoj desktopových aplikací .net** úloh je k dispozici. Pokud zatížení není nainstalována, zaškrtněte políčko a potom klikněte na **změnit** spusťte instalaci. Může trvat pár minut a můžete stáhnout a nainstalovat.

   Pokud zaškrtávací políčko vedle **vývoj desktopových aplikací .NET** je vybrána, teď můžete zavřít dialogové okno.

   ![Povolení Vývoje desktopových aplikací pomocí .NET](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. V dalším kroku vytvoříme projektu. V panelu nabídky vyberte **soubor > Nový > projekt**. Když se zobrazí dialogové okno, na levém panelu rozbalit tyto oddíly **nainstalováno > Visual C# > Windows Desktop** a vyberte **Konzolová aplikace (.NET Framework)**. Pojmenujte tento projekt *helloworld*.

    ![Vytvoření konzolové aplikace v jazyce Visual C# (.NET Framework)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "Vytvoření konzolové aplikace v jazyce Visual C# (.NET Framework)")

1. Teď, když je nastaven projekt, musíme nainstalovat [balíček NuGet sady SDK pro řeč](https://aka.ms/csspeech/nuget) odkazovat v kódu. Vyhledejte v Průzkumníku řešení a klikněte pravým tlačítkem na helloworld. V nabídce vyberte **spravovat balíčky NuGet...** .

   ![Kliknutí pravým tlačítkem na Spravovat balíčky NuGet pro řešení](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "Spravovat balíčky NuGet pro řešení")

1. V pravém horním rohu Správce balíčků NuGet, vyhledejte **zdroj balíčku** rozevírací seznam a ujistěte se, že **nuget.org** zaškrtnuto. Vyberte **Procházet** , vyhledejte `Microsoft.CognitiveServices.Speech` balíček a nainstalujte nejnovější stabilní verzi.

   ![Instalace balíčku NuGet Microsoft.CognitiveServices.Speech](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png "Instalace balíčku NuGet")

1. Přijměte všechny smlouvy a licencí ke spuštění instalace.

   ![Přijetí licence](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "Přijetí licence")

    Až se balíček nainstaluje, v konzole Správce balíčků se zobrazí potvrzení.

1. Dalším krokem je vytvoření konfigurace platformy, která odpovídá architektuře počítače, které používáte k sestavení a spuštění aplikace konzoly. V panelu nabídky vyberte **sestavení** > **nástroje Configuration Manager...** .

    ![Spuštění Správce konfigurace](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "Spuštění Správce konfigurace")

1. V **nástroje Configuration Manager** dialogové okno vyhledejte **platformou aktivního řešení** rozevíracího seznamu a vyberte **nový**.

    ![Přidání nové platformy pod oknem Správce konfigurace](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "Přidání nové platformy pod oknem Správce konfigurace")

1. Pokud používáte systém Windows 64-bit, po zobrazení výzvy s **zadejte nebo vyberte novou platformu**, `x64`. Pokud používáte systém Windows 32-bit, vyberte `x86`. Jakmile budete hotovi, klikněte na tlačítko **OK**.

    ![Přidání nové platformy s názvem x64 na 64bitovém systému Windows](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "Přidání platformy x64")
