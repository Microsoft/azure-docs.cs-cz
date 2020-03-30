---
title: Úvodní příručka pro konfiguraci aplikací Azure s jádrem .NET | Dokumenty společnosti Microsoft
description: Rychlý start pro používání konfigurace aplikací Azure s aplikacemi .NET Core
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 1/9/2019
ms.author: lcozzens
ms.openlocfilehash: 420d9b48013f5f6debe588667fe1cc0390517e66
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80245374"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>Úvodní příručka: Vytvoření aplikace .NET Core s konfigurací aplikace

V tomto rychlém startu začlenit Azure App Configuration do konzolové aplikace .NET Core centralizovat úložiště a správu nastavení aplikací odděleně od kódu.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
- [Sada .NET Core SDK](https://dotnet.microsoft.com/download) – dostupná také v [prostředí Azure Cloud Shell](https://shell.azure.com).

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikací

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Vyberte **Průzkumník** > konfigurace**Vytvořit** > **hodnotu klíče a** přidejte následující dvojice klíč-hodnota:

    | Klíč | Hodnota |
    |---|---|
    | testapp:nastavení:zpráva | Data z konfigurace aplikací Azure |

    Ponechte **popisek** a **typ obsahu** prozatím prázdný.

7. Vyberte **Použít**.

## <a name="create-a-net-core-console-app"></a>Vytvoření konzolové aplikace .NET Core

Pomocí [rozhraní příkazového řádku .NET Core (CLI)](https://docs.microsoft.com/dotnet/core/tools/) můžete vytvořit nový projekt konzoly .NET Core. Výhodou použití rozhraní PŘÍKAZOVÉHO PŘÍKAZU .NET Core oproti Visual Studiu je, že je k dispozici na platformách Windows, macOS a Linux.  Případně můžete použít předinstalované nástroje, které jsou k dispozici v [prostředí Azure Cloud Shell](https://shell.azure.com).

1. Vytvořte novou složku pro váš projekt.

2. V nové složce spusťte následující příkaz a vytvořte nový projekt konzoly ASP.NET Core:

    ```dotnetcli
    dotnet new console
    ```

## <a name="connect-to-an-app-configuration-store"></a>Připojení k úložišti konfigurace aplikací

1. Přidejte odkaz `Microsoft.Extensions.Configuration.AzureAppConfiguration` na balíček NuGet spuštěním následujícího příkazu:

    ```dotnetcli
    dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration
    ```

2. Chcete-li obnovit balíčky pro projekt, spusťte následující příkaz:

    ```dotnetcli
    dotnet restore
    ```

3. Otevřete *Program.cs*a přidejte odkaz na zprostředkovatele konfigurace aplikace .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

4. Aktualizujte `Main` metodu pro použití `builder.AddAzureAppConfiguration()` konfigurace aplikace voláním metody.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));

        var config = builder.Build();
        Console.WriteLine(config["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Sestavení a spuštění aplikace místně

1. Nastavte proměnnou prostředí s názvem **ConnectionString**a nastavte ji na přístupový klíč do úložiště konfigurace aplikace. Na příkazovém řádku spusťte následující příkaz:

    ```cmd
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Pokud používáte prostředí Windows PowerShell, spusťte následující příkaz:

    ```azurepowershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Pokud používáte macOS nebo Linux, spusťte následující příkaz:

        export ConnectionString='connection-string-of-your-app-configuration-store'

    Chcete-li změnu projevit, restartujte příkazový řádek. Vytiskněte hodnotu proměnné prostředí, abyste ověřili, zda je správně nastavena.

2. Chcete-li vytvořit konzolovou aplikaci, spusťte následující příkaz:

    ```dotnetcli
    dotnet build
    ```

3. Po úspěšném dokončení sestavení spusťte následující příkaz a spusťte aplikaci místně:

    ```dotnetcli
    dotnet run
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili nový obchod SKonfiguraci aplikací a použili jste ho s konzolovou aplikací .NET Core prostřednictvím [poskytovatele konfigurace aplikace](https://go.microsoft.com/fwlink/?linkid=2074664). Chcete-li se dozvědět, jak nakonfigurovat aplikaci .NET Core pro dynamickou aktualizaci nastavení konfigurace, pokračujte dalším kurzem.

> [!div class="nextstepaction"]
> [Povolit dynamickou konfiguraci](./enable-dynamic-configuration-dotnet-core.md)
