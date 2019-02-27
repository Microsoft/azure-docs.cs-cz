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
ms.openlocfilehash: f8b072752a6cf538cb1a85d3b255af6201aa041e
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884806"
---
# <a name="quickstart-create-an-net-core-app-with-app-configuration"></a>Rychlý start: Vytvoření .NET Core aplikace s konfigurací aplikace

Konfigurace aplikace pro Azure je služba spravované konfigurace v Azure. To umožňuje snadno ukládat a spravovat nastavení aplikace na jednom místě, který je oddělen od kódu. V tomto rychlém startu se dozvíte, jak tuto službu začlenit do aplikace konzoly .NET Core.

K dokončení kroků v tomto rychlém startu můžete použít jakýkoli editor kódu. Skvělou volbou je však editor [Visual Studio Code](https://code.visualstudio.com/), který je dostupný pro platformy Windows, macOS a Linux.

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, nainstalujte [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikace

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-net-core-console-app"></a>Vytvoření konzolové aplikace .NET Core

Budete používat [rozhraní příkazového řádku .NET Core (CLI)](https://docs.microsoft.com/dotnet/core/tools/) vytvoříte nový projekt konzolové aplikace .NET Core. Výhodou použití rozhraní .NET Core CLI místo sady Visual Studio je jeho dostupnost na platformách Windows, Linux i macOS.

1. Vytvořte novou složku pro váš projekt.

2. V nové složce spuštěním následujícího příkazu vytvořte nový projekt webové aplikace ASP.NET Core MVC:

        dotnet new console

## <a name="connect-to-app-configuration-store"></a>Připojit k úložišti konfigurace aplikace

1. Přidejte odkaz na balíček `Microsoft.Extensions.Configuration.AzureAppConfiguration` spuštěním následujícího příkazu:

        dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration

2. Spuštěním následujícího příkazu obnovte balíčky pro váš projekt.

        dotnet restore

3. Otevřít *Program.cs* a aktualizovat `Main` používat konfiguraci aplikací pomocí volání metody `builder.AddAzureAppConfiguration()` metoda.

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

1. Nastavte proměnnou prostředí s názvem **ConnectionString** a nastavte ho na přístupový klíč k úložišti konfigurace aplikace. Pokud používáte Windows příkazového řádku, spusťte následující příkaz a potom restartujte příkazového řádku umožňující změna se projeví:

        setx ConnectionString "Endpoint=<service_endpoint>;Id=<store_id>;Secret=<secret_key>"

    Pokud používáte prostředí Windows PowerShell, spusťte následující příkaz:

        $Env:ConnectionString = "Endpoint=<service_endpoint>;Id=<store_id>;Secret=<secret_key>"

    Pokud používáte v systému macOS nebo Linux, spusťte následující příkaz:

        export ConnectionString='Endpoint=<service_endpoint>;Id=<store_id>;Secret=<secret_key>'

2. Spusťte následující příkaz k sestavení aplikace konzoly:

        dotnet build

3. Po úspěšném dokončení sestavení spusťte následující příkaz a spusťte aplikaci místně:

        dotnet run

    ![Běh aplikace rychlý start](./media/quickstarts/dotnet-core-app-run.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili novým úložištěm konfigurace aplikace a použít ho s konzolovou aplikaci .NET Core. Další informace o použití konfigurace aplikace, pokračujte k dalšímu kurzu, který ukazuje ověření.

> [!div class="nextstepaction"]
> [Spravované identity pro integraci prostředků Azure](./integrate-azure-managed-service-identity.md)
