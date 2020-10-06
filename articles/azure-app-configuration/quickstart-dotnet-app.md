---
title: Rychlý Start pro konfiguraci aplikací Azure s .NET Framework | Microsoft Docs
description: V tomto článku vytvoříte aplikaci .NET Framework s využitím konfigurace aplikací Azure k centralizaci úložiště a správy nastavení aplikace odděleně od vašeho kódu.
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 09/28/2020
ms.author: lcozzens
ms.openlocfilehash: d338ca5cf2c794f15d3eb535b5280208236d1e34
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91767752"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Rychlý Start: Vytvoření aplikace .NET Framework s využitím konfigurace aplikace Azure

V tomto rychlém startu zařadíte konfiguraci aplikací Azure do konzolové aplikace založené na .NET Framework pro centralizaci úložiště a správy nastavení aplikace odděleně od vašeho kódu.

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/dotnet) .
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.2](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikace

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Vyberte možnost **Průzkumník konfigurace**  >  **vytvořit**  >  **klíč-hodnota** a přidejte následující páry klíč-hodnota:

    | Klíč | Hodnota |
    |---|---|
    | TestApp: nastavení: zpráva | Data z konfigurace aplikace Azure |

    V tuto chvíli ponechat **popisek** a **typ obsahu** prázdné.

8. Vyberte **Použít**.

## <a name="create-a-net-console-app"></a>Vytvoření konzolové aplikace .NET

1. Spusťte Visual Studio a vyberte **soubor**  >  **Nový**  >  **projekt**.

1. V části **vytvořit nový projekt**, vyfiltrujte typ projektu **konzoly** a klikněte na **Konzolová aplikace (.NET Framework)**. Vyberte **Další**.

1. V **konfiguraci nového projektu**zadejte název projektu. V části **rozhraní**vyberte **.NET Framework 4.7.1** nebo vyšší. Vyberte **Vytvořit**.

## <a name="connect-to-an-app-configuration-store"></a>Připojení k úložišti konfigurace aplikace

1. Klikněte pravým tlačítkem na projekt a vyberte **Spravovat balíčky NuGet**. Na kartě **Procházet** vyhledejte a přidejte do svého projektu následující balíčky NuGet.

    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 or later
    System.Configuration.ConfigurationManager version 4.6.0 or later
    ```

1. Aktualizujte soubor *App.config* projektu následujícím způsobem:

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

   Připojovací řetězec úložiště konfigurace aplikace je načtený z proměnné prostředí `ConnectionString` . Přidejte nástroj `Environment` Configuration Builder před do `MyConfigStore` `configBuilders` vlastnosti `appSettings` oddílu.

1. Otevřete *program.cs*a aktualizujte `Main` metodu na použití konfigurace aplikace voláním `ConfigurationManager` .

    ```csharp
    static void Main(string[] args)
    {
        string message = System.Configuration.ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
        Console.ReadKey();
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Místní sestavení a spuštění aplikace

1. Aktualizujte soubor **App.config** nahrazením `${ConnectionString}` skutečným připojovacím řetězcem pro instanci konfigurace aplikace. Můžete ji najít na kartě **přístupové klíče** prostředku konfigurace aplikace na webu Azure Portal.

1. Stisknutím kombinace kláves CTRL + F5 Sestavte a spusťte konzolovou aplikaci.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili nové úložiště konfigurace aplikace a použili ho v aplikaci .NET Framework konzolu. Hodnota se `AppSettings` `ConfigurationManager` po spuštění aplikace nezmění. Knihovna zprostředkovatele konfigurace .NET Standard konfigurace aplikace se ale dá použít i v aplikaci .NET Framework. Další informace o tom, jak aplikaci .NET Framework povolit, aby dynamicky aktualizovala nastavení konfigurace, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Povolení dynamické konfigurace](./enable-dynamic-configuration-dotnet.md)
