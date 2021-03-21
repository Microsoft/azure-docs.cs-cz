---
title: 'Rychlý Start: Klientská knihovna Azure Queue Storage V12-Python'
description: Naučte se, jak pomocí klientské knihovny Azure Queue Storage V12 pro Python vytvořit frontu a do ní přidat zprávy. Pak se dozvíte, jak číst a odstraňovat zprávy z fronty. Naučíte se také, jak odstranit frontu.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/10/2019
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-python
ms.openlocfilehash: 0eeb1333952533cdf392b93a926f256097557151
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97585492"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-python"></a>Rychlý Start: V12 klientské knihovny Azure Queue Storage pro Python

Začínáme s klientskou knihovnou Azure Queue Storage V12 pro Python. Azure Queue Storage je služba pro ukládání velkého počtu zpráv pro pozdější načtení a zpracování. Postupujte podle těchto kroků a nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úlohy.

Pomocí klientské knihovny Azure Queue Storage V12 pro Python:

- Vytvoření fronty
- Přidání zpráv do fronty
- Prohlížet zprávy ve frontě
- Aktualizace zprávy ve frontě
- Přijímání zpráv z fronty
- Odstranění zpráv z fronty
- Odstranění fronty

Další prostředky:

- [Referenční dokumentace k rozhraní API](/python/api/azure-storage-queue/index)
- [Zdrojový kód knihovny](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue)
- [Balíček (index balíčku Pythonu)](https://pypi.org/project/azure-storage-queue/)
- [ukázky](../common/storage-samples-python.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>Předpoklady

- Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/) .
- Účet Azure Storage – [Vytvoření účtu úložiště](../common/storage-account-create.md)
- [Python](https://www.python.org/downloads/) pro váš operační systém – 2,7, 3,5 nebo novější

## <a name="setting-up"></a>Nastavení

Tato část vás provede přípravou projektu pro práci s klientskou knihovnou Azure Queue Storage V12 for Python.

### <a name="create-the-project"></a>Vytvoření projektu

Vytvořte aplikaci v Pythonu s názvem `queues-quickstart-v12` .

1. V okně konzoly (například cmd, PowerShell nebo bash) vytvořte nový adresář pro projekt.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Přepněte do nově vytvořeného `queues-quickstart-v12` adresáře.

    ```console
    cd queues-quickstart-v12
    ```

### <a name="install-the-package"></a>Instalace balíčku

Nainstalujte knihovnu klienta Azure Blob Storage pro balíček python pomocí `pip install` příkazu.

```console
pip install azure-storage-queue
```

Tento příkaz nainstaluje knihovnu klienta Azure Queue Storage pro balíček Pythonu a všechny knihovny, na kterých závisí. V tomto případě je to jenom knihovna Azure Core pro Python.

### <a name="set-up-the-app-framework"></a>Nastavení aplikační architektury

1. Otevřít nový textový soubor v editoru kódu
1. Přidat `import` příkazy
1. Vytvoření struktury pro program, včetně velmi základního zpracování výjimek

    Zde je kód:

    ```python
    import os, uuid
    from azure.storage.queue import QueueServiceClient, QueueClient, QueueMessage

    try:
        print("Azure Queue storage v12 - Python quickstart sample")
        # Quick start code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)

    ```

1. Uložte nový soubor jako `queues-quickstart-v12.py` v `queues-quickstart-v12` adresáři.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektový model

Azure Queue Storage je služba pro ukládání velkých objemů zpráv. Zpráva fronty může mít velikost až 64 KB. Fronta může obsahovat miliony zpráv až do celkového limitu kapacity účtu úložiště. Fronty se běžně používají k vytváření nevyřízených položek pro asynchronní zpracování. Queue Storage nabízí tři typy prostředků:

- Účet úložiště
- Fronta v účtu úložiště
- Zprávy ve frontě

Na následujícím diagramu jsou vztahy těchto prostředků.

![Diagram architektury služby Queue Storage](./media/storage-queues-introduction/queue1.png)

Pro interakci s těmito prostředky použijte následující třídy Pythonu:

- [`QueueServiceClient`](/python/api/azure-storage-queue/azure.storage.queue.queueserviceclient): `QueueServiceClient` Umožňuje spravovat všechny fronty v účtu úložiště.
- [`QueueClient`](/python/api/azure-storage-queue/azure.storage.queue.queueclient): `QueueClient` Třída umožňuje spravovat a manipulovat s jednotlivou frontou a jejími zprávami.
- [`QueueMessage`](/python/api/azure-storage-queue/azure.storage.queue.queuemessage): `QueueMessage` Třída představuje jednotlivé objekty vracené při volání [`receive_messages`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) ve frontě.

## <a name="code-examples"></a>Příklady kódu

Tyto ukázkové fragmenty kódu ukazují, jak provést následující akce s klientskou knihovnou Azure Queue Storage pro Python:

- [Získání připojovacího řetězce](#get-the-connection-string)
- [Vytvoření fronty](#create-a-queue)
- [Přidání zpráv do fronty](#add-messages-to-a-queue)
- [Prohlížet zprávy ve frontě](#peek-at-messages-in-a-queue)
- [Aktualizace zprávy ve frontě](#update-a-message-in-a-queue)
- [Přijímání zpráv z fronty](#receive-messages-from-a-queue)
- [Odstranění zpráv z fronty](#delete-messages-from-a-queue)
- [Odstranění fronty](#delete-a-queue)

### <a name="get-the-connection-string"></a>Získání připojovacího řetězce

Následující kód načte připojovací řetězec pro účet úložiště. Připojovací řetězec ukládá proměnnou prostředí vytvořenou v části [Konfigurace připojovacího řetězce úložiště](#configure-your-storage-connection-string) .

Přidejte tento kód do `try` bloku:

```python
    # Retrieve the connection string for use with the application. The storage
    # connection string is stored in an environment variable on the machine
    # running the application called AZURE_STORAGE_CONNECTION_STRING. If the
    # environment variable is created after the application is launched in a
    # console or with Visual Studio, the shell or application needs to be
    # closed and reloaded to take the environment variable into account.
    connect_str = os.getenv('AZURE_STORAGE_CONNECTION_STRING')
```

### <a name="create-a-queue"></a>Vytvoření fronty

Určete název nové fronty. Následující kód připojí k názvu fronty hodnotu UUID, aby bylo zajištěno, že je jedinečný.

> [!IMPORTANT]
> Názvy front můžou obsahovat jenom malá písmena, číslice a spojovníky a musí začínat písmenem nebo číslicí. Před i za každým spojovníkem musí být jiný znak než spojovník. Název musí mít také délku 3 až 63 znaků. Další informace najdete v tématu [pojmenování front a metadat](/rest/api/storageservices/naming-queues-and-metadata).

Vytvořte instanci [`QueueClient`](/python/api/azure-storage-queue/azure.storage.queue.queueclient) třídy. Pak zavolejte [`create_queue`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#create-queue---kwargs-) metodu pro vytvoření fronty ve vašem účtu úložiště.

Přidejte tento kód na konec `try` bloku:

```python
    # Create a unique name for the queue
    queue_name = "quickstartqueues-" + str(uuid.uuid4())

    print("Creating queue: " + queue_name)

    # Instantiate a QueueClient which will be
    # used to create and manipulate the queue
    queue_client = QueueClient.from_connection_string(connect_str, queue_name)

    # Create the queue
    queue_client.create_queue()
```

### <a name="add-messages-to-a-queue"></a>Přidání zpráv do fronty

Následující fragment kódu přidá zprávy do fronty voláním [`send_message`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) metody. Ukládá také [`QueueMessage`](/python/api/azure-storage-queue/azure.storage.queue.queuemessage) vrácené třetí `send_message` volání. `saved_message`Používá se k aktualizaci obsahu zprávy později v programu.

Přidejte tento kód na konec `try` bloku:

```python
    print("\nAdding messages to the queue...")

    # Send several messages to the queue
    queue_client.send_message(u"First message")
    queue_client.send_message(u"Second message")
    saved_message = queue_client.send_message(u"Third message")
```

### <a name="peek-at-messages-in-a-queue"></a>Prohlížet zprávy ve frontě

Podívejte se na zprávy ve frontě tím, že zavoláte [`peek_messages`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) metodu. Tato metoda načte jednu nebo více zpráv z přední části fronty, ale nezmění viditelnost zprávy.

Přidejte tento kód na konec `try` bloku:

```python
    print("\nPeek at the messages in the queue...")

    # Peek at messages in the queue
    peeked_messages = queue_client.peek_messages(max_messages=5)

    for peeked_message in peeked_messages:
        # Display the message
        print("Message: " + peeked_message.content)
```

### <a name="update-a-message-in-a-queue"></a>Aktualizace zprávy ve frontě

Aktualizujte obsah zprávy voláním [`update_message`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-) metody. Tato metoda může změnit časový limit a obsah viditelnosti zprávy. Obsah zprávy musí být řetězec kódovaný v kódování UTF-8, který má velikost až 64 KB. Společně s novým obsahem předejte hodnoty z zprávy, která byla uložena dříve v kódu. `saved_message`Hodnoty identifikují, která zpráva se má aktualizovat.

```python
    print("\nUpdating the third message in the queue...")

    # Update a message using the message saved when calling send_message earlier
    queue_client.update_message(saved_message, pop_receipt=saved_message.pop_receipt, \
        content="Third message has been updated")
```

### <a name="receive-messages-from-a-queue"></a>Přijímání zpráv z fronty

Stažení dříve přidaných zpráv voláním [`receive_messages`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) metody.

Přidejte tento kód na konec `try` bloku:

```python
    print("\nReceiving messages from the queue...")

    # Get messages from the queue
    messages = queue_client.receive_messages(messages_per_page=5)
```

### <a name="delete-messages-from-a-queue"></a>Odstranění zpráv z fronty

Po přijetí a zpracování odstraní zprávy z fronty. V tomto případě se zpracovává jenom zpráva v konzole nástroje.

Aplikace pozastaví vstup uživatele voláním `input` před jeho zpracováním a odstraněním zpráv. Před odstraněním ověřte ve svém [Azure Portal](https://portal.azure.com) , že se prostředky vytvořily správně. Všechny zprávy, které se explicitně neodstraní, se později stanou viditelnými ve frontě, aby se mohly zpracovat další šance na jejich zpracování.

Přidejte tento kód na konec `try` bloku:

```python
    print("\nPress Enter key to 'process' messages and delete them from the queue...")
    input()

    for msg_batch in messages.by_page():
            for msg in msg_batch:
                # "Process" the message
                print(msg.content)
                # Let the service know we're finished with
                # the message and it can be safely deleted.
                queue_client.delete_message(msg)
```

### <a name="delete-a-queue"></a>Odstranění fronty

Následující kód vyčistí prostředky, které aplikace vytvořila, odstraněním fronty pomocí [`delete_queue`](/python/api/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-) metody.

Přidejte tento kód na konec `try` bloku a uložte soubor:

```python
    print("\nPress Enter key to delete the queue...")
    input()

    # Clean up
    print("Deleting queue...")
    queue_client.delete_queue()

    print("Done")
```

## <a name="run-the-code"></a>Spuštění kódu

Tato aplikace vytvoří a přidá tři zprávy do fronty Azure. Kód vypíše zprávy ve frontě a pak je načte a odstraní před tím, než se nakonec odstraní fronta.

V okně konzoly přejděte do adresáře obsahujícího `queues-quickstart-v12.py` soubor a pak použijte následující `python` příkaz ke spuštění aplikace.

```console
python queues-quickstart-v12.py
```

Výstup aplikace je podobný následujícímu příkladu:

```output
Azure Queue Storage client library v12 - Python quickstart sample
Creating queue: quickstartqueues-cac365be-7ce6-4065-bd65-3756ea052cb8

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Receiving messages from the queue...

Press Enter key to 'process' messages and delete them from the queue...

First message
Second message
Third message has been updated

Press Enter key to delete the queue...

Deleting queue...
Done
```

Když se aplikace před přijetím zpráv pozastaví, ověřte si účet úložiště v [Azure Portal](https://portal.azure.com). Ověřte, zda jsou zprávy ve frontě.

Pokud `Enter` chcete zprávy přijmout a odstranit, stiskněte klávesu. Po zobrazení výzvy znovu stiskněte klávesu `Enter` a odstraňte frontu a dokončete ukázku.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak vytvořit frontu a přidat do ní zprávy pomocí kódu Pythonu. Pak jste se naučili prohlížet, načítat a odstraňovat zprávy. Nakonec jste zjistili, jak odstranit frontu zpráv.

Kurzy, ukázky, rychlé starty a další dokumentace najdete na webu:

> [!div class="nextstepaction"]
> [Azure pro vývojáře v Pythonu](/azure/python/)

- Další informace najdete v tématu [knihovny Azure Storage pro Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage).
- Další ukázkové aplikace pro Azure Queue Storage najdete v tématu [azure Queue Storage Client Library V12 for Python-Samples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue/samples).
