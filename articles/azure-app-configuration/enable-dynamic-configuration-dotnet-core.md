---
title: Kurz použití dynamické konfigurace konfigurace aplikací Azure v aplikaci .NET Core | Microsoft Docs
description: V tomto kurzu se naučíte dynamicky aktualizovat konfigurační data pro aplikace .NET Core.
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
ms.openlocfilehash: 3eee34f594cb23a8b64f6fd10837c9a641eda62d
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71075975"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-core-app"></a>Kurz: Použití dynamické konfigurace v aplikaci .NET Core

Klientská knihovna .NET Core konfigurace aplikace podporuje aktualizaci sady nastavení konfigurace na vyžádání, aniž by způsobila restartování aplikace. To může být implementováno tak, že nejprve získá `IConfigurationRefresher` instanci z možností pro poskytovatele konfigurace a potom zavolá `Refresh` tuto instanci kdekoli v kódu.

Aby se nastavení zachovalo jako aktualizované a zabránilo se příliš velkému počtu volání do úložiště konfigurace, použije se pro každé nastavení mezipaměť. Dokud neuplyne hodnota nastavení uložené v mezipaměti, operace aktualizace neaktualizuje hodnotu, a to ani v případě, že se hodnota v úložišti konfigurace změnila. Výchozí doba vypršení platnosti každé žádosti je 30 sekund, ale v případě potřeby může být přepsána.

V tomto kurzu se dozvíte, jak můžete implementovat aktualizace dynamické konfigurace do kódu. Sestavuje se v aplikaci představené v rychlých startech. Než budete pokračovat, dokončete nejprve [Vytvoření aplikace .NET Core pomocí konfigurace aplikace](./quickstart-dotnet-core-app.md) .

K provedení kroků v tomto kurzu můžete použít libovolný editor kódu. [Visual Studio Code](https://code.visualstudio.com/) je vynikající možnost, která je dostupná na platformách Windows, MacOS a Linux.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavte svoji aplikaci tak, aby aktualizovala konfiguraci pomocí úložiště konfigurace aplikace na vyžádání.
> * Vloží nejnovější konfiguraci do řadičů vaší aplikace.

## <a name="prerequisites"></a>Požadavky

K provedení tohoto kurzu nainstalujte [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Znovu načíst data z konfigurace aplikace

Otevřete *program.cs* a aktualizujte soubor pro přidání odkazu na `System.Threading.Tasks` obor názvů, pro zadání konfigurace `AddAzureAppConfiguration` aktualizace v metodě `Refresh` a aktivaci ruční aktualizace pomocí metody.

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
        IConfigurationRefresher refresher = null;

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

`ConfigureRefresh` Metoda se používá k určení nastavení, která se použijí k aktualizaci konfiguračních dat pomocí úložiště konfigurace aplikace při aktivaci operace aktualizace. Instance `IConfigurationRefresher` může být načtena voláním `GetRefresher` metody `AddAzureAppConfiguration` na možnosti poskytované metodě a `Refresh` metoda v této instanci může být použita k aktivaci operace aktualizace kdekoli v kódu.
    
> [!NOTE]
> Výchozí doba vypršení platnosti mezipaměti pro konfigurační nastavení je 30 sekund, ale lze ji přepsat voláním `SetCacheExpiration` metody v inicializátoru možnosti předaného jako argument `ConfigureRefresh` metody.

## <a name="build-and-run-the-app-locally"></a>Místní sestavení a spuštění aplikace

1. Nastavte proměnnou prostředí s názvem **ConnectionString**a nastavte ji na přístupový klíč na úložiště konfigurace aplikace. Použijete-li příkazový řádek systému Windows, spusťte následující příkaz a restartujte příkazový řádek, aby se změna projevila:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Pokud používáte Windows PowerShell, spusťte následující příkaz:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Pokud používáte macOS nebo Linux, spusťte následující příkaz:

        export ConnectionString='connection-string-of-your-app-configuration-store'

1. Spusťte následující příkaz, který sestaví konzolovou aplikaci:

        dotnet build

1. Po úspěšném dokončení sestavení spusťte následující příkaz pro místní spuštění aplikace:

        dotnet run

    ![Spuštění aplikace pro rychlý Start – místní](./media/quickstarts/dotnet-core-app-run.png)

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **všechny prostředky**a vyberte instanci úložiště konfigurace aplikace, kterou jste vytvořili v rychlém startu.

1. Vyberte **Průzkumník konfigurace**a aktualizujte hodnoty následujících klíčů:

    | Klíč | Value |
    |---|---|
    | TestApp: nastavení: zpráva | Data z konfigurace aplikace Azure – Aktualizováno |

1. Stiskněte klávesu ENTER, aby se aktivovala aktualizace a tiskla aktualizovaná hodnota v příkazovém řádku nebo v okně PowerShellu.

    ![Rychlé obnovení místní aktualizace aplikace](./media/quickstarts/dotnet-core-app-run-refresh.png)
    
    > [!NOTE]
    > Vzhledem k tomu, že doba vypršení platnosti mezipaměti byla nastavena `SetCacheExpiration` na 10 sekund pomocí metody při určování konfigurace pro operaci aktualizace, bude hodnota nastavení konfigurace aktualizována pouze v případě, že od poslední aktualizace uplynula alespoň 10 sekund. pro toto nastavení.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přidali identitu spravované služby Azure, abyste zjednodušili přístup ke konfiguraci aplikací a vylepšili správu přihlašovacích údajů pro vaši aplikaci. Další informace o tom, jak používat konfiguraci aplikací, najdete v ukázkách Azure CLI.

> [!div class="nextstepaction"]
> [Ukázky rozhraní příkazového řádku](./cli-samples.md)
