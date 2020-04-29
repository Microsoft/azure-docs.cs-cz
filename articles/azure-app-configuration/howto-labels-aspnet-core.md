---
title: Použití konfigurace pro jednotlivé prostředí
titleSuffix: Azure App Configuration
description: Použití popisků k zadání hodnot konfigurace podle prostředí
ms.service: azure-app-configuration
author: lisaguthrie
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: d1a3323154fc73b453e041a064cfcd36299b8a08
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80056805"
---
# <a name="use-labels-to-enable-different-configurations-for-different-environments"></a>Použití popisků pro povolení různých konfigurací pro různá prostředí

Mnohé aplikace potřebují pro různá prostředí používat různé konfigurace. Předpokládejme, že aplikace má konfigurační hodnotu definující připojovací řetězec, který se má použít pro svou back-end databázi. Vývojáři aplikace používají jinou databázi než tu, která se používá v produkčním prostředí. Připojovací řetězec databáze používaný aplikací se musí změnit, protože aplikace se přesune z vývoje do produkčního prostředí.

V konfiguraci aplikací Azure můžete použít *popisky* k definování různých hodnot pro stejný klíč. Můžete například definovat jeden klíč s různými hodnotami pro *vývoj* a *produkci*. Můžete určit, které jmenovky mají být načteny při připojování k konfiguraci aplikace.

Aby bylo možné tuto funkci předvést, upraví webovou aplikaci vytvořenou v [rychlém startu: Vytvoření aplikace ASP.NET Core s využitím konfigurace aplikací Azure](./quickstart-aspnet-core-app.md) pro použití různých nastavení konfigurace pro vývoj a produkci. Než budete pokračovat, dokončete prosím rychlý Start.

## <a name="specify-a-label-when-adding-a-configuration-value"></a>Zadejte popisek při přidávání konfigurační hodnoty.

V Azure Portal přejděte do **Průzkumníka konfigurace** a vyhledejte klíč *TestApp: Settings: fontcolor* , který jste vytvořili v rychlém startu. Vyberte kontextovou nabídku a klikněte na **Přidat hodnotu**.

> [!div class="mx-imgBorder"]
> ![Položka nabídky Přidat hodnotu](media/labels-add-value.png)

Na obrazovce **Přidat hodnotu** zadejte **červenou** **hodnotu** a **popisek** **vývoje**. Nechejte **typ obsahu** prázdný. Vyberte **Použít**.

## <a name="loading-configuration-values-with-a-specified-label"></a>Načítají se konfigurační hodnoty se zadaným popiskem.

Ve výchozím nastavení načte konfigurace aplikace Azure jenom konfigurační hodnoty bez popisku. Pokud jste definovali popisky pro hodnoty konfigurace, budete chtít zadat popisek, který se má použít při připojování k konfiguraci aplikace.

V poslední části jste pro *vývojové* prostředí vytvořili jinou hodnotu konfigurace. `HostingEnvironment.EnvironmentName` Proměnnou použijete k dynamickému určení prostředí, ve kterém je aplikace momentálně spuštěná. Další informace najdete v tématu [použití více prostředí v ASP.NET Core](/aspnet/core/fundamentals/environments).

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
> Výše uvedený fragment kódu načte připojovací řetězec konfigurace aplikace z proměnné prostředí s názvem `AppConfigConnectionString`. Ujistěte se, že je tato proměnná prostředí správně nastavená.

`Select` Metoda je volána dvakrát. Při prvním načtení načte konfigurační hodnoty bez popisku. Pak načte konfigurační hodnoty s popiskem odpovídajícím aktuálnímu prostředí. Tyto hodnoty specifické pro prostředí přepíšou všechny odpovídající hodnoty bez jmenovky. Pro každý klíč nemusíte definovat hodnoty specifické pro konkrétní prostředí. Pokud klíč neobsahuje hodnotu s popiskem odpovídajícím aktuálnímu prostředí, je použita hodnota bez popisku.

## <a name="testing-in-different-environments"></a>Testování v různých prostředích

Chcete-li otestovat různé konfigurační hodnoty, otevřete `launchSettings.json` soubor v `Properties` adresáři. Vyhledejte `config` položku v části `profiles`. V `environmentVariables` části nastavte `ASPNETCORE_ENVIRONMENT` proměnnou na. `Production`

Pomocí nových hodnot nastavte sestavení a spusťte aplikaci.

```dotnetcli
dotnet build
dotnet run
```

Pomocí webového prohlížeče přejděte na `http://localhost:5000`. Všimněte si, že barva písma je černá.

![Webová aplikace spuštěná s konfigurací výroby](media/labels-website-prod.png)

Nyní aktualizujte `launchSettings.json` a nastavte `ASPNETCORE_ENVIRONMENT` proměnnou na `Development`. Spusťte `dotnet run` znovu. Všimněte si, že barva písma je teď červená. Je to proto `TestApp:Settings:FontColor` , že aplikace teď používá hodnotu, která má `Development` popisek. Všechny ostatní konfigurační hodnoty zůstanou stejné jako jejich produkční hodnoty.

![Webová aplikace spuštěná s konfigurací vývoje](media/labels-website-dev.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Konfigurace v ASP.NET Core](/aspnet/core/fundamentals/configuration/)
