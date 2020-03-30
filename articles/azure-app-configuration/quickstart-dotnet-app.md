---
title: Úvodní příručka pro konfiguraci aplikací Azure pomocí rozhraní .NET Framework | Dokumenty společnosti Microsoft
description: Rychlý start pro používání konfigurace aplikací Azure s aplikacemi rozhraní .NET Framework
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: lcozzens
ms.openlocfilehash: 1c56088a2c51c50c7f9cf1ff1e790d580fdb08d8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80245391"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Úvodní příručka: Vytvoření aplikace rozhraní .NET Framework s konfigurací aplikací Azure

V tomto rychlém startu začlenit Azure App Configuration do konzolové aplikace založené na rozhraní .NET Framework centralizovat úložiště a správu nastavení aplikací odděleně od vašeho kódu.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [Rozhraní .NET 4.7.2](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikací

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Vyberte **Průzkumník** > konfigurace**Vytvořit** > **hodnotu klíče a** přidejte následující dvojice klíč-hodnota:

    | Klíč | Hodnota |
    |---|---|
    | testapp:nastavení:zpráva | Data z konfigurace aplikací Azure |

    Ponechte **popisek** a **typ obsahu** prozatím prázdný.

7. Vyberte **Použít**.

## <a name="create-a-net-console-app"></a>Vytvoření aplikace konzoly ROZHRANÍ .NET

1. Spusťte Visual Studio a vyberte **Soubor** > **nový** > **projekt**.

1. V **části Vytvořit nový projekt**vyfiltrujte typ projektu **konzoly** a klikněte na **Konzolová aplikace (.NET Framework).** Vyberte **další**.

1. V **okně Konfigurace nového projektu**zadejte název projektu. V **části Framework**vyberte rozhraní **.NET Framework 4.7.1** nebo vyšší. Vyberte **Vytvořit**.

## <a name="connect-to-an-app-configuration-store"></a>Připojení k úložišti konfigurace aplikací

1. Klikněte pravým tlačítkem myši na projekt a vyberte **spravovat balíčky NuGet**. Na **kartě Procházet** vyhledejte a přidejte do projektu následující balíčky NuGet. Pokud je nemůžete najít, zaškrtněte políčko **Zahrnout předběžnou verzi.**

    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 preview or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 preview or later
    System.Configuration.ConfigurationManager version 4.6.0 or later
    ```

1. Aktualizujte soubor *App.config* projektu takto:

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

   Připojovací řetězec úložiště konfigurace aplikace se `ConnectionString`čte z proměnné prostředí . Přidejte `Environment` tvůrce konfigurace `MyConfigStore` před `configBuilders` ve `appSettings` vlastnosti oddílu.

1. Otevřete *Program.cs*a `Main` aktualizujte metodu `ConfigurationManager`pro použití konfigurace aplikace voláním .

    ```csharp
    static void Main(string[] args)
    {
        string message = System.Configuration.ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Sestavení a spuštění aplikace místně

1. Nastavte proměnnou prostředí s názvem **ConnectionString** na připojovací řetězec úložiště konfigurace aplikace. Pokud používáte příkazový řádek systému Windows, spusťte následující příkaz:

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Pokud používáte prostředí Windows PowerShell, spusťte následující příkaz:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```
1. Restartujte visual studio, aby se změna projevila. Stisknutím kláves Ctrl + F5 vytvořte a spusťte konzolovou aplikaci.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili nový obchod SKonfiguraci aplikací a použili jste ho s konzolovou aplikací rozhraní .NET Framework. Hodnota `AppSettings` `ConfigurationManager` se po spuštění aplikace nezmění. The App Configuration .NET Standard configuration provider library, however can also be used in a .NET Framework app. Chcete-li se dozvědět, jak povolit aplikaci .NET Framework dynamicky aktualizovat nastavení konfigurace, pokračujte dalším kurzem.

> [!div class="nextstepaction"]
> [Povolit dynamickou konfiguraci](./enable-dynamic-configuration-dotnet.md)
