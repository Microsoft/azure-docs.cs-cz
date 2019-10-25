---
title: Rychlý Start pro přidání příznaků funkcí do aplikací .NET Framework | Microsoft Docs | Microsoft Docs
description: Rychlý Start pro přidání příznaků funkcí do .NET Framework aplikací a jejich správu v konfiguraci aplikací Azure
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET
ms.workload: tbd
ms.date: 10/21/2019
ms.author: lcozzens
ms.openlocfilehash: cba0a9a003b53eeb102e14c00cb91f344ebbe3d6
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821729"
---
# <a name="quickstart-add-feature-flags-to-a-net-framework-app"></a>Rychlý Start: Přidání příznaků funkcí do aplikace .NET Framework

V tomto rychlém startu zařadíte do aplikace .NET Framework aplikaci Azure App Configuration a vytvoříte ucelenou implementaci správy funkcí. Službu konfigurace aplikací můžete použít k centrálnímu ukládání všech příznaků funkcí a řízení jejich stavů. 

Knihovny pro správu funkcí .NET rozšíří rozhraní s kompletní podporou příznaků funkcí. Tyto knihovny jsou postaveny na systému konfigurace .NET. Bez problémů se integrují s konfigurací aplikací prostřednictvím poskytovatele konfigurace .NET.

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.2](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikace

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-net-console-app"></a>Vytvoření konzolové aplikace .NET

1. Spusťte Visual Studio a vyberte **soubor** > **Nový** **projekt** > .

1. V části **vytvořit nový projekt**, vyfiltrujte typ projektu **konzoly** a klikněte na **Konzolová aplikace (.NET Framework)** . Klikněte na **Další**.

1. V **konfiguraci nového projektu**zadejte název projektu. V části **rozhraní**vyberte **.NET Framework 4.7.1** nebo vyšší. Klikněte na **Vytvořit**.

## <a name="connect-to-an-app-configuration-store"></a>Připojení k úložišti konfigurace aplikace

1. Klikněte pravým tlačítkem na projekt a vyberte **Spravovat balíčky NuGet**. Na kartě **Procházet** vyhledejte a přidejte do svého projektu následující balíčky NuGet. Pokud je nemůžete najít, zaškrtněte políčko **zahrnout předběžné verze** .

    ```
    Microsoft.Extensions.DependencyInjection
    Microsoft.Extensions.Configuration.AzureAppConfiguration
    Microsoft.FeatureManagement
    ```

1. Otevřete *program.cs* a přidejte následující příkazy:

    ```csharp
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    using Microsoft.FeatureManagement;
    ```

1. Aktualizujte metodu `Main` pro připojení k konfiguraci aplikace, zadáním možnosti `UseFeatureFlags`, aby se načetly příznaky funkcí. Pokud je povolen příznak funkce `Beta`, zobrazí se zpráva.

    ```csharp
        static void Main(string[] args)
        {
            IConfigurationRoot configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options => 
                { 
                    options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                           .UseFeatureFlags(); 
                }).Build();
            
            IServiceCollection services = new ServiceCollection(); 
            services.AddSingleton<IConfiguration>(configuration).AddFeatureManagement(); 
            IFeatureManager featureManager = services.BuildServiceProvider().GetRequiredService<IFeatureManager>(); 
            
            if (featureManager.IsEnabled("Beta")) 
            { 
                Console.WriteLine("Welcome to the beta"); 
            }

            Console.WriteLine("Hello World!");
        }
    ```

## <a name="build-and-run-the-app-locally"></a>Místní sestavení a spuštění aplikace

1. Nastavte proměnnou prostředí s názvem **ConnectionString** na připojovací řetězec úložiště konfigurace vaší aplikace. Pokud použijete příkazový řádek Windows, spusťte následující příkaz:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Pokud používáte Windows PowerShell, spusťte následující příkaz:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

1. Pokud chcete, aby se změna projevila, restartujte Visual Studio. 

1. Stisknutím kombinace kláves CTRL + F5 Sestavte a spusťte konzolovou aplikaci.

    ![Aplikace s povoleným příznakem funkce](./media/quickstarts/dotnet-app-feature-flag.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste v konfiguraci aplikace vytvořili příznak funkce a použili ho v aplikaci .NET Framework konzolové aplikace. Pokud se chcete dozvědět, jak dynamicky aktualizovat příznaky funkcí a jiné hodnoty konfigurace bez restartování aplikace, přejděte k dalšímu kurzu.

> [!div class="nextstepaction"]
> [Povolit dynamickou konfiguraci](./enable-dynamic-configuration-dotnet.md)