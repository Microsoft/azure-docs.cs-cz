---
title: Jak používat úložiště front (C++) – Azure Storage
description: Přečtěte si, jak používat službu úložiště fronty v Azure. Ukázky jsou zapsány v jazyce C++.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/11/2017
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 4fe543010df9514cb2b22c56482a4b592574e917
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941780"
---
# <a name="how-to-use-queue-storage-from-c"></a>Používání úložiště Queue z C++
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Přehled
Tato příručka vám ukáže, jak provádět běžné scénáře pomocí služby úložiště Fronty Azure. Ukázky jsou napsané v C++ a využívají [klientskou knihovnu služby Azure Storage pro C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Zahrnuté scénáře zahrnují **vkládání**, **prohlížení**, **získávání**a **odstranění** zpráv fronty, stejně jako vytváření a **mazání front**.

> [!NOTE]
> Tato příručka je určená pro klientskou knihovnu služby Azure Storage pro C++ verze 1.0.0 nebo novější. Doporučená verze klientské knihovny služby Storage je 2.2.0, která je k dispozici přes [NuGet](https://www.nuget.org/packages/wastorage) nebo [GitHub](https://github.com/Azure/azure-storage-cpp/).
> 
> 

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Vytvoření aplikace C++
V této příručce budete používat funkce úložiště, které lze spustit v rámci aplikace Jazyka C++.

Abyste mohli pokračovat, musíte si nainstalovat klientskou knihovnu služby Azure Storage pro C++ a vytvořit ve svém předplatném účet úložiště Azure.

Klientskou knihovnu služby Azure Storage pro C++ můžete nainstalovat následujícími způsoby:

* **Linux:** Postupujte podle pokynů uvedených v [klientské knihovně úložiště Azure pro C++ README: Začínáme na](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux) stránce Linux.
* **Windows:** V systému Windows použijte [vcpkg](https://github.com/microsoft/vcpkg) jako správce závislostí. Postupujte podle [rychlého startu](https://github.com/microsoft/vcpkg#quick-start) a inicializovat vcpkg. Potom použijte následující příkaz k instalaci knihovny:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

Můžete najít návod, jak vytvořit zdrojový kód a exportovat do NuGet v souboru [README.](https://github.com/Azure/azure-storage-cpp#download--install)

## <a name="configure-your-application-to-access-queue-storage"></a>Konfigurace aplikace pro přístup k úložišti fronty
Přidejte následující příkazy zahrnout do horní části souboru C++, kde chcete použít Azure storage API pro přístup k frontám:  

```cpp
#include <was/storage_account.h>
#include <was/queue.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Nastavení připojovacího řetězce úložiště Azure
Klient úložiště Azure používá připojovací řetězec úložiště k uložení koncových bodů a přihlašovacích údajů pro přístup ke službám správy dat. Při spuštění v klientské aplikaci, musíte zadat připojovací řetězec úložiště v následujícím formátu, pomocí názvu účtu úložiště a přístupový klíč úložiště pro účet úložiště uvedené na [portálu Azure pro](https://portal.azure.com) *AccountName* a *AccountKey* hodnoty. Informace o účtech úložiště a přístupových klíčích najdete [v tématu O účtech úložiště Azure](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json). Tento příklad ukazuje deklaraci statického pole pro uložení připojovacího řetězce:  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Chcete-li otestovat aplikaci v místním počítači se systémem Windows, můžete použít [emulátor úložiště](../common/storage-use-emulator.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) Microsoft Azure, který je nainstalovaný pomocí [sady Azure SDK](https://azure.microsoft.com/downloads/). Emulátor úložiště je nástroj, který simuluje služby objektů Blob, Fronty a Tabulka dostupné v Azure na místním vývojovém počítači. Následující příklad ukazuje deklaraci statického pole pro uložení připojovacího řetězce k místnímu emulátoru úložiště:  

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Pokud chcete spustit emulátor úložiště Azure, vyberte tlačítko **Start** nebo stiskněte klávesu **Windows.** Začněte psát **Emulátor úložiště Azure**a ze seznamu aplikací vyberte **Emulátor úložiště Microsoft Azure.**

V následujících ukázkách se předpokládá, že jste pomocí některé z těchto dvou metod získali připojovací řetězec úložiště.

## <a name="retrieve-your-connection-string"></a>Načtení připojovacího řetězce
Třídu **cloud_storage_account** můžete použít k reprezentaci informací o účtu úložiště. K načtení informací o vašem účtu úložiště z připojovacího řetězce úložiště můžete použít metodu **parse**.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="how-to-create-a-queue"></a>Postup: Vytvoření fronty
Objekt **cloud_queue_client** umožňuje získat referenční objekty pro fronty. Následující kód vytvoří **cloud_queue_client** objekt.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create a queue client.
azure::storage::cloud_queue_client queue_client = storage_account.create_cloud_queue_client();
```

Pomocí **objektu cloud_queue_client** získáte odkaz na frontu, kterou chcete použít. Frontu můžete vytvořit, pokud neexistuje.

```cpp
// Retrieve a reference to a queue.
azure::storage::cloud_queue queue = queue_client.get_queue_reference(U("my-sample-queue"));

// Create the queue if it doesn't already exist.
 queue.create_if_not_exists();  
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Postup: Vložení zprávy do fronty
Chcete-li vložit zprávu do existující fronty, nejprve vytvořte novou **cloud_queue_message**. Dále zavolejte **metodu add_message.** **Cloud_queue_message** lze vytvořit z řetězce nebo **bajtového** pole. Tady je kód, který vytvoří frontu (pokud neexistuje) a vloží zprávu „Hello, World“:

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

## <a name="how-to-peek-at-the-next-message"></a>Postup: Náhled na další zprávu
Zprávu můžete nahlédnout před frontou, aniž byste ji odebrali z fronty voláním **metody peek_message.**

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

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Postup: Změna obsahu zprávy ve frontě
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

## <a name="how-to-de-queue-the-next-message"></a>Postup: Vyřazení další zprávy do fronty
Váš kód vyřazuje zprávy z fronty ve dvou krocích. Při volání **get_message**se zobrazí další zpráva ve frontě. Zpráva vrácená z **get_message** stane neviditelnou pro jakýkoli jiný kód čtení zpráv z této fronty. Chcete-li dokončit odebrání zprávy z fronty, musíte také volat **delete_message**. Tento dvoukrokový proces odebrání zprávy zaručuje, aby v případě, že se vašemu kódu nepodaří zprávu zpracovat z důvodu selhání hardwaru nebo softwaru, mohla stejnou zprávu získat jiná instance vašeho kódu a bylo možné to zkusit znovu. Váš kód volá **delete_message** hned po zpracování zprávy.

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

## <a name="how-to-leverage-additional-options-for-de-queuing-messages"></a>Postup: Využití dalších možností pro zrušení fronty zpráv
Načítání zpráv z fronty si můžete přizpůsobit dvěma způsoby. Za prvé si můžete načíst dávku zpráv (až 32). Za druhé si můžete nastavit delší nebo kratší časový limit neviditelnosti, aby měl váš kód více nebo méně času na úplné zpracování jednotlivých zpráv. Následující příklad kódu používá **metodu get_messages** k získání 20 zpráv v jednom volání. Pak zpracovává každou zprávu pomocí **smyčky for.** Také se pro každou zprávu nastaví časový limit neviditelnosti 5 minut. Všimněte si, že 5 minut začíná pro všechny zprávy ve stejnou dobu, takže po 5 minutách uplynulo od volání **get_messages**, všechny zprávy, které nebyly odstraněny budou znovu viditelné.

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
Podle potřeby můžete získat odhadovaný počet zpráv ve frontě. Metoda **download_attributes** požádá službu Queue o načtení atributů fronty, včetně počtu zpráv. Metoda **approximate_message_count** získá přibližný počet zpráv ve frontě.

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
Chcete-li odstranit frontu a všechny zprávy v ní obsažené, zavolejte metodu **delete_queue_if_exists** na objekt fronty.

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
Teď, když jste se naučili základy úložiště fronty, postupujte podle těchto odkazů další informace o Azure Storage.

* [Použití úložiště objektů blob z C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Použití úložiště tabulek z jazyka C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [Seznam prostředků úložiště Azure v jazyce C++](../common/storage-c-plus-plus-enumeration.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
* [Klientská knihovna úložiště pro odkaz na C++](https://azure.github.io/azure-storage-cpp)
* [Dokumentace k Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
