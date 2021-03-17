---
title: 'Kurz: použití dynamické konfigurace pomocí aktualizace Push v aplikaci .NET Core'
titleSuffix: Azure App Configuration
description: V tomto kurzu se naučíte dynamicky aktualizovat konfigurační data pro aplikace .NET Core pomocí aktualizace push.
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
ms.date: 07/25/2020
ms.author: abarora
ms.openlocfilehash: 977982bf1a36b4b85524df2513f2272fe4a8d1bf
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101701514"
---
# <a name="tutorial-use-dynamic-configuration-using-push-refresh-in-a-net-core-app"></a>Kurz: použití dynamické konfigurace pomocí aktualizace Push v aplikaci .NET Core

Klientská knihovna .NET Core konfigurace aplikace podporuje aktualizaci konfigurace na vyžádání, aniž by způsobila restartování aplikace. Aplikaci lze nakonfigurovat tak, aby zjistila změny v konfiguraci aplikace pomocí jednoho nebo obou následujících dvou přístupů.

1. Model cyklického dotazování: Jedná se o výchozí chování, které používá cyklické dotazování k detekci změn v konfiguraci. Jakmile hodnota nastavení v mezipaměti vyprší, další volání `TryRefreshAsync` nebo `RefreshAsync` pošle požadavek serveru, aby zkontroloval, jestli se konfigurace změnila, a v případě potřeby načte aktualizovanou konfiguraci.

1. Model nabízených oznámení: používá [události konfigurace aplikace](./concept-app-configuration-event.md) k detekci změn v konfiguraci. Jakmile je konfigurace aplikace nastavená tak, aby odesílala události změny hodnot klíčů Azure Event Grid, může aplikace tyto události použít k optimalizaci celkového počtu požadavků nutných k aktualizaci konfigurace. Aplikace se můžou zvolit, aby se k nim přihlásili buď přímo z Event Grid, nebo když některou z [podporovaných obslužných rutin událostí](../event-grid/event-handlers.md) , jako je Webhook, funkce Azure nebo Service Bus téma.

Aplikace se můžou rozhodnout k přihlášení k odběru těchto událostí buď přímo z Event Grid, nebo prostřednictvím webového zavěšení, nebo předáním událostí do Azure Service Bus. Sada Azure Service Bus SDK poskytuje rozhraní API k registraci obslužné rutiny zpráv, která tento proces zjednodušuje pro aplikace, které nemají koncový bod HTTP, nebo nechtějí dotazovat se na průběžné změny v mřížce událostí.

V tomto kurzu se dozvíte, jak můžete implementovat aktualizace dynamické konfigurace v kódu pomocí aktualizace push. Sestavuje se v aplikaci představené v rychlých startech. Než budete pokračovat, dokončete nejprve [Vytvoření aplikace .NET Core pomocí konfigurace aplikace](./quickstart-dotnet-core-app.md) .

