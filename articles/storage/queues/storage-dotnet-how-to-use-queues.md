---
title: Začínáme s Azure Queue Storage pomocí rozhraní .NET Azure Storage
description: Azure Queue Storage poskytuje spolehlivé asynchronní zasílání zpráv mezi součástmi aplikací. Cloudový přenos zpráv umožňuje nezávislé škálování součástí vaší aplikace.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 10/08/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: d54b8f15c90aa8f6ffcc04453fee0349e501f47d
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585747"
---
# <a name="get-started-with-azure-queue-storage-using-net"></a>Začínáme s Azure Queue Storage pomocí rozhraní .NET

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Přehled

Azure Queue Storage poskytuje cloudové zprávy mezi součástmi aplikace. Při navrhování aplikací pro škálování se součásti aplikace často odpojí, takže se můžou škálovat nezávisle. Queue Storage poskytuje asynchronní zasílání zpráv mezi komponentami aplikace, ať už běží v cloudu, v desktopovém prostředí, na místním serveru nebo na mobilním zařízení. Queue Storage také podporuje správu asynchronních úloh a vytváření pracovních toků procesů.

### <a name="about-this-tutorial"></a>O tomto kurzu

V tomto kurzu se dozvíte, jak napsat kód .NET pro některé běžné scénáře s využitím Azure Queue Storage. Jsou například zahrnuty scénáře vytváření a odstraňování front a přidávání, čtení a odstraňování front zpráv.

**Odhadovaný čas dokončení:** 45 minut

### <a name="prerequisites"></a>Předpoklady

