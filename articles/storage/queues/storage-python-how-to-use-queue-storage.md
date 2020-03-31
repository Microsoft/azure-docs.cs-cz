---
title: Jak používat úložiště fronty Azure v2.1 z Pythonu – Azure Storage
description: Zjistěte, jak pomocí služby Fronta Azure v2.1 z Pythonu vytvářet a odstraňovat fronty a vkládat, získat a odstraňovat zprávy.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/17/2019
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.custom: seo-javascript-october2019
ms.openlocfilehash: ca0831fd7554058d21e315b67d6965579af1d38b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060914"
---
# <a name="how-to-use-azure-queue-storage-v21-from-python"></a>Jak používat úložiště fronty Azure v2.1 z Pythonu

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

Tento článek ukazuje běžné scénáře pomocí služby úložiště Fronty Azure. Zahrnuté scénáře zahrnují vkládání, prohlížení, získávání a odstranění zpráv fronty a vytváření a mazání front.

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Přehled

Ukázky v tomto článku jsou napsány v Pythonu a použít [Sadu Microsoft Azure Storage SDK pro Python]. Další informace o frontách naleznete v části [Další kroky.](#next-steps)

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Stáhnout a nainstalovat sadu SDK služby Azure Storage pro Python

Sada [Azure Storage SDK pro Python](https://github.com/azure/azure-storage-python) vyžaduje Python verze 2.7, 3.3 nebo novější.
 
### <a name="install-via-pypi"></a>Instalace přes PyPi

Chcete-li nainstalovat prostřednictvím indexu balíčků Pythonu (PyPI), zadejte:

```bash
pip install azure-storage-queue==2.1.0
```

> [!NOTE]
> Pokud upgradujete z sady Azure Storage SDK pro Python verze 0.36 `pip uninstall azure-storage` nebo starší, odinstalujte starší sdk pomocí před instalací nejnovějšího balíčku.

Alternativní metody instalace najdete v [tématu Azure Storage SDK pro Python](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Zobrazit ukázkovou aplikaci

Pokud chcete zobrazit a spustit ukázkovou aplikaci, která ukazuje, jak používat Python s frontami Azure, přečtěte si informace [o azure storage: Začínáme s frontami Azure v Pythonu](https://github.com/Azure-Samples/storage-queue-python-getting-started). 

Chcete-li spustit ukázkovou aplikaci, `azure-storage-queue` ujistěte se, že jste nainstalovali oba balíčky a. `azure-storage-common`

## <a name="create-a-queue"></a>Vytvoření fronty

Objekt [QueueService](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice) umožňuje pracovat s frontami. Následující kód vytvoří `QueueService` objekt. V horní části libovolného souboru Pythonu, ve kterém chcete programově přistupovat k Úložišti Azure, přidejte následující:

```python
from azure.storage.queue import QueueService
```

Následující kód vytvoří `QueueService` objekt pomocí názvu účtu úložiště a klíče účtu. Nahraďte *můj účet* a *mykey* názvem a klíčem svého účtu.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="insert-a-message-into-a-queue"></a>Vložení zprávy do fronty

Chcete-li vložit zprávu do fronty, použijte metodu [put_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) k vytvoření nové zprávy a její přidání do fronty.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

Zprávy fronty Azure jsou uloženy jako text. Pokud chcete uložit binární data, nastavte funkce kódování base64 a dekódování na objekt služby fronty před uvedením zprávy do fronty.

```python
# setup queue Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

## <a name="peek-at-the-next-message"></a>Zobrazení náhledu další zprávy

Zprávu můžete nahlédnout před frontou, aniž byste ji odebrali z fronty voláním metody [peek_messages.](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#peek-messages-queue-name--num-messages-none--timeout-none-) Ve výchozím `peek_messages` nastavení se podívá na jednu zprávu.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="dequeue-messages"></a>Vyřazení zpráv ze zařazení do fronty

Kód odebere zprávu z fronty ve dvou krocích. Při volání [get_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-)se ve výchozím nastavení zobrazí další zpráva ve frontě. Zpráva vrácená `get_messages` z stane neviditelné pro jakýkoli jiný kód čtení zpráv z této fronty. Ve výchozím nastavení tato zpráva zůstává neviditelná po dobu 30 sekund. Chcete-li dokončit odebrání zprávy z fronty, musíte také volat [delete_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-message-queue-name--message-id--pop-receipt--timeout-none-). Tento dvoustupňový proces odebrání zprávy zajišťuje, že když váš kód nezpracuje zprávu z důvodu selhání hardwaru nebo softwaru, může jiná instance kódu získat stejnou zprávu a zkusit to znovu. Váš kód `delete_message` volá hned po zpracování zprávy.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

Načítání zpráv z fronty si můžete přizpůsobit dvěma způsoby. Za prvé si můžete načíst dávku zpráv (až 32). Za druhé si můžete nastavit delší nebo kratší časový limit neviditelnosti, aby měl váš kód více nebo méně času na úplné zpracování jednotlivých zpráv. Následující příklad kódu `get_messages` používá metodu k získání 16 zpráv v jednom volání. Pak zpracovává každou zprávu pomocí smyčky for. Také se pro každou zprávu nastaví časový limit neviditelnosti 5 minut.

```python
messages = queue_service.get_messages(
    'taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

## <a name="change-the-contents-of-a-queued-message"></a>Změna obsahu zpráv zařazených ve frontě

Podle potřeby můžete změnit obsah zprávy přímo ve frontě. Pokud zpráva představuje pracovní úlohu, mohli byste tuto funkci použít k aktualizaci stavu pracovních úloh. Níže uvedený kód používá [metodu update_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) k aktualizaci zprávy. Časový limit viditelnosti je nastaven na 0, což znamená, že zpráva se zobrazí okamžitě a obsah je aktualizován.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message(
        'taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="get-the-queue-length"></a>Získání délky fronty

Podle potřeby můžete získat odhadovaný počet zpráv ve frontě. Metoda [get_queue_metadata](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-queue-metadata-queue-name--timeout-none-) požádá službu fronty o vrácení metadat `approximate_message_count`o frontě a . Výsledek je pouze přibližný, protože zprávy mohou být přidány nebo odebrány poté, co služba fronty odpoví na váš požadavek.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="delete-a-queue"></a>Odstranění fronty

Chcete-li odstranit frontu a všechny zprávy v ní obsažené, zavolejte [metodu delete_queue.](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-queue-queue-name--fail-not-exist-false--timeout-none-)

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili základy úložiště front, postupujte podle těchto odkazů a dozvíte se další informace.

* [Odkaz na rozhraní API Pythonu azure fronty](/python/api/azure-storage-queue)
* [Středisko pro vývojáře programující v Pythonu](https://azure.microsoft.com/develop/python/)
* [REST API služby Azure Storage](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[Sada Microsoft Azure Storage SDK pro Python]: https://github.com/Azure/azure-storage-python
