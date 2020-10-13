---
title: Začínáme s úložištěm Azure Queue pomocí Azure Storage .NET
description: Fronty Azure Queue poskytují spolehlivý asynchronní přenos zpráv mezi součástmi aplikace. Cloudový přenos zpráv umožňuje nezávislé škálování součástí vaší aplikace.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 10/08/2020
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.custom: devx-track-csharp
ms.openlocfilehash: c07ad6e631482b47da674549e976953842cf983e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91855918"
---
# <a name="get-started-with-azure-queue-storage-using-net"></a>Začínáme s úložištěm Azure Queue pomocí rozhraní .NET

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>Přehled

Úložiště Azure Queue zajišťuje cloudový přenos zpráv mezi součástmi aplikace. Při navrhování aplikací pro škálování se součásti aplikace často odpojí, takže se můžou škálovat nezávisle. Queue Storage zajišťuje asynchronní zasílání zpráv mezi komponentami aplikace, ať už běží v cloudu, v desktopovém prostředí, na místním serveru nebo na mobilním zařízení. Queue Storage také podporuje správu asynchronních úloh a pracovní postupy procesů sestavování buildů.

### <a name="about-this-tutorial"></a>O tomto kurzu

V tomto kurzu si ukážeme, jak napsat kód .NET pro některé běžné scénáře s využitím služby Azure Queue Storage. Jsou například zahrnuty scénáře vytváření a odstraňování front a přidávání, čtení a odstraňování front zpráv.

**Odhadovaný čas dokončení:** 45 minut

### <a name="prerequisites"></a>Požadavky

