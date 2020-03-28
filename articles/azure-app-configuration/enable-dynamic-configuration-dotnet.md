---
title: 'Kurz rozhraní .NET Framework: dynamická konfigurace v konfiguraci aplikací Azure'
description: V tomto kurzu se dozvíte, jak dynamicky aktualizovat konfigurační data pro aplikace rozhraní .NET Framework pomocí konfigurace aplikací Azure.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: lcozzens
ms.openlocfilehash: 7780bdbc92868f62e8d066d171b2a04fe06a981d
ms.sourcegitcommit: 940e16ff194d5163f277f98d038833b1055a1a3e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2020
ms.locfileid: "80245799"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-framework-app"></a>Kurz: Použití dynamické konfigurace v aplikaci rozhraní .NET Framework

Klientská knihovna Konfigurace aplikace .NET podporuje aktualizaci sady nastavení konfigurace na vyžádání, aniž by došlo k restartování aplikace. To lze implementovat tak, `IConfigurationRefresher` že nejprve získáte instanci z možností pro poskytovatele konfigurace a pak voláte `Refresh` tuto instanci kdekoli ve vašem kódu.

Chcete-li zachovat aktualizace nastavení a vyhnout se příliš mnoho volání do úložiště konfigurace, mezipaměti se používá pro každé nastavení. Dokud nevyprší hodnota nastavení uložená v mezipaměti, operace aktualizace neaktualizuje hodnotu, a to ani v případě, že se hodnota změnila v úložišti konfigurace. Výchozí doba vypršení platnosti pro každý požadavek je 30 sekund, ale v případě potřeby může být přepsána.

Tento kurz ukazuje, jak můžete implementovat dynamické aktualizace konfigurace v kódu. Vychází z aplikace zavedené v rychlých startech. Než budete pokračovat, nejprve [dokončete vytvoření aplikace .NET Framework s konfigurací aplikace.](./quickstart-dotnet-app.md)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavte aplikaci .NET Framework tak, aby aktualizovala svou konfiguraci v reakci na změny v úložišti konfigurace aplikací.
> * Vložte nejnovější konfiguraci do aplikace.
## <a name="prerequisites"></a>Požadavky

- Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [Rozhraní .NET Framework 4.7.1 nebo novější](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Vytvoření úložiště konfigurace aplikací

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Vyberte **Průzkumník** > konfigurace **+ Vytvořit** > **klíč-hodnotu** přidat následující dvojice klíč-hodnota:

    | Klíč | Hodnota |
    |---|---|
    | testapp:nastavení:zpráva | Data z konfigurace aplikací Azure |

    Ponechte **popisek** a **typ obsahu** prozatím prázdný.

7. Vyberte **Použít**.

## <a name="create-a-net-framework-console-app"></a>Vytvoření konzolové aplikace rozhraní .NET Framework

1. Spusťte Visual Studio a vyberte **Soubor** > **nový** > **projekt**.

1. V **části Vytvořit nový projekt**vyfiltrujte typ projektu **konzoly** a klikněte na **Konzolová aplikace (.NET Framework).** Klikněte na **Další**.

1. V **okně Konfigurace nového projektu**zadejte název projektu. V **části Framework**vyberte rozhraní **.NET Framework 4.7.1** nebo vyšší. Klikněte na **Vytvořit**.

## <a name="reload-data-from-app-configuration"></a>Opětovné načtení dat z konfigurace aplikace
1. Klikněte pravým tlačítkem myši na projekt a vyberte **spravovat balíčky NuGet**. Na kartě **Procházet** vyhledejte a přidejte balíček *Microsoft.Extensions.Configuration.AzureAppConfiguration* NuGet do projektu. Pokud ji nemůžete najít, zaškrtněte políčko **Zahrnout předběžnou verzi.**

1. Otevřete *Program.cs*a přidejte odkaz na zprostředkovatele konfigurace aplikace .NET Core.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Přidejte dvě proměnné pro uložení objektů souvisejících s konfigurací.

    ```csharp
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;
    ```

1. Aktualizujte `Main` metodu pro připojení ke konfiguraci aplikace pomocí zadaných možností aktualizace.

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
    Metoda `ConfigureRefresh` se používá k určení nastavení použitého k aktualizaci konfiguračních dat pomocí úložiště konfigurace aplikace při aktivaci operace aktualizace. Instance `IConfigurationRefresher` lze načíst voláním `GetRefresher` metody na možnosti `AddAzureAppConfiguration` poskytované metody `Refresh` a metodu v této instanci lze použít k aktivaci operace aktualizace kdekoli v kódu.

    > [!NOTE]
    > Výchozí doba vypršení platnosti mezipaměti pro nastavení konfigurace je 30 `SetCacheExpiration` sekund, ale může být přepsána voláním metody inicializátoru možností předané jako argument `ConfigureRefresh` metody.

1. Přidejte volanou `PrintMessage()` metodu, která spustí ruční aktualizaci konfiguračních dat z konfigurace aplikace.

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

## <a name="build-and-run-the-app-locally"></a>Sestavení a spuštění aplikace místně

1. Nastavte proměnnou prostředí s názvem **ConnectionString**a nastavte ji na přístupový klíč do úložiště konfigurace aplikace. Pokud používáte příkazový řádek systému Windows, spusťte následující příkaz a restartujte příkazový řádek, aby se změna projevila:

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Pokud používáte prostředí Windows PowerShell, spusťte následující příkaz:

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

1. Restartujte visual studio, aby se změna projevila. 

1. Stisknutím kláves Ctrl + F5 vytvořte a spusťte konzolovou aplikaci.

    ![Spuštění aplikace místní](./media/dotnet-app-run.png)

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyberte **Všechny prostředky**a vyberte instanci úložiště konfigurace aplikace, kterou jste vytvořili v rychlém startu.

1. Vyberte **Průzkumník konfigurace**a aktualizujte hodnoty následujících klíčů:

    | Klíč | Hodnota |
    |---|---|
    | testapp:nastavení:zpráva | Data z konfigurace aplikací Azure – aktualizována |

1. V běžící aplikaci stiskněte klávesu Enter a aktivujte aktualizaci a vytiskněte aktualizovanou hodnotu v okně Příkazový řádek nebo PowerShell.

    ![Aktualizace aplikace místní](./media/dotnet-app-run-refresh.png)
    
    > [!NOTE]
    > Vzhledem k tomu, že doba vypršení `SetCacheExpiration` platnosti mezipaměti byla nastavena na 10 sekund pomocí metody při zadávání konfigurace pro operaci aktualizace, bude hodnota pro nastavení konfigurace aktualizována pouze v případě, že od poslední aktualizace pro toto nastavení uplynulo alespoň 10 sekund.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste povolili aplikaci .NET Framework dynamicky aktualizovat nastavení konfigurace z konfigurace aplikace. Chcete-li se dozvědět, jak používat spravovanou identitu Azure ke zjednodušení přístupu ke konfiguraci aplikací, pokračujte dalším kurzem.

> [!div class="nextstepaction"]
> [Integrace spravované identity](./howto-integrate-azure-managed-service-identity.md)
