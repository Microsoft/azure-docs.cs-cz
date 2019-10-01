---
title: Rychlý Start pro konfiguraci aplikací Azure s .NET Core | Microsoft Docs
description: Rychlý Start pro použití konfigurace aplikací Azure s aplikacemi .NET Core
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
ms.openlocfilehash: 149542965737b2703bc18e985bfaf4d90c8dab29
ms.sourcegitcommit: d4c9821b31f5a12ab4cc60036fde00e7d8dc4421
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709416"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>Rychlý Start: Vytvoření aplikace .NET Core s konfigurací aplikace

V tomto rychlém startu zařadíte konfiguraci aplikací Azure do konzolové aplikace .NET Core, která umožňuje centralizaci úložiště a správy nastavení aplikace odděleně od vašeho kódu.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
- [.NET Core SDK](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikace

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Vyberte **Průzkumníka konfigurace** >  **+ vytvořit** a přidejte následující páry klíč-hodnota:

    | Key | Hodnota |
    |---|---|
    | TestApp: nastavení: zpráva | Data z konfigurace aplikace Azure |

    V tuto chvíli ponechat **popisek** a **typ obsahu** prázdné.

## <a name="create-a-net-core-console-app"></a>Vytvoření konzolové aplikace .NET Core

K vytvoření nového projektu konzolové aplikace .NET Core použijete [rozhraní příkazového řádku .NET Core (CLI)](https://docs.microsoft.com/dotnet/core/tools/) . Výhodou použití .NET Core CLI přes Visual Studio je to, že je k dispozici na platformách Windows, macOS a Linux.

1. Vytvořte pro svůj projekt novou složku.

2. V nové složce spusťte následující příkaz, který vytvoří nový projekt ASP.NET Core konzolové aplikace:

        dotnet new console

## <a name="connect-to-an-app-configuration-store"></a>Připojení k úložišti konfigurace aplikace

1. Přidejte odkaz na balíček NuGet `Microsoft.Extensions.Configuration.AzureAppConfiguration` spuštěním následujícího příkazu:

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration --version 2.0.0-preview-009470001-1371

2. Spusťte následující příkaz pro obnovení balíčků pro váš projekt:

        dotnet restore

3. Otevřete *program.cs*a přidejte odkaz na poskytovatele konfigurace aplikace .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

4. Aktualizujte metodu `Main` pro použití konfigurace aplikace voláním metody `builder.AddAzureAppConfiguration()`.

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));

        var config = builder.Build();
        Console.WriteLine(config["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Místní sestavení a spuštění aplikace

1. Nastavte proměnnou prostředí s názvem **ConnectionString**a nastavte ji na přístupový klíč na úložiště konfigurace aplikace. Použijete-li příkazový řádek systému Windows, spusťte následující příkaz a restartujte příkazový řádek, aby se změna projevila:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Pokud používáte Windows PowerShell, spusťte následující příkaz:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Pokud používáte macOS nebo Linux, spusťte následující příkaz:

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. Spusťte následující příkaz, který sestaví konzolovou aplikaci:

        dotnet build

3. Po úspěšném dokončení sestavení spusťte následující příkaz pro místní spuštění aplikace:

        dotnet run

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili nové úložiště konfigurace aplikace a použili ho s konzolovou aplikací .NET Core přes [poskytovatele konfigurace aplikace](https://go.microsoft.com/fwlink/?linkid=2074664). Další informace o tom, jak používat konfiguraci aplikací, najdete v dalším kurzu, který předvádí ověřování.

> [!div class="nextstepaction"]
> [Spravovaná integrace identit](./howto-integrate-azure-managed-service-identity.md)
