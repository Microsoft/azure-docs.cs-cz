---
title: Použití konfigurace podle prostředí
titleSuffix: Azure App Configuration
description: Použití popisků k zadání hodnot konfigurace pro prostředí
ms.service: azure-app-configuration
author: lisaguthrie
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: d1a3323154fc73b453e041a064cfcd36299b8a08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80056805"
---
# <a name="use-labels-to-enable-different-configurations-for-different-environments"></a>Použití popisků k povolení různých konfigurací pro různá prostředí

Mnoho aplikací musí používat různé konfigurace pro různá prostředí. Předpokládejme, že aplikace má hodnotu konfigurace, která definuje připojovací řetězec pro jeho back-end databáze. Vývojáři aplikace používají jinou databázi, než je databáze používaná v produkčním prostředí. Připojovací řetězec databáze používaný aplikací se musí změnit, jak se aplikace přesouvá z vývoje do produkčního prostředí.

V konfiguraci aplikace Azure můžete pomocí *popisků* definovat různé hodnoty pro stejný klíč. Můžete například definovat jeden klíč s různými hodnotami pro *vývoj* a *výrobu*. Můžete určit, které popisky se mají načíst při připojování ke konfiguraci aplikace.

Abychom tuto funkci předvedli, upravíme webovou aplikaci vytvořenou na [úvodním panelu: Vytvoříme ASP.NET základní aplikaci s Azure App Configuration,](./quickstart-aspnet-core-app.md) aby se pro vývoj vs. produkční prostředí používalo různá nastavení konfigurace. Než budete pokračovat, dokončete rychlý start.

## <a name="specify-a-label-when-adding-a-configuration-value"></a>Určení popisku při přidávání hodnoty konfigurace

Na webu Azure Portal přejděte do **Průzkumníka konfigurace** a vyhledejte klíč *TestApp:Settings:FontColor,* který jste vytvořili v rychlém startu. Vyberte jeho místní nabídku a klepněte na tlačítko **Přidat hodnotu**.

> [!div class="mx-imgBorder"]
> ![Přidat položku nabídky Přidaná hodnota](media/labels-add-value.png)

Na obrazovce **Přidaná hodnota** zadejte **hodnotu** **červené** a **popisek** **vývoje**. Ponechte **typ obsahu** prázdný. Vyberte **Použít**.

## <a name="loading-configuration-values-with-a-specified-label"></a>Načítání konfiguračních hodnot se zadaným popiskem

Ve výchozím nastavení azure app configuration načte pouze hodnoty konfigurace bez popisku. Pokud jste definovali popisky pro hodnoty konfigurace, budete chtít zadat popisky, které se mají použít při připojování ke konfiguraci aplikace.

V poslední části jste vytvořili jinou hodnotu konfigurace pro *vývojové* prostředí. Proměnnou `HostingEnvironment.EnvironmentName` můžete použít k dynamickému určení prostředí, ve kterém aplikace aktuálně běží. Další informace najdete [v tématu Použití více prostředí v ASP.NET jádra](/aspnet/core/fundamentals/environments).

Načtěte hodnoty konfigurace s popiskem odpovídajícím aktuálnímu `Select` prostředí předáním názvu prostředí do metody:

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
> Výše uvedený fragment kódu načte připojovací řetězec `AppConfigConnectionString`Konfigurace aplikace z proměnné prostředí s názvem . Ujistěte se, že tato proměnná prostředí je nastavena správně.

Metoda `Select` se nazývá dvakrát. Při prvním načtení hodnot konfigurace bez popisku. Poté načte hodnoty konfigurace s popiskem odpovídajícím aktuálnímu prostředí. Tyto hodnoty specifické pro prostředí přepsat všechny odpovídající hodnoty bez popisku. Není nutné definovat hodnoty specifické pro prostředí pro každý klíč. Pokud klíč nemá hodnotu s popiskem odpovídajícím aktuálnímu prostředí, použije se hodnota bez popisku.

## <a name="testing-in-different-environments"></a>Testování v různých prostředích

Chcete-li otestovat různé `launchSettings.json` hodnoty konfigurace, otevřete soubor pod adresářem. `Properties` Vyhledejte `config` položku pod `profiles`. V `environmentVariables` části nastavte `ASPNETCORE_ENVIRONMENT` proměnnou `Production`na .

S nastavenými novými hodnotami vytvořte a spusťte aplikaci.

```dotnetcli
dotnet build
dotnet run
```

Pomocí webového prohlížeče `http://localhost:5000`přejděte na aplikaci . Všimněte si, že barva písma je černá.

![Webová aplikace spuštěná s konfigurací produkčního prostředí](media/labels-website-prod.png)

Nyní `launchSettings.json` aktualizujte `ASPNETCORE_ENVIRONMENT` a `Development`nastavte proměnnou na . Spusťte `dotnet run` znovu. Všimněte si, že barva písma je nyní červená. Důvodem je, že aplikace `TestApp:Settings:FontColor` nyní používá `Development` hodnotu, která má popisek. Všechny ostatní hodnoty konfigurace zůstávají stejné jako jejich výrobní hodnoty.

![Webová aplikace spuštěná s konfigurací vývoje](media/labels-website-dev.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Konfigurace v ASP.NET jádru](/aspnet/core/fundamentals/configuration/)
