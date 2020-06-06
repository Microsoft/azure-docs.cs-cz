---
title: Použití Azure Queue Storage v 2.1 z Pythonu – Azure Storage
description: Naučte se používat Azure Služba front v 2.1 z Pythonu k vytváření a odstraňování front a vkládání, získávání a odstraňování zpráv.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/17/2019
ms.service: storage
ms.subservice: queues
ms.topic: how-to
ms.reviewer: cbrooks
ms.custom: seo-javascript-october2019
ms.openlocfilehash: 6cbaf19c5c4d52a5de1ef1d63ad5465859555810
ms.sourcegitcommit: 813f7126ed140a0dff7658553a80b266249d302f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2020
ms.locfileid: "84465299"
---
# <a name="how-to-use-azure-queue-storage-v21-from-python"></a>Jak používat Azure Queue Storage v 2.1 z Pythonu

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

Tento článek popisuje běžné scénáře použití služby Azure Queue Storage. Mezi zahrnuté scénáře patří vkládání, prohlížení, získávání a odstraňování zpráv fronty a vytváření a odstraňování front.

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Přehled

Ukázky v tomto článku jsou napsané v Pythonu a používají [sadu SDK Microsoft Azure Storage pro Python]. Další informace o frontách najdete v části [Další kroky](#next-steps) .

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Stáhnout a nainstalovat sadu SDK služby Azure Storage pro Python

[Sada SDK Azure Storage pro Python](https://github.com/azure/azure-storage-python) vyžaduje python verze 2,7, 3,3 nebo novější.
 
### <a name="install-via-pypi"></a>Instalace prostřednictvím PyPi

K instalaci prostřednictvím indexu balíčku Pythonu (PyPI) zadejte:

```bash
pip install azure-storage-queue==2.1.0
```

> [!NOTE]
> Pokud provádíte upgrade ze sady Azure Storage SDK pro Python verze 0,36 nebo starší, odinstalujte starší sadu SDK pomocí nástroje `pip uninstall azure-storage` před instalací nejnovějšího balíčku.

Alternativní metody instalace najdete v tématu [Azure Storage SDK pro Python](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Zobrazit ukázkovou aplikaci

Pokud chcete zobrazit a spustit ukázkovou aplikaci, která ukazuje použití Pythonu s frontami Azure, přečtěte si téma [Azure Storage: Začínáme s frontami Azure v Pythonu](https://github.com/Azure-Samples/storage-queue-python-getting-started). 

Chcete-li spustit ukázkovou aplikaci, ujistěte se, že jste `azure-storage-queue` nainstalovali `azure-storage-common` balíčky a.

## <a name="create-a-queue"></a>Vytvoření fronty

Objekt [QueueService](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice) vám umožní pracovat s frontami. Následující kód vytvoří `QueueService` objekt. V horní části každého souboru Pythonu, do kterého chcete programově přistupovat, přidejte následující Azure Storage:

```python
from azure.storage.queue import QueueService
```

Následující kód vytvoří `QueueService` objekt pomocí názvu účtu úložiště a klíče účtu. Pomocí názvu a klíče účtu nahraďte *MyAccount* a *myKey* .

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="insert-a-message-into-a-queue"></a>Vložení zprávy do fronty

Chcete-li vložit zprávu do fronty, pomocí metody [put_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) vytvořte novou zprávu a přidejte ji do fronty.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

Zprávy fronty Azure se ukládají jako text. Chcete-li uložit binární data, nastavte funkce kódování a dekódování Base64 do objektu Queue Service před vložením zprávy do fronty.

```python
# setup queue Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

## <a name="peek-at-the-next-message"></a>Zobrazení náhledu další zprávy

Můžete prohlížet zprávy před frontou, aniž byste je museli odebírat z fronty voláním metody [peek_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#peek-messages-queue-name--num-messages-none--timeout-none-) . Ve výchozím nastavení se `peek_messages` v jedné zprávě prohlédne.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="dequeue-messages"></a>Vyřadit zprávy z fronty

Kód ve dvou krocích odstraní zprávu z fronty. Když zavoláte [get_messages](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-), ve výchozím nastavení se ve frontě zobrazí další zpráva. Zpráva vrácená z `get_messages` se bude neviditelná pro jakýkoliv jiný kód, který čte zprávy z této fronty. Ve výchozím nastavení tato zpráva zůstává neviditelná po dobu 30 sekund. Chcete-li dokončit odebrání zprávy z fronty, je nutné také volat [delete_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-message-queue-name--message-id--pop-receipt--timeout-none-). Tento dvoustupňový proces odebrání zprávy zaručuje, že pokud váš kód nedokáže zpracovat zprávu z důvodu selhání hardwaru nebo softwaru, může jiná instance kódu získat stejnou zprávu a zkusit to znovu. Váš kód volá `delete_message` hned po zpracování zprávy.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

Načítání zpráv z fronty si můžete přizpůsobit dvěma způsoby. Za prvé si můžete načíst dávku zpráv (až 32). Za druhé si můžete nastavit delší nebo kratší časový limit neviditelnosti, aby měl váš kód více nebo méně času na úplné zpracování jednotlivých zpráv. Následující příklad kódu používá `get_messages` metodu k získání 16 zpráv v jednom volání. Potom zpracuje každou zprávu pomocí smyčky for. Také se pro každou zprávu nastaví časový limit neviditelnosti 5 minut.

```python
messages = queue_service.get_messages(
    'taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

## <a name="change-the-contents-of-a-queued-message"></a>Změna obsahu zpráv zařazených ve frontě

Podle potřeby můžete změnit obsah zprávy přímo ve frontě. Pokud zpráva představuje pracovní úlohu, mohli byste tuto funkci použít k aktualizaci stavu pracovních úloh. Následující kód používá metodu [update_message](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) k aktualizaci zprávy. Časový limit viditelnosti je nastavený na 0, což znamená, že se zpráva zobrazuje hned a obsah se aktualizuje.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message(
        'taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="get-the-queue-length"></a>Získání délky fronty

Podle potřeby můžete získat odhadovaný počet zpráv ve frontě. Metoda [get_queue_metadata](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#get-queue-metadata-queue-name--timeout-none-) požádá službu front, aby vrátila metadata o frontě, a `approximate_message_count` . Výsledek je pouze přibližný, protože je možné přidat nebo odebrat zprávy poté, co služba Queue odpoví na vaši žádost.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="delete-a-queue"></a>Odstranění fronty

Pokud chcete odstranit frontu a všechny zprávy, které jsou v ní obsažené, zavolejte metodu [delete_queue](/python/api/azure-storage-queue/azure.storage.queue.queueservice.queueservice#delete-queue-queue-name--fail-not-exist-false--timeout-none-) .

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili základy ukládání do fronty, můžete získat další informace pomocí těchto odkazů.

* [Reference k rozhraní API Pythonu pro fronty Azure](/python/api/azure-storage-queue)
* [Středisko pro vývojáře programující v Pythonu](https://azure.microsoft.com/develop/python/)
* [REST API služby Azure Storage](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[Sada SDK Microsoft Azure Storage pro Python]: https://github.com/Azure/azure-storage-python
