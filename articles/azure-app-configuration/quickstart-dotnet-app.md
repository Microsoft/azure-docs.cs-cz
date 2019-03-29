---
title: Rychlý start pro konfiguraci aplikací Azure pomocí rozhraní .NET Framework | Dokumentace Microsoftu
description: Rychlý start pro konfiguraci aplikací Azure pomocí aplikace rozhraní .NET Framework
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: b4cb571653cbe69939a1cbdc92338663e4e7125f
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2019
ms.locfileid: "58576068"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Rychlý start: Vytvoření .NET Framework aplikace s konfigurací aplikace Azure

Konfigurace aplikace pro Azure je služba spravované konfigurace v Azure. Můžete ho snadno ukládat a spravovat nastavení aplikace na jednom místě, který je oddělen od kódu. V tomto rychlém startu se dozvíte, jak tuto službu začlenit do aplikace klasické pracovní plochy konzoly Windows založené na rozhraní .NET Framework.

![Kompletní místní rychlý start](./media/quickstarts/dotnet-fx-app-run.png)

## <a name="prerequisites"></a>Požadavky

V tomto rychlém startu provedete instalaci [Visual Studio 2017](https://visualstudio.microsoft.com/vs) a [rozhraní .NET Framework 4.7.1](https://dotnet.microsoft.com/download) nebo novější, pokud jste tak již neučinili.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikace

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-net-console-app"></a>Vytvoření konzolové aplikace .NET

1. Spusťte sadu Visual Studio a vyberte **souboru** > **nový** > **projektu**.

2. V **nový projekt**vyberte **nainstalováno** > **Visual C#**   >  **Windows Desktop**. Vyberte **Konzolová aplikace (.NET Framework)** a zadejte název pro váš projekt. Vyberte **rozhraní .NET Framework 4.7.1** nebo si a vyberte **OK**.

## <a name="connect-to-an-app-configuration-store"></a>Připojit k úložišti konfigurace aplikace

1. Klikněte pravým tlačítkem na projekt a vyberte **spravovat balíčky NuGet**. Na **Procházet** kartu, vyhledávání a přidejte následující balíčky NuGet do projektu. Pokud je nemůžete najít, vyberte **zahrnout předběžné verze** zaškrtávací políčko.

    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 preview or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 preview or later
    ```

2. Aktualizace *App.config* souboru projektu následujícím způsobem:

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" connectionString="${ConnectionString}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="ConnectionString" value ="Set via an environment variable - for example, dev, test, staging, or production connection string." />
    </appSettings>
    ```

   Připojovací řetězec úložiště konfigurace aplikace čte z proměnné prostředí `ConnectionString`. Přidat `Environment` Tvůrce konfigurace před `MyConfigStore` v `configBuilders` vlastnost `appSettings` oddílu.

3. Otevřít *Program.cs*a aktualizovat `Main` používat konfiguraci aplikací pomocí volání metody `ConfigurationManager`.

    ```csharp
    static void Main(string[] args)
    {
        string message = ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Sestavte a spusťte aplikaci místně

1. Nastavte proměnnou prostředí s názvem **ConnectionString** na připojovací řetězec úložiště konfigurace vaší aplikace. Pokud používáte Windows příkazového řádku, spusťte následující příkaz:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Pokud používáte prostředí Windows PowerShell, spusťte následující příkaz:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

2. Restartujte aplikaci Visual Studio, aby změna projevila. Stisknutím kláves Ctrl + F5 sestavte a spusťte konzolovou aplikaci.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili novým úložištěm konfigurace aplikace a použít s konzolovou aplikaci .NET Framework. Další informace o tom, jak používat konfiguraci aplikací, pokračujte k dalšímu kurzu, který ukazuje ověření.

> [!div class="nextstepaction"]
> [Spravované identity pro integraci prostředků Azure](./integrate-azure-managed-service-identity.md)
