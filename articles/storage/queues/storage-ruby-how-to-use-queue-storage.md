---
title: Jak používat úložiště front z Ruby – Azure Storage
description: Zjistěte, jak pomocí služby Azure Queue vytvářet a odstraňovat fronty a vkládat, získat a odstraňovat zprávy. Vzorky napsané v Ruby.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/08/2016
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: c7211bc805f4ed1d026faedbfdc9d53d3c1dfd93
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68721295"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>Používání úložiště Queue z Ruby
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Přehled
Tato příručka ukazuje, jak provádět běžné scénáře pomocí služby Microsoft Azure Queue Storage. Ukázky jsou zapsány pomocí rozhraní Ruby Azure API.
Zahrnuté scénáře zahrnují **vkládání**, **prohlížení**, **získávání**a **odstranění** zpráv fronty, stejně jako vytváření a **mazání front**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Vytvoření aplikace Ruby
Vytvořte aplikaci Ruby. Pokyny najdete [v tématu Vytvoření aplikace Ruby ve službě App Service na Linuxu](https://docs.microsoft.com/azure/app-service/containers/quickstart-ruby).

## <a name="configure-your-application-to-access-storage"></a>Konfigurace aplikace pro přístup k úložišti
Chcete-li používat úložiště Azure, musíte stáhnout a použít balíček Ruby azure, který obsahuje sadu knihoven pohodlí, které komunikují se službami REST úložiště.

### <a name="use-rubygems-to-obtain-the-package"></a>Získání balíčku pomocí RubyGems
1. Použijte rozhraní příkazového řádku, jako je **PowerShell** (Windows), **Terminál** (Mac) nebo **Bash** (Unix).
2. Zadejte "gem install azure" v okně příkazu pro instalaci drahokamu a závislostí.

### <a name="import-the-package"></a>Import balíčku
Použijte svůj oblíbený textový editor, přidejte následující do horní části souboru Ruby, kde máte v úmyslu použít úložiště:

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Nastavení připojení úložiště Azure
Modul Azure bude číst proměnné prostředí **AZURE\_STORAGE\_ACCOUNT** a AZURE **\_STORAGE\_ACCESS_KEY** pro informace potřebné pro připojení k účtu úložiště Azure. Pokud tyto proměnné prostředí nejsou nastaveny, je nutné zadat informace o účtu před použitím **Azure::QueueService** s následujícím kódem:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

Získání těchto hodnot z klasického účtu úložiště nebo účtu úložiště Resource Manageru na webu Azure Portal:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Přejděte na účet úložiště, který chcete použít.
3. V okně Nastavení na pravé straně klikněte na **Přístupové klíče**.
4. V okně Přístupové klíče, které se zobrazí, uvidíte přístupový klíč 1 a přístupový klíč 2. Můžete použít libovolný z nich. 
5. Kliknutím na ikonu kopírování zkopírujte klíč do schránky. 

## <a name="how-to-create-a-queue"></a>Postup: Vytvoření fronty
Následující kód vytvoří objekt **Azure::QueueService,** který umožňuje pracovat s frontami.

```ruby
azure_queue_service = Azure::QueueService.new
```

Pomocí metody **create_queue()** vytvořte frontu se zadaným názvem.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Postup: Vložení zprávy do fronty
Chcete-li vložit zprávu do fronty, použijte metodu **create_message()** k vytvoření nové zprávy a její přidání do fronty.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>Postup: Náhled na další zprávu
Můžete nahlédnout na zprávu v přední části fronty bez odebrání z fronty voláním **peek\_messages()** metoda. Ve výchozím nastavení **náhled\_zprávy()** prohlížet na jednu zprávu. Můžete také určit, kolik zpráv chcete prohlížet.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Postup: Vyřazení další zprávy ze zařazení do fronty
Zprávu můžete z fronty odebrat ve dvou krocích.

1. Při volání **\_seznam messages()**, zobrazí se další zpráva ve frontě ve výchozím nastavení. Můžete také určit, kolik zpráv chcete získat. Zprávy vrácené ze **seznamu\_messages()** stane neviditelné pro všechny ostatní kód čtení zpráv z této fronty. Předáte časový limit viditelnosti v sekundách jako parametr.
2. Chcete-li dokončit odebrání zprávy z fronty, musíte také volat **delete_message()**.

Tento dvoustupňový proces odebrání zprávy zajišťuje, že když váš kód nezpracuje zprávu z důvodu selhání hardwaru nebo softwaru, může jiná instance kódu získat stejnou zprávu a zkusit to znovu. Volání kódu **\_odstraní zprávu()** ihned po zpracování zprávy.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue", 
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Postup: Změna obsahu zprávy ve frontě
Podle potřeby můžete změnit obsah zprávy přímo ve frontě. Níže uvedený kód používá **metodu update_message()** k aktualizaci zprávy. Metoda vrátí řazenou kolekce členů, která obsahuje pop příjem zprávy fronty a uTC datum čas, který představuje, kdy zpráva bude viditelná ve frontě.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message", 
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Postup: Další možnosti pro dequeuing zpráv
Načítání zpráv z fronty si můžete přizpůsobit dvěma způsoby.

1. Můžete získat dávku zprávy.
2. Můžete nastavit delší nebo kratší časový limit neviditelnosti, což umožňuje kódu více či méně času na úplné zpracování každé zprávy.

Následující příklad kódu používá metodu **list\_messages()** k získání 15 zpráv v jednom volání. Pak vytiskne a odstraní každou zprávu. Také se pro každou zprávu nastaví časový limit neviditelnosti 5 minut.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Postup: Získání délky fronty
Můžete získat odhad počtu zpráv ve frontě. Metoda **\_get\_queue ()** požádá službu fronty, aby vrátila přibližný počet zpráv a metadata o frontě.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Postup: Odstranění fronty
Chcete-li odstranit frontu a všechny zprávy v ní obsažené, zavolejte metodu **delete\_queue()** na objektu fronty.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili základy úložiště front, postupujte podle těchto odkazů a získejte další informace o složitějších úlohách úložiště.

* Navštivte [blog týmu úložišť Azure](https://blogs.msdn.com/b/windowsazurestorage/)
* Navštivte azure [sdk pro ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) úložiště na GitHubu

Porovnání mezi službou fronty Azure popsané v tomto článku a frontami služby Azure Service Bus popsanými v článku [Jak používat fronty služby Service Bus](https://azure.microsoft.com/develop/ruby/how-to-guides/service-bus-queues/) najdete v [tématu Fronty Azure a fronty sběrnice – porovnání a kontrastní](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)
