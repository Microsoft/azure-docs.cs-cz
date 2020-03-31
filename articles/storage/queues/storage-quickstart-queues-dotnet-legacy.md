---
title: 'Úvodní příručka: Ke správě fronty použijte Azure Storage v11 pro rozhraní .NET.'
description: V tomto rychlém startu se dozvíte, jak pomocí klientské knihovny Azure Storage pro rozhraní .NET vytvořit frontu a přidat do ní zprávy. Dále se dozvíte, jak číst a zpracovávat zprávy z fronty.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 02/06/2018
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.reviewer: cbrooks
ms.openlocfilehash: c327629f0c5e88520a8bb0b9c4ff68e6edc00c35
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79137335"
---
# <a name="quickstart-use-the-azure-storage-sdk-v11-for-net-to-manage-a-queue"></a>Úvodní příručka: Ke správě fronty použijte sadku Azure Storage SDK v11 pro rozhraní .NET.

V tomto rychlém startu se dozvíte, jak pomocí knihovny klienta Azure Storage verze 11 pro rozhraní .NET vytvořit frontu a přidat do ní zprávy. Dále se dozvíte, jak číst a zpracovávat zprávy z fronty. 

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Dále stáhněte a nainstalujte .NET Core 2.0 pro váš operační systém. Pokud používáte Windows, můžete nainstalovat sadu Visual Studio a použít .NET Framework, pokud chcete. Můžete také nainstalovat editor pro použití ve vašem operačním systému.

### <a name="windows"></a>Windows

