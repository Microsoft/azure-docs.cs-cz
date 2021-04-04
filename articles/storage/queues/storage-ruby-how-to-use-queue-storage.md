---
title: Použití Queue Storage z Ruby-Azure Storage
description: Naučte se používat Queue Storage Azure k vytváření a odstraňování front a vkládání, získávání a odstraňování zpráv. Ukázky napsané v Ruby.
author: mhopkins-msft
ms.author: mhopkins
ms.reviewer: dineshm
ms.date: 12/08/2016
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.openlocfilehash: 8558949e49bcf551c9276458d375fb9ac9636184
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97587658"
---
# <a name="how-to-use-queue-storage-from-ruby"></a>Použití Queue Storage z Ruby

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Přehled

V této příručce se dozvíte, jak provádět běžné scénáře pomocí služby Microsoft Azure Queue Storage. Ukázky se napíší pomocí rozhraní API Ruby Azure. Mezi zahrnuté scénáře patří **vkládání**, **prohlížení**, **získávání** a **odstraňování** zpráv fronty a **vytváření a odstraňování front**.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Vytvoření aplikace v Ruby

Vytvořte aplikaci v Ruby. Pokyny najdete v tématu [Vytvoření aplikace v Ruby v App Service v systému Linux](../../app-service/quickstart-ruby.md).

## <a name="configure-your-application-to-access-storage"></a>Konfigurace aplikace pro přístup k úložišti

Pokud chcete použít Azure Storage, musíte si stáhnout a použít balíček Ruby Azure, který zahrnuje sadu praktických knihoven, které komunikují se službou REST (Storage).

<!-- docutune:ignore Terminal -->

### <a name="use-rubygems-to-obtain-the-package"></a>Získání balíčku pomocí RubyGems

1. Použijte rozhraní příkazového řádku, jako je PowerShell (Windows), Terminál (Mac) nebo Bash (Unix).
2. `gem install Azure`Do příkazového řádku zadejte a nainstalujte Gem a závislosti.

### <a name="import-the-package"></a>Import balíčku

Použijte svůj oblíbený textový editor, do horní části souboru Ruby přidejte následující, kde chcete úložiště použít:

```ruby
require "azure"
```

## <a name="setup-an-azure-storage-connection"></a>Nastavení Azure Storageho připojení

Modul Azure přečte proměnné prostředí `AZURE_STORAGE_ACCOUNT` a `AZURE_STORAGE_ACCESS_KEY` informace požadované pro připojení k vašemu Azure Storage účtu. Nejsou-li tyto proměnné prostředí nastaveny, je nutné zadat informace o účtu před použitím `Azure::QueueService` s následujícím kódem:

```ruby
Azure.config.storage_account_name = "<your azure storage account>"
Azure.config.storage_access_key = "<your Azure storage access key>"
```

Získání těchto hodnot z klasického účtu úložiště nebo účtu úložiště Resource Manageru na webu Azure Portal:

