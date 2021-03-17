---
title: 'Rychlý Start: použití Azure Storage V11 pro .NET ke správě fronty'
description: V tomto rychlém startu se dozvíte, jak pomocí Azure Storage klientské knihovny pro .NET vytvořit frontu a přidat do ní zprávy. V dalším kroku se dozvíte, jak číst a zpracovávat zprávy z fronty.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 07/24/2020
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: 23703dc507aa909aea4711289a4d7d5c5e6a170e
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97588195"
---
# <a name="quickstart-use-the-azure-storage-sdk-v11-for-net-to-manage-a-queue"></a>Rychlý Start: použití sady Azure Storage SDK V11 pro .NET ke správě fronty

V tomto rychlém startu se dozvíte, jak pomocí Azure Storage klientské knihovny V11 pro .NET vytvořit frontu a do ní přidat zprávy. V dalším kroku se dozvíte, jak číst a zpracovávat zprávy z fronty.

> [!NOTE]
> V tomto rychlém startu se používá starší verze klientské knihovny Azure Queue Storage. Pokud chcete začít používat nejnovější verzi, přečtěte si téma [rychlý Start: Klientská knihovna Azure Queue Storage V12 pro .NET](storage-quickstart-queues-dotnet.md).

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Dále stáhněte a nainstalujte .NET Core 2.0 pro váš operační systém. Pokud používáte Windows, můžete nainstalovat sadu Visual Studio a použít .NET Framework, pokud chcete. Můžete také nainstalovat editor pro použití ve vašem operačním systému.

### <a name="windows"></a>Windows

