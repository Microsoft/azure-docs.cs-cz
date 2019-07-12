---
title: Rychlý start pro konfiguraci aplikací Azure pomocí .NET Core | Dokumentace Microsoftu
description: Rychlý start pro konfiguraci aplikací Azure pomocí aplikace .NET Core
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET Core
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: c42066b3ac02264ee357bac7ed3fdd360e9d5d4a
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798419"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>Rychlý start: Vytvoření .NET Core aplikace s konfigurací aplikace

Konfigurace aplikace pro Azure je služba spravované konfigurace v Azure. Můžete ho snadno ukládat a spravovat nastavení aplikace na jednom místě, který je oddělen od kódu. V tomto rychlém startu se dozvíte, jak tuto službu začlenit do aplikace konzoly .NET Core.

Provést kroky v tomto rychlém startu můžete použít libovolný editor kódu. [Visual Studio Code](https://code.visualstudio.com/) skvělou možnost je k dispozici ve Windows, macOS a Linux platformy.

![Běh aplikace rychlý start](./media/quickstarts/dotnet-core-app-run.png)

## <a name="prerequisites"></a>Požadavky

To provedete v tomto rychlém startu, nainstalujete [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikace

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Vyberte **Průzkumník konfigurací** >  **+ vytvořit** přidáte následující páry klíč hodnota:

    | Klíč | Hodnota |
    |---|---|
    | TestApp:Settings:Message | Data z konfigurace aplikace Azure |

    Ponechte **popisek** a **typ obsahu** zatím prázdný.

## <a name="create-a-net-core-console-app"></a>Vytvoření konzolové aplikace .NET Core

Můžete použít [rozhraní příkazového řádku .NET Core (CLI)](https://docs.microsoft.com/dotnet/core/tools/) k vytvoření nového projektu aplikace konzoly .NET Core. Výhodou použití rozhraní příkazového řádku .NET Core v sadě Visual Studio je, že je k dispozici ve Windows, macOS a Linux platformy.

1. Vytvořte novou složku pro váš projekt.

2. V nové složce spuštěním následujícího příkazu vytvořte nový projekt konzolové aplikace ASP.NET Core:

        dotnet new console

## <a name="connect-to-an-app-configuration-store"></a>Připojit k úložišti konfigurace aplikace

1. Přidejte odkaz na `Microsoft.Azure.AppConfiguration.AspNetCore` balíček NuGet spuštěním následujícího příkazu:

        dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore --version 2.0.0-preview-009200001-7

2. Spusťte následující příkaz k obnovení balíčků pro váš projekt:

        dotnet restore

3. Otevřít *Program.cs*a přidejte odkaz na poskytovatele konfigurace pro aplikace .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

4. Aktualizace `Main` používat konfiguraci aplikací pomocí volání metody `builder.AddAzureAppConfiguration()` metody.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));

        var config = builder.Build();
        Console.WriteLine(config["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Sestavte a spusťte aplikaci místně

1. Nastavte proměnnou prostředí s názvem **ConnectionString**a nastavte ho na přístupový klíč k úložišti konfigurace aplikace. Pokud používáte Windows příkazového řádku, spusťte následující příkaz a restartujte příkazového řádku umožňující změna se projeví:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Pokud používáte prostředí Windows PowerShell, spusťte následující příkaz:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Pokud používáte systému macOS nebo Linux, spusťte následující příkaz:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Spusťte následující příkaz k sestavení aplikace konzoly:

        dotnet build

3. Po úspěšném dokončení sestavení spusťte následující příkaz a spusťte aplikaci místně:

        dotnet run

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili novým úložištěm konfigurace aplikace a použít s konzolovou aplikaci .NET Core prostřednictvím [poskytovatele konfigurace aplikace](https://go.microsoft.com/fwlink/?linkid=2074664). Další informace o tom, jak používat konfiguraci aplikací, pokračujte k dalšímu kurzu, který ukazuje ověření.

> [!div class="nextstepaction"]
> [Integrace spravovaných identit](./howto-integrate-azure-managed-service-identity.md)
