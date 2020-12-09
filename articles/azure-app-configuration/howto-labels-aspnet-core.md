---
title: Použití konfigurace pro jednotlivé prostředí
titleSuffix: Azure App Configuration
description: Pomocí popisků zadejte hodnoty konfigurace pro jednotlivé prostředí.
ms.service: azure-app-configuration
author: AlexandraKemperMS
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 3/12/2020
ms.author: alkemper
ms.openlocfilehash: 84286df063994f3def15079cb9b190550d5bd977
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929612"
---
# <a name="use-labels-to-enable-configurations-for-different-environments"></a>Použití popisků pro povolení konfigurací pro různá prostředí

Mnohé aplikace potřebují pro různá prostředí používat různé konfigurace. Předpokládejme, že aplikace má konfigurační hodnotu definující připojovací řetězec, který se má použít pro svou back-end databázi. Vývojáři aplikací používají jinou databázi než tu, která se používá v produkčním prostředí. Připojovací řetězec databáze, který aplikace používá, se musí změnit, protože aplikace se přesune z vývoje do produkčního prostředí.

V konfiguraci aplikací Azure můžete použít *popisky* k definování různých hodnot pro stejný klíč. Můžete například definovat jeden klíč s různými hodnotami pro vývoj a produkci. Můžete určit, který štítek se má načíst při připojování k konfiguraci aplikace.

Aby bylo možné tuto funkci předvést, upravte webovou aplikaci vytvořenou v [rychlém startu: Vytvoření aplikace ASP.NET Core s využitím konfigurace aplikací Azure](./quickstart-aspnet-core-app.md) pro použití různých nastavení konfigurace pro vývoj a produkci. Než budete pokračovat, dokončete modul pro rychlý Start.

## <a name="specify-a-label-when-adding-a-configuration-value"></a>Zadejte popisek při přidávání konfigurační hodnoty.

V Azure Portal přejít do **Průzkumníka konfigurace** a vyhledejte klíč *TestApp: Settings: fontcolor* , který jste vytvořili v rychlém startu. Vyberte kontextovou nabídku a pak vyberte **Přidat hodnotu**.

> [!div class="mx-imgBorder"]
> ![Položka nabídky Přidat hodnotu](media/labels-add-value.png)

Na obrazovce **Přidat hodnotu** zadejte **červenou** **hodnotu** a **popisek** **vývoje**. Nechejte **typ obsahu** prázdný. Vyberte **Použít**.

## <a name="load-configuration-values-with-a-specified-label"></a>Načíst hodnoty konfigurace se zadaným popiskem

Ve výchozím nastavení načte konfigurace aplikace Azure jenom konfigurační hodnoty bez popisku. Pokud jste definovali popisky pro hodnoty konfigurace, budete chtít zadat popisky, které se mají použít při připojování k konfiguraci aplikace.

V předchozí části jste pro vývojové prostředí vytvořili jinou hodnotu konfigurace. Proměnnou použijete `HostingEnvironment.EnvironmentName` k dynamickému určení prostředí, ve kterém se aplikace aktuálně spouští. Další informace najdete v tématu [použití více prostředí v ASP.NET Core](/aspnet/core/fundamentals/environments).

Načtěte hodnoty konfigurace pomocí popisku odpovídajícího aktuálnímu prostředí tím, že předáte název prostředí do `Select` metody:

```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options
                        .Connect(Environment.GetEnvironmentVariable("AppConfigConnectionString"))
                        // Load configuration values with no label
                        .Select(KeyFilter.Any, LabelFilter.Null)
                        // Override with any configuration values specific to current hosting env
                        .Select(KeyFilter.Any, hostingContext.HostingEnvironment.EnvironmentName)
                );
            })
            .UseStartup<Startup>());
```

> [!IMPORTANT]
> Předchozí fragment kódu načte připojovací řetězec konfigurace aplikace z proměnné prostředí s názvem `AppConfigConnectionString` . Ujistěte se, že je tato proměnná prostředí správně nastavená.

`Select`Metoda je volána dvakrát. Při prvním načtení načte konfigurační hodnoty bez popisku. Pak načte konfigurační hodnoty s popiskem odpovídajícím aktuálnímu prostředí. Tyto hodnoty specifické pro prostředí přepíšou všechny odpovídající hodnoty bez jmenovky. Pro každý klíč nemusíte definovat hodnoty specifické pro konkrétní prostředí. Pokud klíč neobsahuje hodnotu s popiskem odpovídajícím aktuálnímu prostředí, použije se hodnota bez popisku.

## <a name="test-in-different-environments"></a>Testování v různých prostředích

Otevřete `launchSettings.json` soubor v `Properties` adresáři. Vyhledejte `config` položku v části `profiles` . V `environmentVariables` části nastavte `ASPNETCORE_ENVIRONMENT` proměnnou na `Production` .

Pomocí nových hodnot nastavte sestavení a spusťte aplikaci.

```dotnetcli
dotnet build
dotnet run
```

Pomocí webového prohlížeče přejít na `http://localhost:5000` . Všimněte si, že barva písma je černá.

![Webová aplikace spuštěná s konfigurací výroby](media/labels-website-prod.png)

Aktualizujte `launchSettings.json` a nastavte `ASPNETCORE_ENVIRONMENT` proměnnou na `Development` . Spusťte `dotnet run` znovu. 

Všimněte si, že barva písma je teď červená. Je to proto, že aplikace teď používá hodnotu `TestApp:Settings:FontColor` , která má `Development` popisek. Všechny ostatní konfigurační hodnoty zůstanou stejné jako jejich produkční hodnoty.

![Webová aplikace spuštěná s konfigurací vývoje](media/labels-website-dev.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Konfigurace v ASP.NET Core](/aspnet/core/fundamentals/configuration/)
