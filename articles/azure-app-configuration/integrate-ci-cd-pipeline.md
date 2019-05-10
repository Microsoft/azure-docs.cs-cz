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
ms.openlocfilehash: 26bd49af7245d6e6dde3162a2e1d95c54f13e35b
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415954"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integrace s kanálem CI/CD

Tento článek popisuje různé způsoby použití dat z konfigurace aplikace pro Azure v průběžné integrace a průběžného nasazování systému.

## <a name="use-app-configuration-in-your-azure-devops-pipeline"></a>Používat konfiguraci aplikací v Azure kanálu DevOps

Pokud máte kanál DevOps Azure, můžete načíst hodnoty klíče z konfigurace aplikace a nastavit je jako proměnné úkolů. [Rozšíření Azure DevOps konfigurace aplikace](https://go.microsoft.com/fwlink/?linkid=2091063) je modul doplněk, který tuto funkci poskytuje. Jednoduše postupujte podle jeho pokynů použití rozšíření v sestavení nebo vydání pořadí úkolů.

## <a name="deploy-app-configuration-data-with-your-application"></a>Nasazení aplikace konfiguračních dat s vaší aplikací

Vaše aplikace nemusí podařit spustit, pokud závisí na konfiguraci aplikace Azure a nemá přístup. Můžete zvýšit odolnost vaší aplikace pracovala s takovou událost, ale pravděpodobně je provést. Uděláte to tak, balíček aktuální konfiguračních dat do souboru, který je nasazen s aplikací a načíst místně při jeho spuštění. Tento postup zaručuje, že vaše aplikace má alespoň výchozí hodnoty nastavení. Až bude k dispozici jsou tyto hodnoty přepíše všechny novější změny v obchodě s aplikacemi konfigurace.

Použití [exportovat](./howto-import-export-data.md#export-data) funkce Konfigurace aplikací v Azure, můžete automatizovat proces načítání aktuální konfigurační data jako jeden soubor. Potom můžete vložte tento soubor v kroku sestavení nebo nasazení v průběžné integrace a průběžného nasazování (CI/CD) kanálu.

Následující příklad ukazuje, jak zahrnout konfigurace aplikace data jako sestavení krok pro webovou aplikaci zavedený rychlých startech. Než budete pokračovat, dokončete [vytvoření aplikace ASP.NET Core s konfigurací aplikace](./quickstart-aspnet-core-app.md) první.

Provést kroky v tomto kurzu můžete použít libovolný editor kódu. [Visual Studio Code](https://code.visualstudio.com/) skvělou možnost je k dispozici ve Windows, macOS a Linux platformy.

### <a name="prerequisites"></a>Požadavky

Pokud vytvoříte místně, stáhněte a nainstalujte [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Pokud jste tak již neučinili.

Provedete sestavení do cloudu s Azure DevOps například, ujistěte se, že [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) nainstalovaný v systému sestavení.

### <a name="export-an-app-configuration-store"></a>Exportovat konfiguraci app storu

1. Otevřete váš *.csproj* soubor a přidejte následující skript:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -f $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    Přidat *ConnectionString* přidružený k úložišti konfigurace aplikace jako proměnné prostředí.

2. Otevřít *Program.cs*a aktualizovat `CreateWebHostBuilder` metoda se má použít v exportovaném souboru JSON voláním `config.AddJsonFile()` metody.

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

### <a name="build-and-run-the-app-locally"></a>Sestavte a spusťte aplikaci místně

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

V tomto kurzu jste exportovali Azure aplikace konfigurační data pro použití v kanálu nasazení. Další informace o tom, jak používat konfiguraci aplikací, i nadále ukázky Azure CLI.

> [!div class="nextstepaction"]
> [Integrace spravovaných identit](./howto-integrate-azure-managed-service-identity.md)
