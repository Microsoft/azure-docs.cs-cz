---
title: Rychlý Start pro konfiguraci aplikací Azure s .NET Framework | Microsoft Docs
description: Rychlý Start pro použití konfigurace aplikací Azure s aplikacemi .NET Framework
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
ms.openlocfilehash: 8aa8c8132220965d55097c4fed8ba1b2e9501301
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326533"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>Rychlý start: Vytvoření aplikace .NET Framework s využitím konfigurace aplikace Azure

V tomto rychlém startu zařadíte konfiguraci aplikací Azure do konzolové aplikace založené na .NET Framework pro centralizaci úložiště a správy nastavení aplikace odděleně od vašeho kódu.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.1](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikace

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Vyberte **Průzkumník** > konfigurace **+ vytvořit** a přidejte následující páry klíč-hodnota:

    | Klíč | Value |
    |---|---|
    | TestApp: nastavení: zpráva | Data z konfigurace aplikace Azure |

    V tuto chvíli ponechat **popisek** a **typ obsahu** prázdné.

## <a name="create-a-net-console-app"></a>Vytvoření konzolové aplikace .NET

1. Spusťte Visual Studio a vyberte **soubor** > **Nový** > **projekt**.

2. V **novém projektu**vyberte možnost **nainstalované** > **prostředí C#Visual**   >  **Windows Desktop**. Vyberte **Konzolová aplikace (.NET Framework)** a zadejte název projektu. Vyberte **.NET Framework 4.7.1** nebo up a vyberte **OK**.

## <a name="connect-to-an-app-configuration-store"></a>Připojení k úložišti konfigurace aplikace

1. Klikněte pravým tlačítkem na projekt a vyberte **Spravovat balíčky NuGet**. Na kartě **Procházet** vyhledejte a přidejte do svého projektu následující balíčky NuGet. Pokud je nemůžete najít, zaškrtněte políčko **zahrnout předběžné verze** .

    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 preview or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 preview or later
    ```

2. Aktualizujte soubor *App. config* projektu následujícím způsobem:

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

   Připojovací řetězec úložiště konfigurace aplikace je načtený z proměnné `ConnectionString`prostředí. Přidejte nástroj `Environment` Configuration Builder `MyConfigStore` před `configBuilders` do vlastnosti `appSettings` oddílu.

3. Otevřete *program.cs*a aktualizujte `Main` metodu na použití konfigurace aplikace voláním `ConfigurationManager`.

    ```csharp
    static void Main(string[] args)
    {
        string message = ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Místní sestavení a spuštění aplikace

1. Nastavte proměnnou prostředí s názvem **ConnectionString** na připojovací řetězec úložiště konfigurace vaší aplikace. Pokud použijete příkazový řádek Windows, spusťte následující příkaz:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Pokud používáte Windows PowerShell, spusťte následující příkaz:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

2. Pokud chcete, aby se změna projevila, restartujte Visual Studio. Stisknutím kombinace kláves CTRL + F5 Sestavte a spusťte konzolovou aplikaci.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili nové úložiště konfigurace aplikace a použili ho v aplikaci .NET Framework konzolu. Další informace o tom, jak používat konfiguraci aplikací, najdete v dalším kurzu, který předvádí ověřování.

> [!div class="nextstepaction"]
> [Spravovaná integrace identit](./howto-integrate-azure-managed-service-identity.md)
