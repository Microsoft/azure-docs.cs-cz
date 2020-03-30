---
title: 'Úvodní příručka: Knihovna úložiště fronty Azure v12 – .NET'
description: Zjistěte, jak pomocí knihovny Azure Queue .NET v12 vytvořit frontu a přidat zprávy do fronty. Dále se dozvíte, jak číst a odstraňovat zprávy z fronty. Dozvíte se také, jak odstranit frontu.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/22/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: c69aa91596ff203445aa4fa3ccd59001ffe16649
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78197483"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-net"></a>Úvodní příručka: Knihovna klienta úložiště fronty Azure v12 pro rozhraní .NET

Začínáme s klientskou knihovnou úložiště fronty Azure verze 12 pro rozhraní .NET. Azure Queue Storage je služba pro ukládání velkého počtu zpráv pro pozdější načítání a zpracování. Následujícím postupem nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úkoly.

> [!NOTE]
> Pokud chcete začít s předchozí verzí sady SDK, přečtěte si [úvodní příručku: Pomocí sady Azure Storage SDK v11 pro rozhraní .NET můžete spravovat frontu](storage-quickstart-queues-dotnet-legacy.md).

Pomocí klientské knihovny úložiště fronty Azure v12 pro rozhraní .NET:

* Vytvoření fronty
* Přidání zpráv do fronty
* Náhled na zprávy ve frontě
* Aktualizace zprávy ve frontě
* Příjem zpráv z fronty
* Odstranění zpráv z fronty
* Odstranění fronty

