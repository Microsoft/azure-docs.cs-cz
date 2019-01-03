---
title: Postup používání úložiště Queue z Pythonu | Dokumentace Microsoftu
description: Zjistěte, jak používat službu Azure Queue z Pythonu vytvářet a odstraňovat fronty a vložit, získání a odstranit zprávy.
services: storage
author: tamram
ms.service: storage
ms.devlang: python
ms.topic: article
ms.date: 12/14/2018
ms.author: tamram
ms.component: queues
ms.openlocfilehash: 0edb90ca7324d47beaa5133d423928e615ff33a9
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53742809"
---
# <a name="how-to-use-queue-storage-from-python"></a>Používání úložiště Queue z Pythonu
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Přehled
Tento průvodce vám ukáže, jak provádět běžné scénáře pomocí služby Azure Queue storage. Ukázky jsou napsané v Pythonu a použití [Microsoft Azure Storage SDK pro Python]. Mezi popsané scénáře patří **vkládání**, **prohlížení**, **získávání**, a **odstranění** fronty zpráv, stejně jako  **vytváření a odstraňování front**. Další informace o frontách najdete v části [Další kroky].

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Stáhněte a nainstalujte sadu SDK služby Azure Storage pro Python

[Azure Storage SDK pro Python](https://github.com/azure/azure-storage-python) vyžaduje Python 2.7, 3.3, 3.4, 3.5 a 3.6.
 
### <a name="install-via-pypi"></a>Instalace přes PyPi

Pokud chcete nainstalovat prostřednictvím indexu balíčků Pythonu (PyPI), zadejte:

```bash
pip install azure-storage-queue
```

> [!NOTE]
> Pokud provádíte upgrade z Azure Storage SDK pro Python verze 0.36 nebo starší, odinstalujte starší pomocí sady SDK `pip uninstall azure-storage` před instalací nejnovější balíček.

Instalace alternativní způsoby najdete v tématu [Azure Storage SDK pro Python](https://github.com/Azure/azure-storage-python/).

## <a name="view-the-sample-application"></a>Zobrazit ukázkové aplikace

Zobrazit a spustit ukázkovou aplikaci, která ukazuje, jak pomocí front Azure pomocí Pythonu najdete v tématu [služby Azure Storage: Začínáme s frontami Azure v Pythonu](https://github.com/Azure-Samples/storage-queue-python-getting-started). 

Spustit ukázkovou aplikaci, ujistěte se, že máte nainstalovanou i `azure-storage-queue` a `azure-storage-common` balíčky.

## <a name="how-to-create-a-queue"></a>Jak: Vytvoření fronty

**QueueService** objekt vám umožňuje spolupracovat s frontami. Následující kód vytvoří **QueueService** objektu. Přidejte následující v horní části všechny soubory Pythonu, ve kterém chcete programovému přístupu ke službě Azure Storage:

```python
from azure.storage.queue import QueueService
```

Následující kód vytvoří **QueueService** pomocí úložiště účtu název a klíč účtu. Nahraďte "myaccount" a 'mykey' název účtu a klíč.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Jak: Vložit zprávu do fronty
Chcete-li vložit zprávu do fronty, použijte **umístit\_zpráva** metoda vytvořit novou zprávu a přidat do fronty.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

## <a name="how-to-peek-at-the-next-message"></a>Jak: Zobrazení náhledu další zprávy
Můžete prohlížet zprávy ve frontě bez odebrání z fronty pomocí volání **Náhled\_zprávy** metody. Ve výchozím nastavení **Náhled\_zprávy** prohlédne do jedné zprávy.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="how-to-dequeue-messages"></a>Jak: Odstranění z fronty zpráv
Váš kód odebere zprávu z fronty ve dvou krocích. Při volání **získat\_zprávy**, získáte další zprávu ve frontě ve výchozím nastavení. Zpráva vrácená metodou **získat\_zprávy** stane neviditelnou pro jakýkoli jiný kód přečte zprávy z této fronty. Ve výchozím nastavení tato zpráva zůstává neviditelná po dobu 30 sekund. K dokončení odebrání zprávy z fronty, musíte také zavolat **odstranit\_zpráva**. Tento dvoukrokový proces odebrání zprávy zaručuje, že pokud váš kód se nepodaří zpracovat zprávu z důvodu selhání hardwaru nebo softwaru, jiná instance vašeho kódu můžete získat stejná zpráva a zkuste to znovu. Kód volá **odstranit\_zpráva** hned po zpracování zprávy.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

Načítání zpráv z fronty si můžete přizpůsobit dvěma způsoby.
Za prvé si můžete načíst dávku zpráv (až 32). Za druhé si můžete nastavit delší nebo kratší časový limit neviditelnosti, aby měl váš kód více nebo méně času na úplné zpracování jednotlivých zpráv. Následující příklad kódu používá **získat\_zprávy** metodu k získání 16 zpráv v jednom volání. Pak se každá zpráva zpracuje pomocí smyčky for. Také se pro každou zprávu nastaví časový limit neviditelnosti 5 minut.

```python
messages = queue_service.get_messages('taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)        
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Jak: Změna obsahu zpráv zařazených ve frontě
Podle potřeby můžete změnit obsah zprávy přímo ve frontě. Pokud zpráva představuje pracovní úlohu, mohli byste tuto funkci použít k aktualizaci stavu pracovních úloh. Kód uvedený níže používá **aktualizovat\_zpráva** způsob aktualizace zprávu. Časový limit viditelnosti je nastavena na hodnotu 0, což znamená, okamžitě se zobrazí zpráva a aktualizovat obsah.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message('taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="how-to-get-the-queue-length"></a>Jak: Získání délky fronty
Podle potřeby můžete získat odhadovaný počet zpráv ve frontě. **Získat\_fronty\_metadat** metoda požádá službu front vrátit metadata o frontě a **approximate_message_count**. Výsledek je pouze přibližná, protože zprávy můžete přidat nebo odebrat po služba fronty jsou reaguje na váš požadavek.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="how-to-delete-a-queue"></a>Jak: Odstranění fronty
Chcete-li odstranit frontu se všemi zprávami, které v ní, zavolejte **odstranit\_fronty** metody.

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili základy používání služby Queue storage, použijte tyto odkazy na další informace.

* [Středisko pro vývojáře programující v Pythonu](https://azure.microsoft.com/develop/python/)
* [REST API služby Azure Storage](https://msdn.microsoft.com/library/azure/dd179355)

[Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK pro Python]: https://github.com/Azure/azure-storage-python
