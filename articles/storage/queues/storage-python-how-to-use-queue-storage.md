---
title: Používání úložiště Queue z Pythonu – Azure Storage
description: Naučte se používat Azure Služba front z Pythonu k vytváření a odstraňování front a vkládání, získávání a odstraňování zpráv.
author: mhopkins-msft
ms.service: storage
ms.author: mhopkins
ms.date: 12/14/2018
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 1ed084bfa0cf6879983e38ac6a8c5ab57e8948a8
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68721360"
---
# <a name="how-to-use-queue-storage-from-python"></a>Používání úložiště Queue z Pythonu
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Přehled
V této příručce se dozvíte, jak provádět běžné scénáře pomocí služby Azure Queue Storage. Ukázky jsou napsané v Pythonu a používají [Sada SDK Microsoft Azure Storage pro Python]. Mezi zahrnuté scénáře patří **vkládání**, **prohlížení**, **získávání**a **odstraňování** zpráv fronty a **vytváření a odstraňování front**. Další informace o frontách najdete v části [další kroky].

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Stažení a instalace sady Azure Storage SDK pro Python

[Sada SDK Azure Storage pro Python](https://github.com/azure/azure-storage-python) vyžaduje Python 2,7, 3,3, 3,4, 3,5 nebo 3,6.
 
### <a name="install-via-pypi"></a>Instalace prostřednictvím PyPi

K instalaci prostřednictvím indexu balíčku Pythonu (PyPI) zadejte:

```bash
pip install azure-storage-queue
```

> [!NOTE]
> Pokud provádíte upgrade ze sady Azure Storage SDK pro Python verze 0,36 nebo starší, odinstalujte starší sadu SDK `pip uninstall azure-storage` pomocí nástroje před instalací nejnovějšího balíčku.

Alternativní metody instalace najdete v tématu [Azure Storage SDK pro Python](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Zobrazit ukázkovou aplikaci

Pokud chcete zobrazit a spustit ukázkovou aplikaci, která ukazuje použití Pythonu s frontami Azure [, přečtěte si téma Azure Storage: Začínáme s frontami Azure v](https://github.com/Azure-Samples/storage-queue-python-getting-started)Pythonu. 

Chcete-li spustit ukázkovou aplikaci, ujistěte se, že jste `azure-storage-queue` nainstalovali `azure-storage-common` balíčky a.

## <a name="how-to-create-a-queue"></a>Jak: Vytvoření fronty

Objekt **QueueService** vám umožní pracovat s frontami. Následující kód vytvoří objekt **QueueService** . V horní části každého souboru Pythonu, do kterého chcete programově přistupovat, přidejte následující Azure Storage:

```python
from azure.storage.queue import QueueService
```

Následující kód vytvoří objekt **QueueService** pomocí názvu a klíče účtu úložiště. Nahraďte ' myaccount ' a ' myKey ' názvem svého účtu a klíčem.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Jak: Vložení zprávy do fronty
Chcete-li vložit zprávu do fronty, použijte metodu **Put\_zprávy** a vytvořte novou zprávu a přidejte ji do fronty.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

## <a name="how-to-peek-at-the-next-message"></a>Jak: Prohlížet další zprávu
Můžete prohlížet zprávu v přední části fronty bez jejího odebrání z fronty voláním metody **prohlížet\_zprávy** . Ve výchozím nastavení **\_prohlížet zprávy** v rámci jedné zprávy.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="how-to-dequeue-messages"></a>Jak: Vyřadit zprávy z fronty
Kód ve dvou krocích odstraní zprávu z fronty. Když zavoláte **Get\_Messages**, dostanete ve výchozím nastavení další zprávu ve frontě. Zpráva vrácená ze **zprávy Get\_** se bude neviditelná pro jakýkoliv jiný kód, který čte zprávy z této fronty. Ve výchozím nastavení tato zpráva zůstává neviditelná po dobu 30 sekund. Chcete-li dokončit odebrání zprávy z fronty, je nutné také zavolat **Delete\_Message**. Tento dvoustupňový proces odebrání zprávy zaručuje, že pokud váš kód nedokáže zpracovat zprávu z důvodu selhání hardwaru nebo softwaru, může jiná instance kódu získat stejnou zprávu a zkusit to znovu. Kód volá po zpracování zprávy okamžitě **zprávu o odstranění\_** .

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

Načítání zpráv z fronty si můžete přizpůsobit dvěma způsoby.
Za prvé si můžete načíst dávku zpráv (až 32). Za druhé si můžete nastavit delší nebo kratší časový limit neviditelnosti, aby měl váš kód více nebo méně času na úplné zpracování jednotlivých zpráv. Následující příklad kódu používá metodu **Get\_Messages** k získání 16 zpráv v jednom volání. Potom zpracuje každou zprávu pomocí smyčky for. Také se pro každou zprávu nastaví časový limit neviditelnosti 5 minut.

```python
messages = queue_service.get_messages(
    'taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Jak: Změna obsahu zprávy ve frontě
Podle potřeby můžete změnit obsah zprávy přímo ve frontě. Pokud zpráva představuje pracovní úlohu, mohli byste tuto funkci použít k aktualizaci stavu pracovních úloh. Následující kód používá metodu **zprávy aktualizace\_** k aktualizaci zprávy. Časový limit viditelnosti je nastavený na 0, což znamená, že se zpráva zobrazuje hned a obsah se aktualizuje.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message(
        'taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="how-to-get-the-queue-length"></a>Jak: Získat délku fronty
Podle potřeby můžete získat odhadovaný počet zpráv ve frontě. Metoda **Get\_Queue\_metadata** požádá službu front, aby vrátila metadata o frontě, a **approximate_message_count**. Výsledek je pouze přibližný, protože je možné přidat nebo odebrat zprávy poté, co služba Queue odpoví na vaši žádost.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="how-to-delete-a-queue"></a>Jak: Odstranění fronty
Pokud chcete odstranit frontu a všechny zprávy, které jsou v ní obsažené, zavolejte metodu **Queue Delete\_** .

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili základy ukládání do fronty, můžete získat další informace pomocí těchto odkazů.

* [Středisko pro vývojáře programující v Pythonu](https://azure.microsoft.com/develop/python/)
* [REST API služby Azure Storage](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: https://blogs.msdn.com/b/windowsazurestorage/
[Sada SDK Microsoft Azure Storage pro Python]: https://github.com/Azure/azure-storage-python
