---
title: Jak nastavit počítač pro vývoj Media Services pomocí .NET
description: Přečtěte si o požadavcích pro Media Services používání sady Media Services SDK pro .NET. Naučíte se také, jak vytvořit aplikaci Visual Studio.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ec2804c7-c656-4fbf-b3e4-3f0f78599a7f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: a2a9ecbcafa89b4981fe403d116ac0288e60b6b6
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103013886"
---
# <a name="media-services-development-with-net"></a>Media Services vývoj pomocí .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Do Media Services v2 se nepřidávají žádné nové funkce. <br/>Podívejte se na nejnovější verzi [Media Services V3](../latest/index.yml). Podívejte se taky na [pokyny k migraci z v2 na V3](../latest/migrate-v-2-v-3-migration-introduction.md) .

Tento článek popisuje, jak začít s vývojem Media Services aplikací pomocí rozhraní .NET.

Knihovna **Azure Media Services .NET SDK** umožňuje programovat v rámci Media Services pomocí .NET. Aby bylo možné ještě snazší vývoj pomocí rozhraní .NET, je k dispozici knihovna **rozšíření Azure Media Services .NET SDK** . Tato knihovna obsahuje sadu rozšiřujících metod a pomocných funkcí, které zjednodušují kód .NET. Obě knihovny jsou k dispozici prostřednictvím **NuGet** a **GitHubu**.

## <a name="prerequisites"></a>Požadavky
* Účet Media Services v novém nebo existujícím předplatném Azure. Přečtěte si článek [jak vytvořit účet Media Services](media-services-portal-create-account.md).
* Operační systémy: Windows 10, Windows 7, Windows 2008 R2 nebo Windows 8.
* .NET Framework 4,5 nebo novější.
* Visual Studio

## <a name="create-and-configure-a-visual-studio-project"></a>Vytvoření a konfigurace projektu Visual Studia
V této části se dozvíte, jak vytvořit projekt v sadě Visual Studio a nastavit jej pro Media Services vývoj.  V tomto případě se jedná o konzolovou aplikaci systému Windows v C#, ale stejný postup nastavení se vztahuje na další typy projektů, které můžete vytvořit pro Media Services aplikace (například model Windows Forms aplikace nebo webová aplikace ASP.NET).

V této části se dozvíte, jak pomocí **NuGet** přidat Media Services rozšíření sady .NET SDK a dalších závislých knihoven.

Alternativně můžete získat nejnovější Media Services sady .NET SDK z GitHubu ([GitHub.com/Azure/Azure-SDK-for-Media-Services](https://github.com/Azure/azure-sdk-for-media-services) nebo [GitHub.com/Azure/Azure-SDK-for-Media-Services-Extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), sestavit řešení a přidat odkazy na klientský projekt. Všechny nezbytné závislosti se stáhnou a automaticky extrahují.

1. Vytvořte novou konzolovou aplikaci v jazyce C# v sadě Visual Studio. Zadejte **název**, **umístění** a **název řešení** a potom klikněte na tlačítko OK.
2. Sestavte řešení.
3. Použijte **NuGet** k instalaci a přidání **rozšíření Azure Media Services .NET SDK** (**windowsazure. MediaServices. Extensions**). Při instalaci tohoto balíčku se nainstaluje také **sada SDK služby Media Services pro .NET** a přidá všechny ostatní požadované závislosti.
   
    Ujistěte se, že máte nainstalovanou nejnovější verzi NuGet. Další informace a pokyny k instalaci najdete v tématu [NuGet](https://nuget.codeplex.com/).

    1. V Průzkumník řešení klikněte pravým tlačítkem myši na název projektu a vyberte možnost **Spravovat balíčky NuGet**.

    2. Zobrazí se dialogové okno Správa balíčků NuGet.

    3. V online galerii vyhledejte rozšíření Azure MediaServices, zvolte **Azure Media Services rozšíření .NET SDK** (**windowsazure. MediaServices. Extensions**) a pak klikněte na tlačítko **nainstalovat** .
   
    4. Projekt je upraven a odkazuje na rozšíření Media Services .NET SDK, Media Services .NET SDK a další závislá sestavení jsou přidána.
4. Pokud chcete zvýšit úroveň čisticího vývojového prostředí, zvažte možnost povolit obnovení balíčku NuGet. Další informace najdete v tématu [obnovení balíčku NuGet](https://docs.nuget.org/consume/package-restore).
5. Přidejte odkaz na **System.Configuration** sestavení. Toto sestavení obsahuje System.Configuration. Třída **ConfigurationManager** , která se používá pro přístup ke konfiguračním souborům (například App.config).
   
    1. Chcete-li přidat odkazy pomocí dialogu spravovat odkazy, klikněte pravým tlačítkem myši na název projektu v Průzkumník řešení. Pak klikněte na **Přidat** a pak na **odkaz...**.
   
    2. Zobrazí se dialogové okno spravovat odkazy.
    3. V části sestavení rozhraní .NET Framework vyhledejte a vyberte System.Configuration sestavení a stiskněte **OK**.
6. Otevřete soubor App.config a přidejte do souboru oddíl **appSettings** . Nastavte hodnoty, které jsou potřeba pro připojení k rozhraní Media Services API. Další informace najdete v tématu [přístup k rozhraní Azure Media Services API pomocí ověřování Azure AD](media-services-use-aad-auth-to-access-ams-api.md). 

    Nastavte hodnoty, které jsou potřeba pro připojení pomocí metody ověřování **instančního objektu** .

    ```xml
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

7. Přidejte do projektu odkaz **System.Configuration** .
8. Přepsat existující příkazy **using** na začátku souboru program.cs pomocí následujícího kódu:

    ```csharp      
    using System;
    using System.Configuration;
    using System.IO;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Threading;
    using System.Collections.Generic;
    using System.Linq;
    ```

    V tuto chvíli jste připraveni začít s vývojem aplikace Media Services.    

## <a name="example"></a>Příklad

Tady je malý příklad, který se připojuje k rozhraní API AMS a uvádí všechny dostupné procesory médií.

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

Nyní se [můžete připojit k rozhraní API AMS](media-services-use-aad-auth-to-access-ams-api.md) a začít [vyvíjet](media-services-dotnet-get-started.md).


## <a name="media-services-learning-paths"></a>Mapy kurzů k Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