[Referenční dokumentace](/dotnet/api/azure.storage.queues) | rozhraní[Knihovna ukázky zdrojového kódu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues) | [knihovny (NuGet)](https://www.nuget.org/packages/Azure.Storage.Queues/12.0.0) | [Ukázky](https://docs.microsoft.com/azure/storage/common/storage-samples-dotnet?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* Účet úložiště Azure – [vytvoření účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* Aktuální [sada .NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) pro váš operační systém. Ujistěte se, že získat SDK a ne runtime.

## <a name="setting-up"></a>Nastavení

Tato část vás provede přípravou projektu pro práci s klientskou knihovnou úložiště fronty Azure v12 pro rozhraní .NET.

### <a name="create-the-project"></a>Vytvoření projektu

Vytvořte aplikaci .NET Core s názvem *QueuesQuickstartV12*.

1. V okně konzoly (například cmd, PowerShell `dotnet new` nebo Bash) vytvořte pomocí příkazu novou konzolovou aplikaci s názvem *QueuesQuickstartV12*. Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: *Program.cs*.

   ```console
   dotnet new console -n QueuesQuickstartV12
   ```

1. Přepněte do nově vytvořeného *adresáře QueuesQuickstartV12.*

   ```console
   cd QueuesQuickstartV12
   ```

### <a name="install-the-package"></a>Instalace balíčku

Zatímco ještě v adresáři aplikace, nainstalujte knihovnu klienta `dotnet add package` úložiště fronty Azure pro balíček .NET pomocí příkazu.

```console
dotnet add package Azure.Storage.Queues
```

### <a name="set-up-the-app-framework"></a>Nastavení architektury aplikace

Z adresáře projektu:

1. Otevření *souboru Program.cs* v editoru
1. Odebrat `Console.WriteLine("Hello World!");` příkaz
1. Přidání `using` směrnic
1. Aktualizace `Main` deklarace metody pro [podporu asynchronního kódu](https://docs.microsoft.com/dotnet/csharp/whats-new/csharp-7-1#async-main)



Zde je kód:

```csharp
using Azure;
using Azure.Storage.Queues;
using Azure.Storage.Queues.Models;
using System;
using System.Threading.Tasks;

namespace QueuesQuickstartV12
{
    class Program
    {
        static async Task Main(string[] args)
        {
        }
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektový model

Azure Queue Storage je služba pro ukládání velkých objemů zpráv. Zpráva fronty může mít velikost až 64 kB. Fronta může obsahovat miliony zpráv až do celkového limitu kapacity účtu úložiště. Fronty se běžně používají k vytvoření nevyřízených položek práce pro zpracování asynchronně. Úložiště front nabízí tři typy prostředků:

* Účet úložiště
* Fronta v účtu úložiště
* Zprávy ve frontě

Na následujícím diagramu jsou vztahy těchto prostředků.

![Diagram architektury úložiště fronty](./media/storage-queues-introduction/queue1.png)

K interakci s těmito prostředky použijte následující třídy .NET:

* [QueueServiceClient](/dotnet/api/azure.storage.queues.queueserviceclient): `QueueServiceClient` Umožňuje spravovat všechny fronty v účtu úložiště.
* [QueueClient](/dotnet/api/azure.storage.queues.queueclient): `QueueClient` Třída umožňuje spravovat a manipulovat s jednotlivou frontou a jejími zprávami.
* [QueueMessage](/dotnet/api/azure.storage.queues.models.queuemessage): `QueueMessage` Třída představuje jednotlivé objekty vrácené při volání [ReceiveMessages](/dotnet/api/azure.storage.queues.queueclient.receivemessages) ve frontě.

## <a name="code-examples"></a>Příklady kódu

Tyto ukázkové fragmenty kódu ukazují, jak provést následující akce s klientskou knihovnou úložiště fronty Azure pro rozhraní .NET:

* [Získání připojovacího řetězce](#get-the-connection-string)
* [Vytvoření fronty](#create-a-queue)
* [Přidání zpráv do fronty](#add-messages-to-a-queue)
* [Náhled na zprávy ve frontě](#peek-at-messages-in-a-queue)
* [Aktualizace zprávy ve frontě](#update-a-message-in-a-queue)
* [Příjem zpráv z fronty](#receive-messages-from-a-queue)
* [Odstranění zpráv z fronty](#delete-messages-from-a-queue)
* [Odstranění fronty](#delete-a-queue)

### <a name="get-the-connection-string"></a>Získání připojovacího řetězce

Níže uvedený kód načte připojovací řetězec pro účet úložiště. Připojovací řetězec je uložen v proměnné prostředí vytvořené v části [Konfigurovat připojovací řetězec úložiště.](#configure-your-storage-connection-string)

Přidejte tento `Main` kód uvnitř metody:

```csharp
Console.WriteLine("Azure Queue storage v12 - .NET quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable called
// AZURE_STORAGE_CONNECTION_STRING on the machine running the application.
// If the environment variable is created after the application is launched
// in a console or with Visual Studio, the shell or application needs to be
// closed and reloaded to take the environment variable into account.
string connectionString = Environment.GetEnvironmentVariable("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-queue"></a>Vytvoření fronty

Rozhodněte o názvu nové fronty. Níže uvedený kód připojí hodnotu GUID k názvu fronty, aby bylo zajištěno, že je jedinečný.

> [!IMPORTANT]
> Názvy front mohou obsahovat pouze malá písmena, čísla a pomlčky a musí začínat písmenem nebo číslem. Před i za každým spojovníkem musí být jiný znak než spojovník. Název musí být také mezi 3 a 63 znaků dlouhé. Další informace o pojmenování front naleznete [v tématu Pojmenování front a metadat](/rest/api/storageservices/naming-queues-and-metadata).


Vytvořte instanci třídy [QueueClient.](/dotnet/api/azure.storage.queues.queueclient) Potom zavolejte [CreateAsync](/dotnet/api/azure.storage.queues.queueclient.createasync) metoda k vytvoření fronty v účtu úložiště.

Přidejte tento kód na `Main` konec metody:

```csharp
// Create a unique name for the queue
string queueName = "quickstartqueues-" + Guid.NewGuid().ToString();

Console.WriteLine($"Creating queue: {queueName}");

// Instantiate a QueueClient which will be
// used to create and manipulate the queue
QueueClient queueClient = new QueueClient(connectionString, queueName);

// Create the queue
await queueClient.CreateAsync();
```

### <a name="add-messages-to-a-queue"></a>Přidání zpráv do fronty

Následující fragment kódu asynchronně přidá zprávy do fronty voláním metody [SendMessageAsync.](/dotnet/api/azure.storage.queues.queueclient.sendmessageasync) Také uloží [SendReceipt](/dotnet/api/azure.storage.queues.models.sendreceipt) vrácena `SendMessageAsync` z volání. Příjemka slouží k pozdější aktualizaci zprávy v programu.

Přidejte tento kód na `Main` konec metody:

```csharp
Console.WriteLine("\nAdding messages to the queue...");

// Send several messages to the queue
await queueClient.SendMessageAsync("First message");
await queueClient.SendMessageAsync("Second message");

// Save the receipt so we can update this message later
SendReceipt receipt = await queueClient.SendMessageAsync("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>Náhled na zprávy ve frontě

Prohlédněte si zprávy ve frontě voláním [PeekMessagesAsync](/dotnet/api/azure.storage.queues.queueclient.peekmessagesasync) metoda. Metoda `PeekMessagesAsync` načte jednu nebo více zpráv z přední části fronty, ale nezmění viditelnost zprávy.

Přidejte tento kód na `Main` konec metody:

```csharp
Console.WriteLine("\nPeek at the messages in the queue...");

// Peek at messages in the queue
PeekedMessage[] peekedMessages = await queueClient.PeekMessagesAsync(maxMessages: 10);

foreach (PeekedMessage peekedMessage in peekedMessages)
{
    // Display the message
    Console.WriteLine($"Message: {peekedMessage.MessageText}");
}
```

### <a name="update-a-message-in-a-queue"></a>Aktualizace zprávy ve frontě

Aktualizujte obsah zprávy voláním metody [UpdateMessageAsync.](/dotnet/api/azure.storage.queues.queueclient.updatemessageasync) Metoda `UpdateMessageAsync` může změnit časový limit viditelnosti zprávy a obsah. Obsah zprávy musí být kódovaný řetězec UTF-8, který má velikost až 64 kB. Spolu s novým obsahem zprávy předavěte `SendReceipt` hodnoty z toho, který byl uložen dříve v kódu. Hodnoty `SendReceipt` identifikují, kterou zprávu chcete aktualizovat.

```csharp
Console.WriteLine("\nUpdating the third message in the queue...");

// Update a message using the saved receipt from sending the message
await queueClient.UpdateMessageAsync(receipt.MessageId, receipt.PopReceipt, "Third message has been updated");
```

### <a name="receive-messages-from-a-queue"></a>Příjem zpráv z fronty

Stáhnout dříve přidané zprávy voláním [ReceiveMessagesAsync](/dotnet/api/azure.storage.queues.queueclient.receivemessagesasync) metoda.

Přidejte tento kód na `Main` konec metody:

```csharp
Console.WriteLine("\nReceiving messages from the queue...");

// Get messages from the queue
QueueMessage[] messages = await queueClient.ReceiveMessagesAsync(maxMessages: 10);
```

### <a name="delete-messages-from-a-queue"></a>Odstranění zpráv z fronty

Po zpracování odstraníte zprávy z fronty. V tomto případě zpracování je pouze zobrazení zprávy na konzoli.

Aplikace pozastaví vstup uživatele `Console.ReadLine` voláním před tím, než zpracuje a odstraní zprávy. Před odstraněním na [webu Azure Portal](https://portal.azure.com) ověřte, že prostředky byly vytvořeny správně. Všechny zprávy, které nejsou explicitně odstraněny, se nakonec znovu zobrazí ve frontě, aby byla možné je zpracovat.

Přidejte tento kód na `Main` konec metody:

```csharp
Console.WriteLine("\nPress Enter key to 'process' messages and delete them from the queue...");
Console.ReadLine();

// Process and delete messages from the queue
foreach (QueueMessage message in messages)
{
    // "Process" the message
    Console.WriteLine($"Message: {message.MessageText}");

    // Let the service know we're finished with
    // the message and it can be safely deleted.
    await queueClient.DeleteMessageAsync(message.MessageId, message.PopReceipt);
}
```

### <a name="delete-a-queue"></a>Odstranění fronty

Následující kód vyčistí prostředky, které aplikace vytvořila odstraněním fronty pomocí metody [DeleteAsync.](/dotnet/api/azure.storage.queues.queueclient.deleteasync)

Přidejte tento kód na `Main` konec metody:

```csharp
Console.WriteLine("\nPress Enter key to delete the queue...");
Console.ReadLine();

// Clean up
Console.WriteLine($"Deleting queue: {queueClient.Name}");
await queueClient.DeleteAsync();

Console.WriteLine("Done");
```

## <a name="run-the-code"></a>Spuštění kódu

Tato aplikace vytvoří a přidá tři zprávy do fronty Azure. Kód uvádí zprávy ve frontě, pak načte a odstraní je, před konečnou odstranění fronty.

V okně konzoly přejděte do adresáře aplikace a potom aplikaci sestavte a spusťte.

```console
dotnet build
```

```console
dotnet run
```

Výstup aplikace je podobný následujícímu příkladu:

```output
Azure Queue storage v12 - .NET quickstart sample

Creating queue: quickstartqueues-5c72da2c-30cc-4f09-b05c-a95d9da52af2

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Receiving messages from the queue...

Press Enter key to 'process' messages and delete them from the queue...

Message: First message
Message: Second message
Message: Third message has been updated

Press Enter key to delete the queue...

Deleting queue: quickstartqueues-5c72da2c-30cc-4f09-b05c-a95d9da52af2
Done
```

Když se aplikace před příjemem zpráv pozastaví, zkontrolujte svůj účet úložiště na [webu Azure Portal](https://portal.azure.com). Ověřte, zda jsou zprávy ve frontě.

Stisknutím klávesy **Enter** přijměte a odstraňte zprávy. Po zobrazení výzvy znovu stisknete klávesu **Enter,** abyste odstranili frontu a dokončili ukázku.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se dozvěděli, jak vytvořit frontu a přidat do ní zprávy pomocí asynchronního kódu .NET. Pak jste se naučili prohlížet, načítat a odstraňovat zprávy. Nakonec jste se naučili, jak odstranit frontu zpráv.

Výukové programy, ukázky, rychlé starty a další dokumentaci naleznete na adrese:

> [!div class="nextstepaction"]
> [Azure pro vývojáře na platformě .NET a .NET Core](https://docs.microsoft.com/dotnet/azure/)

* Další informace najdete v [knihovnách Úložiště Azure pro rozhraní .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage).
* Chcete-li zobrazit další ukázkové aplikace úložiště fronty Azure, pokračujte na [ukázky knihovny klienta Azure Queue v12 .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Queues/samples).
* Další informace o .NET Core najdete v tématu [Začínáme s .NET během 10 minut](https://www.microsoft.com/net/learn/get-started/).
