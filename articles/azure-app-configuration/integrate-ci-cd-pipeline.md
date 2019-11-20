---
title: Kurz pro integraci s kanálem pro průběžnou integraci a doručování pomocí Azure App Configurationu | Microsoft Docs
description: V tomto kurzu se naučíte generovat konfigurační soubor pomocí dat v konfiguraci aplikace Azure během průběžné integrace a doručování.
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
ms.openlocfilehash: e9b81baed14b18c6db736bd94a2aba43a4e671ad
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185104"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integrace s kanálem CI/CD

Tento článek popisuje různé způsoby použití dat z konfigurace aplikací Azure v systému průběžné integrace a průběžného nasazování.

## <a name="use-app-configuration-in-your-azure-devops-pipeline"></a>Použití konfigurace aplikace v kanálu Azure DevOps

Pokud máte kanál Azure DevOps, můžete načíst hodnoty klíč-hodnota z konfigurace aplikace a nastavit je jako proměnné úkolu. [Rozšíření DevOps App Configuration pro Azure](https://go.microsoft.com/fwlink/?linkid=2091063) je modul doplňku, který poskytuje tuto funkci. Stačí postupovat podle pokynů pro použití rozšíření v pořadí úkolů sestavení nebo vydání.

## <a name="deploy-app-configuration-data-with-your-application"></a>Nasazení dat konfigurace aplikace pomocí vaší aplikace

Pokud je vaše aplikace závislá na konfiguraci aplikace Azure a nemůže se k ní připojit, může se stát, že se vaše aplikace nespustí. Odolnost vaší aplikace se dá vylepšit tak, aby se na takovou událost mohla vypořádat, ale nepravděpodobné, že by k tomu mohlo dojít. Provedete to tak, že zabalíte aktuální konfigurační data do souboru, který je nasazený s aplikací a načtete místně během jejího spuštění. Tento přístup zaručuje, že vaše aplikace má alespoň výchozí hodnoty nastavení. Tyto hodnoty jsou přepsány libovolnými novějšími změnami v úložišti konfigurace aplikace, když jsou k dispozici.

Pomocí funkce [exportu](./howto-import-export-data.md#export-data) v konfiguraci aplikace Azure můžete automatizovat proces načítání aktuálních konfiguračních dat jako jednoho souboru. Pak tento soubor vložte do kanálu sestavení nebo nasazení do kanálu průběžné integrace a průběžného nasazování (CI/CD).

Následující příklad ukazuje, jak zahrnout konfigurační data aplikace jako krok sestavení pro webovou aplikaci zavedenou v rychlých startech. Než budete pokračovat, dokončete nejprve [Vytvoření aplikace ASP.NET Core s konfigurací aplikace](./quickstart-aspnet-core-app.md) .

K provedení kroků v tomto kurzu můžete použít libovolný editor kódu. [Visual Studio Code](https://code.visualstudio.com/) je vynikající možnost dostupná na platformách Windows, MacOS a Linux.

### <a name="prerequisites"></a>Požadavky

Pokud jste místně sestavili, Stáhněte a nainstalujte [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) , pokud jste to ještě neudělali.

Aby bylo možné vytvořit cloudové sestavení pomocí Azure DevOps, ujistěte se, že rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) je nainstalováno v systému sestavení.

### <a name="export-an-app-configuration-store"></a>Exportovat úložiště konfigurace aplikace

1. Otevřete soubor *. csproj* a přidejte následující skript:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -d file --path $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```

    Přidejte *připojovací řetězec* přidružený k úložišti konfigurace aplikace jako proměnnou prostředí.

2. Otevřete *program.cs*a aktualizujte metodu `CreateWebHostBuilder` pro použití exportovaného souboru JSON voláním metody `config.AddJsonFile()`.

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

### <a name="build-and-run-the-app-locally"></a>Místní sestavení a spuštění aplikace

1. Nastavte proměnnou prostředí s názvem **ConnectionString**a nastavte ji na přístupový klíč na úložiště konfigurace aplikace. Použijete-li příkazový řádek systému Windows, spusťte následující příkaz a restartujte příkazový řádek, aby se změna projevila:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Pokud používáte Windows PowerShell, spusťte následující příkaz:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Pokud používáte macOS nebo Linux, spusťte následující příkaz:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Pokud chcete aplikaci vytvořit pomocí .NET Core CLI, spusťte v příkazovém prostředí následující příkaz:

        dotnet build

3. Po úspěšném dokončení sestavení spusťte následující příkaz pro místní spuštění webové aplikace:

        dotnet run

4. Otevřete okno prohlížeče a klikněte na `http://localhost:5000`, což je výchozí adresa URL pro webovou aplikaci hostovanou místně.

    ![Spuštění aplikace pro rychlý Start – místní](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste exportovali data konfigurace aplikace Azure, která se mají použít v kanálu nasazení. Další informace o tom, jak používat konfiguraci aplikací, najdete v ukázkách Azure CLI.

> [!div class="nextstepaction"]
> [Spravovaná integrace identit](./howto-integrate-azure-managed-service-identity.md)
