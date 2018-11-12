---
title: Postup používání úložiště Queue z Pythonu | Dokumentace Microsoftu
description: Zjistěte, jak používat službu Azure Queue z Pythonu vytvářet a odstraňovat fronty a vložit, získání a odstranit zprávy.
services: storage
author: tamram
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.component: queues
ms.openlocfilehash: 1e52f199847b9e03eb31da71f1f0577df92d2b51
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51230406"
---
# <a name="how-to-use-queue-storage-from-python"></a>Používání úložiště Queue z Pythonu
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Přehled
Tento průvodce vám ukáže, jak provádět běžné scénáře pomocí služby Azure Queue storage. Ukázky jsou napsané v Pythonu a použití [Microsoft Azure Storage SDK pro Python]. Mezi popsané scénáře patří **vkládání**, **prohlížení**, **získávání**, a **odstranění** fronty zpráv, stejně jako  **vytváření a odstraňování front**. Další informace o frontách najdete v části [Další kroky].

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Stáhněte a nainstalujte sadu SDK služby Azure Storage pro Python

Azure Storage SDK pro Python vyžaduje Python 2.7, 3.3, 3.4, 3.5 a 3.6 a je k dispozici ve 4 různých balíčcích: `azure-storage-blob`, `azure-storage-file`, `azure-storage-table` a `azure-storage-queue`. V tomto kurzu budeme používat `azure-storage-queue` balíčku.
 
### <a name="install-via-pypi"></a>Instalace přes PyPi

Pokud chcete nainstalovat prostřednictvím indexu balíčků Pythonu (PyPI), zadejte:

```bash
pip install azure-storage-queue
```


> [!NOTE]
> Pokud provádíte upgrade z Azure Storage SDK pro Python verze 0.36 nebo starší, budete nejdřív muset, odinstalujte ji pomocí `pip uninstall azure-storage` jako už vydáváme Storage SDK pro Python v jediném balíčku.
> 
> 

Metody alternativní instalace, najdete [Azure Storage SDK pro Python na Githubu](https://github.com/Azure/azure-storage-python/).

## <a name="how-to-create-a-queue"></a>Postupy: Vytvoření fronty
**QueueService** objekt vám umožňuje spolupracovat s frontami. Následující kód vytvoří **QueueService** objektu. Přidejte následující v horní části všechny soubory Pythonu, ve kterém chcete programovému přístupu ke službě Azure Storage:

```python
from azure.storage.queue import QueueService
```

Následující kód vytvoří **QueueService** pomocí úložiště účtu název a klíč účtu. Nahraďte "myaccount" a 'mykey' název účtu a klíč.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Postupy: Vložit zprávu do fronty
Chcete-li vložit zprávu do fronty, použijte **umístit\_zpráva** metoda vytvořit novou zprávu a přidat do fronty.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

## <a name="how-to-peek-at-the-next-message"></a>Postupy: Zobrazení náhledu další zprávy
Můžete prohlížet zprávy ve frontě bez odebrání z fronty pomocí volání **Náhled\_zprávy** metody. Ve výchozím nastavení **Náhled\_zprávy** prohlédne do jedné zprávy.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="how-to-dequeue-messages"></a>Postupy: Odstranění z fronty zpráv
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

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Postupy: Změna obsahu zpráv zařazených ve frontě
Podle potřeby můžete změnit obsah zprávy přímo ve frontě. Pokud zpráva představuje pracovní úlohu, mohli byste tuto funkci použít k aktualizaci stavu pracovních úloh. Kód uvedený níže používá **aktualizovat\_zpráva** způsob aktualizace zprávu. Časový limit viditelnosti je nastavena na hodnotu 0, což znamená, okamžitě se zobrazí zpráva a aktualizovat obsah.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message('taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="how-to-get-the-queue-length"></a>Postupy: Získání délky fronty
Podle potřeby můžete získat odhadovaný počet zpráv ve frontě. **Získat\_fronty\_metadat** metoda požádá službu front vrátit metadata o frontě a **approximate_message_count**. Výsledek je pouze přibližná, protože zprávy můžete přidat nebo odebrat po služba fronty jsou reaguje na váš požadavek.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="how-to-delete-a-queue"></a>Postupy: Odstranění fronty
Chcete-li odstranit frontu se všemi zprávami, které v ní, zavolejte **odstranit\_fronty** metody.

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili základy používání služby Queue storage, použijte tyto odkazy na další informace.

* [Středisko pro vývojáře programující v Pythonu](https://azure.microsoft.com/develop/python/)
* [REST API služby Azure Storage](https://msdn.microsoft.com/library/azure/dd179355)
* [Blog týmu Azure Storage]
* [Microsoft Azure Storage SDK pro Python]

[Blog týmu Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK pro Python]: https://github.com/Azure/azure-storage-python