---
title: Úvodní příručka pro přidávání příznaků funkcí do aplikací rozhraní .NET Framework | Dokumenty microsoftu | Dokumenty společnosti Microsoft
description: Rychlý start pro přidávání příznaků funkcí do aplikací rozhraní .NET Framework a jejich správu v konfiguraci aplikací Azure
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
ms.openlocfilehash: 5ea9749c07aadc7037e753160e9b053992bebae2
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77619316"
---
# <a name="quickstart-add-feature-flags-to-a-net-framework-app"></a>Úvodní příručka: Přidání příznaků funkcí do aplikace rozhraní .NET Framework

V tomto rychlém startu začlenit Azure App Configuration do aplikace .NET Framework vytvořit komplexní implementaci správy funkcí. Službu Konfigurace aplikace můžete použít k centrálnímu ukládání všech příznaků funkcí a řízení jejich stavů. 

Knihovny správy funkcí .NET rozšiřují architekturu o komplexní podporu příznaku funkce. Tyto knihovny jsou postaveny na konfiguračním systému .NET. Hladce se integrují s konfigurací aplikací prostřednictvím zprostředkovatele konfigurace rozhraní .NET.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [Rozhraní .NET Framework 4.8](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikací

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Vyberte **Správce** > funkcí **+Přidat,** `Beta`chcete-li přidat příznak prvku s názvem .

    > [!div class="mx-imgBorder"]
    > ![Povolit příznak funkce s názvem Beta](media/add-beta-feature-flag.png)

    Prozatím `label` nenechte být nedefinovaní.

## <a name="create-a-net-console-app"></a>Vytvoření aplikace konzoly ROZHRANÍ .NET

1. Spusťte Visual Studio a vyberte **Soubor** > **nový** > **projekt**.

1. V **části Vytvořit nový projekt**vyfiltrujte typ projektu **konzoly** a klikněte na **Konzolová aplikace (.NET Framework).** Klikněte na **Další**.

1. V **okně Konfigurace nového projektu**zadejte název projektu. V části **Framework**vyberte **rozhraní .NET Framework 4.8** nebo vyšší. Klikněte na **Vytvořit**.

## <a name="connect-to-an-app-configuration-store"></a>Připojení k úložišti konfigurace aplikací

1. Klikněte pravým tlačítkem myši na projekt a vyberte **spravovat balíčky NuGet**. Na **kartě Procházet** vyhledejte a přidejte do projektu následující balíčky NuGet. Pokud je nemůžete najít, zaškrtněte políčko **Zahrnout předběžnou verzi.**

    ```
    Microsoft.Extensions.DependencyInjection
    Microsoft.Extensions.Configuration.AzureAppConfiguration
    Microsoft.FeatureManagement
    ```

1. Otevřete *Program.cs* a přidejte následující příkazy:

    ```csharp
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    using Microsoft.FeatureManagement;
    ```

1. Aktualizujte `Main` metodu pro připojení ke `UseFeatureFlags` konfiguraci aplikace a zadejte možnost tak, aby byly načteny příznaky funkce. Potom zobrazte `Beta` zprávu, pokud je povolen příznak funkce.

    ```csharp
        public static async Task Main(string[] args)
        {         
            IConfigurationRoot configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options =>
                {
                    options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                           .UseFeatureFlags();
                }).Build();

            IServiceCollection services = new ServiceCollection();

            services.AddSingleton<IConfiguration>(configuration).AddFeatureManagement();

            using (ServiceProvider serviceProvider = services.BuildServiceProvider())
            {
                IFeatureManager featureManager = serviceProvider.GetRequiredService<IFeatureManager>();

                if (await featureManager.IsEnabledAsync("Beta"))
                {
                    Console.WriteLine("Welcome to the beta!");
                }
            }

            Console.WriteLine("Hello World!");
        }
    ```

## <a name="build-and-run-the-app-locally"></a>Sestavení a spuštění aplikace místně

1. Nastavte proměnnou prostředí s názvem **ConnectionString** na připojovací řetězec úložiště konfigurace aplikace. Pokud používáte příkazový řádek systému Windows, spusťte následující příkaz:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Pokud používáte prostředí Windows PowerShell, spusťte následující příkaz:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

1. Restartujte visual studio, aby se změna projevila. 

1. Stisknutím kláves Ctrl + F5 vytvořte a spusťte konzolovou aplikaci.

    ![Aplikace s povoleným příznakem funkce](./media/quickstarts/dotnet-app-feature-flag.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili příznak funkce v konfiguraci aplikace a použili ho s konzolovou aplikací rozhraní .NET Framework. Chcete-li se dozvědět, jak dynamicky aktualizovat příznaky funkcí a další hodnoty konfigurace bez restartování aplikace, pokračujte dalším kurzem.

> [!div class="nextstepaction"]
> [Povolit dynamickou konfiguraci](./enable-dynamic-configuration-dotnet.md)
