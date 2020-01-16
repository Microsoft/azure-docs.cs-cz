---
title: Začínáme s úložištěm Azure Queue pomocí Azure Storage .NET
description: Fronty Azure Queue poskytují spolehlivý asynchronní přenos zpráv mezi součástmi aplikace. Cloudový přenos zpráv umožňuje nezávislé škálování součástí vaší aplikace.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/21/2019
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 0806c1101c0bc93a1b917cb2d18709721ff0c6d6
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75968291"
---
# <a name="get-started-with-azure-queue-storage-using-net"></a>Začínáme s úložištěm Azure Queue Storage s použitím .NET

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="overview"></a>Přehled

Úložiště Azure Queue zajišťuje cloudový přenos zpráv mezi součástmi aplikace. Při navrhování aplikací pro škálování ve větším měřítku jsou jednotlivé součásti aplikací často nepropojené, aby je bylo možné škálovat nezávisle. Queue Storage zajišťuje asynchronní přenos zpráv pro komunikaci mezi součástmi aplikace bez ohledu na to, jestli běží v cloudu, na desktopu, na místním serveru nebo na mobilním zařízení. Queue Storage také podporuje správu asynchronních úloh a pracovní postupy procesů sestavování buildů.

### <a name="about-this-tutorial"></a>O tomto kurzu

V tomto kurzu si ukážeme, jak napsat kód .NET pro některé běžné scénáře s využitím služby Azure Queue Storage. Jsou například zahrnuty scénáře vytváření a odstraňování front a přidávání, čtení a odstraňování front zpráv.

**Odhadovaný čas dokončení:** 45 minut

