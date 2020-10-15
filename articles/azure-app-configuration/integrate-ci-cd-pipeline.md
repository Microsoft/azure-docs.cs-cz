---
title: Integrace konfigurace aplikace Azure s použitím kanálu průběžné integrace a doručování
description: Naučte se implementovat průběžnou integraci a doručování pomocí Azure App Configuration.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.date: 01/30/2020
ms.author: lcozzens
ms.openlocfilehash: b8756db881448edcaac1fda44b60229975350676
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92074732"
---
# <a name="integrate-with-a-cicd-pipeline"></a>Integrace s kanálem CI/CD

Tento článek vysvětluje, jak používat data z konfigurace aplikací Azure v systému kontinuální integrace a průběžného nasazování.

## <a name="use-app-configuration-in-your-azure-devops-pipeline"></a>Použití konfigurace aplikace v kanálu Azure DevOps

Pokud máte kanál Azure DevOps, můžete načíst hodnoty klíč-hodnota z konfigurace aplikace a nastavit je jako proměnné úkolu. [Rozšíření DevOps App Configuration pro Azure](https://go.microsoft.com/fwlink/?linkid=2091063) je modul doplňku, který poskytuje tuto funkci. Postupujte podle pokynů pro použití rozšíření v pořadí úkolů sestavení nebo vydání.

## <a name="deploy-app-configuration-data-with-your-application"></a>Nasazení dat konfigurace aplikace pomocí vaší aplikace

Pokud je vaše aplikace závislá na konfiguraci aplikace Azure a nemůže se k ní připojit, může se stát, že se vaše aplikace nespustí. Vylepšete odolnost vaší aplikace tím, že zabalíte konfigurační data do souboru, který je nasazený s aplikací, a načtete místně během spuštění aplikace. Tento přístup zaručuje, že vaše aplikace má při spuštění výchozí hodnoty nastavení. Tyto hodnoty jsou přepsány libovolnými novějšími změnami v úložišti konfigurace aplikace, když jsou k dispozici.

Pomocí funkce [exportu](./howto-import-export-data.md#export-data) v konfiguraci aplikace Azure můžete automatizovat proces načítání aktuálních konfiguračních dat jako jednoho souboru. Tento soubor pak můžete vložit do kanálu sestavení nebo nasazení v kanálu průběžné integrace a průběžného nasazování (CI/CD).

Následující příklad ukazuje, jak zahrnout konfigurační data aplikace jako krok sestavení pro webovou aplikaci zavedenou v rychlých startech. Než budete pokračovat, dokončete nejprve [Vytvoření aplikace ASP.NET Core s konfigurací aplikace](./quickstart-aspnet-core-app.md) .

K provedení kroků v tomto kurzu můžete použít libovolný editor kódu. [Visual Studio Code](https://code.visualstudio.com/) je vynikající možnost dostupná na platformách Windows, MacOS a Linux.

### <a name="prerequisites"></a>Požadavky

Pokud jste místně sestavili, Stáhněte a nainstalujte [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) , pokud jste to ještě neudělali.

Aby bylo možné vytvořit cloudové sestavení pomocí Azure DevOps, ujistěte se, že rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) je nainstalováno v systému sestavení.

### <a name="export-an-app-configuration-store"></a>Exportovat úložiště konfigurace aplikace

1. Otevřete soubor *. csproj* a přidejte následující skript:

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -d file --path $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```
1. Otevřete *program.cs*a aktualizujte `CreateWebHostBuilder` metodu pro použití exportovaného souboru JSON voláním `config.AddJsonFile()` metody.  Přidejte `System.Reflection` také obor názvů.

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

1. Nastavte proměnnou prostředí s názvem **ConnectionString**a nastavte ji na přístupový klíč na úložiště konfigurace aplikace. 
    Použijete-li příkazový řádek systému Windows, spusťte následující příkaz a restartujte příkazový řádek, aby se změna projevila:

    ```console
     setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Pokud používáte Windows PowerShell, spusťte následující příkaz:

    ```powershell
     $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Pokud používáte macOS nebo Linux, spusťte následující příkaz:

    ```console
     export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. Pokud chcete aplikaci vytvořit pomocí .NET Core CLI, spusťte v příkazovém prostředí následující příkaz:

    ```console
     dotnet build
    ```

3. Po úspěšném dokončení sestavení spusťte následující příkaz pro místní spuštění webové aplikace:

    ```console
     dotnet run
    ```

4. Otevřete okno prohlížeče a `http://localhost:5000` v části přejít na, což je výchozí adresa URL webové aplikace hostované na místním počítači.

    ![Spuštění aplikace pro rychlý Start – místní](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste exportovali data konfigurace aplikace Azure, která se mají použít v kanálu nasazení. Další informace o tom, jak používat konfiguraci aplikací, najdete v ukázkách Azure CLI.

> [!div class="nextstepaction"]
> [Azure CLI](/cli/azure/appconfig?view=azure-cli-latest)