---
title: Jak nastavit počítače pro vývoj pro Media Services s .NET
description: Informace o požadavcích pro Media Services pomocí sady Media Services SDK pro .NET. Také zjistěte, jak vytvořit aplikaci Visual Studio.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: ec2804c7-c656-4fbf-b3e4-3f0f78599a7f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/29/2018
ms.author: juliako
ms.openlocfilehash: 5f111fea8fa1ef9f6e07c01f507e487c992729d5
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233527"
---
# <a name="media-services-development-with-net"></a>Vývoj pro Media Services s .NET
[!INCLUDE [media-services-selector-setup](../../../includes/media-services-selector-setup.md)]

Tento článek popisuje, jak začít s vývojem aplikací Media Services pomocí rozhraní .NET.

**Azure Media Services .NET SDK** knihovny umožňuje programovat proti Media Services pomocí rozhraní .NET. Chcete-li to bylo ještě jednodušší vývoj pomocí .NET, **rozšíření Azure Media Services .NET SDK** knihovny je k dispozici. Tato knihovna obsahuje sadu metod rozšíření a pomocných funkcí, které zjednodušují kódu .NET. Obě knihovny jsou k dispozici prostřednictvím **NuGet** a **Githubu**.

## <a name="prerequisites"></a>Požadavky
* Účet Media Services v novém nebo existujícím předplatném Azure. Přečtěte si článek [postup vytvoření účtu Media Services](media-services-portal-create-account.md).
* Operační systémy: Windows 10, Windows 7, Windows 2008 R2 nebo Windows 8.
* Rozhraní .NET framework 4.5 nebo novější.
* Visual Studio.

## <a name="create-and-configure-a-visual-studio-project"></a>Vytvoření a konfigurace projektu Visual Studia
Tato část ukazuje, jak vytvořit projekt v sadě Visual Studio a připravit prostředí pro vývoj pro Media Services.  V takovém případě je projekt C# Windows konzolovou aplikaci, ale stejný postup instalace je vidět tady platí pro jiné typy projektů, můžete vytvořit pro aplikace služby Media Services (například aplikace modelu Windows Forms nebo webové aplikace ASP.NET).

Tato část ukazuje způsob použití **NuGet** přidejte sadu Media Services .NET SDK rozšíření a dalších závislých knihoven.

Alternativně můžete získat nejnovější součásti Media Services .NET SDK z Githubu ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) nebo [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), sestavte řešení a přidejte odkazy do projektu klienta. Všechny potřebné závislosti získat stažené a rozbalené automaticky.

1. Vytvořte novou konzolovou aplikaci v jazyce C# v sadě Visual Studio. Zadejte **název**, **umístění**, a **název řešení**a potom klikněte na tlačítko OK.
2. Sestavte řešení.
3. Použití **NuGet** nainstalujte a přidejte **rozšíření Azure Media Services .NET SDK** (**windowsazure.mediaservices.extensions**). Při instalaci tohoto balíčku se nainstaluje také **sada SDK služby Media Services pro .NET** a přidá všechny ostatní požadované závislosti.
   
    Ujistěte se, že máte nejnovější verzi aplikace nainstalován nástroj NuGet. Další informace a pokyny k instalaci, naleznete v tématu [NuGet](http://nuget.codeplex.com/).

    1. V Průzkumníku řešení klikněte pravým tlačítkem myši na název projektu a zvolte **spravovat balíčky NuGet**.

    2. Zobrazí se dialogové okno Spravovat balíčky NuGet.

    3. V galerii Online hledání MediaServices rozšíření Azure, zvolte **rozšíření Azure Media Services .NET SDK** (**windowsazure.mediaservices.extensions**) a potom klikněte na tlačítko  **Nainstalujte** tlačítko.
   
    4. Upravit projekt a jsou přidány odkazy na rozšíření Media Services .NET SDK, Media Services .NET SDK a dalších závislých sestavení.
4. Ke zvýšení úrovně čištění vývojového prostředí, zvažte povolení obnovení balíčků NuGet. Další informace najdete v tématu [obnovení balíčků NuGet "](http://docs.nuget.org/consume/package-restore).
5. Přidejte odkaz na **System.Configuration** sestavení. Toto sestavení obsahuje System.Configuration. **ConfigurationManager** třídu, která se používá pro přístup k konfigurační soubory (například App.config).
   
    1. Přidání odkazů pomocí dialogového okna Spravovat odkazy, klikněte pravým tlačítkem na název projektu v Průzkumníku řešení. Potom klikněte na **přidat**, pak klikněte na tlačítko **odkaz...** .
   
    2. Zobrazí se dialogové okno Spravovat odkazy.
    3. V části sestavení rozhraní .NET framework, vyhledejte a vyberte sestavení System.Configuration a stiskněte klávesu **OK**.
6. Otevřete soubor App.config a přidat **appSettings** část do souboru. Nastavte hodnoty, které jsou potřebné pro připojení k rozhraní API služby Media Services. Další informace najdete v tématu [přístup k rozhraní API Azure Media Services pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

    Nastavit hodnoty, které jsou potřebné pro připojení pomocí **instanční objekt služby** metodu ověřování.

        ```csharp
                <configuration>
                ...
                    <appSettings>
                        <add key="AMSAADTenantDomain" value="tenant"/>
                        <add key="AMSRESTAPIEndpoint" value="endpoint"/>
                        <add key="AMSClientId" value="id"/>
                        <add key="AMSClientSecret" value="secret"/>
                    </appSettings>
                </configuration>
        ```

7. Přidat **System.Configuration** odkaz na svůj projekt.
8. Přepsat existující **pomocí** příkazy na začátku souboru Program.cs následujícím kódem:

    ```csharp      
            using System;
            using System.Configuration;
            using System.IO;
            using Microsoft.WindowsAzure.MediaServices.Client;
            using System.Threading;
            using System.Collections.Generic;
            using System.Linq;
    ```

    V tuto chvíli jste připraveni začít vyvíjet aplikace služby Media Services.    

## <a name="example"></a>Příklad:

Tady je malý příklad, který se připojuje k rozhraní API pro AMS a uvádí všechny dostupné procesory médií.

```csharp
        class Program
        {
            // Read values from the App.config file.

            private static readonly string _AADTenantDomain =
                ConfigurationManager.AppSettings["AMSAADTenantDomain"];
            private static readonly string _RESTAPIEndpoint =
                ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
            private static readonly string _AMSClientId =
                ConfigurationManager.AppSettings["AMSClientId"];
            private static readonly string _AMSClientSecret =
                ConfigurationManager.AppSettings["AMSClientSecret"];
        
            private static CloudMediaContext _context = null;
            static void Main(string[] args)
            {
                AzureAdTokenCredentials tokenCredentials = 
                    new AzureAdTokenCredentials(_AADTenantDomain,
                        new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                        AzureEnvironments.AzureCloudEnvironment);

                var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

                _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
        
                // List all available Media Processors
                foreach (var mp in _context.MediaProcessors)
                {
                    Console.WriteLine(mp.Name);
                }
        
            }
 ```

## <a name="next-steps"></a>Další postup

Nyní [se můžete připojit k rozhraní API pro AMS](media-services-use-aad-auth-to-access-ams-api.md) a spustit [vývoj](media-services-dotnet-get-started.md).


## <a name="media-services-learning-paths"></a>Mapy kurzů ke službě Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

