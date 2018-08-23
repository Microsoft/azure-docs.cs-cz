---
title: Postup používání úložiště Queue z Ruby | Dokumentace Microsoftu
description: Zjistěte, jak pomocí služby front Azure můžete vytvářet a odstraňovat fronty a vložit, získání a odstranění zprávy. Ukázky napsané v Ruby.
services: storage
author: tamram
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.component: queues
ms.openlocfilehash: 907175ac341632d2ba0048ab486392b949f83626
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2018
ms.locfileid: "42060538"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>Používání úložiště Queue z Ruby
[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Přehled
Tento průvodce vám ukáže, jak provádět běžné scénáře pomocí služby Microsoft Azure Queue Storage. Ukázky jsou napsané pomocí rozhraní API služby Azure Ruby.
Mezi popsané scénáře patří **vkládání**, **prohlížení**, **získávání**, a **odstranění** fronty zpráv, stejně jako  **vytváření a odstraňování front**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Vytvoření aplikace v Ruby
Vytvoření aplikace v Ruby. Pokyny najdete v tématu [vytvoření aplikace v Ruby ve službě App Service v Linuxu](https://docs.microsoft.com/azure/app-service/containers/quickstart-ruby).

## <a name="configure-your-application-to-access-storage"></a>Konfigurace aplikace pro přístup ke službě Storage
K použití služby Azure storage, budete muset stáhnout a použít Ruby azure balíček, který obsahuje sadu knihoven pohodlí, které komunikují s REST služby úložiště.

### <a name="use-rubygems-to-obtain-the-package"></a>Získání balíčku pomocí RubyGems
1. Použijte rozhraní příkazového řádku, jako je **PowerShell** (Windows), **Terminál** (Mac) nebo **Bash** (Unix).
2. Zadejte "azure gem instalace" v příkazovém okně instalace gem a závislostí.

### <a name="import-the-package"></a>Import balíčku
Pomocí oblíbeného textového editoru, přidejte do horní části souboru Ruby, kde máte v úmyslu používat úložiště následující:

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Nastavit připojení k Azure Storage
Modul azure načte proměnné prostředí **AZURE\_úložiště\_účet** a **AZURE\_úložiště\_ACCESS_KEY** informace požadované pro připojení k účtu služby Azure storage. Pokud nejsou nastavené tyto proměnné prostředí, je nutné zadat informace o účtu před použitím **Azure::QueueService** následujícím kódem:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

Získání těchto hodnot z klasického účtu úložiště nebo účtu úložiště Resource Manageru na webu Azure Portal:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Přejděte na účet úložiště, který chcete použít.
3. V okně Nastavení na pravé straně klikněte na **Přístupové klíče**.
4. V okně Přístupové klíče, které se zobrazí, uvidíte přístupový klíč 1 a přístupový klíč 2. Můžete použít libovolný z nich. 
5. Kliknutím na ikonu kopírování zkopírujte klíč do schránky. 

## <a name="how-to-create-a-queue"></a>Postupy: Vytvoření fronty
Následující kód vytvoří **Azure::QueueService** objektu, který umožňuje pracovat s frontami.

```ruby
azure_queue_service = Azure::QueueService.new
```

Použití **create_queue()** metodu pro vytvoření fronty se zadaným názvem.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Postupy: Vložit zprávu do fronty
Chcete-li vložit zprávu do fronty, použijte **create_message()** metoda vytvořit novou zprávu a přidat do fronty.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>Postupy: Zobrazení náhledu další zprávy
Můžete prohlížet zprávy ve frontě bez odebrání z fronty pomocí volání **Náhled\_messages()** metody. Ve výchozím nastavení **Náhled\_messages()** prohlédne do jedné zprávy. Můžete také určit počet zpráv, které chcete náhled.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Postupy: Odstranění z fronty další zprávy
Zpráva můžete odebrat z fronty ve dvou krocích.

1. Při volání **seznamu\_messages()**, získáte další zprávu ve frontě ve výchozím nastavení. Můžete také určit počet zpráv, které chcete načíst. Zprávy vrácené **seznamu\_messages()** stane neviditelnou pro jakýkoli jiný kód přečte zprávy z této fronty. Můžete předat časový limit viditelnosti v řádu sekund jako parametr.
2. K dokončení odebrání zprávy z fronty, musíte také zavolat **delete_message()**.

Tento dvoukrokový proces odebrání zprávy zaručuje, že pokud váš kód se nepodaří zpracovat zprávu z důvodu selhání hardwaru nebo softwaru, jiná instance vašeho kódu můžete získat stejná zpráva a zkuste to znovu. Kód volá **odstranit\_message()** hned po zpracování zprávy.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue", 
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Postupy: Změna obsahu zpráv zařazených ve frontě
Podle potřeby můžete změnit obsah zprávy přímo ve frontě. Kód uvedený níže používá **update_message()** způsob aktualizace zprávu. Metoda vrátí řazené kolekce členů, který obsahuje pop přijetí zprávy fronty a hodnotu čas UTC data, která reprezentuje čas zprávy se nebude zobrazovat ve frontě.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message", 
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Postupy: Dalších možností pro vyřazování z fronty zpráv
Načítání zpráv z fronty si můžete přizpůsobit dvěma způsoby.

1. Můžete načíst dávku zpráv.
2. Můžete nastavit časový limit neviditelnosti delší nebo kratší, umožňuje váš kód více nebo méně času na úplné zpracování jednotlivých zpráv.

Následující příklad kódu používá **seznamu\_messages()** metodu k získání 15 zpráv v jednom volání. Vytiskne a každou zprávu odstraní. Také se pro každou zprávu nastaví časový limit neviditelnosti 5 minut.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Postupy: Získání délky fronty
Můžete získat odhad počtu zpráv ve frontě. **Získat\_fronty\_metadata()** metoda požádá službu front vrátí počet zpráv přibližné a metadata o frontě.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Postupy: Odstranění fronty
Chcete-li odstranit frontu se všemi zprávami, které v ní, zavolejte **odstranit\_queue()** metodu na objekt fronty.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>Další kroky
Teď, když jste se naučili základy používání služby queue storage, použijte tyto odkazy na další informace o složitějších úlohách úložiště.

* Přejděte [Blog týmu Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/)
* Přejděte [sady Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) úložišti na Githubu

Porovnání mezi službou popisovaných v tomto článku služby front Azure a fronty Azure Service Bus podrobněji [tom, jak používat fronty Service Bus](https://azure.microsoft.com/develop/ruby/how-to-guides/service-bus-queues/) článek, naleznete v tématu [front Azure a fronty Service Bus – porovnání a Rozdíly](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md)