- [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
- [Účet úložiště Azure](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="set-up-your-development-environment"></a>Nastavení vývojového prostředí

Potom si nastavte vývojové prostředí v sadě Visual Studio, abyste byli připraveni vyzkoušet příklady kódů z této příručky.

### <a name="create-a-windows-console-application-project"></a>Vytvoření projektu konzolové aplikace pro Windows

V sadě Visual Studio vytvořte novou konzolovou aplikaci pro Windows. Následující kroky ukazují, jak vytvořit konzolovou aplikaci v aplikaci Visual Studio 2019. Kroky u ostatních verzí sady Visual Studio jsou podobné.

1. Vybrat **soubor**  >  **Nový**  >  **projekt**
2. Vybrat **Platform**  >  **okna** platformy
3. Vybrat **konzolovou aplikaci (.NET Framework)**
4. Vyberte **Další**.
5. Do pole **název projektu** zadejte název vaší aplikace.
6. Vyberte **Vytvořit**.

Všechny příklady kódu v tomto kurzu můžete přidat do metody **Main ()** souboru **program.cs** vaší konzolové aplikace.

Můžete použít klientské knihovny Azure Storage v jakémkoli typu aplikace .NET, včetně cloudové služby Azure nebo webové aplikace a desktopových a mobilních aplikací. V této příručce použijeme konzolovou aplikaci kvůli zjednodušení.

### <a name="use-nuget-to-install-the-required-packages"></a>Použití balíčku NuGet k instalaci požadovaných balíčků

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

Abyste mohli dokončit tento kurz, musíte odkazovat na následující čtyři balíčky v projektu:

- [Knihovna Azure Core pro .NET](https://www.nuget.org/packages/Azure.Core/): Tento balíček poskytuje sdílené primitivní prvky, abstrakce a pomocníky pro moderní klientské knihovny .NET Azure SDK.
- [Azure Storage Common Client Library for .NET](https://www.nuget.org/packages/Azure.Storage.Common/): Tento balíček poskytuje infrastrukturu sdílenou ostatními klientskými knihovnami Azure Storage.
- [Knihovna front Azure Storage pro .NET](https://www.nuget.org/packages/Azure.Storage.Queues/): Tento balíček umožňuje pracovat s Služba frontem Azure Storage pro ukládání zpráv, ke kterým může klient přicházet.
- [Knihovna Configuration Manager pro .NET](https://www.nuget.org/packages/System.Configuration.ConfigurationManager/): Tento balíček poskytuje přístup ke konfiguračním souborům pro klientské aplikace.

K získání těchto balíčků můžete použít NuGet. Postupujte takto:

1. V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt a vyberte možnost **Spravovat balíčky NuGet**.
1. Vyberte **Procházet** .
1. Vyhledejte v online režimu "Azure. Storage. Queues" a vyberte **nainstalovat** a nainstalujte tak knihovnu klienta úložiště a její závislosti. Tím se také nainstalují knihovny Azure. Storage. Common a Azure. Core, které jsou závislé na knihovně fronty.
1. Hledejte online System.Configuration.ConfigurationManager a vyberte **nainstalovat** a nainstalujte Configuration Manager.

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Abyste mohli dokončit tento kurz, musíte odkazovat na následující tři balíčky v projektu:

- [Microsoft Azure Storage Common Client Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/): Tento balíček poskytuje programový přístup k datovým prostředkům ve vašem účtu úložiště.
- [Knihovna Microsoft Azure Storage Queue Library pro .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/): Tato Klientská knihovna umožňuje pracovat s služba front Microsoft Azure Storage pro ukládání zpráv, ke kterým může klient přicházet.
- [Microsoft Azure Configuration Manager library for .NET:](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) Tento balíček poskytuje třídu pro potřeby analýzy připojovacího řetězce v konfiguračním souboru bez ohledu na to, kde je aplikace spuštěná.

K získání těchto balíčků můžete použít NuGet. Postupujte takto:

1. V **Průzkumník řešení**klikněte pravým tlačítkem myši na projekt a vyberte možnost **Spravovat balíčky NuGet**.
1. Vyberte **Procházet** .
1. Online vyhledejte "Microsoft. Azure. Storage. Queue" a vyberte **nainstalovat** a nainstalujte tak knihovnu klienta úložiště a její závislosti. Tím se nainstaluje také knihovna Microsoft. Azure. Storage. Common, což je závislost knihovny front.
1. Hledejte online Microsoft.Azure.ConfigurationManager a vyberte **nainstalovat** a nainstalujte Configuration Manager Azure.

---

### <a name="determine-your-target-environment"></a>Určení cílového prostředí

Ke spuštění příkladů z této příručky máte dvě možnosti prostředí:

- Svůj kód můžete spustit na účtu služby Azure Storage v cloudu.
- Svůj kód můžete spustit pomocí emulátoru úložiště Azurite. Azurite je místní prostředí, které emuluje účet Azure Storage v cloudu. Azurite je bezplatná možnost pro testování a ladění kódu během vývoje aplikace. Emulátor používá známý účet a klíč. Další informace najdete v tématu [použití emulátoru Azurite pro místní Azure Storage vývoj a testování](../common/storage-use-azurite.md).

> [!NOTE]
> Pokud se chcete vyhnout nákladům spojeným se službou Azure Storage, můžete se zaměřit na emulátor úložiště. I když se zaměříte na účet úložiště Azure v cloudu, budou náklady na vyzkoušení postupů z tohoto kurzu zanedbatelné.

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

Připojovací řetězec úložiště se nejlépe uchovává v konfiguračním souboru. Pokud chcete nakonfigurovat připojovací řetězec, otevřete *app.config* soubor z Průzkumník řešení v aplikaci Visual Studio. Přidejte obsah níže uvedeného prvku `\<appSettings\>`. Nahraďte *řetězec připojení* hodnotou, kterou jste zkopírovali z účtu úložiště na portálu:

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

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UsingStatements":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

```csharp
using System; // Namespace for Console output
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for CloudStorageAccount
using Microsoft.Azure.Storage.Queue; // Namespace for Queue storage types
```

---

### <a name="create-the-queue-service-client"></a>Vytvoření klienta Služby front

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

Třída [QueueClient](/dotnet/api/azure.storage.queues.queueclient) vám umožňuje načíst fronty uložené v úložišti front. Tady je jeden ze způsobů, jak vytvořit klienta služby:

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateClient":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Třída [CloudQueueClient](/dotnet/api/microsoft.azure.storage.queue.cloudqueueclient?view=azure-dotnet-legacy&preserve-view=true) vám umožňuje načíst fronty uložené v rámci Queue Storage. Tady je jeden ze způsobů, jak vytvořit klienta služby:

```csharp
// Retrieve storage account from connection string
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
```

---

Teď můžete napsat kód, který bude číst data z Queue Storage a bude je tam také zapisovat.

## <a name="create-a-queue"></a>Vytvoření fronty

Tento příklad ukazuje, jak vytvořit frontu:

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_CreateQueue":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

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

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

Chcete-li vložit zprávu do existující fronty, zavolejte metodu [SendMessage](/dotnet/api/azure.storage.queues.queueclient.sendmessage) . Zpráva může být buď `string` (ve formátu UTF-8), nebo v `byte` poli. Následující kód vytvoří frontu (Pokud neexistuje) a vloží zprávu:

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_InsertMessage":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Pokud chcete vložit zprávu do existující fronty, vytvořte nejdříve novou třídu [CloudQueueMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet-legacy&preserve-view=true). Pak zavolejte metodu [AddMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet-legacy&preserve-view=true). A `CloudQueueMessage` lze vytvořit buď z typu `string` (ve formátu UTF-8), nebo `byte` pole. Tady je kód, který vytvoří frontu (Pokud neexistuje) a vloží zprávu "Hello, World":

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

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

Můžete prohlížet zprávy ve frontě bez jejich odebrání z fronty voláním metody [PeekMessages](/dotnet/api/azure.storage.queues.queueclient.peekmessages) . Pokud nepředáte hodnotu parametru *maxMessages* , ve výchozím nastavení je prohlížení jedné zprávy.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_PeekMessage":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Pomocí volání metody [PeekMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.peekmessage?view=azure-dotnet-legacy&preserve-view=true) můžete prohlížet zprávy ve frontě, aniž byste je z fronty odebrali.

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

Podle potřeby můžete změnit obsah zprávy přímo ve frontě. Pokud zpráva představuje pracovní úlohu, mohli byste tuto funkci použít k aktualizaci stavu pracovních úloh. Následující kód aktualizuje zprávy ve frontě o nový obsah a prodlouží časový limit viditelnosti na 60 sekund. Uloží se tím stav práce spojený se zprávou a klient získá další minutu, aby mohl pokračovat ve zpracování zprávy. Tímto způsobem může sledovat vícekrokového pracovní postupy pro zprávy ve frontě, aniž by bylo nutné v případě, že krok zpracování z důvodu selhání hardwaru nebo softwaru selže, začít znovu od začátku. Obvykle byste udržovali také hodnotu počtu opakování, a pokud by se pokus o zpracování zprávy opakoval více než *n*krát, odstranili byste ji. Je to ochrana proti tomu, aby zpráva při každém pokusu o zpracování nevyvolala chyby aplikace.

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_UpdateMessage":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

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

## <a name="de-queue-the-next-message"></a>Vyřazení další zprávy z fronty

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

Odřadí zprávu z fronty ve dvou krocích. Když zavoláte [ReceiveMessages](/dotnet/api/azure.storage.queues.queueclient.receivemessages), dostanete další zprávu ve frontě. Zpráva vrácená z `ReceiveMessages` se bude neviditelná pro jakýkoliv jiný kód, který čte zprávy z této fronty. Ve výchozím nastavení tato zpráva zůstává neviditelná po dobu 30 sekund. Aby bylo možné odebrání zprávy z fronty dokončit, musíte také zavolat metodu [DeleteMessage](/dotnet/api/azure.storage.queues.queueclient.deletemessage). Tento dvoukrokový proces odebrání zprávy zaručuje, aby v případě, že se vašemu kódu nepodaří zprávu zpracovat z důvodu selhání hardwaru nebo softwaru, mohla stejnou zprávu získat jiná instance vašeho kódu a bylo možné to zkusit znovu. Váš kód volá `DeleteMessage` hned po zpracování zprávy.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessage":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Váš kód vyřazuje zprávy z fronty ve dvou krocích. Zavoláním metody [GetMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage?view=azure-dotnet-legacy&preserve-view=true) získáte další zprávu ve frontě. Zpráva vrácená z `GetMessage` se bude neviditelná pro jakýkoliv jiný kód, který čte zprávy z této fronty. Ve výchozím nastavení tato zpráva zůstává neviditelná po dobu 30 sekund. Aby bylo možné odebrání zprávy z fronty dokončit, musíte také zavolat metodu [DeleteMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage?view=azure-dotnet-legacy&preserve-view=true). Tento dvoukrokový proces odebrání zprávy zaručuje, aby v případě, že se vašemu kódu nepodaří zprávu zpracovat z důvodu selhání hardwaru nebo softwaru, mohla stejnou zprávu získat jiná instance vašeho kódu a bylo možné to zkusit znovu. Váš kód volá `DeleteMessage` hned po zpracování zprávy.

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

## <a name="use-async-await-pattern-with-common-queue-storage-apis"></a>Použití vzoru Async-Await s běžnými rozhraním API Queue Storage

Tento příklad ukazuje způsob použití vzoru Async-Await s běžnými rozhraním API Queue Storage. Ukázka volá asynchronní verzi každé z daných metod, jak indikuje přípona *Async* každé metody. Při použití asynchronní metody pozastaví vzor async-await místní provádění kódu až do dokončení volání. Toto chování umožňuje aktuálnímu vláknu provádět další činnosti, což pomáhá zabránit vzniku kritických bodů z hlediska výkonu a zlepšuje celkovou rychlost reakce aplikace. Další podrobnosti o použití vzoru Async-Await v rozhraní .NET najdete v tématu [Async a Await (C# a Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx).

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_AsyncQueue":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

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

## <a name="leverage-additional-options-for-de-queuing-messages"></a>Využívání dalších možností pro vyřazování zpráv z fronty

Načítání zpráv z fronty si můžete přizpůsobit dvěma způsoby. Za prvé si můžete načíst dávku zpráv (až 32). Za druhé si můžete nastavit delší nebo kratší časový limit neviditelnosti, aby měl váš kód více nebo méně času na úplné zpracování jednotlivých zpráv.

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

Následující příklad kódu používá metodu [ReceiveMessages](/dotnet/api/azure.storage.queues.queueclient.receivemessages) k získání 20 zpráv v jednom volání. Pak každou zprávu zpracuje pomocí `foreach` smyčky. Také se pro každou zprávu nastaví časový limit neviditelnosti 5 minut. Všimněte si, že 5 minut začne u všech zpráv současně, takže po uplynutí 5 minut od jejich volání `ReceiveMessages` budou všechny zprávy, které nebyly odstraněny, opět viditelné.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DequeueMessages":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

V následujícím příkladu kódu se pomocí metody [GetMessages](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessages?view=azure-dotnet-legacy&preserve-view=true) získá 20 zpráv v jednom volání. Pak každou zprávu zpracuje pomocí `foreach` smyčky. Také se pro každou zprávu nastaví časový limit neviditelnosti 5 minut. Všimněte si, že 5 minut začne u všech zpráv současně, takže po uplynutí 5 minut od jejich volání `GetMessages` budou všechny zprávy, které nebyly odstraněny, opět viditelné.

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

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

Podle potřeby můžete získat odhadovaný počet zpráv ve frontě. Metoda [GetProperties](/dotnet/api/azure.storage.queues.queueclient.getproperties) požádá služba front o načtení vlastností fronty, včetně počtu zpráv. Vlastnost [ApproximateMessagesCount](/dotnet/api/azure.storage.queues.models.queueproperties.approximatemessagescount) obsahuje přibližný počet zpráv ve frontě. Toto číslo není nižší než skutečný počet zpráv ve frontě, ale může být vyšší.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_GetQueueLength":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Podle potřeby můžete získat odhadovaný počet zpráv ve frontě. Metoda [FetchAttributes](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.fetchattributes?view=azure-dotnet-legacy&preserve-view=true) požádá Službu front o načtení atributů fronty, včetně počtu zpráv. Vlastnost [ApproximateMessageCount](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.approximatemessagecount?view=azure-dotnet-legacy&preserve-view=true) vrací poslední hodnotu získanou `FetchAttributes` metodou bez volání služba front.

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

# <a name="net-v12"></a>[\.NET V12](#tab/dotnet)

Pokud budete chtít odstranit frontu se všemi zprávami, které v ní jsou, zavolejte metodu [Delete](/dotnet/api/azure.storage.queues.queueclient.delete) pro objekt fronty.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/QueueBasics.cs" id="snippet_DeleteQueue":::

# <a name="net-v11"></a>[\.NET v11](#tab/dotnetv11)

Pokud budete chtít odstranit frontu se všemi zprávami, které v ní jsou, zavolejte metodu [Delete](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.delete?view=azure-dotnet-legacy&preserve-view=true) pro objekt fronty.

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

Teď, když jste se naučili základy používání služby Queue Storage, podívejte se na následujících odkazech na další informace o složitějších úlohách úložiště.

- Projděte si referenční dokumentaci ke Službě front, kde najdete úplné podrobnosti o dostupných rozhraních API:
  - [Klientská knihovna pro úložiště – referenční informace pro .NET](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
  - [REST API – referenční informace](https://msdn.microsoft.com/library/azure/dd179355)
- Projděte si další průvodce funkcemi, kde najdete další informace o dalších možnostech pro ukládání dat v Azure.
  - [Začínáme s Azure Table Storage pomocí rozhraní .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md) pro ukládání strukturovaných dat
  - [Začínáme s Azure Blob Storage pomocí rozhraní .NET](../blobs/storage-dotnet-how-to-use-blobs.md) pro ukládání nestrukturovaných dat
  - [Připojení k SQL Database s použitím rozhraní .NET (C#)](../../azure-sql/database/connect-query-dotnet-core.md) pro uložení relačních dat
- Naučte se, jak zjednodušit psaní kódu pro práci s Azure Storage pomocí [sady Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).