K provedení kroků v tomto kurzu můžete použít libovolný editor kódu. [Visual Studio Code](https://code.visualstudio.com/) je vynikající možnost, která je dostupná na platformách Windows, MacOS a Linux.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nastavení předplatného pro odesílání událostí změny konfigurace z konfigurace aplikace do tématu Service Bus
> * Nastavte si aplikaci .NET Core, abyste aktualizovali její konfiguraci v reakci na změny v konfiguraci aplikace.
> * Využijte ve své aplikaci nejnovější konfiguraci.

## <a name="prerequisites"></a>Požadavky

K provedení tohoto kurzu nainstalujte [.NET Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-azure-service-bus-topic-and-subscription"></a>Nastavení Azure Service Busho tématu a předplatného

V tomto kurzu se používá Service Bus Integration pro Event Grid ke zjednodušení detekce změn konfigurace pro aplikace, u kterých nechcete provádět dotazování konfigurace aplikace nepřetržitě. Sada Azure Service Bus SDK poskytuje rozhraní API k registraci obslužné rutiny zpráv, kterou je možné použít k aktualizaci konfigurace při zjištění změn v konfiguraci aplikace. Postupujte podle kroků v [rychlém startu: pomocí Azure Portal vytvořte Service Bus téma a předplatné](../service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal.md) pro vytvoření oboru názvů služby Service Bus, tématu a předplatného.

Po vytvoření prostředků přidejte následující proměnné prostředí. Budou použity k registraci obslužné rutiny události pro změny konfigurace v kódu aplikace.

| Klíč | Hodnota |
|---|---|
| ServiceBusConnectionString | Připojovací řetězec pro obor názvů služby Service Bus |
| ServiceBusTopic | Název Service Busho tématu |
| ServiceBusSubscription | Název předplatného služby Service Bus |

## <a name="set-up-event-subscription"></a>Nastavení předplatného události

1. Otevřete prostředek konfigurace aplikace v Azure Portal a potom v podokně klikněte na zapnuto `+ Event Subscription` `Events` .

    ![Události konfigurace aplikace](./media/events-pane.png)

1. Zadejte název `Event Subscription` a `System Topic` .

    ![Vytvořit odběr události](./media/create-event-subscription.png)

1. Vyberte `Endpoint Type` jako `Service Bus Topic` , zvolte Service Bus téma a pak klikněte na `Confirm Selection` .

    ![Koncový bod služby Service Bus pro odběr událostí](./media/event-subscription-servicebus-endpoint.png)

1. Kliknutím na tlačítko `Create` Vytvořte odběr události.

1. Kliknutím na zapnout `Event Subscriptions` v `Events` podokně ověříte, že se předplatné úspěšně vytvořilo.

    ![Odběry událostí konfigurace aplikace](./media/event-subscription-view.png)

> [!NOTE]
> Při přihlášení k odběru změn konfigurace je možné použít jeden nebo více filtrů ke snížení počtu událostí odeslaných do aplikace. Ty můžete nakonfigurovat buď jako [Event Grid filtry předplatného](../event-grid/event-filtering.md) , nebo [Service Bus filtry předplatného](../service-bus-messaging/topic-filters.md). Například filtr předplatného se dá použít jenom k přihlášení k odběru událostí pro změny v klíči, který začíná specifickým řetězcem.

## <a name="register-event-handler-to-reload-data-from-app-configuration"></a>Registrovat obslužnou rutinu události pro opětovné načtení dat z konfigurace aplikace

Otevřete *program.cs* a aktualizujte soubor pomocí následujícího kódu.

```csharp
using Microsoft.Azure.ServiceBus;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Configuration.AzureAppConfiguration;
using System;
using System.Diagnostics;
using System.Text;
using System.Text.Json;
using System.Threading.Tasks;

namespace TestConsole
{
    class Program
    {
        private const string AppConfigurationConnectionStringEnvVarName = "AppConfigurationConnectionString"; // e.g. Endpoint=https://{store_name}.azconfig.io;Id={id};Secret={secret}
        private const string ServiceBusConnectionStringEnvVarName = "ServiceBusConnectionString"; // e.g. Endpoint=sb://{service_bus_name}.servicebus.windows.net/;SharedAccessKeyName={key_name};SharedAccessKey={key}
        private const string ServiceBusTopicEnvVarName = "ServiceBusTopic";
        private const string ServiceBusSubscriptionEnvVarName = "ServiceBusSubscription";

        private static IConfigurationRefresher _refresher = null;

        static async Task Main(string[] args)
        {
            string appConfigurationConnectionString = Environment.GetEnvironmentVariable(AppConfigurationConnectionStringEnvVarName);

            IConfiguration configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options =>
                {
                    options.Connect(appConfigurationConnectionString);
                    options.ConfigureRefresh(refresh =>
                        refresh
                            .Register("TestApp:Settings:Message")
                            .SetCacheExpiration(TimeSpan.FromDays(30))  // Important: Reduce poll frequency
                    );

                    _refresher = options.GetRefresher();
                }).Build();

            RegisterRefreshEventHandler();
            var message = configuration["TestApp:Settings:Message"];
            Console.WriteLine($"Initial value: {configuration["TestApp:Settings:Message"]}");

            while (true)
            {
                await _refresher.TryRefreshAsync();

                if (configuration["TestApp:Settings:Message"] != message)
                {
                    Console.WriteLine($"New value: {configuration["TestApp:Settings:Message"]}");
                    message = configuration["TestApp:Settings:Message"];
                }
                
                await Task.Delay(TimeSpan.FromSeconds(1));
            }
        }

        private static void RegisterRefreshEventHandler()
        {
            string serviceBusConnectionString = Environment.GetEnvironmentVariable(ServiceBusConnectionStringEnvVarName);
            string serviceBusTopic = Environment.GetEnvironmentVariable(ServiceBusTopicEnvVarName);
            string serviceBusSubscription = Environment.GetEnvironmentVariable(ServiceBusSubscriptionEnvVarName);
            SubscriptionClient serviceBusClient = new SubscriptionClient(serviceBusConnectionString, serviceBusTopic, serviceBusSubscription);

            serviceBusClient.RegisterMessageHandler(
                handler: (message, cancellationToken) =>
               {
                   string messageText = Encoding.UTF8.GetString(message.Body);
                   JsonElement messageData = JsonDocument.Parse(messageText).RootElement.GetProperty("data");
                   string key = messageData.GetProperty("key").GetString();
                   Console.WriteLine($"Event received for Key = {key}");

                   _refresher.SetDirty();
                   return Task.CompletedTask;
               },
                exceptionReceivedHandler: (exceptionargs) =>
                {
                    Console.WriteLine($"{exceptionargs.Exception}");
                    return Task.CompletedTask;
                });
        }
    }
}
```

Metoda [SetDirty](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.iconfigurationrefresher.setdirty) slouží k nastavení hodnoty v mezipaměti pro klíčové hodnoty zaregistrované pro obnovení jako nečisté. Tím se zajistí, že se další volání `RefreshAsync` nebo `TryRefreshAsync` znovu ověří hodnoty v mezipaměti s konfigurací aplikace a v případě potřeby je aktualizuje.

Náhodné zpoždění je přidáno před tím, než je hodnota uložená v mezipaměti označena jako nečistá, aby se snížilo potenciální omezení pro případ, že se aktualizuje více instancí současně. Výchozí maximální zpoždění před hodnotou, která je uložena v mezipaměti, je označena jako nečistá hodnota 30 sekund, ale lze ji přepsat předáním volitelného `TimeSpan` parametru `SetDirty` metodě.

> [!NOTE]
> Chcete-li snížit počet požadavků na konfiguraci aplikace při použití nabízené aktualizace, je důležité volat `SetCacheExpiration(TimeSpan cacheExpiration)` s odpovídající hodnotou `cacheExpiration` parametru. Tato operace řídí dobu vypršení platnosti mezipaměti pro získání aktualizace a lze ji použít jako bezpečnostní síť pro případ, že dojde k potížím s odběrem události nebo předplatným Service Bus. Doporučená hodnota je `TimeSpan.FromDays(30)` .

## <a name="build-and-run-the-app-locally"></a>Místní sestavení a spuštění aplikace

1. Nastavte proměnnou prostředí s názvem **AppConfigurationConnectionString** a nastavte ji na přístupový klíč na úložiště konfigurace aplikace. Použijete-li příkazový řádek systému Windows, spusťte následující příkaz a restartujte příkazový řádek, aby se změna projevila:

    ```console
     setx AppConfigurationConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Pokud používáte Windows PowerShell, spusťte následující příkaz:

    ```powershell
     $Env:AppConfigurationConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Pokud používáte macOS nebo Linux, spusťte následující příkaz:

    ```console
     export AppConfigurationConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. Spusťte následující příkaz, který sestaví konzolovou aplikaci:

    ```console
     dotnet build
    ```

1. Po úspěšném dokončení sestavení spusťte následující příkaz pro místní spuštění aplikace:

    ```console
     dotnet run
    ```

    ![Spustit aktualizaci před aktualizací](./media/dotnet-core-app-pushrefresh-initial.png)

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Vyberte **všechny prostředky** a vyberte instanci úložiště konfigurace aplikace, kterou jste vytvořili v rychlém startu.

1. Vyberte **Průzkumník konfigurace** a aktualizujte hodnoty následujících klíčů:

    | Klíč | Hodnota |
    |---|---|
    | TestApp: nastavení: zpráva | Data z konfigurace aplikace Azure – Aktualizováno |

1. Počkejte 30 sekund, než se povolí zpracování události a konfigurace se aktualizuje.

    ![Spustit aktualizaci po aktualizaci](./media/dotnet-core-app-pushrefresh-final.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste povolili aplikaci .NET Core dynamickou aktualizaci nastavení konfigurace z konfigurace aplikace. Další informace o tom, jak používat spravovanou identitu Azure ke zjednodušení přístupu ke konfiguraci aplikace, najdete v dalším kurzu.

> [!div class="nextstepaction"]
> [Spravovaná integrace identit](./howto-integrate-azure-managed-service-identity.md)