- Nainstalujte [.NET Core pro Windows](https://www.microsoft.com/net/download/windows) nebo [.NET Framework](https://www.microsoft.com/net/download/windows) (ten je součástí sady Visual Studio pro Windows).
- Nainstalujte sadu [Visual Studio pro Windows](https://www.visualstudio.com/). Pokud používáte .NET Core, instalace sady Visual Studio je volitelná.

Informace o výběru mezi .NET Core a .NET Framework najdete v tématu [Výběr mezi .NET Core a .NET Framework pro serverové aplikace](/dotnet/standard/choosing-core-framework-server).

### <a name="linux"></a>Linux

- Nainstalujte [.NET Core pro Linux](https://www.microsoft.com/net/download/linux).
- Volitelně nainstalujte [Visual Studio Code](https://www.visualstudio.com/) a [rozšíření jazyka C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

### <a name="macos"></a>macOS

- Nainstalujte [.NET Core pro macOS](https://www.microsoft.com/net/download/macos).
- Volitelně nainstalujte sadu [Visual Studio pro Mac](https://www.visualstudio.com/vs/visual-studio-mac/).

## <a name="download-the-sample-application"></a>Stažení ukázkové aplikace

Ukázková aplikace použitá v tomto rychlém startu je základní konzolová aplikace. Ukázkovou aplikaci můžete prozkoumat na [GitHubu](https://github.com/Azure-Samples/storage-queues-dotnet-quickstart).

Pomocí [Gitu](https://git-scm.com/) si stáhněte kopii aplikace do vývojového prostředí.

```bash
git clone https://github.com/Azure-Samples/storage-queues-dotnet-quickstart.git
```

Tento příkaz naklonuje úložiště do místní složky Gitu. Chcete-li otevřít řešení sady Visual Studio, vyhledejte `storage-queues-dotnet-quickstart` složku, otevřete ji a dvakrát klikněte na položku `storage-queues-dotnet-quickstart.sln` .

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurace připojovacího řetězce úložiště

Pro spuštění aplikace je potřeba zadat připojovací řetězec pro váš účet úložiště. Ukázková aplikace načte připojovací řetězec z proměnné prostředí a použije ho k autorizaci požadavků ve službě Azure Storage.

Po zkopírování připojovacího řetězce ho zapište do nové proměnné prostředí na místním počítači, na kterém aplikaci spouštíte. Proměnnou prostředí nastavíte tak, že otevřete okno konzoly a budete postupovat podle pokynů pro váš operační systém. Nahraďte `<yourconnectionstring>` svým skutečným připojovacím řetězcem:

### <a name="windows"></a>Windows

```cmd
setx storageconnectionstring "<yourconnectionstring>"
```

Po přidání proměnné prostředí možná bude nutné restartovat všechny spuštěné programy, které budou potřebovat číst tuto proměnnou prostředí, a to včetně okna konzoly. Pokud například jako editor používáte sadu Visual Studio, před spuštěním ukázky sadu Visual Studio restartujte.

### <a name="linux"></a>Linux

```bash
export storageconnectionstring=<yourconnectionstring>
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source ~/.bashrc`, aby se změny projevily.

### <a name="macos"></a>macOS

Upravte svůj soubor .bash_profile a přidejte do něj proměnnou prostředí:

```bash
export STORAGE_CONNECTION_STRING=<yourconnectionstring>
```

Po přidání proměnné prostředí spusťte v okně konzoly příkaz `source .bash_profile`, aby se změny projevily.

## <a name="run-the-sample"></a>Spuštění ukázky

Ukázková aplikace vytvoří frontu a přidá do ní zprávu. Aplikace nejprve prohlédne zprávu, aniž by ji odebrala z fronty, a pak ji načte a odstraní z fronty.

### <a name="windows"></a>Windows

Pokud používáte Visual Studio jako editor, můžete spustit stisknutím klávesy `F5` .

Jinak, přejděte do adresáře aplikace a spusťte aplikaci příkazem `dotnet run`.

```
dotnet run
```

### <a name="linux"></a>Linux

Přejděte do adresáře aplikace a spusťte aplikaci příkazem `dotnet run`.

```
dotnet run
```

### <a name="macos"></a>macOS

Přejděte do adresáře aplikace a spusťte aplikaci příkazem `dotnet run`.

```
dotnet run
```

Výstup ukázkové aplikace je podobný jako v následujícím příkladu:

```
Azure Queues - .NET Quickstart sample

Created queue 'quickstartqueues-3136fe9a-fa52-4b19-a447-8999a847da52'

Added message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7' to queue 'quickstartqueues-3136fe9a-fa52-4b19-a447-8999a847da52'
Message insertion time: 2/7/2019 4:30:46 AM +00:00
Message expiration time: 2/14/2019 4:30:46 AM +00:00

Contents of peeked message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7': Hello, World

Message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7' becomes visible again at 2/7/2019 4:31:16 AM +00:00

Processed and deleted message 'aa8fa95f-07ea-4df7-bf86-82b3f7debfb7'

Press any key to delete the sample queue.
```

## <a name="understand-the-sample-code"></a>Vysvětlení vzorového kódu

Dále prozkoumejte vzorový kód, abyste pochopili, jak funguje.

### <a name="try-parsing-the-connection-string"></a>Zkouška parsování připojovacího řetězce

Ukázka nejprve zkontroluje, zda proměnná prostředí obsahuje připojovací řetězec, který lze analyzovat a vytvořit tak [`CloudStorageAccount`](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount) objekt odkazující na účet úložiště. Chcete-li ověřit, zda je připojovací řetězec platný, ukázka používá [`TryParse`](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount.tryparse) metodu. Pokud `TryParse` je úspěšná, inicializuje `storageAccount` proměnnou a vrátí `true` .

```csharp
// Retrieve the connection string for use with the application. The storage connection string is stored
// in an environment variable called storageconnectionstring, on the machine where the application is running.
// If the environment variable is created after the application is launched in a console or with Visual
// Studio, the shell needs to be closed and reloaded to take the environment variable into account.
string storageConnectionString = Environment.GetEnvironmentVariable("storageconnectionstring");

// Check whether the connection string can be parsed.
if (CloudStorageAccount.TryParse(storageConnectionString, out storageAccount))
{
    // If the connection string is valid, proceed with calls to Azure Queues here.
    ...
}
else
{
    Console.WriteLine(
        "A connection string has not been defined in the system environment variables. " +
        "Add an environment variable named 'storageconnectionstring' with your storage " +
        "connection string as a value.");
}
```

### <a name="create-the-queue"></a>Vytvoření fronty

Nejprve ukázka vytvoří frontu a přidá do ní zprávu.

```csharp
// Create a queue called 'quickstartqueues' and append a GUID value so that the queue name
// is unique in your storage account.
queue = cloudQueueClient.GetQueueReference("quickstartqueues-" + Guid.NewGuid().ToString());
await queue.CreateAsync();

Console.WriteLine("Created queue '{0}'", queue.Name);
Console.WriteLine();
```

### <a name="add-a-message"></a>Přidat zprávu

V dalším kroku přidá ukázka zprávu do pozadí fronty.

Zpráva musí být ve formátu, který může být součástí požadavku XML s kódováním UTF-8 a může mít velikost až 64 KB. Pokud zpráva obsahuje binární data, doporučujeme, abyste zprávu zakódovat ve formátu base64.

Ve výchozím nastavení je maximální hodnota TTL (Time-to-Live) pro zprávu nastavená na 7 dní. Můžete zadat libovolné kladné číslo pro hodnotu TTL (Time to Live).

```csharp
// Create a message and add it to the queue. Set expiration time to 14 days.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await queue.AddMessageAsync(message, new TimeSpan(14,0,0,0), null, null, null);
Console.WriteLine("Added message '{0}' to queue '{1}'", message.Id, queue.Name);
Console.WriteLine("Message insertion time: {0}", message.InsertionTime.ToString());
Console.WriteLine("Message expiration time: {0}", message.ExpirationTime.ToString());
Console.WriteLine();
```

Chcete-li přidat zprávu, která nevyprší, použijte `Timespan.FromSeconds(-1)` při volání metody [`AddMessageAsync`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync) .

```csharp
await queue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

### <a name="peek-a-message-from-the-queue"></a>Prohlížet zprávu z fronty

Ukázka ukazuje, jak prohlížet zprávu z fronty. Po prohlížení zprávy si můžete přečíst obsah zprávy. Tato zpráva ale zůstane viditelná pro ostatní klienty, takže ji může následně načítat a zpracovávat i jiný klient.

```csharp
// Peek at the message at the front of the queue. Peeking does not alter the message's
// visibility, so that another client can still retrieve and process it.
CloudQueueMessage peekedMessage = await queue.PeekMessageAsync();

// Display the ID and contents of the peeked message.
Console.WriteLine("Contents of peeked message '{0}': {1}", peekedMessage.Id, peekedMessage.AsString);
Console.WriteLine();
```

### <a name="dequeue-a-message"></a>Odstranění zprávy z fronty

Ukázka také ukazuje, jak vyřadit zprávu z fronty. Když vyřadíte zprávu z fronty, načtěte zprávu z přední části fronty a dočasně ji vykreslíte ostatním klientům. Ve výchozím nastavení zůstane zpráva po dobu 30 sekund neviditelná. Během této doby může váš kód zpracovat zprávu. Chcete-li dokončit vyřazení zprávy z fronty, odstraňte zprávu hned po zpracování, aby jiný klient vyřadí stejnou zprávu do fronty.

Pokud kód nedokáže zpracovat zprávu z důvodu chyby hardwaru nebo softwaru, bude zpráva po uplynutí doby neviditelnosti opět viditelná. Jiný klient může získat stejnou zprávu a zkusit to znovu.

```csharp
// Retrieve the message at the front of the queue. The message becomes invisible for
// a specified interval, during which the client attempts to process it.
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();

// Display the time at which the message will become visible again if it is not deleted.
Console.WriteLine("Message '{0}' becomes visible again at {1}", retrievedMessage.Id, retrievedMessage.NextVisibleTime);
Console.WriteLine();

//Process and delete the message within the period of invisibility.
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Processed and deleted message '{0}'", retrievedMessage.Id);
Console.WriteLine();
```

### <a name="clean-up-resources"></a>Vyčištění prostředků

Ukázka vyčistí prostředky, které vytvořila, odstraněním fronty. Odstraněním fronty se odstraní také všechny zprávy, které obsahuje.

```csharp
Console.WriteLine("Press any key to delete the sample queue.");
Console.ReadLine();
Console.WriteLine("Deleting the queue and any messages it contains...");
Console.WriteLine();
if (queue != null)
{
    await queue.DeleteIfExistsAsync();
}
```

## <a name="resources-for-developing-net-applications-with-queues"></a>Prostředky pro vývoj aplikací .NET s frontami

Podívejte se na tyto další zdroje informací o vývoji pro .NET pomocí Azure Queue Storage:

### <a name="binaries-and-source-code"></a>Binární soubory a zdrojový kód

- Stažení balíčků NuGet pro nejnovější verzi [Azure Storage klientské knihovny pro .NET](/dotnet/api/overview/azure/storage)
  - [Společné](https://www.nuget.org/packages/microsoft.azure.storage.common/)
  - [Fronty](https://www.nuget.org/packages/Azure.Storage.Queues/)
- Prohlédněte si [zdrojový kód klientské knihovny pro .NET](https://github.com/Azure/azure-storage-net) na GitHubu.

### <a name="azure-storage-client-library-reference-and-samples"></a>Referenční dokumentace a ukázky klientské knihovny Azure Storage

- Další informace o knihovnách klienta .NET naleznete v tématu [Azure Storage klientských knihoven pro rozhraní .NET](/dotnet/api/overview/azure/storage) .
- Prozkoumejte [Queue Storage ukázek](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=queues) napsaných pomocí klientské knihovny .NET.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak přidat zprávy do fronty, prohlížet zprávy z fronty a vyřadit zprávy z fronty a zpracovávat je pomocí .NET.

> [!div class="nextstepaction"]
> [Komunikace mezi aplikacemi pomocí Azure Queue Storage](/learn/modules/communicate-between-apps-with-azure-queue-storage/index)

- Další informace o .NET Core najdete v tématu [Začínáme s .NET během 10 minut](https://www.microsoft.com/net/learn/get-started/).
