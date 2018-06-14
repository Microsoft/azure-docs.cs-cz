---
title: Používání úložiště Queue z Pythonu | Microsoft Docs
description: Naučte se používat službu Azure Queue z Pythonu vytvářet a odstraňovat fronty a vložit, získání a odstranění zprávy.
services: storage
documentationcenter: python
author: tamram
manager: timlt
editor: tysonn
ms.assetid: cc0d2da2-379a-4b58-a234-8852b4e3d99d
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: c7976c01436b1c30880bfd4c57cb97f72a4f48b0
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2017
ms.locfileid: "25986745"
---
# <a name="how-to-use-queue-storage-from-python"></a>Používání úložiště Queue z Pythonu
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Přehled
Tento průvodce vám ukáže, jak provádět běžné scénáře s využitím služby Azure Queue storage. Ukázky jsou napsané v Pythonu a použití [Microsoft Azure SDK úložiště pro jazyk Python]. Pokryté scénáře zahrnují **vkládání**, **prohlížení**, **získávání**, a **odstraňování** fronty zpráv, a také **vytváření a odstraňování front**. Další informace o frontách najdete v oddílu [Další kroky].

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Stáhněte a nainstalujte úložiště Azure SDK pro Python

Azure SDK úložiště pro Python vyžaduje Python 2.7 3.3, 3.4, 3.5 nebo 3.6 a je rozdělena na 4 různých balíčcích: `azure-storage-blob`, `azure-storage-file`, `azure-storage-table` a `azure-storage-queue`. V tomto kurzu budeme používat `azure-storage-queue` balíčku.
 
### <a name="install-via-pypi"></a>Nainstalovat prostřednictvím úložiště PyPi

Chcete-li nainstalovat prostřednictvím indexu balíčků Pythonu (úložiště PyPI), zadejte:

```bash
pip install azure-storage-queue
```


> [!NOTE]
> Pokud upgradujete ze sady SDK úložiště Azure pro jazyk Python 0.36 nebo starší verze, bude nejprve musíte odinstalovat pomocí `pip uninstall azure-storage` jako už sady SDK úložiště pro jazyk Python v vydáváme jeden balíček.
> 
> 

Metody alternativní instalace, najdete v článku [sada SDK úložiště Azure pro jazyk Python na Githubu](https://github.com/Azure/azure-storage-python/).

## <a name="how-to-create-a-queue"></a>Postupy: Vytvoření fronty
**QueueService** objekt vám umožňuje spolupracovat s fronty. Následující kód vytvoří **QueueService** objektu. Přidejte následující v horní části všech soubor Python, ve kterém chcete k programovému přístupu ke službě Azure Storage:

```python
from azure.storage.queue import QueueService
```

Následující kód vytvoří **QueueService** pomocí klíč účet a název účtu úložiště. Nahraďte název účtu a klíč 'stránku Můj účet' a 'mykey.

```python
queue_service = QueueService(account_name='myaccount', account_key='mykey')

queue_service.create_queue('taskqueue')
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Postupy: Vložit zprávu do fronty
Chcete-li vložit zprávu do fronty, použijte **put\_zpráva** metodu pro vytvoření nové zprávy a přidejte ji do fronty.

```python
queue_service.put_message('taskqueue', u'Hello World')
```

## <a name="how-to-peek-at-the-next-message"></a>Postupy: Zobrazení náhledu další zprávy
Můžete prohlížet zprávy ve frontě bez odebere ji z fronty voláním **funkce Náhled\_zprávy** metoda. Ve výchozím nastavení **funkce Náhled\_zprávy** prohlédne do jedné zprávy.

```python
messages = queue_service.peek_messages('taskqueue')
for message in messages:
    print(message.content)
```

## <a name="how-to-dequeue-messages"></a>Postupy: Dequeue – zprávy
Váš kód odebere zprávu z fronty ve dvou krocích. Při volání **získat\_zprávy**, získáte další zprávu ve frontě ve výchozím nastavení. Zpráva vrácená metodou **získat\_zprávy** stane neviditelnou pro jakýkoli jiný kód čtení zpráv z této fronty. Ve výchozím nastavení tato zpráva zůstává neviditelná po dobu 30 sekund. K dokončení odebrání zprávy z fronty, musíte také zavolat **odstranit\_zpráva**. Tento dvoukrokový proces odebrání zprávy zaručuje, že když kódu se nepodaří zpracovat zprávu z důvodu selhání hardwaru nebo softwaru, jiná instance vašeho kódu můžete stejnou zprávu získat a zkuste to znovu. Váš kód zavolá metodu **odstranit\_zpráva** pravým po zpracování zprávy.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)
```

Načítání zpráv z fronty si můžete přizpůsobit dvěma způsoby.
Za prvé si můžete načíst dávku zpráv (až 32). Za druhé si můžete nastavit delší nebo kratší časový limit neviditelnosti, aby měl váš kód více nebo méně času na úplné zpracování jednotlivých zpráv. Následující příklad kódu používá **získat\_zprávy** metoda získat 16 zpráv v jednom volání. Pak se každá zpráva zpracuje pomocí pro smyčky. Také se pro každou zprávu nastaví časový limit neviditelnosti 5 minut.

```python
messages = queue_service.get_messages('taskqueue', num_messages=16, visibility_timeout=5*60)
for message in messages:
    print(message.content)
    queue_service.delete_message('taskqueue', message.id, message.pop_receipt)        
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Postupy: Změna obsahu zpráv zařazených ve frontě
Podle potřeby můžete změnit obsah zprávy přímo ve frontě. Pokud zpráva představuje pracovní úlohu, mohli byste tuto funkci použít k aktualizaci stavu pracovních úloh. Kód pod používá **aktualizace\_zpráva** metoda aktualizace zprávu. Časový limit viditelnosti nastavena na hodnotu 0, což znamená, tato zpráva se zobrazí okamžitě a je obsah aktualizován.

```python
messages = queue_service.get_messages('taskqueue')
for message in messages:
    queue_service.update_message('taskqueue', message.id, message.pop_receipt, 0, u'Hello World Again')
```

## <a name="how-to-get-the-queue-length"></a>Postupy: Získání délky fronty
Podle potřeby můžete získat odhadovaný počet zpráv ve frontě. **Získat\_fronty\_metadata** metoda požádá službu front vrátit metadata o fronty a **approximate_message_count**. Výsledkem je přibližný, protože zprávy můžete přidat nebo odebrat po služby front odpoví na žádost.

```python
metadata = queue_service.get_queue_metadata('taskqueue')
count = metadata.approximate_message_count
```

## <a name="how-to-delete-a-queue"></a>Postupy: Odstranění fronty
Chcete-li odstranit frontu se všemi zprávami, které v ní, zavolejte **odstranit\_fronty** metoda.

```python
queue_service.delete_queue('taskqueue')
```

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili základy používání služby Queue storage, postupujte podle následujících odkazech na další informace.

* [Středisko pro vývojáře programující v Pythonu](/develop/python/)
* [REST API služby Azure Storage](http://msdn.microsoft.com/library/azure/dd179355)
* [Blog týmu Azure Storage]
* [Microsoft Azure SDK úložiště pro jazyk Python]

[Blog týmu Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure SDK úložiště pro jazyk Python]: https://github.com/Azure/azure-storage-python