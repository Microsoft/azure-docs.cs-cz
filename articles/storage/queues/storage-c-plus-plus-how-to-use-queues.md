---
title: Používání úložiště queue (C++) | Dokumentace Microsoftu
description: Další informace o použití služby úložiště front v Azure. Ukázky jsou napsané v jazyce C++.
services: storage
author: cbrooksmsft
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: cpp
ms.topic: article
ms.date: 05/11/2017
ms.author: cbrooksmsft
ms.subservice: queues
ms.openlocfilehash: b1a566c4179e940b82790c69b3036e3be9e352cc
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55564523"
---
# <a name="how-to-use-queue-storage-from-c"></a>Používání úložiště Queue z jazyka C++
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Přehled
Tato příručka se ukazují, jak provádět běžné scénáře pomocí služby Azure Queue storage. Ukázky jsou napsané v C++ a využívají [klientskou knihovnu služby Azure Storage pro C++](http://github.com/Azure/azure-storage-cpp/blob/master/README.md). Mezi popsané scénáře patří **vkládání**, **prohlížení**, **získávání**, a **odstranění** fronty zpráv, stejně jako  **vytváření a odstraňování front**.

> [!NOTE]
> Tato příručka je určená pro klientskou knihovnu služby Azure Storage pro C++ verze 1.0.0 nebo novější. Doporučená verze klientské knihovny služby Storage je 2.2.0, která je k dispozici přes [NuGet](http://www.nuget.org/packages/wastorage) nebo [GitHub](http://github.com/Azure/azure-storage-cpp/).
> 
> 

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Vytvoření aplikace C++
V této příručce se bude používat funkce úložiště, které můžou běžet v rámci aplikace v jazyce C++.

Abyste mohli pokračovat, musíte si nainstalovat klientskou knihovnu služby Azure Storage pro C++ a vytvořit ve svém předplatném účet úložiště Azure.

Klientskou knihovnu služby Azure Storage pro C++ můžete nainstalovat následujícími způsoby:

* **Linux:** Postupujte podle pokynů v [Klientská knihovna Azure Storage pro C++ – soubor README](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) stránky.
* **Windows:** V sadě Visual Studio klikněte na **Nástroje > Správce balíčků NuGet > Konzola Správce balíčků**. Zadejte následující příkaz do [Konzola správce balíčků NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) a stiskněte klávesu **ENTER**.

```powershell
Install-Package wastorage
```

## <a name="configure-your-application-to-access-queue-storage"></a>Konfigurace aplikace pro přístup k úložišti front
Přidejte následující příkazy do horní části souboru C++, ve které chcete použít pro přístup k frontám rozhraním API v Azure storage zahrnují:  

```cpp
#include <was/storage_account.h>
#include <was/queue.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Nastavení připojovacího řetězce služby Azure storage
Klient úložiště Azure používá připojovací řetězec úložiště k uložení koncových bodů a přihlašovacích údajů pro přístup ke službám správy dat. Při spuštění v klientské aplikaci, musíte zadat připojovací řetězec úložiště v následujícím formátu, pomocí názvu účtu úložiště a přístupový klíč úložiště pro účet úložiště uvedený v seznamu [webu Azure Portal](https://portal.azure.com) pro *AccountName* a *AccountKey* hodnoty. Informace o účtech úložiště a přístupové klíče najdete v tématu [o účtech Azure Storage](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). Tento příklad ukazuje deklaraci statického pole pro uložení připojovacího řetězce:  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

K testování aplikace v místním počítači Windows, můžete použít Microsoft Azure [emulátor úložiště](../common/storage-use-emulator.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) , který se instaluje s [sady Azure SDK](https://azure.microsoft.com/downloads/). Emulátor úložiště je nástroj, který simuluje objektů Blob, Queue a Table služeb dostupných v Azure na svém místním vývojovém počítači. Následující příklad ukazuje deklaraci statického pole pro uložení připojovacího řetězce k místnímu emulátoru úložiště:  

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Chcete-li spustit emulátor úložiště Azure, vyberte **Start** tlačítko nebo stisknutím klávesy **Windows** klíč. Začněte psát **emulátoru úložiště Azure**a vyberte **emulátor úložiště Microsoft Azure** ze seznamu aplikací.

V následujících ukázkách se předpokládá, že jste pomocí některé z těchto dvou metod získali připojovací řetězec úložiště.

## <a name="retrieve-your-connection-string"></a>Načtení připojovacího řetězce
Můžete použít **cloud_storage_account** pro reprezentaci informace o vašem účtu úložiště. K načtení informací o vašem účtu úložiště z připojovacího řetězce úložiště můžete použít metodu **parse**.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="how-to-create-a-queue"></a>Postup: Vytvoření fronty
A **cloud_queue_client** objektu umožňuje získat odkaz na objekty pro fronty. Následující kód vytvoří **cloud_queue_client** objektu.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create a queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();
```

Použít **cloud_queue_client** můžete získat odkaz na frontu, kterou chcete použít. Můžete vytvořit frontu, pokud neexistuje.

```cpp
// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
 queue.create_if_not_exists();  
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Postup: Vložení zprávy do fronty
Chcete-li vložit zprávu do existující fronty, vytvořte nejdřív nový **cloud_queue_message**. Pak zavolejte **add_message** metody. A **cloud_queue_message** lze vytvořit buď z řetězce nebo **bajtů** pole. Tady je kód, který vytvoří frontu (pokud neexistuje) a vloží zprávu „Hello, World“:

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

## <a name="how-to-peek-at-the-next-message"></a>Postup: Zobrazení náhledu další zprávy
Můžete prohlížet zprávy ve frontě bez odebrání z fronty pomocí volání **peek_message** metody.

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

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Postup: Změna obsahu zpráv zařazených ve frontě
Podle potřeby můžete změnit obsah zprávy přímo ve frontě. Pokud zpráva představuje pracovní úlohu, mohli byste tuto funkci použít k aktualizaci stavu pracovních úloh. Následující kód aktualizuje zprávy ve frontě o nový obsah a prodlouží časový limit viditelnosti na 60 sekund. Uloží se tím stav práce spojený se zprávou a klient získá další minutu, aby mohl pokračovat ve zpracování zprávy. Tímto způsobem může sledovat vícekrokového pracovní postupy pro zprávy ve frontě, aniž by bylo nutné v případě, že krok zpracování z důvodu selhání hardwaru nebo softwaru selže, začít znovu od začátku. Obvykle byste udržovali také hodnotu počtu opakování, a pokud se zpráva, je možné vícekrát, odstranili byste ji. Je to ochrana proti tomu, aby zpráva při každém pokusu o zpracování nevyvolala chyby aplikace.

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

## <a name="how-to-de-queue-the-next-message"></a>Postup: Vyřazení další zprávy z fronty
Váš kód vyřazuje zprávy z fronty ve dvou krocích. Při volání **get_message**, získáte další zprávu ve frontě. Zpráva vrácená metodou **get_message** stane neviditelnou pro jakýkoli jiný kód přečte zprávy z této fronty. K dokončení odebrání zprávy z fronty, musíte také zavolat **delete_message**. Tento dvoukrokový proces odebrání zprávy zaručuje, aby v případě, že se vašemu kódu nepodaří zprávu zpracovat z důvodu selhání hardwaru nebo softwaru, mohla stejnou zprávu získat jiná instance vašeho kódu a bylo možné to zkusit znovu. Kód volá **delete_message** hned po zpracování zprávy.

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

## <a name="how-to-leverage-additional-options-for-de-queuing-messages"></a>Postup: Využívání dalších možností pro vyřazování zpráv z fronty
Načítání zpráv z fronty si můžete přizpůsobit dvěma způsoby. Za prvé si můžete načíst dávku zpráv (až 32). Za druhé si můžete nastavit delší nebo kratší časový limit neviditelnosti, aby měl váš kód více nebo méně času na úplné zpracování jednotlivých zpráv. Následující příklad kódu používá **get_messages** metodu k získání 20 zpráv v jednom volání. Pak se každá zpráva zpracuje pomocí **pro** smyčky. Také se pro každou zprávu nastaví časový limit neviditelnosti 5 minut. Všimněte si, že začíná pro všechny zprávy ve stejnou dobu, tak po mít 5 minut předané od posledního volání **get_messages**, všechny zprávy, které nebyly odstraněny, opět viditelné.

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

## <a name="how-to-get-the-queue-length"></a>Postup: Získání délky fronty
Podle potřeby můžete získat odhadovaný počet zpráv ve frontě. **Download_attributes** metoda požádá službu front o načtení atributů fronty, včetně počtu zpráv. **Approximate_message_count** metoda získá přibližný počet zpráv ve frontě.

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

## <a name="how-to-delete-a-queue"></a>Postup: Odstranění fronty
Chcete-li odstranit frontu se všemi zprávami, které v ní, zavolejte **delete_queue_if_exists** metodu na objekt fronty.

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

## <a name="next-steps"></a>Další postup
Teď, když jste se naučili základy používání služby Queue storage, použijte tyto odkazy na další informace o službě Azure Storage.

* [Používání úložiště Blob z jazyka C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Používání úložiště Table z jazyka C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Seznam prostředků služby Azure Storage v C++](../common/storage-c-plus-plus-enumeration.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Klientská knihovna pro úložiště pro C++ – referenční informace](http://azure.github.io/azure-storage-cpp)
* [Dokumentace k Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
