---
title: 'Kurz: Použití dynamické konfigurace v aplikaci .NET Core'
titleSuffix: Azure App Configuration
description: V tomto kurzu se dozvíte, jak dynamicky aktualizovat konfigurační data pro aplikace .NET Core
services: azure-app-configuration
documentationcenter: ''
author: abarora
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: abarora
ms.openlocfilehash: afecc84748ae8ce85c07e3b482bd9b596bdca251
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75433680"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-core-app"></a>Kurz: Použití dynamické konfigurace v aplikaci .NET Core

Klientská knihovna Client Configuration .NET Core podporuje aktualizaci sady nastavení konfigurace na vyžádání, aniž by došlo k restartování aplikace. To lze implementovat tak, `IConfigurationRefresher` že nejprve získáte instanci z možností pro poskytovatele konfigurace a pak voláte `Refresh` tuto instanci kdekoli ve vašem kódu.

Chcete-li zachovat aktualizace nastavení a vyhnout se příliš mnoho volání do úložiště konfigurace, mezipaměti se používá pro každé nastavení. Dokud nevyprší hodnota nastavení uložená v mezipaměti, operace aktualizace neaktualizuje hodnotu, a to ani v případě, že se hodnota změnila v úložišti konfigurace. Výchozí doba vypršení platnosti pro každý požadavek je 30 sekund, ale v případě potřeby může být přepsána.

Tento kurz ukazuje, jak můžete implementovat dynamické aktualizace konfigurace v kódu. Vychází z aplikace zavedené v rychlých startech. Než budete pokračovat, nejprve [dokončete vytvoření aplikace .NET Core s konfigurací aplikace.](./quickstart-dotnet-core-app.md)

Můžete použít libovolný editor kódu k tomu kroky v tomto kurzu. [Visual Studio Code](https://code.visualstudio.com/) je vynikající možnost, která je k dispozici na platformách Windows, macOS a Linux.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavte aplikaci .NET Core tak, aby aktualizovala svou konfiguraci v reakci na změny v úložišti konfigurace aplikací.
> * Využijte nejnovější konfiguraci v aplikaci.

## <a name="prerequisites"></a>Požadavky

Chcete-li tento kurz provést, nainstalujte sadu [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Opětovné načtení dat z konfigurace aplikace

Otevřete *Program.cs* a aktualizujte soubor, `System.Threading.Tasks` chcete-li přidat odkaz `AddAzureAppConfiguration` na obor názvů, určit `Refresh` konfiguraci aktualizace v metodě a spustit ruční aktualizaci pomocí metody.

```csharp
using System;
using System.Threading.Tasks;

namespace TestConsole
{
class Program
{
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;

    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                    .ConfigureRefresh(refresh =>
                    {
                        refresh.Register("TestApp:Settings:Message")
                               .SetCacheExpiration(TimeSpan.FromSeconds(10));
                    });
                    
                    _refresher = options.GetRefresher();
        });

        _configuration = builder.Build();
        PrintMessage().Wait();
    }

    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.Refresh();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
}
}
```

Metoda `ConfigureRefresh` se používá k určení nastavení použitého k aktualizaci konfiguračních dat pomocí úložiště konfigurace aplikace při aktivaci operace aktualizace. Instance `IConfigurationRefresher` lze načíst voláním `GetRefresher` metody na možnosti `AddAzureAppConfiguration` poskytované metody `Refresh` a metoda v této instanci lze použít k aktivaci operace aktualizace kdekoli v kódu.
    
> [!NOTE]
> Výchozí doba vypršení platnosti mezipaměti pro nastavení konfigurace je 30 `SetCacheExpiration` sekund, ale může být přepsána voláním metody inicializátoru možností předané jako argument `ConfigureRefresh` metody.

## <a name="build-and-run-the-app-locally"></a>Sestavení a spuštění aplikace místně

1. Nastavte proměnnou prostředí s názvem **ConnectionString**a nastavte ji na přístupový klíč do úložiště konfigurace aplikace. Pokud používáte příkazový řádek systému Windows, spusťte následující příkaz a restartujte příkazový řádek, aby se změna projevila:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Pokud používáte prostředí Windows PowerShell, spusťte následující příkaz:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Pokud používáte macOS nebo Linux, spusťte následující příkaz:

        export ConnectionString='connection-string-of-your-app-configuration-store'

1. Chcete-li vytvořit konzolovou aplikaci, spusťte následující příkaz:

        dotnet build

1. Po úspěšném dokončení sestavení spusťte následující příkaz a spusťte aplikaci místně:

        dotnet run

    ![Spuštění aplikace QuickStart místní](./media/quickstarts/dotnet-core-app-run.png)

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **Všechny prostředky**a vyberte instanci úložiště konfigurace aplikace, kterou jste vytvořili v rychlém startu.

1. Vyberte **Průzkumník konfigurace**a aktualizujte hodnoty následujících klíčů:

    | Klíč | Hodnota |
    |---|---|
    | testapp:nastavení:zpráva | Data z konfigurace aplikací Azure – aktualizována |

1. Stisknutím klávesy Enter aktivujte aktualizaci a vytiskněte aktualizovanou hodnotu v okně Příkazového řádku nebo Prostředí PowerShell.

    ![Aktualizace aplikace QuickStart místní](./media/quickstarts/dotnet-core-app-run-refresh.png)
    
    > [!NOTE]
    > Vzhledem k tomu, že doba vypršení `SetCacheExpiration` platnosti mezipaměti byla nastavena na 10 sekund pomocí metody při zadávání konfigurace pro operaci aktualizace, bude hodnota pro nastavení konfigurace aktualizována pouze v případě, že od poslední aktualizace pro toto nastavení uplynulo alespoň 10 sekund.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste povolili aplikaci .NET Core dynamicky aktualizovat nastavení konfigurace z konfigurace aplikace. Chcete-li se dozvědět, jak používat spravovanou identitu Azure ke zjednodušení přístupu ke konfiguraci aplikací, pokračujte dalším kurzem.

> [!div class="nextstepaction"]
> [Integrace spravované identity](./howto-integrate-azure-managed-service-identity.md)
