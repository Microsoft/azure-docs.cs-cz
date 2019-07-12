---
title: Kurz pro používání v aplikaci .NET Core dynamickou konfiguraci konfigurace aplikace pro Azure | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak se dynamicky aktualizovat konfigurační data pro aplikace .NET Core
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
ms.openlocfilehash: 1649fefda5073761d616fc48c602cab84d293ed0
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799970"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-core-app"></a>Kurz: Použití dynamické konfigurace v aplikaci .NET Core

Klientské knihovny .NET Core konfigurace aplikací podporuje aktualizace sadu konfiguračních nastavení na vyžádání bez způsobení aplikací k restartování. To může být implementována nejprve získáte instance `IConfigurationRefresher` z možností pro zprostředkovatele konfigurace a potom voláním `Refresh` u dané instance kdekoli ve vašem kódu.

Aby bylo možné zachovat nastavení aktualizována a vyhnout se příliš mnoho volání do konfigurace úložiště, mezipaměť se používá pro každé nastavení. Dokud se hodnota uložená v mezipaměti nastavení vypršela platnost, operace aktualizace neaktualizuje hodnotu, i v případě, že je hodnota změněna v úložišti konfigurace. Výchozí doba vypršení platnosti pro každý požadavek je 30 sekund, ale lze přepsat, pokud je to nutné.

Tento kurz ukazuje, jak můžete implementovat konfigurace dynamické aktualizace ve vašem kódu. Je nástavbou aplikace zavedený rychlých startech. Než budete pokračovat, dokončete [vytvoření aplikace .NET Core s konfigurací aplikace](./quickstart-dotnet-core-app.md) první.

Provést kroky v tomto kurzu můžete použít libovolný editor kódu. [Visual Studio Code](https://code.visualstudio.com/) je skvělou možnost, která je k dispozici ve Windows, macOS a Linux platformy.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavení aplikace k aktualizaci konfigurace s úložištěm konfigurace aplikace na vyžádání.
> * Vloží nejnovější konfiguraci v řadiči vaší aplikace.

## <a name="prerequisites"></a>Požadavky

V tomto kurzu provedete instalaci [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Znovu načíst data z konfigurace aplikace

Otevřít *Program.cs* a aktualizujte soubor k určení konfigurace aktualizace v `AddAzureAppConfiguration` metoda a aktivační události pomocí ruční aktualizace `Refresh` metoda.

```csharp
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
```

`ConfigureRefresh` Metoda se používá k určení nastavení používá k aktualizaci konfiguračních dat s úložištěm konfigurace aplikace, když se aktivuje operace aktualizace. Instance `IConfigurationRefresher` může být načten voláním `GetRefresher` metodu na možnosti pro `AddAzureAppConfiguration` metody a `Refresh` metodu na tuto instanci lze použít k aktivaci operace aktualizace kdekoli v kódu.
    
> [!NOTE]
> Výchozí doba vypršení platnosti mezipaměti pro nastavení konfigurace je 30 sekund, ale lze přepsat pomocí volání `SetCacheExpiration` metodě v inicializátoru možnosti předán jako argument pro `ConfigureRefresh` metody.

## <a name="build-and-run-the-app-locally"></a>Sestavte a spusťte aplikaci místně

1. Nastavte proměnnou prostředí s názvem **ConnectionString**a nastavte ho na přístupový klíč k úložišti konfigurace aplikace. Pokud používáte Windows příkazového řádku, spusťte následující příkaz a restartujte příkazového řádku umožňující změna se projeví:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Pokud používáte prostředí Windows PowerShell, spusťte následující příkaz:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    Pokud používáte systému macOS nebo Linux, spusťte následující příkaz:

        export ConnectionString='connection-string-of-your-app-configuration-store'

1. Spusťte následující příkaz k sestavení aplikace konzoly:

        dotnet build

1. Po úspěšném dokončení sestavení spusťte následující příkaz a spusťte aplikaci místně:

        dotnet run

    ![Místní spuštění aplikace rychlý start](./media/quickstarts/dotnet-core-app-run.png)

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyberte **všechny prostředky**a vyberte instance aplikace, který konfigurace úložiště, který jste vytvořili v tomto rychlém startu.

1. Vyberte **Průzkumník konfigurací**a aktualizujte hodnoty následujících klíčů:

    | Klíč | Value |
    |---|---|
    | TestApp:Settings:Message | Data z aplikací Azure – aktualizované konfigurace |

1. Stisknutím klávesy Enter aktivovat aktualizaci a tisk aktualizované hodnoty v okně příkazového řádku nebo Powershellu.

    ![Místní aktualizace aplikace rychlý start](./media/quickstarts/dotnet-core-app-run-refresh.png)
    
    > [!NOTE]
    > Vzhledem k tomu, že doba vypršení platnosti mezipaměti byl nastaven na 10 sekund pomocí `SetCacheExpiration` metoda při zadávání konfigurace pro operace aktualizace hodnoty pro nastavení konfigurace budou aktualizováni, pouze pokud alespoň 10 sekund uplynuly od poslední aktualizace pro toto nastavení.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přidali do Azure se identita spravované služby pro přístup ke konfiguraci aplikací pro zjednodušení a zlepšení Správa přihlašovacích údajů pro vaši aplikaci. Další informace o tom, jak používat konfiguraci aplikací, i nadále ukázky Azure CLI.

> [!div class="nextstepaction"]
> [Ukázky rozhraní příkazového řádku](./cli-samples.md)
