---
title: Kurz pro integraci s kanálu průběžné integrace a doručování s využitím konfigurace aplikace pro Azure | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak chcete vygenerovat konfigurační soubor s použitím dat v konfiguraci aplikací pro Azure během průběžnou integraci a doručování
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: a92c00148d4b612a4360e3843d66503d528928cd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59700131"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integrace s kanálem CI/CD

Můžete zvýšit odolnost aplikace proti vzdálené možnosti, že nelze dosáhnout konfiguraci aplikací Azure. Uděláte to tak, balíček aktuální konfiguračních dat do souboru, který je nasazen s aplikací a načíst místně při jeho spuštění. Tento postup zaručuje, že vaše aplikace má alespoň výchozí hodnoty nastavení. Až bude k dispozici jsou tyto hodnoty přepíše všechny novější změny v obchodě s aplikacemi konfigurace.

Použití [exportovat](./howto-import-export-data.md#export-data) funkce Konfigurace aplikací v Azure, můžete automatizovat proces načítání aktuální konfigurační data jako jeden soubor. Potom můžete vložte tento soubor v kroku sestavení nebo nasazení v průběžné integrace a průběžného nasazování (CI/CD) kanálu.

Následující příklad ukazuje, jak zahrnout konfigurace aplikace data jako sestavení krok pro webovou aplikaci zavedený rychlých startech. Než budete pokračovat, dokončete [vytvoření aplikace ASP.NET Core s konfigurací aplikace](./quickstart-aspnet-core-app.md) první.

Provést kroky v tomto rychlém startu můžete použít libovolný editor kódu. [Visual Studio Code](https://code.visualstudio.com/) skvělou možnost je k dispozici ve Windows, macOS a Linux platformy.

## <a name="prerequisites"></a>Požadavky

Pokud vytvoříte místně, stáhněte a nainstalujte [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Pokud jste tak již neučinili.

Provedete sestavení do cloudu s Azure DevOps například, ujistěte se, že [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) nainstalovaný v systému sestavení.

## <a name="export-an-app-configuration-store"></a>Exportovat konfiguraci app storu

1. Otevřete váš *.csproj* soubor a přidejte následující skript:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -f $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    Přidat *ConnectionString* přidružený k úložišti konfigurace aplikace jako proměnné prostředí.

2. Otevřete soubor Program.cs a aktualizujte `CreateWebHostBuilder` metoda se má použít v exportovaném souboru JSON voláním `config.AddJsonFile()` metody.

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

## <a name="build-and-run-the-app-locally"></a>Sestavte a spusťte aplikaci místně

1. Nastavte proměnnou prostředí s názvem **ConnectionString**a nastavte ho na přístupový klíč k úložišti konfigurace aplikace. Pokud používáte Windows příkazového řádku, spusťte následující příkaz a restartujte příkazového řádku umožňující změna se projeví:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Pokud používáte prostředí Windows PowerShell, spusťte následující příkaz:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Pokud používáte systému macOS nebo Linux, spusťte následující příkaz:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Pokud chcete vytvořit aplikaci pomocí rozhraní příkazového řádku .NET Core, spusťte následující příkaz v příkazovém prostředí:

        dotnet build

3. Po úspěšném dokončení sestavení spusťte následující příkaz pro místní spuštění webové aplikace:

        dotnet run

4. Otevřete okno prohlížeče a přejděte na `http://localhost:5000`, což je výchozí adresa URL pro webové aplikace hostované místně.

    ![Místní spuštění aplikace rychlý start](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Další postup

* [Spravované identity pro integraci prostředků Azure](./howto-integrate-azure-managed-service-identity.md)
