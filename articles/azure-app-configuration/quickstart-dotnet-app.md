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
ms.openlocfilehash: 01302bf206c4205abca23cb90684e0672215eeda
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56884846"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Rychlý start: Vytvoření .NET Framework aplikace s konfigurací aplikace Azure

Konfigurace aplikace pro Azure je služba spravované konfigurace v Azure. To umožňuje snadno ukládat a spravovat nastavení aplikace na jednom místě, který je oddělen od kódu. V tomto rychlém startu se dozvíte, jak tuto službu začlenit do aplikace klasické pracovní plochy konzoly Windows založené na rozhraní .NET Framework.

![Dokončený rychlý start v místním prostředí](./media/quickstarts/dotnet-fx-app-run.png)

## <a name="prerequisites"></a>Požadavky

Abyste mohli absolvovat tento rychlý start, nainstalujte [Visual Studio 2017](https://visualstudio.microsoft.com/vs) a [rozhraní .NET Framework 4.7.1](https://dotnet.microsoft.com/download) nebo novější, pokud jste tak již neučinili.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikace

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

## <a name="create-a-net-console-app"></a>Vytvoření konzolové aplikace .NET

1. Spusťte sadu Visual Studio a vyberte **souboru** > **nový** > **projektu**.

2. V **nový projekt** dialogového okna, vyberte **nainstalováno**, rozbalte **Visual C#**   >  **Windows Desktop**, vyberte  **Aplikace konzoly (.NET Framework)**, zadejte **název** pro váš projekt, zvolte **rozhraní .NET Framework 4.7.1** nebo nahoru a klikněte na tlačítko **OK**.

## <a name="connect-to-app-configuration-store"></a>Připojit k úložišti konfigurace aplikace

1. Klikněte pravým tlačítkem na projekt a vyberte **spravovat balíčky NuGet...** . V **Procházet** kartu, vyhledávání a přidat následující balíčky NuGet do projektu (zkontrolujte **zahrnout předběžné verze** pole, pokud nemůžete najít, je).
    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 preview or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 preview or later
    ```

2. Aktualizace *App.config* souboru projektu následujícím způsobem

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
        <add key="ConnectionString" value ="First filled in by 'Environment'. Could be a dev, test, staging, or production connection string." />
    </appSettings>
    ```
   Nezapomeňte prosím, jak jsme čtení připojovací řetězec úložiště konfigurace vaší aplikace z proměnné prostředí `ConnectionString`, je potřeba přidat `Environment` Tvůrce konfigurace před `MyConfigStore` v `configBuilders` vlastnost `appSettings` oddílu.

3. Otevřít *Program.cs* a aktualizovat `Main` používat konfiguraci aplikací pomocí volání metody `ConfigurationManager`.

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

2. Restartujte aplikaci Visual Studio, aby tato změna se projeví a potom stiskněte klávesu **Ctrl + F5** na klávesnici a sestavíte a spustíte aplikaci konzoly.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili novým úložištěm konfigurace aplikace a použít s konzolovou aplikaci .NET Framework. Další informace o použití konfigurace aplikace, pokračujte k dalšímu kurzu, který ukazuje ověření.

> [!div class="nextstepaction"]
> [Spravované identity pro integraci prostředků Azure](./integrate-azure-managed-service-identity.md)
