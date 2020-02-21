---
title: '.NET Framework kurz: dynamická konfigurace v konfiguraci aplikace Azure'
description: V tomto kurzu se naučíte dynamicky aktualizovat konfigurační data pro .NET Framework aplikace pomocí konfigurace aplikací Azure.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: lcozzens
ms.openlocfilehash: 7ba3eae4ea5557b4bb1b1be4e2c79eab8f6e7988
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484872"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-framework-app"></a>Kurz: použití dynamické konfigurace v aplikaci .NET Framework

Klientská knihovna .NET pro konfiguraci aplikací podporuje aktualizaci sady nastavení konfigurace na vyžádání, aniž by způsobila restartování aplikace. To může být implementováno pomocí prvního získání instance `IConfigurationRefresher` z možností pro poskytovatele konfigurace a potom volání `Refresh` na této instanci kdekoli ve vašem kódu.

Aby se nastavení zachovalo jako aktualizované a zabránilo se příliš velkému počtu volání do úložiště konfigurace, použije se pro každé nastavení mezipaměť. Dokud neuplyne hodnota nastavení uložené v mezipaměti, operace aktualizace neaktualizuje hodnotu, a to ani v případě, že se hodnota v úložišti konfigurace změnila. Výchozí doba vypršení platnosti každé žádosti je 30 sekund, ale v případě potřeby může být přepsána.

V tomto kurzu se dozvíte, jak můžete implementovat aktualizace dynamické konfigurace do kódu. Sestavuje se v aplikaci představené v rychlých startech. Než budete pokračovat, dokončete nejprve [Vytvoření aplikace .NET Framework s konfigurací aplikace](./quickstart-dotnet-app.md) .

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavte si aplikaci .NET Framework pro aktualizaci konfigurace v reakci na změny v úložišti konfigurace aplikace.
> * Vloží do aplikace nejnovější konfiguraci.
## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.1 nebo novější](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikace

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Vyberte **Průzkumníka konfigurace** >  **+ vytvořit** a přidejte následující páry klíč-hodnota:

    | Klíč | Hodnota |
    |---|---|
    | TestApp: nastavení: zpráva | Data z konfigurace aplikace Azure |

    V tuto chvíli ponechat **popisek** a **typ obsahu** prázdné.

## <a name="create-a-net-framework-console-app"></a>Vytvoření konzolové aplikace .NET Framework

1. Spusťte Visual Studio a vyberte **soubor** > **Nový** > **projekt**.

1. V části **vytvořit nový projekt**, vyfiltrujte typ projektu **konzoly** a klikněte na **Konzolová aplikace (.NET Framework)** . Klikněte na **Další**.

1. V **konfiguraci nového projektu**zadejte název projektu. V části **rozhraní**vyberte **.NET Framework 4.7.1** nebo vyšší. Klikněte na možnost **Vytvořit**.

## <a name="reload-data-from-app-configuration"></a>Znovu načíst data z konfigurace aplikace
1. Klikněte pravým tlačítkem na projekt a vyberte **Spravovat balíčky NuGet**. Na kartě **Procházet** vyhledejte a do svého projektu přidejte balíček NuGet *Microsoft. Extensions. Configuration. AzureAppConfiguration* . Pokud nemůžete najít, zaškrtněte políčko **zahrnout předběžné verze** .

1. Otevřete *program.cs*a přidejte odkaz na poskytovatele konfigurace aplikace .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Přidejte dvě proměnné pro uložení objektů souvisejících s konfigurací.

    ```csharp
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;
    ```

1. Aktualizujte metodu `Main` pro připojení k konfiguraci aplikace se zadanými možnostmi aktualizace.

    ```csharp
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
    ```
    Metoda `ConfigureRefresh` slouží k určení nastavení použitých k aktualizaci konfiguračních dat pomocí úložiště konfigurace aplikace při aktivaci operace aktualizace. Instance `IConfigurationRefresher` lze načíst voláním metody `GetRefresher` na možnosti poskytované `AddAzureAppConfiguration` metodě a metoda `Refresh` této instance může být použita k aktivaci operace aktualizace kdekoli v kódu.

    > [!NOTE]
    > Výchozí doba vypršení platnosti mezipaměti pro konfigurační nastavení je 30 sekund, ale lze ji přepsat voláním metody `SetCacheExpiration` v inicializátoru možnosti předaného jako argument metody `ConfigureRefresh`.

1. Přidejte metodu nazvanou `PrintMessage()`, která spustí ruční aktualizaci konfiguračních dat z konfigurace aplikace.

    ```csharp
    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.Refresh();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Místní sestavení a spuštění aplikace

1. Nastavte proměnnou prostředí s názvem **ConnectionString**a nastavte ji na přístupový klíč na úložiště konfigurace aplikace. Použijete-li příkazový řádek systému Windows, spusťte následující příkaz a restartujte příkazový řádek, aby se změna projevila:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Pokud používáte Windows PowerShell, spusťte následující příkaz:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

1. Pokud chcete, aby se změna projevila, restartujte Visual Studio. 

1. Stisknutím kombinace kláves CTRL + F5 Sestavte a spusťte konzolovou aplikaci.

    ![Místní spuštění aplikace](./media/dotnet-app-run.png)

1. Přihlaste se k webu [Portál Azure](https://portal.azure.com). Vyberte **všechny prostředky**a vyberte instanci úložiště konfigurace aplikace, kterou jste vytvořili v rychlém startu.

1. Vyberte **Průzkumník konfigurace**a aktualizujte hodnoty následujících klíčů:

    | Klíč | Hodnota |
    |---|---|
    | TestApp: nastavení: zpráva | Data z konfigurace aplikace Azure – Aktualizováno |

1. Vraťte se do běžící aplikace stisknutím klávesy ENTER aktivujte aktualizaci a vytisknete aktualizovanou hodnotu v příkazovém řádku nebo v okně PowerShellu.

    ![Místní aktualizace aplikace](./media/dotnet-app-run-refresh.png)
    
    > [!NOTE]
    > Vzhledem k tomu, že doba vypršení platnosti mezipaměti byla nastavena na 10 sekund pomocí metody `SetCacheExpiration` při určování konfigurace pro operaci aktualizace, bude hodnota nastavení konfigurace aktualizována pouze v případě, že od poslední aktualizace tohoto nastavení uplynula alespoň 10 sekund.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste povolili aplikaci .NET Framework k dynamické aktualizaci nastavení konfigurace z konfigurace aplikace. Další informace o tom, jak používat spravovanou identitu Azure ke zjednodušení přístupu ke konfiguraci aplikace, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Spravovaná integrace identit](./howto-integrate-azure-managed-service-identity.md)