### <a name="prerequisites"></a>Požadavky

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Storage společnou klientskou knihovnu pro .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
* [Klientská knihovna Azure Storage Queue pro .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/)
* [Azure Configuration Manager for .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)
* [Účet úložiště Azure](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="set-up-your-development-environment"></a>Nastavení vývojového prostředí

Potom si nastavte vývojové prostředí v sadě Visual Studio, abyste byli připraveni vyzkoušet příklady kódů z této příručky.

### <a name="create-a-windows-console-application-project"></a>Vytvoření projektu konzolové aplikace pro Windows

V sadě Visual Studio vytvořte novou konzolovou aplikaci pro Windows. Následující kroky ukazují, jak vytvořit konzolovou aplikaci v aplikaci Visual Studio 2019. Kroky u ostatních verzí sady Visual Studio jsou podobné.

1. Vyberte **Soubor** > **Nový** > **Projekt**.
2. Vybrat **platformu** > **Windows**
3. Vyberte **Aplikace konzoly (.NET Framework)** .
4. Vyberte **Další**.
5. Do pole **název projektu** zadejte název vaší aplikace.
6. Vyberte **Vytvořit**.

Všechny příklady kódu v tomto kurzu můžete přidat do metody **Main ()** souboru **program.cs** vaší konzolové aplikace.

Můžete použít klientské knihovny Azure Storage v jakémkoli typu aplikace .NET, včetně cloudové služby Azure nebo webové aplikace a desktopových a mobilních aplikací. V této příručce použijeme konzolovou aplikaci kvůli zjednodušení.

### <a name="use-nuget-to-install-the-required-packages"></a>Použití balíčku NuGet k instalaci požadovaných balíčků

Abyste mohli dokončit tento kurz, musíte odkazovat na následující tři balíčky v projektu:

* [Microsoft Azure Storage Common Client Library for .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/): Tento balíček poskytuje programový přístup k datovým prostředkům ve vašem účtu úložiště.
* [Knihovna Microsoft Azure Storage Queue Library pro .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Queue/): Tato Klientská knihovna umožňuje pracovat s služba front Microsoft Azure Storage pro ukládání zpráv, ke kterým může klient přicházet.
* [Microsoft Azure Configuration Manager library for .NET:](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) Tento balíček poskytuje třídu pro potřeby analýzy připojovacího řetězce v konfiguračním souboru bez ohledu na to, kde je aplikace spuštěná.

K získání těchto balíčků můžete použít NuGet. Postupujte následovně:

1. V **Průzkumníku řešení** klikněte pravým tlačítkem na projekt a vyberte **Spravovat balíčky NuGet**.
2. Vyberte **Procházet** .
3. Online vyhledejte "Microsoft. Azure. Storage. Queue" a vyberte **nainstalovat** a nainstalujte tak knihovnu klienta úložiště a její závislosti. Tím se nainstaluje také knihovna Microsoft. Azure. Storage. Common, což je závislost knihovny front.
4. Online vyhledejte "Microsoft. Azure. ConfigurationManager" a vyberte **nainstalovat** a nainstalujte Configuration Manager Azure.

> [!NOTE]
> Balíčky klientských knihoven pro úložiště jsou taky součástí [sady Azure SDK for .NET](https://azure.microsoft.com/downloads/). Doporučujeme ale nainstalovat taky klientské knihovny pro úložiště z NuGet, abyste měli jistotu, že máte vždycky nejnovější verze.
>
> ODataLibé závislosti v knihovnách klienta úložiště pro .NET jsou vyřešeny balíčky ODataLib dostupnými v NuGet, nikoli z WCF Data Services. Knihovny ODataLib můžete stáhnout přímo nebo z odkazu ve vašem kódovém projektu prostřednictvím balíčku NuGet. Konkrétní balíčky ODataLib používané klientskými knihovnami pro úložiště jsou [OData](https://nuget.org/packages/Microsoft.Data.OData/), [EDM](https://nuget.org/packages/Microsoft.Data.Edm/)a [prostor](https://nuget.org/packages/System.Spatial/). I když tyto knihovny používají třídy úložiště tabulek Azure, jsou požadované závislosti pro programování s klientskými knihovnami pro úložiště.

### <a name="determine-your-target-environment"></a>Určení cílového prostředí

Ke spuštění příkladů z této příručky máte dvě možnosti prostředí:

* Svůj kód můžete spustit na účtu služby Azure Storage v cloudu.
* Svůj kód můžete spustit v emulátoru úložiště Azure. Emulátor úložiště je místní prostředí, které emuluje účet služby Azure Storage v cloudu. Emulátor je bezplatnou možností pro testování a ladění kódu během vývoje aplikace. Emulátor používá známý účet a klíč. Další informace najdete v článku [Použití emulátoru úložiště Azure pro vývoj a testování](../common/storage-use-emulator.md).

Pokud se zaměřujete na účet úložiště v cloudu, zkopírujte z webu Azure Portal primární přístupový klíč svého účtu úložiště. Další informace najdete v tématu [Správa přístupových klíčů účtu úložiště](../common/storage-account-keys-manage.md).

> [!NOTE]
> Pokud se chcete vyhnout nákladům spojeným se službou Azure Storage, můžete se zaměřit na emulátor úložiště. I když se zaměříte na účet úložiště Azure v cloudu, budou náklady na vyzkoušení postupů z tohoto kurzu zanedbatelné.

### <a name="configure-your-storage-connection-string"></a>Konfigurace připojovacího řetězce úložiště

Klientské knihovny Azure Storage pro podporu rozhraní .NET pomocí připojovacího řetězce úložiště ke konfiguraci koncových bodů a přihlašovacích údajů pro přístup ke službám úložiště. Připojovací řetězec úložiště se nejlépe uchovává v konfiguračním souboru.

Další informace o připojovacích řetězcích najdete v tématu věnovaném [konfiguraci připojovacího řetězce pro službu Azure Storage](../common/storage-configure-connection-string.md).

> [!NOTE]
> Klíč účtu úložiště je podobný kořenovému heslu vašeho účtu úložiště. Vždy klíč účtu úložiště pečlivě chraňte. Nedávejte ho jiným uživatelům, nezakódovávejte ho ani ho neukládejte do souboru ve formátu prostého textu, který je přístupný ostatním uživatelům. Pokud se domníváte, že klíč je ohrožený, vygenerujte ho znovu pomocí webu Azure Portal.

Pokud chcete nakonfigurovat připojovací řetězec, otevřete soubor **App. config** z Průzkumník řešení v aplikaci Visual Studio. Přidejte obsah elementu **\<appSettings\>** níže zobrazeným. Nahraďte *název účtu* názvem svého účtu úložiště a klíčovým klíčem *účtu* pro přístup k účtu:

```xml
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key" />
    </appSettings>
</configuration>
```

Nastavení konfigurace může vypadat následovně:

```xml
<add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=GMuzNHjlB3S9itqZJHHCnRkrokLkcSyW7yK9BRbGp0ENePunLPwBgpxV1Z/pVo9zpem/2xSHXkMqTHHLcx8XRA==" />
```

Pokud se chcete zaměřit na emulátor úložiště, můžete vytvořit zástupce, který se namapuje na název a klíč známého účtu. V takovém případě bude nastavení připojovacího řetězce vypadat následovně:

```xml
<add key="StorageConnectionString" value="UseDevelopmentStorage=true;" />
```

### <a name="add-using-directives"></a>Přidání direktiv using

Přidejte na začátek souboru `Program.cs` následující direktivy `using`:

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage; // Namespace for CloudStorageAccount
using Microsoft.Azure.Storage.Queue; // Namespace for Queue storage types
```

### <a name="copy-your-credentials-from-the-azure-portal"></a>Zkopírování přihlašovacích údajů z webu Azure Portal

Ukázkový kód potřebuje autorizovat přístup k vašemu účtu úložiště. Autorizaci provedete tak, že aplikaci poskytnete přihlašovací údaje svého účtu úložiště v podobě připojovacího řetězce. Zobrazení přihlašovacích údajů účtu úložiště:

1. Přejděte na [Azure Portal](https://portal.azure.com).
2. Vyhledejte svůj účet úložiště.
3. V části **Nastavení** v přehledu účtu úložiště vyberte **Přístupové klíče**. Zobrazí se přístupové klíče vašeho účtu a také úplný připojovací řetězec pro jednotlivé klíče.
4. V části **key1** vyhledejte hodnotu **Připojovací řetězec** a kliknutím na tlačítko **Kopírovat** zkopírujte připojovací řetězec. V dalším kroku přidáte hodnotu připojovacího řetězce do proměnné prostředí.

    ![Snímek obrazovky ukazující zkopírování připojovacího řetězce z webu Azure Portal](media/storage-dotnet-how-to-use-queues/portal-connection-string.png)

### <a name="parse-the-connection-string"></a>Analýza připojovacího řetězce

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-queue-service-client"></a>Vytvoření klienta Služby front

Třída [CloudQueueClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueueclient?view=azure-dotnet) vám umožňuje načíst fronty uložené v rámci Queue Storage. Tady je jeden ze způsobů, jak vytvořit klienta služby:

```csharp
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
```

Teď můžete napsat kód, který bude číst data z Queue Storage a bude je tam také zapisovat.

## <a name="create-a-queue"></a>Vytvoření fronty

Tento příklad ukazuje, jak vytvořit frontu, pokud ještě neexistuje:

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a container.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist
queue.CreateIfNotExists();
```

## <a name="insert-a-message-into-a-queue"></a>Vložení zprávy do fronty

Pokud chcete vložit zprávu do existující fronty, vytvořte nejdříve novou třídu [CloudQueueMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage?view=azure-dotnet). Pak zavolejte metodu [AddMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.addmessage?view=azure-dotnet). **CloudQueueMessage** je možné vytvořit buď z řetězce (ve formátu UTF-8), nebo z **bajtového** pole. Tady je kód, který vytvoří frontu (pokud neexistuje) a vloží zprávu „Hello, World“:

```csharp
// Retrieve storage account from connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the queue client.
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

// Retrieve a reference to a queue.
CloudQueue queue = queueClient.GetQueueReference("myqueue");

// Create the queue if it doesn't already exist.
queue.CreateIfNotExists();

// Create a message and add it to the queue.
CloudQueueMessage message = new CloudQueueMessage("Hello, World");
queue.AddMessage(message);
```

## <a name="peek-at-the-next-message"></a>Zobrazení náhledu další zprávy

Pomocí volání metody [PeekMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.peekmessage?view=azure-dotnet) můžete prohlížet zprávy ve frontě, aniž byste je z fronty odebrali.

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

## <a name="change-the-contents-of-a-queued-message"></a>Změna obsahu zpráv zařazených ve frontě

Podle potřeby můžete změnit obsah zprávy přímo ve frontě. Pokud zpráva představuje pracovní úlohu, mohli byste tuto funkci použít k aktualizaci stavu pracovních úloh. Následující kód aktualizuje zprávy ve frontě o nový obsah a prodlouží časový limit viditelnosti na 60 sekund. Uloží se tím stav práce spojený se zprávou a klient získá další minutu, aby mohl pokračovat ve zpracování zprávy. Tímto způsobem může sledovat vícekrokového pracovní postupy pro zprávy ve frontě, aniž by bylo nutné v případě, že krok zpracování z důvodu selhání hardwaru nebo softwaru selže, začít znovu od začátku. Obvykle byste udržovali také hodnotu počtu opakování, a pokud by se pokus o zpracování zprávy opakoval více než *n*krát, odstranili byste ji. Je to ochrana proti tomu, aby zpráva při každém pokusu o zpracování nevyvolala chyby aplikace.

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

## <a name="de-queue-the-next-message"></a>Vyřazení další zprávy z fronty

Váš kód vyřazuje zprávy z fronty ve dvou krocích. Zavoláním metody [GetMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage?view=azure-dotnet) získáte další zprávu ve frontě. Zpráva vrácená metodou **GetMessage** se stane neviditelnou pro jakýkoli jiný kód, který čte zprávy z této fronty. Ve výchozím nastavení tato zpráva zůstává neviditelná po dobu 30 sekund. Aby bylo možné odebrání zprávy z fronty dokončit, musíte také zavolat metodu [DeleteMessage](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage?view=azure-dotnet). Tento dvoukrokový proces odebrání zprávy zaručuje, aby v případě, že se vašemu kódu nepodaří zprávu zpracovat z důvodu selhání hardwaru nebo softwaru, mohla stejnou zprávu získat jiná instance vašeho kódu a bylo možné to zkusit znovu. Váš kód zavolá metodu **DeleteMessage** hned po zpracování zprávy.

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

## <a name="use-async-await-pattern-with-common-queue-storage-apis"></a>Použití vzoru Async-Await s běžnými rozhraním API Queue Storage

Tento příklad ukazuje způsob použití vzoru Async-Await s běžnými rozhraním API Queue Storage. Ukázka volá asynchronní verzi každé z daných metod, jak indikuje přípona *Async* každé metody. Při použití asynchronní metody pozastaví vzor async-await místní provádění kódu až do dokončení volání. Toto chování umožňuje aktuálnímu vláknu provádět další činnosti, což pomáhá zabránit vzniku kritických bodů z hlediska výkonu a zlepšuje celkovou rychlost reakce aplikace. Další podrobnosti o použití vzoru Async-Await v rozhraní .NET najdete v tématu [Async a Await (C# a Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx).

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

## <a name="leverage-additional-options-for-de-queuing-messages"></a>Využívání dalších možností pro vyřazování zpráv z fronty

Načítání zpráv z fronty si můžete přizpůsobit dvěma způsoby. Za prvé si můžete načíst dávku zpráv (až 32). Za druhé si můžete nastavit delší nebo kratší časový limit neviditelnosti, aby měl váš kód více nebo méně času na úplné zpracování jednotlivých zpráv. V následujícím příkladu kódu se pomocí metody [GetMessages](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessages?view=azure-dotnet) získá 20 zpráv v jednom volání. Následně se každá zpráva zpracuje pomocí smyčky **foreach**. Také se pro každou zprávu nastaví časový limit neviditelnosti 5 minut. Pozor, 5minutový časový limit začíná pro všechny zprávy najednou, takže po uplynutí 5 minut od volání metody **GetMessages** pak budou všechny zprávy, které nebyly odstraněny, opět viditelné.

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

## <a name="get-the-queue-length"></a>Získání délky fronty

Podle potřeby můžete získat odhadovaný počet zpráv ve frontě. Metoda [FetchAttributes](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.fetchattributes?view=azure-dotnet) požádá Službu front o načtení atributů fronty, včetně počtu zpráv. Vlastnost [ApproximateMessageCount](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.approximatemessagecount?view=azure-dotnet) vrátí poslední hodnotu načtenou metodou **FetchAttributes** bez volání Služby front.

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
int? cachedMessageCount = queue.ApproximateMessageCount;

// Display number of messages.
Console.WriteLine("Number of messages in queue: " + cachedMessageCount);
```

## <a name="delete-a-queue"></a>Odstranění fronty

Pokud budete chtít odstranit frontu se všemi zprávami, které v ní jsou, zavolejte metodu [Delete](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.queue.cloudqueue.delete?view=azure-dotnet) pro objekt fronty.

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

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili základy používání služby Queue Storage, podívejte se na následujících odkazech na další informace o složitějších úlohách úložiště.

* Projděte si referenční dokumentaci ke Službě front, kde najdete úplné podrobnosti o dostupných rozhraních API:
  * [Klientská knihovna Storage pro .NET – referenční informace](https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
  * [REST API – referenční informace](https://msdn.microsoft.com/library/azure/dd179355)
* Zjistěte, jak můžete zjednodušit kód, který vytváříte, aby fungoval s Azure Storage, pomocí sady [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).
* Projděte si další průvodce funkcemi, kde najdete další informace o dalších možnostech pro ukládání dat v Azure.
  * [Začínáme s Azure Table Storage pomocí rozhraní .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md) pro ukládání strukturovaných dat
  * [Začínáme s Azure Blob Storage pomocí rozhraní .NET](../blobs/storage-dotnet-how-to-use-blobs.md) pro ukládání nestrukturovaných dat
  * [Připojení k SQL Database s použitím rozhraní .NET (C#)](../../sql-database/sql-database-connect-query-dotnet-core.md) pro uložení relačních dat

[Download and install the Azure SDK for .NET]: /develop/net/
[.NET client library reference]: https://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
[Creating an Azure Project in Visual Studio]: https://msdn.microsoft.com/library/azure/ee405487.aspx
[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[OData]: https://nuget.org/packages/Microsoft.Data.OData/5.0.2
[Edm]: https://nuget.org/packages/Microsoft.Data.Edm/5.0.2
[Spatial]: https://nuget.org/packages/System.Spatial/5.0.2
