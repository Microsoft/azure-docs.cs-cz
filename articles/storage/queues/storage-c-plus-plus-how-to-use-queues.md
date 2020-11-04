---
title: Použití úložiště Queue (C++) – Azure Storage
description: Naučte se používat službu Queue Storage v Azure. Ukázky jsou napsány v jazyce C++.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/16/2020
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: dineshm
ms.openlocfilehash: f31a883a2b10f37d6a4a7a91fff37739e340ac60
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348844"
---
# <a name="how-to-use-queue-storage-from-c"></a>Používání úložiště Queue z C++

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Přehled

Tato příručka vám ukáže, jak provádět běžné scénáře pomocí služby Azure Queue Storage. Ukázky jsou napsané v C++ a využívají [klientskou knihovnu služby Azure Storage pro C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Mezi zahrnuté scénáře patří **vkládání** , **prohlížení** , **získávání** a **odstraňování** zpráv fronty a **vytváření a odstraňování front**.

> [!NOTE]
> Tato příručka je určená pro klientskou knihovnu služby Azure Storage pro C++ verze 1.0.0 nebo novější. Doporučená verze klientské knihovny služby Storage je 2.2.0, která je k dispozici přes [NuGet](https://www.nuget.org/packages/wastorage) nebo [GitHub](https://github.com/Azure/azure-storage-cpp/).

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Vytvoření aplikace C++

V této příručce budete používat funkce úložiště, které se dají spouštět v rámci aplikace v jazyce C++.

Abyste mohli pokračovat, musíte si nainstalovat klientskou knihovnu služby Azure Storage pro C++ a vytvořit ve svém předplatném účet úložiště Azure.

Klientskou knihovnu služby Azure Storage pro C++ můžete nainstalovat následujícími způsoby:

- **Linux:** Postupujte podle pokynů uvedených v tématu [Azure Storage Client Library for C++ Ready: Začínáme na stránce systému Linux](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux) .
- **Windows:** Ve Windows použijte [vcpkg](https://github.com/microsoft/vcpkg) jako správce závislostí. Postupujte podle pokynů v [rychlém](https://github.com/microsoft/vcpkg#quick-start) startu a inicializujte vcpkg. Potom pomocí následujícího příkazu nainstalujte knihovnu:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

Můžete najít průvodce pro sestavení zdrojového kódu a exportovat ho do NuGet v souboru [Readme](https://github.com/Azure/azure-storage-cpp#download--install) .

## <a name="configure-your-application-to-access-queue-storage"></a>Konfigurace aplikace pro přístup k Queue Storage

Do horní části souboru C++ přidejte následující příkazy include, u kterých chcete používat rozhraní API služby Azure Storage pro přístup k frontám:

```cpp
#include <was/storage_account.h>
#include <was/queue.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Nastavení připojovacího řetězce služby Azure Storage

Klient úložiště Azure používá připojovací řetězec úložiště k uložení koncových bodů a přihlašovacích údajů pro přístup ke službám správy dat. Při spuštění v klientské aplikaci musíte zadat připojovací řetězec úložiště v následujícím formátu s použitím názvu účtu úložiště a přístupového klíče úložiště pro účet úložiště, který je uvedený v [Azure Portal](https://portal.azure.com) pro hodnoty *account* a *AccountKey* . Informace o účtech úložiště a přístupových klíčích najdete v tématu [informace o Azure Storagech účtech](../common/storage-account-create.md?toc=%252fazure%252fstorage%252fqueues%252ftoc.json). Tento příklad ukazuje deklaraci statického pole pro uložení připojovacího řetězce:

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

K otestování aplikace na místním počítači s Windows můžete použít [emulátor úložiště Azurite](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). Azurite je nástroj, který simuluje služby objektů BLOB a front, které jsou dostupné v Azure, na vašem místním vývojovém počítači. Následující příklad ukazuje deklaraci statického pole pro uložení připojovacího řetězce k místnímu emulátoru úložiště:

```cpp
// Define the connection-string with Azurite.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Pokud chcete začít Azurite, přečtěte si téma [použití emulátoru Azurite pro vývoj místních Azure Storage](../common/storage-use-azurite.md).

V následujících ukázkách se předpokládá, že jste pomocí některé z těchto dvou metod získali připojovací řetězec úložiště.

## <a name="retrieve-your-connection-string"></a>Načtení připojovacího řetězce

K reprezentaci informací o účtu úložiště můžete použít třídu **cloud_storage_account** . K načtení informací o vašem účtu úložiště z připojovacího řetězce úložiště můžete použít metodu **parse**.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="how-to-create-a-queue"></a>Postupy: vytvoření fronty

Objekt **cloud_queue_client** umožňuje získat referenční objekty pro fronty. Následující kód vytvoří objekt **cloud_queue_client** .

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create a queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();
```

Pomocí objektu **cloud_queue_client** získat odkaz na frontu, kterou chcete použít. Tuto frontu můžete vytvořit, pokud neexistuje.

```cpp
// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
 queue.create_if_not_exists();  
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Postupy: vložení zprávy do fronty

Chcete-li vložit zprávu do existující fronty, vytvořte nejprve novou **cloud_queue_message**. Dále zavolejte metodu **add_message** . **Cloud_queue_message** lze vytvořit buď z řetězce, nebo z pole **bajtů** . Tady je kód, který vytvoří frontu (pokud neexistuje) a vloží zprávu „Hello, World“:

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
queue.create_if_not_exists();

// Create a message and add it to the queue.
azure::storage::cloud_queue_message message1(U("Hello, World"));
queue.add_message(message1);  
```

## <a name="how-to-peek-at-the-next-message"></a>Postupy: prohlížení další zprávy

Můžete prohlížet zprávy před frontou, aniž byste je museli odebírat z fronty voláním metody **peek_message** .

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Peek at the next message.
azure::storage::cloud_queue_message peeked_message = queue.peek_message();

// Output the message content.
std::wcout << U("Peeked message content: ") << peeked_message.content_as_string() << std::endl;
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Postupy: Změna obsahu zprávy ve frontě

Podle potřeby můžete změnit obsah zprávy přímo ve frontě. Pokud zpráva představuje pracovní úlohu, mohli byste tuto funkci použít k aktualizaci stavu pracovních úloh. Následující kód aktualizuje zprávy ve frontě o nový obsah a prodlouží časový limit viditelnosti na 60 sekund. Uloží se tím stav práce spojený se zprávou a klient získá další minutu, aby mohl pokračovat ve zpracování zprávy. Tímto způsobem může sledovat vícekrokového pracovní postupy pro zprávy ve frontě, aniž by bylo nutné v případě, že krok zpracování z důvodu selhání hardwaru nebo softwaru selže, začít znovu od začátku. Obvykle byste udržovali také hodnotu počtu opakování, a pokud by se pokus o zpracování zprávy opakoval více než nkrát, odstranili byste ji. Je to ochrana proti tomu, aby zpráva při každém pokusu o zpracování nevyvolala chyby aplikace.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Get the message from the queue and update the message contents.
// The visibility timeout "0" means make it visible immediately.
// The visibility timeout "60" means the client can get another minute to continue
// working on the message.
azure::storage::cloud_queue_message changed_message = queue.get_message();

changed_message.set_content(U("Changed message"));
queue.update_message(changed_message, std::chrono::seconds(60), true);

// Output the message content.
std::wcout << U("Changed message content: ") << changed_message.content_as_string() << std::endl;  
```

## <a name="how-to-de-queue-the-next-message"></a>Postupy: zrušení zařazení další zprávy do fronty

Váš kód vyřazuje zprávy z fronty ve dvou krocích. Když zavoláte **get_message** , dostanete další zprávu ve frontě. Zpráva vrácená z **get_message** bude neviditelná pro jakýkoliv jiný kód, který čte zprávy z této fronty. Chcete-li dokončit odebrání zprávy z fronty, je nutné také volat **delete_message**. Tento dvoukrokový proces odebrání zprávy zaručuje, aby v případě, že se vašemu kódu nepodaří zprávu zpracovat z důvodu selhání hardwaru nebo softwaru, mohla stejnou zprávu získat jiná instance vašeho kódu a bylo možné to zkusit znovu. Váš kód volá **delete_message** hned po zpracování zprávy.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Get the next message.
azure::storage::cloud_queue_message dequeued_message = queue.get_message();
std::wcout << U("Dequeued message: ") << dequeued_message.content_as_string() << std::endl;

// Delete the message.
queue.delete_message(dequeued_message);
```

## <a name="how-to-leverage-additional-options-for-de-queuing-messages"></a>Postupy: využití dalších možností pro zprávy ve více frontách

Načítání zpráv z fronty si můžete přizpůsobit dvěma způsoby. Za prvé si můžete načíst dávku zpráv (až 32). Za druhé si můžete nastavit delší nebo kratší časový limit neviditelnosti, aby měl váš kód více nebo méně času na úplné zpracování jednotlivých zpráv. Následující příklad kódu používá metodu **get_messages** k získání 20 zpráv v jednom volání. Potom zpracuje každou zprávu pomocí smyčky **for** . Také se pro každou zprávu nastaví časový limit neviditelnosti 5 minut. Všimněte si, že 5 minut začne u všech zpráv současně, takže po uplynutí 5 minut od volání **get_messages** se všechny zprávy, které nebyly odstraněny, opět zobrazí.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Dequeue some queue messages (maximum 32 at a time) and set their visibility timeout to
// 5 minutes (300 seconds).
azure::storage::queue_request_options options;
azure::storage::operation_context context;

// Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
std::vector<azure::storage::cloud_queue_message> messages = queue.get_messages(20, std::chrono::seconds(300), options, context);

for (auto it = messages.cbegin(); it != messages.cend(); ++it)
{
    // Display the contents of the message.
    std::wcout << U("Get: ") << it->content_as_string() << std::endl;
}
```

## <a name="how-to-get-the-queue-length"></a>Postupy: získání délky fronty

Podle potřeby můžete získat odhadovaný počet zpráv ve frontě. Metoda **download_attributes** požádá služba front o načtení atributů fronty, včetně počtu zpráv. Metoda **approximate_message_count** získá přibližný počet zpráv ve frontě.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Fetch the queue attributes.
queue.download_attributes();

// Retrieve the cached approximate message count.
int cachedMessageCount = queue.approximate_message_count();

// Display number of messages.
std::wcout << U("Number of messages in queue: ") << cachedMessageCount << std::endl;  
```

## <a name="how-to-delete-a-queue"></a>Postupy: odstranění fronty

Pokud chcete odstranit frontu a všechny zprávy, které jsou v ní obsažené, zavolejte metodu **delete_queue_if_exists** u objektu Queue.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();

// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// If the queue exists and delete it.
queue.delete_queue_if_exists();  
```

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili se základy úložiště front, můžete získat další informace o Azure Storage pomocí těchto odkazů.

- [Použití Blob Storage z C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
- [Použití Table Storage z C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
- [Seznam prostředků Azure Storage v jazyce C++](../common/storage-c-plus-plus-enumeration.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Reference ke klientské knihovně pro úložiště pro C++](https://azure.github.io/azure-storage-cpp)
- [Dokumentace k Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
