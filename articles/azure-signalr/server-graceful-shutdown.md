---
title: Zastavte svůj aplikační server korektně.
description: Tento článek poskytuje informace o řádně vypínání aplikačního serveru signálu.
author: terencefan
ms.author: tefa
ms.date: 11/12/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 06aa91ff414e5575f7b1a743d2cc17765437ef72
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2020
ms.locfileid: "97797475"
---
# <a name="server-graceful-shutdown"></a>Bezproblémové vypnutí serveru
Služba signalizace Microsoft Azure poskytuje dva režimy pro bezproblémové vypnutí serveru. 

Klíčovou výhodou použití této funkce je zabránit vašemu zákazníkovi v neočekávaném poklesu připojení. 

Místo toho můžete buď počkat vaše připojení klientů, aby se uzavřela s ohledem na obchodní logiku, nebo dokonce migrovat připojení klienta na jiný server, aniž by došlo ke ztrátě dat. 

## <a name="how-it-works"></a>Jak to funguje

Obecně platí, že v procesu bezproblémového vypnutí budou čtyři fáze:

1. **Nastavení serveru do offline režimu**

    Znamená to, že se na tento server nebudou směrovat žádná další připojení klientů.

2. **Aktivační `OnShutdown` moduly triggerů**

    Můžete zaregistrovat zavěšení vypnutí pro každé centrum, které máte ve vlastnictví svého serveru.
    Budou se volat v souvislosti s registrovaným pořadím hned po získání odpovědi **FINACK** od naší služby Azure Signal. to znamená, že tento server byl ve službě Azure Signal Service nastavený jako offline.

    V této fázi můžete vysílat zprávy nebo provést některé čisticí úlohy, jakmile se spustí všechny zavěšení vypnutí, budeme pokračovat k další fázi.

3. **Počkejte, až budou všechna připojení klientů dokončena**, záleží na zvoleném režimu:

    **Režim nastavený na WaitForClientsToClose**

    Služba signalizace Azure bude obsahovat stávající klienty.

    Možná budete muset navrhnout způsob, třeba vysílat koncovou zprávu všem klientům a pak svým klientům umožnit rozhodování, kdy se má sám zavřít nebo znovu připojit.

    Přečtěte si [ChatSample](https://github.com/Azure/azure-signalr/tree/dev/samples/ChatSample/ChatSample) pro použití ukázkového využití, které pošle zprávu "Exit", která aktivuje ukončení činnosti klienta v zavěšení vypnutí.

    **Režim nastavený na MigrateClients**

    Služba signalizace Azure se pokusí znovu směrovat připojení klienta na tomto serveru na jiný platný server. 
    
    V tomto scénáři `OnConnectedAsync` `OnDisconnectedAsync` se aktivuje na novém serveru a starém serveru se `IConnectionMigrationFeature` sadou `HttpContext` , která se dá použít k určení, jestli se při migraci připojení klienta provádí migrace. Může být užitečná hlavně pro stavové scénáře.

    Po doručení aktuální zprávy se připojení klienta okamžitě migruje, což znamená, že se další zpráva směruje na nový server.

4. **Zastavení připojení serveru**

    Po zavření nebo migraci všech připojení klientů nebo překročení časového limitu (ve výchozím nastavení 30 s),

    Sada SDK serveru pro signalizování bude pokračovat v procesu vypnutí této fáze a ukončí všechna připojení serveru.

    Připojení klienta budou stále vyřazena, pokud se nepodaří ho zavřít nebo migrovat. Například není dokončen žádný vhodný cílový server/aktuální zpráva klienta se serverem.

## <a name="sample-codes"></a>Ukázkové kódy.

Přidejte následující možnosti, když `AddAzureSignalR` :

```csharp
services.AddSignalR().AddAzureSignalR(option =>
{
    option.GracefulShutdown.Mode = GracefulShutdownMode.WaitForClientsClose;
    // option.GracefulShutdown.Mode = GracefulShutdownMode.MigrateClients;
    option.GracefulShutdown.Timeout = TimeSpan.FromSeconds(30);

    option.GracefulShutdown.Add<Chat>(async (c) =>
    {
        await c.Clients.All.SendAsync("exit");
    });
});
```

### <a name="configure-onconnected-and-ondisconnected-while-setting-graceful-shutdown-mode-to-migrateclients"></a>Nakonfigurujte `OnConnected` a `OnDisconnected` při nastavení režimu řádného vypnutí na `MigrateClients` .

Zavedli jsme "IConnectionMigrationFeature", který označuje, jestli se připojení navázáno-zaneslo.

```csharp
public class Chat : Hub {

    public override async Task OnConnectedAsync()
    {
        Console.WriteLine($"{Context.ConnectionId} connected.");

        var feature = Context.GetHttpContext().Features.Get<IConnectionMigrationFeature>();
        if (feature != null)
        {
            Console.WriteLine($"[{feature.MigrateTo}] {Context.ConnectionId} is migrated from {feature.MigrateFrom}.");
            // Your business logic.
        }

        await base.OnConnectedAsync();
    }

    public override async Task OnDisconnectedAsync(Exception e)
    {
        Console.WriteLine($"{Context.ConnectionId} disconnected.");

        var feature = Context.GetHttpContext().Features.Get<IConnectionMigrationFeature>();
        if (feature != null)
        {
            Console.WriteLine($"[{feature.MigrateFrom}] {Context.ConnectionId} will be migrated to {feature.MigrateTo}.");
            // Your business logic.
        }

        await base.OnDisconnectedAsync(e);
    }
}
```