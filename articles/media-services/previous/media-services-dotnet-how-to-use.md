---
title: Jak nastavit počítač pro vývoj mediálních služeb pomocí rozhraní .NET
description: Informace o předpokladech pro služby Media Services pomocí sady Media Services SDK pro rozhraní .NET. Přečtěte si taky, jak vytvořit aplikaci Visual Studia.
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
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 51fffbd170daecfec6fcea95caa0526e6d881407
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "64724117"
---
# <a name="media-services-development-with-net"></a>Vývoj mediálních služeb pomocí rozhraní .NET 

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Viz také [pokyny k migraci z v2 na v3](../latest/migrate-from-v2-to-v3.md)

Tento článek popisuje, jak začít vyvíjet aplikace služby Media Services pomocí rozhraní .NET.

Knihovna **Sady Azure Media Services .NET SDK** umožňuje programovat proti mediálním službám pomocí rozhraní .NET. Chcete-li ještě usnadnit vývoj s rozhraním .NET, je k dispozici knihovna **rozšíření Azure Media Services .NET SDK Extensions.** Tato knihovna obsahuje sadu rozšiřujících metod a pomocných funkcí, které zjednodušují kód .NET. Obě knihovny jsou k dispozici prostřednictvím **NuGet** a **GitHub**.

## <a name="prerequisites"></a>Požadavky
* Účet Media Services v novém nebo existujícím předplatném Azure. V článku [Jak vytvořit účet mediálních služeb](media-services-portal-create-account.md).
* Operační systémy: Windows 10, Windows 7, Windows 2008 R2 nebo Windows 8.
* Rozhraní .NET Framework 4.5 nebo novější.
* Visual Studio.

## <a name="create-and-configure-a-visual-studio-project"></a>Vytvoření a konfigurace projektu Visual Studia
Tato část ukazuje, jak vytvořit projekt v sadě Visual Studio a nastavit jej pro vývoj mediálních služeb.  V tomto případě je projekt aplikace konzoly Systému Windows jazyka C#, ale stejné kroky instalace uvedené zde platí pro jiné typy projektů, které můžete vytvořit pro aplikace Služby Media (například aplikace Windows Forms nebo ASP.NET webová aplikace).

Tato část ukazuje, jak pomocí **NuGet** přidat rozšíření sady Media Services .NET SDK a další závislé knihovny.

Případně můžete získat nejnovější bity Media Services .NET SDK z GitHubu[(github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) nebo [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), sestavit řešení a přidat odkazy na klientský projekt. Všechny potřebné závislosti se automaticky stáhnou a extrahují.

1. Vytvořte novou konzolovou aplikaci v jazyce C# v sadě Visual Studio. Zadejte **název**, **umístění**a **název řešení**a klepněte na tlačítko OK.
2. Sestavte řešení.
3. Pomocí **nugetu** nainstalujte a přidejte **rozšíření Sady Azure Media Services .NET SDK** **(windowsazure.mediaservices.extensions).** Při instalaci tohoto balíčku se nainstaluje také **sada SDK služby Media Services pro .NET** a přidá všechny ostatní požadované závislosti.
   
    Ujistěte se, že máte nainstalovanou nejnovější verzi NuGet. Další informace a pokyny k instalaci naleznete v tématu [NuGet](https://nuget.codeplex.com/).

    1. V Průzkumníku řešení klikněte pravým tlačítkem myši na název projektu a zvolte **Spravovat balíčky NuGet**.

    2. Zobrazí se dialogové okno Správa balíčků NuGet.

    3. V galerii Online vyhledejte rozšíření Azure MediaServices, zvolte **rozšíření Azure Media Services .NET SDK Extensions** **(windowsazure.mediaservices.extensions)** a klikněte na tlačítko **Instalovat.**
   
    4. Projekt je upraven a jsou přidány odkazy na rozšíření sady Media Services .NET SDK, sady Media Services .NET SDK a další závislá sestavení.
4. Chcete-li podporovat čistší vývojové prostředí, zvažte povolení NuGet package restore. Další informace naleznete v tématu [NuGet Package Restore"](https://docs.nuget.org/consume/package-restore).
5. Přidejte odkaz na sestavení **System.Configuration.** Toto sestavení obsahuje System.Configuration. **Třída ConfigurationManager,** která se používá pro přístup ke konfiguračním souborům (například App.config).
   
    1. Chcete-li přidat odkazy pomocí dialogového okna Spravovat odkazy, klepněte pravým tlačítkem myši na název projektu v Průzkumníku řešení. Potom klepněte na tlačítko **Přidat**a potom klepněte na **odkaz...**.
   
    2. Zobrazí se dialogové okno Spravovat odkazy.
    3. V části sestavení rozhraní .NET framework vyhledejte a vyberte sestavení System.Configuration a stiskněte **ok**.
6. Otevřete soubor App.config a přidejte do něj oddíl **AppSettings.** Nastavte hodnoty potřebné pro připojení k rozhraní API mediálních služeb. Další informace najdete [v tématu Přístup k rozhraní API Azure Media Services s ověřováním Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

    Nastavte hodnoty, které jsou potřebné pro připojení pomocí metody ověřování **instančního objektu.**

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

7. Přidejte odkaz **System.Configuration** do projektu.
8. Přepište existující **příkazy using** na začátku Program.cs souboru pomocí následujícího kódu:

    ```csharp      
            using System;
            using System.Configuration;
            using System.IO;
            using Microsoft.WindowsAzure.MediaServices.Client;
            using System.Threading;
            using System.Collections.Generic;
            using System.Linq;
    ```

    V tomto okamžiku jste připraveni začít vyvíjet aplikaci mediálních služeb.    

## <a name="example"></a>Příklad

Zde je malý příklad, který se připojuje k rozhraní API AMS a obsahuje seznam všech dostupných mediálních procesorů.

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

## <a name="next-steps"></a>Další kroky

Nyní [se můžete připojit k Rozhraní API AMS](media-services-use-aad-auth-to-access-ams-api.md) a začít [vyvíjet](media-services-dotnet-get-started.md).


## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

