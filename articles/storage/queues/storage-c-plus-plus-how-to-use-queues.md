---
title: Postup používání úložiště queue (C++) | Microsoft Docs
description: Naučte se používat fronty služby úložiště v Azure. Ukázky jsou napsané v jazyce C++.
services: storage
documentationcenter: .net
author: cbrooksmsft
manager: jahogg
editor: tysonn
ms.assetid: c8a36365-29f6-404d-8fd1-858a7f33b50a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: cpp
ms.topic: article
ms.date: 05/11/2017
ms.author: cbrooksmsft
ms.openlocfilehash: 5e81d5e0af9871099b7f921f355cf94249e4d30c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874049"
---
# <a name="how-to-use-queue-storage-from-c"></a>Postup používání úložiště Queue z jazyka C++
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Přehled
Tento průvodce vám ukáže, jak provádět běžné scénáře s využitím služby Azure Queue storage. Ukázky jsou napsané v C++ a použít [Klientská knihovna pro úložiště Azure pro jazyk C++](http://github.com/Azure/azure-storage-cpp/blob/master/README.md). Pokryté scénáře zahrnují **vkládání**, **prohlížení**, **získávání**, a **odstraňování** fronty zpráv, a také **vytváření a odstraňování front**.

> [!NOTE]
> Tato příručka se zaměřuje Klientská knihovna pro úložiště Azure pro jazyk C++ verze 1.0.0 a vyšší. Doporučená verze je klientská knihovna pro úložiště 2.2.0, která je k dispozici prostřednictvím [NuGet](http://www.nuget.org/packages/wastorage) nebo [Githubu](http://github.com/Azure/azure-storage-cpp/).
> 
> 

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Vytvoření aplikace C++
V této příručce bude používat funkce úložiště, které lze spustit v rámci aplikace C++.

V takovém případě budete muset nainstalovat Klientská knihovna pro úložiště Azure pro jazyk C++ a vytvoření účtu úložiště Azure ve vašem předplatném Azure.

Pokud chcete nainstalovat Klientská knihovna pro úložiště Azure pro jazyk C++, můžete použít následující metody:

* **Linux:** postupujte podle pokynů uvedených v [Klientská knihovna pro úložiště Azure pro C++ – soubor README](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) stránky.
* **Windows:** v sadě Visual Studio, klikněte na tlačítko **nástroje > Správce balíčků NuGet > Konzola správce balíčků**. Pomocí následujícího příkazu do [Konzola správce balíčků NuGet](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) a stiskněte klávesu **ENTER**.

```  
Install-Package wastorage
```

## <a name="configure-your-application-to-access-queue-storage"></a>Konfigurace aplikace pro přístup k Queue Storage
Přidejte následující příkazy na začátek souboru C++, ve které chcete používat službu Azure storage rozhraní API pro přístup k frontám obsahovat:  

```cpp
#include <was/storage_account.h>
#include <was/queue.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Nastavit připojovací řetězec úložiště Azure
Klienta Azure storage používá připojovací řetězec úložiště k ukládání koncových bodů a pověření pro přístup ke službám dat správy. Když spustíte v aplikaci klienta, je nutné zadat připojovací řetězec úložiště v následujícím formátu, pomocí názvu účtu úložiště a přístupový klíč úložiště pro účet úložiště uvedené v [portálu Azure](https://portal.azure.com) pro *AccountName* a *AccountKey* hodnoty. Informace o účtech úložiště a přístupové klávesy, najdete v části [o účtech úložiště Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). Tento příklad ukazuje, jak můžou deklarovat statické pole pro uložení připojovací řetězec:  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

K testování aplikace v místním počítači s Windows, můžete použít Microsoft Azure [emulátor úložiště](../common/storage-use-emulator.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) který se instaluje s [Azure SDK](https://azure.microsoft.com/downloads/). Emulátor úložiště je nástroj, který simuluje služby objektů Blob, fronty a tabulky, které jsou v Azure k dispozici na místním vývojovém počítači. Následující příklad ukazuje, jak můžou deklarovat statické pole pro uložení připojovací řetězec k vaší emulátor místního úložiště:  

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Spusťte emulátor úložiště Azure, vyberte **spustit** tlačítko nebo klikněte na tlačítko **Windows** klíč. Začněte psát **emulátoru úložiště Azure**a vyberte **emulátor úložiště Microsoft Azure** ze seznamu aplikací.

Následující ukázky předpokládejme, že používáte jednu z těchto dvou metod k získání připojovacího řetězce úložiště.

## <a name="retrieve-your-connection-string"></a>Načtení připojovacího řetězce
Můžete použít **cloud_storage_account** třída představující informací o vašem účtu úložiště. Chcete-li načíst informace o účtu úložiště z připojovacího řetězce úložiště, můžete použít **analyzovat** metoda.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="how-to-create-a-queue"></a>Postupy: vytvoření fronty
A **cloud_queue_client** objektu umožňuje získat odkaz na objekty pro fronty. Následující kód vytvoří **cloud_queue_client** objektu.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create a queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();
```

Použít **cloud_queue_client** objekt, který chcete získat odkaz na frontu, kterou chcete použít. Můžete vytvořit frontu, pokud neexistuje.

```cpp
// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
 queue.create_if_not_exists();  
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Postupy: vložit zprávu do fronty
Pokud chcete vložit zprávu do existující fronty, vytvořte nejdříve novou **cloud_queue_message**. Pak zavolejte **add_message** metoda. A **cloud_queue_message** můžete vytvořit buď z řetězce nebo **bajtů** pole. Tady je kód, který vytvoří frontu (pokud neexistuje) a vloží zprávu „Hello, World“:

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

## <a name="how-to-peek-at-the-next-message"></a>Postupy: zobrazení náhledu další zprávy
Můžete prohlížet zprávy ve frontě bez odebere ji z fronty voláním **peek_message** metoda.

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

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Postupy: Změna obsahu zpráv zařazených ve frontě
Podle potřeby můžete změnit obsah zprávy přímo ve frontě. Pokud zpráva představuje pracovní úlohu, mohli byste tuto funkci použít k aktualizaci stavu pracovních úloh. Následující kód aktualizuje zprávy ve frontě o nový obsah a prodlouží časový limit viditelnosti na 60 sekund. Uloží se tím stav práce spojený se zprávou a klient získá další minutu, aby mohl pokračovat ve zpracování zprávy. Tímto způsobem může sledovat vícekrokového pracovní postupy pro zprávy ve frontě, aniž by bylo nutné v případě, že krok zpracování z důvodu selhání hardwaru nebo softwaru selže, začít znovu od začátku. Obvykle byste udržovali také počet opakování, a pokud zpráva se pokus o více než n krát, odstranili byste ji. Je to ochrana proti tomu, aby zpráva při každém pokusu o zpracování nevyvolala chyby aplikace.

```cpp
// Retrieve storage account from connection-string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_conection_string);

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

## <a name="how-to-de-queue-the-next-message"></a>Postupy: zrušte další zprávy z fronty
Váš kód vyřazuje zprávy z fronty ve dvou krocích. Při volání **get_message**, získáte další zprávu ve frontě. Zpráva vrácená metodou **get_message** stane neviditelnou pro jakýkoli jiný kód čtení zpráv z této fronty. K dokončení odebrání zprávy z fronty, musíte také zavolat **delete_message**. Tento dvoukrokový proces odebrání zprávy zaručuje, aby v případě, že se vašemu kódu nepodaří zprávu zpracovat z důvodu selhání hardwaru nebo softwaru, mohla stejnou zprávu získat jiná instance vašeho kódu a bylo možné to zkusit znovu. Váš kód zavolá metodu **delete_message** pravým po zpracování zprávy.

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

## <a name="how-to-leverage-additional-options-for-de-queuing-messages"></a>Postupy: využívání dalších možností pro vyřazování zpráv z fronty
Načítání zpráv z fronty si můžete přizpůsobit dvěma způsoby. Za prvé si můžete načíst dávku zpráv (až 32). Za druhé si můžete nastavit delší nebo kratší časový limit neviditelnosti, aby měl váš kód více nebo méně času na úplné zpracování jednotlivých zpráv. Následující příklad kódu používá **get_messages** metoda získá 20 zpráv v jednom volání. Pak se každá zpráva zpracuje pomocí **pro** smyčky. Také se pro každou zprávu nastaví časový limit neviditelnosti 5 minut. Všimněte si, že 5 minut začíná pro všechny zprávy ve stejnou dobu, takže po 5 minutách mít předán od volání **get_messages**, všechny zprávy, které nebyly odstraněny, opět viditelné.

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

## <a name="how-to-delete-a-queue"></a>Postupy: odstranění fronty
Chcete-li odstranit frontu se všemi zprávami, které v ní, zavolejte **delete_queue_if_exists** metoda pro objekt fronty.

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
Teď, když jste se naučili základy používání služby Queue storage, postupujte podle následujících odkazech na další informace o službě Azure Storage.

* [Používání úložiště Blob z jazyka C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Postup používání úložiště Table z jazyka C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Seznam prostředků úložiště Azure v jazyce C++](../common/storage-c-plus-plus-enumeration.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Klientská knihovna pro úložiště pro C++ – referenční informace](http://azure.github.io/azure-storage-cpp)
* [Dokumentace k Azure Storage](https://azure.microsoft.com/documentation/services/storage/)