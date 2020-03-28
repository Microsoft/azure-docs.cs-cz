---
title: Integrace konfigurace aplikací Azure pomocí kanálu průběžné integrace a doručování
description: Naučte se implementovat průběžnou integraci a doručování pomocí konfigurace aplikací Azure
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 01/30/2020
ms.author: lcozzens
ms.openlocfilehash: c744557471a9b37bd620bb9195bdb709c24649ab
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77047288"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integrace s kanálem CI/CD

Tento článek vysvětluje, jak používat data z Azure App Configuration v systému průběžné integrace a průběžného nasazování.

## <a name="use-app-configuration-in-your-azure-devops-pipeline"></a>Použití konfigurace aplikace v kanálu Azure DevOps

Pokud máte kanál Azure DevOps, můžete načíst hodnoty klíčů z konfigurace aplikace a nastavit je jako proměnné úlohy. Rozšíření [Azure App Configuration DevOps](https://go.microsoft.com/fwlink/?linkid=2091063) je doplňkový modul, který poskytuje tuto funkci. Postupujte podle jeho pokynů k použití rozšíření v pořadí sestavení nebo vydání úloh.

## <a name="deploy-app-configuration-data-with-your-application"></a>Nasazení dat konfigurace aplikace s vaší aplikací

Vaše aplikace může selhat ke spuštění, pokud závisí na konfiguraci aplikace Azure a nemůže k ní dosáhnout. Vylepšete odolnost aplikace zabalením konfiguračních dat do souboru, který je nasazen s aplikací a načten místně při spuštění aplikace. Tento přístup zaručuje, že aplikace má výchozí hodnoty nastavení při spuštění. Tyto hodnoty jsou přepsány všechny novější změny v úložišti konfigurace aplikací, když je k dispozici.

Pomocí funkce [Export](./howto-import-export-data.md#export-data) konfigurace aplikace Azure můžete automatizovat proces načítání aktuálních konfiguračních dat jako jednoho souboru. Tento soubor pak můžete vložit do kroku sestavení nebo nasazení v kanálu průběžné integrace a průběžného nasazování (CI/CD).

Následující příklad ukazuje, jak zahrnout data konfigurace aplikace jako krok sestavení pro webovou aplikaci zavedenou v rychlých startech. Než budete pokračovat, nejprve [dokončete vytvoření aplikace ASP.NET Jádro pomocí konfigurace aplikace.](./quickstart-aspnet-core-app.md)

Můžete použít libovolný editor kódu k tomu kroky v tomto kurzu. [Visual Studio Code](https://code.visualstudio.com/) je vynikající volba dostupná na platformách Windows, macOS a Linux.

### <a name="prerequisites"></a>Požadavky

Pokud vytváříte místně, stáhněte a nainstalujte [rozhraní příkazového příkazu Azure,](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) pokud jste tak ještě neučinili.

Chcete-li provést cloudové sestavení, s Azure DevOps například ujistěte se, že [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) je nainstalovaný ve vašem systému sestavení.

### <a name="export-an-app-configuration-store"></a>Export úložiště konfigurace aplikací

1. Otevřete soubor *.csproj* a přidejte následující skript:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -d file --path $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```
1. Otevřete *Program.cs*a `CreateWebHostBuilder` aktualizujte metodu pro použití exportovaného souboru JSON voláním `config.AddJsonFile()` metody.  Přidejte `System.Reflection` také obor názvů.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var directory = System.IO.Path.GetDirectoryName(Assembly.GetExecutingAssembly().Location);
                var settings = config.Build();

                config.AddJsonFile(Path.Combine(directory, "azureappconfig.json"));
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

### <a name="build-and-run-the-app-locally"></a>Sestavení a spuštění aplikace místně

1. Nastavte proměnnou prostředí s názvem **ConnectionString**a nastavte ji na přístupový klíč do úložiště konfigurace aplikace. 
    Pokud používáte příkazový řádek systému Windows, spusťte následující příkaz a restartujte příkazový řádek, aby se změna projevila:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Pokud používáte prostředí Windows PowerShell, spusťte následující příkaz:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Pokud používáte macOS nebo Linux, spusťte následující příkaz:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Chcete-li vytvořit aplikaci pomocí rozhraní PŘÍKAZU .NET Core CLI, spusťte v příkazovém prostředí následující příkaz:

        dotnet build

3. Po úspěšném dokončení sestavení spusťte následující příkaz a spusťte webovou aplikaci místně:

        dotnet run

4. Otevřete okno prohlížeče `http://localhost:5000`a přejděte na stránku , což je výchozí adresa URL webové aplikace hostované místně.

    ![Spuštění aplikace QuickStart místní](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste exportovali data konfigurace aplikací Azure, která se mají použít v kanálu nasazení. Další informace o tom, jak používat konfiguraci aplikací, pokračujte ukázkami azure cli.

> [!div class="nextstepaction"]
> [Integrace spravované identity](./howto-integrate-azure-managed-service-identity.md)
