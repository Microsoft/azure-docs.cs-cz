---
title: Integrace s průběžnou integraci a doručování kanálu pomocí konfigurace aplikace pro Azure | Dokumentace Microsoftu
description: Zjistěte, jak vygenerovat konfigurační soubor používá data v konfiguraci aplikací pro Azure během průběžnou integraci a doručování
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 7b2e919bc46810e8478956675ffeb1cb0542da85
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957364"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integrace s kanálem CI/CD

K vylepšení odolnosti vaší aplikace proti vzdálené možnosti, že nelze dosáhnout konfigurace aplikace pro Azure, musí balíček aktuální konfiguračních dat do souboru, který je nasazen s aplikací a místně načtení při jeho spuštění . Tento postup zaručuje, že vaše aplikace bude mít výchozí hodnoty nastavení alespoň. Tyto hodnoty se přepíšou všechny novější změny v obchodě s aplikacemi konfigurace až bude k dispozici.

Použití [ **exportovat** ](./howto-import-export-data.md#export-data) funkce Konfigurace aplikací v Azure, můžete automatizovat proces načítání aktuální konfigurační data jako jeden soubor. Tento soubor pak můžete vložit v kroku sestavení nebo nasazení v průběžnou integraci a průběžné nasazování kanálu.

Následující příklad ukazuje, jak zahrnout konfigurace aplikace data jako sestavení krok pro webovou aplikaci zavedený rychlých startech. Kompletní [vytvoření aplikace ASP.NET Core s konfigurací aplikace](./quickstart-aspnet-core-app.md) první předtím, než budete pokračovat.

K dokončení kroků v tomto rychlém startu můžete použít jakýkoli editor kódu. Skvělou volbou je však editor [Visual Studio Code](https://code.visualstudio.com/), který je dostupný pro platformy Windows, macOS a Linux.

## <a name="prerequisites"></a>Požadavky

Pokud vytvoříte místně, stáhněte a nainstalujte [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Pokud jste tak již neučinili.

Pokud chcete provést sestavení do cloudu s Azure DevOps například, ujistěte se, že [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) nainstalovaný v systému sestavení.

## <a name="export-app-configuration-store"></a>Export konfigurace obchodu s aplikacemi

1. Otevřete váš *.csproj* soubor a přidejte následující:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -f $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    *ConnectionString* související s konfigurací aplikace úložiště přidaly jako proměnnou prostředí.

2. Otevřít *Program.cs* a aktualizovat `CreateWebHostBuilder` metodu použít soubor exportovaného json voláním `config.AddJsonFile()` metoda.

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

1. Nastavte proměnnou prostředí s názvem **ConnectionString** a nastavte ho na přístupový klíč k úložišti konfigurace aplikace. Pokud používáte Windows příkazového řádku, spusťte následující příkaz a potom restartujte příkazového řádku umožňující změna se projeví:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Pokud používáte prostředí Windows PowerShell, spusťte následující příkaz:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Pokud používáte v systému macOS nebo Linux, spusťte následující příkaz:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Pokud chcete aplikaci sestavit pomocí .NET Core CLI, spusťte v příkazovém prostředí následující příkaz:

        dotnet build

3. Po úspěšném dokončení sestavení spuštěním následujícího příkazu místně spusťte webovou aplikaci:

        dotnet run

4. Spusťte okno prohlížeče a přejděte na `http://localhost:5000`, což je výchozí adresa URL pro webové aplikace hostované místně.

    ![Místní spuštění aplikace rychlý start](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Další postup

* [Spravované identity pro integraci prostředků Azure](./integrate-azure-managed-service-identity.md)
