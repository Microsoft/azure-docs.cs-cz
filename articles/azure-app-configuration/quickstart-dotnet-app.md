---
title: Rychlý Start pro konfiguraci aplikací Azure s .NET Framework | Microsoft Docs
description: Rychlý Start pro použití konfigurace aplikací Azure s aplikacemi .NET Framework
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: lcozzens
ms.openlocfilehash: 8190265753bddb3038c5403411c4be193dd8075c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75433622"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Rychlý Start: Vytvoření aplikace .NET Framework s využitím konfigurace aplikace Azure

V tomto rychlém startu zařadíte konfiguraci aplikací Azure do konzolové aplikace založené na .NET Framework pro centralizaci úložiště a správy nastavení aplikace odděleně od vašeho kódu.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.2](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikace

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Vyberte **Průzkumník konfigurace** > **vytvořit** a přidejte následující páry klíč-hodnota:

    | Klíč | Hodnota |
    |---|---|
    | TestApp: nastavení: zpráva | Data z konfigurace aplikace Azure |

    V tuto chvíli ponechat **popisek** a **typ obsahu** prázdné.

## <a name="create-a-net-console-app"></a>Vytvoření konzolové aplikace .NET

1. Spusťte Visual Studio a vyberte **soubor** > **Nový** > **projekt**.

1. V části **vytvořit nový projekt**, vyfiltrujte typ projektu **konzoly** a klikněte na **Konzolová aplikace (.NET Framework)** . Vyberte **Next** (Další).

1. V **konfiguraci nového projektu**zadejte název projektu. V části **rozhraní**vyberte **.NET Framework 4.7.1** nebo vyšší. Vyberte **Vytvořit**.

## <a name="connect-to-an-app-configuration-store"></a>Připojení k úložišti konfigurace aplikace

1. Klikněte pravým tlačítkem na projekt a vyberte **Spravovat balíčky NuGet**. Na kartě **Procházet** vyhledejte a přidejte do svého projektu následující balíčky NuGet. Pokud je nemůžete najít, zaškrtněte políčko **zahrnout předběžné verze** .

    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 preview or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 preview or later
    System.Configuration.ConfigurationManager version 4.6.0 or later
    ```

1. Aktualizujte soubor *App. config* projektu následujícím způsobem:

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

   Připojovací řetězec úložiště konfigurace aplikace je čten z proměnné prostředí `ConnectionString`. Přidejte `Environment` Configuration Builder před `MyConfigStore` do vlastnosti `configBuilders` oddílu `appSettings`.

1. Otevřete *program.cs*a aktualizujte metodu `Main` tak, aby používala konfiguraci aplikace, voláním `ConfigurationManager`.

    ```csharp
    static void Main(string[] args)
    {
        string message = System.Configuration.ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Místní sestavení a spuštění aplikace

1. Nastavte proměnnou prostředí s názvem **ConnectionString** na připojovací řetězec úložiště konfigurace vaší aplikace. Pokud použijete příkazový řádek Windows, spusťte následující příkaz:

    ```CLI
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Pokud používáte Windows PowerShell, spusťte následující příkaz:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```
1. Pokud chcete, aby se změna projevila, restartujte Visual Studio. Stisknutím kombinace kláves CTRL + F5 Sestavte a spusťte konzolovou aplikaci.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili nové úložiště konfigurace aplikace a použili ho v aplikaci .NET Framework konzolu. Hodnota `AppSettings` `ConfigurationManager` se po spuštění aplikace nezmění. Knihovna zprostředkovatele konfigurace .NET Standard konfigurace aplikace se ale dá použít i v aplikaci .NET Framework. Další informace o tom, jak aplikaci .NET Framework povolit, aby dynamicky aktualizovala nastavení konfigurace, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Povolit dynamickou konfiguraci](./enable-dynamic-configuration-dotnet.md)