- Nainstalujte [.NET Core pro Windows](https://www.microsoft.com/net/download/windows) nebo [.NET Framework](https://www.microsoft.com/net/download/windows) (ten je součástí sady Visual Studio pro Windows).
- Nainstalujte sadu [Visual Studio pro Windows](https://www.visualstudio.com/). Pokud používáte .NET Core, instalace sady Visual Studio je volitelná.  

Informace o výběru mezi .NET Core a .NET Framework najdete v tématu [Výběr mezi .NET Core a .NET Framework pro serverové aplikace](https://docs.microsoft.com/dotnet/standard/choosing-core-framework-server).

### <a name="linux"></a>Linux

- Nainstalujte [.NET Core pro Linux](https://www.microsoft.com/net/download/linux).
- Volitelně nainstalujte [Visual Studio Code](https://www.visualstudio.com/) a [rozšíření jazyka C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

### <a name="macos"></a>macOS

- Nainstalujte [.NET Core pro macOS](https://www.microsoft.com/net/download/macos).
- Volitelně nainstalujte sadu [Visual Studio pro Mac](https://www.visualstudio.com/vs/visual-studio-mac/).

## <a name="download-the-sample-application"></a>Stažení ukázkové aplikace

Ukázková aplikace použitá v tomto rychlém startu je základní konzolová aplikace. Ukázkovou aplikaci můžete prozkoumat na [GitHubu](https://github.com/Azure-Samples/storage-queues-dotnet-quickstart).

Pomocí [gitu](https://git-scm.com/) stáhněte kopii aplikace do vývojového prostředí. 

```bash
git clone https://github.com/Azure-Samples/storage-queues-dotnet-quickstart.git
```

Tento příkaz naklonuje úložiště do vaší místní složky gitu. Chcete-li otevřít řešení sady Visual Studio, vyhledejte složku *storage-queues-dotnet-quickstart,* otevřete ji a poklepejte na *soubor storage-queues-dotnet-quickstart.sln*. 

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

Ukázková aplikace vytvoří frontu a přidá do ní zprávu. Aplikace nejprve prohlédne zprávu bez odebrání z fronty, potom načte zprávu a odstraní ji z fronty.

### <a name="windows"></a>Windows

Pokud jako editor používáte sadu Visual Studio, můžete ukázku spustit stisknutím **F5**. 

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

Ukázka nejprve zkontroluje, zda proměnná prostředí obsahuje připojovací řetězec, který lze analyzovat k vytvoření objektu [CloudStorageAccount](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount) směřujícího na účet úložiště. Chcete-li zkontrolovat, zda je připojovací řetězec platný, ukázka používá [metodu TryParse.](/dotnet/api/microsoft.azure.cosmos.table.cloudstorageaccount.tryparse) Pokud se metoda **TryParse** úspěšně provede, inicializuje proměnnou *storageAccount* a vrátí hodnotu **true**.

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

### <a name="add-a-message"></a>Přidání zprávy

V dalším případě ukázka přidá zprávu do zadní části fronty. 

Zpráva musí být ve formátu, který může být součástí požadavku XML s kódováním UTF-8 a může mít velikost až 64 kB. Pokud zpráva obsahuje binární data, doporučujeme zakódovat zprávu Base64.

Ve výchozím nastavení je maximální doba života zprávy nastavena na 7 dní. Pro aktuální dobu přenosu zprávy můžete zadat libovolné kladné číslo.

```csharp
// Create a message and add it to the queue. Set expiration time to 14 days.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
await queue.AddMessageAsync(message, new TimeSpan(14,0,0,0), null, null, null);
Console.WriteLine("Added message '{0}' to queue '{1}'", message.Id, queue.Name);
Console.WriteLine("Message insertion time: {0}", message.InsertionTime.ToString());
Console.WriteLine("Message expiration time: {0}", message.ExpirationTime.ToString());
Console.WriteLine();
```

Chcete-li přidat zprávu, jejíž platnost nevyprší, použijte `Timespan.FromSeconds(-1)` ve svém volání [AddMessageAsync](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessageasync).

```csharp
await queue.AddMessageAsync(message, TimeSpan.FromSeconds(-1), null, null, null);
```

### <a name="peek-a-message-from-the-queue"></a>Náhled zprávy z fronty

Ukázka ukazuje, jak nahlédnout zprávu z fronty. Při prohlížení zprávy si můžete přečíst obsah zprávy. Zpráva však zůstane viditelná pro ostatní klienty, takže jiný klient může následně načíst a zpracovat zprávu.

```csharp
// Peek at the message at the front of the queue. Peeking does not alter the message's 
// visibility, so that another client can still retrieve and process it. 
CloudQueueMessage peekedMessage = await queue.PeekMessageAsync();

// Display the ID and contents of the peeked message.
Console.WriteLine("Contents of peeked message '{0}': {1}", peekedMessage.Id, peekedMessage.AsString);
Console.WriteLine();
```

### <a name="dequeue-a-message"></a>Odstranění zprávy z fronty

Ukázka také ukazuje, jak dequeue zprávy. Při vyřazení zprávy z fronty načíst zprávu z přední části fronty a vykreslit dočasně neviditelné pro ostatní klienty. Ve výchozím nastavení zůstává zpráva neviditelná po dobu 30 sekund. Během této doby může váš kód zprávu zpracovat. Chcete-li dokončit dequeueing zprávy, odstraňte zprávu ihned po zpracování, takže jiný klient není dequeue stejnou zprávu.

Pokud se kódu nepodaří zpracovat zprávu z důvodu selhání hardwaru nebo softwaru, zpráva se znovu zobrazí po uplynutí doby neviditelnosti. Jiný klient může načíst stejnou zprávu a akci opakujte.

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

Ukázka vyčistí prostředky, které vytvořil odstraněním fronty. Odstraněním fronty také odstraníte všechny zprávy, které obsahuje.

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

Podívejte se na tyto další prostředky pro vývoj rozhraní .NET s Azure Queues:

### <a name="binaries-and-source-code"></a>Binární soubory a zdrojový kód

- Stáhněte si balíčky NuGet pro nejnovější verzi [klientské knihovny Azure Storage pro rozhraní .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet)
    - [Společné](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
    - [Fronty](https://www.nuget.org/packages/Azure.Storage.Queues/)
- Prohlédněte si [zdrojový kód klientské knihovny pro .NET](https://github.com/Azure/azure-storage-net) na GitHubu.

### <a name="client-library-reference-and-samples"></a>Klientská knihovna – referenční informace a ukázky

- Další informace o klientské knihovně pro .NET najdete v [referenčních informacích k rozhraní .NET API](https://docs.microsoft.com/dotnet/api/overview/azure/storage).
- Prozkoumejte [ukázky úložiště fronty](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=dotnet&term=queues) napsané pomocí knihovny klienta .NET.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se dozvěděli, jak přidat zprávy do fronty, prohlížet zprávy z fronty a odřadit zprávy do fronty a zpracovat zprávy pomocí rozhraní .NET. 

> [!div class="nextstepaction"]
> [Komunikace mezi aplikacemi pomocí úložiště Azure Queue](https://docs.microsoft.com/learn/modules/communicate-between-apps-with-azure-queue-storage/index)

- Další informace o .NET Core najdete v tématu [Začínáme s .NET během 10 minut](https://www.microsoft.com/net/learn/get-started/).