1. Přihlaste se k [Azure Portal](https://portal.azure.com).
2. Přejděte k účtu úložiště, který chcete použít.
3. V okně **Nastavení** na pravé straně klikněte na **přístupové klíče**.
4. V zobrazeném okně **přístupové klíče** uvidíte přístupová klávesa 1 a přístupová klávesa 2. Můžete použít libovolný z nich.
5. Kliknutím na ikonu kopírování zkopírujte klíč do schránky.

## <a name="how-to-create-a-queue"></a>Postupy: vytvoření fronty

Následující kód vytvoří `Azure::QueueService` objekt, který vám umožní pracovat s frontami.

```ruby
azure_queue_service = Azure::QueueService.new
```

Pomocí `create_queue()` metody vytvořte frontu se zadaným názvem.

```ruby
begin
  azure_queue_service.create_queue("test-queue")
rescue
  puts $!
end
```

## <a name="how-to-insert-a-message-into-a-queue"></a>Postupy: vložení zprávy do fronty

Chcete-li vložit zprávu do fronty, použijte `create_message()` metodu k vytvoření nové zprávy a jejímu přidání do fronty.

```ruby
azure_queue_service.create_message("test-queue", "test message")
```

## <a name="how-to-peek-at-the-next-message"></a>Postupy: prohlížení další zprávy

Můžete prohlížet zprávy před frontou, aniž byste je museli odebírat z fronty voláním `peek_messages()` metody. Ve výchozím nastavení se `peek_messages()` v jedné zprávě prohlédne. Můžete také zadat, kolik zpráv chcete prohlížet.

```ruby
result = azure_queue_service.peek_messages("test-queue",
  {:number_of_messages => 10})
```

## <a name="how-to-dequeue-the-next-message"></a>Postupy: vyřazení další zprávy z fronty

Můžete odebrat zprávu z fronty ve dvou krocích.

1. Když zavoláte `list_messages()` , dostanete ve výchozím nastavení další zprávu ve frontě. Můžete také zadat, kolik zpráv chcete získat. Zprávy vrácené z `list_messages()` se stávají neviditelné pro jakýkoliv jiný kód, který čte zprávy z této fronty. Jako parametr předáte časový limit viditelnosti v sekundách.
2. Chcete-li dokončit odebrání zprávy z fronty, je také nutné zavolat `delete_message()` .

Tento dvoustupňový proces odebrání zprávy zaručuje, že pokud váš kód nedokáže zpracovat zprávu z důvodu selhání hardwaru nebo softwaru, může jiná instance kódu získat stejnou zprávu a zkusit to znovu. Váš kód volá `delete_message()` hned po zpracování zprávy.

```ruby
messages = azure_queue_service.list_messages("test-queue", 30)
azure_queue_service.delete_message("test-queue",
  messages[0].id, messages[0].pop_receipt)
```

## <a name="how-to-change-the-contents-of-a-queued-message"></a>Postupy: Změna obsahu zprávy ve frontě

Podle potřeby můžete změnit obsah zprávy přímo ve frontě. Následující kód používá `update_message()` metodu k aktualizaci zprávy. Metoda vrátí řazenou kolekci členů, která obsahuje přijetí pop zprávy fronty, a `DateTime` hodnotu UTC, která představuje, kdy bude zpráva ve frontě viditelná.

```ruby
message = azure_queue_service.list_messages("test-queue", 30)
pop_receipt, time_next_visible = azure_queue_service.update_message(
  "test-queue", message.id, message.pop_receipt, "updated test message",
  30)
```

## <a name="how-to-additional-options-for-dequeuing-messages"></a>Postupy: Další možnosti pro vyřazování zpráv z fronty

Načítání zpráv z fronty si můžete přizpůsobit dvěma způsoby.

1. Můžete získat dávku zprávy.
2. Můžete nastavit delší nebo kratší časový limit neviditelnosti, což umožňuje, aby váš kód měl více nebo méně času na úplné zpracování každé zprávy.

Následující příklad kódu používá `list_messages()` metodu k získání 15 zpráv v jednom volání. Pak vytiskne a odstraní každou zprávu. Také se pro každou zprávu nastaví časový limit neviditelnosti 5 minut.

```ruby
azure_queue_service.list_messages("test-queue", 300
  {:number_of_messages => 15}).each do |m|
  puts m.message_text
  azure_queue_service.delete_message("test-queue", m.id, m.pop_receipt)
end
```

## <a name="how-to-get-the-queue-length"></a>Postupy: získání délky fronty

Můžete získat odhad počtu zpráv ve frontě. `get_queue_metadata()`Metoda vrátí přibližný počet zpráv a další metadata fronty.

```ruby
message_count, metadata = azure_queue_service.get_queue_metadata(
  "test-queue")
```

## <a name="how-to-delete-a-queue"></a>Postupy: odstranění fronty

Pokud chcete odstranit frontu a všechny zprávy, které jsou v ní obsažené, zavolejte `delete_queue()` metodu u objektu Queue.

```ruby
azure_queue_service.delete_queue("test-queue")
```

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili základy Queue Storage, postupujte podle těchto odkazů a získejte další informace o složitějších úlohách úložiště.

- Navštívit [Blog týmu Azure Storage](/archive/blogs/windowsazurestorage/)
- Navštivte úložiště [Azure SDK for Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) na GitHubu.

Srovnání mezi Azure Queue Storage popsané v tomto článku a v Azure Service Busch frontách popsaných v tématu [Jak používat Service Bus fronty](https://azure.microsoft.com/develop/ruby/how-to-guides/service-bus-queues/)najdete v tématu [azure Queue Storage a Service Bus queueed a contrastd](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md) .
