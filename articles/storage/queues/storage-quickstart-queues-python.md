---
title: 'Úvodní příručka: Knihovna úložiště fronty Azure v12 – Python'
description: Naučte se používat knihovnu Azure Queue Python v12 k vytvoření fronty a přidání zpráv do fronty. Dále se dozvíte, jak číst a odstraňovat zprávy z fronty. Dozvíte se také, jak odstranit frontu.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/10/2019
ms.service: storage
ms.subservice: queues
ms.topic: quickstart
ms.openlocfilehash: d6ccd3cc61f9d8244874823be76496a4f4e1073c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78199763"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-python"></a>Úvodní příručka: Klientská knihovna úložiště fronty Azure v12 pro Python

Začínáme s klientskou knihovnou úložiště Fronty Azure verze 12 pro Python. Azure Queue Storage je služba pro ukládání velkého počtu zpráv pro pozdější načítání a zpracování. Následujícím postupem nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úkoly.

Pomocí klientské knihovny úložiště fronty Azure v12 pro Python:

* Vytvoření fronty
* Přidání zpráv do fronty
* Náhled na zprávy ve frontě
* Aktualizace zprávy ve frontě
* Příjem zpráv z fronty
* Odstranění zpráv z fronty
* Odstranění fronty

[Referenční dokumentace](https://docs.microsoft.com/python/api/azure-storage-queue/index) | rozhraní[API Ukázky zdrojového kódu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue) | [knihovny](https://docs.microsoft.com/azure/storage/common/storage-samples-python?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples) [(Python Package Index)](https://pypi.org/project/azure-storage-queue/) | 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [vytvořte si ho zdarma](https://azure.microsoft.com/free/)
* Účet úložiště Azure – [vytvoření účtu úložiště](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* [Python](https://www.python.org/downloads/) pro váš operační systém - 2.7, 3.5 nebo vyšší

## <a name="setting-up"></a>Nastavení

Tato část vás provede přípravou projektu pro práci s klientskou knihovnou úložiště fronty Azure v12 pro Python.

### <a name="create-the-project"></a>Vytvoření projektu

Vytvořte aplikaci Pythonu s názvem *queues-quickstart-v12*.

1. V okně konzoly (například cmd, PowerShell nebo Bash) vytvořte nový adresář pro projekt.

    ```console
    mkdir queues-quickstart-v12
    ```

1. Přepněte do nově vytvořeného *adresáře front-quickstart-v12.*

    ```console
    cd queues-quickstart-v12
    ```

### <a name="install-the-package"></a>Instalace balíčku

Nainstalujte klientskou knihovnu úložiště azure blob pro balíček Pythonpomocí příkazu. `pip install`

```console
pip install azure-storage-queue
```

Tento příkaz nainstaluje klientskou knihovnu úložiště fronty Azure pro balíček Pythonu a všechny knihovny, na kterých závisí. V tomto případě je to jen základní knihovna Azure pro Python.

### <a name="set-up-the-app-framework"></a>Nastavení architektury aplikace

1. Otevření nového textového souboru v editoru kódu
1. Přidat `import` příkazy
1. Vytvoření struktury programu, včetně velmi základní zpracování výjimek

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

1. Uložte nový soubor jako *queues-quickstart-v12.py* do *adresáře front-quickstart-v12.*

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>Objektový model

Azure Queue Storage je služba pro ukládání velkých objemů zpráv. Zpráva fronty může mít velikost až 64 kB. Fronta může obsahovat miliony zpráv až do celkového limitu kapacity účtu úložiště. Fronty se běžně používají k vytvoření nevyřízených položek práce pro zpracování asynchronně. Úložiště front nabízí tři typy prostředků:

* Účet úložiště
* Fronta v účtu úložiště
* Zprávy ve frontě

Na následujícím diagramu jsou vztahy těchto prostředků.

![Diagram architektury úložiště fronty](./media/storage-queues-introduction/queue1.png)

K interakci s těmito prostředky použijte následující třídy Pythonu:

* [QueueServiceClient](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueserviceclient): `QueueServiceClient` Umožňuje spravovat všechny fronty v účtu úložiště.
* [QueueClient](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient): `QueueClient` Třída umožňuje spravovat a manipulovat s jednotlivou frontou a jejími zprávami.
* [QueueMessage](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queuemessage): `QueueMessage` Třída představuje jednotlivé objekty vrácené při volání [receive_messages](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) ve frontě.

## <a name="code-examples"></a>Příklady kódu

Tyto ukázkové fragmenty kódu ukazují, jak provést následující akce s klientskou knihovnou úložiště Fronty Azure pro Python:

* [Získání připojovacího řetězce](#get-the-connection-string)
* [Vytvoření fronty](#create-a-queue)
* [Přidání zpráv do fronty](#add-messages-to-a-queue)
* [Náhled na zprávy ve frontě](#peek-at-messages-in-a-queue)
* [Aktualizace zprávy ve frontě](#update-a-message-in-a-queue)
* [Příjem zpráv z fronty](#receive-messages-from-a-queue)
* [Odstranění zpráv z fronty](#delete-messages-from-a-queue)
* [Odstranění fronty](#delete-a-queue)

### <a name="get-the-connection-string"></a>Získání připojovacího řetězce

Níže uvedený kód načte připojovací řetězec pro účet úložiště. Připojovací řetězec je uložen proměnnou prostředí vytvořenou v části [Konfigurovat připojovací řetězec úložiště.](#configure-your-storage-connection-string)

Přidejte tento `try` kód do bloku:

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

Rozhodněte o názvu nové fronty. Níže uvedený kód připojí hodnotu UUID k názvu fronty, aby bylo zajištěno, že je jedinečný.

> [!IMPORTANT]
> Názvy front mohou obsahovat pouze malá písmena, čísla a pomlčky a musí začínat písmenem nebo číslem. Před i za každým spojovníkem musí být jiný znak než spojovník. Název musí být také mezi 3 a 63 znaků dlouhé. Další informace o pojmenování front naleznete [v tématu Pojmenování front a metadat](https://docs.microsoft.com/rest/api/storageservices/naming-queues-and-metadata).

Vytvořte instanci třídy [QueueClient.](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient) Potom zavolejte [metodu create_queue](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#create-queue---kwargs-) k vytvoření fronty v účtu úložiště.

Přidejte tento kód na `try` konec bloku:

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

Následující fragment kódu přidá zprávy do fronty voláním [metody send_message.](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) Také uloží [QueueMessage](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queuemessage) vrácena z `send_message` třetího volání. Slouží `saved_message` k aktualizaci obsahu zprávy později v programu.

Přidejte tento kód na `try` konec bloku:

```python
    print("\nAdding messages to the queue...")

    # Send several messages to the queue
    queue_client.send_message(u"First message")
    queue_client.send_message(u"Second message")
    saved_message = queue_client.send_message(u"Third message")
```

### <a name="peek-at-messages-in-a-queue"></a>Náhled na zprávy ve frontě

Prohlédněte si zprávy ve frontě voláním [metody peek_messages.](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) Metoda `peek_messages` načte jednu nebo více zpráv z přední části fronty, ale nezmění viditelnost zprávy.

Přidejte tento kód na `try` konec bloku:

```python
    print("\nPeek at the messages in the queue...")

    # Peek at messages in the queue
    peeked_messages = queue_client.peek_messages(max_messages=5)

    for peeked_message in peeked_messages:
        # Display the message
        print("Message: " + peeked_message.content)
```

### <a name="update-a-message-in-a-queue"></a>Aktualizace zprávy ve frontě

Aktualizujte obsah zprávy voláním metody [update_message.](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-) Metoda `update_message` může změnit časový limit viditelnosti zprávy a obsah. Obsah zprávy musí být kódovaný řetězec UTF-8, který má velikost až 64 kB. Spolu s novým obsahem předavěte hodnoty ze zprávy, která byla uložena dříve v kódu. Hodnoty `saved_message` identifikují, kterou zprávu chcete aktualizovat.

```python
    print("\nUpdating the third message in the queue...")

    # Update a message using the message saved when calling send_message earlier
    queue_client.update_message(saved_message, pop_receipt=saved_message.pop_receipt, \
        content="Third message has been updated")
```

### <a name="receive-messages-from-a-queue"></a>Příjem zpráv z fronty

Stáhnout dříve přidané zprávy voláním [receive_messages](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) metody.

Přidejte tento kód na `try` konec bloku:

```python
    print("\nReceiving messages from the queue...")

    # Get messages from the queue
    messages = queue_client.receive_messages(messages_per_page=5)
```

### <a name="delete-messages-from-a-queue"></a>Odstranění zpráv z fronty

Po přijetí a zpracování odstraní zprávy z fronty. V tomto případě zpracování je pouze zobrazení zprávy na konzoli.

Aplikace pozastaví vstup uživatele `input` voláním před tím, než zpracuje a odstraní zprávy. Před odstraněním na [webu Azure Portal](https://portal.azure.com) ověřte, že prostředky byly vytvořeny správně. Všechny zprávy, které nejsou explicitně odstraněny, se nakonec znovu zobrazí ve frontě, aby byla možné je zpracovat.

Přidejte tento kód na `try` konec bloku:

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

Následující kód vyčistí prostředky, které aplikace vytvořila odstraněním fronty pomocí [metody delete_queue.](https://docs.microsoft.com/python/api/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-)

Přidejte tento kód na `try` konec bloku a uložte soubor:

```python
    print("\nPress Enter key to delete the queue...")
    input()

    # Clean up
    print("Deleting queue...")
    queue_client.delete_queue()

    print("Done")
```

## <a name="run-the-code"></a>Spuštění kódu

Tato aplikace vytvoří a přidá tři zprávy do fronty Azure. Kód uvádí zprávy ve frontě, pak načte a odstraní je, před konečnou odstranění fronty.

V okně konzoly přejděte do adresáře obsahujícího soubor `python` *queues-quickstart-v12.py* a spusťte následující příkaz ke spuštění aplikace.

```console
python queues-quickstart-v12.py
```

Výstup aplikace je podobný následujícímu příkladu:

```output
Azure Queue storage v12 - Python quickstart sample
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

Když se aplikace před příjemem zpráv pozastaví, zkontrolujte svůj účet úložiště na [webu Azure Portal](https://portal.azure.com). Ověřte, zda jsou zprávy ve frontě.

Stisknutím klávesy **Enter** přijměte a odstraňte zprávy. Po zobrazení výzvy znovu stisknete klávesu **Enter,** abyste odstranili frontu a dokončili ukázku.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili, jak vytvořit frontu a přidat do ní zprávy pomocí kódu Pythonu. Pak jste se naučili prohlížet, načítat a odstraňovat zprávy. Nakonec jste se naučili, jak odstranit frontu zpráv.

Výukové programy, ukázky, rychlé starty a další dokumentaci naleznete na adrese:

> [!div class="nextstepaction"]
> [Azure pro vývojáře Pythonu](https://docs.microsoft.com/azure/python/)

* Další informace najdete v [knihovnách Azure Storage pro Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage).
* Chcete-li zobrazit další ukázkové aplikace úložiště fronty Azure, pokračujte na [ukázky knihovny klienta Azure Queue v12 Pythonu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue/samples).