- [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
- [Účet Azure Storage](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="set-up-your-development-environment"></a>Nastavení vývojového prostředí

Potom si nastavte vývojové prostředí v sadě Visual Studio, abyste byli připraveni vyzkoušet příklady kódů z této příručky.

### <a name="create-a-windows-console-application-project"></a>Vytvoření projektu konzolové aplikace pro Windows

V sadě Visual Studio vytvořte novou konzolovou aplikaci pro Windows. Následující kroky ukazují, jak vytvořit konzolovou aplikaci v aplikaci Visual Studio 2019. Kroky u ostatních verzí sady Visual Studio jsou podobné.

1. Vybrat **soubor**  >  **Nový**  >  **projekt**
2. Vybrat   >  **okna** platformy
3. Vybrat **konzolovou aplikaci (.NET Framework)**
4. Vyberte **Další**.
5. Do pole **název projektu** zadejte název vaší aplikace.
6. Vyberte **Vytvořit**.

Všechny příklady kódu v tomto kurzu můžete přidat do metody `Main()` v souboru `Program.cs` vaší konzolové aplikace.

Můžete použít klientské knihovny Azure Storage v jakémkoli typu aplikace .NET, včetně cloudové služby Azure nebo webové aplikace a desktopových a mobilních aplikací. V této příručce použijeme konzolovou aplikaci kvůli zjednodušení.

### <a name="use-nuget-to-install-the-required-packages"></a>Použití balíčku NuGet k instalaci požadovaných balíčků

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Abyste mohli dokončit tento kurz, musíte odkazovat na následující čtyři balíčky v projektu:

- [Knihovna Azure. Core pro .NET](https://www.nuget.org/packages/azure.core/): Tento balíček poskytuje sdílené primitivní prvky, abstrakce a pomocníky pro moderní klientské knihovny .NET Azure SDK.
- [Azure. Storage. Common Client Library for .NET](https://www.nuget.org/packages/azure.storage.common/): Tento balíček poskytuje infrastrukturu sdílenou ostatními klientskými knihovnami Azure Storage.
- [Klientská knihovna pro Azure. Storage. Queues pro .NET](https://www.nuget.org/packages/azure.storage.queues/): Tento balíček umožňuje pracovat se službou Azure Queue Storage pro ukládání zpráv, ke kterým může klient přicházet.
- [System.Configuration.ConfigurationManager Library for .NET](https://www.nuget.org/packages/system.configuration.configurationmanager/): Tento balíček poskytuje přístup ke konfiguračním souborům pro klientské aplikace.

K získání těchto balíčků můžete použít NuGet. Postupujte takto:

1. V **Průzkumník řešení** klikněte pravým tlačítkem myši na projekt a vyberte možnost **Spravovat balíčky NuGet**.
1. Vyberte **Procházet** .
1. Vyhledejte online `Azure.Storage.Queues` a vyberte **nainstalovat** a nainstalujte tak Azure Storage klientskou knihovnu a její závislosti. Tím se také nainstalují knihovny Azure. Storage. Common a Azure. Core, které jsou závislé na knihovně fronty.
1. Vyhledejte online `System.Configuration.ConfigurationManager` a vyberte **nainstalovat** a nainstalujte Configuration Manager.

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

Abyste mohli dokončit tento kurz, musíte odkazovat na následující tři balíčky v projektu:

- [Microsoft. Azure. Storage. Common Client Library for .NET](https://www.nuget.org/packages/microsoft.azure.storage.common/): Tento balíček poskytuje programový přístup k datovým prostředkům ve vašem účtu úložiště.
- [Microsoft Azure Queue Storage klientské knihovny pro .NET](https://www.nuget.org/packages/microsoft.azure.storage.queue/): Tato Klientská knihovna umožňuje práci s Azure Queue Storage pro ukládání zpráv, ke kterým může klient přicházet.
- [Microsoft Azure Configuration Manager library for .NET:](https://www.nuget.org/packages/microsoft.azure.configurationmanager/) Tento balíček poskytuje třídu pro potřeby analýzy připojovacího řetězce v konfiguračním souboru bez ohledu na to, kde je aplikace spuštěná.

K získání těchto balíčků můžete použít NuGet. Postupujte takto:

1. V **Průzkumník řešení** klikněte pravým tlačítkem myši na projekt a vyberte možnost **Spravovat balíčky NuGet**.
1. Vyberte **Procházet** .
1. Vyhledejte online `Microsoft.Azure.Storage.Queue` a vyberte **nainstalovat** a nainstalujte tak Azure Storage klientskou knihovnu a její závislosti. Tím se nainstaluje také knihovna Microsoft. Azure. Storage. Common, což je závislost knihovny front.
1. Vyhledejte online `Microsoft.Azure.ConfigurationManager` a vyberte **nainstalovat** a nainstalujte Configuration Manager Azure.

---

### <a name="determine-your-target-environment"></a>Určení cílového prostředí

Ke spuštění příkladů z této příručky máte dvě možnosti prostředí:

- Svůj kód můžete spustit na účtu služby Azure Storage v cloudu.
- Svůj kód můžete spustit pomocí emulátoru úložiště Azurite. Azurite je místní prostředí, které emuluje účet Azure Storage v cloudu. Azurite je bezplatná možnost pro testování a ladění kódu během vývoje aplikace. Emulátor používá známý účet a klíč. Další informace najdete v tématu [použití emulátoru Azurite pro místní Azure Storage vývoj a testování](../common/storage-use-azurite.md).

> [!NOTE]
> Pokud se chcete vyhnout nákladům spojeným se službou Azure Storage, můžete se zaměřit na emulátor úložiště. Pokud se však rozhodnete cílit na účet Azure Storage v cloudu, budou náklady na tento kurz zanedbatelné.

## <a name="get-your-storage-connection-string"></a>Získání připojovacího řetězce pro úložiště

Klientské knihovny Azure Storage pro podporu rozhraní .NET pomocí připojovacího řetězce úložiště ke konfiguraci koncových bodů a přihlašovacích údajů pro přístup ke službám úložiště. Další informace najdete v tématu [Správa přístupových klíčů účtu úložiště](../common/storage-account-keys-manage.md).

### <a name="copy-your-credentials-from-the-azure-portal"></a>Zkopírování přihlašovacích údajů z webu Azure Portal

Ukázkový kód potřebuje autorizovat přístup k vašemu účtu úložiště. Autorizaci provedete tak, že aplikaci poskytnete přihlašovací údaje svého účtu úložiště v podobě připojovacího řetězce. Zobrazení přihlašovacích údajů účtu úložiště:

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. Vyhledejte svůj účet úložiště.
3. V části **Nastavení** v přehledu účtu úložiště vyberte **Přístupové klíče**. Zobrazí se přístupové klíče vašeho účtu a také úplný připojovací řetězec pro jednotlivé klíče.
4. V části **key1** vyhledejte hodnotu **Připojovací řetězec** a kliknutím na tlačítko **Kopírovat** zkopírujte připojovací řetězec. V dalším kroku přidáte hodnotu připojovacího řetězce do proměnné prostředí.

    ![Snímek obrazovky ukazující zkopírování připojovacího řetězce z webu Azure Portal](media/storage-dotnet-how-to-use-queues/portal-connection-string.png)

Další informace o připojovacích řetězcích najdete v tématu věnovaném [konfiguraci připojovacího řetězce pro službu Azure Storage](../common/storage-configure-connection-string.md).

> [!NOTE]
> Klíč účtu úložiště je podobný kořenovému heslu vašeho účtu úložiště. Vždy klíč účtu úložiště pečlivě chraňte. Nedávejte ho jiným uživatelům, nezakódovávejte ho ani ho neukládejte do souboru ve formátu prostého textu, který je přístupný ostatním uživatelům. Pokud se domníváte, že klíč je ohrožený, vygenerujte ho znovu pomocí webu Azure Portal.

Připojovací řetězec úložiště se nejlépe uchovává v konfiguračním souboru. Pokud chcete konfigurovat připojovací řetězec, otevřete v sadě Visual Studio Průzkumníka řešení a v něm soubor `app.config`. Přidejte obsah elementu, který je `<appSettings>` zde zobrazen. Nahraďte `connection-string` hodnotou, kterou jste zkopírovali z účtu úložiště na portálu:

```xml
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.7.2" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="connection-string" />
    </appSettings>
</configuration>
```

Nastavení konfigurace může vypadat následovně:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==EndpointSuffix=core.windows.net" />
```

Pokud chcete cílit na emulátor úložiště Azurite, můžete použít zástupce, který se mapuje na známý název a klíč účtu. V takovém případě bude nastavení připojovacího řetězce vypadat následovně:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
```

### <a name="add-using-directives"></a>Přidání direktiv using

Přidejte na začátek souboru `Program.cs` následující direktivy `using`:

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UsingStatements":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

```csharp
using System; // Namespace for Console output
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for CloudStorageAccount
using Microsoft.Azure.Storage.Queue; // Namespace for Queue storage types
```

---

### <a name="create-the-queue-storage-client"></a>Vytvoření klienta Queue Storage

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

[`QueueClient`](/dotnet/api/azure.storage.queues.queueclient)Třída umožňuje načtení front uložených v Queue Storage. Tady je jeden ze způsobů, jak vytvořit klienta služby:

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateClient":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

[`CloudQueueClient`](/dotnet/api/microsoft.azure.storage.queue.cloudqueueclient?view=azure-dotnet-legacy&preserve-view=true)Třída umožňuje načtení front uložených v Queue Storage. Tady je jeden ze způsobů, jak vytvořit klienta služby:

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
```

---

Nyní jste připraveni napsat kód, který čte data z a zapisuje data do Queue Storage.

## <a name="create-a-queue"></a>Vytvoření fronty

Tento příklad ukazuje, jak vytvořit frontu:

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateQueue":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();
```

---

## <a name="insert-a-message-into-a-queue"></a>Vložení zprávy do fronty

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Chcete-li vložit zprávu do existující fronty, zavolejte [`SendMessage`](/dotnet/api/azure.storage.queues.queueclient.sendmessage) metodu. Zpráva může být buď řetězec (ve formátu UTF-8), nebo pole bajtů. Následující kód vytvoří frontu (Pokud neexistuje) a vloží zprávu:

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_InsertMessage":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

Chcete-li vložit zprávu do existující fronty, vytvořte nejprve novou [`CloudQueueMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet-legacy&preserve-view=true) . Dále zavolejte [`AddMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet-legacy&preserve-view=true) metodu. A `CloudQueueMessage` lze vytvořit buď z řetězce (ve formátu UTF-8), nebo pole bajtů. Tady je kód, který vytvoří frontu (Pokud neexistuje) a vloží zprávu: Pokud `Hello, World` chcete vložit zprávu do existující fronty, nejdřív vytvořte novou [`CloudQueueMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet-legacy&preserve-view=true) . Dále zavolejte [`AddMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet-legacy&preserve-view=true) metodu. A `CloudQueueMessage` lze vytvořit buď z řetězce (ve formátu UTF-8), nebo pole bajtů. Zde je kód, který vytvoří frontu (Pokud neexistuje) a vloží zprávu `Hello, World` :

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();

// Create a message and add it to the queue
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
queue.AddMessage(message);
```

---

## <a name="peek-at-the-next-message"></a>Zobrazení náhledu další zprávy

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Můžete prohlížet zprávy ve frontě bez jejich odebrání z fronty voláním [`PeekMessages`](/dotnet/api/azure.storage.queues.queueclient.peekmessages) metody. Pokud nepředáte hodnotu `maxMessages` parametru, výchozí hodnota je prohlížení jedné zprávy.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_PeekMessage":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

Můžete prohlížet zprávy před frontou, aniž byste je museli odebírat z fronty voláním [`PeekMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.peekmessage?view=azure-dotnet-legacy&preserve-view=true) metody.

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Peek at the next message
CloudQueueMessage peekedMessage = queue.PeekMessage();

// Display message.
Console.WriteLine(peekedMessage.AsString);
```

---

## <a name="change-the-contents-of-a-queued-message"></a>Změna obsahu zpráv zařazených ve frontě

Podle potřeby můžete změnit obsah zprávy přímo ve frontě. Pokud zpráva představuje pracovní úlohu, mohli byste tuto funkci použít k aktualizaci stavu pracovních úloh. Následující kód aktualizuje zprávy ve frontě o nový obsah a prodlouží časový limit viditelnosti na 60 sekund. Uloží se tím stav práce spojený se zprávou a klient získá další minutu, aby mohl pokračovat ve zpracování zprávy. Tento postup můžete použít ke sledování pracovních postupů ve frontě, aniž byste museli začít znovu od začátku, pokud krok zpracování selže kvůli selhání hardwaru nebo softwaru. Obvykle byste udržovali také hodnotu počtu opakování, a pokud by se pokus o zpracování zprávy opakoval více než *n* krát, odstranili byste ji. Je to ochrana proti tomu, aby zpráva při každém pokusu o zpracování nevyvolala chyby aplikace.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UpdateMessage":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the message from the queue and update the message contents.
CloudQueueMessage message = queue.GetMessage();
message.SetMessageContent2("Updated contents.", false);
queue.UpdateMessage(message,
    TimeSpan.FromSeconds(60.0),  // Make it invisible for another 60 seconds.
    MessageUpdateFields.Content | MessageUpdateFields.Visibility);
```

---

## <a name="dequeue-the-next-message"></a>Vyřazení další zprávy z fronty

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Vyřadí zprávu z fronty ve dvou krocích. Když zavoláte [`ReceiveMessages`](/dotnet/api/azure.storage.queues.queueclient.receivemessages) , dostanete další zprávu ve frontě. Zpráva vrácená z `ReceiveMessages` se bude neviditelná pro jakýkoliv jiný kód, který čte zprávy z této fronty. Ve výchozím nastavení tato zpráva zůstává neviditelná po dobu 30 sekund. Chcete-li dokončit odebrání zprávy z fronty, je také nutné zavolat [`DeleteMessage`](/dotnet/api/azure.storage.queues.queueclient.deletemessage) . Tento dvoukrokový proces odebrání zprávy zaručuje, aby v případě, že se vašemu kódu nepodaří zprávu zpracovat z důvodu selhání hardwaru nebo softwaru, mohla stejnou zprávu získat jiná instance vašeho kódu a bylo možné to zkusit znovu. Váš kód volá `DeleteMessage` hned po zpracování zprávy.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessage":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

Váš kód vyřadí zprávu z fronty ve dvou krocích. Když zavoláte [`GetMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage?view=azure-dotnet-legacy&preserve-view=true) , dostanete další zprávu ve frontě. Zpráva vrácená z `GetMessage` se bude neviditelná pro jakýkoliv jiný kód, který čte zprávy z této fronty. Ve výchozím nastavení tato zpráva zůstává neviditelná po dobu 30 sekund. Chcete-li dokončit odebrání zprávy z fronty, je také nutné zavolat [`DeleteMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage?view=azure-dotnet-legacy&preserve-view=true) . Tento dvoukrokový proces odebrání zprávy zaručuje, aby v případě, že se vašemu kódu nepodaří zprávu zpracovat z důvodu selhání hardwaru nebo softwaru, mohla stejnou zprávu získat jiná instance vašeho kódu a bylo možné to zkusit znovu. Váš kód volá `DeleteMessage` hned po zpracování zprávy.

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Get the next message
CloudQueueMessage retrievedMessage = queue.GetMessage();

//Process the message in less than 30 seconds, and then delete the message
queue.DeleteMessage(retrievedMessage);
```

---

<!-- docutune:casing "Async-Await pattern" "Async and Await" -->

## <a name="use-the-async-await-pattern-with-common-queue-storage-apis"></a>Použití vzorů Async-Await se společnými rozhraními API Queue Storage

Tento příklad ukazuje, jak použít vzor Async-Await se společnými rozhraními API Queue Storage. Ukázka volá asynchronní verzi každé z daných metod, jak je uvedeno v `Async` příponě každé metody. Při použití asynchronní metody pozastaví vzor Async-Await místní spuštění, dokud se volání nedokončí. Toto chování umožňuje aktuálnímu vláknu provádět další činnosti, což pomáhá zabránit vzniku kritických bodů z hlediska výkonu a zlepšuje celkovou rychlost reakce aplikace. Další informace o použití vzoru Async-Await v rozhraní .NET naleznete v tématu [Async a await (C# and Visual Basic)](/previous-versions/hh191443(v=vs.140))

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_AsyncQueue":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

```csharp
// Create the queue if it doesn't already exist
if(await queue.CreateIfNotExistsAsync())
{
    Console.WriteLine("Queue '{0}' Created", queue.Name);
}
else
{
    Console.WriteLine("Queue '{0}' Exists", queue.Name);
}

// Create a message to put in the queue
CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

// Async enqueue the message
await queue.AddMessageAsync(cloudQueueMessage);
Console.WriteLine("Message added");

// Async dequeue the message
CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

// Async delete the message
await queue.DeleteMessageAsync(retrievedMessage);
Console.WriteLine("Deleted message");
```

---

## <a name="use-additional-options-for-dequeuing-messages"></a>Použití dalších možností pro vyřazování zpráv do fronty

Načítání zpráv z fronty si můžete přizpůsobit dvěma způsoby. Za prvé si můžete načíst dávku zpráv (až 32). Za druhé si můžete nastavit delší nebo kratší časový limit neviditelnosti, aby měl váš kód více nebo méně času na úplné zpracování jednotlivých zpráv.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Následující příklad kódu používá [`ReceiveMessages`](/dotnet/api/azure.storage.queues.queueclient.receivemessages) metodu k získání 20 zpráv v jednom volání. Pak každou zprávu zpracuje pomocí `foreach` smyčky. Také se pro každou zprávu nastaví časový limit neviditelnosti 5 minut. Všimněte si, že pět minut začíná u všech zpráv současně, takže po pěti minutách od jejich volání se `ReceiveMessages` všechny zprávy, které nebyly odstraněny, opět zobrazí.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessages":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

Následující příklad kódu používá [`GetMessages`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessages?view=azure-dotnet-legacy&preserve-view=true) metodu k získání 20 zpráv v jednom volání. Pak každou zprávu zpracuje pomocí `foreach` smyčky. Také se pro každou zprávu nastaví časový limit neviditelnosti 5 minut. Všimněte si, že pět minut začíná u všech zpráv současně, takže po pěti minutách od jejich volání se `GetMessages` všechny zprávy, které nebyly odstraněny, opět zobrazí.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
{
    // Process all messages in less than 5 minutes, deleting each message after processing.
    queue.DeleteMessage(message);
}
```

---

## <a name="get-the-queue-length"></a>Získání délky fronty

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Podle potřeby můžete získat odhadovaný počet zpráv ve frontě. [`GetProperties`](/dotnet/api/azure.storage.queues.queueclient.getproperties)Metoda vrátí vlastnosti fronty včetně počtu zpráv. [`ApproximateMessagesCount`](/dotnet/api/azure.storage.queues.models.queueproperties.approximatemessagescount)Vlastnost obsahuje přibližný počet zpráv ve frontě. Toto číslo není nižší než skutečný počet zpráv ve frontě, ale může být vyšší.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_GetQueueLength":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

Podle potřeby můžete získat odhadovaný počet zpráv ve frontě. [`FetchAttributes`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.fetchattributes?view=azure-dotnet-legacy&preserve-view=true)Metoda vrátí atributy fronty včetně počtu zpráv. [`ApproximateMessageCount`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.approximatemessagecount?view=azure-dotnet-legacy&preserve-view=true)Vlastnost vrátí poslední hodnotu získanou `FetchAttributes` metodou bez volání Queue Storage.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Fetch the queue attributes.
queue.FetchAttributes();

// Retrieve the cached approximate message count.
int cachedMessageCount = queue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

---

## <a name="delete-a-queue"></a>Odstranění fronty

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Pokud chcete odstranit frontu a všechny zprávy, které jsou v ní obsažené, zavolejte [`Delete`](/dotnet/api/azure.storage.queues.queueclient.delete) metodu u objektu Queue.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DeleteQueue":::

# <a name="net-v11"></a>[.NET v11](#tab/dotnetv11)

Pokud chcete odstranit frontu a všechny zprávy, které jsou v ní obsažené, zavolejte [`Delete`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.delete?view=azure-dotnet-legacy&preserve-view=true) metodu u objektu Queue.

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Delete the queue.
queue.Delete();
```

---

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili základy Queue Storage, postupujte podle těchto odkazů a získejte další informace o složitějších úlohách úložiště.

- Úplné podrobnosti o dostupných rozhraních API najdete v Queue Storage referenční dokumentaci:
  - [Odkaz na klientskou knihovnu Azure Storage pro .NET](/dotnet/api/overview/azure/storage)
  - [Odkaz na Azure Storage REST API](/rest/api/storageservices/)
- Projděte si další průvodce funkcemi, kde najdete další informace o dalších možnostech pro ukládání dat v Azure.
  - [Začínáme s Azure Table Storage používáním rozhraní .NET](../../cosmos-db/tutorial-develop-table-dotnet.md) k ukládání strukturovaných dat.
  - [Začínáme s Azure Blob Storage s využitím .NET](../blobs/storage-quickstart-blobs-dotnet.md) k ukládání nestrukturovaných dat.
  - [Připojení k SQL Database s použitím rozhraní .NET (C#)](../../azure-sql/database/connect-query-dotnet-core.md) pro uložení relačních dat
- Naučte se, jak zjednodušit psaní kódu pro práci s Azure Storage pomocí [sady Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).
